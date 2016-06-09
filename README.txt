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

7. Change the wallet from auto-login to password type.
    a. Find the SSO file location using:
    select * from v$encryption_wallet;
    b. Remove the file cwallet.ssh from the location above.
    c. Close the wallet using:
    administer key management set keystore close;
    d. Re-open the wallet using:
    administer key management set keystore open identified by "Welcome1#";
    e. Verify that the wallet type is now "PASSWORD" using:
    select * from v$encryption_wallet;
    
8. Import the included encryption key into the PDB:
ALTER SESSION SET CONTAINER=TEST;
ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY "Welcome1#"; 
ADMINISTER KEY MANAGEMENT IMPORT KEYS WITH SECRET "Welcome1#" 
FROM '/u01/app/oracle/admin/ORCL/tde_wallet/pdb1.exp' IDENTIFIED BY "Welcome1#" WITH BACKUP;

9. Check the status of the PDB
select pdb_name, status from CDB_PDBS;  
select name, open_mode from V$PDBS;    
