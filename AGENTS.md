# AGENTS.md

Customisations for our private v83 server, built on [Cosmic](https://github.com/P0nk/Cosmic). The live server runs on the host's (Will's) Raspberry Pi; player setup and host notes are in `../Private_Maple`, and `../AGENTS.md` describes the whole workspace.

## Remotes and branches
- `origin` is TommyTealu/Cosmic, a personal staging fork. `upstream` is P0nk/Cosmic.
- Work on `maple-custom` (or a feature branch off it). Keep `master` equal to upstream.
- Pull upstream fixes with `git fetch upstream && git merge upstream/master` into `maple-custom`.
- Will plans a shared repo that the Pi pulls from. When it exists, add it as a remote and open PRs there.

## Local test server
- `docker compose up -d --build` starts the server and a MySQL 8.4 DB. Needs Docker Desktop running. Rebuild after Java changes; scripts, `wz/` and `config.yaml` are mounted, so those only need `docker compose restart maplestory`.
- `docker-compose.override.yml` is local-only (in `.git/info/exclude`) and binds every port to 127.0.0.1, because the DB root password is empty. Don't commit it or remove it.
- The DB schema is created and migrated by Liquibase on startup (`src/main/resources/db/`). Schema changes go in as new changesets there, never as hand-run SQL.
- Make a local GM: `docker compose exec db mysql cosmic -e "update characters set gm = 6 where name = 'Name';"`, then relog.
- Logs: `docker compose logs -f maplestory`.
- Client: `C:\Nexon\MapleStory\Maple-Local.exe` (desktop shortcut "Maple Local Test"), installed by `../Private_Maple/setup-local-client.ps1`. It only connects to 127.0.0.1.

## Making changes
- Prefer NPC scripts (`scripts/npc/<npcId>.js`) over Java. Reuse an existing NPC id and sprite; place it permanently in game with `!pnpc <npcId>` (GM level 4+).
- Player commands live in `src/main/java/client/command/commands/gm0/` and are registered in `CommandsExecutor`.
- Don't change rates; the host is happy with them.
- `config.yaml` here is the local copy. The Pi's copy holds the DB password and isn't in any repo, so put proposed config changes in the PR description for the host to apply.
- The Pi runs MariaDB 11.8, not MySQL, so keep SQL and changesets portable.
- Test every change on the local server before proposing it.

## Don't
- Don't SSH to the Pi or touch the live server; the host deploys.
- Don't commit client files (`*.exe`, client `.wz`) or anything with a password.
