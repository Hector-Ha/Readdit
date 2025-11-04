# Readdit - A Reddit Clone

A production-ready, full-stack Reddit clone built with modern technologies. Features user authentication, post management, real-time sessions, and a responsive UI. Built entirely with TypeScript for end-to-end type safety.

## 🚀 Features

### User Authentication & Authorization

- **User Registration** with email, username, and password validation
- **Login System** with support for username or email
- **Session Management** using Redis for fast, scalable sessions
- **Password Reset** functionality via email with token expiration
- **Protected Routes** with middleware authentication
- **Secure Password Hashing** using Argon2 algorithm
- **HTTP-only Cookies** for enhanced security

### Post Management

- **Create Posts** with title and rich text content
- **View All Posts** with server-side rendering for SEO
- **Authentication Required** for post creation
- **User Association** - posts linked to authors
- **Timestamps** for creation and updates

### User Experience

- **Responsive Design** that works on all devices
- **Form Validation** with real-time error messages
- **Loading States** for better user feedback
- **Navigation Bar** with dynamic login/logout
- **Server-Side Rendering** for faster initial page loads
- **Client-Side Caching** with URQL for optimal performance

## 🛠️ Tech Stack

### Backend Architecture

- **Node.js** - JavaScript runtime
- **Express.js** - Web application framework
- **TypeScript** - Static typing for JavaScript
- **Apollo Server Express** - GraphQL server implementation
- **Type-GraphQL** - TypeScript-first GraphQL schema definition
- **TypeORM** - Object-Relational Mapping for database operations
- **PostgreSQL** - Relational database for data persistence
- **Redis** - In-memory data store for session management
- **Argon2** - Modern password hashing algorithm
- **Nodemailer** - Email sending for password resets
- **Express Session** - Session middleware
- **Connect-Redis** - Redis session store for Express
- **CORS** - Cross-origin resource sharing support

### Frontend Architecture

- **Next.js** - React framework with SSR and routing
- **React** - UI component library
- **TypeScript** - Type safety on the frontend
- **URQL** - Lightweight GraphQL client with caching
- **Chakra UI** - Accessible component library
- **Formik** - Form management and validation
- **GraphQL Code Generator** - Auto-generate TypeScript types from schema
- **Next-URQL** - URQL integration for Next.js with SSR support

### Development Tools

