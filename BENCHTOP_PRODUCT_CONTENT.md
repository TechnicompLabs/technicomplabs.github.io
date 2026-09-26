# Benchtop Linux: product content source

Editorial source material for the future benchtoplinux.org site. This is not a draft of that site and does not imply its page structure or design. Copy for benchtoplinux.org should be written fresh for its actual layout, screenshots, navigation, release status, and download experience, using this file as source material.

This file is excluded from the Jekyll build of technicomplabs.io. The Labs site keeps a shorter, conceptual explanation of Benchtop Linux at `/benchtop/`; the longer versions of that material are preserved here.

Conventions used below:

- **Established** marks facts confirmed for publication.
- **Rationale** marks design reasoning that can be stated as the project's intent.
- **Not yet established** marks things that should not be published as fact until they are confirmed.

---

## 1. Terminology and naming

**Established**

- Full name: **Technicomp Benchtop Linux**. Short form: **Benchtop Linux**. Spelled "Technicomp," never "TechniComp."
- Category: a **stabilized workstation rolling release derived from openSUSE Tumbleweed and MicroOS**.
- Desktop: the **Blueprint Environment** is the complete, integrated GNOME-based desktop experience. The **Blueprint Shell** is only the GNOME Shell layer of it (extensions, Shell configuration, Shell behavior). Neither is a fork of GNOME.
- GNOME policy: in prose, "**the previous upstream-supported GNOME release**" (or "the previous supported GNOME release"). GNOME's own name for this branch is "old stable," two words; "old-stable" is only GNOME's release-type label. Never write "previous stable," which implies the release is unsupported.
- Kernel policy: **LTS by default; a current kernel for hardware enablement.**
- Never write "LTS core packages." Only the kernel follows an LTS branch; most of the system follows Tumbleweed.
- Tagline: "The operating system for the technical workbench."
- Retired: "Conservative where regressions are expensive. Current where staleness is expensive." The idea is correct, but the phrasing is not part of the product voice; explain the release model in ordinary sentences, and don't replace it with another symmetrical slogan.
- Retired: "Updates that don't break your bench." Slowroll was an earlier base and is no longer used; don't mention it.

**Words to avoid in product copy:** purpose-built, deliberately, seamlessly, powerful, modern, cohesive, designed to. Avoid stacked slogans, sentence fragments, and repeated "not X but Y" constructions.

**Principles** (use sparingly, one or two per page at most):

- "Do not modify the operating system. Extend your account."
- "The operating system is a finished product, not a construction kit."
- Informal, for the desktop in particular: "Let other people be the beta testers." It works as an aside after the policy has been explained. It should not be a headline.

---

## 2. Status and public resources

**Established**

- Status: alpha. Installable images are released.
- Architectures: x86 and ARM.
- Images are built and published through the openSUSE Build Service: <https://build.opensuse.org/project/show/home:technicomp>
- Source and packaging: <https://github.com/TechnicompLabs>
- Design notes: <https://github.com/pauldmartinphd/benchtop-notes>
- The domain benchtoplinux.org is registered. As of September 2026 the site is not live.

---

## 3. Audience and positioning

**Established / rationale**

Technicomp Benchtop Linux is a complete workstation operating system for professionals and technically sophisticated users who want to sit down and work without first assembling, and then continually maintaining, the Linux installation itself. Its components do not all follow the same update policy, because they do not all fail in the same way. A regression in the kernel or the desktop can take a working machine out of service for a day; a compiler or library that is two years old quietly costs time on every project. Benchtop Linux treats those risks differently, maintains the system layer as a single coherent snapshot, and leaves the user's own environment flexible.

The system is opinionated about the operating-system layer and flexible in the user's own environment.

A user who prefers a minimal base installation and wants to assemble every part of the workstation by hand will probably be better served by a different distribution, and that is a reasonable preference.

---

## 4. The stabilized workstation rolling-release model

**Established**

