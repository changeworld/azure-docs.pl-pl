---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172940"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego. Ustaw poziom ochrony, aby upewnić się, że wszystkie komunikaty między węzłami są szyfrowane i podpisane cyfrowo |Inspekcja, wyłączona |1.0.0 |
|[W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Inspekcja użycia uwierzytelniania klienta tylko za pośrednictwem Azure Active Directory w Service Fabric |Inspekcja, wyłączona |1.0.0 |
