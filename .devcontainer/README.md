# Devcontainer & renv Setup Guide

This document outlines the troubleshooting steps and fixes applied to the `devcontainer.json` and `Dockerfile` to handle R version updates and workspace mounting. Reference this guide when updating R or moving the project to a new host machine.

## 1. Handling R Version Mismatches (`renv.lock`)
**The Issue:** `renv::restore()` halts the container build if the R version installed by the `Dockerfile` (e.g., pulling the latest from `jammy-cran40`) does not exactly match the version recorded in `renv.lock`.
**The Fix Applied:** 
*   Added `"RENV_CONFIG_R_VERSION_CHECK": "false"` to the `remoteEnv` section of `devcontainer.json`.
*   Added `prompt = FALSE` to `renv::restore()` in the `postCreateCommand`.
**Future Prevention:** When you intentionally upgrade R, let the container build using these bypassed checks. Once inside the container, test that your scripts run properly on the new R version, and then run `renv::snapshot()` to update `renv.lock` to reflect the new version.

## 2. Docker Mount Failures
**The Issue:** The container crashes on startup with `mkdir E:\: The system cannot find the path specified`.
**The Fix Applied:** Removed missing hard drives (`E:/`, `F:/`) from the `mounts` array in `devcontainer.json`.
**Future Prevention:** Only mount local drives that are physically present, plugged in, and accessible on the host machine running Docker.

## 3. R Language Server `00LOCK` Errors
**The Issue:** The R Language Server fails to start, displaying `ERROR: failed to lock directory .../00LOCK-renv`.
**The Fix Applied:** Deleted the leftover lock folder created by a previously interrupted installation.
**Future Prevention:** If a devcontainer rebuild or `renv` installation gets interrupted abruptly in the future, run the following command in the terminal to clear the lock before restarting the R Language Server:

```bash
rm -rf /workspaces/boredengineer-quarto-website/renv/library/linux-ubuntu-jammy/R-*/x86_64-pc-linux-gnu/00LOCK-*
```
