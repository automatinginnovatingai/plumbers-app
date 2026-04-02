# Plumbers App – Architecture Overview

## Overview
Plumbers App is a closed‑source Windows `.exe` application built for offline operation, secure license enforcement, and automated reporting. It provides a fast, local Operational CRM tailored specifically for plumbing production workflows. Unlike cloud‑based CRMs, the system runs entirely on the user’s machine or internal network, ensuring reliability, data ownership, and zero external dependencies.

The application supports both SQL Server Express and Full SQL Server, selected during installation.

---

# Architecture

## Database Selection (Installer‑Driven)
During installation, the user selects one of two supported database modes:

### 1. SQL Server Express (Local Database – Recommended for 1–5 Users)
- Installer automatically installs and configures SQL Server Express
- Ideal for single‑machine setups or small office networks
- No licensing cost

### 2. Full SQL Server (Standard/Enterprise)
- User connects to an existing SQL Server instance
- Supports multi‑user environments and IT‑managed deployments
- Designed for medium to large companies

The installer writes a registry flag (`UseSQLExpress`) that determines which connection setup page the application loads on first launch.

---

## License Enforcement Flow
User Launch → License Prompt → Gumroad API Validation → Local Unlock

- User enters their Gumroad license key on first launch
- Key is validated through Gumroad’s `/v2/licenses/verify` endpoint
- Valid keys unlock the application and store the license state locally
- Periodic revalidation may occur (e.g., every 7 days)
- License tier determines access to Basic, Pro, or Enterprise features

---

## Local Session Context
- All data is stored in SQL Server Express or Full SQL Server
- No cloud sync, Redis, or external session store
- Secure login using salted + hashed admin credentials
- Fully offline operation after initial license activation

---

## Data Transfer Between Computers
The app includes a built‑in SQL migration tool for moving data to a new machine:

- Creates a `.bak` backup file from the old computer
- Restores the `.bak` file on the new computer
- Works for both SQL Express and Full SQL Server
- Ideal for machine upgrades or office migrations

---

## SQL Connection Pages
The application routes users to the correct connection setup page based on installer selection:

- `sql_connection_page.py` – SQL Express
- `sql_server_connection_page.py` – Full SQL Server

Both pages support:
- Connection testing
- Credential validation
- Database creation (if needed)
- Secure admin login setup

---

## Payroll & Work Ticket Workflow
Admin Login → Work Ticket Entry → Payroll Calculation → Excel Export

- Admin enters job details, employee info, and material usage
- Payroll supports multiple calculation methods:
  - Fixture count × rate (e.g., toilets, sinks, water heaters)
  - Linear feet of pipe × rate
  - Hourly wage
  - Flat rate per job
- Pay is automatically split across employees per job
- Excel payroll reports are generated two days after the pay period ends

---

## File Storage & Export
All reports are stored locally under:

Documents/AIAI_PM_App/Payroll_Excels/YYYY-MM/Payroll_YYYY-MM.xlsx

- Each employee receives a dedicated worksheet labeled by week
- Reports are grouped by month
- Pro and Enterprise tiers support enhanced export customization

---

# Feature Tiers

## Basic
- Core plumbing production‑management tools
- Supports multiple admin accounts (Admin Add‑on required)
- Limited export customization
- Automatic database cleanup

## Pro
- All Basic features
- Enhanced Excel exports
- Prepopulated database fields for:
  - Materials
  - Employees
  - Builders
- Faster data entry with structured dropdowns

## Enterprise
- All Pro features
- Installer login functionality
- Daily production input by installers
- Separate login pages for admins and installers
- Custom reporting templates
- Priority support
- Multiple installer accounts (Installer Add‑on required)

---

## Update System
The application includes a built‑in update mechanism:

- Checks GitHub for the latest version
- Downloads update packages (`plumbers_app_update.zip`)
- Automatically backs up the current installation
- Rolls back automatically if an update fails
- Uses a unified `version_info.json` structure

---

## Security
- Salted + hashed admin credentials
- Encrypted local storage for license and session data
- No cloud storage or external session tracking
- Role‑based access control for admins and installers

---

# License Statement
All Rights Reserved  
Copyright © 2025 Automating Innovating AI

Plumbers App (`.exe`) is proprietary software and is not distributed via GitHub.

Unauthorized copying, distribution, reverse engineering, or modification of the software or its components is strictly prohibited.

Possession of this documentation does not grant any rights to use, reproduce, or distribute the Plumbers App.

The software is provided “AS IS”, without warranty of any kind, express or implied.

For access to the app, visit:  
https://automatinginnovatingai.wordpress.com

For support, contact:  
automatinginnovatingai@outlook.com