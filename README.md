# ETE-Oracle-Database-Migration-from-On-Premises-to-Azure-Virtual-Machine
Performed end-to-end Oracle database migration from on-prem to Azure VM using Data Pump utilities. Ensured reliable data transfer and consistency through validation checks.

##  Project Overview

This project demonstrates the migration of an Oracle database from an on-premises environment to an Azure Virtual Machine (VM). The migration follows a lift-and-shift approach, where the database is moved with minimal changes to ensure consistency and reliability.

The solution uses Oracle Data Pump utilities to export data from the source system and import it into the target Oracle database hosted on Azure VM.

##  Objective

- To migrate Oracle database tables from on-premises to Azure VM
- To ensure data consistency during migration
- To simulate a real-world enterprise database migration scenario
- To validate migrated data in the target environment

## Architecture Diagram



##  Key Features

- Oracle database setup using DBCA in both source and target environments
- Data export using Data Pump (expdp)
- Data import using Data Pump (impdp)
- Directory object configuration for dump file handling
- Data validation after migration

 
