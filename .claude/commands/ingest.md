Read SCHEMA.md first to understand all conventions.

Then perform an ingest operation:

1. Read wiki/log.md to identify which raw/ files have already been processed (check entries in the log).
2. List all files in raw/ and identify those NOT yet in the log.
3. If there are no new files, report "Nothing new to ingest" and stop.
4. For each new source file:
   a. Read the file fully.
   b. Identify all significant topics, entities, methods, and claims.
   c. For each: check wiki/index.md to see if a page already exists.
      - If yes: open the page, add new facts under "Key Facts / Claims" with [source-slug] citations, add new cross-links, upgrade Status if appropriate.
      - If no: create a new file at wiki/topics/<slug>.md using the template in SCHEMA.md.
   d. For every page you create or update, scan all OTHER existing wiki pages for mentions of this topic and add back-links where missing.
5. Update wiki/index.md: add a row for each new page, update Last updated date and Total pages count.
6. Append to wiki/log.md: date, source slug(s) processed, list of pages created and updated.

Report a summary: N sources processed, M pages created, K pages updated.