- Most of the software stack follows openSUSE Tumbleweed and stays current: compilers, language runtimes, system libraries, firmware, graphics components, and development tools.
- The kernel and the desktop are handled more conservatively.

**Rationale** (longer version):

> Fixed-release distributions trade currency for predictability: the whole system is frozen for months or years, and fixes are backported into old versions. Rolling distributions make the opposite trade. Benchtop Linux is a stabilized workstation rolling release, which means it applies stability selectively instead of to everything.
>
> Most of the software stack follows openSUSE Tumbleweed and stays current. Compilers, language runtimes, system libraries, firmware, graphics components, and development tools are more useful new than old, and Benchtop Linux does not try to freeze them. The components with a demonstrated tendency to cause disruptive workstation regressions are handled more conservatively.

Useful short framing: *not every part of an operating system fails in the same way.*

### 4.1 Kernel policy

**Established**

- Two kernels are provided: **LTS** (the default) and **Current** (for hardware that needs support not yet in the LTS kernel).
- Both kernels carry Benchtop's hardware-support patches. They are not unmodified upstream kernels; never describe them as "verbatim."

**Rationale:** Kernel regressions are among the most disruptive failures a workstation can have (boot, suspend, graphics, storage, networking), so the default kernel follows an LTS branch. *(The parenthetical examples were inferred during editing; confirm them.)* The current kernel exists for the same reason as the rest of the hardware-integration work: some hardware needs support that the LTS kernel does not yet have.

### 4.2 GNOME n−1 policy

**Established**

- The desktop follows the previous upstream-supported GNOME release. It does not move to each new major version as soon as it ships.
- That release still receives upstream bug and security fixes.
- Benchtop Linux moves to the next major GNOME version when upstream ends support for the current one. By then, the new release has been through its initial round of fixes.

**Rationale:**

- Desktop regressions interrupt work directly, and new GNOME majors typically receive a round of fixes after release.
- The Blueprint Environment's extensions and behavior are a maintained product surface and should not change unpredictably every six months. Following n−1 also gives extensions time to catch up to each GNOME release before users see it. *(Inferred during editing; confirm before publishing.)*
- Applications are not held back by this policy, because they are Flatpaks and update independently of the desktop platform (see section 6).

---

## 5. The immutable, transactional system

**Established**

- Built on the immutable, transactional model of openSUSE MicroOS.
- Updates are applied to a new btrfs snapshot, not to the running system. The machine switches to that snapshot at the next boot.
- If an update causes a problem, the previous snapshot is still available to roll back to.

**Rationale** (longer version):

> The system package set is treated as a finished product, not a construction kit, and users are not expected to modify it. The image contains the operating system, the desktop platform, drivers, firmware, privileged services, hardware integration, development toolchains, and the other system-level plumbing that its intended workflows need.

The base operating system stays consistent, recoverable, and identical to what was tested.

### Update, rollback, and recovery philosophy

- An update never modifies the running system, so a failed or interrupted update does not leave the machine half-upgraded.
- Every update is a snapshot, so rollback is always available.
- Because the system image is the same on every machine, a problem found on one machine can be reproduced on another. This is also why users do not layer packages onto the system. *(Inferred during editing; confirm before publishing.)*

**Not yet established:** snapshot retention counts, automatic rollback or boot-health checks, update cadence, and the update notification/scheduling UX. Document these from the actual implementation.

---

## 6. Ownership layers: what goes where

**Established**

The working rule: **do not modify the operating system; extend your account.**

| Layer | Owns | Mechanism |
|---|---|---|
| System image | OS, desktop platform, drivers, firmware, privileged services, hardware integration, language toolchains | Benchtop Linux (transactional updates) |
| Graphical applications | Per-user apps | Flatpak, installed in the user's profile |
| Extra CLI software; alternate or pinned toolchain versions | Per-user tools | Homebrew, per user |
| Project dependencies | Libraries used by a project | The language's own package manager (pip/venv, Cargo, npm, RubyGems, etc.) |
| Environments needing their own mutable package set | Other distributions, isolated toolchains, services | Containers and virtual machines |
| Personal configuration | Everything else | Home directory and ordinary user settings |

