# RClient

A launcher and client for **Minecraft: Java Edition** with **cosmetics** (capes today, more soon) that every RClient
player can see on each other, a modern launcher with **instances**, and **Modrinth mod management**.

> **Status: early development (pre-release).** Target version: Minecraft Java **26.3**. Not affiliated with, endorsed
> by, or associated with Mojang Studios, Microsoft or Modrinth. *Minecraft* is a trademark of Mojang Studios.

## Download

The Windows installer (`setup.exe`) will be published under **[Releases](../../releases)** once the first public
version is ready. Requirements: Windows 10/11 (64-bit) and a Microsoft account that owns **Minecraft: Java Edition**.

The source code is not published.

## What it does

- **Launcher:** downloads Minecraft **directly from Mojang**, verifies every file by hash, and starts it with the RClient client.
- **Instances:** separate setups with their own mods, worlds and options.
- **Mods from Modrinth:** search, install (with required dependencies), enable/disable, remove and update.
  Downloads come only from `cdn.modrinth.com` over HTTPS and are verified (SHA-1 and SHA-512).
- **Cosmetics:** the client shows the cape a player was given. Changes appear live for everyone who can see that player.
- **Microsoft sign-in:** the standard Microsoft → Xbox Live → XSTS → Minecraft services chain, including a game-ownership check.

## What it does **not** do

- **No gameplay advantages.** RClient adds cosmetics and launcher features only. No X-ray, reach, auto-clickers, or similar.
- **No Mojang code or assets are redistributed.** Game files are downloaded from Mojang's servers at install time.
- **No bypassing of authentication or ownership checks.** Released builds require a verified Microsoft account that owns Minecraft: Java Edition.
- **No user-uploaded code.** The client only downloads data (PNG textures), never code, and verifies it by SHA-256.

## How sign-in works

1. The launcher opens Microsoft's own sign-in page in your browser (OAuth authorization code flow with **PKCE**, redirect to
   `http://localhost` on a random port, `state` checked; a device-code fallback exists). **Your password is entered on Microsoft's page only.**
2. The launcher exchanges the result for Xbox Live, XSTS and Minecraft services tokens and checks that the account owns the game.
3. Tokens stay **on your PC**. The refresh token is encrypted with the Windows user's key (DPAPI); the short-lived Minecraft token is kept in memory only.
4. **The RClient server never receives your Minecraft or Xbox tokens.** It receives only Microsoft's signed OpenID *ID token*
   (issued for RClient's own app), which proves who you are, so it can assign a role and look up your cosmetics. The server verifies
   Microsoft's signature, issuer, audience and freshness.

Scopes requested: `XboxLive.signin offline_access openid profile`.

## Security overview

- Every downloaded file is verified by hash; downloads are HTTPS-only and limited to known hosts (Mojang, Fabric, Modrinth, the RClient server).
- Roles are decided by the server and checked on **every** request.
- The server rate-limits sign-ins, limits request sizes, validates all input and uses parameterised queries only.
- Secrets are never stored in the application or in this repository.

## Contact

Questions, bug reports and security concerns: please open an [issue](../../issues).
