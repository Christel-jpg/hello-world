## Data Analysis

<img width="1385" height="641" alt="image" src="https://github.com/user-attachments/assets/2321ce35-6089-45c1-b1d4-95f380be581d" />

# 🎵 Music Streaming Database Analysis

## 📘 About the Project
This project simulates the backend of a music streaming platform using Supabase (PostgreSQL) and integrates it with RStudio Cloud for data analysis. It includes key entities such as users, songs, artists, playlists, and user interactions like favorites and play history. The goal is to analyze streaming behavior, song popularity, artist performance, and user engagement.

## 🧱 Database Structure
The database is hosted on Supabase and includes the following tables:
- `users`: listener profiles
- `songs`: track metadata (title, duration, genre)
- `artists`: performer details
- `playlists`: user-curated song collections
- `plays`: streaming history
- `user_favorites`: liked songs

Relationships are defined using foreign keys, enabling joins across tables for deeper analysis.

## 🔌 R Integration
RStudio Cloud connects to Supabase via REST API using the `httr` and `jsonlite` packages. This allows secure querying and manipulation of live data.

### Sample Connection Code
```r
response <- GET(
  url = "https://your-project.supabase.co/rest/v1/songs",
  add_headers(
    apikey = "your-anon-key",
    Authorization = paste("Bearer", "your-anon-key")
  )
)
songs <- fromJSON(content(response, "text"))
```

## 💻 Getting Started

To run this project locally and perform data analysis using RStudio Cloud and Supabase, follow these steps:

### 🔧 Prerequisites

Before you begin, make sure you have:
- A Supabase account
- A Posit Cloud (RStudio Cloud) account
- Installed R packages:
  ```r
  install.packages(c("DBI", "RPostgres", "httr", "jsonlite", "ggplot2"))

## ⚙️ Setup

To get started with this project, follow these steps:

### 1. Clone the Repository

```bash
git clone https://github.com/Christel-jpg/music-streaming-database.git
cd music-streaming-database
```
## 🚀 Usage

Follow these steps to interact with your Supabase-hosted music streaming database using RStudio Cloud:

### 🥇 Step 1: Connect to Supabase via REST API

Use the following R code to connect to your `songs` table:

```r
library(httr)
library(jsonlite)

supabase_key <- "your-anon-key"  
supabase_url <- "https://your-project.supabase.co/rest/v1/songs"

response <- GET(
  url = supabase_url,
  add_headers(
    apikey = supabase_key,
    Authorization = paste("Bearer", supabase_key)
  )
)

songs <- fromJSON(content(response, "text"))
print(songs)
```

### Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

1. Install required R packages:

```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```

2. Create a `connect_db.R` file:

```r
library(DBI)
connect_db <- function() {
  dbConnect(
    RPostgres::Postgres(),
    dbname = "your_dbname",
    host = "your_project.supabase.co",
    port = 5432,
    user = "your_username",
    password = "your_password",
    sslmode = "require"
  )
}
```

3. Use this connection in R scripts:

```r
source("connect_db.R")
con <- connect_db()
dbListTables(con)
```


### Outcome After Establishing Connection.

<img width="1307" height="961" alt="image" src="https://github.com/user-attachments/assets/e02c47c7-7bd0-481a-8da4-3cbc8d312dda" />


## 📊 R Data Analysis

### 🎯 Overview

This section demonstrates how R is used to analyze music streaming data hosted on Supabase. The goal is to uncover insights such as song popularity, artist performance, and user engagement. Using R packages like `httr`, `jsonlite`, `dplyr`, and `ggplot2`, we connect to the Supabase backend via REST API and visualize key metrics.

The analysis is designed to be reproducible in Posit Cloud (RStudio Cloud), making it easy to explore and extend.

---

### 🔍 Code: Most Played Songs

```r
# 📦 Load required packages
install.packages(c("httr", "jsonlite", "dplyr", "ggplot2"))
library(httr)
library(jsonlite)
library(dplyr)
library(ggplot2)
```

### 🔑 Supabase credentials
supabase_key <- "your-anon-key"
base_url <- "https://crbssmxzyhttgjazinvm.supabase.co/rest/v1"

### 📊 Query: Join plays with songs and count plays
```
response <- GET(
  url = paste0(base_url, "/plays?select=song_id,songs(title)&limit=1000"),
  add_headers(
    apikey = supabase_key,
    Authorization = paste("Bearer", supabase_key)
  )
)

