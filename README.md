# Celements parent POMs

This repository contains the Maven BOM and parent POMs used by Celements modules.

The root POM is the release aggregator for the parent POM release train. Release
from the repository root to keep all published POM artifacts on the same version.

The current setup separates dependency version alignment from parent inheritance:

- `celements-bom` manages dependency versions and common dependency-management fixes.
- `celements-parent` imports the BOM and provides common build behavior.

## Intent

The new structure keeps the release train aligned without using parent POMs as hidden
dependency bundles. Modules should declare the dependencies they actually use; the BOM
supplies the versions.

This avoids the old `base-pom` / `components-pom` split where foundational modules could
not inherit the component parent without creating dependency cycles.

## `celements-bom`

`celements-bom` is the version catalog. It manages Celements artifacts and shared
third-party libraries such as XWiki, Spring, Jackson, logging, and test libraries.

It does not add dependencies by itself. Importing the BOM only means declared
dependencies can omit versions when they are managed by the BOM.

The Celements release train is controlled by the parent/BOM version. Inside the BOM,
Celements artifacts use the `celements.version` property so module versions stay aligned.

## `celements-parent`

`celements-parent` is the default parent for migrated Celements modules.

It owns build behavior: Java version, plugin management, compiler/resources/test/source/
deploy defaults, repositories, distribution management, and enforcer rules.

It should not contain normal production dependencies. Dependencies and scopes belong in
the module POM.

## Dependency scopes

Scopes are intentional:

- `compile`: part of the module's public or runtime classpath and transitive to consumers
- `provided`: needed to compile, but supplied by the final webapp/runtime container
- `test`: only needed for tests

For plugin-style Celements modules deployed into `celements-web`, core webapp runtime
dependencies are often `provided`; the final WAR lists the runtime set explicitly.
