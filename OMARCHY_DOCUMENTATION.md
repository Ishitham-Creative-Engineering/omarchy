# Omarchy Project Documentation

### 1. Project Overview

Omarchy is a comprehensive, opinionated framework for installing, managing, and maintaining a complete Linux desktop environment. It is built for power users, developers, and Arch Linux enthusiasts who desire a highly automated, consistent, and aesthetically pleasing workspace.

The core of the project is a vast collection of shell scripts that abstract away complex configurations and commands into simple, memorable actions. The environment is centered around a modern Wayland stack, with **Hyprland** as the window compositor and **Waybar** as the status bar.

**Primary Goals:**
*   **Automation:** Automate the entire setup process from a bare Arch Linux install to a fully configured desktop.
*   **Centralized Management:** Manage all application configurations ("dotfiles"), themes, and system settings from a single Git repository.
*   **Simplicity through Abstraction:** Provide simple, high-level commands (e.g., `omarchy-theme-set`, `omarchy-pkg-add`) for tasks that would normally require multiple steps.
*   **Aesthetic Cohesion:** A powerful theming engine ensures a consistent look and feel across the entire system, from the terminal to the browser.
*   **Robust Updates:** A built-in migration system allows the framework itself to be updated without breaking the user's existing setup.

---

### 2. Directory Structure Breakdown

#### `bin/` - The Command Hub
This is the heart of Omarchy's functionality. It contains a large number of executable scripts, each performing a specific action. These scripts are intended to be called directly by the user or, more commonly, bound to keyboard shortcuts in the Hyprland configuration. They are prefixed with `omarchy-` for clear namespacing.

The scripts can be categorized as follows:

*   **Commands (`omarchy-cmd-*`):** User-facing actions for daily use.
    *   `omarchy-cmd-screenshot`: Takes a screenshot.
    *   `omarchy-cmd-audio-switch`: Switches between audio outputs.
    *   `omarchy-cmd-close-all-windows`: A script to close all open application windows.

*   **Refreshers (`omarchy-refresh-*`):** Scripts that apply configuration changes. They likely read from the `config/` directory and update the live configuration for a specific application.
    *   `omarchy-refresh-hyprland`: Reloads the Hyprland configuration.
    *   `omarchy-refresh-waybar`: Reloads the Waybar style and configuration.
    *   `omarchy-refresh-config`: A master script to refresh all configurations.

*   **Package Management (`omarchy-pkg-*`):** An abstraction layer over Arch Linux's package manager (`pacman`) and likely an AUR (Arch User Repository) helper.
    *   `omarchy-pkg-add`: Installs a new package.
    *   `omarchy-pkg-remove`: Removes an existing package.
    *   `omarchy-pkg-missing`: Lists packages defined in the configuration that are not currently installed.

*   **Theming Engine (`omarchy-theme-*`):** The core of the theming system.
    *   `omarchy-theme-list`: Lists all available themes from the `themes/` directory.
    *   `omarchy-theme-set`: Applies a chosen theme to all managed applications.
    *   `omarchy-theme-set-vscode`, `...-browser`, etc.: Sub-scripts called by `omarchy-theme-set` to apply theme details to specific applications.

*   **Launchers (`omarchy-launch-*`):** Intelligent application launchers.
    *   `omarchy-launch-or-focus`: A powerful script that will either launch an application if it's not running or focus it if it's already open on another workspace.
    *   `omarchy-launch-browser`, `...-editor`: Pre-configured launchers for the user's preferred applications.

*   **Updates & Migrations (`omarchy-update-*`, `omarchy-migrate`):** System for keeping both the system packages and the Omarchy framework itself up-to-date.
    *   `omarchy-update`: The main update script.
    *   `omarchy-update-system-pkgs`: Updates all system packages.
    *   `omarchy-migrate`: Runs any pending migration scripts from the `migrations/` directory.

