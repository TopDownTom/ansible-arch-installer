## package_install

Install all packages needed for a functional system. If yay isn't already installed, make it then install it with pacman, then install the rest of the packages. At the time of writing, this equates to 888 packages $(pacman -Q | wc -l).

### Variables

- build_user
- parallel_downloads
