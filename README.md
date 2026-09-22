# Munawar AI — Offline + Gemini Native Audio Voice Assistant (Java)

Android voice assistant, **Java only** (no Kotlin), XML layouts, MVVM.
Wake phrase: **"Hey Munawar"**. Works offline for phone commands and online with **Gemini native audio (Live API)** for human-like conversation.

## Features
- **Setup gate:** app pehle API key + system tests chalata hai; tests pass hone ke baad hi assistant shuru hota hai.
  Tests: (1) API key valid (2) Gemini text model (3) Gemini native-audio Live session (4) mic + speech recognition (5) text-to-speech.
- **Gemini Live (native audio):** real-time streaming voice, interruption, transcripts, function-calling (phone commands Gemini se bhi chalte hain).
- **Offline mode:** Android SpeechRecognizer (offline preferred) + local command parser + offline TTS + offline fallback replies.
- **Personalities:** Girlfriend / Boyfriend / Normal (alag system prompt, alag Gemini voice, alag TTS pitch).
- **Phone commands** (English + Roman Urdu/Hindi + Urdu/Hindi script): camera, gallery, WhatsApp/YouTube/Instagram/any installed app, flashlight, Bluetooth, Wi-Fi, brightness, volume, mute/unmute, music play/stop, call, dial, message, contacts, battery, time/date, web search.
- **Extras:** conversation memory, recent command history, smart suggestions (most-used apps/commands), foreground listening service with notification controls (Talk / Pause / Stop), continuous listening, battery-optimization helper, dark/light/system theme.
- **UI:** animated avatar orb, waveform, listening/speaking indicators, typing animation, glass cards, personality bottom sheet, portrait + landscape.

## Build without a PC — GitHub Actions
No Android Studio/AndroidIDE build needed: push this project to GitHub and a
free GitHub Actions runner builds the APK for you. Full step-by-step guide:
**[GITHUB_BUILD_GUIDE.md](GITHUB_BUILD_GUIDE.md)**.

## Setup (Android Studio)
1. Android Studio (Ladybug ya naya) me **Open** → `MunawarAI` folder.
2. Gradle sync hone do (AGP 8.2.2, Gradle 8.2, JDK 17). SDK Platform **34** install hona chahiye.
3. Device/emulator chalao → **Run**.

### AndroidIDE (phone par build)
Project open karo → Gradle sync → Build → `assembleDebug`. JDK 17 use karo. Ye project AndroidIDE ke liye conservative versions par set hai (AGP 8.2.2, compileSdk 34). Agar error aaye to Build Output ki **pehli** error line dekho — baaki aksar usi ka asar hoti hain.

## Gemini API key
1. https://aistudio.google.com/apikey par free key banao.
2. App kholo → key paste karo → **Sab tests chalao**.
3. Sab green hone par **Save karo aur shuru karo**. Key sirf phone me *EncryptedSharedPreferences* me store hoti hai; APK me kabhi nahi.
4. Baad me Settings → *API key & tests dobara chalao*.

**Model note:** `gemini-2.5-flash-native-audio-preview-12-2025` Google retire kar raha hai. Setup test automatically ye order try karta hai:
`gemini-2.5-flash-native-audio-preview-12-2025` → `gemini-2.5-flash-native-audio-latest` → `...preview-09-2025` → `gemini-3.1-flash-live-preview`
aur jo pehla kaam kare wo save ho jata hai. Agar koi bhi Live model na chale to text (Gemini) + phone ki awaaz (TTS) use hoti hai.

## APK build
- Debug: `./gradlew assembleDebug` → `app/build/outputs/apk/debug/app-debug.apk`
- Release: `./gradlew assembleRelease` (debug key se sign hota hai — personal use). Play Store ke liye apna keystore banao.
- Android Studio: *Build → Build Bundle(s)/APK(s) → Build APK(s)*.

## Dependencies
appcompat 1.6.1, material 1.11.0, recyclerview 1.3.2, core 1.12.0, lifecycle-viewmodel/livedata 2.7.0, security-crypto 1.0.0, okhttp 4.12.0, gson 2.10.1. (compileSdk/targetSdk 34, minSdk 24)

