# Plugins

Active plugins in this Caldera 5.3.0 instance (both caldera-1 and caldera-2):

* [sandcat](sandcat.md) - Default Go-based cross-platform RAT/implant
* [stockpile](stockpile.md) - Core ability library and scenario adversaries
* [atomic](atomic.md) - Red Canary Atomic Red Team abilities
* [emu](emu.md) - CTID adversary emulation profiles (APT29, OilRig, Sandworm, etc.)
* [manx](manx.md) - Shell-access terminal agent
* [compass](compass.md) - ATT&CK Navigator visualization
* [debrief](debrief.md) - Post-operation reporting and timelines
* [response](response.md) - Automated incident response
* [access](access.md) - Initial access modules
* [training](training.md) - Caldera SME certification course
* [fieldmanual](fieldmanual.md) - In-app documentation
* [human](human.md) - Simulated user activity (Chrome/OS) to disguise red-team traffic
* [gameboard](gameboard.md) - Red vs. blue exercise monitoring dashboard

**Inactive:** `ssl`

## Installing Additional Plugins

See [Plugin Installation](plugin_installation.md) for the correct procedure — the Docker setup has non-obvious config persistence behavior that requires a specific steps to make changes survive restarts.
