# 🎧 Music Streaming Database – Data Fundamentals Project

This project is a secure, role-based music streaming database built with Supabase and PostgreSQL. It was developed as part of the Data Fundamentals course to demonstrate key concepts in relational database design, access control, and SQL policy enforcement.

The system supports two user roles — `admin` and `customer` — and includes features such as playlist creation, song management, and secure access to user-specific data. Admins have full control over the platform, while customers can manage their own playlists and stream published songs.

Security is enforced using Supabase Auth and Row Level Security (RLS), with custom SQL policies and admin-only functions to ensure data privacy and integrity.

This repository includes:
- A normalized relational schema for music streaming
- SQL scripts for table creation, sample data, and policies
- Admin-only functions using `SECURITY DEFINER`
- A complete set of CRUD operations for songs, playlists, and playlist_songs
- Documentation of security features in `security_notes.md`

## 🧑‍💼 Identity, Role & Mapping

This project uses Supabase Auth to manage user authentication and securely map each logged-in user to their internal database identity and role.

### 🔐 Identity Mapping

Supabase Auth provides a unique `auth.uid()` for each authenticated user. To link this to internal user records, we use a mapping table:

```sql
CREATE TABLE auth_user_map (
  auth_uid UUID PRIMARY KEY,
  user_id INTEGER REFERENCES users(user_id)
);
```
### Role Assignment
```
CREATE TABLE users (
  user_id SERIAL PRIMARY KEY,
  username TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  role TEXT CHECK (role IN ('admin', 'customer')) DEFAULT 'customer',
  created_at TIMESTAMP DEFAULT NOW()
);
````

### 🛡️Example Role-Based Policy
```
CREATE POLICY "Customers can view their own playlists"
ON playlists
FOR SELECT
USING (
  user_id = (
    SELECT user_id FROM auth_user_map
    WHERE auth_uid = auth.uid()
  )
);
```

## 👤 User Policies

These policies ensure that regular users (role = `customer`) can only access and manage their own data. All policies rely on mapping `auth.uid()` to the internal `user_id` via the `auth_user_map` table.

---

### 📁 Playlists: Users Can Only Access Their Own

```sql
CREATE POLICY "Users can view their own playlists"
ON playlists
FOR SELECT
USING (
  user_id = (
    SELECT user_id FROM auth_user_map
    WHERE auth_uid = auth.uid()
  )
);
```
```
CREATE POLICY "Users can insert playlists"
ON playlists
FOR INSERT
WITH CHECK (
  user_id = (
    SELECT user_id FROM auth_user_map
    WHERE auth_uid = auth.uid()
  )
);
```
### 🎶 Playlist Songs: Users Can Only Modify Their Own Playlists
```
CREATE POLICY "Users can view songs in their playlists"
ON playlist_songs
FOR SELECT
USING (
  playlist_id IN (
    SELECT playlist_id FROM playlists
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    )
  )
);
```
```
CREATE POLICY "Users can add songs to their playlists"
ON playlist_songs
FOR INSERT
WITH CHECK (
  playlist_id IN (
    SELECT playlist_id FROM playlists
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    )
  )
);
```
### 🧑‍💼 Users Table: Users Cannot Access Other Users
```
CREATE POLICY "Users can view their own profile"
ON users
FOR SELECT
USING (
  user_id = (
    SELECT user_id FROM auth_user_map
    WHERE auth_uid = auth.uid()
  )
);
```

## 🧑‍💼 Admin Policies

Admins are granted full access to manage all data in the system. These policies ensure that users with the `admin` role can bypass Row Level Security (RLS) restrictions and perform any operation (SELECT, INSERT, UPDATE, DELETE) on protected tables.

All admin policies rely on mapping `auth.uid()` to the internal `user_id` via the `auth_user_map` table and checking the user's role in the `users` table.

---

### 📁 Playlists: Admins Have Full Access

```sql
CREATE POLICY "Admins full access to playlists"
ON playlists
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    )
    AND role = 'admin'
  )
);
```
### 🎶 Playlist Songs: Admins Can Manage All Entries
```
CREATE POLICY "Admins full access to playlist_songs"
ON playlist_songs
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    )
    AND role = 'admin'
  )
);
```
### 💿 Songs: Admins Can View, Add, Edit, and Delete Songs
```
CREATE POLICY "Admins full access to songs"
ON songs
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM users
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    )
    AND role = 'admin'
  )
);
```
### 🛠️ Admin-Only Function Example
```
CREATE OR REPLACE FUNCTION delete_song_admin(song_id INT)
RETURNS VOID AS $$
BEGIN
  IF EXISTS (
    SELECT 1 FROM users
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    ) AND role = 'admin'
  ) THEN
    DELETE FROM songs WHERE song_id = delete_song_admin.song_id;
  ELSE
    RAISE EXCEPTION 'Unauthorized';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```


## ⚡️ Testing Roles & Policies

### ✅ User Tests
1.  **SELECT own tickets** (works)\
2.  **INSERT a new ticket purchase** (works)\
3.  **UPDATE or DELETE tickets/payments** (blocked)\
   
- **Create test accounts** in Supabase Auth:
 - Admin user (set profiles.role = 'admin')
 - Regular user A and user B
- **Create/Update profiles** or use signup trigger to auto-create a profile for each auth.users row.. **Populate sample data** (events, customers with auth_user_id, tickets, payments). Ensure customers.auth_user_id points to users.
- Test with **supabase** :
 -Sign in as regular user → request tickets and payments. Confirm user only sees their own.
 - Try to perform admin-only actions (update/delete events) as regular user → **should fail**.
- Sign in as **admin** → confirm full access, call admin RPCs (delete_event_by_admin).
- Use **SQL Editor** for debugging only (SQL Editor runs as service_role and bypasses RLS — do not use this for policy tests).

### ✅ Admin Tests

1.  **UPDATE event details** (works)\
2.  **DELETE event**(works)\
3.  **SELECT all tickets** (works)

<img width="1388" height="954" alt="image" src="https://github.com/user-attachments/assets/adb5642c-334f-45a3-a867-b70bb26ea6cc" />


### 🛠 Admin-only Function
```
CREATE OR REPLACE FUNCTION delete_song_admin(song_id INT)
RETURNS VOID AS $$
BEGIN
  IF EXISTS (
    SELECT 1 FROM users
    WHERE user_id = (
      SELECT user_id FROM auth_user_map
      WHERE auth_uid = auth.uid()
    ) AND role = 'admin'
  ) THEN
    DELETE FROM songs WHERE song_id = delete_song_admin.song_id;
  ELSE
    RAISE EXCEPTION 'Unauthorized';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

<img width="1341" height="646" alt="image" src="https://github.com/user-attachments/assets/758dab35-eb9d-4753-b936-8b585397a1b3" />

## 📎 References

- Supabase Row Level Security (RLS) Guide: [https://supabase.com/docs/guides/database/postgres/row-level-security](https://supabase.com/docs/guides/database/postgres/row-level-security)
- Supabase Auth Documentation: [https://supabase.com/docs/guides/auth](https://supabase.com/docs/guides/auth)
- Supabase SQL Policies: [https://supabase.com/docs/guides/auth/row-level-security](https://supabase.com/docs/guides/auth/row-level-security)
- PostgreSQL SECURITY DEFINER Functions: [https://www.postgresql.org/docs/current/sql-createfunction.html](https://www.postgresql.org/docs/current/sql-createfunction.html)
- Supabase Functions Overview: [https://supabase.com/docs/guides/functions](https://supabase.com/docs/guides/functions)
