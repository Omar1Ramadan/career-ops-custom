# Mode: latex - One-Page Jake-Style LaTeX Resume Export

Export a tailored, ATS-optimized one-page resume as a `.tex` file and compile it to PDF via `tectonic` or `pdflatex`.

This is the default generation path for Omar. Use one general-purpose Jake-style LaTeX format for every generated resume. Tailor the content, not the structure.

## Hard Constraints

1. **Exactly one page.** The compiled PDF must be 1 page. If it becomes 2+ pages, shorten bullets and rerun generation. If it is sparse, add relevant `cv.md` content and rerun generation.
2. **Fill the page.** The generated resume should look dense and complete, not half-full. Use the strongest relevant experience from `cv.md` until the page is close to full while still staying on one page.
3. **Never omit core sections.** Required sections are `Education`, `Experience`, `Projects`, and `Technical Skills`.
4. **Use `cv.md` as the source of truth.** Pull resume content from `cv.md` first. Use `config/profile.yml` for identity/contact and high-level targeting only.
5. **No invented claims.** Rewrite and prioritize existing proof points; do not add skills, metrics, employers, dates, or outcomes that are not supported by `cv.md` or `config/profile.yml`.
6. **Jake format only.** Use `templates/cv-template.tex`; do not switch to HTML or make alternate visual layouts unless the user explicitly asks.

## Pipeline

<<<<<<< HEAD
1. Read `cv.md` as source of truth.
2. Read `config/profile.yml` for candidate identity and contact info.
3. Ask the user for the JD if not already in context, or use the JD passed in from auto-pipeline.
4. Extract 15-20 keywords from the JD.
5. Detect JD language -> resume language. English is the default.
6. Detect role archetype -> adapt content priority.
7. Select the strongest relevant Experience entries from `cv.md`; default target is 2-3 roles with 2-4 bullets each.
8. Select the strongest relevant Projects from `cv.md`; default target is 3-4 projects with 2-3 bullets each.
9. Reorder bullets by JD relevance and naturally inject JD keywords into existing achievements.
10. Select relevant coursework and skills from `cv.md`/`profile.yml`; keep only items that support this role.
11. Generate the `.tex` file using `templates/cv-template.tex`.
12. Write to `output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex`.
13. Run: `node generate-latex.mjs output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf`.
14. If the script reports any `issues`, fix the `.tex` and rerun until `valid: true`, `compiled: true`, `pages: 1`, and no fullness or missing-section warnings remain.
15. Report: `.tex` path, `.pdf` path, page count, section count, bullet count, keyword coverage %, and any tradeoffs made to fit one page.
=======
1. Read `cv.md` as source of truth
2. Read `config/profile.yml` for candidate identity and contact info
3. Ask the user for the JD if not already in context (text or URL)
4. Extract 15-20 keywords from the JD
5. Detect JD language → CV language (EN default)
6. Detect role archetype → adapt framing
7. Rewrite Professional Summary injecting JD keywords (same rules as `pdf` mode — NEVER invent skills)
8. Select top 3-4 most relevant projects for the offer
9. Reorder experience bullets by JD relevance
10. Inject keywords naturally into existing achievements
11. Build a JSON payload (see schema below) and write to `/tmp/cv-{candidate}-{company}.json`
12. Run: `node build-cv-latex.mjs /tmp/cv-{candidate}-{company}.json output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex`
13. Run: `node generate-latex.mjs output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf`
    *(Replace `{candidate}`, `{company}`, `{YYYY-MM-DD}` with actual values.)*
14. Report: .tex path, .pdf path, file sizes, section count, keyword coverage %
>>>>>>> upstream/main

**Requires:** `tectonic` or `pdflatex` on PATH.

## Language support

- **Localized section titles are fine.** The validator counts `\section{}` blocks instead of matching English titles, so a Spanish/French/German CV (e.g. `\section{Educación}`) validates normally.
- **CJK (Japanese / Chinese / Korean) is NOT supported on this path yet.** The template is a pdfLaTeX / Computer-Modern setup with no CJK font, so kana/kanji/hangul cannot render. `generate-latex.mjs` detects CJK characters and stops with guidance. For a Japanese CV, use `pdf` mode (HTML → PDF), which renders CJK via a `lang="ja"` font fallback.

