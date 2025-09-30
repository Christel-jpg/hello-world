# 🎧 Music Streaming Database Project

## 📌 Project Overview
This project simulates a simplified music streaming service using a relational database. It includes tables for users, artists, songs, playlists, and the relationships between them.

## 🗃️ Database Features
- 5 tables: `Users`, `Artists`, `Songs`, `Playlists`, `PlaylistSongs`
- Sample data with at least 5 rows per table
- Foreign key relationships between tables
- ERD diagram included in `/docs/`

## 🛠️ Setup Instructions
1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/music-streaming-db.git




---

## 📗 `data_dictionary.md` Template

This file explains each table and column in your database.

```markdown
# 📚 Data Dictionary

## 1. Users
Stores information about listeners.

| Column Name | Data Type | Description           |
|-------------|-----------|-----------------------|
| user_id     | INT       | Primary key           |
| username    | VARCHAR   | User's display name   |
| email       | VARCHAR   | User's email address  |
| dob         | DATE      | Date of birth         |

---

## 2. Artists
Stores information about music creators.

| Column Name | Data Type | Description           |
|-------------|-----------|-----------------------|
| artist_id   | INT       | Primary key           |
| name        | VARCHAR   | Artist's name         |
| genre       | VARCHAR   | Music genre           |

---

## 3. Songs
Stores individual tracks.

| Column Name | Data Type | Description           |
|-------------|-----------|-----------------------|
| song_id     | INT       | Primary key           |
| title       | VARCHAR   | Song title            |
| artist_id   | INT       | Foreign key to Artists |
| genre       | VARCHAR   | Song genre            |

---

## 4. Playlists
Stores user-created playlists.

| Column Name | Data Type | Description           |
|-------------|-----------|-----------------------|
| playlist_id | INT       | Primary key           |
| title       | VARCHAR   | Playlist name         |
| user_id     | INT       | Foreign key to Users  |

---

## 5. PlaylistSongs
Maps songs to playlists (many-to-many relationship).

| Column Name | Data Type | Description           |
|-------------|-----------|-----------------------|
| playlist_id | INT       | Foreign key to Playlists |
| song_id     | INT       | Foreign key to Songs     |
