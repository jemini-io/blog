# Fakes instead of Mocks

Start with branch: `tut/add-fakes` from <https://github.com/kingnebby/chat-app-with-turbo>

Let’s start with a normal Jest Mock

```tsx
// src/auth/test/auth.services.spec.ts

jest.mock('@nestjs/jwt');
jest.mock('../../users/users.service');
jest.mock('../salt-password');
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../../users/users.service';
import { compare } from '../salt-password';

// class under test
import { AuthService } from '../auth.service';

describe('AuthService::UnitTest::Mocks', () => {
  it('should return valid user', async () => {
    // mock your classes
    const mockedUsersService = jest.mocked(new UsersService());
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedCompare = jest.mocked(compare);

    // internals of the function are exposed and realistic values are isolated.
    mockedUsersService.getPassword.mockResolvedValue('password');
    mockedCompare.mockResolvedValue(true);
    mockedUsersService.findOne.mockResolvedValue({
      email: 'email',
      id: 1,
      username: 'username',
      usersRoles: [],
    });

    const authService = new AuthService(mockedUsersService, mockedJwtService);
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

- Yegor - Elegant Objects ([ref](https://www.yegor256.com/2014/09/23/built-in-fake-objects.html))
- James Shore ([youtube](https://www.youtube.com/watch?v=jwbKSiqG0DI))
- Bob Martin ([ref](https://blog.cleancoder.com/uncle-bob/2014/05/10/WhenToMock.html))

## Replace the UsersService Mock with a UsersServiceFake

Create the world we want to live in.

```tsx
// src/auth/test/auth.services.spec.ts

describe('AuthService::UnitTest::Fakes', () => {
  it('should return valid user', async () => {
    const { UsersService } = jest.requireActual('../../users/users.service');
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedCompare = jest.mocked(compare);

    mockedCompare.mockResolvedValue(true);

    const authService = new AuthService(
      UsersService.createFake(), // new
      mockedJwtService,
    );
    const user = await authService.validateUser('email', 'password');
    expect(user).toHaveProperty('email');
    expect(user).not.toHaveProperty('password');
  });
});
```

- Require a real UsersService and create a Fake.

```tsx
// user.service.ts
class UsersServiceFake implements UsersService {}
```

Create the class and then implement the methods.

```tsx
// user.service.ts
class UserService {
  static createFake(): UsersService {
    return new UsersServiceFake();
  }
}
```

Add the fake so we can see the tests failing.

```tsx
// user.service.ts
class UsersServiceFake implements UsersService {
  fakeUsers: User[] = [
    {
      email: 'email',
      id: 1,
      password: 'password',
      username: 'username',
      usersRoles: [],
    },
  ];
  // other methods
  async getUsers(): Promise<User[]> {
    throw new Error('Method not implemented.');
  }
  async findOne(email: string): Promise<UserType> {
    return this.fakeUsers[0] as User;
  }
  async getPassword(email: string): Promise<string> {
    return this.fakeUsers[0].password;
  }
}
```

1. Add seed users value
2. Implement a Lo-Fi version of the Service.

Tradeoffs

- Yes, you could do this with abstract classes or interfaces or whatever. But this type casting works fine for me and for this video.
- Yes, this is “test” code in your production code. If you can stomach that, consider that users of your code can also reuse your Fake for their tests.

## An Unhappy Path ⇒ User Not Found

Consider the Mocked version of the test.

```tsx
// auth.service.spec.ts

// ...
  it('should fail when user is not found', async () => {
    // mock your classes
    const mockedUsersService = jest.mocked(new UsersService());
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedCompare = jest.mocked(compare);

    //
    // Simulate a failure
    //
    mockedUsersService.getPassword.mockRejectedValue(
      new Error('could not find user'),
    );
    mockedCompare.mockResolvedValue(true);
    mockedUsersService.findOne.mockResolvedValue({
      email: 'email',
      id: 1,
      username: 'username',
      usersRoles: [],
    });

    const authService = new AuthService(mockedUsersService, mockedJwtService);
    const expectedMessage = 'could not find user';
    try {
      await authService.validateUser('email', 'password');
      throw new Error(`should throw error: ${expectedMessage}`);
    } catch (error) {
      expect(error.message).toEqual(expectedMessage);
    }
  });
```

Now the Fake version

```tsx
// user.service.ts

  it('should fail when user is not found', async () => {
    const { UsersService } = jest.requireActual('../../users/users.service');
    const mockedJwtService = jest.mocked(new JwtService());
    const mockedCompare = jest.mocked(compare);

    mockedCompare.mockResolvedValue(true);

    const authService = new AuthService(
      UsersService.createFake(),
      mockedJwtService,
    );
    const expectedMessage = 'could not find user';
    try {
      await authService.validateUser('wrongemail', 'password');
      throw new Error(`should throw error: ${expectedMessage}`);
    } catch (error) {
      expect(error.message).toEqual(expectedMessage);
    }
  });
