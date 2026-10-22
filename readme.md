# Cooklio - Cooking Assistant 🍲

A full-stack web application that helps users discover recipes, explore meal ideas, save favorites, and organize ingredients into a personal shopping list. The project combines a React frontend with a Node.js/Express backend and MongoDB, with support for recipe search, external recipe data, personalized food preferences, and AI-generated recipe streaming.

## 🚀 Deployment

🔗 [Cooklio - Cooking Assistant](https://quick-healthy-meal-planner.vercel.app/)

🔗 [Portfolio](https://anoop-kumar-singh.vercel.app/)



## Table of Contents

- [Features](#features)
- [Technology Stack](#technology-stack)
- [Architecture](#architecture)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Running the Application](#running-the-application)
- [Main Functional Areas](#main-functional-areas)
- [API Overview](#api-overview)
- [Recipe Data Model](#recipe-data-model)
- [Security Notes](#security-notes)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)

## Features

### Recipe discovery and search
- Search recipes using a query and filters such as cuisine, diet type, difficulty, and estimated cost.
- Search the local MongoDB recipe collection first and use Spoonacular as an external source when additional results are needed.
- Rank and filter recipe results.
- Load database search results with pagination and infinite scrolling.
- Cache search results in browser storage and use Redis for server-side search caching where configured.
- Stream AI-generated recipe results to the frontend using Server-Sent Events (SSE), with browser-side caching.

### Recipe details
- View recipe images, descriptions, ingredients, instructions, cooking time, calories, cuisine, diet type, difficulty, and estimated cost.
- Save or unsave recipes.
- Use recipe ingredients to generate a shopping list.

### User accounts and preferences
- Register and log in with an account.
- Manage account details, profile preferences, allergies, and avatar.
- Maintain diet preferences, cooking skill, budget range, and preferred cuisines.
- Use access and refresh tokens with HTTP-only cookies.

### Shopping list
- Add, update, and remove grocery items.
- Set item quantities and categories.
- Mark items as purchased or pending.
- Add ingredients from a recipe to the shopping list.
- Avoid duplicate ingredients by normalizing item names.
- Generate a smart grocery list through the project's smart-list service.

### Engagement and data management
- Track recipe views, likes, saves, ratings, and ranking scores in the recipe model.
- Store user gamification information such as points, cooking streaks, badges, and cooked-recipe count.
- Use MongoDB indexes for text search, filtering, popularity, and lookup performance.

## Technology Stack

| Area | Technologies |
|---|---|
| Frontend | React, Vite, React Router |
| Styling | Tailwind CSS |
| HTTP requests | Axios |
| Authentication | JWT, HTTP-only cookies |
| Backend | Node.js, Express.js |
| Database | MongoDB, Mongoose |
| Recipe data provider | Spoonacular API |
| Caching | Browser `localStorage` / `sessionStorage`, in-memory caches, Redis where configured |
| Media storage | Cloudinary |
| Real-time streaming | Server-Sent Events (SSE) |


## Architecture

```text
React + Tailwind CSS
        |
        | Axios / HTTP / SSE
        v
Node.js + Express API
        |
        +--> Authentication and user profile
        |
        +--> Recipe search and ranking
        |       +--> MongoDB recipe collection
        |       +--> Spoonacular API
        |       +--> Redis cache (if configured)
        |
        +--> Shopping-list service
        |
        +--> User saves, likes, and gamification
        |
        +--> Cloudinary media storage
```

## Getting Started

### Prerequisites

Install the following before running the project:

- Node.js (use a version supported by your installed dependencies)
- npm
- MongoDB database, local or hosted through MongoDB Atlas
- Spoonacular API key for external recipe search, if enabled
- Cloudinary account for image uploads, if enabled
- Redis instance if the Redis caching service is enabled
<!-- - Google OAuth client ID if Google sign-in is enabled -->

### 1. Clone the repository

```bash
git clone https://github.com/Anoopsingh023/Quick-healthy-meal-planner.git
```


### 2. Install dependencies

For the frontend:

```bash
cd frontend
npm install
```

For the backend, in a separate terminal:

```bash
cd backend
npm install
```


### 3. Configure environment variables

Create a `.env` file in the backend directory and configure the variables your application reads. A typical setup may look like this:

```dotenv
PORT=
MONGODB_URI=
ACCESS_TOKEN_SECRET = 
ACCESS_TOKEN_EXPIRY = 
REFRESH_TOKEN_SECRET = 
REFRESH_TOKEN_EXPIRY = 
CORS_ORIGIN=
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
SPOONACULAR_API_KEY=
GEMINI_API_KEY=
UNSPLASH_KEY=
REDIS_URL = 
```


For the frontend, configure the API base URL 

```dotenv
base_url = http://localhost:5000/api/v1
```


### 4. Start the application

Use the scripts defined in each `package.json`. Common Vite and Node.js commands are:

Frontend:

```bash
npm run dev
```

Backend:

```bash
npm run dev
```

## Environment Variables

| Variable | Purpose |
|---|---|
| `PORT` | Port used by the backend server |
| `MONGODB_URI` | MongoDB connection string |
| `ACCESS_TOKEN_SECRET` | Secret used to sign access tokens |
| `ACCESS_TOKEN_EXPIRY` | Access-token lifetime |
| `REFRESH_TOKEN_SECRET` | Secret used to sign refresh tokens |
| `REFRESH_TOKEN_EXPIRY` | Refresh-token lifetime |
| Spoonacular API key | Enables external recipe search |
| Cloudinary credentials | Enable image upload and deletion |
| Redis URL or host settings | Enables Redis-backed caching, if configured |
| Frontend API base URL | Points the React app to the backend |

Confirm exact environment-variable names in the source before starting the server.

## Main Functional Areas

### Authentication and user profiles
The user model includes account information, password hashing with bcrypt, profile and dietary preferences, saved recipe references, refresh-token data, and gamification fields. Authentication middleware validates JWTs, checks account status, and supports token-version checks for session invalidation.

### Recipe search
The search controller applies filters such as cuisine, diet type, difficulty, and cost. It searches the local database and can supplement results through Spoonacular, then ranks the combined results. The frontend includes regular search and a streaming mode for AI-generated recipes.

### Shopping list
The shopping-list feature stores items per user. Items can include a name, normalized name, quantity, category, purchase status, estimated price, priority, alternatives, and an optional reference to the recipe they came from.

### Gamification
The user schema contains fields for points, streaks, badges, cooking history, and the number of recipes cooked. The exact rules for awarding points and badges depend on the gamification controller and related routes.

## API Overview


| Method | Path shown in code | Purpose |
|---|---|---|
| `POST` | `/users/login` | Log in |
| `POST` | `/users/logout` | Log out |
| `POST` | `/users/refresh-token` | Refresh authentication |
| `GET` | `/users/me` | Fetch the current user |
| `POST` | `/users/me/toggle-save/:recipeId` | Save or unsave a recipe |
| `GET` | `/recipes/search` | Search recipes with filters |
| `GET` | `/recipes/db-search` | Search database recipes |
| `GET` | `/recipes/stream-ai?query=...` | Stream generated recipe results |
| `GET` | `/shopinglists/` | Fetch the shopping list |
| `POST` | `/shopinglists/add` | Add a shopping-list item |
| `PATCH` | `/shopinglists/update/:itemId` | Update item details |
| `PATCH` | `/shopinglists/toggle/:itemId` | Update purchase status |
| `DELETE` | `/shopinglists/remove/:itemId` | Remove an item |
| `POST` | `/shopinglists/generate/:recipeId` | Generate a shopping list from a recipe |


## Recipe Data Model

Recipes can contain:

- **Basic information:** title, image, description, tags
- **Ingredients:** name, quantity, optional flag, and image
- **Instructions:** numbered steps, instructions, and optional time
- **Metadata:** cooking time, difficulty, cuisine, diet type, food types, cost estimate, and calories
- **Search and ranking:** searchable text, embeddings, views, likes, saves, ratings, popularity score, and quality score
- **Source and caching:** creator, source, hash, cache key, and optional expiration time

The schema validates recipe embeddings as 384-dimensional vectors. Recipe documents must therefore include an embedding that satisfies this validation unless the schema is intentionally changed.

## Security Notes

- Never commit `.env` files, API keys, OAuth secrets, database credentials, or JWT secrets.
- Use different, randomly generated secrets for access and refresh tokens.
- Keep authentication cookies `HttpOnly` and configure `Secure` and `SameSite` appropriately for your deployment.
- Configure CORS and cookie settings for your actual frontend and backend origins.
- Validate and authorize all protected routes on the server.
- Keep private service credentials on the backend; frontend variables are visible to users.
- Remove any demo credentials from the login page before making the application public.
- Use HTTPS in production and review upload validation, rate limits, and error handling.

## Troubleshooting

### Frontend cannot connect to the backend
- Verify the API base URL in the frontend configuration.
- Confirm the backend is running and that its route prefix matches the frontend requests.
- Check CORS and cookie settings if authenticated requests fail.

### Authentication repeatedly returns `401`
- Check that cookies are being sent with requests.
- Verify token secrets and expiry settings.
- Confirm the refresh-token endpoint and cookie configuration match the frontend.

### Recipe search returns no results
- Confirm MongoDB is connected and the recipe collection contains searchable documents.
- Check that recipes have ingredients and steps if the search filter requires them.
- Verify Spoonacular credentials and API quota when external search is enabled.
- Confirm the requested query and filter values match the schema.

### AI recipe streaming fails
- Check the backend SSE endpoint and its response headers.
- Verify any AI provider configuration used by the generation service.
- Inspect browser and server logs for connection or parsing errors.

### Images do not load
- Verify image URLs and Cloudinary configuration.
- Check network requests and browser console errors.
- Ensure the frontend uses valid HTTPS image URLs in production.

## Future Improvements

- Add automated unit and integration tests for authentication, search, and shopping-list endpoints.
- Improve accessibility and mobile responsiveness across all screens.
- Add meal planning by day and week, with nutrition summaries.
- Add clearer loading, empty, and error states to all recipe and shopping-list views.
- Add API documentation with Swagger/OpenAPI.
- Add CI checks, deployment instructions, and a production environment checklist.
- Add stronger validation and rate limiting to public and authentication endpoints.

## Contributing

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature`.
3. Make your changes and test them locally.
4. Commit your changes: `git commit -m "Add your feature"`.
5. Push the branch and open a pull request.


