# Running Samsung Notes on Linux via a Windows VM

A complete guide to accessing your Samsung Notes (synced via Samsung Cloud) from a Linux machine, using a KVM/QEMU virtual machine running Windows 11.

---

## My Setup

- **Host OS:** Ubuntu 22.04 (Jammy Jellyfish) on Wayland
- **Hardware:** x86_64 desktop with 32+ GB RAM, NVIDIA GPU
- **Goal:** Access Samsung Notes synced from Galaxy Tab S11 Ultra and Galaxy Tab S9 FE
- **Final solution:** Windows 11 in KVM/QEMU with Samsung Notes UWP from Microsoft Store

---

## Why This Method?

Several approaches were tested and **failed** before settling on a Windows VM:

| Approach | Why it failed |
|----------|---------------|
| **Waydroid (Android container)** | Samsung Notes APK requires `com.samsung.device` shared library, which only exists on Samsung One UI ROMs. LineageOS-based Waydroid cannot provide it. |
| **Wine** | Samsung Notes for Windows is a UWP (Universal Windows Platform) app. Wine has no meaningful UWP support. |
| **Android emulators (BlueStacks, Genymotion, AVD)** | Same `com.samsung.device` problem as Waydroid, they're all AOSP-based, not Samsung One UI. |
| **OneNote sync** | Samsung is discontinuing this integration in July 2026. Also requires sync to have been previously set up. Plus tried it before and was not convinced... |
| **Samsung Notes web** | Doesn't exist (despite some incorrect claims online). |

The Windows VM approach works because the Samsung Notes UWP officially runs on any Windows 10/11 PC since May 2025.

---

## Prerequisites

- Linux host with **virtualization enabled** in BIOS (VT-x for Intel, AMD-V for AMD)
- At least **16 GB RAM** (8 GB allocated to VM)
- At least **60 GB free disk space**
- A working **Samsung account** with notes synced via Samsung Cloud
- Patience and a willingness to read logs 🤣🤣

---

## Step 1: Install KVM and Virtualization Stack

```bash
# Verify virtualization works
egrep -c '(vmx|svm)' /proc/cpuinfo  # should be > 0
sudo apt install cpu-checker -y
sudo kvm-ok  # should output "KVM acceleration can be used"

# Install KVM, libvirt, virt-manager, and OVMF (UEFI firmware)
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients \
                    bridge-utils virt-manager ovmf

# Add your user to the necessary groups
sudo usermod -aG libvirt,kvm $USER

# Enable libvirt service
sudo systemctl enable --now libvirtd
```

**Log out and back in** (or reboot) for the group changes to take effect.

> **Note on VirtualBox:** KVM and VirtualBox cannot use hardware acceleration simultaneously, but they can coexist. Just don't run a VirtualBox VM and a KVM VM at the same time.

---

## Step 2: Download Windows 11 ISO

Download the official ISO from Microsoft:

