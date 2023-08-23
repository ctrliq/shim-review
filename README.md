This repo is for review of requests for signing shim.  To create a request for review:

- clone this repo
- edit the template below
- add the shim.efi to be signed
- add build logs
- add any additional binaries/certificates/SHA256 hashes that may be needed
- commit all of that
- tag it with a tag of the form "myorg-shim-arch-YYYYMMDD"
- push that to github
- file an issue at https://github.com/rhboot/shim-review/issues with a link to your tag
- approval is ready when the "accepted" label is added to your issue

Note that we really only have experience with using GRUB2 on Linux, so asking
us to endorse anything else for signing is going to require some convincing on
your part.

Here's the template:

*******************************************************************************
### What organization or people are asking to have this signed?
*******************************************************************************
CIQ Inc.  ( https://ciq.com )

*******************************************************************************
### What product or service is this for?
*******************************************************************************
CIQ provides enhancements to, and customizations around Rocky Linux for our customers.  We are especially interested in customized/improved Linux kernel builds, along with packaging and improving the out-of-tree driver experience.

*******************************************************************************
### What's the justification that this really does need to be signed for the whole world to be able to boot it?
*******************************************************************************
Our customers use a variety of hardware platforms.  Many of them have policies in place, or are contractually obligated in some way to use the default EFI firmware with no customized secureboot/MOK key injection.  At the same time, many customers require some modification from the stock Rocky / RHEL kernel, mostly around the area of security backports (supporting older minor versions), or customized options for their workload.

*******************************************************************************
### Why are you unable to reuse shim from another distro that is already signed?
*******************************************************************************
We need these customized kernels to boot properly on stock hardware.  This is not possible with the default Rocky Linux (or RHEL) shim binary.

*******************************************************************************
### Who is the primary contact for security updates, etc.?
The security contacts need to be verified before the shim can be accepted. For subsequent requests, contact verification is only necessary if the security contacts or their PGP keys have changed since the last successful verification.

An authorized reviewer will initiate contact verification by sending each security contact a PGP-encrypted email containing random words.
You will be asked to post the contents of these mails in your `shim-review` issue to prove ownership of the email addresses and PGP keys.
*******************************************************************************
- Name: Skip Grube
- Position: Engineer
- Email address: sgrube@ciq.com
- PGP key fingerprint: F58E D7A0 91B6 E50D E7CA EB07 D391 F839 3BEA 6D9C
- PGP key available on keyserver.ubuntu.com and keys.openpgp.org
- https://keys.openpgp.org/vks/v1/by-fingerprint/F58ED7A091B6E50DE7CAEB07D391F8393BEA6D9C

(Key should be signed by the other security contacts, pushed to a keyserver
like keyserver.ubuntu.com, and preferably have signatures that are reasonably
well known in the Linux community.)

*******************************************************************************
### Who is the secondary contact for security updates, etc.?
*******************************************************************************
- Name: Michael Young
- Position: Information Technology Director
- Email address: myoung@ciq.com
- PGP key fingerprint:  CD82 9808 7BCA C022 B5EC  84FA D84A 6A59 1392 6D2B
- http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xcd8298087bcac022b5ec84fad84a6a5913926d2b

(Key should be signed by the other security contacts, pushed to a keyserver
like keyserver.ubuntu.com, and preferably have signatures that are reasonably
well known in the Linux community.)

*******************************************************************************
### Were these binaries created from the 15.7 shim release tar?
Please create your shim binaries starting with the 15.7 shim release tar file: https://github.com/rhboot/shim/releases/download/15.7/shim-15.7.tar.bz2

This matches https://github.com/rhboot/shim/releases/tag/15.7 (plus the NX patch) and contains the appropriate gnu-efi source.

******************************************************************************
Yes.  We are adding the relevant enable-NX-by-default patch to this code:  https://github.com/rhboot/shim/pull/530

Same patch in our RPM source:  https://bitbucket.org/ciqinc/shim-unsigned-x64/src/ciq8/SOURCES/0001-Enable-the-NX-compatibility-flag-by-default.patch




*******************************************************************************
### URL for a repo that contains the exact code which was built to get this binary:
*******************************************************************************
CIQ shim-unsigned-x64 RPM repository:  https://bitbucket.org/ciqinc/shim-unsigned-x64/src/ciq8/

This code is a combination of:  https://github.com/rhboot/shim/releases/download/15.7/shim-15.7.tar.bz2 , NX patch https://github.com/rhboot/shim/pull/530 , and an RPM spec file derived from the Rocky (and in turn RHEL) one.

Additionally, I have a "frozen" repository copy of the Mock buildroot and build dependencies (gcc, openssl, et al.) here:  https://rl-secure-boot.ewr1.vultrobjects.com/repos/shim_review_deps/  (this gets used by Mock as a source of RPM dependencies)

Using this repository (consisting of public Rocky Linux 8 packages) ensures a reproducible binary when building the shim-unsigned-x64 with mock (or Docker/Podman) and rpmbuild.



*******************************************************************************
### What patches are being applied and why:
*******************************************************************************
We are including the NX-compatibility-by default patch, in addition to the stock 15.7 tag:

https://github.com/rhboot/shim/pull/530


This is to align with updated Microsoft requirements, ( https://techcommunity.microsoft.com/t5/hardware-dev-center/updated-uefi-signing-requirements/ba-p/1062916 )



*******************************************************************************
### If shim is loading GRUB2 bootloader what exact implementation of Secureboot in GRUB2 do you have? (Either Upstream GRUB2 shim_lock verifier or Downstream RHEL/Fedora/Debian/Canonical-like implementation)
*******************************************************************************
We intend to use the Rocky 8 + 9 (based on RHEL 8 + 9) GRUB2 source code unmodified, as our projects have no need for bootloader modifications.  The Rocky/RHEL Grub versions (and their patches) are what we are using.


*******************************************************************************
### If shim is loading GRUB2 bootloader and your previously released shim booted a version of grub affected by any of the CVEs in the July 2020 grub2 CVE list, the March 2021 grub2 CVE list, the June 7th 2022 grub2 CVE list, or the November 15th 2022 list, have fixes for all these CVEs been applied?

* CVE-2020-14372
* CVE-2020-25632
* CVE-2020-25647
* CVE-2020-27749
* CVE-2020-27779
* CVE-2021-20225
* CVE-2021-20233

* CVE-2020-10713
* CVE-2020-14308
* CVE-2020-14309
* CVE-2020-14310
* CVE-2020-14311
* CVE-2020-15705
* CVE-2021-3418 (if you are shipping the shim_lock module)

* CVE-2021-3695
* CVE-2021-3696
* CVE-2021-3697
* CVE-2022-28733
* CVE-2022-28734
* CVE-2022-28735
* CVE-2022-28736
* CVE-2022-28737

* CVE-2022-2601
* CVE-2022-3775
*******************************************************************************
We are a new vendor, and this is our first submission.  But I can confirm that our grub2 builds will not be affected by any of those, as they've all been fixed in our upstream:

https://git.rockylinux.org/staging/rpms/grub2/-/blob/r9/SPECS/grub2.spec#L536

https://git.rockylinux.org/staging/rpms/grub2/-/blob/r8/SPECS/grub2.spec#L511


*******************************************************************************
### If these fixes have been applied, have you set the global SBAT generation on your GRUB binary to 3?
*******************************************************************************
We have not, as we are a new vendor.  Our release should exactly match the Rocky and RHEL equivalents.

*******************************************************************************
### Were old shims hashes provided to Microsoft for verification and to be added to future DBX updates?
### Does your new chain of trust disallow booting old GRUB2 builds affected by the CVEs?
*******************************************************************************
This is our first submission, we have no older shims.

*******************************************************************************
### If your boot chain of trust includes a Linux kernel:
### Is upstream commit [1957a85b0032a81e6482ca4aab883643b8dae06e "efi: Restrict efivar_ssdt_load when the kernel is locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1957a85b0032a81e6482ca4aab883643b8dae06e) applied?
### Is upstream commit [75b0cea7bf307f362057cc778efe89af4c615354 "ACPI: configfs: Disallow loading ACPI tables when locked down"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75b0cea7bf307f362057cc778efe89af4c615354) applied?
### Is upstream commit [eadb2f47a3ced5c64b23b90fd2a3463f63726066 "lockdown: also lock down previous kgdb use"](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eadb2f47a3ced5c64b23b90fd2a3463f63726066) applied?
*******************************************************************************
Yes, all of these patches are already in the Rocky/RHEL 8 + 9 kernels we plan to base on.


*******************************************************************************
### Do you build your signed kernel with additional local patches? What do they do?
*******************************************************************************
Generally we'll be performing 2 sorts of mofifications:

- Fixes and enhancements (especially security updates) to continue long-term support of a previous Rocky Linux release.  For example, further backports to the Rocky/RHEL 8.6 kernel (kernel-4.18.0-372) to keep it updated for customers, or FIPS enhancements/restrictions for those that require compliance.

- Builds of recent mainline (ML) and longterm (LT) upstream kernel releases designed for installation on Rocky Linux.  Different variants are planned with compile-time configuration tweaks, especially around enhancing high performance computing (HPC) applications.



*******************************************************************************
### If you use vendor_db functionality of providing multiple certificates and/or hashes please briefly describe your certificate setup.
### If there are allow-listed hashes please provide exact binaries for which hashes are created via file sharing service, available in public with anonymous access for verification.
*******************************************************************************
We aren't using vendor_db functionality at this time.

*******************************************************************************
### If you are re-using a previously used (CA) certificate, you will need to add the hashes of the previous GRUB2 binaries exposed to the CVEs to vendor_dbx in shim in order to prevent GRUB2 from being able to chainload those older GRUB2 binaries. If you are changing to a new (CA) certificate, this does not apply.
### Please describe your strategy.
*******************************************************************************
Our CA and shim are new.

*******************************************************************************
### What OS and toolchain must we use to reproduce this build?  Include where to find it, etc.  We're going to try to reproduce your build as closely as possible to verify that it's really a build of the source tree you tell us it is, so these need to be fairly thorough. At the very least include the specific versions of gcc, binutils, and gnu-efi which were used, and where to find those binaries.
### If the shim binaries can't be reproduced using the provided Dockerfile, please explain why that's the case and what the differences would be.
*******************************************************************************
This build is all Rocky 8.8 dependencies, using rpmbuild.

To ensure reproducibility, I have "frozen" all the dependent Rocky 8 packages needed and put them in their own repository.  It can be found in the builder's Dockerfile.

Using a tagged container base plus this repository should ensure binaries are 100% reproducible.

Current reproducible shim build location:  https://bitbucket.org/ciqinc/ciq-shim-build


*******************************************************************************
### Which files in this repo are the logs for your build?
This should include logs for creating the buildroots, applying patches, doing the build, creating the archives, etc.
*******************************************************************************
shim_rpmbuild.log contains a log of the docker build run.  This includes dependency install, compilation, hash comparison, etc.

*******************************************************************************
### What changes were made since your SHIM was last signed?
*******************************************************************************
N/A

*******************************************************************************
### What is the SHA256 hash of your final SHIM binary?
*******************************************************************************
SHA256 (shimx64.efi) = 7f739b33cd666685cdba46a9b16265d12b73e73c41838a76be4d01f342b5831b


*******************************************************************************
### How do you manage and protect the keys used in your SHIM?
*******************************************************************************
- Private key for CA is kept in offline vault by default
- Private keys for child certificates stored on HSM in a FIPS-140-2 environment for signing
- Private keys are not exportable from HSM by design
- Key backups are also kept in secure offline vault

*******************************************************************************
### Do you use EV certificates as embedded certificates in the SHIM?
*******************************************************************************
No, only the CIQ secureboot CA (PKI) is embedded in our Shim

*******************************************************************************
### Do you add a vendor-specific SBAT entry to the SBAT section in each binary that supports SBAT metadata ( grub2, fwupd, fwupdate, shim + all child shim binaries )?
### Please provide exact SBAT entries for all SBAT binaries you are booting or planning to boot directly through shim.
### Where your code is only slightly modified from an upstream vendor's, please also preserve their SBAT entries to simplify revocation.
*******************************************************************************
Besides being signed with our keys, We intend to leave our grub2 and fwupd source code completely unchanged from the upstream Rocky (RHEL) versions, as we have no need to customize it beyond that.


*******************************************************************************
### Which modules are built into your signed grub image?
*******************************************************************************
Rocky 8 / Grub 2.02-148 :
```
efinet lsefi lsefimmap connectefi
all_video boot blscfg btrfs 
cat configfile cryptodisk echo 
ext2 fat font gcry_rijndael 
gcry_rsa gcry_serpent gcry_sha256 gcry_twofish 
gcry_whirlpool gfxmenu gfxterm gzio 
halt hfsplus http increment
iso9660 jpeg loadenv loopback
linux lvm luks mdraid09
mdraid1x minicmd net normal
part_apple part_msdos part_gpt password_pbkdf2
png reboot regexp search
search_fs_uuid search_fs_file search_label serial
sleep syslinuxcfg test tftp
video xfs efi_netfs efifwsetup 
backtrace chain usb usbserial_common
usbserial_pl2303 usbserial_ftdi usbserial_usbdebug keylayouts 
at_keyboard
```

Rocky 9 / Grub 2.06-61 :
```
efinet lsefi lsefimmap connectefi 
backtrace chain all_video boot 
blscfg btrfs cat configfile 
cryptodisk echo ext2 fat 
font gcry_rijndael gcry_rsa gcry_serpent gcry_sha256 
gcry_twofish gcry_whirlpool gfxmenu gfxterm 
gzio halt hfsplus http 
increment iso9660 jpeg loadenv
loopback linux lvm luks 
mdraid09 mdraid1x minicmd net 
normal part_apple part_msdos part_gpt 
password_pbkdf2 png reboot regexp 
search search_fs_uuid search_fs_file search_label serial 
sleep syslinuxcfg test tftp 
video xfs efi_netfs efifwsetup
usb usbserial_common usbserial_pl2303 usbserial_ftdi 
usbserial_usbdebug keylayouts at_keyboard
```

*******************************************************************************
### What is the origin and full version number of your bootloader (GRUB or other)?
*******************************************************************************

Rocky / RHEL 8 base:
  - grub2-2.02 base version
  - grub2-2.02-148.1 (current version)
  
(We plan to track security updates from upstream when building Grub)

*******************************************************************************
### If your SHIM launches any other components, please provide further details on what is launched.
*******************************************************************************
We have successfully packaged and tested a RockyLinux version of certwrapper (formerly certmule).  That is, a certmule package signed by us, but containing the Rocky Linux CA.

This seems perfect for our use-case, as the Rocky grub2 + fwupd upstream packages could be used as-is without the need for recompilation or re-signing.  While keenly interested in kernel modifications, we don't have as much cause to update fwupd or grub2, and would prefer to use our upstream whenever feasible.

I want to inquire about signing this wrapper efi and making it available to users.

The certmule package in question (with the embedded Rocky CA) is located at:  https://bitbucket.org/ciqinc/certmule-rocky/

*******************************************************************************
### If your GRUB2 launches any other binaries that are not the Linux kernel in SecureBoot mode, please provide further details on what is launched and how it enforces Secureboot lockdown.
*******************************************************************************
No, Linux kernel launches are all we are interested in.


*******************************************************************************
### How do the launched components prevent execution of unauthenticated code?
*******************************************************************************
In the case of the kernel, both the RHEL variant and the upstream ("new") variants prevent this by default, and we do not want to change that.

In the case of Grub + Fwupd, we will be running the same Rocky/RHEL versions unmodified, which also do not execute unauthenticated code by default.

*******************************************************************************
### Does your SHIM load any loaders that support loading unsigned kernels (e.g. GRUB)?
*******************************************************************************
Grub2 will only load unsigned code if the secureboot feature is turned off  load unsigned kernels, but only with secureboot mode turned off on an end-user's system.

*******************************************************************************
### What kernel are you using? Which patches does it includes to enforce Secure Boot?
*******************************************************************************
We are using our RHEL upstream variants: 4.18 and 5.14 with minor patches (on top of the many patches from Red Hat and others).

We are also building and packaging supported upstream kernels designed for use on Rocky and enterprise-Linux variants.  These include supported LT versions (5.4, 5.10, 5.15, 6.1), as well as the rollling latest-stable version.

I understand that these all enforce secure boot "out of the box".

*******************************************************************************
### Add any additional information you think we may need to validate this shim.
*******************************************************************************
No extra info, just some questions about using certwrapper/certmule to trust upstream distro components. (I like the "mule" name better ;-) )  Can't find this being used or approved in other reviews, but it's very interesting.  We're maintaining the beginnings of an RPM, and it's definitely something that should find its way into distros!