plays <- fromJSON(content(response, "text"))
```
<img width="1536" height="1024" alt="Copilot_20251029_183023" src="https://github.com/user-attachments/assets/94c82263-f150-42c5-891a-2935df80220d" />


### 🧮 Count most played songs
```
play_count <- plays %>%
  group_by(songs$title) %>%
  summarise(play_count = n()) %>%
  arrange(desc(play_count))

print(play_count)
```
<img width="1536" height="1024" alt="Copilot_20251029_182723" src="https://github.com/user-attachments/assets/df04d6ca-2c84-42e5-8851-7536c852fbe5" />



### 📊 Visualize
```
ggplot(play_count, aes(x = reorder(`songs$title`, play_count), y = play_count)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  theme_minimal() +
  labs(title = "Most Played Songs", x = "Song Title", y = "Play Count")
```

## 📘 Overview- Data Dictionary

This data dictionary describes the structure of the music streaming database hosted on Supabase. It includes key tables such as `users`, `artists`, `songs`, `plays`, `playlists`, and `playlist_songs`. Each table is defined with its columns, data types, and relationships.

---

### 👤 Users

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| user_id     | UUID     | Unique identifier for each user      |
| username    | TEXT     | Display name of the user             |
| email       | TEXT     | User's email address                 |
| created_at  | TIMESTAMP| Account creation timestamp           |

---

###  Artists

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| artist_id   | UUID     | Unique identifier for each artist    |
| name        | TEXT     | Artist's name                        |
| genre       | TEXT     | Primary genre of the artist          |

---

### 🎵 Songs

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| song_id     | UUID     | Unique identifier for each song      |
| title       | TEXT     | Title of the song                    |
| duration    | INTEGER  | Duration in seconds                  |
| artist_id   | UUID     | Foreign key linking to `artists`     |

---

### ▶️ Plays

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| play_id     | UUID     | Unique identifier for each play event|
| song_id     | UUID     | Foreign key linking to `songs`       |
| timestamp   | TIMESTAMP| When the song was played             |

---

### 📁 Playlists

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| playlist_id | UUID     | Unique identifier for each playlist  |
| name        | TEXT     | Playlist name                        |
| user_id     | UUID     | Foreign key linking to `users`       |

---

### 🎶 Playlist_songs

| Column      | Type     | Description                          |
|-------------|----------|--------------------------------------|
| playlist_id | UUID     | Foreign key linking to `playlists`   |
| song_id     | UUID     | Foreign key linking to `songs`       |

---

### 🔗 Relationships

- `songs.artist_id` → `artists.artist_id`
- `plays.song_id` → `songs.song_id`
- `playlists.user_id` → `users.user_id`
- `playlist_songs.playlist_id` → `playlists.playlist_id`
- `playlist_songs.song_id` → `songs.song_id`

---

## 🔭 Future Features

- 🎧 Add user-specific listening history and recommendations
- 📈 Visualize artist growth and trending songs over time
- 🗂️ Enable playlist sharing and collaborative curation
- 🧠 Integrate basic machine learning for genre prediction
- 📱 Build a mobile-friendly dashboard for real-time stats

---

## 🤝 Contributing

Contributions are welcome! If you'd like to improve the analysis, add new visualizations, or extend the Supabase schema:

1. Fork the repository
2. Create a new branch (`git checkout -b feature-name`)
3. Commit your changes (`git commit -m 'Add new feature'`)
4. Push to the branch (`git push origin feature-name`)
5. Open a pull request

---

## ⭐️ Show Your Support

If you found this project helpful or inspiring:

- Give it a ⭐️ on GitHub
- Share it with fellow data enthusiasts
- Tag me if you remix or build on it — I'd love to see it!

---

## 🙏 Acknowledgements

- Supabase for the powerful open-source backend
- Posit Cloud for making R analysis accessible and reproducible
- `ggplot2`, `httr`, `jsonlite`, and `dplyr` for enabling elegant data workflows
- All artists and creators whose data inspired this project

---

## ❓ FAQ

**Q: Can I run this project without Supabase?**  
A: Not currently — Supabase is required for data storage and API access.

**Q: Can I use this with Shiny or Dash?**  
A: Yes! The data pipeline is flexible and can be integrated with interactive dashboards.

**Q: What if I want to use Python instead of R?**  
A: You can replicate the API calls using `requests` and `pandas`.

---

## 📝 License

This project is licensed under the MIT License.  
Feel free to use, modify, and distribute with attribution.


