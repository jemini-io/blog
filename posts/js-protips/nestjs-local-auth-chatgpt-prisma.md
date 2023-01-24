# Local Auth and Chat GPT

I want to see how good Chat GPT is, let’s see if it can help us add a local-auth strategy to a nestJs application.

## Validate our current setup

- You should have run through the nestjs authorization tutorial
- POSTMAN to validate that things kinda sorta work.

## Setup a Database

```yaml
# docker-compose.yaml
version: '3.1'

services:

  db:
    image: postgres
    restart: always
    environment:
      POSTGRES_PASSWORD: example
    ports:
      - 5432:5432

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

Add this docker-compose file to run postgres locally.

The default user is `postgres` and the default database is the name of the default user, again: `postgres`.

Validate that everything is working with adminer.

## Create the New Database Model

```bash
pnpm i prisma
pnpm i -D @prisma/client
pnpx prisma init
```

Creates a new prisma file.

## Create the Database Model

```prisma
# prisma/schema.prisma
model User {
  userId     Int      @id @default(autoincrement())
  password   String
  username   String   @unique
}
```

Create a new User model in your schema.prisma file.

```bash
# .env
DATABASE_URL="postgresql://postgres:example@localhost:5432/postgres?schema=public"
```

Also, update your `.env` to include the proper URL to the database.

```bash
pnpx prisma db push
```

Ensure your database has been updated to match the schema defined in your prisma model.

## Seed Users in the Database

```bash
pnpx prisma generate
```

```tsx
// prisma/seed.ts

import { PrismaClient } from '@prisma/client';
import { hashPassword } from '../src/auth/salt-password';
const prisma = new PrismaClient();
async function main() {
  // create user
  await prisma.user.createMany({
    data: [
      {
        username: 'john',
        password: 'changeme',
      },
      {
        username: 'maria',
        password: 'guess',
      },
    ],
  });
}

main()
  .then(() => prisma.$disconnect())
  .catch((e) => {
    console.log(e);
    prisma.$disconnect();
  });
```

This is a script that prisma will call when seeding data. But, prisma doesn’t know it’s here by default, we have to tell it in our package.json.

```bash
"prisma": {
  "seed": "ts-node --transpile-only prisma/seed.ts"
}

npx prisma db seed
```

BUT WAIT! This password is not hashed.

## Ask Chat GPT: How to Hash

```bash
npm i -g gpt3-cli
npx gpt auth
pnpx gpt "how do I hash a password?"
```

Do this until you finally get some code.

```tsx
// pnpm i bcrypt @types/bcrypt

// src/auth/passwords-util.ts
import { hash } from 'bcrypt';
export async function hashPassword(password: string) {
  const saltRounds = 10;
  const hashedPassword = await hash(password, saltRounds);
  return hashedPassword;
}
```

Add this file and use it when seeding users in the `prisma/seed.ts` file.

Can use `prisma migrate dev` and then `prisma migrate reset` to bootstrap your db.

According to ChatGPT we’re kind of done here.

## Setup Auth to Use the Database

```tsx
// auth.service.ts - v1
async validateUser(username: string, pass: string): Promise<any> {
    const user = await this.usersService.findOne(username);
    if (hashPassword(pass) === user.password) {
      return user
    }
    Logger.error({ username }, 'passwords do not match');
    throw new Error('unable to validate users');
  }
```

We might do something simple like look up the user and check the incoming password with the password provided.

```tsx
// src/users/user.service.ts - v1
async findOne(email: string): Promise<UserType> {
    const user = await prisma.user.findUnique({
      where: { email: email },
    });
    return user;
  }
```

The `user.service.ts` also will simply use the prisma cli to find a user.

However, this doesn’t work.

### Ask ChatGPT Again

```bash
npx gpt "how do i validate a hash?"
```

## A Better Auth Solution

```tsx
// src/auth/salt-passwords.ts
import { hash, _compare } from 'bcrypt';
export async function compare(password: string, hashString: string) {
  return await _compare(password, hashString);
}
```

This is how bcrypt wants us to compare hashes.

```tsx
// auth.service.ts
async validateUser(username: string, pass: string): Promise<any> {
  const dbPassword = await this.usersService.getPassword(username);
  if (await compare(pass, dbPassword)) {
  return await this.usersService.findOne(_email);
  }
  Logger.error({ username }, 'passwords do not match');
  throw new Error('unable to validate users');
}
```

- Uses `compare`

## Extra Credit: Don’t Leak the Password

- Uses `getPassword` and `findOne` that helps us not leak sensitive data.

```tsx
// user.service.ts
export type UserType = Omit<User, 'password'>;
export class UsersService {

  async findOne(email: string): Promise<UserType> {
    const user = await prisma.user.findUnique({
      where: { email: email },
    });

  // ensure the password is removed!
    return exclude(user, ['password']);
  }

  // ensure it's clear the password is here
  async getPassword(email: string) {
    const user = await prisma.user.findUnique({ where: { email: email } });
    return user.password;
  }
}

// TODO: make a generic utility
function exclude<T, Key extends keyof T>(object: T, keys: Key[]): Omit<T, Key> {
  for (const key of keys) {
    delete object[key];
  }
  return object;
}
```
