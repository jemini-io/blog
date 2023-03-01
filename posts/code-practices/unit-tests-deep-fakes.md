# UnitTest like a Boss: Deep Fakes, not Mocks

# Intro

Early in my career I was fiend for unit tests. I thought unit tests triple-equaled quality.
Then, after witnessing first hand the spagetti-code of UnitTest mocking most projects do in order to have good coverage I swung back the other way and said, "no unit tests! only integration tests".

Well, here, I'd like to offer a balanced approach: Deep Unit Testing without Mocks.

## what this isn’t

This is not a treatise on when to use E2E tests or what level of coverage you should have for perfect quality.

This is not a gold standard for NestJs or any of the other technologies under test. I am focused on the practice of writing tests.

## what it is

This is simply a plea to stop mocking and if you are going to write unit tests, make them as valuable, reusable, and clean as possible.

# Context

- Nestjs API - Auth
  - DI → If you don’t have it, this still works, but it will look different. You should just get a DI framework.
    - Leave a comment and maybe we’ll do a vid for that.
  - TS → We’ll resolve some TS typing issues along the way.
  - Jest → We’ll be migrating to Fakes and resolving issues along the way.
  - TS Prisma → Git it.

These concepts transcend the architecture and tech choices of the app.

# Fakes instead of Mocks

Let’s start with a normal Jest Mock

```tsx
// auth.services.spec.ts

import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import { SaltService } from './salt.service';
jest.mock('@nestjs/jwt');
jest.mock('../users/users.service');
jest.mock('./salt.service');

// class under test
import { AuthService } from './auth.service';

describe('AuthService::UnitTest::Mocks', () => {
  it('should return valid user', async () => {
  // mock your classes
    const mockedUsersService = jest.mocked(new UsersService({} as any));
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedSaltService = jest.mocked(new SaltService());

    // internals of the function are exposed and realistic values are isolated.
    mockedUsersService.getPassword.mockResolvedValue('password');
    mockedSaltService.compare.mockResolvedValue(true);
    mockedUsersService.findOne.mockResolvedValue({
      email: 'email',
      id: 1,
      username: 'username',
      usersRoles: [],
    });

    const authService = new AuthService(
      mockedUsersService,
      mockedJwtService,
      mockedSaltService,
    );
    const user = await authService.validateUser('email', 'password');
    expect(user).toHaveProperty('email');
    expect(user).not.toHaveProperty('password');
  });
});
```

If you’re watching this video, chances are you already have a history of bad experiences with unit testing with mocks. In my experience there are a couple problems:

- A lot of set up just to mock. Even when I refactor, it’s generally localized to this test which means down the road, someone will be duplicating work.
- The mocks expose the internals of ***how*** our method does the job. This makes for brittle tests.
- Leads to terrible practices like, `getPassword.calledWithArgs`
- They encourage traditional unit tests, testing one function at time.
- Tends to mess with the global cache, we’ll see why that’s a problem later.

If you need more proof that mocking is bad:

