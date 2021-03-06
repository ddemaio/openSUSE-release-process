# RC

#### update desktop file translations

* in weblate commit all pending changes

In desktop-file-translations:

* check 51-xml/download_data.sh
* export password
* run make
* verify and check

#### clean up build fails

be more agressive with build fails
- harass maintainers
- wipebinaries of failed packages after checking whatdependson
- wipebinaries of unresolvables
- file delete requests

#### set up redirects for repository structure on download.o.o

The paths referenced in this view:
https://api.opensuse.org/public/source/openSUSE:Leap:15.1/000product?view=productrepositories&product=openSUSE

should be reflected on http://download.opensuse.org/repositories/

Should be just a matter of installing some redirects.

#### eliminate duplicate binaries

packages with duplicate binaries need to be cleaned up as OBS has undefined behavior with those

https://build.opensuse.org/package/view_file/openSUSE:Leap:15.1:Staging/dashboard/duplicate_binaries?expand=1

#### compile a list of packages that are from sle but not maintained there

some packages are marked as coming from SLE even though not all or none of it's source packages are actually maintained there. Need to compute a list and discuss with maintenance.

#### update branding
Responsible: hellcp

the new release needs a new branding

* <del>yast color scheme</del>
* <del>yast headline image</del>
* wallpapers
  * KDE
  * GNOME
  * icewm
* plymouth boot splash screen
* grub2 theme
* syslinux theme
* displaymanager themes
  * sddm
  * gdm
  * lightdm

#### make sure new maintenance packages end up in the release

maintenance probably released several packages that are new in the previous release. We need to make sure to collect them for the new release

#### release manager checklist

To branch the current ftp tree and isos for a milestone, log in to download.opensuse.org as mirror user and run:

    distro_hardlink_copy 42.2-Current 42.2-RC1

Remove x bit for others from the directory so only staging mirrors can pull it but it's not visible on download.o.o

on release day every once in a while scan for mirrors as mirror user:

    mb scan -a -d /distribution/leap/42.2-RC1 -j4

In the morning add the x bit again to make the content show up and give mirrors time to catch up adding the x bit.

At release time:

- change the symlink to the new milestone

  distro_symlink 42.2 42.2-RC1

- sync the staging are to the area shown in download.opensuse.org

    publish_opensuse

You may want to add '-n' before to check what it's doing and not deleting files

#### enable OBS:Maintained attribute in :Update project

The :Update project needs to have

    <attributes>
    <attribute name="Maintained" namespace="OBS"/>
    <attribute name="BranchTarget" namespace="OBS"/>
    <attribute name="ApprovedRequestSource" namespace="OBS"/>
    </attributes>

=>

    $ osc meta attribute -c -a OBS:Maintained openSUSE:Leap:15.1:Update

And set update project:

    $ osc meta attribute -c -a OBS:UpdateProject -s openSUSE:Leap:15.1:Update openSUSE:Leap:15.1

#### remove beta marks

* remove %_with_betatest from prjconf
* remove beta tag from openSUSE.product
* remove BETA=1 from openQA

#### verify release counters for update

release number needs to be higher than in previous distributions. Make sure the are. mls knows how to.

#### openSUSE-EULAs up to date

devel:openSUSE:Factory/openSUSE-EULAs needs to be up to date to include all relevant licenses. Ie licenses that require explicit agreement via dialog by the user. That affects mostly packages in the openSUSE:Leap:15.0:NonFree project.

The sources are at github: https://github.com/openSUSE/openSUSE-EULAs

#### submit translation packages

Packages that contain translations may need to be updated and submitted

- package-translations : system:install:head
- desktop-translations : X11:common:Factory
- yast2-trans : YaST:Head                    (submit directly to YaST:Head, it does not accept SRs)
- skelcd-openSUSE : system:install:head
- openSUSE-EULAs : devel:openSUSE:Factory

    
    osc rm *.tar.?z
    osc service dr
    osc addremove
    osc vc         # beautify *.changes: 'M-x sort-lines' and 'M-x delete-duplicate-lines'
    osc ci
    osc sr


#### Security Audit

Security needs to take a look at the current snapshot

#### verify piwik works



#### submit translation packages

Packages that contain translations may need to be updated and submitted

- package-translations : system:install:head
- desktop-translations : X11:common:Factory
- yast2-trans : YaST:Head                    (submit directly to YaST:Head, it does not accept SRs)
- skelcd-openSUSE : system:install:head
- openSUSE-EULAs : devel:openSUSE:Factory

    
    osc rm *.tar.gz
    osc service dr
    osc addremove
    osc vc         # beautify *.changes: 'M-x sort-lines' and 'M-x delete-duplicate-lines'
    osc ci
    osc sr

#### make sure nvidia packages are ready for GA

NVidia drivers are nowadays usually ready way before GA. Ping the driver maintainer to make sure that is the case actually.

#### merge back maintenance updates

maintenance updates from the update project need to be merged back into the main repo

* use osc checkupdate (https://github.com/coolo/factory-auto/osc-plugin-checkupdate.py) to find updated packages
* copy them back using 'osc copypac -e openSUSE:13.2:Update openSUSE:13.2 -m "Update to match Update channel update#."'; On purpose do it by hand without automation to avoid screwups
* adjust the release number to be higher than in the update repo using FIXMEπ
* remove updates from the update repo by using "osc killupdate NUMBER"


[1] adjust the release number by checking the state of the package 'osc getbinaries openSUSE:13.2:Update . standard x86_64' and then writting higher revision to .spec file. To submit this update to repository just use 'osc ci --noservice -m "Add revision to ensure new-enough over Update channel."'
There should be a way to do this by means of obs


#### make counter.o.o/link point to new portal

https://counter.opensuse.org/link/ should point to https://en.opensuse.org/Portal:15.1?pk_campaign=counter


#### prepare software.opensuse.org

prepare software.opensuse.org for RC1

* https://github.com/openSUSE/software-o-o, app/controllers/main_controller.rb
* Wait for package to build https://build.opensuse.org/package/show/openSUSE:infrastructure:software.opensuse.org/software_opensuse_org
* Deploy when mirrors are ready

#### EULA review/update

Ask Legal to review and potentially adjust the EULA.

The request form can be found in innerweb

Current license is at https://github.com/openSUSE/skelcd-openSUSE/blob/master/license/license.txt

The year can be adjusted by means of sed before sending to Legal.

Wiki: https://en.opensuse.org/openSUSE:License

#### announce package freeze

send anounncement internally and externally to warn about the package freeze

#### enable countdown on www.opensuse.org

* Update the Leap release date in https://github.com/openSUSE/landing-page/blob/master/assets/js/countdown.js#L4
* Update the assets in a way described in the [readme](https://github.com/openSUSE/landing-page/#2-last-steps-build-and-test)

#### update OpenSUSE_Localization_Teams
Responsible: AdaLovelace

Ping everyone listed in https://en.opensuse.org/OpenSUSE_Localization_Teams to make sure translation teams are aware of the release and are available

Fix the links to the statistics.

#### update openSUSE:Localization_guide
Responsible: AdaLovelace

Update https://en.opensuse.org/openSUSE:Localization_guide to document the Weblate workflow

When done, link it from https://en.opensuse.org/Portal:15.1

#### disable Factory submitter

disable the FCC submitter

