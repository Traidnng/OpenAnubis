<img width="439" height="387" alt="image (9)" src="https://github.com/user-attachments/assets/3be940e5-c846-4c41-85ba-64a92f17efea" /> <img width="439" height="387" alt="image" src="https://github.com/user-attachments/assets/cfa74081-2a32-4fff-9f25-68583a3aac4e" /> https://github.com/Traidnng/OpenAnubis/releases/tag/Realase

# Only Open Anubis
**Runtime Target:** JDK 21  
**Minecraft / Mod Loader:** Minecraft 1.21.11 • Fabric Loader  
**Tooling:** CFR 0.152 Java Decompiler • Custom Resource Extractor  
**Original Author:** Traidnng    

---

## build & runtime status

The extracted source tree represents the complete decompiler output of **Anubis Client 1.0** for Minecraft 1.21.11 (Fabric).

### Running the Mod (Client Runtime)
Place `Anubis Client 1.0.jar` into your `.minecraft/mods/` directory and launch the game with Fabric Loader (>= 0.16.0) on Java 21. If the JAR is launched standalone outside Minecraft (`java -jar "Anubis Client 1.0.jar"`), the embedded `net.anubis.launcher.InstallNotice` entrypoint displays an installation helper dialog prompting the user to locate their active mods folder.

### Source Status
The source is organized into a standard Fabric mod structure under `anubis-src/`. All mixin declarations, mod metadata (`fabric.mod.json`), GLSL shaders, 3D character assets (`.anc`), fonts, and sound effects are bundled with the decompiled `.java` sources. The recovered tree is retained as verified decompiler output.

---

## static security review

The supplied `Anubis Client 1.0.jar` and its decompiled codebase were thoroughly reviewed statically without remote execution:

- **No persistence, startup registry modification, arbitrary class loading, hidden RAT command channels, Discord bot tokens, or credential-file harvesting was identified.**
- **Credential Safety:** No access to `.minecraft` session tokens, `launcher_profiles.json`, `launcher_accounts.json`, Discord `Local Storage/leveldb`, or browser cookies/passwords exists anywhere in the codebase.
- **Network Targets:** Network activity is strictly confined to:
  - Mojang session & skin APIs (`api.mojang.com`, `sessionserver.mojang.com`) for the player skin changer.
  - LRCLIB (`https://lrclib.net/`) for synchronized song lyrics in the Spotify HUD module.
  - An optional public event text feed (`https://donutbets.net/api/client/feed`) that receives text-only community announcements without transmitting player data.
  - YouTube / TikTok live stream status checks (`StaffLiveModule`) for notifying the player when server staff are streaming.
  - Local Discord IPC communication over Windows Named Pipe (`\\.\pipe\discord-ipc-*`) and UNIX domain sockets for Discord Rich Presence.
- **Webhooks:** There are **no hardcoded Discord webhook URLs**. Webhook calls (`WebhookService`, `SpawnerAlerts`) execute strictly if and when the user inputs their own Discord Webhook URL inside module settings (e.g., Spawner Protect alerts, Player Detection radar, Coordinate Snapper).
- **Process Execution:** `ProcessBuilder` is utilized in only two verified benign contexts:
  - In `TamperStop`, to spawn a standalone Swing warning window if JAR integrity validation fails.
  - In `ScriptedPlayer`, to query media metadata and control playback on Linux (`dbus-send` via MPRIS) and macOS (`osascript` via AppleScript) for the Spotify HUD overlay.
- **Native Binaries (DLLs):** The embedded `io/imgui/java/native-bin/imgui-java64.dll` binary was cryptographically checked against Maven Central. Its SHA-256 matches the official `io.github.spair:imgui-java-natives-windows:1.90.0` release bit-for-bit.
- **Built-in In-Game Theft Protection:** Includes `PayGuard.java`, an internal security interceptor that prevents rogue mods and macros from secretly executing `/pay` or `/epay` packets behind the user's back on economy servers.

*This is a static review, not a guarantee against behavior that only appears under an unobserved runtime condition.*

---

## what is included

The repository contains the complete decompiled source tree from the client JAR, preserving the original unminified package structure:

- `net.anubis.client.module.impl.combat` — Combat modules, rotation helpers, bots
- `net.anubis.client.module.impl.movement` — Movement utilities, flight, bypasses
- `net.anubis.client.module.impl.render` — Shaders, ESPs, cosmetics, custom HUD
- `net.anubis.client.module.impl.misc` — Utility modules, staff checkers, radars
- `net.anubis.client.module.impl.donutmacros` — Specialized server automation and macros
- `net.anubis.client.easyplace` — Schematic placement engine and builder routines
- `net.anubis.client.gui` — Dear ImGui bindings, HUD editor, and UI palette
- `net.anubis.client.discord` — Native Discord RPC IPC pipe client
- `net.anubis.client.security` — Build signature verification and PayGuard
- `com.example.client.mixin` — Fabric Mixin hooks and accessors

### Recovered Feature & Support Classes:
- **Combat:** `CrystalAuraModule`, `TriggerbotModule`, `AutoCityMinecraft`, `CrystalBotModule`
- **Movement:** `ElytraFlyEntity`, `JesusModule`, `SpeedModule`, `BoatFlyModule`, `AirStuckModule`
- **Render:** `ShaderEspPass`, `HoleEspModule`, `TrajectoriesModule`, `HeadCosmeticModule`, `CharacterModule`
- **Utility & Misc:** `PlayerDetectionModule`, `CoordSnapperModule`, `FreecamModule`, `SkinChangeModule`, `SpawnerAlerts`
- **Automation:** `SchematicBuilderModule`, `AreaMinerModule`, `OrderAhBot`, `SellRoutine`, `Eater`, `Breaker`
- **Configuration & Core:** `BooleanSetting`, `NumberSetting`, `ModeSetting`, `StringSetting`, `KeySetting`, `ModuleRegistry`

---

## anti-tamper & custom builds

The original client contains an integrity verification routine in `net.anubis.client.security.OfficialBuild`:

- In release environments, it validates every JAR entry against the SHA-256 certificate parts defined in `BuildConfig.SIGN_CERT_PARTS`.
- If modified without resigning with the official key, `TamperStop` terminates launch and displays an unofficial copy warning.
- **To compile and run your own modified version:**
  - Clear the array in `net.anubis.client.BuildConfig.SIGN_CERT_PARTS` (set to `new String[]{}`), or
  - Bypass `OfficialBuild.checkBeforeLaunch()` / set `state = State.NOT_CHECKED`.

---

## recovery notes

- **1,284 total JVM class entries** were analyzed.
- **741 clean Java source files** were reconstructed.
- The bytecode contained **no commercial obfuscation markers** (ZKM, Allatori, Stringer, ProGuard names), preserving original class and method nomenclature.
- All non-class assets (3D character models `cj.anc`, `neighbor.anc`, GLSL `.vsh`/`.fsh` shaders, TTF fonts, and audio effects) were extracted from the archive.
- *This is decompiled source, not a clean-room rewrite. Compiler-generated synthetic artifacts and some control-flow structures reflect decompiler output.*

---

## legal

Anubis Client is an independent community project and is not an official Minecraft product. Minecraft is a trademark of Mojang Studios and Microsoft Corporation. Third-party libraries (Dear ImGui, Fabric API, Gson) retain their respective licenses.
