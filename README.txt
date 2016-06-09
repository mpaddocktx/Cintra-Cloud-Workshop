The key passphrase is Cintra21

1. Once the database server is up and running download the PDB datafiles while on the server command line.
wget https://www.dropbox.com/s/12jd18cpnpxoos8/pdb1.zip?dl=0

2. Unzip the datafiles into an empty directory - e.g. /u02/app/oracle/oradata/ORCL/TEST/datafile

3. Move the pdb1.xml file into the $ORACLE_HOME/dbs

4. Check compatibility in the source database
SET SERVEROUTPUT ON
DECLARE
  compatible CONSTANT VARCHAR2(3) := 
    CASE DBMS_PDB.CHECK_PLUG_COMPATIBILITY(
           pdb_descr_file => '/u01/app/oracle/product/12.1.0/dbhome_1/dbs/pdb1.xml',
           pdb_name       => 'pdb1')
    WHEN TRUE THEN 'YES'
    ELSE 'NO'
END;
BEGIN
  DBMS_OUTPUT.PUT_LINE(compatible);
END;
/

5. Plug the database in
CREATE PLUGGABLE DATABASE test USING '/u01/app/oracle/product/12.1.0/dbhome_1/dbs/pdb1.xml' 
create_file_dest='/u02/app/oracle/oradata/ORCL/TEST'
source_file_name_convert=('/u02/app/oracle/oradata/ORCL/34A31D2002E41E89E053DE4EC40A8291','/u02/app/oracle/oradata/ORCL/TEST')
NOCOPY
TEMPFILE REUSE;
  
6. Open the pluggable database
alter pluggable database test open;  

7. Check the status of the PDB
select pdb_name, status from CDB_PDBS;  
select name, open_mode from V$PDBS;    