```

In the Faked version:

- Since changing to `wrongemail` doesn’t fail as we expect, we have to update the Fake.

```tsx
// users.service.ts

class UsersServiceFake implements UsersService {
  async findOne(email: string): Promise<UserType> {
    return this.fakeUsers.find((user) => user.email === email);
  }
  async getPassword(email: string): Promise<string> {
    const user = this.fakeUsers.find((user) => user.email === email);
    if (user) {
      return user.password;
    }
    throw new Error('could not find user'); // TODO: make errors consistent
  }
}
```

- Tradeoff: You have to either implement the method like so, or pass in some configuration in the constructor. Key is: don’t expose the internals. Don’t treat it like mock. Don’t pass in “returnValueOfGetPassword” or something. We’ll see later why.
- But, this test is *************more real.************* So the interactions of the AuthService will be slightly more real. We’ll expand on this later.
  - We actually don’t need to mock if `findOne` returns a bad response, because if the userService code works then if `getPassword` works, `findOne` should also work.

The next natural test to write would be to validate what happens when the `compare` method fails. Here we run into a problem though, `salt-password` is a module of util methods not an injectable class. We could convert it to class and inject it as a NestJs Provider and then fake it the same way we did UsersService. In fact, if you want to practice your faker skills, go do that.

But we’re going to leave that problem for now and come back to it later. For now, we’ll allow mocking to cover that case for us. Hmmm. Maybe there IS a valid case for Mocks…. Nahhhhh.

## Testing `login` with a 3rd Party Fake

First, let’s show the Mock version.

```tsx
// auth.service.spec.ts

  it('should return a login token', async () => {
    const mockedUsersService = jest.mocked(new UsersService());
    const mockedJwtService = jest.mocked(new JwtService());

    // setup test
    mockedJwtService.sign.mockReturnValue('somestring');

    const authService = new AuthService(mockedUsersService, mockedJwtService);
    const { access_token } = await authService.login({
      email: 'email',
      id: 1,
      roles: [],
      username: 'username',
    });
    expect(access_token).toBeDefined();
    expect(access_token).toBe('somestring');
  });
});
```

- We mock the return value, usually with some dummy value
- We check against the dummy value. Again, my issue is that the general approach to mocking almost seems to promote tests like this: Useless tests!

Now the Fake.

```tsx
// auth.service.spec.ts
  
  it('should return a login token', async () => {
    const { UsersService } = jest.requireActual('../../users/users.service');
    const { JwtService } = jest.requireActual('@nestjs/jwt');

    const authService = new AuthService(
      UsersService.createFake(),
      JwtService.createFake(),
    );
    const { access_token } = await authService.login({
      email: 'email',
      id: 1,
      roles: [],
      username: 'username',
    });
    expect(access_token).toBeDefined();
    expect(access_token).toBe('jwt-token');
  });
```

- You can see that it’s straightforward code. No more complicated than mocks.
- This fails, because don’t handle types cleanly.

We need to wrap the JwtService in a class. This is a pattern known as NullableInfrastructure and has it’s own benefits.

```tsx
// src/auth/utils/jwt.service.ts

import { Injectable } from '@nestjs/common';
import { JwtService as _JwtService, JwtSignOptions } from '@nestjs/jwt';

// add to providers?
@Injectable()
export class JwtService extends _JwtService {
  static createFake() {
    return new JwtServiceFake();
  }
}

class JwtServiceFake extends JwtService {
  sign(_payload: string | object | Buffer, _options?: JwtSignOptions): string {
    return `jwt-token`;
  }
}
```

- Using `extends` here instead of `implements` means that we can only override the methods we’re using. Noice.
- To the small degree that I’ve seen mocks encourage bad practices, Fakes encourage good practices.

Update the `auth.module.ts` to use the provider and `auth.service.ts` to inject it.

Then update the `jest.requireActual()` to use the proper `JwtService`.

# Deep Tests with Optional Fakes

We’ve seen how Fakes can fairly easily replace Mocks. But we’re not done. Fakes make it easy to write multi-level tests and choose ****when**** to Fake and when to use something real.

The great Achilles Heal of mocks is that they are shallow. They do not test contracts between caller and callee. Yes TS helps, but it does not test internals of the called function. Some have called these social or deep tests.

“Social Tests” are basically tests with 1-level of depth. So in our case we’d test AuthService ***and*** UsersService. Fakes make this easy to do and these tests are far more effective at testing your code than traditional shallow unit tests. It’s like a links in a chain, if each link is strong, the whole chain is.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6df61ddf-555e-412e-b7e0-c212cf235aff/Untitled.png)

Let’s look at what that looks like.

## Sociable Test for Auth and User Service

We’ll return to

AuthService →

UsersService →

Prisma (Model);

We start with the world we want to live in

```tsx
// auth.service.fakes.spec.ts
describe('AuthService::UnitTest::DeepFakes', () => {
  it('should return a valid user', async () => {
    const { JwtService } = jest.requireActual('../utils/jwt.service');

    // Service Under Test
    const prisma = PrismaClientProvider.createFake();
    const authService = new AuthService(
      new UsersService(prisma),
      JwtService.createFake(),
    );
    // Test
    const user = await authService.validateUser('email', 'password');
    expect(user).toHaveProperty('email');
    expect(user).not.toHaveProperty('password');
  });
});
```

- Happy Path test
- See that we want to use a real `UsersService`and pass in it’s private member of prisma.

We need to create the Injectable prisma client, same way we did the JwtService.

```tsx
// src/user/utils/prisma.provider.ts

