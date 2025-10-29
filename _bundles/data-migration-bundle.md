---
layout: bundle
title: "DataMigrationBundle"
slug: data-migration-bundle
repo: "https://github.com/talkywalky/DataMigrationBundle"
composer: "talkywalky/data-migration-bundle"
docs: "https://github.com/talkywalky/DataMigrationBundle#readme"
status: "stable" # draft | experimental | stable | deprecated
symfony_min: "7.1"
symfony_max: "7.4"
php_min: "8.2"
maintainers:
- team: "backend"
  owners: ["@talkywalky"]
---

## Overview

A Symfony bundle for managing and executing data migrations in your application. Unlike Doctrine migrations which handle schema changes, this bundle focuses on data transformations and seeding.

## Key Features

- 🔄 **Organized Migrations**: Group migrations by namespace for better organization
- 📊 **Priority System**: Control the execution order within namespaces
- 🔍 **Tracking**: Automatically tracks executed migrations to prevent duplicates
- 🧪 **Dry Run Mode**: Test migrations without actually executing them
- 🚀 **Automatic Discovery**: Migrations are auto-discovered and registered
- 💾 **Flush Control**: Decide when to flush changes to the database
