# Simply Connect — HR Portal

Vanilla HTML / CSS / JS HR portal, live data Google Sheets + Apps Script se.

## Folder structure
```
index.html        → main app (login + dashboard)
styles.css         → sab styling
app.js             → sab JavaScript logic (API calls, rendering, calendar, theme toggle)
assets/            → logo.png (sidebar/topbar/login)
Code.gs            → Google Apps Script backend (yeh Google Sheet mein jaata hai, repo mein reference ke liye)
vercel.json        → Vercel static hosting config
```

## Step 1 — Google Sheet backend

0. Tabs required: `Employee`, `Attendance`, `Payroll`, `Users`, `Requests`, `Devices` — same as `Data_for_portal.xlsx`. (`LeaveBalances` and `Notifications` are in that file too but aren't wired into the app yet.)

**Attendance rule (auto-calculated — don't fill Working Hours/Status manually):**
For every Attendance row (Date, EMP ID, Employee Name, Punch In, Punch Out):
- Punch In maujood hai → Status = **Present**, Working Hours khud calculate hoti hain (raat ko cross-midnight shifts, e.g. 5 PM–2 AM, bhi sahi handle hoti hain).
- Punch In khali hai + Saturday/Sunday → Status = **Weekend**.
- Punch In khali hai + koi aur din → Status = **Absent**.

Ye Apps Script (`computeAttendanceStatus_`) mein hoti hai — sheet ke Status/Working Hours columns ignore ho jate hain, sirf Date/EMP ID/Punch In/Punch Out bharna kaafi hai.

1. `Employee_Details.xlsx` ko Google Sheets mein import karein — tabs bilkul same naam se: `Employee`, `Attendance`, `Payroll`, `Users`, `Requests`, `Devices`.
2. Sheet mein **Extensions > Apps Script** kholein, `Code.gs` ka pura content wahan paste kar dein.
3. **Deploy > New deployment**:
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Deploy karne ke baad jo `.../exec` URL milay usay copy kar lein.

## Step 2 — Frontend mein URL daalein

`app.js` kholein, sab se upar ye line dhoondein:
```js
const CONFIG = {
  API_URL: 'PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE'
};
```
Yahan apna `.../exec` URL paste kar dein aur save kar dein.

## Step 3 — GitHub pe daalein

```bash
git init
git add .
git commit -m "Simply Connect HR Portal"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## Step 4 — Vercel se connect karein

1. [vercel.com](https://vercel.com) pe login karein (GitHub account se).
2. **Add New > Project** → apna GitHub repo select karein.
3. Framework preset: **Other** (ya "No framework") — kuch build command ki zaroorat nahi, ye static site hai.
4. **Deploy** dabayein — 30-60 second mein live ho jayega.
5. Har baar `main` branch pe push karenge, Vercel automatically redeploy kar dega.

## Login test
`Users` sheet ke credentials use karein — e.g. `abdulsaboor#5` / `sc#5` (Employee), ya Admin role wala koi bhi row.

## Performance
- Backend mein 30-second server-side cache — bar bar Sheet read nahi hoti.
- Frontend data ek dafa fetch ho ke memory mein cache hota hai — tabs switch karne pe reload nahi hota, sirf "Refresh" (topbar ka ⟳ icon) dabane se naya data aata hai.
- Employee Add/Edit turant Sheet mein likhta hai aur list foran refresh ho jati hai.

## Abhi khali sheets
`Attendance`, `Payroll`, `Requests`, `Devices` sheets abhi khali hain — jaise hi inme rows aayengi (biometric device se ya manually), dashboard turant unhe dikhana shuru kar dega, code mein kuch change nahi karna parta.
