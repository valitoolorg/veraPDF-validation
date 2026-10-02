# Valitool maintained veraPDF fork

This repository contains the Valitool development branch for the Jakarta EE build of veraPDF. Development and reviews take place on Gitea; approved commits are pushed to the public GitHub fork.

- Development repository: https://dev.validool.org/valitool/veraPDF-validation
- Public source repository: https://github.com/valitoolorg/veraPDF-validation
- Upstream repository: https://github.com/veraPDF/veraPDF-validation
- Development branch: `codex/valitool-jakarta-1.30`
- Upstream source tag: `jakarta-v1.30.2`, commit `26e8194476390d9d25e8062547af2fd64a0ba5b1`
- Maven group: `org.valitool.verapdf`
- Development version: `1.30.2-valitool.1-SNAPSHOT`

## Baseline and identity

The initial fork changes Maven coordinates, build metadata and documentation. Java sources and PDF/A rule behavior are unchanged relative to the upstream tag. The tag still declares project version 1.30.0, whereas the published Jakarta artifacts have version 1.30.2. The fork records this distinction explicitly in `valitool-upstream.json`.

External veraPDF parser and PDF model dependencies are pinned to 1.30.2. The validation reactor uses the corresponding library fork and pins the optional WCAG algorithms dependency to the published 1.30.1 version. Java packages and automatic module names retain their upstream names. Use a consistent fork dependency graph so original and fork artifacts do not provide duplicate classes.

The snapshot is a development identity. Published releases will use distinct immutable versions and source tags. Changing the Maven group does not constitute a CVE fix; scanners and SBOMs must retain the upstream identity and patch history.

## Build

Build the library first and install it in a dedicated local Maven repository. Then build validation against the same repository. From the directory containing both checkouts, run:

```powershell
$taskMavenRepository = Join-Path $PWD 'maven-repository'
mvn -B -ntp "-Dmaven.repo.local=$taskMavenRepository" -f veraPDF-library/pom.xml clean install
mvn -B -ntp "-Dmaven.repo.local=$taskMavenRepository" -f veraPDF-validation/pom.xml clean install
```

Maven 3.9.16 and Temurin JDK 21.0.12.1 were used for the baseline. The inherited source/target setting remains Java 8. This is not a Java 17 runtime acceptance test for Valitool. Existing compiler warnings about source/target 8 and the missing bootstrap classpath are recorded rather than suppressed.

Source JARs are attached through the existing parent configuration. ZIP entry timestamps and the filtered veraPDF build date are fixed to the upstream source commit timestamp. Both clean builds succeeded; the second ran offline. All 12 binary and source JARs across the two reactors were byte-identical. Repository-specific hashes and executed test counts are recorded in `valitool-baseline.json`. The library ran 291 tests; the validation reactor ran no Surefire tests. Targeted regression tests are the next work package.

The private Gitea Maven registry is configured at `https://dev.validool.org/api/packages/valitool/maven`, with server IDs `valitool-verapdf-releases` and `valitool-verapdf-snapshots`. Registry authentication belongs in local or CI Maven settings. No credentials are stored here. The baseline artifacts were installed locally; registry deployment and a product release are separate steps.

## Upstream maintenance

Keep `origin` pointed at Gitea, `upstream` at veraPDF and `github` at the public fork. Integrate a new upstream release on a separate Gitea branch, reapply only necessary commits, update pinned dependencies and source provenance, then run the relevant tests and comparisons. Preserve upstream tags and immutable release tags. Push selected approved branches and tags to GitHub after the Gitea review.

Each functional or security patch must record its purpose, regression test and upstream reference. Drop patches already included upstream. A fixed development build does not change the security status of older published product artifacts.

## Licenses and current scope

Upstream license and copyright notices remain in place. The MPL 2.0 option is the intended basis for distributing the covered veraPDF components with Valitool; individual components such as XMP retain their own applicable license notices. Recipients must be able to obtain the corresponding covered sources for distributed artifacts.

The baseline establishes source identity and a repeatable build. Structured document diagnostics, processor isolation, HybridDoc integration, performance improvements and CVE fixes follow in separate commits.
