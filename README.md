# Ride Home

A replacement home screen for the original Peloton Bike (manual resistance knob).

**Three buttons:** YouTube, Ride Log, Interval Timer.
**Automatic:** when she starts pedaling, a metrics bar (time, cadence, resistance,
output, total kJ) appears over whatever is on screen, YouTube included. It hides
about 45 seconds after she stops. Every ride is saved to the Ride Log.

## STATUS: NOT YET RUN ON A BIKE

This was written without a compiler or a bike. Expect the first build to need a
fix or two. Send the build error or `adb logcat` output back and it gets patched.

## 1. Build the APK

**GitHub (no computer setup):** make a new GitHub repo, upload this whole folder,
open the Actions tab, run "Build APK", download `ride-home-apk` when it finishes.
The file inside is `app-debug.apk`.

**Android Studio:** File > Open this folder, let it sync, Build > Build APK.

## 2. Test on any Android phone first (demo mode)

    adb install -r app-debug.apk
    adb shell appops set com.ridehome SYSTEM_ALERT_WINDOW allow
    adb shell am start -n com.ridehome/.LauncherActivity

Off the bike it runs a simulated rider: 100 s pedaling, 60 s rest, repeat. About
3 s in, the metrics bar should appear. About 45 s into the rest it hides, and the
ride shows up in Ride Log. Check the interval timer and the long-press admin menu too.

## 3. Install on the bike

Same three commands with the bike connected over ADB (OpenPelo's tooling works).
Then press the bike's home (P) button and pick **Ride Home**, "Always".
If no picker appears, try:

    adb shell cmd package set-home-activity com.ridehome/.LauncherActivity

**Turn off grupetto's overlay** (or uninstall it) so two bars do not stack.

The status line at the bottom of the home screen should say "Bike connected".

## Admin menu

Long-press the **Ride Home** title, PIN `2580` (change `ADMIN_PIN` in `Cfg.java`).
Gets you back to the stock Peloton home screen, Android settings, metrics bar
top/bottom, demo mode, and a sensor restart.

## Things to verify on the bike

- Resistance matches the number on the stock Peloton screen (assumed 0-100).
- Output (watts) is in the right range for her effort.
- A ride is saved after she stops, and it is long enough to count (60 s minimum).
- The bar does not cover YouTube controls she needs. Move it from the admin menu.

## Limits

- **Original Bike only.** Bike+ and Tread read their sensors differently.
- Reads the bike through Peloton's undocumented internal sensor service. The
  protocol comes from the grupetto and Pelomon projects (this is an independent
  implementation). A Peloton software update could break it. Pin the firmware.
- Needs the YouTube app or a browser installed on the bike. The button tries the
  YouTube app, then Chrome, then any browser.
- Tuning knobs (pedaling thresholds, idle timeout, minimum ride length) are in `Cfg.java`.

## Debugging

    adb logcat -s RideHome.Sensor RideHome.Service RideHome.Store
