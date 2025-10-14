# 🎵 Music Streaming Database Project <a name="about-project"></a>

## 📋 Table of Contents
- [🎵 Music Streaming Database Project](#about-project)
- [🛠 Built With](#built-with)
- [🧰 Tech Stack](#tech-stack)
- [✨ Key Features](#key-features)
- [💻 Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Setup](#setup)
  - [DB Schema](#db-schema)
- [👩‍💻 Authors](#authors)
- [🔮 Future Features](#future-features)
- [🤝 Contributing](#contributing)

---

## 🎵 Music Streaming Database Project

This project is a relational database built using **SQL**, **PostgreSQL**, and **Supabase** to simulate a basic music streaming service. It includes tables for **artists**, **albums**, **songs**, **users**, and **play history**, allowing for queries and analysis of user listening behavior.

---

## 🛠 Built With <a name="built-with"></a>

### 🧰 Tech Stack <a name="tech-stack"></a>
- SQL
- PostgreSQL
- Supabase

---

### ✨ Key Features <a name="key-features"></a>
- [x] Artists, Albums, Songs, Users, Play History tables
- [x] Relational schema with foreign keys
- [x] Sample data for testing
- [x] Query examples for analytics

## 💻 Getting Started

### Prerequisites
To run this project, you need:
- A [Supabase account](https://owledge of [SQL](https://www.w3schools.comhine

### Setup

Clone this repository to your desired folder:


git clone https://github.com/yourusername/music-streaming-db

      

---


---

### ✅ Database Schema Code

```sql
-- Drop existing tables
DROP TABLE IF EXISTS plays CASCADE;
DROP TABLE IF EXISTS playlist_songs CASCADE;
DROP TABLE IF EXISTS playlists CASCADE;
DROP TABLE IF EXISTS play_history CASCADE;
DROP TABLE IF EXISTS songs CASCADE;
DROP TABLE IF EXISTS albums CASCADE;
DROP TABLE IF EXISTS artists CASCADE;
DROP TABLE IF EXISTS users CASCADE;

-- Artists
CREATE TABLE artists (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  genre TEXT
);

-- Albums
CREATE TABLE albums (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  artist_id INT REFERENCES artists(id),
  release_year INT
);

-- Songs
CREATE TABLE songs (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  album_id INT REFERENCES albums(id),
  duration INT, -- duration in seconds
  is_explicit BOOLEAN DEFAULT false
);

-- Users
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  email TEXT UNIQUE NOT NULL
);

-- Play History
CREATE TABLE play_history (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(id),
  song_id INT REFERENCES songs(id),
  played_at TIMESTAMP DEFAULT now()
);

-- Playlists
CREATE TABLE playlists (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  user_id INT REFERENCES users(id)
);

-- Playlist Songs
CREATE TABLE playlist_songs (
  id SERIAL PRIMARY KEY,
  playlist_id INT REFERENCES playlists(id),
  song_id INT REFERENCES songs(id)
);

-- Plays
CREATE TABLE plays (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(id),
  song_id INT REFERENCES songs(id),
  played_at TIMESTAMP DEFAULT now()
);
```
- The Tables should look like this in Supabase:

- Albums.
<img width="1912" height="459" alt="image" src="https://github.com/user-attachments/assets/e40909df-5796-4d0d-b4ab-256be8a2c5f3" />

- Artists.
<img width="1912" height="441" alt="image" src="https://github.com/user-attachments/assets/157b699f-ac6b-4b56-8fe7-f6f5b58186cf" />

- Playlist Songs.
<img width="1917" height="462" alt="image" src="https://github.com/user-attachments/assets/3d8381b9-f1cf-47c8-8171-4533ea37be08" />

- Playlists.
<img width="1899" height="498" alt="image" src="https://github.com/user-attachments/assets/248b7e6e-992d-491a-8306-848bf7175e60" />

- Songs.
<img width="1907" height="448" alt="image" src="https://github.com/user-attachments/assets/0aaa8e9e-c6fb-48bd-b43e-2fe5f37cf8ef" />

- Users.
<img width="1908" height="454" alt="image" src="https://github.com/user-attachments/assets/c6571136-c536-4e20-ae8b-d8d9c4d80b24" />

- The ERD screenshot from Supabase looks like this: 
<img width="1611" height="922" alt="image" src="https://github.com/user-attachments/assets/a920f02b-6968-4001-aae1-91ae9a812bcf" />

- To test the table I use two queries:

```
SELECT * FROM songs

```
<img width="961" height="561" alt="Screenshot 2025-10-14 162718" src="https://github.com/user-attachments/assets/badb8b7f-374e-4b52-a63d-ac8d78c79bfd" />

```
SELECT * FROM playlists
```
<img width="934" height="568" alt="Screenshot 2025-10-14 163503" src="https://github.com/user-attachments/assets/5ac885be-94bc-4f79-ad72-4e317085c893" />



## 👩‍💻 Authors

👤 **Christel Chepkemboi**  
GitHub: [@Christel-jpg](https://github.com/Christel-jpg)  
LinkedIn: [@christelchepkemboi](https://linkedin.com/in/christelchepkemboi)

---

## 🔮 Future Features

- Add user subscription plans
- Link database to R or Python for visualizations
- Implement Supabase Auth for secure access
- Add song ratings and user reviews
- Enable playlist sharing between users

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!  
Feel free to:
- Fork the repository
- Create a new branch
- Submit a pull request

If you find a bug or have a feature request, please open an issue.


































