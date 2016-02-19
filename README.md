1. Download the zip file:
wget https://github.com/mpaddocktx/Cintra-Cloud-Workshop/archive/master.zip
	- Putty
	- Keys
	- Cloudberry
	- Notes
	
wget https://www.dropbox.com/s/j8uq1h4bpg6r5da/orcl.zip

2. Unzip the datafiles into an empty directory - /u02/app/oracle/oradata/CDB1/orcl

3. Move the orcl.xml file into the $ORACLE_HOME/dbs

2. Check compatibility in the source database
SET SERVEROUTPUT ON
DECLARE
  compatible CONSTANT VARCHAR2(3) := 
    CASE DBMS_PDB.CHECK_PLUG_COMPATIBILITY(
           pdb_descr_file => '/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orcl.xml',
           pdb_name       => 'orcl')
    WHEN TRUE THEN 'YES'
    ELSE 'NO'
END;
BEGIN
  DBMS_OUTPUT.PUT_LINE(compatible);
END;
/

3. Plug the database in
CREATE PLUGGABLE DATABASE orcl USING '/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orcl.xml' 
create_file_dest='/u02/app/oracle/oradata/CDB1/orcl'
source_file_name_convert=('/home/oracle/app/oracle/oradata/cdb1/orcl','/u02/app/oracle/oradata/CDB1/orcl')
NOCOPY
TEMPFILE REUSE;
  
4. Open the pluggable database
alter pluggable database orcl open;  

5. Check the status of the PDB
select pdb_name, status from CDB_PDBS;  
select name, open_mode from V$PDBS;    
