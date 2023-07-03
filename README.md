# Template-PERN

+ Prisma (postgreSQL)
+ Express
+ React
+ Node

## How to Use

run from terminal :
```
npm install
npx prisma
npx prisma init
```

configure your own .env & schema.prisma
```
npx prisma migrate dev --name init
```

configure commitlint
```
npm run prepare
npm pkg set scripts.commitlint="commitlint --edit"
npx husky add .husky/commit-msg 'npm run commitlint ${1}'
```

configure react js
```
cd frontend
npm install
npm run start:react
```

edit your react app as you need, if you've finished run :
```
npm run build:react
```

# Create Vanilla Fullstack From Scratch

## Connect Database

```
npm init --y
npm install prisma --save-dev
npx prisma
npx prisma init
```
it creates .env file, edit :
```dotenv
DATABASE_URL="postgresql://{username}:{pass}@localhost:5432/{db-name}?schema={schema}"
```
and also prisma/schema.prisma, add these lines to migrate. this is only example :

```prisma
model Post {
  id        Int      @id @default(autoincrement())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title     String   @db.VarChar(255)
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}

model Profile {
  id     Int     @id @default(autoincrement())
  bio    String?
  user   User    @relation(fields: [userId], references: [id])
  userId Int     @unique
}

model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  name    String?
  posts   Post[]
  profile Profile?
}
```
run terminal :
```
npx prisma migrate dev --name init
npm install @prisma/client
```
test db by crate .js file 
```javascript
const { PrismaClient } = require('@prisma/client')

const prisma = new PrismaClient()

async function checkAllUsers() {
    // ... you will write your Prisma Client queries here
    const allUsers = await prisma.user.findMany()
    console.log(allUsers)
}

async function createUser() {
    await prisma.user.create({
        data: {
            name: 'Alice',
            email: 'alice@prisma.io',
            posts: {
                create: { title: 'Hello World' },
            },
            profile: {
                create: { bio: 'I like turtles' },
            },
        },
    })

    const allUsers = await prisma.user.findMany({
        include: {
            posts: true,
            profile: true,
        },
    })
    console.dir(allUsers, { depth: null })
}
checkAllUsers()
    .then(async () => {
        await prisma.$disconnect()
    })
    .catch(async (e) => {
        console.error(e)
        await prisma.$disconnect()
        process.exit(1)
    })

createUser()
    .then(async () => {
        await prisma.$disconnect()
    })
    .catch(async (e) => {
        console.error(e)
        await prisma.$disconnect()
        process.exit(1)
    })

```

## Create Frontend (React js)
run from terminal : 
```
npx create-react-app frontend
cd frontend
npm run build
```

### Frontend css framework (tailwind css)
run from terminal inside frontend folder :
```
npm install -D tailwindcss
npx tailwindcss init
```
create tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
add these lines to src/index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```
Edit your pages, last you need to run :
```
npm run build
```

## Create Backend (Node js + Express)

run from terminal :
```
npm i express
npm i nodemon --save -dev
```

create server using express.js
```javascript
const express = require("express")
const app = express()
const path = require("path")
const port = 3000;

app.use(express.json())
app.use(express.static(path.join('frontend', 'build')))
app.use(express.urlencoded({extended : true}))

app.get('/', (req,res) =>{
    res.status(200)
        .sendFile(path.join('frontend', 'build', 'index.html'))
})

app.listen(port, () => console.log('server is running on http://localhost:' + port))
```
