---
title: Przechowywania poświadczeń w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechowywania poświadczeń dla magazynów danych używany w usługi Azure key vault, która fabryka danych Azure mogą automatycznie pobierać w czasie wykonywania.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: 4a8c96bf9124feede2e5a28beb791636784dcad7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Przechowywania poświadczeń w usłudze Azure Key Vault

Można zapisać poświadczenia na potrzeby magazynów danych i oblicza w [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Fabryka danych Azure pobiera poświadczeń podczas wykonywania działanie, które korzysta z magazynu danych/obliczeniowej.

Ta funkcja obsługuje obecnie, wszystkie typy działań z wyjątkiem działania niestandardowego. Dla konfiguracji łącznika w szczególności, Sprawdź sekcję "połączone właściwości usługi" [każdego tematu łącznika](copy-activity-overview.md#supported-data-stores-and-formats) szczegółowe informacje.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [dokumentacji dla fabryki danych version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości usługi fabryka danych. Dowiedz się, jak to działa z [tożsamości usługi fabryka danych](data-factory-service-identity.md) i upewnij się, że skojarzona jeden ma fabrykę danych.

>[!TIP]
>W przypadku usługi Azure Key Vault, tworząc klucz tajny **put wprowadza całej wartości właściwości tajne, że ADF połączone usługi (np. połączenia usługi ciągu/hasła główny klucz/itp.)**. Na przykład w przypadku usługi Azure Storage połączonej usługi, umieść `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` jako AKV klucz tajny, a następnie w polu "connectionString" odwołania z ADF; dla połączonej usługi Dynamics, umieść `myPassword` jako AKV klucza tajnego, następnie odwołać się do pola "hasła usługi" ADF. Można znaleźć w artykule każdy łącznik/obliczeń, w szczegółach obsługiwanych właściwości.

## <a name="steps"></a>Kroki

Aby odwołać poświadczenia przechowywane w usłudze Azure Key Vault, musisz:

1. **Pobieranie tożsamości usługi fabryka danych** przez skopiowanie wartości "Usługa tożsamości aplikacji Identyfikatora" generowane wraz z fabryką. Jeśli używasz ADF tworzenia interfejsu użytkownika, identyfikator tożsamości usługi ma być wyświetlana w oknie Tworzenie połączonej usługi Azure Key Vault; można je również pobrać z portalu Azure, zobacz [pobrać tożsamość usługi fabryka danych](data-factory-service-identity.md#retrieve-service-identity).
2. **Udzielać dostępu tożsamości usługi Magazyn kluczy Azure.** W magazynie kluczy, -> zasady -> dostępu Dodaj nowy -> wyszukiwania IDENTYFIKATORA aplikacji tożsamości usługi przyznania **uzyskać** uprawnień w listy rozwijanej tajny uprawnienia. Umożliwia to wyznaczonych fabryki klucza tajnego w magazynie kluczy dostępu do.
3. **Tworzenie połączonej usługi wskazujący magazyn kluczy Azure.** Zapoznaj się [usługi Azure Key Vault połączona usługa](#azure-key-vault-linked-service).
4. **Tworzenie usługi magazynu połączone danych, w których odwołanie magazynu odpowiadający mu klucz tajny przechowywanych w.** Zapoznaj się [odwołanie klucza tajnego przechowywane w magazynie kluczy](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Usługa Azure Key Vault połączone

Obsługiwane są następujące właściwości dla usługi Azure Key Vault połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AzureKeyVault**. | Yes |
| baseUrl | Podaj adres URL magazynu kluczy Azure. | Yes |

**Za pomocą tworzenia interfejsu użytkownika:**

Kliknij przycisk **połączeń** -> **połączonych usług** -> **+ nowy** -> Wyszukaj "Usługa Azure Key Vault":

![Wyszukiwanie AKV](media/store-credentials-in-key-vault/search-akv.png)

Wybierz udostępnione magazyn kluczy Azure, gdzie są przechowywane poświadczenia. Możesz zrobić **Testuj połączenie** upewnij się, że Twoje AKV połączenia jest nieprawidłowy. 

![Skonfiguruj AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Przykład JSON:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Klucz tajny odwołanie przechowywane w magazynie kluczy

Po skonfigurowaniu pola w połączonej usłudze odwołujące się do magazynu kluczy klucz tajny, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość Typ pola: **AzureKeyVaultSecret**. | Yes |
| secretName | Nazwa klucza tajnego w magazynie kluczy azure. | Yes |
| secretVersion | Wersja klucza tajnego w magazynie kluczy azure.<br/>Jeśli nie zostanie określony, zawsze używa najnowszą wersję klucza tajnego.<br/>Jeśli zostanie określona, następnie go systemu danej wersji.| Nie |
| sklep | Odwołuje się do usługi Azure Key Vault połączone, które służy do przechowywania poświadczeń. | Yes |

**Za pomocą tworzenia interfejsu użytkownika:**

Wybierz **usługi Azure Key Vault** tajny pól podczas tworzenia połączenia magazynu danych/obliczeniowych. Wybierz udostępnione klucza magazynu połączonej usługi Azure i podaj **nazwa klucza tajnego**. Opcjonalnie można podać również wersję klucza tajnego. 

![Skonfiguruj AKV klucz tajny](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Przykład JSON: (zobacz sekcję "password")**

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
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).