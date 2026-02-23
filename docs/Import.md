# Importing the App Protection Framework

This document explains how the import automation works and what to expect during execution.

## Overview

The framework imports Intune configuration from JSON templates by calling dedicated worker scripts for each policy type. The orchestrator/entry point coordinates the import in a fixed order to ensure dependencies are created consistently.

## Import order

The import runs in the following order:

1. **Device compliance policies** via `Import-CompliancePolicy.ps1`
2. **Device configuration profiles** via `Import-DeviceConfiguration.ps1`
3. **Managed app protection policies (MAM)** via `Import-ManagedAppPolicy.ps1`

## Duplicate detection (idempotent re-runs)

Each worker script checks whether a policy with the same `displayName` already exists in the tenant. If it exists, the import for that JSON file is skipped. This design allows you to re-run the framework without unintentionally creating duplicates.

At a high level:

- Compliance policies are checked in the compliance policy collection.
- Device configuration profiles are checked in the device configuration collection.
- App protection policies are checked across the relevant app protection policy endpoints.

## Authentication model

The scripts use the Microsoft Graph PowerShell SDK. When executed, they:

- Validate that the required Graph module is installed.
- Establish a Graph connection (or reuse an existing one).
- Reconnect with additional scopes when required.

## Inputs (JSON)

The worker scripts accept a JSON file path parameter. When the parameter is omitted, the scripts prompt for a JSON path. The orchestrator passes explicit JSON file paths to each worker script.

During processing, the scripts remove properties that must not be present in create requests (for example IDs and timestamps) and then submit the resulting JSON to Microsoft Graph.

## Output and reporting

The orchestrator prints a summary of what was processed and whether each JSON file was:

- Imported successfully
- Skipped because it already existed
- Failed due to an error

## Recommended validation

After the import completes, validate the created artifacts in the Intune admin center:

- **Devices → Compliance policies**
- **Devices → Configuration profiles**
- **Apps → App protection policies**

Then assign the imported policies to the appropriate user/device groups according to your rollout plan.
