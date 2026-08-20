# cloud-itonami-lei-549300rs7ewpm9ma6c78

> **Disclaimer**: This is an independent third-party archive/analysis. Not affiliated with, endorsed by, or sponsored by Synchrony Financial.

Archives the publicly published Terms of Service / legal document for **Synchrony Financial**, keyed by its ISO 17442 Legal Entity Identifier (LEI), per ADR-2607110300 (`cloud-itonami-lei-corporate-tos-catalog`, `com-junkawasaki/root`).

## Company

- **Legal name**: Synchrony Financial (GLEIF spells it `SYNCHRONY FINANCIAL`)
- **LEI**: `549300RS7EWPM9MA6C78`
- **Jurisdiction**: US-DE (Delaware file number `3703204`, ELF `XTIQ` = Corporation)
- **Website**: <https://www.synchrony.com>
- **Ticker**: SYF (NYSE)
- **SEC CIK**: `0001601712`

Each of these is cited to a public register in `facts/catalog.edn` and checked
live by the gate below. None of them is asserted here on its own authority.

## Data

- `80-data/public/tos.journal.edn` — the archived document (full text, source URL, retrieval date, sha256).
- `80-data/public/site.journal.edn` — official-website enrichment (title, description, reachability).
- `facts/catalog.edn` — verified public-register citations, with the claim each URL is being relied on for.

## Verifying the citations

```sh
nbb tools/verify_citations.cljs facts/catalog.edn --min 30
```

For every row in `facts/catalog.edn` the gate performs a live `GET`, requires
HTTP 2xx, and requires `:cite/expect-substring` to appear in the response body.

Exit codes distinguish three outcomes that must never share one:

| exit | meaning |
| --- | --- |
| `0` | every citation was checked and every one held |
| `1` | a citation is **wrong** — the body no longer carries the claim (`DRIFT`) |
| `2` | the gate **could not answer** — catalog missing, unparseable, empty, or fewer rows than `--min` |

`--min` is a floor, not a target: it exists so that a truncated or half-written
catalog cannot report a pass by checking two rows and finding both fine.

### What the gate is actually testing

Three independent authorities are cited — GLEIF, the FDIC, and the SEC — and two
joins across them carry the weight:

- **FDIC certificate 27314.** GLEIF records the subsidiary Synchrony Bank as
  validated at authority `RA000744` (which GLEIF itself resolves to the Federal
  Deposit Insurance Corporation) under id `27314`. Asked for certificate 27314,
  the FDIC returns Synchrony Bank *and* names its holding company as
  `SYNCHRONY FINANCIAL`. The parent-child edge is therefore corroborated by a
  regulator that does not source it from GLEIF.
- **777 Long Ridge Road.** GLEIF gives it as the headquarters address; SEC EDGAR
  gives the same street as the filer's business address, via an independent
  filing chain.

Repointing the FDIC rows at a different certificate number still returns HTTP
200 — a different bank — so **status alone does not carry those rows; the
substring does.** That is the property the gate exists to hold.

`facts/catalog.edn` also records, in its header, the sources deliberately *not*
cited and why: `www.sec.gov` and `www.ffiec.gov` answer 403 to automated clients
at the edge, `opencorporates.com` serves a CAPTCHA, and `www.nyse.com` renders
via JavaScript and delivers no matchable text. Those are left out rather than
recorded as citations the gate cannot check.