<<<<<<< HEAD
| Placeholder | Source |
|-------------|--------|
| `{{NAME}}` | `profile.yml -> candidate.full_name` |
| `{{CONTACT_LINE}}` | City/province + phone, built from `profile.yml` |
| `{{EMAIL_URL}}` | Raw email for `mailto:` URL; do not LaTeX-escape |
| `{{EMAIL_DISPLAY}}` | Escaped email for display text |
| `{{PORTFOLIO_URL}}` | Full URL with scheme from `profile.yml -> candidate.portfolio_url` |
| `{{PORTFOLIO_DISPLAY}}` | Display text only, e.g. `omar-ramadan.dev` |
| `{{LINKEDIN_URL}}` | Full URL with scheme |
| `{{LINKEDIN_DISPLAY}}` | Display text only |
| `{{GITHUB_URL}}` | Full URL with scheme |
| `{{GITHUB_DISPLAY}}` | Display text only |
| `{{EDUCATION}}` | LaTeX `\resumeSubheading` block from `cv.md` Education |
| `{{EXPERIENCE}}` | LaTeX `\resumeSubheading` + `\resumeItem` blocks, reordered by JD relevance |
| `{{PROJECTS}}` | LaTeX `\resumeProjectHeading` + `\resumeItem` blocks, top 3-4 selected |
| `{{SKILLS}}` | A full `\ResumeSkills{Languages}{Frameworks}{Developer Tools}{Libraries}` call |

## Page-Fill Selection Rules

Start from this target density, then adjust after compilation:

| Section | Target |
|---------|--------|
| Education | 1 entry + 1 coursework line with 6-10 relevant courses |
| Experience | 2-3 entries, 2-4 bullets each |
| Projects | 3-4 entries, 2-3 bullets each |
| Technical Skills | 4 compact categories using `\ResumeSkills` |

If the resume is **over 1 page**:
- Keep all required sections.
- First shorten long bullets to one line where possible.
- Then reduce projects from 4 to 3.
- Then reduce experience bullets, but keep at least 2 bullets for each selected experience.
- Do not remove the Technical Skills section.

If the resume is **underfilled or looks half-empty**:
- Add another relevant project from `cv.md`.
- Add the strongest omitted bullet from an already-selected role.
- Expand coursework and skill categories with relevant real items.
- Prefer adding content with metrics, named tools, and direct JD overlap.
=======
## JSON Input Schema
>>>>>>> upstream/main

Write a JSON file with this structure. `build-cv-latex.mjs` handles template merge and LaTeX escaping — no need to escape special characters yourself.

<<<<<<< HEAD
### Education

Each entry becomes:

```latex
    \resumeSubheading
      {Institution}{City, State}
      {Degree}{Date Range}
    \resumeSubItem{Relevant Coursework: Course1, Course2, ...}
```
=======
```json
{
  "name": "Jane Smith",
  "contact_line": "San Francisco, CA | +1 415 555 0100",
  "email": { "url": "jane@example.com", "display": "jane@example.com" },
  "linkedin": { "url": "https://linkedin.com/in/janesmith", "display": "linkedin.com/in/janesmith" },
  "github": { "url": "https://github.com/janesmith", "display": "github.com/janesmith" },
  "education": [
    {
      "institution": "University Name",
      "location": "City, State",
      "degree": "Bachelor of Science in Computer Science",
      "dates": "2018 - 2022",
      "coursework": ["Data Structures", "Algorithms", "Machine Learning"]
    }
  ],
  "experience": [
    {
      "company": "Company Name",
      "role": "Job Title",
      "location": "Remote",
      "dates": "June 2022 - Present",
      "bullets": [
        "Achievement bullet with JD keywords injected",
        "Another bullet with quantified impact"
      ]
    }
  ],
  "projects": [
    {
      "name": "Project Name",
      "context": "Tech stack summary for the project line",
      "dates": "",
      "bullets": [
        "What you built and what it does"
      ]
    }
  ],
  "skills": [
    { "category": "Languages", "items": "Python, JavaScript, C++" },
    { "category": "Frameworks", "items": "FastAPI, React, PyTorch" }
  ]
}
```

### Field reference

