## <a name="next-steps"></a>Kolejne kroki

Po włączeniu integracji magazynu kluczy Azure, programu SQL Server szyfrowanie można włączyć na maszynie Wirtualnej SQL. Najpierw należy utworzyć klucz asymetryczny wewnątrz magazynu kluczy oraz klucz symetryczny w programie SQL Server na maszynie Wirtualnej. Następnie można wykonać instrukcje T-SQL, aby włączyć szyfrowanie dla baz danych i tworzenie kopii zapasowych.

Istnieje kilka metod można korzystać z szyfrowania:

* [Przezroczystego szyfrowania danych (funkcji TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Zaszyfrowanej kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx)
* [Szyfrowanie na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Następujące skrypty języka Transact-SQL zawierają przykłady dla każdego z tych obszarów.

### <a name="prerequisites-for-examples"></a>Wymagania wstępne dotyczące przykłady

Każdy przykład jest oparty na dwóch wymagań wstępnych: klucza asymetrycznego z magazynu kluczy o nazwie **CONTOSO_KEY** i wywołać poświadczenie utworzona przez funkcję Integracja **Azure_EKM_TDE_cred**. Następujące polecenia języka Transact-SQL, należy skonfigurować te wymagania wstępne dotyczące uruchamiania przykładów.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Przezroczystego szyfrowania danych (funkcji TDE)

1. Utwórz dane logowania programu SQL Server używanego przez aparat bazy danych dla funkcji TDE, a następnie Dodaj poświadczenia do niej.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Utwórz klucz szyfrowania bazy danych, który będzie używany dla funkcji TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Zaszyfrowanej kopii zapasowych

1. Utwórz dane logowania programu SQL Server używanego przez aparat bazy danych do szyfrowania kopii zapasowych, a następnie Dodaj poświadczenia do niej.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Kopia zapasowa szyfrowania Określanie bazy danych przy użyciu klucza asymetrycznego przechowywane w magazynie kluczy.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Szyfrowanie na poziomie kolumny (CLE)

Ten skrypt tworzy klucza symetrycznego chronione za pomocą klucza asymetrycznego w magazynie kluczy, a następnie używa klucza symetrycznego do szyfrowania danych w bazie danych.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji na temat sposobu korzystania z tych funkcji szyfrowania, zobacz [EKM korzystanie z funkcji programu SQL Server szyfrowania](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Należy pamiętać, że kroki opisane w tym artykule założono, że już istnieje na serwerze SQL działa na maszynie wirtualnej platformy Azure. Jeśli nie, zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server na platformie Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Aby inne programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
