## How to start `Prisma` in `node.js`

### 1. Install `Prisma` in your project
```bash
npm -i --save-dev prisma
```

### 2. Initialize `Prisma`
```bash
npx prisma init
```

### 3. Set environmental variables in the `.env` file
```json
DATABASE_URL="prisma+postgres://accelerate.prisma-data.net/?api_key=ey..."
```

### 4. Set a model in `schema.prisma` file
```sql
model UserAccount {
  id        Int       @id @default(autoincrement())
  email     String    @unique
  password  String
  createdAt DateTime  @default(now())
  updatedAt DateTime?
}
```

### 5. Apply the Schema to the database and Create a migration file(a track of database schema changes)
```bash
npx prisma migrate dev name --init
```

### 6. Install `Prisma Client`, which is an api you can use for query.
```bash
npm install @prisma/client
```

### 7. Create a javscript file `script.js` for testing your schema and run it
```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

async function main() {
  const user = await prisma.user.create({
    data: {
      email: 'alice@prisma.io',
    },
  });
  console.log(user);
}

main()
  .then(async () => {
    await prisma.$disconnect();
  })
  .catch(async (e) => {
    console.error(e);
    await prisma.$disconnect();
    process.exit(1);
  });
```
### Execute the above code with the following command
```bash
node script.js
```

### 8. After making some chnages or adding more tables, run the following code
```bash
npx prisma migrate dev
```


### Tips
If you want to see a log for debugging, using the following options
```javascript
const prisma = new PrismaClient({log : ["query"]});
```

If you want to apply your schema without creating a migration file:
```bash
npx prisma db push
```