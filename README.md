# Smart Bookmark Manager

A full-stack web application built with **Next.js (App Router)** and **Supabase** that enables users to authenticate via Google and securely manage private bookmarks.

## Features

- Google OAuth authentication
- Add, view, and delete bookmarks (title + URL)
- Private bookmarks per user
- Secure data storage with PostgreSQL
- Row Level Security (RLS) for data isolation

## Tech Stack

- **Next.js** (App Router)
- **Supabase** (Auth + Database + RLS)
- **Tailwind CSS**
- **TypeScript**

## Project Structure

```
smart-bookmark/
├── src/
│   ├── app/
│   │   ├── page.tsx
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   ├── layout.tsx
│   │   └── globals.css
│   └── utils/
│       └── supabase.ts
├── .env.local
├── package.json
└── README.md
```

## Setup

### Environment Variables

Create `.env.local` in the root directory:

```
NEXT_PUBLIC_SUPABASE_URL=your_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_public_key
```

Retrieve from **Supabase → Settings → API**. Use only the anon public key.

### Database Setup

Run this SQL in Supabase SQL Editor:

```sql
create table bookmarks (
    id uuid default uuid_generate_v4() primary key,
    user_id uuid references auth.users not null,
    title text not null,
    url text not null,
    created_at timestamp default now()
);

alter table bookmarks enable row level security;

create policy "Users can select own bookmarks" on bookmarks for select using (auth.uid() = user_id);
create policy "Users can insert own bookmarks" on bookmarks for insert with check (auth.uid() = user_id);
create policy "Users can delete own bookmarks" on bookmarks for delete using (auth.uid() = user_id);
```

### Google OAuth Setup

1. Go to **Google Cloud Console**
2. Create a new project
3. Configure **OAuth Consent Screen** (External)
4. Create **OAuth Client ID** (Web Application)
5. Add Authorized Redirect URI: `https://YOUR_PROJECT_ID.supabase.co/auth/v1/callback`
6. Add credentials to **Supabase → Authentication → Providers → Google**

## Installation

```bash
git clone https://github.com/your-username/smart-bookmark.git
cd smart-bookmark
npm install
npm run dev
```

Open `http://localhost:3000`

## How It Works

1. User authenticates via Google OAuth
2. Supabase creates authenticated session
3. Bookmarks fetch filtered by user_id
4. RLS enforces that users can only access their own bookmarks

## Deployment

1. Push to GitHub
2. Import repository in Vercel
3. Add environment variables
4. Deploy

Update Google OAuth redirect URIs for production domain after deployment.

## Security

- RLS must remain enabled
- Never expose `service_role` key in frontend
- Always verify session before database operations
