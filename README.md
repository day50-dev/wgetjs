# wgetjs

`wget` and `curl` can't deal with Javascript. You know this, I know this, the whole internet does. Why do we have to write code to `wget -rc` a site with javascript? That's stupid. 

So finally, **wget for Single Page Applications (SPAs) and JavaScript websites.** About fucking time.

wgetjs is a command-line tool that brings wget-style recursive downloading to modern dynamic websites. Unlike traditional wget, wgetjs uses a headless Chrome browser to render JavaScript, ensuring you capture the fully-rendered HTML that SPAs and dynamic sites generate.

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

# Convert downloaded HTML to markdown
wgetjs -rc --post='markitdown {} > {}.md' http://example.com/
```

## Options

### Download Control

| Option | Description |
|--------|-------------|
| `-rc` | Recursive download (follow links) |
| `-np` | No parent — don't ascend to parent directories |
| `-P N` | Number of parallel workers (default: 1) |
| `-l N`, `--level=N` | Max recursion depth (default: 5, use `0` or `inf` for infinite) |
| `-w SECONDS` | Wait SECONDS between retrievals |
| `--waitretry=SECONDS` | Wait 1..SECONDS between retries (backoff) |
| `--random-wait` | Wait 0.5×WAIT to 1.5×WAIT secs (randomized) |

### URL Filtering

| Option | Description |
|--------|-------------|
| `-A LIST`, `--accept=LIST` | Accept extensions/patterns (e.g., `html,pdf`, `*.mp3`) |
| `-R LIST`, `--reject=LIST` | Reject extensions/patterns (e.g., `jpg,png`, `*.[zb]p2`) |
| `--accept-regex=REGEX` | Accept URLs matching regex |
| `--reject-regex=REGEX` | Reject URLs matching regex |
| `--regex-type=TYPE` | Regex type: `posix` (default) or `pcre` |
| `-D LIST`, `--domains=LIST` | Accepted domains (requires `-H`) |
| `--exclude-domains=LIST` | Rejected domains |
| `-H`, `--span-hosts` | Follow links to other hosts |
| `-L`, `--relative` | Follow relative links only |
| `-I LIST`, `--include-dirs=LIST` | Include directories (wildcards supported) |
| `-X LIST`, `--exclude-dirs=LIST` | Exclude directories (wildcards supported) |
| `--ignore-case` | Ignore case when matching patterns |
| `--post=CMD` | Run command after each download (`{}` = file path) |

### Other

| Option | Description |
|--------|-------------|
| `--method=CMD` | Custom command to fetch HTML (alternative to Chrome) |
| `-h, --help` | Show help |

## Custom Fetch Method

By default, wgetjs uses headless Chrome via the Chrome DevTools Protocol. If you prefer a different renderer, use `--method` with a command that outputs HTML to stdout:

```bash
# URL is appended by default
wgetjs --method="lightpanda fetch --dump" http://somesite.com/

# Or use {} for explicit URL placement (like --post)
wgetjs --method="curl -s {} | tidy -q -" http://somesite.com/
wgetjs --method="playwright fetch {} --html" http://somesite.com/
```

## Requirements

- Node.js
- Google Chrome (or Chromium) installed and PATH
