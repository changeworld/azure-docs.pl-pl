---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183181"
---
## <a name="next-steps"></a>Następne kroki

Po włączeniu integracji usługi Azure Key Vault można włączyć szyfrowanie programu SQL Server na maszynie wirtualnej SQL. Najpierw należy utworzyć klucz asymetryczny wewnątrz magazynu kluczy i klucz symetryczny w programie SQL Server na maszynie wirtualnej. Następnie będzie można wykonać instrukcje T-SQL, aby włączyć szyfrowanie baz danych i kopii zapasowych.

Istnieje kilka form szyfrowania, z których można skorzystać:

* [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Zaszyfrowane kopie zapasowe](https://msdn.microsoft.com/library/dn449489.aspx)
* [Szyfrowanie poziomu kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Następujące skrypty Transact-SQL zawierają przykłady dla każdego z tych obszarów.

### <a name="prerequisites-for-examples"></a>Wymagania wstępne dla przykładów

Każdy przykład jest oparty na dwóch wymaganiach wstępnych: klucz asymetryczny z magazynu kluczy o nazwie **CONTOSO_KEY** i poświadczenie utworzone przez funkcję integracji AKV o nazwie **Azure_EKM_TDE_cred**. Następujące polecenia Transact-SQL konfigurują te wymagania wstępne dotyczące uruchamiania przykładów.

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

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Utwórz sql server logowania do użycia przez aparat bazy danych dla TDE, a następnie dodać poświadczenia do niego.

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

1. Utwórz klucz szyfrowania bazy danych, który będzie używany dla TDE.

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

### <a name="encrypted-backups"></a>Zaszyfrowane kopie zapasowe

1. Utwórz sql server logowania do użycia przez aparat baz danych do szyfrowania kopii zapasowych i dodać poświadczenia do niego.

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

1. Utwórz kopię zapasową bazy danych, określając szyfrowanie za pomocą klucza asymetrycznego przechowywanego w magazynie kluczy.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Szyfrowanie poziomu kolumny (CLE)

Ten skrypt tworzy klucz symetryczny chroniony przez klucz asymetryczny w magazynie kluczy, a następnie używa klucza symetrycznego do szyfrowania danych w bazie danych.

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

Aby uzyskać więcej informacji na temat korzystania z tych funkcji szyfrowania, zobacz [Korzystanie z EKM z funkcjami szyfrowania programu SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Należy zauważyć, że kroki opisane w tym artykule zakładają, że masz już program SQL Server uruchomiony na maszynie wirtualnej platformy Azure. Jeśli nie, zobacz [Aprowizuj maszynę wirtualną programu SQL Server na platformie Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Aby uzyskać inne wskazówki dotyczące uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
