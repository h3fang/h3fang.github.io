---
layout: post
title: Debloating texlive-fontsextra
---

The [texlive-fontsextra](https://www.archlinux.org/packages/extra/any/texlive-fontsextra/) package is so bloated. It leaves a 1.1 GB package on your file system and pollutes your font cache with thousands of useless fonts.

I want to modify the official [PKGBUILD](https://git.archlinux.org/svntogit/packages.git/tree/trunk/PKGBUILD?h=packages/texlive-fontsextra) file to only package fonts that I need. These include

* Fira/Fira Math (Required by beamer Metropolis template)
* FontAwesome4/5 (Required by some resume templates)
* SourceCodePro/Newpx/Newtx/Newtxsf/Newtxtt/Boondox (Required by SJTUThesis template)

```bash
source=("https://ctan.org/tex-archive/systems/texlive/tlnet/archive/boondox.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/fira.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/firamath.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/firamath-otf.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/fontawesome.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/fontawesome5.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/newpx.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/newtx.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/newtxsf.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/newtxtt.tar.xz"
        "https://ctan.org/tex-archive/systems/texlive/tlnet/archive/sourcecodepro.tar.xz"
        "$pkgname.maps::https://git.archlinux.org/svntogit/packages.git/plain/trunk/texlive-fontsextra.maps?h=packages/texlive-fontsextra")
```

Then I have to generate the `${pkgname}_${_revnr}.pkgs` file and the `$pkgname.maps` file accordingly.

The `${pkgname}_${_revnr}.pkgs` file contains font package name and the revision number. The revision number can be get from `tlpkg/tlpobj/*.tlpobj` file.

The `$pkgname.maps` contains somethings I don't understand. So I just copied corresponding lines from the original `$pkgname.maps` file.

```bash
build() {
   cd "$srcdir"
   touch CONTENTS
   touch MAPS
   for p in tlpkg/tlpobj/*.tlpobj; do
       pstem=${p#tlpkg/tlpobj/}
       pstem=${pstem%.tlpobj}
       echo $pstem $(grep revision $p | awk '{print $2}') >> CONTENTS
       echo $(grep -i $pstem.map $pkgname.maps) >> MAPS
   done
   sed -i '/^$/d' MAPS
   rm -rf {tlpkg,doc,source} || true
}

package() {
   cd "$srcdir"
   install -m755 -d $pkgdir/var/lib/texmf/arch/installedpkgs
   install -m644 CONTENTS $pkgdir/var/lib/texmf/arch/installedpkgs/${pkgname}_${_revnr}.pkgs
   install -m644 MAPS $pkgdir/var/lib/texmf/arch/installedpkgs/$pkgname.maps
   # ...
}
```

If the official package got an update, it ill be installed and overwrite our changes. To make maintenance easier, we can add it to the `modified` group

```bash
groups=('texlive-most' 'modified')
```

and ignore the `modified` group in `/etc/pacman.conf` file.

```ini
# ...
IgnoreGroup = modified
# ...
```

This way the official update to this package will be ignored, but we can still get a warning, so we can update our modified package accordingly.

The full PKGBUILD can be found [here](https://github.com/h3fang/AUR/blob/master/texlive-fontsextra-h3f/PKGBUILD).