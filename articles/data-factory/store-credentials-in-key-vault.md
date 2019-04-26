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
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311898"
---
# <a name="store-credential-in-azure-key-vault"></a>Store poświadczeń w usłudze Azure Key Vault

Możesz przechowywać poświadczenia dla magazynów danych i obliczeń w [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Usługa Azure Data Factory umożliwia pobranie poświadczeń podczas wykonywania działania, który używa magazynu danych/obliczenia.

Obecnie wszystkie typy działań, z wyjątkiem działań niestandardowych obsługują tę funkcję. Dla konfiguracji łącznika w szczególności Sprawdź sekcję "właściwości połączonej usługi" w folderze [każdego tematu łącznika](copy-activity-overview.md#supported-data-stores-and-formats) Aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanych fabryki danych. Dowiedz się, jak to działa z [tożsamości zarządzanej dla usługi Data factory](data-factory-service-identity.md) i upewnij się, fabryki danych skojarzony jeden.

## <a name="steps"></a>Kroki

Aby odwołać poświadczenia przechowywane w usłudze Azure Key Vault, musisz:

1. **Pobieranie danych fabryki zarządzanych tożsamości** przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi" wygenerowane wraz z fabryką. Jeśli używasz usługi ADF tworzenia interfejsu użytkownika, identyfikator aplikacji tożsamości zarządzanej będą wyświetlane w oknie tworzenia połączonej usługi Azure Key Vault; można je również pobrać z witryny Azure portal, zapoznaj się [tożsamości zarządzanej usługi fabryka danych pobierania](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udzielić dostępu tożsamości zarządzanej do usługi Azure Key Vault.** W magazynie kluczy -> zasady -> dostępu Dodaj nowy -> search to zarządzane identyfikator aplikacji tożsamości, aby udzielić **uzyskać** uprawnienie na liście rozwijanej uprawnienia klucza tajnego. Umożliwia ona tej fabryce wyznaczone do dostępu do klucza tajnego w magazynie kluczy.
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

>[!TIP]
>Łączniki przy użyciu parametrów połączenia w połączonej usłudze, takiej jak SQL Server, usługi Blob storage itp., można przechowywać tylko pole klucza tajnego np. hasła w AKV lub do przechowywania ciągu całego połączenia w AKV. Możesz znaleźć obie opcje w interfejsie użytkownika.

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
