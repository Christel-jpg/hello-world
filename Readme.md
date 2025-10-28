# 🎟 Music Streaming Database – Admin & User Roles in Supabase

<img width="1385" height="641" alt="image" src="https://github.com/user-attachments/assets/8e4eefe6-ce82-4c6d-bfe6-288e6fb31d17" />

---
## 📗 Table of Contents

* [📖 About the Project](#about-project)  
* [🛠 Built With](#built-with)  
* [🚀 Live Demo](#live-demo)  
* [💻 Getting Started](#getting-started)  
* [💾 Sample SQL Queries & Policies](#sample-sql-queries)   
* [🛡 Security Notes](#security-notes)  
* [👥 Authors](#authors)  
* [🔭 Future Features](#future-features)  
* [🤝 Contributing](#contributing)  
* [⭐️ Show your support](#support)  
* [🙏 Acknowledgements](#acknowledgements)  
* [❓ FAQ](#faq)  
* [📝 License](#license)  

---

## 🎵 About the Project

This project is a Music Streaming Database designed to simulate the backend of a streaming service using **Supabase** and **PostgreSQL**. It features secure role-based access for **Admins** and **Users**, and implements **Row Level Security (RLS)** to ensure data privacy and integrity.

The system includes:
- 🎤 Tables for **artists**, **albums**, **songs**, **users**, and **playlists**
- 🔐 UUID-based authentication via Supabase Auth
- 🧑‍💼 Role-based access policies (Admin vs Regular User)
- 🛡️ Row Level Security (RLS) on all tables
- ⚙️ SQL functions for admin-only operations like managing songs and playlists

This setup allows users to create and manage their own playlists, view songs and albums, and stream music securely. Admins have full control over content management and analytics.


## 🛠️ Built With

- [Supabase](https://supabase.com/) – Backend-as-a-Service platform built on PostgreSQL
- [PostgreSQL](https://www.postgresql.org/) – Relational database for structured data
- [SQL](https://www.w3schools.com/sql/) – Used for querying and managing data
- [Supabase Auth](https://supabase.com/docs/guides/auth) – Handles user authentication with UUIDs
- [Row Level Security (RLS)](https://supabase.com/docs/guides/auth/row-level-security) – Ensures secure, role-based access control

## 🔮 Future Features

- 🎧 Music playback integration
- 📊 Admin analytics dashboard
- 🗂️ Genre and mood tagging for songs
- 🧠 Recommendation engine based on user activity
- 📱 Mobile-friendly UI for playlist management


## 💻 Live Demo

You can explore the live version of this project hosted on Supabase:

🔗 [Live Demo Link](crbssmxzyhttgjazinvm)

## 🚀 Getting Started

To get a local copy up and running, follow these simple steps:

### Prerequisites

- Node.js and npm installed
- Supabase account (free tier works)
- Git installed

## ⚙️ Setup

To set up the Music Streaming Database locally or on Supabase, follow these steps:

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/music-streaming-database.git
cd music-streaming-database

ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE playlists ENABLE ROW LEVEL SECURITY;
ALTER TABLE songs ENABLE ROW LEVEL SECURITY;
ALTER TABLE albums ENABLE ROW LEVEL SECURITY;
ALTER TABLE artists ENABLE ROW LEVEL SECURITY;
```
## 🛡️ Sample SQL Policies

These policies enforce secure access control using Supabase's Row Level Security (RLS). They distinguish between **Admin** and **User** roles.

### 👤 User Policies

#### 1. Users can view their own playlists
```sql
CREATE POLICY "Users can view their own playlists"
ON playlists
FOR SELECT
USING (auth.uid() = user_id);
```
```
CREATE POLICY "Users can add songs to playlists"
ON playlists
FOR INSERT
WITH CHECK (auth.uid() = user_id);
```
```
CREATE POLICY "Users can view songs"
ON songs
FOR SELECT
USING (true);
```
```
CREATE POLICY "Users can view albums"
ON albums
FOR SELECT
USING (true);
```
---

## 👤 Admin Policies
```
CREATE POLICY "Admins manage all playlists"
ON playlists
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```

```
CREATE POLICY "Admins manage all songs"
ON songs
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```
```
CREATE POLICY "Admins manage all albums"
ON albums
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE id = auth.uid() AND role = 'admin'
  )
);
```
 ### 3️⃣ Example CRUD Queries with their output.

 ### Show Songs in a Specific Playlist
```
SELECT s.song_id, s.title, s.duration, a.name AS artist
FROM playlist_songs ps
JOIN songs s ON ps.song_id = s.song_id
JOIN artists a ON s.artist_id = a.artist_id
WHERE ps.playlist_id = 1;
```
 <img width="1344" height="753" alt="image" src="https://github.com/user-attachments/assets/34148005-e4f5-40af-b4e9-4861fcc22073" />

### Shows Albums and Their Artists
```
SELECT al.album_id, al.title AS album, al.release_date, ar.name AS artist
FROM albums al
JOIN artists ar ON al.artist_id = ar.artist_id;
```
<img width="1347" height="766" alt="image" src="https://github.com/user-attachments/assets/4361c962-2022-4b19-8761-1abc3a347427" />

### View All Playlists and Their Owners
```
SELECT p.playlist_id, p.name, p.created_at, u.username
FROM playlists p
JOIN users u ON p.user_id = u.user_id
ORDER BY p.created_at DESC;
```
<img width="1342" height="762" alt="image" src="https://github.com/user-attachments/assets/0091a19d-f0b2-451b-9b5f-cde1d6673bab" />

## 👥Roles and Output

This has two common roles

| Role     | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| `admin`  | Full access to manage users, songs, playlists, and analytics               |
| `customer` | Regular user who can create playlists, stream songs, and manage their own data |

## 🧑‍💼 Admin: View All Users and Their Roles
```
SELECT user_id, username, email, created_at
FROM users
ORDER BY created_at DESC;
```
<img width="1336" height="728" alt="image" src="https://github.com/user-attachments/assets/317a81bb-0b16-4b0b-8b3c-8dd0f6293b63" />


## 🎧 Customer: View Their Own Playlists
```
SELECT playlist_id, name, created_at
FROM playlists
WHERE user_id = (
  SELECT user_id FROM auth_user_map
  WHERE auth_uid = auth.uid()
);
```
<img width="1339" height="621" alt="image" src="https://github.com/user-attachments/assets/75e4f930-229d-4536-b8f4-e83d4ddfc901" />


## 🛡 Security Notes <a name="security-notes"></a>

See full explanation of RLS, policies, and admin functions in 👉 [security_notes.md](https://github.com/Christel-jpg/Data-Fundamentals/blob/data_test_branch/security_notes.md)


---

## 👥 Authors <a name="authors"></a>

- **Christel Chepkemboi**  
  GitHub: https://github.com/Christel-jpg/hello-world/edit/Music-Streaming-Database/Readme.md
  
---

## 🔭 Future Features <a name="future-features"></a>

- Integrate with front-end event booking portal
- Add analytics for most attended events and top-paying customers
- Implement audit logging for admin actions (create, update, delete events)
- Enable ticket QR code generation for entry validation
-Add email/SMS notifications for successful payments
-Include refund and cancellation management
-Implement role-based access (Admin, Customer)

Add dashboard for revenue and sales insights
---

## 🤝 Contributing <a name="contributing"></a>

Open issues or pull requests are welcome.

---

## ⭐️ Show your support <a name="support"></a>

Give a ⭐️ if you like this project!

---

## 🙏 Acknowledgements <a name="acknowledgements"></a>

- Supabase docs for SQL & RLS policies  
- PostgreSQL official docs  

---

## ❓ FAQ <a name="faq"></a>

**Q: How do I test RLS policies?**  
A: Sign in as User vs Admin and try CRUD operations. Policies will restrict or allow access accordingly.  

**Q: Can I extend this to a front-end?**  
A: Yes, connect Supabase Auth with **Angular**, **Java**, or any front-end framework.  

---

## 📝 License <a name="license"></a>

This project is licensed under the MIT License - see the LICENSE file for details.
