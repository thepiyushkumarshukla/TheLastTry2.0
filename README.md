<div align="center">

# 🎯 The Last Try 2.0

### ⚡ Automated Offensive Reconnaissance Suite for Kali Linux

*Give it a target. Walk away. Come back to everything a careful hacker would have found by hand.*

<br>

![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)
![Language](https://img.shields.io/badge/Python-3.9+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Tools](https://img.shields.io/badge/Tools_Orchestrated-25+-red?style=for-the-badge)
![License](https://img.shields.io/badge/Use-Authorized%20Testing%20Only-critical?style=for-the-badge)
![Version](https://img.shields.io/badge/Version-2.0.0-brightgreen?style=for-the-badge)

</div>

---

> ### ⚠️ Authorized Testing Only
> The Last Try is a **dual-use security tool**. Use it **only** against assets you own or are **explicitly authorized** to test — your own lab, a signed pentest engagement, or a bug-bounty program that lists the target in scope. You are responsible for how you use it. Built for **Linux / Kali**, run as **root**.

---

## 🧠 What Is This?

**The Last Try 2.0** is a single orchestrator that drives **25+ best-in-class recon tools** as one smart, chained pipeline. Instead of running `subfinder`, then copy-pasting into `httpx`, then into `nuclei`, then `ffuf`, then `nmap`… one by one, for hours — you answer **three questions** and walk away.

It does the whole recon journey end-to-end:

```
🎯 target.com
    │
    ├─ 🔍 Subdomains ─── passive + brute-force + permutations
    ├─ 🌐 DNS ────────── records (ANY/TXT/SOA/CNAME…) + zone transfer
    ├─ 🔌 Ports ──────── naabu → per-host nmap service detection
    ├─ 📡 HTTP ───────── live host probing (the pivot point)
    ├─ 📂 Content ─────── ffuf dirs + katana crawl + gau/wayback + JS + params
    ├─ 🧪 Vulns ───────── nuclei templated scan
    ├─ 📸 Extras ─────── screenshots + fingerprint + takeover checks
    │
    └─ 📊 SUMMARY.md ── one clean report tying it all together
```

**No babysitting. No copy-pasting. No missed steps.**

---

## ✨ Why It's Different

Most "run all the tools" scripts are noisy, unsafe, and dumb. The Last Try is built by someone who's actually felt the pain of manual recon:

| 🛡️ | **Router-Safe by Design** | A global concurrency governor + per-tool rate limits so it **won't knock your home Wi-Fi offline** (the classic "nuclei took down my router" problem). |
| :---: | :--- | :--- |
| 🎚️ | **Two Modes** | **Regular** (gentle, rate-limited, for home connections) and **Beast** (unleashed, recursive, for a fast VPS). |
| 🎯 | **Scope-Aware** | Only *your* in-scope targets get scanned. Third-party nameservers, CDNs, and IPs are recorded but **never touched**. |
| 🧹 | **Smart, Not Dumb** | Soft-404 / fake-200 killer, per-host port targeting, cross-tool deduplication — you see *valid findings*, not noise. |
| ⏸️ | **Pause & Resume** | Hit `Ctrl+C` (or lose internet) → it saves everything and writes a report. Re-run to **continue exactly where you left off**. |
| ⏭️ | **Skip on the Fly** | Press `S` twice to skip whatever's running and jump to the next element. |
| 🌍 | **Network Resilient** | Detects a real internet outage (not just a tool erroring), pauses, waits up to 2 minutes, and resumes. |
| 🔧 | **Fully Customizable** | Override any tool's exact command, pin your own wordlists/templates, add a required bug-bounty User-Agent — all via simple config files, **no code editing**. |
| 📦 | **Auto-Everything** | Installs missing tools from official repos and auto-downloads default wordlists on first run. |
| 🎨 | **Beautiful TUI** | Clean, aligned output with motivational quotes — while saving complete, untruncated tool output to disk. |

---

## 🚀 Installation

> 🐧 **Target platform: Kali / Debian-based Linux, run as root.**

### 1️⃣ Clone the repo

```bash
git clone https://github.com/<your-username>/The-Last-Try-2.0.git
cd The-Last-Try-2.0
```

### 2️⃣ Run the bootstrap installer

```bash
sudo bash install.sh
```

This installs the lightweight essentials the orchestrator needs — Python deps (`rich`, `pyyaml`) and the build toolchain (`go`, `git`, `pipx`, `chromium`). The **25+ recon binaries are auto-installed on first run** from their official sources, so you don't have to hunt them down.

> 💡 **Optional:** pre-install *every* recon tool up front (great for provisioning a fresh VPS):
> ```bash
> sudo bash install.sh --with-tools
> # or
> sudo python3 tlt.py --install-all
> ```

### 3️⃣ Verify it works (no network touched)

```bash
python3 tlt.py --dry-run
```

This walks the prompts and prints the **entire execution plan** without running a single tool — perfect for a first look.

> ⚙️ After install, open a new shell (or `source ~/.bashrc`) so `$(go env GOPATH)/bin` is on your `PATH` and freshly-installed Go tools resolve.

---

## 🎮 How To Use It

### The 3 questions

When you launch it, you answer three things and walk away:

| Step | Question | What it means |
| :---: | :--- | :--- |
| 1️⃣ | **Wildcard in scope?** `[y/N]` | `y` unlocks full subdomain discovery. `n` = test only the exact host(s), skip all subdomain hunting. |
| 2️⃣ | **Mode?** `[regular/beast]` | **Regular** = rate-limited & router-safe. **Beast** = unlimited + recursive depth-4 fuzzing (VPS). |
| 3️⃣ | **Target** | A single domain **or** a path to a scope file (one domain per line). |

Then confirm authorization, and it takes over.

### ⌨️ Real-time controls (while it runs)

| Key | Action |
| :---: | :--- |
| **`S` `S`** (twice) | ⏭️ Skip the current step and move to the next element. |
| **`Ctrl + C`** | ⏸️ Pause safely — saves everything, writes a report, and tells you how to resume. |

### 🚩 Command-line flags

```
sudo python3 tlt.py [options]

  --dry-run              Preview the full plan without running any tool
  --output-dir PATH      Where to create THE_LAST_TRY-<target>/ (default: cwd)
  --fresh                Ignore a saved session and start this target over
  --only PHASES          Run ONLY these phases, e.g. --only content,nuclei
  --skip PHASES          Skip these phases, e.g. --skip ports,extras
  --no-install           Don't auto-install missing tools; skip their phases
  --install-all          Install every recon tool, then exit (VPS provisioning)
  --yes-i-am-authorized  Skip the authorization prompt (for automation)
```

> 🎯 **Phase keys** for `--only` / `--skip`: `subs`, `subs_active`, `dns`, `ports`, `http`, `content`, `nuclei`, `extras`. Filtered runs auto-load prior results from disk, so `--only content` still works even if `http` ran in an earlier session.

> ⚡ **Regular-mode rate:** after choosing Regular, you're asked for a max requests/sec (default **80**, press Enter to keep it). Raise it on a fast line, lower it on weak Wi-Fi — DNS brute-forcing auto-scales with it. On **resume**, the original session's rate is kept (a new value is ignored) so partial + new results stay consistent.

---

## ⚙️ Customization (Zero Code Editing)

Everything is driven by simple config files in the project root. Leave them blank for smart defaults; fill them in to take control.

<details>
<summary><b>📖 <code>custom_wordlist_preference.yaml</code> — your wordlists, templates & ports</b></summary>

<br>

Pin your own assets. **Leave blank and the tool auto-downloads a portable default** into `./tlt_wordlists/` on first run:

```yaml
subdomain_bruteforce_wordlist_path: []   # blank = auto-download
directory_bruteforcing_wordlist_path: []
nuclei_template_path: []                  # supports multiple, comma-separated
resolvers_path: []
permutation_wordlist_path: []
parameter_wordlist_path: []
extensions: []                            # e.g. [php, txt, html]
ports: []                                 # blank = top-5000 popular ports
```
</details>

<details>
<summary><b>🛠️ <code>custom_commands.yaml</code> — override any tool's exact command</b></summary>

<br>

Replace the built-in command for **any** tool, separately per mode, using `{{PLACEHOLDER}}` tokens the suite fills in. Blank = built-in default.

```yaml
tools:
  nmap:  { regular: "nmap {{HOST}} -sV -sT -vv -T5 -p {{PORTS}} --script=banner -oN {{OUTPUT}}", beast: "" }

# Run your OWN commands at the very end, in both modes:
final_commands:
  - echo "{{TARGET}}" | waybackurls >> custom/extra_wayback.txt
```
</details>

<details>
<summary><b>🕵️ <code>user_agent_requirement.txt</code> — good-faith bug-bounty header</b></summary>

<br>

Many programs require you to identify your traffic. Drop your token on one line and **every target-facing tool** appends it automatically:

```
BugBounty-YourName
```
Blank = each tool uses its own default User-Agent.
</details>

<details>
<summary><b>🚫 <code>scope_exclude.txt</code> — out-of-scope safety net</b></summary>

<br>

Hosts listed here are **never scanned**, even if discovered — protecting you from hitting out-of-scope assets (which get you disqualified). One pattern per line:

```
admin.example.com     # this host AND everything under it
*.dev.example.com     # every subdomain of dev.example.com
staging.example.com
```
Enforced at the single scope chokepoint, so it applies to **every** phase automatically. Blank = nothing excluded.
</details>

---

## 🗂️ Output Layout

Everything lands in a self-describing folder — `THE_LAST_TRY-<target>/`:

```
THE_LAST_TRY-target.com/
├── 📁 subdomains/    all found + resolved (alive, in-scope) hosts
├── 📁 dns/           A/CNAME/TXT/SOA records + AXFR attempts
├── 📁 ports/         naabu open ports → per-host nmap service scans
├── 📁 http/          httpx live hosts  ← the pivot
├── 📁 content/       per-host ffuf dirs (soft-404 filtered) + crawl output
├── 📁 urls/          katana + gau + wayback, merged & deduped
├── 📁 javascript/    harvested JS files
├── 📁 params/        discovered parameters (arjun)
├── 📁 nuclei/        vulnerability findings (.jsonl)
├── 📁 extras/        screenshots refs, whatweb, takeover candidates
├── 📁 custom/        output from your custom final commands
├── 📁 logs/          full untruncated commands + tool logs
├── 📊 report/dashboard.html  👈 rich dark-themed dashboard (charts + SHOW-ALL)
├── 📄 report/SUMMARY.md      human-readable markdown index
└── 📄 tlt_session.json       resume checkpoint (crash-safe)
```

> 🎨 **Open `report/dashboard.html` in any browser** for the full experience — KPI tiles, a severity donut chart, coverage bars, and every finding beautifully laid out. Long lists (URLs, subdomains) show a preview with a **SHOW ALL** button that opens the complete raw file in a new tab. Works fully offline (`file://`), no server needed.

---

## 🧰 Tools Orchestrated

<div align="center">

| Phase | Tools |
| :--- | :--- |
| 🔍 **Passive Subdomains** | `subfinder` · `assetfinder` · `findomain` · `amass` |
| 💥 **Active Discovery** | `puredns` · `gotator` · `shuffledns` · `massdns` |
| 🌐 **DNS** | `dnsx` · `dig` |
| 🔌 **Ports & Services** | `naabu` · `nmap` |
| 📡 **HTTP Probing** | `httpx` |
| 📂 **Content & Crawl** | `ffuf` · `katana` · `gau` · `waybackurls` · `arjun` |
| 🧪 **Vulnerabilities** | `nuclei` |
| 📸 **Extras** | `gowitness` · `whatweb` |

</div>

*Missing tools are auto-installed from their official repos. Anything that can't be installed is skipped with a warning — the run always continues.*

---

## 🏁 Getting Started — Your First Run

Here's the simplest way to try it end-to-end. **Start with a dry-run** to see the plan, then do it for real:

```bash
# 1. Sanity check — see the full plan, touches nothing
python3 tlt.py --dry-run

# 2. The real thing — run from the folder where you want results saved
sudo python3 tlt.py
```

Then just answer the prompts:

```
Wildcard in scope? [y/N]:  y
Mode? [regular/beast]:     regular
Target domain or scope:    example.com
Type 'I AM AUTHORIZED':    I AM AUTHORIZED
```

…and walk away. ☕

When you come back, open the report:

```bash
cat THE_LAST_TRY-example.com/SUMMARY.md
```

> 💡 **Interrupted?** Just re-run the exact same command — it auto-detects the saved session and continues **exactly where it left off**, skipping everything already done. Use `--fresh` to start over instead.

---

<div align="center">

### 💬 *"Everybody has a plan until they get punched in the mouth."*

**Point it at a target. Walk away. Come back to everything.**

<br>

⭐ *If this saved you hours of manual recon, drop a star.* ⭐

<sub>The Last Try 2.0 — for authorized reconnaissance only.</sub>

</div>
