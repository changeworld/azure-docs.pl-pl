---
title: Store poświadczeń w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać poświadczenia dla magazynów danych używanych w usługi Azure key vault, usługi Azure Data Factory automatycznie do pobrania w czasie wykonywania.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2017
ms.author: jingwang
ms.openlocfilehash: 3428fb5034435d9f3444347329171d803136177c
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944672"
---
# <a name="store-credential-in-azure-key-vault"></a>Store poświadczeń w usłudze Azure Key Vault

Możesz przechowywać poświadczenia dla magazynów danych i obliczeń w [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Usługa Azure Data Factory umożliwia pobranie poświadczeń podczas wykonywania działania, który używa magazynu danych/obliczenia.

Obecnie wszystkie typy działań, z wyjątkiem działań niestandardowych obsługują tę funkcję. Dla konfiguracji łącznika w szczególności Sprawdź sekcję "właściwości połączonej usługi" w folderze [każdego tematu łącznika](copy-activity-overview.md#supported-data-stores-and-formats) Aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamość usługi fabryki danych. Dowiedz się, jak to działa z [tożsamość usługi fabryki danych](data-factory-service-identity.md) i upewnij się, fabryki danych skojarzony jeden.

>[!TIP]
>W usłudze Azure Key Vault, po utworzeniu wpisu tajnego **put całą wartość właściwości wpisu tajnego, że ADF połączoną usługę poprosi o podanie (np. połączenia usługi ciąg/hasła jednostki klucz/itp.)**. Na przykład w przypadku usługi Azure Storage połączonej usługi, umieść `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` jako klucz tajny AKV, a następnie można się odwołać w polu "connectionString" z usługi ADF; dla połączonej usługi Dynamics, umieść `myPassword` jako klucz tajny AKV, następnie odwoływać się do pola "password" ADF. Można znaleźć w artykule każdego łącznika/obliczeniowe, w szczegółach obsługiwanych właściwości.

## <a name="steps"></a>Kroki

Aby odwołać poświadczenia przechowywane w usłudze Azure Key Vault, musisz:

1. **Pobierz tożsamość usługi fabryki danych** przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi" wygenerowane wraz z fabryką. Jeśli używasz usługi ADF tworzenia interfejsu użytkownika, identyfikator tożsamości usługi będą wyświetlane w oknie tworzenia połączonej usługi Azure Key Vault; można je również pobrać z witryny Azure portal, zapoznaj się [pobrać tożsamość usługi fabryki danych](data-factory-service-identity.md#retrieve-service-identity).
2. **Udzielanie dostępu do tożsamości usługi do usługi Azure Key Vault.** W magazynie kluczy -> zasady -> dostępu Dodaj nowy -> wyszukiwania IDENTYFIKATORA tej aplikacji tożsamości usługi, aby udzielić **uzyskać** uprawnienie na liście rozwijanej uprawnienia klucza tajnego. Umożliwia ona tej fabryce wyznaczone do dostępu do klucza tajnego w magazynie kluczy.
3. **Tworzenie połączonej usługi wskazuje usłudze Azure Key Vault.** Zapoznaj się [usługę połączoną usługi Azure Key Vault](#azure-key-vault-linked-service).
4. **Tworzenie połączonej usługi magazynu danych, wewnątrz której odwołanie odpowiadający mu klucz tajny przechowywanych w magazynie.** Zapoznaj się [odwołanie wpisu tajnego, przechowywanych w magazynie kluczy](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Usługa Azure Key Vault połączone

Następujące właściwości są obsługiwane w przypadku usługi połączonej usługi Azure Key Vault:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **AzureKeyVault**. | Yes |
| baseUrl | Podaj adres URL usługi Azure Key Vault. | Yes |

**Korzystanie z tworzenia interfejsu użytkownika:**

Kliknij przycisk **połączeń** -> **połączonych usług** -> **+ nowy** -> Wyszukaj "Usługi Azure Key Vault":

![Wyszukiwanie AKV](media/store-credentials-in-key-vault/search-akv.png)

Wybierz udostępnione Azure Key Vault gdzie przechowywane są Twoje poświadczenia. Możesz zrobić **Testuj połączenie** aby upewnić się, że Twoje AKV połączenie jest poprawne. 

![Konfigurowanie AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Przykład kodu JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Klucz tajny odwołanie przechowywanych w magazynie kluczy

Następujące właściwości są obsługiwane w przypadku skonfigurowania pola w połączonej usłudze odwołujące się do niej wpisu tajnego usługi key vault:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Typ pola musi być równa: **AzureKeyVaultSecret**. | Yes |
| secretName | Nazwa wpisu tajnego w usłudze azure key vault. | Yes |
| secretVersion | Wersja wpisu tajnego w usłudze azure key vault.<br/>Jeśli nie zostanie określony, zawsze używa najnowszej wersji klucza tajnego.<br/>Jeśli zostanie określony, następnie go pozostanie przypisane do danej wersji.| Nie |
| Store | Odnosi się do usługi połączonej usługi Azure Key Vault, używanej do przechowywania poświadczeń. | Yes |

**Korzystanie z tworzenia interfejsu użytkownika:**

Wybierz **usługi Azure Key Vault** dla wpisu tajnego pól podczas tworzenia połączenia z magazynu danych/obliczeń. Wybierz udostępnione klucza magazynu połączoną usługę Azure i podaj **Nazwa wpisu tajnego**. Opcjonalnie możesz podać również wersja wpisu tajnego. 

![Konfigurowanie AKV wpisu tajnego](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Przykład kodu JSON: (zobacz sekcję "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
