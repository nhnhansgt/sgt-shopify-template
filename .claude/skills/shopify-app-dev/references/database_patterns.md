# Database Patterns Reference

## Prisma Client Singleton

```typescript
// app/db.server.ts
import { PrismaClient } from "@prisma/client";

let prisma: PrismaClient;
declare global { var __db__: PrismaClient; }

if (process.env.NODE_ENV === "production") {
  prisma = new PrismaClient();
} else {
  if (!global.__db__) global.__db__ = new PrismaClient();
  prisma = global.__db__;
}

export default prisma;
```

## Basic Operations

### Query

```typescript
import prisma from "~/app/db.server";

// Find all
const sessions = await prisma.session.findMany();

// With filter and pagination
const activeSessions = await prisma.session.findMany({
  where: { isActive: true },
  skip: 0,
  take: 10,
  orderBy: { createdAt: "desc" }
});

// Find one
const session = await prisma.session.findUnique({
  where: { shop: "test.myshopify.com" }
});
```

### Create

```typescript
const newSession = await prisma.session.create({
  data: { shop: "test.myshopify.com", state: "active", accessToken: "token" }
});
```

### Update

```typescript
const updated = await prisma.session.update({
  where: { shop: "test.myshopify.com" },
  data: { accessToken: "new_token" }
});
```

### Upsert

```typescript
const session = await prisma.session.upsert({
  where: { shop: "test.myshopify.com" },
  update: { accessToken: "new_token" },
  create: { shop: "test.myshopify.com", state: "active", accessToken: "new_token" }
});
```

### Delete

```typescript
await prisma.session.delete({ where: { shop: "test.myshopify.com" } });
await prisma.session.deleteMany({ where: { isActive: false } });
```

## Relations

```typescript
const shop = await prisma.shop.findUnique({
  where: { domain: "test.myshopify.com" },
  include: { sessions: true, settings: true }
});
```

## Aggregations

```typescript
const count = await prisma.order.count({ where: { shop: "test.myshopify.com" } });

const stats = await prisma.order.aggregate({
  _sum: { totalPrice: true },
  _avg: { totalPrice: true },
  _count: true,
  where: { shop: "test.myshopify.com" }
});
```

## Transactions

```typescript
const result = await prisma.$transaction(async (tx) => {
  const session = await tx.session.update({
    where: { shop }, data: { accessToken: newToken }
  });
  await tx.auditLog.create({
    data: { shop, action: "TOKEN_REFRESH", timestamp: new Date() }
  });
  return session;
});
```

## Schema Examples

```prisma
// prisma/schema.prisma
model Session {
  id          String   @id @default(cuid())
  shop        String   @unique
  state       String
  accessToken String?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Order {
  id          String   @id @default(cuid())
  orderId     String   @unique
  shop        String
  orderNumber Int
  totalPrice  Float
  status      String   @default("pending")
  createdAt   DateTime @default(now())
}
```

## Commands

```bash
npx prisma generate           # Generate client
npx prisma migrate dev --name <desc>  # Create migration
npx prisma migrate deploy     # Apply migrations (prod)
npx prisma migrate reset      # Reset database (dev)
npx prisma studio             # Database GUI
```

## Error Handling

```typescript
import { Prisma } from "@prisma/client";

try {
  await prisma.session.create({ data: { shop, accessToken } });
} catch (error) {
  if (error instanceof Prisma.PrismaClientKnownRequestError) {
    if (error.code === "P2002") {
      console.error("Unique constraint violation");
    }
  }
  throw error;
}
```

**See also:**
- [code_patterns.md](code_patterns.md) - Authentication, routes
- [graphql_patterns.md](graphql_patterns.md) - GraphQL queries
