# Intune App Protection Framework (Automation)

This repository provides a PowerShell-based automation framework to import an Intune **App Protection** baseline using JSON templates. The framework is designed to be **repeatable** (safe to re-run) by applying **duplicate detection** based on policy `displayName` before creating new objects.

## Background and origin

Microsoft has published guidance and a recommended **data protection framework** for Intune app protection policies (APP), including the concept of importing **sample JSON templates** for the configuration framework using PowerShell scripts. This repository packages the framework in a customer-ready form and provides an updated automation approach for environments where the original sample distribution method is no longer practical for direct, ready-to-use downloads.

Historically, some Microsoft and community scripts for Intune/Entra automation relied on the legacy **AzureAD** / **AzureADPreview** PowerShell modules. Microsoft has since announced the retirement of these modules and recommends migrating scripts to the **Microsoft Graph PowerShell SDK** (or Microsoft Entra PowerShell) to avoid disruption.

Operationally, the legacy AzureAD/AzureADPreview modules have also been associated with PowerShell compatibility and assembly-loading issues in some contexts (for example, PowerShell 7 / Function Apps), which often led customers to pin module versions or use Windows PowerShell compatibility modes. This framework therefore standardizes on the Microsoft Graph PowerShell module as the supported and forward-looking approach.

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

## Microsoft documentation references

The following Microsoft sources provide the conceptual and operational context for this framework:

- **Data protection framework using app protection policies** (taxonomy and guidance; references importing sample JSON templates using PowerShell)
- **Create and deploy app protection policies** (how APPs are created and assigned; links back to the framework)
- **App protection policy settings for iOS/iPadOS** (settings reference used when reviewing the JSON templates)
- **AzureAD PowerShell retirement and migration guidance** (Microsoft direction to migrate legacy AzureAD/AzureADPreview scripts)
- **Microsoft Graph PowerShell SDK guidance** (Microsoft statement that Azure AD module is being replaced by Graph PowerShell)

## Support statement

This framework uses Microsoft Graph calls to create Intune policy objects from JSON. Always validate the resulting policies and assignments in the Intune admin center before rolling out broadly.
