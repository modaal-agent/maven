# Modaal Maven repository

The static Maven repository for `dev.modaal.*` artifacts, served by GitHub
Pages at:

```
https://modaal-agent.github.io/maven
```

## Consuming

Add one repository block (Gradle, `settings.gradle.kts`):

```kotlin
dependencyResolutionManagement {
  repositories {
    maven {
      url = uri("https://modaal-agent.github.io/maven")
      content { includeGroupByRegex("""dev\.modaal(\..*)?""") }
    }
    mavenCentral()
    google()
  }
}
```

The `content` filter keeps Gradle from probing this host for anything outside
the `dev.modaal.*` groups. No authentication — the host serves anonymous GETs.

Artifacts currently published here:

| Group | Artifacts | Producer |
| --- | --- | --- |
| `dev.modaal.duet` | `kernel` (+ per-target coordinates), `kernel-test`, `shells-compose` | [`modaal-agent/duet`](https://github.com/modaal-agent/duet) |

## Publishing (maintainers)

Nothing is pushed here by hand. Each producing repository has a tag-triggered
publish workflow that stages a release, asserts it is **complete** (every
coordinate of the release present — partial uploads break Gradle Module
Metadata routing), asserts it is **new**, and lands it as one commit. The
credential is a deploy key held by the producing repository; consumers never
need one.

Rules this tree obeys:

- **A published version is immutable.** No `<group>/<artifact>/<version>/`
  path is ever rewritten; a bad release is followed by a new version.
- **A release is one commit.** The tree never carries a half-published
  version.
- **`maven-metadata.xml` is derived.** It is regenerated from the version
  directories on every publish; fixed-version consumers resolve by pure path
  GET and never read it.