import { PrismaClient, User } from '.prisma/client';
import { Injectable } from '@nestjs/common';

@Injectable()
export class PrismaClientProvider extends PrismaClient {
  static createFake(): PrismaClientProvider {
    return <PrismaClientProvider>(<unknown>new PrismaUsersClientFake());
  }
}

class PrismaUsersClientFake implements Partial<PrismaClientProvider> {
  data: Partial<User[]> = [
    {
      email: 'email',
      id: 1,
      password: 'password',
      username: 'username',
      usersRoles: [],
    },
  ];

  get user() {
    const userModel: unknown = <Partial<PrismaUserDAO>>{
      findMany(_query) {
        return this.data;
      },
      findUnique: async (query) => {
        return this.data.find((el) => el.email === query.where.email);
      },
    };

    return <PrismaUserDAO>userModel;
  }
}
type PrismaUserDAO = typeof PrismaClient.prototype.user;
```

- There’s a bit of interesting TS stuff going on here that I’m going to skip for sake of staying on the topics of Fakes. Ask questions in the comments if you want more detail on what’s going on here.
  - If your a TS expert, show me how to do this better!

Key Takeaways

- We use `extends` again. Remember we could use `interface` instead.
- We’re going to allow a config to be passed in to set our seed data. It’s important that we don’t allow `mockUserResponse` but rather accept raw data that our Fake will manipulate as it sees fit.
- `Injectable()`

Inject the new Service in our UserService

```tsx
// src/users/users.service.ts

export class UsersService {
  constructor(private readonly prisma: PrismaClient) {}

  static createFake(): UsersService {
    return <UsersService>(<unknown>new UsersServiceFake());
  }

  // prisma => this.prisma
}

class UsersServiceFake implements PublicMembersOf<UsersService> {
  // ...
}

// TODO: make a generic utility
type PublicMembersOf<T> = { [K in keyof T]: T[K] };
```

- Now that we are faking a class with a `private` member, we have to do some funky stuff in TS because of how Javascript fundamentally treats private members.

Update your tests to use the new PrismaClientProvider

- Import at the top.
- Mocks use `null`

TESTS SHOULD PASS!

Let’s write the test for when the email is wrong.

```tsx
// auth.service.spec.ts

  it('should fail when user is not found', async () => {
    const { JwtService } = jest.requireActual('../utils/jwt.service');
    const { UsersService } = jest.requireActual('../../users/users.service');

    // Service Under Test
    const prisma = PrismaClientProvider.createFake();
    const authService = new AuthService(
      new UsersService(prisma),
      JwtService.createFake(),
    );
    // Test
    const expectedMessage = 'could not find user';
    try {
      await authService.validateUser('wrongemail', 'password');
      throw new Error(`should throw error: ${expectedMessage}`);
    } catch (error) {
      expect(error.message).toEqual(expectedMessage);
    }
  });
```

- This fails for the same reason as before, our Prisma Fake needs to throw the error

```tsx
// prisma.service.ts
class PrismaUsersClientFake implements Partial<PrismaClientProvider> {
  get user() {
    const userModel: unknown = <Partial<PrismaUserDAO>>{
     findUnique: async (query) => {
        const data = this.data.find((el) => el.email === query.where.email);
        if (data) {
          return data;
        }
        // TODO: Can use the prisma types to type these errors?
        throw new Error('could not find user');
      },
    }
  }
}
```

With that fixed, our tests pass and we’ve completed our first deep fake test.

So, we can write tests with Fakes instead of mocks, except that pesky compare method… I haven’t forgotten about you sir. But, what if… we could write tests without mocks AND without fakes??

Maybe next time.