- **Nodemon** - Auto-restart server on changes
- **ts-node** - TypeScript execution for Node.js
- **GraphQL Playground** - Interactive GraphQL API explorer

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14.0.0 or higher) - [Download](https://nodejs.org/)
- **npm** (v6.0.0 or higher) - Comes with Node.js
- **PostgreSQL** (v12.0 or higher) - [Download](https://www.postgresql.org/download/)
- **Redis** (v6.0.0 or higher) - [Download](https://redis.io/download)

### Optional but Recommended

- **Postman** or **Insomnia** - For API testing
- **pgAdmin** or **DBeaver** - For database management
- **Redis Commander** - For Redis data visualization

## 🔧 Installation & Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd fullstack-reddit-clone
```

### 2. Install Server Dependencies

```bash
cd server
npm install
```

### 3. Install Client Dependencies

```bash
cd ../client
npm install
```

### 4. Configure Environment Variables

Create a `.env` file in the `server` directory:

```env
# Application Environment
NODE_ENV=development

# PostgreSQL Configuration
POSTGRESQL_USER=your_postgres_username
POSTGRESQL_PASSWORD=your_postgres_password

# Session Configuration
SESSION_SECRET=your_super_secret_session_key_change_this_in_production

# Email Configuration (use Ethereal for testing)
NODE_MAILER_USER=your_ethereal_email@ethereal.email
NODE_MAILER_PASSWORD=your_ethereal_password
```

**To get Ethereal email credentials:**

1. Visit [Ethereal Email](https://ethereal.email/)
2. Click "Create Ethereal Account"
3. Copy the username and password to your .env file

### 5. Database Setup

**Create the database:**

```bash
# Login to PostgreSQL
psql -U postgres

# Create database
CREATE DATABASE lireddit2;

# Exit PostgreSQL
\q
```

**Note:** TypeORM will automatically create tables on first run due to `synchronize: true` in development.

### 6. Start Redis Server

**On macOS (with Homebrew):**

```bash
brew services start redis
```

**On Linux:**

```bash
sudo systemctl start redis
```

**On Windows:**

```bash
redis-server
```

**Verify Redis is running:**

```bash
redis-cli ping
# Should return: PONG
```

## 🚀 Running the Application

### Development Mode

You'll need **three terminal windows**:

**Terminal 1 - TypeScript Compiler (Server):**

```bash
cd server
npm run watch
```

**Terminal 2 - Node Server:**

```bash
cd server
npm run dev
```

**Terminal 3 - Next.js Client:**

```bash
cd client
npm run dev
```

### Access the Application

- **Frontend:** http://localhost:3000
- **GraphQL Playground:** http://localhost:4000/graphql
- **GraphQL API Endpoint:** http://localhost:4000/graphql

### Production Mode

**Build and start the server:**

```bash
cd server
npm start
```

**Build and start the client:**

```bash
cd client
npm run build
npm start
```

## 📁 Project Structure

```
Readdit/
│
├── server/                      # Backend application
│   ├── src/
│   │   ├── entities/           # TypeORM entities (User, Post)
│   │   ├── resolvers/          # GraphQL resolvers
│   │   ├── middleware/         # Custom middleware (isAuth)
│   │   ├── utils/              # Utility functions
│   │   ├── types.ts            # TypeScript definitions
│   │   ├── constants.ts        # Application constants
│   │   └── index.ts            # Server entry point
│   ├── tsconfig.json
│   ├── package.json
│   └── .env                    # Environment variables
│
├── client/                      # Frontend application
│   ├── src/
│   │   ├── components/         # React components
│   │   ├── pages/              # Next.js pages/routes
│   │   ├── graphql/            # GraphQL operations
│   │   ├── generated/          # Auto-generated types
│   │   ├── utils/              # Utility functions
│   │   └── theme.tsx           # Chakra UI theme
│   ├── tsconfig.json
│   ├── codegen.yml
│   └── package.json
│
└── README.md
```

## 🔑 Key Features Implementation

### Authentication Flow

#### Registration

1. User submits registration form with username, email, and password
2. Frontend validates input using Formik
3. GraphQL mutation sent to server with credentials
4. Server validates:
   - Username length (minimum 4 characters)
   - Username doesn't contain '@' symbol
   - Email contains '@' symbol
   - Password length (minimum 6 characters)
5. Password hashed using Argon2
6. User stored in PostgreSQL database
7. Session created and stored in Redis
8. Session ID sent as HTTP-only cookie
9. User redirected to home page

#### Login

1. User submits username/email and password
2. Server checks if input contains '@' to determine lookup field
3. User retrieved from database
4. Password verified using Argon2
5. Session created on successful authentication
6. User redirected to originally requested page or home

#### Password Reset

1. User requests password reset with email
2. Server generates unique UUID token
3. Token stored in Redis with 3-day expiration: `forget-password:{token}`
4. Email sent with reset link: `http://localhost:3000/change-password/{token}`
5. User clicks link and enters new password
6. Server validates token from Redis
7. Password updated in database
8. Token deleted from Redis
9. User automatically logged in
10. Redirect to home page

### Session Management

Sessions are managed using Redis for optimal performance:

```typescript
// Session Configuration
{
  name: 'qid',                    // Cookie name
  store: RedisStore,              // Redis session store
  cookie: {
    maxAge: 1000 * 60 * 60 * 24 * 365,  // 1 year
    httpOnly: true,               // Prevents JavaScript access
    sameSite: 'lax',             // CSRF protection
    secure: __prod__              // HTTPS only in production
  },
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false
}
```

### GraphQL API Design

The API follows best practices for GraphQL:

#### Queries

- `me` - Get current authenticated user
- `posts` - Get all posts
- `post(id)` - Get single post by ID
- `hello` - Health check endpoint

#### Mutations

- `register(options)` - Create new user account
- `login(usernameOrEmail, password)` - Authenticate user
- `logout` - End user session
- `forgotPassword(email)` - Initiate password reset
- `changePassword(token, newPassword)` - Complete password reset
- `createPost(input)` - Create new post (requires authentication)
- `updatePost(id, title)` - Update existing post
- `deletePost(id)` - Delete post

#### Type Definitions

```graphql
type User {
  id: Float!
  username: String!
  email: String!
  createdAt: String!
  updatedAt: String!
}

type Post {
  id: Float!
  title: String!
  text: String!
  points: Float!
  authorId: Float!
  createdAt: String!
  updatedAt: String!
}

type UserResponse {
  errors: [FieldError!]
  user: User
}

type FieldError {
  field: String!
  message: String!
}
```

### Database Schema

#### Users Table

```sql
CREATE TABLE "user" (
  id SERIAL PRIMARY KEY,
  username VARCHAR UNIQUE NOT NULL,
  email VARCHAR UNIQUE NOT NULL,
  password VARCHAR NOT NULL,
  "createdAt" DATE NOT NULL DEFAULT CURRENT_DATE,
  "updatedAt" TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

#### Posts Table

```sql
CREATE TABLE "post" (
  id SERIAL PRIMARY KEY,
  title VARCHAR NOT NULL,
  text VARCHAR NOT NULL,
  points INTEGER NOT NULL DEFAULT 0,
  "authorId" INTEGER NOT NULL,
  "createdAt" TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  "updatedAt" TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY ("authorId") REFERENCES "user"(id)
);
```

### Protected Routes

The `isAuth` middleware protects routes requiring authentication:

```typescript
// Server-side protection
@Mutation(() => Post)
@UseMiddleware(isAuth)
async createPost(@Arg('input') input: PostInput) {
  // Only authenticated users can reach this
}

// Client-side protection
const CreatePost = () => {
  useIsAuth(); // Redirects to login if not authenticated
  // ... component code
};
```

### Server-Side Rendering (SSR)

Posts are server-side rendered for SEO benefits:

```typescript
export default withUrqlClient(createUrqlClient, { ssr: true })(Index);
```

Benefits:

- Faster initial page load
- Better SEO for post content
- Social media preview cards work correctly

### Client-Side Caching

URQL cache is updated after mutations for instant UI updates:

```typescript
// After login, update 'me' query cache
betterUpdateQuery<LoginMutation, MeQuery>(
  cache,
  { query: MeDocument },
  result,
  (result, query) => {
    if (result.login.errors) {
      return query;
    } else {
      return { me: result.login.user };
    }
  }
);
```

## 📝 Available Scripts

### Server Scripts

```bash
npm run watch      # Run TypeScript compiler in watch mode
npm run dev        # Start development server with auto-restart
npm start          # Start production server (TypeScript execution)
```

### Client Scripts

```bash
npm run dev        # Start Next.js development server
npm run build      # Build optimized production bundle
npm start          # Start production server
npm run gen        # Generate TypeScript types from GraphQL schema
```

## 🔐 Security Features

### Password Security

- **Argon2 Hashing** - Memory-hard algorithm resistant to GPU attacks
- **Automatic Salting** - Unique salt for each password
- **Minimum Length** - 6 character minimum requirement

### Session Security

- **HTTP-Only Cookies** - Inaccessible to JavaScript (XSS protection)
- **SameSite Attribute** - CSRF attack prevention
- **Secure Flag** - HTTPS-only transmission in production
- **Redis Storage** - Fast, secure session storage
- **Session Expiration** - 1-year maximum session lifetime

### Input Validation

- **Server-Side Validation** - Never trust client input
- **Username Requirements** - No '@' symbol, minimum 4 characters
- **Email Validation** - Must contain '@' symbol
- **Password Requirements** - Minimum 6 characters
- **SQL Injection Protection** - TypeORM parameterized queries

### Token Security

- **UUID Tokens** - Cryptographically random
- **Time-Limited** - 3-day expiration for password reset
- **Single-Use** - Token deleted after successful use
- **Redis Storage** - Fast expiration and cleanup

## Upcoming Features

- [ ] Upvote/downvote system
- [ ] Comments on posts
- [ ] User profiles
- [ ] Post pagination
- [ ] Search functionality
- [ ] Email verification
- [ ] OAuth authentication (Google, GitHub)
- [ ] Post categories/subreddits
- [ ] Image upload support
- [ ] Rich text editor
- [ ] Dark mode

## Support

The project is provided as is
