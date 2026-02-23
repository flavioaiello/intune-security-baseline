# Intune App Protection Framework (Automation)

This repository provides a PowerShell-based automation framework to import an Intune **App Protection** baseline using JSON templates. The framework is designed to be **repeatable** (safe to re-run) by applying **duplicate detection** based on policy `displayName` before creating new objects.

## What this framework imports

When executed end-to-end, the framework imports the following Intune artifacts:

- **Device compliance policies** (required to evaluate device posture)
- **Device configuration profiles** (required to harden and configure the device)
- **Managed app protection (MAM) policies** (required to protect corporate data inside apps)

## Quick start

1. Open an administrative PowerShell session.
2. Change directory to the folder that contains the scripts.
3. Run the entry point script **without parameters**: .\Import-AppProtectionPolicy.ps1

### Execution order

The entry point is intended to call the following scripts **in this order**:

1. `Import-CompliancePolicy.ps1`
2. `Import-DeviceConfiguration.ps1`
3. `Import-ManagedAppPolicy.ps1`
<BLOCKQUOTE><P>If your package uses a different entry point name (for example `Import-AppProtectionConfig.ps1`), the expected behavior remains the same: the entry point orchestrates the three imports above.</P></BLOCKQUOTE>

## Optional post-step: scope tags

After importing the policies, you may optionally run the scope tag assignment script: .\AssignEUDScopeTag.ps1

This post-step is **optional** and **separate** from the import flow.

### Requirements

- A scope tag named **`EUD`** must already exist in Intune.
- If you prefer a different scope tag name, you can adjust the script to match your environment.

### PAW separation

The scope tag assignment script intentionally **excludes** policies with the prefix `PAW-`. This supports environments that also manage **Privileged Access Workstations (PAW/PAW-CSM)** and follow the common practice of separating management scope between **Enterprise User Devices (EUD)** and **PAWs**.

## Prerequisites

- Microsoft Graph PowerShell SDK installed (the scripts validate the module presence).
- An account that can consent to and use the required Graph permissions.

## Support statement

This framework uses Microsoft Graph calls to create Intune policy objects from JSON. Always validate the resulting policies and assignments in the Intune admin center before rolling out broadly.




