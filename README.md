# Strategy Cascade

A single-page strategy workspace: 113 levels, 2,352 components, dashboards, review and print, a finance toolkit, and the **Analysis Lab** — 72 working calculators that read from and write back to the same plan.

Everything runs offline. No server, no account, no build step.

## Run it

**Desktop app** — double-click `StrategyCascade.exe`.

On first launch it unpacks the page to `%LOCALAPPDATA%\Strategy Cascade\` and opens it in an Edge or Chrome app window (no address bar, no tabs). If neither browser is installed it falls back to your default browser. Your data stays in that folder.

**Browser** — open `index.html` in any modern browser. It is fully self-contained (2.6 MB, no external requests).

## What is inside

| Area | What it does |
| --- | --- |
| Cascade | 113 levels across 20 general domains, 2,352 components with status, owner, due date, answers, evidence, risks, KPIs, decisions and history |
| Overview | Progress, what is late, live, blocked or waiting on a decision |
| Analysis Lab (page) | 72 tools in 10 categories, searchable and filterable, with saved and recent shortcuts |
| Analysis Lab (quick panel) | The same tools in a two-column panel; `Alt + L` or the Lab button |
| Command palette | `Ctrl/Cmd + K` searches pages, levels, components, tools and actions |
| Finance toolkit | Corporate finance menu, level 21 working tables |
| Campaign budget, software options, versions, data sources | Planning and reference pages |
| Review and print | Export the plan, print or save as PDF |

### The 72 tools

Finance and valuation (18), pricing and revenue, marketing and growth (8), risk and governance (8), operations and supply (8), people and organisation, data and technology (12), strategy and portfolio (18), ESG and impact, planning and delivery.

Every tool recalculates as you type, exports the result to CSV, copies to the clipboard, prints, and resets.

**Eight tools read your plan:** risk register, component calculator, OKR alignment, 90-day plan, Gantt, resource histogram, decision log and benefits tracker.

**Seven tools write back to a component:** risk register, pre-mortem and vendor risk add risks; OKR alignment and benefits upsert KPIs by name; decision log adds decisions; the 90-day plan generator sets due dates and owners across a chosen scope.

## Keyboard

| Keys | Action |
| --- | --- |
| `Ctrl/Cmd + K` | Command palette |
| `Alt + L` | Analysis Lab page |
| `Alt + T` | Finance toolkit |
| `Alt + I` | Cascade insights |
| `Ctrl + B` | Collapse or expand the sidebar |
| `?` | Shortcut help |
| `Esc` | Close any panel |

## Repository layout

```
index.html                the whole app, self-contained
StrategyCascade.exe       Windows launcher with index.html embedded
Strategy-Cascade-1.1.zip  both files, this README and the two source folders below
lab/                      (inside the ZIP) source of the Analysis Lab and its tests
desktop/                  (inside the ZIP) launcher source and icon
```

Unpack `Strategy-Cascade-1.1.zip` to rebuild: the commands in the next section expect `lab/` and `desktop/` next to `index.html`.

## Rebuilding

The Lab bundle is concatenated from `lab/*.js` (except files starting with `_`) and injected before the last `</body>`:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File lab\build.ps1
```

Rebuild the launcher (needs the .NET Framework compiler that ships with Windows):

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File desktop\make-icon.ps1
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe /nologo /target:winexe /platform:anycpu /optimize+ `
  /out:"StrategyCascade.exe" /win32icon:"desktop\app.ico" /resource:"index.html",index.html `
  "desktop\Program.cs" /r:System.Windows.Forms.dll
```

## Tests

Start Chrome with `--remote-debugging-port` is handled by the scripts; just run:

```powershell
node lab\smoke.mjs   # registry, palette, plan mutation API, write-backs
node lab\ux.mjs      # 72 tools, stress inputs, Lab page, palette keys, regressions
node lab\shot.mjs    # screenshots to %TEMP%\opencode
```

## Data

State is stored in `localStorage` under `strategy-cascade-v1`; Lab favourites, recents and inputs use `sc-lab-*`. Nothing leaves the machine. Clearing site data resets the workspace.