## Commands (examples)
| Kaam | Bolo / likho |
|---|---|
| Camera / Gallery | "open camera", "camera kholo", "open gallery" |
| Apps | "open WhatsApp", "YouTube kholo", "open Instagram", "open <koi bhi app>" |
| Flashlight | "turn on flashlight", "torch on karo", "torch band karo" |
| Bluetooth / Wi-Fi | "turn on Bluetooth", "wifi off karo" |
| Brightness / Volume | "increase brightness", "brightness kam karo", "volume up" |
| Mute | "mute phone", "unmute" |
| Music | "play music", "stop music" |
| Call / SMS | "call Rahul", "Mom ko call karo", "dial Mom", "message John saying hello" |
| Contacts | "open contacts" |
| Info | "battery kitni hai", "time kya hua hai" |
| Mode | "girlfriend mode", "boyfriend mode", "normal mode" |

## Permissions
RECORD_AUDIO, INTERNET, CALL_PHONE, CAMERA (declared; runtime request nahi hoti kyunki flashlight/camera launch ko zaroorat nahi), BLUETOOTH, BLUETOOTH_CONNECT, MODIFY_AUDIO_SETTINGS, ACCESS_WIFI_STATE, CHANGE_WIFI_STATE, READ_CONTACTS, POST_NOTIFICATIONS, FOREGROUND_SERVICE (+ MICROPHONE type), VIBRATE, FLASHLIGHT, WRITE_SETTINGS (brightness), SYSTEM_ALERT_WINDOW (optional: background se app kholna), REQUEST_IGNORE_BATTERY_OPTIMIZATIONS.
Runtime permissions pehle ek explanation dialog ke saath maangi jati hain; deny/permanent-deny par graceful fallback aur Settings shortcut milta hai.

## Honest limits (Android ki wajah se)
- **Wi-Fi / Bluetooth on-off:** Android 10+ (Wi-Fi) aur Android 13+ (Bluetooth) apps ko seedha toggle nahi karne dete. App system panel / confirmation dialog kholti hai, ek tap user ko karna hota hai. Android 9 aur purane par direct toggle hota hai.
- **Wake word "Hey Munawar"** SpeechRecognizer loop se chalta hai (true low-power hotword engine nahi), isliye battery zyada lagti hai aur kuch phones par recognizer ki beep aa sakti hai. Settings me Battery optimization band karna behtar hai.
- **Background se app kholna:** Android 10+ me restricted hai. "Display over other apps" permission do, warna notification me "tap to open" aata hai.
- **Mute:** Silent mode ke liye DND access chahiye; warna vibrate mode lagta hai.
- **Brightness** ke liye "Modify system settings" permission chahiye (Settings screen me button hai).
- Girlfriend/Boyfriend modes wholesome hain (caring/cute/funny); explicit content nahi.

## Troubleshooting
- *Speech recognition nahi mili:* Google app install/update karo. Offline ke liye Settings → Google → Offline speech recognition me language pack download karo.
- *Live test fail:* Detail line me Google ka error dikhta hai (model available nahi / key ko Live API permission nahi / internet).
- *Wake word trigger nahi hota:* Mic permission, battery optimization, aur Settings me "Sunne ki zabaan" check karo.