👉 [https://www.microsoft.com/software-download/windows11](https://www.microsoft.com/software-download/windows11)

Choose **Windows 11 (multi-edition ISO for x64 devices)** in your preferred language. The file is ~6 GB.

```bash
mkdir -p ~/VMs/iso
mv ~/Downloads/Win11_*.iso ~/VMs/iso/
```

---

## Step 3: Create the VM in virt-manager

1. Launch `virt-manager`
2. **File → New Virtual Machine** → "Local install media"
3. Browse to your Windows 11 ISO
4. RAM: **8192 MB** (8 GB), CPUs: **4**
5. Disk size: **64 GB**
6. Name: `win11-samsung-notes`
7. ✅ **Check "Customize configuration before install"** (required)

### Critical config before first boot:

**Firmware (UEFI is mandatory for Windows 11):**
- Overview → Firmware → `UEFI x86_64: /usr/share/OVMF/OVMF_CODE_4M.fd`

**TPM 2.0 (also mandatory):**
- Add Hardware → TPM → Type: Emulated, Model: TIS, Version: 2.0
- ⚠️ Make sure there's only ONE TPM device

**CPU:**
- CPUs → check "Copy host CPU configuration"

Click **Begin Installation**.

---

## Step 4: Install Windows 11

If the VM lands in a UEFI shell instead of booting the ISO:
- Force-off the VM
- View → Details → Boot Options → check CDROM, move it to top → Apply
- Restart and press a key when "Press any key to boot from CD" appears

### During install:

- **Product key:** Click "I don't have a product key" (Windows runs unactivated indefinitely; only cosmetic features are limited)
- **Edition:** Windows 11 Pro
- **Type:** Custom (not Upgrade)

### Skipping the Microsoft account requirement:

When Windows 11 demands a Microsoft account, press **Shift + F10** to open a command prompt, then run:

```cmd
start ms-cxh:localonly
```

This opens the local account creation dialog. (The older `oobe\BypassNRO` no longer works in recent Windows 11 builds.)

If Shift+F10 doesn't reach the VM, try **Send Key** menu in virt-manager.

### Disable telemetry/Cortana/etc.

Decline everything during the privacy questions (you can re-enable later if you want).

---

## Step 5: Install SPICE Guest Tools (clipboard, drag-and-drop)

Once on the Windows desktop, in Edge inside the VM, download:

👉 [https://www.spice-space.org/download/binaries/spice-guest-tools/spice-guest-tools-latest.exe](https://www.spice-space.org/download/binaries/spice-guest-tools/spice-guest-tools-latest.exe)

Run the installer. Reboot Windows.

After reboot:
- Bidirectional clipboard (Ctrl+C/V between host and VM) works
- Drag-and-drop files from host to VM works
- Auto-resize when you resize the virt-manager window

In virt-manager: **View → Scale Display → Auto resize VM with window**

---

## Step 6: Spoof Galaxy Book SMBIOS (the critical step)

This is what makes Samsung Account work. Without it, Samsung Account fails with `Catastrophic failure (HRESULT 0x8000FFFF)` because the app tries to read a hardware ID that doesn't exist on a generic QEMU system.

**Shut down the VM cleanly**, then on the Linux host:

```bash
virsh --connect qemu:///system edit win11-samsung-notes
```

(Replace `win11-samsung-notes` with your VM name. Check with `virsh list --all` if unsure.)

### Modify the `<os>` block:

Add `<smbios mode='sysinfo'/>` inside it:

```xml
<os>
  <type arch='x86_64' machine='pc-q35-...'>hvm</type>
  <loader .../>
  <nvram>...</nvram>
  <boot dev='hd'/>
  <smbios mode='sysinfo'/>
</os>
```

### Add the `<sysinfo>` block right after `</os>`:

```xml
<sysinfo type='smbios'>
  <bios>
    <entry name='vendor'>American Megatrends Inc.</entry>
    <entry name='version'>P11RGF.057.230927.JJ</entry>
    <entry name='date'>09/27/2023</entry>
  </bios>
  <system>
    <entry name='manufacturer'>Samsung Electronics Co., Ltd.</entry>
    <entry name='product'>NP960XFG-KC4UK</entry>
    <entry name='version'>P11RGF</entry>
    <entry name='serial'>GBFA12345678</entry>
    <entry name='sku'>SCAI-MWB14-DECEMBER22</entry>
    <entry name='family'>Galaxy Book4 Pro</entry>
  </system>
  <baseBoard>
    <entry name='manufacturer'>Samsung Electronics Co., Ltd.</entry>
    <entry name='product'>NP960XFG-KC4UK</entry>
    <entry name='version'>P11RGF</entry>
    <entry name='serial'>GBFA12345678</entry>
  </baseBoard>
  <chassis>
    <entry name='manufacturer'>Samsung Electronics Co., Ltd.</entry>
    <entry name='version'>P11RGF</entry>
    <entry name='serial'>GBFA12345678</entry>
    <entry name='asset'>No Asset Tag</entry>
    <entry name='sku'>Notebook</entry>
  </chassis>
</sysinfo>
```

### ⚠️ Common pitfall: UUID mismatch

**Do NOT** add a `<entry name='uuid'>` line in `<system>`. If you do, libvirt will reject the config with "UUID mismatch between domain and sysinfo" because the domain already has its own UUID at the top of the XML. Just omit the UUID entry — libvirt will use the domain UUID automatically.

Save and exit (`:wq` in vi, or Ctrl+O / Ctrl+X in nano).

---

## Step 7: Verify the SMBIOS Spoofing

Boot the VM. In Windows, open PowerShell (admin) and run:

```powershell
Get-WmiObject -Class Win32_ComputerSystem | Select-Object Manufacturer, Model
Get-ItemProperty -Path "HKLM:\HARDWARE\DESCRIPTION\System\BIOS" |
    Select-Object SystemManufacturer, SystemProductName, BaseBoardManufacturer, BaseBoardProduct
```

You should see:
- Manufacturer: **Samsung Electronics Co., Ltd.**
- Model: **NP960XFG-KC4UK**

These values now persist across reboots because they come from the actual virtual firmware, not just registry tweaks.

---

## Step 8: Install Samsung Account and Samsung Notes

In the VM, open **Microsoft Store**:

1. Search for "Samsung Account" → Install
2. Search for "Samsung Notes" → Install

---

## Step 9: Sign In

Launch **Samsung Account** (or directly Samsung Notes). Click **Sign in**.

You should now be redirected to a working Samsung login page where you can enter your credentials.

Once signed in, your notes will sync down from Samsung Cloud automatically. The first sync may take a few minutes depending on how many notes you have.

---

## Troubleshooting

### `Something went wrong. Try again later.` instantly when clicking Sign in

This is the symptom that requires the SMBIOS spoofing from Step 6. Verify:

1. The Samsung Account log:
   ```powershell
   Get-Content "$env:LOCALAPPDATA\Packages\SAMSUNGELECTRONICSCO.LTD.SamsungAccount_*\LocalState\logs\SamsungAccount_*.log" -Tail 100
   ```
2. Look for: `Failed during URL (startUri & endUri) generation: Catastrophic failure (Exception from HRESULT: 0x8000FFFF)`
3. If present → Step 6 was incomplete or didn't apply.

### apt install hangs / no internet during package downloads

If you have a VPN client running on the host (e.g., NordVPN with the `nordlynx` interface), it may break routing for `apt`. Disconnect the VPN, install KVM, then reconnect.

### iptables rules dropped after reboot (Docker conflict)

If you have Docker installed, it may rewrite the FORWARD chain to DROP, which can break VM networking later. Quick fix:
```bash
sudo iptables -P FORWARD ACCEPT
```

For a permanent solution, use `iptables-persistent`.

### VM is slow

- Edit VM config: change disk bus to **virtio**, network to **virtio**
- CPU mode: **host-passthrough** (instead of generic CPU)
- Allocate more vCPUs (4-6 is fine on a modern host)

### Display doesn't auto-resize

- Make sure SPICE is selected in virt-manager (not VNC)
- View → Scale Display → Auto resize VM with window
- Verify QXL or virtio video driver is loaded in Windows Device Manager

---

## Maintenance Notes

- **Windows unactivated:** The VM works fine without a Windows license. Cosmetic limitations only (watermark, no theme customization). No forced reboots or time limit.
- **VM startup:** The VM doesn't start automatically. Open virt-manager and start it when you need Samsung Notes.
- **Samsung updates:** Samsung occasionally tightens detection on their server. If sign-in stops working in the future, check the log file for new error patterns and adjust SMBIOS values accordingly.

---

## Credits

This solution emerged from a long debugging session. 

Special thanks to the kellwinr/galaxybook_mask repository (now archived) for the original concept of presenting a Windows machine as a Galaxy Book (although the registry-only approach doesn't survive reboots in a VM), leading to the SMBIOS-level spoofing documented here.

The breakthrough insight: Samsung Account fails before any network call when `hardwareID` is empty. The SMBIOS sysinfo block populates it correctly, allowing the URL builder to succeed.