---
layout: bundle
title: "DataFlowBundle"
slug: data-flow-bundle
repo: "https://github.com/talkywalky/DataFlowBundle"
composer: "talkywalky/data-flow-bundle"
docs: "https://github.com/talkywalky/DataFlowBundle#readme"
status: "stable" # draft | experimental | stable | deprecated
symfony_min: "7.1"
symfony_max: "7.4"
php_min: "8.2"
maintainers:
- team: "backend"
  owners: ["@talkywalky"]
---

## Overview

A Symfony bundle to build reliable, composable data pipelines (Read → Process → Write) with minimal boilerplate.

## Features

- 📥 **CSV/API/Custom Readers**: pull data from files, HTTP APIs, DB, or any source
- 🔧 **Processor Orchestration**: rename indexes + validate rows before writing
- 📤 **Writers out of the box**: Doctrine ORM writer; easy to add file/API writers
- 🔁 **Transformers**: regular and postponed steps for clean normalization/hydration
- 🧩 **Small, testable building blocks**: plug only what you need
