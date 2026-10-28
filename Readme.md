PyOdin Web 2.0.1

An experimental, browser-based Samsung firmware flashing build with enhanced USB handling, streaming LZ4 decompression, protocol-aware transfers, and improved diagnostics.

PyOdin Web 2.0.1 is a modified version of PyOdin Web focused on improving firmware-transfer reliability and making failures easier to investigate when communicating with compatible Samsung devices through WebUSB.

The project includes changes to USB data handling, large compressed firmware transfers, LZ4 decompression validation, transfer logging, and PIT partition matching.

«Status: Experimental. The changes require further testing across devices, firmware packages, browsers, and protocol versions.»

Key Features

1. Improved USB communication

- Added chunked USB writes for payloads larger than 64 KiB.
- Improved logging of USB write sizes and partial-write conditions.
- Added visibility into USB responses and transfer progress.
- Improved diagnostics for communication and transfer failures.

2. Protocol-aware packet sizing

- Configures transfer packet sizes according to the negotiated protocol version.
- Uses protocol-dependent transfer settings rather than assuming every device supports the same packet size.
- Logs the selected packet size to help diagnose compatibility issues.

3. Large firmware-file streaming

Large compressed firmware images can require substantial memory when extracted into a single buffer. The modified transfer engine supports a streaming path for large LZ4-compressed files.

- Uses streaming transfer logic for qualifying large compressed images.
- Processes firmware data in manageable chunks instead of requiring the complete decompressed image to remain in memory.
- Helps reduce memory pressure when transferring large partitions.
- Improves compatibility with browser environments that have limited available memory.

Streaming behavior and transfer acknowledgements still require testing against compatible devices and known-good protocol implementations.

4. LZ4 decoder validation

The modified streaming LZ4 implementation includes additional bounds and input validation.

- Checks compressed-input and decompressed-output boundaries.
- Validates literal and match lengths.
- Rejects invalid match offsets.
- Checks for truncated or malformed block data.
- Adds safeguards against invalid memory-position calculations.

The custom decoder was tested against a large compressed firmware image, and its decompressed output matched the output produced by the standard LZ4 command-line decoder in the tested case.

This result validates that test image only; it does not establish compatibility with every LZ4 frame or firmware package.

5. Firmware-transfer diagnostics

Expanded logging helps identify where an operation fails, including:

- Transfer initialization and protocol negotiation
- Firmware filenames and partition information
- Block numbers and transfer sizes
- USB write results
- Response lengths and device responses
- Transfer finalization
- Completion and failure messages

These diagnostics help distinguish host-side USB or memory problems from device-side rejection.

6. PIT partition matching

- Improves handling of PIT entries during applicable firmware-transfer matching.
- Uses stricter matching for non-PIT partitions.
- Avoids silently assigning a default partition type or identifier when a match cannot be established.
- Provides more diagnostic information for partition-matching failures.

These changes do not replace validation of the PIT, firmware compatibility, or device-specific flashing requirements.

7. Source-change backups

Backup copies of modified JavaScript files were retained during development to support comparison, debugging, and recovery of source changes.

Backup files are development artifacts and are not required for normal operation.

---

Installation

PyOdin Web runs in a compatible browser environment. USB flashing requires a supported browser, WebUSB access, and a compatible USB connection.

Option A — Download the project from GitHub

1. Open the project's GitHub repository.
2. Select Code → Download ZIP.
3. Extract the ZIP archive.
4. Open the project directory and follow any existing project-specific setup instructions.
5. Serve the project using a compatible local web server or host it on a secure origin that supports WebUSB.

Important: WebUSB generally requires a secure context, such as HTTPS or an eligible localhost origin. Opening "index.html" directly as a local file may not provide the required browser capabilities.

Option B — Install and run from Termux

Install the required tools:

pkg update
pkg install git python

Clone the repository:

git clone https://github.com/pyodinweb/pyodinweb.git
cd pyodinweb

