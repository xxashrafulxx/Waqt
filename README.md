# Waqt

# Waqt

*Waqt* — time. Turns an automatic tap into a decision. Guards the apps you choose
with a pause screen and a small deliberate task before the app opens.

## Repo layout

```
app/src/main/java/com/waqt/
  App.kt          Application, Card / TextLayer models, background image cache
  Store.kt        All persistence: settings, per-app rules, counters, 7-day history
  Theme.kt        Resolves light / dark / system
  Ui.kt           Design tokens and shared view builders
  Base.kt         Themed activity scaffold with the bottom tab bar
  MainActivity.kt Today tab
  AppsActivity.kt Apps tab
  AppRuleActivity.kt  Per-app timing and hours
  CardsActivity.kt    Screens tab
  EditorActivity.kt   Pause screen editor
  SettingsActivity.kt Settings tab, including the theme picker
  GuardService.kt Accessibility service that watches foreground app changes
  Overlay.kt      The pause screen itself
  Challenges.kt   Tap / hold / swipe challenges
```

The sources are real files now rather than a heredoc inside the workflow, so the
IDE, incremental builds, and diffs all work.

## Building

Push to `main` and the workflow builds a debug APK and publishes it as a release.

**Set the signing key first**, or every build gets a throwaway key and will not
install over the previous one:

```bash
keytool -genkey -v -keystore keys/debug.keystore -storepass android \
  -alias androiddebugkey -keypass android -keyalg RSA -keysize 2048 \
  -validity 10000 -dname "CN=Waqt"
base64 -w0 keys/debug.keystore
```

Put that base64 string in a repo secret named `DEBUG_KEYSTORE_B64`. Do not commit
`keys/debug.keystore` itself.

`versionCode` comes from the CI run number, so updates install cleanly.

## Theme

Settings → Appearance offers System, Light, and Dark. System follows the phone.
Pause screens keep their own per-screen colours in every mode — they are meant to
read as an interruption, not as another surface of the app.

## Notes on behaviour

- The pause overlay uses no system keyboard. Every challenge is tap, hold, or
  swipe driven, because an IME over an overlay window is unreliable across OEMs.
- Text on a pause screen sits in one of three bands (top, middle, bottom) rather
  than at a free x/y position, so it can never end up under the buttons.
- Turning back at a pause screen does not open a free re-entry window. Only
  leaving an app normally does.
- The one-minute auto-close covers the challenge, not the countdown before it.
- Google Play does not allow accessibility services used for non-accessibility
  purposes, so GitHub releases is the distribution channel.
