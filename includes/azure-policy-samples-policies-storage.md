---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 49934fbb9b317ac99a7c9473164f25436eefb0b0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192677"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Allowed storage account SKUs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) (Dozwolone jednostki SKU konta magazynu) |Te zasady umożliwiają określenie zestawu jednostek SKU konta magazynu, które można wdrożyć w organizacji. |Zablokuj |1.0.0 |
|[Inspekcja nieograniczonego dostępu sieciowego do kont magazynu](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Inspekcja nieograniczonego dostępu do sieci w ustawieniach zapory konta magazynu. Zamiast tego należy skonfigurować reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP w Internecie |Inspekcja, wyłączona |1.0.0 |
|[Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Te zasady umożliwiają zaawansowaną ochronę przed zagrożeniami na kontach magazynu. |DeployIfNotExists, wyłączone |1.0.0 |
|[Magazyn Geograficznie nadmiarowy powinien być włączony dla kont magazynu](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Ta zasada przeprowadza inspekcję wszystkich kont magazynu z magazynem geograficznie nadmiarowym, który nie jest włączony. |Inspekcja, wyłączona |1.0.0 |
|[Należy włączyć bezpieczny transfer do kont magazynu](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Przeprowadź inspekcję requirment bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |1.0.0 |
|[Konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu działają z sieci, których nie można udzielić dostępu za pomocą reguł sieci. Aby ułatwić tego typu elementu roboczego usług, zgodnie z oczekiwaniami, zezwala zestaw zaufanych usług firmy Microsoft w celu obejścia zasad sieci. Te usługi użyje silnego uwierzytelniania dostępu do konta magazynu. |Inspekcja, Odmów, wyłączone |1.0.0 |
|[Konta magazynu należy migrować do nowych zasobów Azure Resource Manager](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Użyj nowych Azure Resource Manager dla kont magazynu, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie oparte na Azure Resource Manager i zarządzanie, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisy tajne, uwierzytelnianie oparte na usłudze Azure AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami |Inspekcja, Odmów, wyłączone |1.0.0 |
