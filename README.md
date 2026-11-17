**🎬 Cool Movie Gamez**

A full-stack MERN web app with movie-themed browser games, built by a four-person team as a CSC 300 project at Salem State University. Play rounds, earn coins, climb the daily leaderboards, and spend your coins on profile cosmetics.

Live site: https://cool-movie-gamez.vercel.app
API: https://cool-movie-gamez.onrender.com

**Games
🔼🔽 HiLo**
Two movie posters, one question: which movie has more IMDb votes?

Pick the movie you think is more popular to keep your streak going. One wrong guess ends the run.
Each correct guess is worth up to 5,000 points, decaying the longer you take (minimum 500 per round).
A run caps at 10 correct guesses.
Score turns into coins, which are added to your profile when you're logged in.
High score and best streak are also kept in your browser between sessions.

**🌍 Guessr**
A GeoGuessr-style game for movies: where in the world is this movie set?

You're shown a random movie poster and have 30 seconds to drop a pin on an interactive world map.
The backend uses Google Gemini to pick the real-world city the movie is mainly set in, then geocodes it with OpenStreetMap Nominatim.
Your score is based on the distance between your pin and the answer (haversine formula, up to 10,000 points), plus a bonus for time left on the clock.
After each guess, the map shows both your pin and the correct location.

**Other Features**
**Accounts:** sign up and log in with JWT authentication (tokens expire after 1 hour); passwords hashed with bcrypt and input validated with Zod.
**Profiles:** bio, avatar upload (stored in AWS S3), name/email edits, and password changes.
**Coins and shop:** coins from either game can buy cosmetics:
Gold Avatar Border	100 coins
Rainbow Username	150 coins
Rainbow Profile Border	200 coins

**Daily leaderboards:** top 10 per game, reset each day, one high score per user per game.
**Admin dashboard:** manage users and roles, view leaderboards for both games, and see a score distribution chart.
Dark mode that persists between visits.
Sound effects for button clicks, correct answers, and game over.

**Tech Stack**
Frontend - React 18, React Router, Tailwind CSS, React Bootstrap, React Leaflet, Recharts
Backend - Node.js, Express, Mongoose
Database - MongoDB Atlas
Auth - JSON Web Tokens, bcrypt, Zod
External services - IMDb API (imdbapi.dev), Google Gemini (gemini-2.5-flash-lite), OpenStreetMap / Nominatim, AWS S3
Hosting - Vercel (frontend), Render (backend)

**Project Structure**
MovieGamez/
├── frontend/                 # React client
│   ├── public/sounds/        # Game sound effects
│   └── src/
│       ├── components/
│       │   ├── navbar.js
│       │   └── pages/        # hilo, guessr, shop, leaderboard, admin, profile, login, register
│       ├── css/              # Game-specific styles
│       ├── utilities/        # JWT decode, HiLo sound hook
│       └── config.js         # API base URL (local vs. production)
├── backend/
│   └── server/               # Express API
│       ├── config/           # MongoDB + S3 setup
│       ├── models/           # Mongoose schemas
│       ├── routes/           # Users, HiLo, Guessr, leaderboard, profile, movie data
│       ├── tests/            # .rest files for the VS Code REST Client
│       ├── utilities/        # JWT generation
│       └── server.js
└── Equi_Metrics.ipynb        # Colab notebook

**Running Locally**
Prerequisites:
- Node.js and npm
- A MongoDB Atlas cluster
- A Google Gemini API key (for Guessr)
- An AWS S3 bucket (for avatar uploads)

**1. Backend**
bash
cd backend/server
npm install

Create backend/server/.env:

env
DB_URL=mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<database>
ACCESS_TOKEN_SECRET=<a long random string>
PORT=8081

# Guessr
GEMINI_API_KEY=<your Gemini API key>
FOR_THE_MAP_API=<User-Agent string for Nominatim, e.g. "CoolMovieGamez (you@example.com)">
BASE_URL=http://localhost:8081

# Avatar uploads
AWS_REGION=<e.g. us-east-1>
AWS_ACCESS_KEY_ID=<key>
AWS_SECRET_ACCESS_KEY=<secret>
AWS_S3_BUCKET_NAME=<bucket name>

Start the server:

bash
npm run server      # nodemon, restarts on changes
# or
npm run Start       # plain node (note the capital S)

The API runs at http://localhost:8081.

Note: CORS in server.js only allows https://cool-movie-gamez.vercel.app. For local development, add http://localhost:8096 to the allowed origins.

**2. Load movie data**

Both games pull from the apiPulls collection, so it has to be filled once:

bash
curl -X POST http://localhost:8081/api/data

This pulls movies from the IMDb API into MongoDB.

**3. Frontend**
bash
cd frontend
npm install
npm start

The app runs at http://localhost:8096. In development it talks to http://localhost:8081 automatically (see src/config.js). For a production build, set REACT_APP_API_URL to your backend's URL.

**API Overview**
All routes are prefixed with /api.

Area	Method & Route	Description
Users	POST /users/signup - Create an account
	POST /users/login - Log in, returns a JWT
	GET /users - List all users
	PUT /users/:id - Edit a user (including role)
  
Profile	GET /userProfile/:username - Get user + profile (coins, cosmetics, bio, avatar)
	PUT /userProfile/:username - Update profile, avatar, password, or coins
	PUT /userProfile/:username/shop - Buy or equip cosmetics
  
HiLo	GET /hilo/movies - 10 random movies with posters and vote counts
	POST /hilo / GET /hilo - Save / list HiLo scores
  
Guessr	GET /guessr/get - Start a round (movie + poster + round ID)
	POST /guessr/post - Submit a guess, returns score, distance, and answer
	GET /guessr/scores - Top 10 Guessr scores
  
Leaderboard	GET /leaderboard/:game - Today's top 10 for hilo or guessr
	PUT /leaderboard/:game - Submit a score (only kept if it beats your best)
  
Movie data	POST /data - Import movies from the IMDb API
	GET /data	- 20 random movies

Sample requests for testing are in backend/server/tests/ and can be run with the REST Client VS Code extension.

**Team**
Built by a four-person team. Original repository: knguyen0923/CoolMovieGamez.

**Known Limitations**
This was built as a class project, and the team focused on gameplay and features rather than security. Most API routes, including the admin and delete routes, don't check whether the user is logged in or has the right permissions. Don't reuse this code in production without adding that first.

**Credits**
Movie data: imdbapi.dev
Maps and geocoding: OpenStreetMap contributors and Nominatim
Sound effects: Freesound users xpoki, etheraudio, and mellau
Based on a MERN user-service starter template

**License**
MIT
