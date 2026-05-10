###  Step 1: Create & Set Up Azure VM for Oracle Database (IaaS)
-	Create VM: Go to Azure Portal → Create a Resource → Virtual Machine.
-	Set SSH Administrator Credentials: Create username and password; this will be used to log in and manage the VM.
-	Select OS: Choose Linux (Oracle Linux / RHEL recommended) or Windows, based on Oracle support and requirements.
-	Select VM Size: Pick CPU and RAM according to your on-prem database workload and storage needs.
-	Select VM Image: Choose the OS image that fits your database requirements; this ensures compatibility with Oracle Database versions.
-	Configure Networking:
-	Set inbound rules for SSH (port 22) to manage the VM.
-	Set Oracle listener port (1521) to allow external database connections, e.g., from your local laptop.
-	Add Disks: Choose OS disk type (SSD recommended) and optionally add data disks for Oracle data files.
-	Deploy VM: Review all settings and create the VM. After deployment, you’ll have SSH

## Step2 : Data Migration
### Phase 1: Exporting your local data to a dump file
1.	Create a directory on your local machine (or Windows folder) to hold the export dump:
C:\dpump
2.	Create a directory object in Oracle pointing to that folder:
sqlplus / as sysdba
CREATE OR REPLACE DIRECTORY dp_dir AS 'C:\dpump';
GRANT READ, WRITE ON DIRECTORY dp_dir TO SYSTEM;
-	Oracle uses directory objects to know where it can read/write files.

3.	Run the export using expdp:
expdp system/****@ORCLPDB \
directory=dp_dir \
dumpfile=5tables.dmp \
logfile=5tables.log \
tables=HR.employees,HR.departments,HR.regions,HR.locations,HR.jobs,HR.countries

-	tables= → only exports the tables you want, not the entire schema.
-	dumpfile= → the name of the .dmp file that will be created.
-	logfile= → tracks what’s happening during export.

### Phase 2: Moving the dump file to Azure VM & loading into PDB
1.	Copy the dump file to your Azure VM (SSH + SCP):
scp C:\dpump\5tables.dmp username@YourVM_IP:/home/oracle/dpump/
•	username → your Linux user (e.g., Jerrydb)
•	/home/oracle/dpump/ → folder in VM where Oracle can access the file.
•	Make sure permissions are correct:
sudo chown -R oracle:oinstall /home/oracle/dpump
sudo chmod -R 755 /home/oracle/dpump
2.	Create Oracle directory object in Azure VM PDB (if not already):
sqlplus / as sysdba
ALTER SESSION SET CONTAINER=ORCLPDB1;  -- switch to PDB
CREATE OR REPLACE DIRECTORY dp_dir AS '/home/oracle/dpump';
GRANT READ, WRITE ON DIRECTORY dp_dir TO SYSTEM;
3.	Run the import using impdp, remapping the schema if needed:
•	impdp system/******@localhost:1521/ORCLPDB1 \
•	> directory=dp_dir \
•	> dumpfile=5tables.dmp \
•	> logfile=import.log \
•	> remap_schema=HR:JERRYDBre → moves tables from the old schema (HR) to your new Azure schema (JERRYDB).
4.	Check your import:
sqlplus JERRYDB/****@localhost:1521/ORCLPDB1
- SELECT COUNT(*) FROM EMPLOYEES;
- SELECT COUNT(*) FROM DEPARTMENTS;