If you are using the modified 2.0.1 source, make sure you obtain the correct branch, tag, or archive containing those modifications. Cloning the upstream repository alone does not guarantee that it contains this modified build.

Start a local HTTP server:

python -m http.server 8000

Then open this address in a compatible browser:

http://localhost:8000

WebUSB limitation: A local HTTP server does not guarantee that every Android browser supports WebUSB. If USB-device access is unavailable, use a browser and environment that support the required WebUSB functionality.

Option C — Use the archived modified build

If you have an archive named "PyOdinWeb2.0.1.tar.gz" in Android's Download folder, extract it in Termux:

mkdir -p ~/downloads/PyOdinWeb2.0.1
tar -xzf ~/storage/shared/Download/PyOdinWeb2.0.1.tar.gz \
  -C ~/downloads/PyOdinWeb2.0.1

Inspect the extracted directory:

find ~/downloads/PyOdinWeb2.0.1 -maxdepth 2 -type f

Enter the directory containing "index.html" and start the server:

cd ~/downloads/PyOdinWeb2.0.1
python -m http.server 8000

If the archive contains an extra top-level directory, change into that directory before starting the server.

---

Using PyOdin Web

1. Ensure the device model and firmware package are compatible.
2. Enable the device's appropriate Download Mode.
3. Connect the device through a supported USB connection.
4. Open PyOdin Web in a browser that supports WebUSB.
5. Select the appropriate firmware files using the application's existing interface.
6. Review the displayed device and partition information.
7. Monitor the transfer logs for USB errors, protocol responses, and completion status.

Do not disconnect the device while an operation is in progress.

A success message indicates that the application reached its completion path. It is not independent proof that every partition was accepted or that the device will boot successfully.

Current Limitations

- The modifications are experimental and have not been validated across all Samsung models.
- WebUSB support varies by browser and platform.
- USB writes can succeed even when the device rejects the data.
- Transfer responses must be interpreted according to the relevant protocol.
- Streaming transfers require further validation of acknowledgements, error handling, and recovery from interrupted operations.
- Larger packet sizes may not be supported by every device or protocol implementation.
- LZ4 validation does not guarantee that every compressed image or archive format is supported.
- PIT matching improvements do not guarantee that a firmware package is appropriate for a device.
- A successful transfer does not guarantee a successful boot.

Safety and Compatibility

Firmware flashing can erase user data, render a device unbootable, or cause other damage.

Before flashing:

- Verify the exact device model.
- Verify firmware and bootloader revision compatibility.
- Use firmware intended for the target device.
- Understand whether the selected CSC file will wipe user data.
- Keep a copy of important data and the original firmware package.
- Do not use repartitioning or PIT options unless the operation is explicitly supported and understood.

These modifications do not bypass Samsung factory-binary authorization, Knox Guard, bootloader verification, or other device-enforced security restrictions.

Troubleshooting

When reporting an issue, include:

- Device model and bootloader revision
- Android and browser versions
- Firmware build and package type
- Whether the transfer used the streaming LZ4 path
- Relevant USB and transfer logs
- The partition and stage at which the operation failed
- Whether the device subsequently booted

Avoid posting serial numbers, account credentials, or other sensitive identifiers in public issue reports.

Contributing

Contributions are welcome, especially:

- Reproducible USB-transfer tests
- Protocol documentation and verified response handling
- LZ4 decoder test cases
- Improved error reporting and recovery
- Compatibility reports from supported devices
- Tests for large firmware images and interrupted transfers

Please include reproducible steps and relevant logs with bug reports.

Disclaimer

PyOdin Web 2.0.1 is an independently modified, experimental project. It is not an official Samsung product, and no affiliation with or endorsement by Samsung is implied.

Use at your own risk. The authors make no guarantee that firmware flashing will succeed or that a device will remain functional.

Project goal: Improve USB communication, large-file handling, transfer observability, and partition-matching diagnostics while making failures easier to reproduce and investigate.
