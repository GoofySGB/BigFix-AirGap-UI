# BigFix AirGap UI

BigFix AirGap UI is a Windows executable interface for running `BESAirgapTool.exe`. It helps users create site lists, choose content options, run airgap processing, monitor live tool output, and collect generated Fixlet, download, manual-list, and zip outputs without manually building command lines.

## Included Application

- `BESAirGapUI.exe` - Windows UI for the airgap workflow.
- `BESAirGapUI-User-Guide.txt` - Detailed user guide.

The UI expects `BESAirgapTool.exe` to be supplied separately in the same working folder as `BESAirGapUI.exe`.

## Version

Current release: `2.0.0`

## Launching

1. Open the folder that contains `BESAirGapUI.exe`.
2. Double-click `BESAirGapUI.exe`.
3. Enter a serial number and email address, or choose a saved pair from `Saved Serial Numbers`.
4. Click `Run` to create or refresh the site list.

## Main Screen Fields

- `Serial Number` - License serial number used for the current run.
- `Email Address` - Email address associated with the serial number.
- `Saved Serial Numbers` - Drop-down list of previously used serial and email pairs.
- `Clear All Saved Serial Numbers` - Removes saved serial/email history when the application closes.
- `Delete All Databases` - Deletes `Airgap.db` and serial-specific `Airgap.db.<serial>` files when the application closes.
- `Run` - Runs `BESAirgapTool.exe` to create or refresh the site list.
- `Search` - Filters visible site rows as you type.
- `Selected Row Preview` - Shows the highlighted row and selected option.
- `Run Airgap Tool` - Starts processing for the selected site options.

## Main Workflow

1. Enter or select the serial number and email address.
2. Click `Run` and wait for the site list to load.
3. Use search to find specific sites.
4. For each required site, select a content option.
5. Review the selected row preview.
6. Click `Run Airgap Tool`.
7. If `R` is selected, choose whether to zip the `Downloads` folder at the end of the run.
8. If prompted, enter optional filter values or leave fields blank to skip filters.
9. Watch the BES AirGap Status window for live command output.
10. Review the final `Fixlet`, `Downloads`, manual list, zip, and status output locations.

## Content Options

| Option | Name | Description |
| --- | --- | --- |
| `Q` | Fixlet Content Only | Retrieves Fixlet content only. |
| `G` | Incremental Fixlet Only | Runs the incremental Fixlet content path. |
| `R` | Fixlet + SHA1 | Retrieves Fixlet content and SHA1/download information. `R` runs also create a serial-numbered manual list. |
| `F` | Create Filter List | Creates a filter list for one site. Only one site can use `F` at a time. |

## Optional Filters

When `R` processing is selected, the UI can prompt for optional filters for each selected download site:

- -fcategory - Fixlet category property
- -fcve - To specify the CVE (Common Vulnerabilities and Exposures) id associated with a security patch
- -fdays - To select Fixlets whose last modified date falls within a specified number of days from the date you run the command.
- -fproduct - To specify the product name to which the Fixlet is applicable, such as Win2008 or Win7. This information is not shown in the Console. This option is available only for sites related to patches for Windows operating systems.
- -fsource - Provider of file, such as BigFix, Adobe, or Microsoft.
- -fseverity - To specify the severity that a vendor associates with a security patch.

Leave a field blank to skip that filter. To specify multiple values, separate values with commas.

Example:

```text
-fseverity "Critical, Important"
```

Values with spaces should be wrapped in double quotes. Filter values are case sensitive.

## Manual Download List

`R` option runs include:

```text
-createmanuallist manual_list_<serialnumber>.txt
```

After the run, the UI moves the manual list into the `Downloads` folder. If the manual list exists in `Downloads`, the UI displays:

```text
MANUAL DOWNLOAD REQUIRED
See Manual List In Downloads Folder
```

Use that manual list to identify items that require manual download handling.

## Zip Output

When zip creation is selected for an `R` run, the `Downloads` folder is compressed into a zip file named:

```text
Downloads_<serialnumber>_<yyyy-MM-dd>.zip
```

Example:

```text
Downloads_1234567890_2026-04-29.zip
```

## Tools Menu

- `Debug mode` - Runs the tool with a visible command prompt for troubleshooting.
- `Delete Fixlet Folder` - Removes the `Fixlet` folder during the next `Run` step.
- `Delete Downloads Folder` - Removes the `Downloads` folder during the next `Run` step.
- `Set Proxy` - Opens proxy settings. User, password, hostname, and port are required when enabled.
- `Use Https` - Adds HTTPS certificate arguments for `BESAirgapTool.exe` using `ca-bundle.crt`.

## Status Window

- The status window appears during tool execution and is centered on the screen.
- The window shows live output from `BESAirgapTool.exe`.
- The `Stop` button is available during active runs and attempts to terminate the running process.
- Completion and failure messages remain visible for review after the run ends.

## Help Menu

- `Version` displays the current application version.
- `Support` opens a help popup with support contact information.

## Generated Files

The application creates and uses working files and folders such as:

- `Airgap.db`
- `Airgap.db.<serialnumber>`
- `Fixlet`
- `Downloads`
- `LOGS`
- `sitelist.txt`
- `sitelistcache.txt`
- `sitelistdownload.txt`
- `sitelistfilter.txt`
- `filter.txt`
- `BESAirGapUI.settings.json`

These generated files should generally stay out of source control because they can contain local state, serial-specific data, logs, or downloaded content.

## Troubleshooting

- If the application cannot start, confirm `BESAirgapTool.exe` is in the same folder as `BESAirGapUI.exe`.
- If a run fails, review the BES AirGap Status window for live output and error messages.
- If an unexpected error occurs, review `BESAirGapUI-crash.log` in the application folder.
- If `F - Create Filter List` is selected, confirm only one site uses `F`.
- If download output is missing, check the `Downloads` folder and the manual list popup.
- If email delivery blocks the executable, use an approved internal distribution channel and consider code signing with a trusted certificate.

## Recommended Practices

- Keep the application and required tool files together in one approved folder.
- Review selected site options before clicking `Run Airgap Tool`.
- Use debug mode only when troubleshooting is required.
- Do not delete databases unless you intend to reset stored `Airgap.db` state.
- Preserve `LOGS` output when troubleshooting failed runs.
