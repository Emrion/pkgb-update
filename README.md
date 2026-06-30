# pkgb-update

pkgb-update is the counterpart of freebsd-update, but for pkgbase systems.  
It's a script that updates (that's simple) and mostly upgrades the FreeBSD OS.  

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

New: in addition, it allows to downgrade from any version to any.
  
# Usage

Usage: pkgb-update command [FreeBSD-version]  

command can be one of:  
* update: apply security patches.  
* upgrade FreeBSD-version: try to upgrade to 'FreeBSD-version'.
* downgrade FreeBSD-version: as above, but more sporty.
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
- [upgrade/downgrade] Check if the REPOS_DIR var of pkg is modified. If yes, refuses to run.
- [upgrade/downgrade] Check if the target version is compatible with upgrade or downgrade.
- [upgrade/downgrade] Check if there is some .pkgnew files and if it's the case, leave a chance to stop the process.
- [upgrade/downgrade] Force to upgrade pkg.
- [upgrade/downgrade] Once the base is upgraded/downgraded, in case it's a STABLE or CURRENT upgrade and if latest RELEASE repos were enabled, disable them.
- [upgrade/downgrade] Force the upgrade of third-party kmods.
- [upgrade/downgrade] If this is a major verson upgrade, force the upgrade of all packages.
- [upgrade/downgrade] Check again if there are some .pkgnew files and possibly warn.

# To do

- Add ALPHAx and BETAx targets.

# Some informations

* There is a discrepancy between official STABLE and CURRENT names and the targets pkgb-update uses.  
  For example, a valid target is 15-STABLE, not 15.1-STABLE.  
  This is because there is no point to put a minor version to STABLE (same for CURRENT).  
  I don't know why FreeBSD does that in its versions name.
* pkg needs some keys to check what it downloads. You may think this is a problem only for major upgrades.  
  It's not. It blocks also for an upgrade to a STABLE version. Apparently, RELEASE doesn't have the right key.  
  So, I systematically use 'signature_type: "none"' (just during the base upgrade).  
* pkg has no way to know that STABLE packages are upgrades for a RELEASE or RCx system.  
  It actually considers that it is a downgrade, hence I use the force flag (-f) in this case for pkg command line.  
  Reference: https://github.com/freebsd/pkg/issues/2703
* There is another pitfall concerning STABLE or CURRENT version upgrade.  
  You may have chosen the latest repos instead of the default ones (quarterly). I mean for a RELEASE version.  
  In this case, you have set a repo .conf file in /usr/local/etc/pkg/repos/ with modified URLs.  
  When you upgrade to STABLE or CURRENT version, these URLs take precedence on the new legitimate ones.  
  pkgb-update checks this and comment out these modified URLs if they exist.   
