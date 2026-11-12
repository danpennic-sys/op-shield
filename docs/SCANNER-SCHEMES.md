# OP-Shield Scanner Schemes (v0.1)

Deterministic analysis schemes that identify the minimal Linux OS functionality required by a platform instantiation and emit evidence-ready findings.

Aligned to Navy DON27BZ02-NV010: determine required OS functionality, remove unnecessary components, configure remaining OS for improved cybersecurity posture while preserving required function.

All schemes produce canonical, sortable outputs so the same inputs yield the same payload (ready for QuantumGuard CONTRACT-v1 sealing).

## Scheme 1: Static Package & Service Inventory

**Inputs:** `dpkg -l` / `rpm -qa`, `systemctl list-unit-files`, package metadata, declared application manifests.

**Logic:**
- Collect installed packages and their dependencies.
- Map declared application requirements (or a seed list) to minimal package set.
- Identify unused packages and non-essential services.
- Output sorted lists of required / removable packages and disabled services.

**Determinism:** Sort all lists lexicographically. Hash the raw inventory before analysis.

**Phase I fit:** High. Can be demonstrated on a reference Ubuntu/Debian or Yocto image without runtime changes.

## Scheme 2: Runtime Observation (Process / Socket / File)

**Inputs:** `/proc`, `ss -tulpn` or `netstat`, `lsof`, open file descriptors, loaded kernel modules.

**Logic:**
- Capture a short observation window of running processes, listening ports, and open files while the platform application is exercised.
- Attribute observed resources to required packages/services.
- Flag any resource not attributable to the declared mission function.

**Determinism:** Canonicalize paths, sort PIDs/ports by name, hash the observation snapshot. Observation window is informational only (not used by verifier).

**Phase I fit:** Medium-high. Requires a controlled exercise of the platform application on the reference system.

## Scheme 3: Config Policy Diff

**Inputs:** Key configuration files (`/etc/ssh/sshd_config`, `/etc/sysctl.conf`, PAM configs, firewall rules, systemd unit files).

**Logic:**
- Compare against a minimal hardened baseline (CIS-like or Navy-relevant STIG subset).
- Emit before/after hashes and recommended deltas.
- Preserve any setting required by the platform application.

**Determinism:** Normalize whitespace, sort directives, hash each file.

**Phase I fit:** High. Pure file analysis.

## Scheme 4: Dependency Graph from Declared Application

**Inputs:** Application binary, shared libraries (`ldd`), package provides, service units it activates.

**Logic:**
- Start from the known mission application.
- Walk dynamic and package dependencies.
- Produce the transitive minimal set.
- Everything outside the graph is candidate for removal.

**Determinism:** Sort graph edges and nodes.

**Phase I fit:** High for COTS or open-source reference platforms.

## Scheme 5: Differential Against Minimal Baseline Image

**Inputs:** Current platform image vs. a known-good minimal image (or previous sealed state).

**Logic:**
- Package list diff, file hash diff, service unit diff.
- Classify differences as required (by application) or removable.

**Determinism:** Content-addressed diffs. Previous evidence pack hash can be referenced.

**Phase I fit:** Medium. Useful once a reference minimal image exists.

## Scheme 6: Embedded / Yocto / Container-Aware

**Inputs:** Yocto layer recipes, `bitbake` dependency graph, Docker/Podman inspect, or rootfs manifest.

**Logic:**
- Parse image recipe or container config for required packages and services.
- Apply the same minimal-set logic as Scheme 1/4.

**Determinism:** Hash the recipe/manifest before analysis.

**Phase I fit:** High if the proposed platform is a Yocto or containerized autonomous system.

## Evidence Output (common)

Every scheme emits findings compatible with `docs/PAYLOAD-SCHEMA.md`:

- Sorted lists
- Input hash of the raw observation
- Config change records with before/after hashes
- Recommendation hash of the proposed hardened state

The resulting payload is sealed into a CONTRACT-v1 bundle. Verification remains a pure function of the sealed bytes.

## Recommended Phase I Combination

1. Scheme 1 + Scheme 3 + Scheme 4 on a reference Ubuntu or Yocto image.
2. Demonstrate package removal and service disable recommendations.
3. Seal the analysis into a QuantumGuard evidence pack.
4. Show `qg verify` returns PASS on the sealed result.

Status: Draft schemes. Not yet implemented. Not demonstrated.

---

Operator Pages LLC — We don’t promise anything we prove it.