**Prose preserved from the Labs page:**

> Graphical applications are installed as Flatpaks in the user's profile. Because applications are decoupled from the operating system's lifecycle, they stay current even while the desktop platform advances more cautiously. Additional command-line software, and alternate or pinned versions of toolchains, are installed through Homebrew, also per user. Linux environments that genuinely need their own mutable package sets belong in containers or virtual machines, where they cannot alter the host.
>
> Users can still customize extensively through Flatpak, Homebrew, language ecosystems, containers, virtual machines, their home directories, and ordinary user configuration. The one firm boundary is the base operating system, which stays consistent, recoverable, and identical to what was tested.

**Not yet established:** which Flatpak remotes are configured by default, how Homebrew is bootstrapped, and which container and VM tools ship by default. Installation and usage instructions for each layer belong in the documentation.

---

## 7. A complete system

**Rationale:**

- Because users do not add system packages, the image has to be complete.
- If a workflow is part of the intended workstation experience and requires system-level support, that support is already present and configured.
- Users should not routinely need to add repositories, patch kernels, install privileged daemons, create udev rules, alter PAM configuration, or reconstruct other system integration to make supported hardware or workflows function.
- Benchtop Linux does not minimize the installation image by leaving out useful infrastructure that every user would then install again.

---

## 8. Hardware integration

**Established**

- x86 and ARM.
- Principle: if making hardware work properly requires changing the operating system (kernel patches, firmware, udev rules, privileged services, or permissions), that integration belongs in Benchtop Linux, not in a setup guide that asks every user to reconstruct it.
- Current examples:
  - ASUS hardware, through asusctl
  - RGB device control, through OpenRGB
  - Microsoft Surface devices
  - Apple computers with the T2 security chip
- The current-kernel option exists for hardware that needs support not yet present in the LTS kernel.

**Guidance:** Don't publish lists of named laptop models unless each one has actually been tested. A compatibility matrix belongs on benchtoplinux.org once there is tested data behind it.

**Not yet established:** the full list of supported hardware families, tested models, and known issues.

---

## 9. Development environments

**Established / rationale**

> Benchtop Linux ships the major programming-language environments commonly used on Linux. The language implementations follow the rolling Tumbleweed packages, and each environment includes the compiler or interpreter, the standard library, the usual development infrastructure, and the package-management tooling of its ecosystem.

**Policy on third-party libraries:**

> Project dependencies belong to the ecosystem, not to the system RPM database. Python dependencies come from the Python ecosystem and normally live in a virtual environment; Rust dependencies belong to Cargo, JavaScript dependencies to npm, and Ruby libraries to RubyGems. The fact that a library is packaged as an RPM is not a reason to install it into the system image. When a project needs a different or pinned version of a language than the one in the current image, Homebrew or the ecosystem's own version manager provides it, so the system image does not need to carry parallel historical versions.

**Not yet established:** the exact list of included languages, compilers, debuggers, and build tools. Enumerate them from the image manifest rather than from memory.

---

## 10. Interactive latency and throughput

**Rationale** (longer version):

> Linux is often tuned for maximum aggregate throughput. That is the right choice for a server or a batch-processing system, but it can be the wrong one for a machine with a person sitting in front of it. A system can post excellent throughput benchmarks and still feel sluggish, because interactive tasks wait behind background work when the CPU, memory, or storage is under pressure.
>
> Benchtop Linux optimizes a workstation as a workstation. When the two goals conflict, it gives priority to low and predictable interactive latency over the last increment of aggregate throughput. The intent is not to give up throughput needlessly; responsiveness comes first, and performance is pursued within that constraint.

Short framing: a desktop should not be treated as a throughput-oriented server with a GUI attached.

