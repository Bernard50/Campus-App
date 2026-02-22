# CAMPUS — Student Organizer

A premium all-in-one student organizer built with React + Vite + Supabase.

## Features

- 📚 **My Classes** — track your classes, times, rooms, and instructors
- ✅ **Tasks** — manage assignments with due dates and priority
- 🗓 **Calendar** — view events alongside your class schedule
- 🗺 **Course Planner** — plan courses across semesters
- 🔔 **Telegram Reminders** — get a message 15 min before each class + daily task digest
- 🖥 **Browser Notifications** — pop-up alerts while the app is open
- 🌙 **Dark Mode** — toggle-able, remembered across sessions
- 📱 **Mobile-first** — bottom navigation, slide-up modals, safe-area aware

---

## Local Development

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/campus.git
cd campus
```

### 2. Install dependencies

```bash
npm install
```

### 3. Set up environment variables

```bash
cp .env.example .env
```

Fill in your `.env`:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
VITE_TELEGRAM_BOT_TOKEN=your-telegram-bot-token
```

### 4. Set up Supabase

Create these tables in your Supabase project (SQL Editor):

```sql
-- Classes
create table classes (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users not null,
  name text not null,
  code text,
  instructor text,
  room text,
  color text,
  days text[],
  start_time text,
  end_time text,
  notes text,
  created_at timestamptz default now()
);
alter table classes enable row level security;
create policy "Users manage own classes" on classes for all using (auth.uid() = user_id);

-- Tasks
create table tasks (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users not null,
  title text not null,
  class_id uuid references classes(id) on delete set null,
  due_date date,
  priority text default 'medium',
  done boolean default false,
  notes text,
  created_at timestamptz default now()
);
alter table tasks enable row level security;
create policy "Users manage own tasks" on tasks for all using (auth.uid() = user_id);

-- Courses to take
create table courses_to_take (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users not null,
  name text not null,
  code text,
  credits int,
  semester text,
  status text default 'planned',
  notes text,
  created_at timestamptz default now()
);
alter table courses_to_take enable row level security;
create policy "Users manage own courses" on courses_to_take for all using (auth.uid() = user_id);

-- Events
create table events (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users not null,
  title text not null,
  date date not null,
  time text,
  type text default 'general',
  notes text,
  created_at timestamptz default now()
);
alter table events enable row level security;
create policy "Users manage own events" on events for all using (auth.uid() = user_id);
```

### 5. Run locally

```bash
npm run dev
```

---

## Deploy to Vercel

### 1. Push to GitHub

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

### 2. Import on Vercel

1. Go to [vercel.com](https://vercel.com) → **Add New Project**
2. Import your GitHub repo
3. Framework preset: **Vite**
4. Add environment variables (Settings → Environment Variables):
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
   - `VITE_TELEGRAM_BOT_TOKEN`
5. Click **Deploy** ✅

The `vercel.json` in the repo handles React Router client-side routing automatically.

### 3. Enable email auth in Supabase

In your Supabase project → **Authentication → Providers → Email** — make sure it's enabled.

---

## Telegram Bot Setup (one-time)

1. Open Telegram → message **@BotFather** → `/newbot`
2. Follow prompts → copy the bot token
3. Add it as `VITE_TELEGRAM_BOT_TOKEN` in Vercel env vars
4. Users visit **Settings** in the app → tap **@campusreminderbot** → send any message → click **Link my Telegram**

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite |
| Routing | React Router v6 |
| Backend | Supabase (PostgreSQL + Auth) |
| Icons | Lucide React |
| Notifications | Web Notifications API + Telegram Bot API |
| Hosting | Vercel |

---

## License

MIT
