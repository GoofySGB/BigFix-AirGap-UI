# BES AirGap UI

BES AirGap UI is a Windows interface for running `BESAirgapTool.exe`. It helps users create site lists, choose content options, run airgap processing, monitor live tool output, and collect generated Fixlet, download, manual-list, and zip outputs without manually building command lines.

## Included Application

- `BESAirGapUI.exe` - Windows UI for the airgap workflow.
- `BESAirgapTool.exe` - BES airgap command-line tool used by the UI.

The UI expects `BESAirGapUI.exe` and `BESAirgapTool.exe` to remain in the same folder.

## Version

Current build: `2.2.1`

## Launching

1. Open the folder that contains `BESAirGapUI.exe`.
2. Double-click `BESAirGapUI.exe`.
3. Enter a serial number and email address, or choose a saved pair from the saved serial number list.
4. Click `Run` to create or refresh the site list.

## Main Workflow

1. Enter or select the serial number and email address.
2. Click `Run` and wait for the site list to load.
3. Use search to find specific sites.
4. For each required site, select a content option.
5. Review the selected row preview.
6. Click `Run Airgap Tool`.
7. If `R` is selected, choose whether to zip the `Downloads` folder at the end of the run.
8. If zip creation is selected, choose whether to split the zip into smaller files and enter the size in GB.
9. If prompted, enter optional filter values or leave fields blank to skip filters.
10. Watch the BES AirGap Status window for live command output.
11. Review the final `Fixlet`, `Downloads`, manual list, zip, and status output locations.

## Content Options

| Option | Name | Description |
| --- | --- | --- |
| `Q` | Fixlet Content Only | Retrieves Fixlet content only. |
| `G` | Incremental Fixlet Only | Runs the incremental Fixlet content path. |
| `R` | Fixlet + SHA1 | Retrieves Fixlet content and SHA1/download information. This can also create a serial-numbered manual list. |
| `F` | Create Filter List | Creates a filter list for one site. Only one site can use `F` at a time. |

## Optional Filters

When `R` processing is selected, the UI can prompt for optional filters for each selected download site:

- `-fcategory` Fixlet category property.
- `-fcve` To specify the CVE (Common Vulnerabilities and Exposures) id associated with a security patch.
- `-fdays` To select Fixlets whose last modified date falls within a specified number of days from the date you run the command.
- `-fproduct` To specify the product name to which the Fixlet is applicable, such as Win2008 or Win7.
- `-fsource` Provider of file, such as BigFix, Adobe, or Microsoft.
- `-fseverity` To specify the severity that a vendor associates with a security patch.

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

During download runs, the UI watches the AirGap tool output for the `Disk Space Required=<size>MB` check. If the required download space is larger than the free space on the `Downloads` drive, the UI stops that download attempt and shows a Retry/Cancel popup. Free up disk space, then choose Retry to continue the download.

Before zip creation, the UI also checks whether the `ZIP` output drive has enough free space for the current `Downloads` folder contents. If there is not enough room, it shows the same Retry/Cancel popup so space can be freed before compression begins.

While zipping, the BES AirGap Status window reports the free-space check, zip part creation, each file being added, manifest writing, and split-zip validation.

When zip creation is selected for an `R` run, the `Downloads` folder is compressed into a zip file in the `ZIP` folder named:

```text
Downloads_<serialnumber>_<yyyy-MM-dd>.zip
```

Example:

```text
Downloads_1234567890_2026-04-29.zip
```

If split zip creation is selected, enter the desired size per file in GB. The application creates multiple standard zip files named:

```text
Downloads_<serialnumber>_<yyyy-MM-dd>_part001.zip
Downloads_<serialnumber>_<yyyy-MM-dd>_part002.zip
```

Each split file is a normal zip file. If an individual file is larger than the selected part size, the UI writes that file as chunk entries across multiple zip parts and records those chunks in `ExtractionManifest.txt`. When split zip files are created, `ExtractionManifest.txt` and `AirGapZipExtractor.exe` are also created or refreshed in the `ZIP` folder. The manifest records every source file's relative path, final size, final SHA1, zip part, internal zip entry path, chunk index, chunk count, and chunk size, grouped by zip part so each part's contents can be inspected.

Before extraction, `AirGapZipExtractor.exe` opens an extraction status window and confirms the manifest's referenced split zip parts are present. The status window includes a Stop button that cancels extraction if it hangs or needs to be stopped. If no manifest exists, it falls back to building one from the zip parts. It extracts into a fresh `Downloads` folder; if `Downloads` already exists, the extractor uses a timestamped `Downloads_Extracted_<yyyyMMdd_HHmmss>` folder instead. Files that were chunked during zip creation are reassembled into their original relative paths. The status window reports the source folder, output folder, files or chunks being extracted, validation progress, and any failure details. When extraction completes, fails, or is stopped, the status window stays open until the user closes it. The extractor continues through the available zip parts even when some files or referenced parts are missing or corrupted. After extraction, it validates every expected file against the manifest. Missing, corrupted, and other validation failures are written to `ExtractionValidationErrors.txt`, and the extractor exits non-zero when issues are found.

## Tools Menu

- `Debug mode` - Runs the tool with a visible command prompt for troubleshooting.
- `Delete Fixlet Folder` - Removes the `Fixlet` folder during the next `Run` step.
- `Delete Downloads Folder` - Removes the `Downloads` folder during the next `Run` step.
- `Set Proxy` - Opens proxy settings. User, password, hostname, and port are required when enabled.
- `Use Https` - Adds HTTPS certificate arguments for `BESAirgapTool.exe` using `ca-bundle.crt`.

## Help Menu

- `Version` displays the current application version.
- `Support` opens a help popup with support contact information.

## Generated Files

The application creates and uses working files and folders such as:

- `Airgap.db`
- `Airgap.db.<serialnumber>`
- `Fixlet`
- `Downloads`
- `ZIP`
- `LOGS`
- `sitelist.txt`
- `sitelistcache.txt`
- `sitelistdownload.txt`
- `sitelistfilter.txt`
- `filter.txt`
- `BESAirGapUI.settings.json`
- `AirGapZipExtractor.exe`

These generated files should generally stay out of source control because they can contain local state, serial-specific data, logs, or downloaded content.

## Troubleshooting

- If the application cannot start, confirm `BESAirgapTool.exe` is in the same folder as `BESAirGapUI.exe`.
- If a run fails, review the BES AirGap Status window for live output and error messages.
- If an unexpected error occurs, review `BESAirGapUI-crash.log` in the application folder.
- If `F - Create Filter List` is selected, confirm only one site uses `F`.
- If download output is missing, check the `Downloads` folder and the manual list popup.

## Recommended Practices

- Keep the application and required runtime files together in one approved folder.
- Review selected site options before clicking `Run Airgap Tool`.
- Use debug mode only when troubleshooting is required.
- Do not delete databases unless you intend to reset stored `Airgap.db` state.
- Preserve `LOGS` output when troubleshooting failed runs.
