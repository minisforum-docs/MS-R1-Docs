# MS-R1 — Debug Commands

A concise reference of debugging commands commonly used when troubleshooting MS-R1 devices.

## Table of contents
- [GPU](#gpu)
- [VPU (Video Processing Unit)](#vpu-video-processing-unit)
- [Notes](#notes)

## GPU

Show GPU utilization (clock / tracing information):

Know issues: When GPU not using, the percent number will be freeze.

```bash
gpu_utilization_clock_tracing
```

## VPU (Video Processing Unit)

If video encoding/decoding using VPU, this will show a lot of logs:

```bash
mvx_logd --follow
```

## Notes

- Ensure you have the necessary permissions(root) to run these commands.
- Use these commands for debugging purposes only.