**Not yet established:** the specific scheduler, I/O, memory-pressure, and power settings; benchmark methodology; and measured results. Publish them only from the actual configuration and real measurements.

---

## 11. Low-latency and professional audio

**Established / rationale**

> Real-time and low-latency audio are intended workloads. The system ships with the scheduling, resource limits, permissions, PipeWire configuration, and related plumbing that professional low-latency audio work requires, so that users do not have to convert a general-purpose desktop into an audio workstation after installation.

**Not yet established:** specific PipeWire quantum and sample-rate defaults, real-time priority limits, group membership, supported audio interfaces, and measured round-trip latency. These belong in the audio documentation once they are confirmed.

---

## 12. The Blueprint Environment

**Established / rationale** (longer version):

> The desktop in Benchtop Linux is the Blueprint Environment, an integrated desktop experience built on GNOME. It covers the interaction model, defaults, visual and behavioral consistency, extensions, configuration, and workflow conventions of the desktop, and how the desktop fits with the rest of the system. The Blueprint Shell is the part of that environment specific to GNOME Shell: the selected extensions, the Shell configuration, and its behavior.
>
> Blueprint is not a fork of GNOME or a separately implemented desktop. It is a GNOME-based environment maintained as part of Benchtop Linux, with the goal that the workstation feels like one designed system, without the seams of an upstream desktop plus an assortment of unrelated customizations. That goal is also a reason for following the previous supported GNOME release. The desktop, including the extensions and behavior that define Blueprint, is a maintained product surface, and it should not change unpredictably every six months.

**For benchtoplinux.org:** This is the part of the product that most needs screenshots and a visual tour. The Labs site deliberately doesn't try to show it.

**Not yet established:** the list of extensions in the Blueprint Shell, specific default behaviors, theming details, and keyboard conventions. Document them from the shipped configuration.

---

## 13. Intended professional workflows

**Established**

The system image provides what each workflow needs at the operating-system level, such as drivers, services, permissions, and toolchains. Most of the applications used in these fields are installed per user, as Flatpaks, through Homebrew, or inside containers, where they stay current and under the user's control.

| Workflow | Scope |
|---|---|
| AI | Local inference and model development |
| Virtualization | KVM and QEMU virtual machines, and containers |
| Software development | Compilers, interpreters, debuggers, and build tools |
| System administration and DevOps | Remote access, configuration management, backup, and storage |
| Security | Vulnerability assessment, network analysis, and digital forensics |
| Reverse engineering | Disassembly, decompilation, and debugging |
| Content creation | Low-latency audio, video, graphics, and publishing |

Keep the distinction between *system readiness* (what the image provides) and *per-user applications* (what users install) explicit wherever workflows are described.

---

## 14. Useful headings and phrases from the Labs copy

- "The operating system for the technical workbench."
- "A stabilized workstation rolling release"
- "An immutable, complete system"
- "Extending your account"
- "A complete system"
- "Latency and professional audio"
- "Its components do not all follow the same update policy, because they do not all fail in the same way."
- "A regression in the kernel or the desktop can take a working machine out of service for a day; a compiler or library that is two years old quietly costs time on every project."
- "Benchtop Linux optimizes a workstation as a workstation."
- "The one firm boundary is the base operating system, which stays consistent, recoverable, and identical to what was tested."
- "…rather than in a setup guide that asks every user to reconstruct it."

---

## 15. Material expected on benchtoplinux.org

This is a list of needs, not a sitemap:

- Screenshots and a visual tour of the Blueprint Environment
- Downloads and installation media, with checksums and signatures
- Installation instructions
- Release notes and update information
- Documentation for each ownership layer (Flatpak, Homebrew, language ecosystems, containers, VMs)
- Hardware support and compatibility information
- Development-environment documentation
- Low-latency and professional-audio documentation
- Update, rollback, and recovery documentation
- FAQ
- Source, contribution, and support links
