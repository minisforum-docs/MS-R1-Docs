# ARM System Ready OS and BIOS for MS-R1

## What is ARM SystemReady?

[ARM SystemReady](https://www.arm.com/architecture/system-architectures/systemready-compliance-program) is a compliance certification program by Arm that ensures a platform boots standard operating systems and hypervisors out of the box — without vendor-specific modifications. A certified device guarantees that any compliant OS (Linux distros, hypervisors, etc.) can install and run on it just like on a standard x86 PC.

MS-R1 is an ARM SystemReady certified platform. You can verify this on the [Arm SystemReady Partners page](https://www.arm.com/architecture/system-architectures/systemready-compliance-program/partners).

This means you can use the standard ARM SystemReady BIOS and a generic ARM64 Linux filesystem on MS-R1, rather than being locked into a vendor-specific OS image.

## Downloads

The ARM SystemReady BIOS and Linux filesystem for MS-R1 are available at:

**Google Drive:** https://drive.google.com/drive/folders/1FC8yqZLZhQOBfQ_oB137jjGcnfNVM7da

| File | Description |
|------|-------------|
| `MS-R1-20260312-ARMSystemReady-BIOS.zip` | ARM SystemReady compliant BIOS firmware for MS-R1 |
| `linux-fs-20260312.7z` | Generic ARM64 Linux filesystem image |

## Important Notes

### Recommended BIOS Settings

For best performance and compatibility, disable CPU cores 2–5 after flashing the ARM SystemReady BIOS. See [Disable small cores](./MS-R1-BaseGuide.md#disable-small-cores-optimize-performance) in the base guide for instructions.

### Windows Compatibility Note

> **Note:** If you intend to use Windows on MS-R1 (not the primary target audience, but possible for development/hacking purposes), you must also set the **LPI max state to LPI0** in addition to disabling cores 2–5.
>
> This is required to satisfy Arm BSA rule **B_PE_01: Check Arch symmetry across PE**. Windows enforces strict PE symmetry and will fail to boot or behave incorrectly if this rule is violated.
>
> **Steps:**
> 1. Enter BIOS (`Del` or `Esc` during boot).
> 2. Go to `System Manager` → `SOC Configuration` → `CPU Configuration`.
> 3. Disable CPU cores `2`, `3`, `4`, and `5`.
> 4. Set **LPI max state** to `LPI0`.
> 5. Save and reboot.
>
> This setting is not required for Linux.

## Flashing the BIOS

Refer to [How To Reinstall OS](./MS-R1-How-To-Reinstall-OS.md) for the general flashing procedure. Use the `MS-R1-20260312-ARMSystemReady-BIOS.zip` file in place of the standard BIOS image.

## See Also

- [Base Guide](./MS-R1-BaseGuide.md)
- [How To Reinstall OS](./MS-R1-How-To-Reinstall-OS.md)
- [Arm SystemReady Compliance Program](https://www.arm.com/architecture/system-architectures/systemready-compliance-program)