| Field | Type | Source |
|-------|------|--------|
| `name` | string | `profile.yml → candidate.full_name` |
| `contact_line` | string | Phone / City, State / Visa — built from profile.yml |
| `email.url` | string | Email for `\href{mailto:...}` (sanitized via sanitizeUrl, not LaTeX-escaped) |
| `email.display` | string | Display text for the email link |
| `linkedin.url` | string | Full URL with scheme for `\href{}` (sanitized via sanitizeUrl, not LaTeX-escaped) |
| `linkedin.display` | string | Display text only (no scheme) |
| `github.url` | string | Full URL with scheme for `\href{}` (sanitized via sanitizeUrl, not LaTeX-escaped) |
| `github.display` | string | Display text only (no scheme) |
| `education[].institution` | string | From cv.md Education |
| `education[].location` | string | Institution location |
| `education[].degree` | string | Degree name |
| `education[].dates` | string | Date range |
| `education[].coursework` | string[] | Optional — generates a coursework line if present |
| `experience[].company` | string | From cv.md Experience |
| `experience[].role` | string | Job title |
| `experience[].location` | string | Work location |
| `experience[].dates` | string | Date range |
| `experience[].bullets` | string[] | Reordered and keyword-injected achievement bullets |
| `projects[].name` | string | From cv.md Projects |
| `projects[].context` | string | Tech stack — appears next to project name |
| `projects[].dates` | string | Date range (or empty) |
| `projects[].bullets` | string[] | Selected project achievements |
| `skills[].category` | string | Skill category name (e.g. "Languages", "Frameworks") |
| `skills[].items` | string | Comma-separated skills in that category |
>>>>>>> upstream/main

## LaTeX Escaping (handled by the script)

<<<<<<< HEAD
Each role becomes:

```latex
    \resumeSubheading
      {Company}{Date Range}
      {Role Title}{Location}
      \resumeItemListStart
        \resumeItem{Bullet text with JD keywords injected}
      \resumeItemListEnd
```

### Projects

Each project becomes:

```latex
    \resumeProjectHeading
      {\textbf{Project Name} $|$ \emph{Stack, Tools, Context}}{}
      \resumeItemListStart
        \resumeItem{Bullet text}
      \resumeItemListEnd
```

### Skills

Use the template's compact four-category macro:

```latex
\ResumeSkills
  {Java, Python, TypeScript, JavaScript, C++, SQL, \Csharp, HTML/CSS}
  {Spring Boot, React, Next.js, Vite.js, Svelte, Node.js, Express.js, REST APIs}
  {Git, Docker, GCP, AWS, Firebase, PostgreSQL, YugabyteDB, H2}
  {OpenAI API, TensorFlow/Keras, OpenCV, Pandas, NumPy, Plotly}
```

## LaTeX Escaping

All text content must be escaped for LaTeX before insertion:
=======
`build-cv-latex.mjs` automatically escapes all user-supplied text before insertion:
>>>>>>> upstream/main

| Character | Escape |
|-----------|--------|
| `&` | `\&` |
| `%` | `\%` |
| `$` | `\$` |
| `#` | `\#` |
| `_` | `\_` |
| `{` | `\{` |
| `}` | `\}` |
| `~` | `\textasciitilde{}` |
| `^` | `\textasciicircum{}` |
| `\` | `\textbackslash{}` |
| `±` | `$\pm$` |
| `→` | `$\rightarrow$` |

<<<<<<< HEAD
Do not escape LaTeX commands themselves. Do not escape URL arguments inside `\href{URL}{...}`; only escape the display text.

## ATS Rules
=======
**Exception:** URLs inside `\href{}` are NOT escaped by the LaTeX escaper, but `sanitizeUrl()` still validates the scheme (mailto/http/https) and removes dangerous characters to prevent injection.
>>>>>>> upstream/main

- Single-column layout.
- Standard section headers: Education, Experience, Projects, Technical Skills.
- UTF-8, machine-readable via `\pdfgentounicode=1`.
- Keywords distributed across the first bullets, projects, and skills section.
- No images, graphics, sidebars, or colored body text.

## Keyword Injection Strategy

- Never add skills the candidate does not have.
- Only reformulate existing experience using JD vocabulary.
- Example: JD says "RAG pipelines"; CV says "LLM workflows with retrieval" -> rewrite as "RAG pipeline design and LLM orchestration workflows."
- Example: JD says "MLOps"; CV says "observability, evals" -> rewrite as "MLOps and observability: evals, error handling, cost monitoring."

## Post-Generation QA

After generating and compiling, inspect the JSON from `generate-latex.mjs`.

The generation is acceptable only when:
- `valid` is `true`
- `compiled` is `true`
- `pages` is exactly `1`
- no required section is missing
- `counts.resumeItems` is at least 10
- `counts.subheadings` is at least 3
- `counts.projectHeadings` is at least 2

If any check fails, revise the `.tex` and rerun the compiler. Do not hand back a PDF that is two pages, half-full, missing sections, or missing the strongest relevant content from `cv.md`.

## Overleaf Compatibility

The generated `.tex` file uses standard CTAN packages only:

- `latexsym`, `fullpage`, `titlesec`, `marvosym`, `color`, `verbatim`, `enumitem`
- `hyperref`, `fancyhdr`, `babel`, `tabularx`, `glyphtounicode`

Upload the `.tex` file directly to Overleaf; it should compile without extra configuration.
