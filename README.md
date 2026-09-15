# dev-one-liners

The one-liners you keep looking up: Base64, URL encoding, file hashes, UUIDs, Unix timestamps, JSON, JWTs, colors, passwords and regex, side by side for the shell, PowerShell, Python, JavaScript, PHP, Java, C# and SQL.

Copy what you need. Each section also links to a browser version on [LogicUtil](https://logicutil.com) for when there's no terminal handy. Those tools are free, need no sign-up, and never upload what you paste.

## Contents

- [Base64](#base64)
- [URL encoding](#url-encoding)
- [File hashes and checksums](#file-hashes-and-checksums)
- [UUIDs](#uuids)
- [Unix timestamps](#unix-timestamps)
- [JSON: pretty print and minify](#json-pretty-print-and-minify)
- [JWT: read the payload](#jwt-read-the-payload)
- [Colors: HEX and RGB](#colors-hex-and-rgb)
- [Random passwords and secrets](#random-passwords-and-secrets)
- [The same regex in every language](#the-same-regex-in-every-language)

---

## Base64

| Where | Encode | Decode |
|---|---|---|
| Linux / macOS | `base64 file.png` or `echo -n 'text' \| base64` | `base64 -d encoded.txt` (`-D` on older macOS) |
| PowerShell | `[Convert]::ToBase64String([IO.File]::ReadAllBytes('file.png'))` | `[Convert]::FromBase64String($s)` |
| Python | `base64.b64encode(data)` | `base64.b64decode(s)` |
| Browser JS | `btoa(str)` | `atob(s)` |
| Node.js | `Buffer.from(str).toString('base64')` | `Buffer.from(s, 'base64')` |
| PHP | `base64_encode($data)` | `base64_decode($s)` |
| Java | `Base64.getEncoder().encodeToString(bytes)` | `Base64.getDecoder().decode(s)` |

**Gotchas**

- `btoa` only accepts Latin-1. Text with accents or emoji needs a `TextEncoder` step first, or it throws.
- Base64URL (used by JWTs) swaps `+/` for `-_` and usually drops the `=` padding.
- Base64 is an encoding, not encryption. Anyone can decode it.

In the browser: [Base64 Encoder & Decoder](https://logicutil.com/tool/base64-encoder), which handles text, files and images and outputs data URIs.

## URL encoding

| Language | Encode one value | Watch out |
|---|---|---|
| JavaScript | `encodeURIComponent(value)` | `encodeURI` is for a whole address |
| Python | `urllib.parse.quote(value, safe='')` | `quote_plus` turns spaces into `+` |
| PHP | `rawurlencode($value)` | `urlencode` uses `+` for spaces |
| Java | `URLEncoder.encode(value, "UTF-8")` | produces `+`; use `%20` inside a path |
| C# / .NET | `Uri.EscapeDataString(value)` | `HttpUtility.UrlEncode` uses `+` |
| PowerShell | `[uri]::EscapeDataString($value)` | same rules as .NET |

**Gotchas**

- `%2520` means the value was encoded twice. Decode once, then encode once.
- In a query string `+` can mean a space; in a path it is a literal plus.

In the browser: [URL Encoder & Decoder](https://logicutil.com/tool/url-encoder)

## File hashes and checksums

| System | SHA-256 | MD5 |
|---|---|---|
| Windows (cmd) | `certutil -hashfile file.iso SHA256` | `certutil -hashfile file.iso MD5` |
| PowerShell | `Get-FileHash file.iso -Algorithm SHA256` | `Get-FileHash file.iso -Algorithm MD5` |
| Linux | `sha256sum file.iso` | `md5sum file.iso` |
| macOS | `shasum -a 256 file.iso` | `md5 file.iso` |
| Python | `hashlib.sha256(data).hexdigest()` | `hashlib.md5(data).hexdigest()` |
| Node.js | `crypto.createHash('sha256').update(data).digest('hex')` | same with `'md5'` |
| PHP | `hash_file('sha256', $path)` | `md5_file($path)` |

**Gotchas**

- The result is identical everywhere. A hash depends only on the bytes, never on the tool.
- A trailing newline changes the digest completely. `echo` adds one; `echo -n` doesn't.
- Hashes can't be "decrypted". MD5 lookup sites only search tables of common inputs.
- Don't store passwords with MD5 or SHA-256. Use bcrypt, scrypt or Argon2.

In the browser: [Hash Generator](https://logicutil.com/tool/hash-generator) computes MD5 and SHA-1/256/384/512 for text or files, with a checksum verifier.

## UUIDs

| Platform | One-liner |
|---|---|
| JavaScript | `crypto.randomUUID()` |
| Python | `import uuid; uuid.uuid4()` |
| Java | `UUID.randomUUID()` |
| C# / .NET | `Guid.NewGuid()`, or `Guid.CreateVersion7()` on .NET 9 |
| PostgreSQL | `gen_random_uuid()`, or `uuidv7()` from PostgreSQL 18 |
| MySQL | `UUID()` (careful: this is a time-based v1) |
| Linux / macOS | `uuidgen` |
| PowerShell | `[guid]::NewGuid()` |

**Gotchas**

- For database primary keys, prefer v7. It starts with a millisecond timestamp, so new rows land at the end of the index.
- GUID and UUID are the same thing; Windows tools often wrap them in `{}`.

In the browser: [UUID Generator](https://logicutil.com/tool/uuid-generator) generates v1, v3, v4, v5 and v7, up to 1,000 at a time as JSON, CSV or SQL, and inspects existing UUIDs.

## Unix timestamps

| Where | Current timestamp | Timestamp to date |
|---|---|---|
| JavaScript | `Math.floor(Date.now() / 1000)` | `new Date(ts * 1000)` |
| Python | `int(time.time())` | `datetime.fromtimestamp(ts, tz=timezone.utc)` |
| PHP | `time()` | `date('Y-m-d H:i:s', $ts)` |
| MySQL | `UNIX_TIMESTAMP()` | `FROM_UNIXTIME(ts)` |
| PostgreSQL | `EXTRACT(EPOCH FROM now())` | `to_timestamp(ts)` |
| Linux | `date +%s` | `date -d @1700000000` |
| Excel / Google Sheets | – | `=A1/86400 + DATE(1970,1,1)` (UTC) |

**Gotchas**

- 10 digits is seconds; 13 digits is milliseconds. A date in 1970 means you mixed them up.
- A timestamp has no time zone. If the date looks a few hours off, the display zone is wrong, not the number.
- Discord renders `<t:1700000000:R>` as a relative time in each reader's own time zone.

In the browser: [Unix Timestamp Converter](https://logicutil.com/tool/timestamp-converter) converts both ways in any time zone.

## JSON: pretty print and minify

| Where | Pretty print | Minify |
|---|---|---|
| Command line | `python -m json.tool file.json` or `jq . file.json` | `jq -c . file.json` |
| JavaScript | `JSON.stringify(obj, null, 2)` | `JSON.stringify(obj)` |
| Python | `json.dumps(obj, indent=2, ensure_ascii=False)` | `json.dumps(obj, separators=(',', ':'))` |
| PHP | `json_encode($data, JSON_PRETTY_PRINT \| JSON_UNESCAPED_UNICODE)` | `json_encode($data)` |
| PowerShell | `Get-Content file.json \| ConvertFrom-Json \| ConvertTo-Json -Depth 10` | `... \| ConvertTo-Json -Depth 10 -Compress` |
| VS Code | `Shift+Alt+F` (Format Document) | |

**Gotchas**

- Trailing commas, single quotes and comments are legal in JavaScript but not in JSON.
- `ConvertTo-Json` stops at depth 2 unless you pass `-Depth`.
- Integers above 2^53 lose precision in JavaScript. Keep them as strings.

In the browser: [JSON Formatter](https://logicutil.com/tool/json-formatter) validates, formats and minifies, and has a collapsible tree view.

## JWT: read the payload

| Where | How |
|---|---|
| Bash | `echo "$TOKEN" \| cut -d. -f2 \| tr '_-' '/+' \| base64 -d` |
| Browser console | `JSON.parse(atob(token.split('.')[1].replace(/-/g, '+').replace(/_/g, '/')))` |
| Node.js (`jwt-decode`) | `import { jwtDecode } from 'jwt-decode'; jwtDecode(token)` |
| Python (PyJWT) | `jwt.decode(token, options={"verify_signature": False})` |

**Gotchas**

- Decoding is not verifying. All of the above read a forged token just as happily as a real one.
- Base64URL drops padding, so `base64 -d` may need `==` appended for a short payload.
- `exp`, `iat` and `nbf` are Unix timestamps in seconds.

In the browser: [JWT Decoder](https://logicutil.com/tool/jwt-decoder). The token is decoded locally and never sent anywhere.

## Colors: HEX and RGB

| Language | HEX → RGB | RGB → HEX |
|---|---|---|
| JavaScript | `[1, 3, 5].map(i => parseInt(hex.slice(i, i + 2), 16))` | `'#' + [r, g, b].map(c => c.toString(16).padStart(2, '0')).join('')` |
| Python | `tuple(int(h.lstrip('#')[i:i+2], 16) for i in (0, 2, 4))` | `'#%02x%02x%02x' % (r, g, b)` |
| PHP | `sscanf('#0A84FF', '#%02x%02x%02x')` | `sprintf('#%02x%02x%02x', $r, $g, $b)` |
| CSS | `rgb(from #0A84FF r g b)` | not needed: both are valid colors |

**Gotchas**

- `#FFF` is shorthand for `#FFFFFF`. Expand it before slicing.
- An 8-digit HEX carries alpha: `#0A84FF80` is about 50% opacity.

In the browser: [Color Converter](https://logicutil.com/tool/color-converter) converts HEX, RGB, HSL, HSV, CMYK and OKLCH, builds palettes and checks WCAG contrast.

## Random passwords and secrets

| Where | One-liner |
|---|---|
| Linux / macOS | `openssl rand -base64 24` (32 characters) |
| Python | `python -c "import secrets; print(secrets.token_urlsafe(24))"` |
| Node.js | `node -e "console.log(require('crypto').randomBytes(24).toString('base64url'))"` |
| PowerShell | `-join ((33..126) \| Get-Random -Count 20 \| ForEach-Object { [char]$_ })` |

**Gotchas**

- Never use `Math.random()` or Python's `random` module for secrets.
- Length beats symbols: 16+ characters for accounts, 20+ for email and banking.

In the browser: [Password Generator](https://logicutil.com/tool/password-generator)

## The same regex in every language

Matching four digits, `\d{4}`:

| Language | Test for a match | Watch out |
|---|---|---|
| JavaScript | `/\d{4}/.test(text)` | add `u` for full Unicode |
| Python | `re.search(r'\d{4}', text)` | use raw strings; named groups are `(?P<name>...)` |
| Java | `Pattern.compile("\\d{4}").matcher(text).find()` | double the backslashes; `matches()` needs the whole string |
| C# / .NET | `Regex.IsMatch(text, @"\d{4}")` | verbatim `@` strings avoid double escaping |
| PHP | `preg_match('/\d{4}/', $text)` | delimiters are required; add `u` for UTF-8 |
| Go | ``regexp.MustCompile(`\d{4}`).MatchString(text)`` | RE2: no lookbehind, no backreferences |

In the browser: [Regex Tester](https://logicutil.com/tool/regex-tester) shows live matches, numbered and named capture groups, and a replace preview.

---

## Contributing

Found a mistake, or know a cleaner one-liner for another language? Pull requests are welcome. Please keep each entry to a single line that runs as written.

Maintained by [Saman](https://github.com/saman-webdev), who also builds [LogicUtil](https://logicutil.com).