## Folder structure (app/src)
    AndroidManifest.xml
    java/com/munawar/ai/MunawarApp.java
    java/com/munawar/ai/ai/AIChatManager.java
    java/com/munawar/ai/ai/ChatTurn.java
    java/com/munawar/ai/ai/GeminiApiService.java
    java/com/munawar/ai/ai/GeminiLiveClient.java
    java/com/munawar/ai/ai/Persona.java
    java/com/munawar/ai/ai/PersonalityManager.java
    java/com/munawar/ai/command/ActionExecutor.java
    java/com/munawar/ai/command/ActionResult.java
    java/com/munawar/ai/command/BluetoothController.java
    java/com/munawar/ai/command/Command.java
    java/com/munawar/ai/command/CommandProcessor.java
    java/com/munawar/ai/command/CommandType.java
    java/com/munawar/ai/command/ContactHelper.java
    java/com/munawar/ai/command/FlashlightController.java
    java/com/munawar/ai/command/IntentLauncher.java
    java/com/munawar/ai/command/WifiController.java
    java/com/munawar/ai/core/AssistantCore.java
    java/com/munawar/ai/core/AssistantState.java
    java/com/munawar/ai/data/AppPrefs.java
    java/com/munawar/ai/data/ChatMessage.java
    java/com/munawar/ai/data/HistoryStore.java
    java/com/munawar/ai/service/ListeningService.java
    java/com/munawar/ai/ui/AvatarView.java
    java/com/munawar/ai/ui/ChatAdapter.java
    java/com/munawar/ai/ui/MainActivity.java
    java/com/munawar/ai/ui/ModesBottomSheet.java
    java/com/munawar/ai/ui/SettingsActivity.java
    java/com/munawar/ai/ui/SetupActivity.java
    java/com/munawar/ai/ui/TypingDotsView.java
    java/com/munawar/ai/ui/WaveformView.java
    java/com/munawar/ai/util/Event.java
    java/com/munawar/ai/util/Fuzzy.java
    java/com/munawar/ai/util/NetworkUtil.java
    java/com/munawar/ai/util/OfflineBrain.java
    java/com/munawar/ai/util/PermissionManager.java
    java/com/munawar/ai/util/UiUtil.java
    java/com/munawar/ai/voice/LiveAudioIO.java
    java/com/munawar/ai/voice/TTSManager.java
    java/com/munawar/ai/voice/VoiceRecognizerManager.java
    java/com/munawar/ai/voice/WakeWordMatcher.java
    res/drawable/bg_app_gradient.xml
    res/drawable/bg_btn_glass.xml
    res/drawable/bg_btn_primary.xml
    res/drawable/bg_bubble_ai.xml
    res/drawable/bg_bubble_user.xml
    res/drawable/bg_button_primary.xml
    res/drawable/bg_chip.xml
    res/drawable/bg_chip_active.xml
    res/drawable/bg_chip_suggest.xml
    res/drawable/bg_dialog.xml
    res/drawable/bg_dot.xml
    res/drawable/bg_glass.xml
    res/drawable/bg_glass_card.xml
    res/drawable/bg_glass_input.xml
    res/drawable/bg_glass_pill.xml
    res/drawable/bg_glass_selected.xml
    res/drawable/bg_glass_strong.xml
    res/drawable/bg_gradient.xml
    res/drawable/bg_input.xml
    res/drawable/bg_input_field.xml
    res/drawable/bg_input_transparent.xml
    res/drawable/bg_mic_button.xml
    res/drawable/bg_mic_glow.xml
    res/drawable/bg_mic_ring.xml
    res/drawable/bg_mode_card.xml
    res/drawable/bg_mode_card_selected.xml
    res/drawable/bg_sheet.xml
    res/drawable/bg_suggestion.xml
    res/drawable/ic_back.xml
    res/drawable/ic_check.xml
    res/drawable/ic_close.xml
    res/drawable/ic_delete.xml
    res/drawable/ic_eye.xml
    res/drawable/ic_launcher_foreground.xml
    res/drawable/ic_mic.xml
    res/drawable/ic_more.xml
    res/drawable/ic_notification.xml
    res/drawable/ic_notify.xml
    res/drawable/ic_paste.xml
    res/drawable/ic_pending.xml
    res/drawable/ic_send.xml
    res/drawable/ic_settings.xml
    res/drawable/ic_stop.xml
    res/drawable/ic_visibility.xml
    res/drawable/ic_warn.xml
    res/layout-land/activity_main.xml
    res/layout/activity_main.xml
    res/layout/activity_settings.xml
    res/layout/activity_setup.xml
    res/layout/bottomsheet_modes.xml
    res/layout/dialog_permission.xml
    res/layout/item_chat.xml
    res/mipmap-anydpi-v26/ic_launcher.xml
    res/mipmap-anydpi-v26/ic_launcher_round.xml
    res/mipmap-anydpi/ic_launcher.xml
    res/mipmap-anydpi/ic_launcher_round.xml
    res/values-night/bools.xml
    res/values-night/colors.xml
    res/values/bools.xml
    res/values/colors.xml
    res/values/strings.xml
    res/values/themes.xml
    res/xml/network_security_config.xml
