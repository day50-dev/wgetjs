# wgetjs

**wget for Single Page Applications (SPAs) and JavaScript-heavy websites**

wgetjs is a command-line tool that brings wget-style recursive downloading to modern dynamic websites. Unlike traditional wget, wgetjs uses a headless Chrome browser to render JavaScript, ensuring you capture the fully-rendered HTML that SPAs and dynamic sites generate.

## Why wgetjs?

Traditional tools like `wget` and `curl` only download the initial HTML response. For sites built with React, Vue, Angular, or any framework that renders content client-side, you end up with empty shells. wgetjs solves this by:

- **Rendering JavaScript** - Executes all client-side code before capturing HTML
- **Waiting for dynamic content** - Lets SPAs fully load before saving
- **Recursive crawling** - Follows links through JavaScript-rendered navigation
- **Multiple workers** - Parallel downloads with `-P` for faster mirroring

## Usage

```bash
# Basic recursive download
wgetjs -rc -np http://somesite.com/

# With parallel workers
wgetjs -rc -np -P 4 http://somesite.com/

# Limit recursion depth
wgetjs -rc -l 3 http://somesite.com/

# Only download HTML and PDF files
wgetjs -rc -A html,pdf http://somesite.com/

# Exclude images and certain directories
wgetjs -rc -R jpg,png,gif -X /admin,/private http://somesite.com/

# Only follow links within specific domains
wgetjs -rc -D example.com,cdn.example.com http://example.com/

# Accept only URLs matching a pattern
wgetjs -rc --accept-regex='/docs/.*' http://somesite.com/
```

## Options

### Download Control

| Option | Description |
|--------|-------------|
| `-rc` | Recursive download (follow links) |
| `-np` | No parent — don't ascend to parent directories |
| `-P N` | Number of parallel workers (default: 1) |
| `-l N` | Max recursion depth (default: 5) |
| `-w SECONDS` | Wait SECONDS between retrievals |
| `--waitretry=SECONDS` | Wait 1..SECONDS between retries (backoff) |
| `--random-wait` | Wait 0.5×WAIT to 1.5×WAIT secs (randomized) |

### URL Filtering

| Option | Description |
|--------|-------------|
| `-A LIST` | Accept only these extensions (e.g., `html,pdf`) |
| `-R LIST` | Reject these extensions (e.g., `jpg,png`) |
| `--accept-regex=REGEX` | Only accept URLs matching regex |
| `--reject-regex=REGEX` | Reject URLs matching regex |
| `--regex-type=TYPE` | Regex type: `posix` (default) or `pcre` |
| `-D LIST` | Only follow these domains (comma-separated) |
| `--exclude-domains=LIST` | Never follow these domains |
| `-H` | Span hosts — follow links to other domains |
| `-L` | Follow relative links only |
| `-I LIST` | Include only these directories |
| `-X LIST` | Exclude these directories |

### Other

| Option | Description |
|--------|-------------|
| `--method=CMD` | Custom command to fetch HTML (alternative to Chrome) |
| `-h, --help` | Show help |

## Custom Fetch Method

By default, wgetjs uses headless Chrome via the Chrome DevTools Protocol. If you prefer a different renderer, use `--method` with a command that outputs HTML to stdout:

```bash
# Example with lightpanda
wgetjs --method="lightpanda fetch --dump" http://somesite.com/
```

## Requirements

- Node.js
- Google Chrome (or Chromium) installed and in PATH
