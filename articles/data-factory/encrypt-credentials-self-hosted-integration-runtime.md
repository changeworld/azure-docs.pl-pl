---
title: Szyfrowania poświadczeń w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szyfrowanie i przechowywanie poświadczeń dla sieci lokalnych magazynów danych na komputerze z programem obsługi integracji hostowania samoobsługowego.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 1cadcdd45e648f315e292bbc806abc9337725670
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619222"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Szyfrowania poświadczeń dla lokalnych magazynów danych w fabryce danych Azure
Można zaszyfrować i przechowywania poświadczeń dla użytkownika magazynów danych lokalnych (połączonej usługi z poufne informacje) na komputerze z programem obsługi integracji hostowania samoobsługowego. 

Przekaż plik definicji JSON przy użyciu poświadczeń do <br/>[**Nowy AzureRmDataFactoryV2LinkedServiceEncryptedCredential** ](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) polecenia cmdlet, aby utworzyć plik definicji danych wyjściowych JSON z zaszyfrowane poświadczenia. Następnie należy użyć zaktualizowanej definicji JSON do utworzenia połączonych usług.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [Data Factory version 1 documentation (Dokumentacja usługi Data Factory w wersji 1)](v1/data-factory-introduction.md).

## <a name="author-sql-server-linked-service"></a>Usługi SQL Server połączone autora
Utwórz plik JSON o nazwie **SqlServerLinkedService.json** do folderu o następującej treści:  

Zastąp `<servername>`, `<databasename>`, `<username>`, i `<password>` wartościami dla programu SQL Server przed zapisaniem pliku. I Zastąp `<integration runtime name>` o nazwie Twojego środowiska uruchomieniowego integracji. 

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
Do szyfrowania poufnych danych, z ładunek JSON na środowiska uruchomieniowego integracji siebie lokalnie, uruchom **AzureRmDataFactoryV2LinkedServiceEncryptedCredential nowy**i przekazywania ładunek JSON. To polecenie cmdlet gwarantuje, że poświadczenia są szyfrowane przy użyciu DPAPI i przechowywane w węźle środowiska uruchomieniowego integracji siebie lokalnie. Ładunek danych wyjściowych mogą zostać przekierowane do innego pliku JSON (w tym przypadku "encryptedLinkedService.json"), który zawiera zaszyfrowane poświadczenia.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Kod JSON za pomocą zaszyfrowane poświadczenia
Teraz, służy do ustawiania JSON plik wyjściowy z poprzedniego polecenia zawierające zaszyfrowane poświadczenia **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Kolejne kroki
Informacji dotyczących zabezpieczeń dla przepływu danych, zobacz [zagadnienia dotyczące zabezpieczeń przepływu danych](data-movement-security-considerations.md).

