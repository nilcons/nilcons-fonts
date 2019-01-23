First: git clone this to `/usr/share/nilcons-fonts`.

Then put this into your X session init file (or bashrc, as you wish):

    [ -d /usr/share/nilcons-fonts ] && {
      # Use only nilcons-fonts for fontconfig
      export FONTCONFIG_FILE=ncfonts.conf
      export FONTCONFIG_PATH=/usr/share/nilcons-fonts

      # Do not use traditional X fonts, only the emergency built-ins
      xset fp= built-ins
    }

(We have to use `/usr/share/<something>`, because there are a lot of
VERY-VERY hard to debug sandboxing measures in GNU/Linux nowadays. E.g.
AppArmor is kind of easy, but Firefox sandbox is a nightmare.  Random
complicated apps will not work if you try somewhere else.  See:
https://github.com/mozilla/gecko-dev/blob/master/security/sandbox/linux/broker/SandboxBrokerPolicyFactory.cpp)

Fontconfig 2.12 (nix @2019-01-21):
  - understands `FONTCONFIG_{FILE,PATH}`
  - with the above config only reads `/usr/share/nilcons-fonts/*`
  - doesn't understand `FONTCONFIG_SYSDIR`
  - works if proper configuration given at `/usr/share/nilcons-fonts/ncfonts.conf`

Fontconfig 2.13 (debian @2019-01-21):
  - would understood `FONTCONFIG_SYSDIR`
  - reads `/usr/share/fontconfig` no matter what (even with `FONTCONFIG_SYSDIR`)
  - fortunately `/usr/share/fontconfig` is only parsed, not loaded,
    and merge request is already submitted to fix this
    (https://gitlab.freedesktop.org/fontconfig/fontconfig/merge_requests/26)
  - otherwise only reads `/usr/share/nilcons-fonts/*`
  - works if proper configuration supplied there

Use cases for this knowledge:
  - run your system with only the 3 liberation fonts + Iosevka, should be enough for everything
  - no Debian/Ubuntu/Arch font stupidity to deal with
  - compatibility with 2.12 AND 2.13 fontconfig libraries and static binaries at the same time
  - test if your website uses any fonts which are not provided by you
    (this needs further research, we need a config, where only a font
    with all boxes are provided, then chrome will have some UI and
    user can test)
  - test your application's font dependence
  - package a GNU/Linux application together with its fonts

TODO:
  - add a directory where user can drop fonts
  - add a directory where user can drop fontconfigs
  - these dirs should be gitignored
  - test these!

Useful commands:
  - `fc-list`
  - `fc-list : file`
  - `fc-cache -fv`
  - `fc-conflist` (you can ignore lines that start with '-')
  - `FC_DEBUG=4 fc-match 'mono,sans,serif'`
  - `pango-list`
  - `pango-view --font '' --text 'foobar quux'`