- Yegor - Elegant Objects
- James Shore (youtube)
- Bob Martin ([ref](https://blog.cleancoder.com/uncle-bob/2014/05/10/WhenToMock.html))

## Step 1: Replace UsersService Mock with a Fake

```tsx
// user.service.ts
class UsersServiceFake implements UsersService {}
```

Create the class and then implement the methods.

```tsx
// user.service.ts
export type PublicMembersOf<T> = { [K in keyof T]: T[K] };
class UsersServiceFake implements PublicMembersOf<UsersService> {
  //
}
```

Error: `Property 'userModel' is missing` add

```tsx
// user.service.ts
class UsersServiceFake PublicMembersOf<UsersService> {
  users: User[] = [
    {
      email: 'email',
      id: 1,
      password: 'password',
      username: 'username',
      usersRoles: [],
    },
  ];
  // other methods
  async getUsers(): Promise<Omit<User, 'password'>[]> {
    return this.fakeUsers.map(({ password, ...rest }) => rest) as User[];
  }
  async findOne(email: string): Promise<UserType> {
    return this.fakeUsers[0] as User;
  }
  async getPassword(email: string): Promise<string> {
    return this.fakeUsers[0].password;
  }
}
```

1. Add a default users value
2. Implement the methods with some reasonable defaults
    1. I call this a “low-fi” model of the class

```tsx
export class UsersService {
  static createFake(): UsersService {
    return <UsersService>(<unknown>new UserServiceFake());
  }
}
```

- Yes, you could do this with abstract classes or interfaces or whatever. But this type casting works fine for me and for this video.
- Yes, this is “test” code in your production code. If you can stomach that, consider that users of your code can also reuse your Fake for their tests.

```tsx
it('should...', () => {
  const { UsersService } = jest.requireActual('../users/users.service');
  // other codez
  const authService = new AuthService(
    UsersService.createFake(), // new
    mockedJwtService,
    mockedSaltService,
  );
})
```

- We have moved the “mock” object into the fake: it’s now very, very reusable.

## Step 2: Unhappy Path ⇒ User Not Found

```tsx
mockedUsersService.getPassword.mockRejectedValue(new Error('could not find user'));
```

In the Mocked version

- Throw a new error. Pretty easy.

```tsx
// auth.service.spec.ts
const user = await authService.validateUser('wrongemail', 'password');
try {
  await authService.validateUser('email', 'wrongpassword');
  throw new Error(`should throw error: ${expectedMessage}`);
} catch (error) {
  expect(error.message).toEqual("unable to validate user");
}

// user.service.ts
class UsersServiceFake PublicMembersOf<UsersService> {
  async findOne(email: string): Promise<UserType> {
    return this.fakeUsers.find((el) => el.email === email) as User;
  }
  async getPassword(email: string): Promise<string> {
    const user = this.fakeUsers.find((el) => el.email === email);
    if (!user) {
      throw new Error('could not find user');
    }
    return user.password;
  }
}
```

In the Faked version:

- Since changing to `wrongemail` doesn’t fail the test, we have to update the Fake.
- Tradeoff: You have to either implement the method like so, or pass in some configuration in the constructor. Key is: don’t expose the internals. Don’t treat it like mock. Don’t pass in “returnValueOfGetPassword” or something. We’ll see later why.
- But, this test is *************more real.************* So the interactions of the AuthService will be slightly more real. We’ll expand on this later.

## Step 3: Unhappy Path ⇒ Password Compare Fails

If we look at our code under test, we’ve testing the happy path. But the unhappy paths are more important, and the first we see is the `compare` method.

```tsx
mockedSaltService.compare.mockResolvedValue(false);
```

- In the Mock test we can just change the resolved value to false. That’s pretty nice.
- This is certainly a tradeoff point where Fakes require a little more work initially.

```tsx
import { Injectable } from '@nestjs/common';
import { hash, compare as _compare } from 'bcrypt';

@Injectable()
export class SaltService {
  static createFake() {
    return new FakeSaltService();
  }

  async hashPassword(password: string) {
    const saltRounds = 10;
    const hashedPassword = await hash(password, saltRounds);
    return hashedPassword;
  }

  async compare(password: string, hashString: string) {
    return await _compare(password, hashString);
  }
}

/**
 * Provide simple logic to default to different kinds of behavior
 * when you can. Otherwise use some easy defaults
 */
class FakeSaltService implements SaltService {
  /**
   * Returns the same string that is passed in.
   */
  async hashPassword(password: string) {
    return password;
  }

  /**
   * Tests equality of password and hashString
   */
  async compare(password: string, hashString: string) {
    if (password === hashString) return true;
    return false;
  }
}
```

- This is where DI becomes pretty important. We can use NestJs’s `@Injectable` to create a service and then fake it pretty easily.
- We do see some new benefits though, we can see that since the “low-fi” model doesn’t do actual hashing, you can start to imagine a more complex test using both the `hashPassword` and the `compare` methods and it would work. More on that later.

```tsx
it('should...', () => {
  const { SaltService } = jest.requireActual('./salt.service');
  // other codez
  const authService = new AuthService(
    UsersService.createFake(),
    mockedJwtService,
    SaltService.createFake(),
  );
  try {
    await authService.validateUser('email', 'wrongpassword');
    throw new Error(`should throw error: ${expectedMessage}`);
  } catch (error) {
    expect(error.message).toEqual("unable to validate user");
  }
})
```

- Since UsersService uses `password` as the default value, we can just update the password passed into `wrongpassword`. That’s nice.
- What’s not nice: what’s the right value? Yes, you have to look into the Fakes to see how they work and what their defaults are. That’s a trade-off, but it does help to write once and forever.

## Step 4: Testing `login` with 3rd Party Fake

With Mock:

```tsx

it('should return a login token', async () => {
    const mockedUsersService = jest.mocked(new UsersService({} as any));
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedSaltService = jest.mocked(new SaltService());
    mockedJwtService.sign.mockReturnValue('somestring');
    const authService = new AuthService(
      mockedUsersService,
      mockedJwtService,
      mockedSaltService,
    );
    const { access_token } = await authService.login({
      email: 'email',
      id: 1,
      roles: [],
      username: 'username',
    });
    expect(access_token).toBeDefined();
    expect(access_token).toBe('somestring');
  });
```

Notice:

- We mock the return value, usually with some dummy value
- We check against the dummy value. Again, my issue is that the general approach to mocking almost seems to promote tests like this: Useless tests!

Now The Fake Test:

```tsx
it('should return a login token', () => {
    const { UsersService } = jest.requireActual('../users/users.service');
    const { SaltService } = jest.requireActual('./salt.service');
    const { JwtService } = jest.requireActual('../lib/jwt/jwt.service');
    const authService = new AuthService(
      UsersService.createFake(),
      JwtService.createFake(),
      SaltService.createFake(),
    );
    const { access_token } = await authService.login({
      email: 'email',
      id: 1,
      roles: [],
      username: 'username',
    });
    expect(access_token).toBeDefined();
  });
```

- Create the test we want: we want a jwt fake.
- You can see that it’s straightforward code. No more complicated than mocks.

Now the JwtService

```tsx
// lib/jwt/jwt.service.ts
import { JwtService as _JwtService, JwtSignOptions } from '@nestjs/jwt';

// add to providers?
@Injectable()
export class JwtService extends _JwtService {
  static createFake() {
    return new JwtServiceFake();
  }
}

class JwtServiceFake extends JwtService {
  sign(payload: string | object | Buffer, _options?: JwtSignOptions): string {
    return `fake: ${payload.toString()}`;
  }
}
```

- You don’t have to, but it’s good to isolate dependencies. Otherwise just put it into your class file.
- Using `extends` here instead of `implements` means that we can only override the methods we’re using. Noice.
- To the small degree that I’ve seen mocks encourage bad practices, Fakes encourage good practices.

# Bonus Achievement: Deep Tests with Optional Fakes

We’ve seen how Fakes can fairly easily replace Mocks. But we’re not done. Fakes make it easy to write multi-level tests and choose ****when**** to Fake and when to use something real.

The great Achellies Heal of mocks is that they are shallow. They do not test contracts between caller and callee. Yes TS helps, but it does not test internals of the called function. Some have called these social or deep tests.

Let’s look at what that looks like.

## Step 1: Use a Real JwtService

Let’s go back to our pretty useless `login` method.

```tsx
new JwtService({ secret: 'secret' })
```

- We’ve actually just removed the fake altogether.

But there’s a snag.

- jest is still mocking the `@nestjs/jwt` dep under the hood. MOCKING SUCKS!
- Solution, break out the fakes into a new file. Eh.

We can now make the unit tests even better.

```tsx
it('should return login token', () => {
  expect(jwtService.decode(access_token)).toHaveProperty('sub');
})
```

- Actually tests that we encode, and we can test the structure of the token with something like `zod`.
- The point is, this is easily switching between Fake and Real is easy.  No fighting with the mock frameworks.

## Step 2: Social Test for Auth and User Service

“Social Tests” are basically tests with 1-level of depth. So in our case we’d test AuthService ***and*** UsersService. Fakes make this easy to do and these tests are far more effective at testing your code than traditional shallow unit tests. It’s like a chain link, if each link is strong, the whole chain is.

```tsx
// user.model.ts
export class UserModel {
  static createFake(config?: Partial<User>[]): UserModel {
    return <UserModel>(<unknown>new UserModelFake(config));
  }
}

class UserModelFake implements PublicMembersOf<UserModel> {
  constructor(private readonly config?: Partial<User>[]) {}
  /**
   * @returns all items
   */
  async findMany() {
    return <User[]>this.config;
  }
  /**
   * @returns returns the first element always
   */
  async findUniqueByEmail() {
    return <User>this.config[0];
  }

  async getPassword(_email: string) {
    return 'password';
  }
}
```

- Here we create a fake for `UserModel` which is a dependency of `UsersService`.
- We also allow a config to be passed in with SeedData. Important that it’s not deciding how to work with the data just the data itself.

```tsx
// auth.service.fakes.spec.ts
it('returns user when password is valid', async () => {
 const config = [{ email: 'email', password: 'password' }];
  const userModel = UserModel.createFake(config);
  const userService = new UsersService(userModel);

  // Service Under Test
  const authService = new AuthService(
    userService,
    new JwtService({ secret: 'secret' }),
    SaltService.createFake(),
  );
  // Test
  const { authService } = await setup();
  const user = await authService.validateUser(email, originalPassword);
  expect(user.email).toEqual(email);
});
```

## Step 3: Deep Test with a real SaltService

```tsx
describe('AuthService::DeepTest', () => {
  const saltService = new SaltService();
  const jwtService = new JwtService({ secret: 'secret' });
  const originalPassword = 'password';
  const email = 'email';

  const setup = async () => {
    // Create a valid password (it's util functions after all)
    const generatedPassword = await saltService.hashPassword(originalPassword);
    const config = [{ email: email, password: generatedPassword }];
    const userModel = UserModel.createFake(config);
    const userService = new UsersService(userModel);

    // Service Under Test
    const authService = new AuthService(userService, jwtService, saltService);
    return { authService };
  };

  it('returns user when password is valid', async () => {
    // Test
    const { authService } = await setup();
    const user = await authService.validateUser(email, originalPassword);
    expect(user.email).toEqual(email);
  });
})
```

- Maximizes use a real services, so if an api changes deep down you know all the places where it’s going to break.
- Yes, it does take a little but to setup. But again, since every class has a mock available, over time, it gets quicker and quicker to create your tests at the level of depth you desire.
- I recommend you use the code coverage metric to see where you should go deep and balance coverage and complexity.

# Conclusion

Yes, tradeoffs

Cons

- More code
- Low-Fi Fakes

Pros

- No mocking internals
- No duplicate mocks, maximize reusability
- Progressive contract testing

## Thanks
