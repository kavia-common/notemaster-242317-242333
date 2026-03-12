# NoteMaster database schema (PostgreSQL)

This app uses a single table, `notes`, with optional tags and full-text search.

## Table: `notes`

- `id` UUID primary key (default `gen_random_uuid()`)
- `title` TEXT (required)
- `content` TEXT (default empty string)
- `tags` TEXT[] (default `{}`)
- `created_at` TIMESTAMPTZ (default `now()`)
- `updated_at` TIMESTAMPTZ (default `now()`)
- `search_tsv` TSVECTOR generated from `(title || ' ' || content)` (stored)

## Indexes

- `idx_notes_updated_at` on `(updated_at DESC)`
- `idx_notes_tags_gin` GIN index on `tags`
- `idx_notes_search_tsv` GIN index on `search_tsv`

## Notes

- Full-text search uses `plainto_tsquery('english', q)` against `search_tsv`.
- Tag filtering uses `tag = ANY(tags)`.
- The backend also attempts to `CREATE TABLE IF NOT EXISTS ...` on startup as a safety net.
