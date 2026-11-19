# Jamshed Tutorials: Student Portal (LMS)

Mobile-first site where students log in with an ID and password and see notes (PDF) and videos for their own board, grade and subjects. The tutor uploads everything from an admin panel; changes appear instantly.

**Stack:** Next.js 14 (App Router) · Supabase (Postgres, Auth, private file storage) · hosted on Vercel.
Free tiers cover ~50 students comfortably.

## One-time setup (about 20 minutes)

### 1. Create the database (Supabase)
1. Sign up at supabase.com, click **New project** (pick the Mumbai / Singapore region for speed in India). Save the database password.
2. Open **SQL Editor**, paste the entire contents of `supabase/schema.sql`, click **Run**.
3. Open **Authentication > Providers > Email** and turn **OFF** "Confirm email" (students use IDs, not real emails).
4. Open **Project Settings > API** and copy: Project URL, `anon` key, and `service_role` key.

### 2. Create the admin (tutor) login
1. **Authentication > Users > Add user > Create new user.** Email: `admin@students.example.com`, set a strong password, tick **Auto Confirm User**.
2. In **SQL Editor** run:
   ```sql
   update public.profiles set role = 'admin', full_name = 'Jamshed Sir'
   where student_code = 'admin';
   ```
   (If you change `NEXT_PUBLIC_ID_DOMAIN` below, use that domain in the admin email.)
   The admin then logs in on the site with ID `admin` and that password.

### 3. Run locally (optional test)
```bash
cp .env.example .env.local    # fill in the three Supabase values
npm install
npm run dev                   # http://localhost:3000
```

### 4. Deploy (Vercel)
1. Push this folder to a GitHub repository.
2. At vercel.com click **Add New > Project**, import the repo.
3. Under **Environment Variables** add every line from `.env.example` (with real values).
4. Deploy. Add the client's own domain under **Settings > Domains** if they have one.

## Daily use
- **Admin > Students:** add one student or paste many lines. Passwords are shown once for you to copy and send. **Reset pw** gives a new one.
- **Admin > Upload:** choose Notes/PDF (file stored privately) or Video link, pick board, grade, subject, **Publish**.
- Students see only their own board and grade. If you set subjects on a student, only those subjects.

## Videos
Do not upload video files to the site. Upload to **unlisted YouTube** (free), **Vimeo**, or **Bunny Stream** (cheap, no ads, better privacy), then paste the link. Unlisted links can be shared by a student, so use Bunny with signed URLs if leaks matter.

## Security notes
- Passwords are hashed by Supabase; nobody (including you) can read them. Only reset them.
- Access rules are enforced in the database (Row Level Security), not just hidden in the page.
- `SUPABASE_SERVICE_ROLE_KEY` must only live in Vercel/server env vars, never in browser code.
- Notes are in a private bucket; each open uses a 10-minute signed link after an access check.
- Change the default admin email domain placeholder (`students.example.com`) before creating students; it cannot be changed later without recreating users.

## Folder map
- `supabase/schema.sql`: tables, access rules, storage bucket
- `app/login`: student/admin login
- `app/dashboard`: student view (filter by subject, search, watch/open)
- `app/admin`: upload, manage materials, manage students
- `app/api/admin/students`: create / reset / delete students (admin only, server side)
- `app/api/notes/[id]`: secure note download
- `lib/`: Supabase helpers, constants, video-link converter
