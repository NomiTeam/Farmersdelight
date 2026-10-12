# Drink food defaults

Verified on 2026-10-13 against the existing 1.4 preview baseline, [75b85f39](https://github.com/vectorwing/FarmersDelight/tree/75b85f39e219bd2b9b7c4d2cd08fc03a61091b73). This is a development source snapshot, not a claim that 1.4 has been released.

The shipped CE item definitions now override the shared zero-nutrition drink template for hot cocoa (3 nutrition / 6 saturation), apple cider (1 / 4) and melon juice (1 / 4). Apple cider Absorption lasts 3600 ticks; its lore displays 03:00. The change adds no Java runtime logic or new API dependency. Hot cocoa's existing multiple-effect removal remains a gameplay difference from upstream.

- Default clean build: 178 suites, 935 JUnit tests, zero failures, errors or skipped tests.
- Debug clean build: 181 suites, 947 JUnit tests, zero failures, errors or skipped tests. The final clean build restored the default release flags and artifacts.
- CraftEngineFoodConfigurationTest reads shipped YAML and pins the three absolute food values plus cider's effect type and duration.
- tools/run_all_checks.py --quiet: all 10 steps passed.
- Current workspace 14-artifact CE symbol checks passed for 26.8.2, 26.9, 26.9.1, 26.9.2 and all three local 26.10 snapshots: 202 classes / 631 members, no missing symbols or abstract gaps. Only FD and BAC were rebuilt in this scope.

The updated defaults have not been copied to the test server. Existing CE files and local recipe/item overrides are not overwritten by a new jar. Food components, duration and lore must be merged before CE reload; these checks do not verify client consumption, Folia load, all effect semantics or every ingredient tag.