*   **Installation & Setup (`omarchy-install-*`, `omarchy-setup-*`):** Scripts for installing larger applications or configuring system services.
    *   `omarchy-install-steam`: Installs and configures Steam.
    *   `omarchy-setup-fingerprint`: Guides the user through setting up fingerprint authentication.

#### `config/` - The Active Configuration
This directory contains the "live" dotfiles for the system. When a script like `omarchy-refresh-hyprland` is run, it's applying the configuration found in `config/hypr/`. This directory is meant to be modified by the user and the Omarchy theming scripts.

#### `default/` - The Baseline Configuration
This directory holds the default, clean-slate configurations. It serves two purposes:
1.  As a template during the initial installation.
2.  As a reference point for users who want to revert their custom changes back to the Omarchy defaults.

#### `install/` - The Bootstrapping System
This directory contains everything needed to set up a new machine with Omarchy.
*   `iso.sh`: Likely a script to prepare a bootable Arch Linux ISO with Omarchy pre-loaded.
*   `omarchy-base.packages`, `omarchy-other.packages`: Text files listing all the packages to be installed. This creates a declarative and repeatable system.
*   `packages.ignored`, `packages.pinned`: Files to manage packages that should be ignored during updates or pinned to a specific version.
*   Subdirectories like `preflight/`, `post-install/`, `login/` contain scripts that are run at different stages of the installation process.

#### `themes/` - The Aesthetics Repository
This is where the visual identity of the system lives. Each subdirectory within `themes/` represents a complete theme (e.g., `gruvbox`, `nord`, `catppuccin`). Inside each theme directory are likely configuration snippets, color palettes, and wallpaper files that the `omarchy-theme-set` script uses to overwrite the relevant files in the `config/` directory.

#### `migrations/` - The Evolution Engine
This is a crucial directory for long-term stability. When a change is made to the Omarchy framework that would break an existing user's configuration (e.g., renaming a script, changing a config file's structure), a new script is added here. The script is named with a timestamp (e.g., `1752091783.sh`).

The `omarchy-migrate` command will check the timestamp of the last migration it ran and execute any new scripts in chronological order. This ensures that a user can safely `git pull` the latest version of Omarchy and have their system automatically adapt to the changes.

#### Root Directory Files
*   `install.sh`: The main entry point for installing Omarchy on a new system.
*   `boot.sh`: Potentially a script used during the initial boot of a freshly installed system.
*   `README.md`: Project documentation.
*   `LICENSE`: The project's software license.

---

### 3. Key User Workflows

*   **Initial Installation:**
    1.  Clone the Omarchy repository onto a bare Arch Linux installation.
    2.  Run the main `./install.sh` script.
    3.  The script will install all packages from the `.packages` lists, copy the `default/` configs to the `config/` directory, and run all post-installation setup scripts.

*   **Changing a Theme:**
    1.  User runs `omarchy-theme-list` to see available options.
    2.  User runs `omarchy-theme-set gruvbox`.
    3.  This script goes into `themes/gruvbox/`, reads the configuration templates and color values, and applies them to `config/hypr/`, `config/waybar/`, `config/alacritty/`, etc.
    4.  Finally, it calls all the `omarchy-refresh-*` scripts to make the changes live without needing a restart.

*   **Updating the System:**
    1.  User runs `omarchy-update`.
    2.  This master script first runs `omarchy-update-git` to pull the latest changes to the framework.
    3.  It then runs `omarchy-migrate` to apply any necessary configuration migrations.
    4.  Finally, it runs `omarchy-update-system-pkgs` to update all system packages.

---

### 4. Conclusion

Omarchy is not just a collection of dotfiles; it is a holistic **desktop experience management system**. It provides a powerful, stable, and highly customized environment that is both easy to use for daily tasks and incredibly robust to manage and update. Its true strength lies in its deep integration and the abstraction layers that hide complexity, allowing the user to focus on their work while enjoying a beautiful and consistent interface.
