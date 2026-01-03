# SHOWING

*A browser-based action-score player for distributed performance*

**SHOWING** is a lightweight web app (served via GitHub Pages) that reads a **CSV score** (typically from Google Sheets) and schedules **text, image, and sound cues** on a performer’s device, based on local time or relative timing.

Each performer opens the same link but receives **their own column** of instructions.

No server, no login, no installation.

---

## 1. Basic idea

* The **score lives in a spreadsheet**
* **Each column = one performer**
* **Each row = one scheduled action**
* The app:

  * waits for the performer to press **Start**
  * treats that moment as *time zero*
  * then executes cues at the specified times

---

## 2. Hosting the app (once)

1. Put `index.html` in a GitHub repository
2. Enable **GitHub Pages** (Settings → Pages → deploy from main branch)
3. Note the public URL, e.g.:

```
https://yourname.github.io/showing/
```

That’s the app URL performers will open.

---

## 3. Preparing the score (Google Sheets)

### 3.1 Spreadsheet structure

* **No header row required**
* Each column is one performer (A = performer 1, B = performer 2, etc.)
* Each cell contains **one action line**

Example:

| Performer 1                            | Performer 2           | Performer 3           |
| -------------------------------------- | --------------------- | --------------------- |
| 0:0:0+0s TEXT=Welcome                  | 0:0:0+0s TEXT=Welcome | 0:0:0+0s TEXT=Welcome |
| 0:0:0+10s SOUND=assets/audio/a.mp3     |                       |                       |
| 20:34:00+0s IMAGE=assets/img/scene.jpg |                       |                       |

---

### 3.2 Publishing the sheet as CSV (important)

In Google Sheets:

1. **File → Share → Publish to web**
2. Choose:

   * Format: **Comma-separated values (.csv)**
   * Sheet: the one containing the score
3. Publish
4. Copy the generated CSV URL

This URL is your **data source**.

> ⚠️ Google may cache updates. Changes usually appear after **~30 seconds to a few minutes**.

---

## 4. URL parameters (how performers join)

The app is controlled entirely via URL parameters.

### Required

```
?csv=PASTE_THE_PUBLISHED_CSV_URL_HERE
```

(URL-encoded)

---

### Performer selection

```
&p=1        → performer column A
&p=2        → performer column B
&p=3        → performer column C
```

#### Random performer assignment

```
&p=random
```

or

```
&p=r
```

The app will randomly choose **one of the columns that actually contains data**.

---

### Debug mode (for rehearsals)

```
&debug=true
```

Shows:

* parsing errors
* loaded cues
* timing info
* refresh activity
* audio diagnostics

Performers normally **do not** use debug mode.

---

### Example full URL

```
https://yourname.github.io/showing/?csv=ENCODED_CSV_URL&p=2
```

---

## 5. Action syntax (the score language)

Each cell must contain **one line**:

```
<TIME> <ACTION>
```

### 5.1 Time formats

#### Relative to Start

```
0:0:0+6s
```

→ 6 seconds after the performer presses **Start**

#### Absolute local time

```
20:34:00+23s
```

→ 23 seconds after 20:34:00 (local device time)

If that time has already passed today, it is scheduled **for tomorrow**.

---

### 5.2 Actions

#### Text

```
TEXT=Some text to display
```

* White text on black
* Automatically scaled to fill the screen

---

#### Image

```
IMAGE=assets/images/photo.jpg
```

* Full-screen
* Covers the entire display (may crop)

---

#### Sound

```
SOUND=assets/audio/sound.mp3
```

* Plays audio
* Shows a centered 🎧 icon

> Audio files should be hosted **inside the same GitHub Pages repo** for reliability.

---

#### Combined actions

```
SOUND&IMAGE=assets/audio/a.mp3 assets/images/b.jpg
```

```
SOUND&TEXT=assets/audio/a.mp3 Listen carefully
```

---

## 6. Refreshing the score during the show

You can **edit the Google Sheet while the show is running**.

To make the app reload the data invisibly, add this line to the score:

```
0:0:0+0s CMD=refreshdata
```

When this cue fires, the app will:

1. Fetch the latest CSV from Google
2. Re-parse the performer’s column
3. Rebuild the remaining schedule
4. Continue without visual interruption

### Practical advice

* Place `CMD=refreshdata` **well before** you need the changes
* For safety, you can include **multiple refresh commands** at different times
* Expect adaptability on the order of **~30 seconds to a few minutes**, depending on Google’s cache

---

## 7. Performer experience

1. Performer opens the link
2. (Optional) taps **Fullscreen**
3. Taps **Start**
4. Puts the phone down
5. The device becomes a silent score reader

No scrolling, no interaction, no browser UI distractions.

---

## 8. Supported media formats

* **Audio:** mp3, wav, m4a (hosted locally)
* **Images:** jpg, png, webp
* **Text:** UTF-8 (emoji supported)

---

## 9. Known limitations

* Uses **local device time**, not an atomic clock
* Google Sheets CSV updates are **not instantaneous**
* True browser chrome removal is not possible on all devices
  (Fullscreen + “Add to Home Screen” gives best results)

---

## 10. Typical workflow

1. Rehearsal:

   * Use `&debug=true`
   * Test timing and parsing
2. Performance:

   * Remove `debug`
   * Distribute performer-specific links
3. During show:

   * Edit the Google Sheet
   * Let `CMD=refreshdata` adapt the score

---

## 11. License / reuse

This project is designed by [Adrián Artacho](https://www.artacho.at/) for **artistic, educational, and experimental use**.
Feel free to fork, adapt, and remix it for your own performances.
