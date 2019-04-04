---
title: Szyfruj poświadczenia usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaszyfrować i zapisać poświadczenia na potrzeby swoich lokalnych magazynów danych na maszynie z własnego środowiska integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8e705a4430f6ccee847dc7d41ef80456a6dc4ea5
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903798"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Szyfruj poświadczenia dla lokalnych magazynów danych w usłudze Azure Data Factory
Można zaszyfrować i przechowywać poświadczenia dla swoich magazynów danych w środowisku lokalnym (połączone usługi z poufnych informacji) na maszynie z własnego środowiska integration runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przekaż plik definicji JSON przy użyciu poświadczeń na <br/>[**Nowe AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) polecenia cmdlet, aby wygenerować plik danych wyjściowych JSON definicji z zaszyfrowanymi poświadczeniami. Następnie użyj zaktualizowanych definicji JSON do utworzenia połączonych usług.

## <a name="author-sql-server-linked-service"></a>Tworzenie połączonej usługi SQL Server
Utwórz plik JSON o nazwie **C:\adfv2tutorial** w dowolnym folderze o następującej zawartości:  

Zastąp `<servername>`, `<databasename>`, `<username>`, i `<password>` wartościami dla programu SQL Server przed zapisaniem pliku. I Zastąp `<integration runtime name>` nazwą Twojego środowiska integration Runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Szyfruj poświadczenia
Aby zaszyfrować poufnych danych z ładunku w formacie JSON w środowisku lokalnym własnego środowiska integration runtime, uruchom **New AzDataFactoryV2LinkedServiceEncryptedCredential**i przekazywać ładunek w formacie JSON. To polecenie cmdlet gwarantuje, że poświadczenia są szyfrowane przy użyciu interfejsu DPAPI i przechowywane na samodzielnie hostowany węzeł środowiska integration runtime lokalnie. Ładunek danych wyjściowych, zawierający odwołanie zaszyfrowane poświadczenia mogą zostać przekierowane do innego pliku JSON (w tym przypadku "encryptedLinkedService.json").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Za pomocą pliku JSON za pomocą zaszyfrowanych poświadczeń
Teraz Użyj wyjściowego pliku JSON z poprzedniego polecenia zawierające zaszyfrowanych poświadczeń do skonfigurowania **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o zagadnieniach dotyczących zabezpieczeń w przypadku przenoszenia danych, zobacz [zagadnienia dotyczące zabezpieczeń przenoszenia danych](data-movement-security-considerations.md).

