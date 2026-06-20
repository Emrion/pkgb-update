# pkgb-update

pkgb-update is the counterpart of freebsd-update, but for pkgbase systems.  
It's a script that updates (well, that's just a simple command line) and mostly upgrade the FreeBSD OS.  

pkgbase opens some upgrades (and updates) that were not supported otherwise than compiling the code source.  
pkgbase dramatically speed up the upgrade comparing to freebsd-update.  
Nevertheless, pkgbase has its own drawbacks. One of them is the complexity of the commands to type to upgrade.  
This is where a tool like pkgb-update has a role to play.  

pkgb-update is able to upgrade from RELEASE to RELEASE, but also from:
- RELEASE to RC
- RC to next RC
- RELEASE or RC to STABLE
- Any to CURRENT

It lacks ALPHA and BETA versions, this will come later.

# Usage

Usage: pkgb-update command [FreeBSD-version]  

command can be one of:  
* update: apply security patches.  
* upgrade FreeBSD-version: try to upgrade to 'FreeBSD-version'.  
* diag : display the different repositories used by pkg.  

FreeBSD-version can be:  
* XX.x-RELEASE  
* XX.x-RCx  
* XX-STABLE  
* XX-CURRENT (only the last major version of FreeBSD)  

# Done

- Check if the host version at least 15.0. If it's not the case, refuses to run.
- Check if a repo using pkgbase is active or die.
- Check if kmods and ports repos are enabled or die.
- [upgrade] Check if the REPOS_DIR var of pkg is modified. If yes, refuses to run.
- [upgrade] Check if the target version leads to a downgrade.
- [upgrade] Force to upgrade pkg.
- [upgrade] Once the base is upgraded, in case it's a STABLE or CURRENT upgrade and if latest RELEASE repos were enabled, disable them.
- [upgrade] Force the upgrade of third-party kmods.
- [upgrade] If this is a major verson upgrade, force the upgrade of all packages.

# To do

- Check what happen if I really downgrade a system.
- Deal with .pkgnew.
- Add ALPHAx and BETAx targets.

# Some informations

* pkg needs some keys to check what it downloads. You may think this is a problem only for major upgrades.  
  It's not. It blocks also for an uograde to a STABLE version. Apparently, RELEASE doesn't have the right key.  
  So, I systematically use 'signature_type: "none"' (just during the base upgrade).  
* pkg has no way to know that STABLE packages are upgrades for a RELEASE or RCx system.  
It actually considers that it is a downgrade, hence I use the force flag (-f) in this case for pkg command line.  
Reference: https://github.com/freebsd/pkg/issues/2703
