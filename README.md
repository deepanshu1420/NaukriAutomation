# Naukri Profile Refresh

Keeps your Naukri profile `recently updated` - recruiters see fresh profiles first.
Every run it toggles a trailing `.` on your **resume headline**, which counts as a
profile update on Naukri. Schedule it hourly and forget about it.

> Important Details:

- Logs in automatically with your **Google account** (session is saved after the first login).
- Runs in an off-screen Chrome window (Naukri blocks headless browsers).
- Verifies the save actually stuck on the server before reporting success.
- All personal data lives in `.env` - nothing sensitive is in the code.

> Requirements:

- Windows 10/11 (uses `Task Scheduler` for the hourly run)
- Node.js v18+
- Google Chrome installed
- A Naukri account that signs in with Google

## Setup & Installation

### 1️⃣ Clone the repository & Install the required dependencies

```bash
https://github.com/deepanshu1420/NaukriAutomation.git
cd NaukriAutomation
npm install
```

### 2️⃣ Open `.env.example` file, fill the required details below, then rename it to `.env`

> `.env` is git-ignored, so your credentials never get pushed.

```env
GOOGLE_EMAIL=you@gmail.com
GOOGLE_PASSWORD=your-google-password
NAUKRI_PROFILE_URL=https://www.naukri.com/mnjuser/profile
NAME=Your Name
EMAIL=you@gmail.com
```

### 3️⃣ First Login (One-Time Setup)

> Run the below command in your IDE's terminal (e.g., Visual Studio Code):

```bash
node naukri-profile-refresh.js login
```

- A Chrome window opens, then signs in with your `Google account`.
- If prompted, complete Google 2-step verification once. The session is saved to `.naukri-chrome-profile/` for future runs.

### 4️⃣ Test a Silent Run (Optional)

```bash
node naukri-profile-refresh.js
```

> Check your terminal or `naukri-refresh.log`, you should see a line like:

```
[27/7/2026, 1:05:12 pm] OK: headline dot added (verified) → "Software Developer | ..."
```

## Run it hourly (Task Scheduler)

> Open Windows PowerShell as Administrator, replace `C:\path\to\NaukriAutomation` with the actual path where you cloned the repository, then copy and run the entire command block below.

```powershell
$repo = "C:\path\to\NaukriAutomation"
$action  = New-ScheduledTaskAction -Execute "node.exe" -Argument "`"$repo\naukri-profile-refresh.js`"" -WorkingDirectory $repo
$trigger = New-ScheduledTaskTrigger -Once -At (Get-Date) -RepetitionInterval (New-TimeSpan -Hours 1)
Register-ScheduledTask -TaskName "NaukriProfileRefresh" -Action $action -Trigger $trigger -Settings (New-ScheduledTaskSettingsSet -StartWhenAvailable)
```

That's it — the script now refreshes your profile every hour while your PC is on.

Useful commands:

```powershell
Get-ScheduledTask NaukriProfileRefresh            # check status
Start-ScheduledTask NaukriProfileRefresh          # run now
Disable-ScheduledTask NaukriProfileRefresh        # pause
Enable-ScheduledTask NaukriProfileRefresh         # resume
Unregister-ScheduledTask NaukriProfileRefresh     # remove
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| `Google login did not complete` in the log | Run `node naukri-profile-refresh.js login` and approve the 2-step verification prompt once manually. |
| `save did not stick` in the log | Naukri changed its headline editor — open an issue. |
| Any other error | Check `naukri-refresh-error-*.png` screenshots in the repo folder — they show exactly what the browser saw when it failed. |
| Want to start fresh | Delete the `.naukri-chrome-profile/` folder and run the `login` step again. |

## Files

| File | Purpose |
|---|---|
| `naukri-profile-refresh.js` | The refresh script |
| `config.js` | Loads `.env` (no dependencies) |
| `.env.example` | Template — copy to `.env` and fill in |
| `naukri-refresh.log` | Run history (git-ignored) |
| `.naukri-chrome-profile/` | Saved Chrome session (git-ignored) |

## Disclaimer

Automating your own profile may be against Naukri's Terms of Service. It only
edits your own headline at a slow, human-like rate, but use at your own risk.
