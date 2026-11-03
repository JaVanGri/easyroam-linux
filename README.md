# easyroam-linux

Set up eduroam with [easyroam](https://www.easyroam.de) (DFN) on Linux – without the official app.

> [!NOTE]
> This is a fork of **[jahtz/easyroam-linux](https://github.com/jahtz/easyroam-linux)** by [Janik Haitz](https://github.com/jahtz), who wrote the original scripts. All credit for the idea and the base goes to him.
> The improvements in this fork have been proposed upstream ([#9](https://github.com/jahtz/easyroam-linux/pull/9), [#10](https://github.com/jahtz/easyroam-linux/pull/10)) – if they are merged, prefer the original repository.

## Why this fork?

On my Pop!_OS 24.04 laptop the official easyroam app (Snap) did not work, so I set up eduroam manually and ended up improving jahtz's NetworkManager script along the way:

- **Server certificate validation** (`DNS:easyroam.eduroam.de`), as required by the [DFN guide](https://doku.tid.dfn.de/de:eduroam:easyroam#installation_der_easyroam_profile_auf_linux_geraeten)
- **Private key and certificates only readable by you** (`umask 077`)
- **Finds your .p12 file automatically** (next to the script, in the current directory or in `~/Downloads`)
- Paths with `~` work (e.g. `~/Downloads/laptop.p12`)
- Shows the **certificate's expiry date** and points you to easyroam.de if no certificate is found

## Usage

You can either use `easyroam.sh` to extract the certificate files and set up your network manually, or, if you have NetworkManager installed, use `easyroam_nm.sh` to set up the network automatically. The scripts do not depend on the official app.

### Obtaining your Certificate

1. Open https://www.easyroam.de
2. Click log-in and search for your university
3. Log-in through your university
4. Select `linux` in `Manual installation`
5. Enter your device name
6. Download the file by clicking on the `Ok` button

![Easyroam home page, the Linux button is circled in red](images/easyroam-home-page.png)

### Option 1: Automatic NetworkManager Setup
> [!WARNING]
> Tested on Fedora Workstation 42 (upstream) and Pop!_OS 24.04 (this fork)

> [!NOTE]
> For immutable distributions like Fedora Atomic Desktops, refer to [kimuraseki's fork](https://github.com/kimuraseki/easyroam-linux).

1. Download the script
    ```bash
    curl -o easyroam_nm.sh https://raw.githubusercontent.com/JaVanGri/easyroam-linux/main/easyroam_nm.sh
    ```
2. Make it executable:
    ```
    chmod +x easyroam_nm.sh
    ```
3. Run the setup:
    ```
    ./easyroam_nm.sh
    ```

> [!TIP]
> If the .p12 file is in the same directory as the script, the current directory or _~/Downloads_, the newest one is suggested automatically – just press Enter. If no file is found, the script points you to https://www.easyroam.de.

> [!TIP]
> To remove the generated configuration, delete the file _/etc/NetworkManager/system-connections/easyroam.nmconnection_ <br>or run: `nmcli connection delete easyroam`

> [!NOTE]
> easyroam certificates expire. The script prints the expiry date at the end. To renew, download a new .p12 file from https://www.easyroam.de and run the script again – the old connection is replaced automatically.

### Option 2: Manual Setup
This script only unpacks the PKCS12 (.p12) file for manual network configuration
> [!TIP]
> After unpacking, you can follow the official DFN guides for:
> - [netctl](https://doku.tid.dfn.de/de:eduroam:easyroam#installation_der_easyroam_profile_auf_linux_geraeten) (e.g. Arch)
> - [wpa-supplicant](https://doku.tid.dfn.de/de:eduroam:easyroam#installation_der_easyroam_profile_auf_linux_geraeten_ohne_desktop_umgebung_wpa-supplicant_only) (e.g. Pi OS Lite)

1. Download the script
    ```bash
    curl -o easyroam.sh https://raw.githubusercontent.com/JaVanGri/easyroam-linux/main/easyroam.sh
    ```
2. Make it executable:
    ```
    chmod +x easyroam.sh
    ```
3. Run the setup:
    ```
    ./easyroam.sh
    ```

Resulting files:
- `easyroam_root_ca.pem` &rarr; CA certificate
- `easyroam_client_cert.pem` &rarr; User certificate
- `easyroam_client_key.pem` &rarr; Private key

## Credits & License

- Original scripts and documentation: [jahtz/easyroam-linux](https://github.com/jahtz/easyroam-linux) by Janik Haitz
- Setup based on the [easyroam documentation of the DFN](https://doku.tid.dfn.de/de:eduroam:easyroam)
- Licensed under the [Apache License 2.0](LICENSE), like the original. `easyroam_nm.sh` and this README were modified in this fork (see commit history).
