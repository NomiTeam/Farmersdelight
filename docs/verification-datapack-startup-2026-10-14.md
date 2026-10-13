# Startup datapack loading

Verified on 2026-10-14. Registry data uses Paper DATAPACK_DISCOVERY before world loading; runtime changes require a full restart instead of an automatic reloadData call. Managed JSON is generated from plugin configuration and addon caches. Legacy packs are preserved in datapack-backups, including dimension-directory copies. Damage definitions now use damage-types.yml; existing YAML takes priority over one-time JSON import.

Paper marks the discovery API experimental. The nine used contracts were checked in ten official source versions from 1.21.5 through 26.2 (90 checks); this does not guarantee future releases. Pack formats were read from eleven official Mojang version.json files. The 1.21.7/8 value is corrected to 81.

- Default clean build: 182 suites / 955 tests. Debug clean build: 185 / 967. Zero failures, errors and skipped tests; default build flags and release artifacts restored.
- New bootstrap tests cover strict configuration errors, initial definitions, cached tag references, legacy migration, disabled packs, safe paths and renamed backstabbing IDs. DamageTypePackTest covers YAML generation and one-time preservation. StoveDamageDatapackTest checks the generated burning/no-knockback declaration.
- Paper 26.2-121 with CE snapshot beef44fd: 9 registry/layer checks and 19 native bread overflow checks passed. Required/force-enabled plugin packs initially reproduced a Paper Adventure title conversion failure before registry initialization; default discovery avoids that path and preserves manual disablement.
- Folia 26.2-7 restart with the same world: 156 checks passed, covering five managed packs, old FD aliases absent, three YAML sources and actual item/damage tag membership. No vanilla datapack command is available on Folia; the probe used public registry and datapack APIs.
- Ten repository check steps passed. Six current artifacts passed all seven CE symbol groups (26.8.2 through three 26.10 snapshots): 206 classes / 638 members, no missing symbols or abstract gaps.
- The test server's five original configuration hashes were unchanged. Final jars were installed while stopped and hashes verified; the server remains stopped. The temporary probe was removed from the loading path. Existing non-Folia third-party plugins were retained.

Raw evidence is under the workspace .codex/tmp/datapack-startup-20261014. Server jar/world-pack backups are under the existing test directory's codex-backups/datapack-startup-20261014. Per-patch real servers, Folia natural villager schedules, client behavior and sustained load were not tested. Addon tag ownership and upstream structure generation were reviewed as advice only.
