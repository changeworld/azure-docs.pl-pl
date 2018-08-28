---
title: Wersje interfejsu API dostawcy zasobów obsługiwane przez profilów w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Informacje o wersji usługi Azure Resource Manager, obsługiwanej przez profilów w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9d33ccf9262d4432ac7255121e97f318d00b5145
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050653"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Wersje interfejsu API dostawcy zasobów obsługiwane przez profilów w usłudze Azure Stack

Dla każdego profilu interfejsu API używanych przez usługę Azure Stack, w tym artykule, można znaleźć dostawcy zasobów i numery wersji. W tym artykule tabelach wersje obsługiwanych wersji interfejsu API profilów i każdy dostawca zasobów. Każdy dostawca zasobów zawiera zestaw typów zasobów i numery określonej wersji.

Profil interfejsu API używa trzech konwencji nazewnictwa:
 - najnowsza
 - Rrrr mm-dd hybrydowe
 - rrrr mm-dd-profile

Objaśnienia dotyczące profilów interfejsu API i cykl wersji wersji dla usługi Azure Stack, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> **Najnowsze** profilu interfejsu API zawiera najnowszej wersji interfejsu API dostawcy zasobów i nie znajduje się w tym artykule.

## <a name="overview-of-2018--03-01-hybrid"></a>Omówienie 2018-03-01-hybrydowe

| Dostawca zasobów | wersja interfejsu API |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Brama sieci VPN będzie 2017-03-01 |
| Microsoft.Storage (płaszczyzny danych) | 2017-04-17 |
| Microsoft.Storage (płaszczyznę kontroli) | 2016-01-01 |
| Firmy Microsoft. Sieć Web | 2016-08-01<br>czyli r (teraz) na platformie Azure |
| Microsoft.KeyVault | 2016-10-01 (nie zostanie zmieniona) |
| Microsoft.Resources (Azure Resource Manager SAM) | 2016-02-01 |
| Microsoft.Authorization (operacje dotyczące zasad) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zasady | 2016-10-01 |
| Zasoby | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Subskrypcje | 2016-10-01 |

Aby uzyskać bardziej listę wersji dla każdego typu zasobu dla dostawców w profilu interfejsu api, zobacz [szczegóły 2018-03-01-hybrydowego](#details-for-the-2018-03-01-hybrid) profilu.

## <a name="overview-of-2017-03-09-profile"></a>Omówienie 2017-03-09-profile

| Dostawca zasobów | wersja interfejsu API |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (płaszczyzny danych) | 2015-04-05  |
| Microsoft.Storage (płaszczyznę kontroli) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Nie zostanie zmieniona) |
| Microsoft.Resources<br>(Usługa azure Resource Manager SAM) | 2016-02-01 |
| Microsoft.Authorization<Br>(operacje dotyczące zasad) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zasady | 2015-10-01-preview |
| Zasoby | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Subskrypcje | 2016-06-1 |

Aby uzyskać bardziej listę wersji dla każdego typu zasobu dla dostawców w profilu interfejsu api, zobacz [szczegóły 2017-03-09-profile](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Szczegóły dotyczące 2018-03-01-rozwiązania hybrydowego

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Kontrola dostępu oparta na rolach umożliwia zarządzanie akcjami, które użytkownicy w organizacji mogą wykonywać względem zasobów. Ten zestaw operacji umożliwia definiowanie ról, przypisywanie ról użytkownikom lub grupom i uzyskiwanie informacji o uprawnieniach. Aby uzyskać więcej informacji, zobacz [autoryzacji](https://docs.microsoft.com/rest/api/authorization/).

| Typy zasobów | Wersje interfejsu API |
|---------------------|--------------------|
| Blokady | 2017-04-01 |
| Operacje | 2015-07-01 |
| Uprawnienia | 2015-07-01 |
| Przypisania zasad | 2016-12-01 (2017-06-01-preview) |
| Definicje zasad | 2016-12-01 |
| Operacje dostawcy | 2015-07-01-preview |
| Przypisania ról | 2015-07-01 |
| Definicje ról | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Typ zasobu | Wersja interfejsu API |
|----------------------------------|----------------------|
| Delegowani dostawcy subskrypcji | 2015-06-01 — wersja zapoznawcza |
| Agregacje użycia delegowanego | 2015-06-01 — wersja zapoznawcza |
| Wydatki oszacowanie zasobów | 2015-06-01 — wersja zapoznawcza |
| Operacje | 2015-06-01 — wersja zapoznawcza |
| Agregacje użycia subskrybenta | 2015-06-01 — wersja zapoznawcza |
| Agregacje użycia | 2015-06-01 — wersja zapoznawcza |

### <a name="microsoftcompute"></a>Microsoft.Compute

Obliczenia interfejsów API usługi Azure umożliwiają dostęp programowy do maszyn wirtualnych i obsługujących je zasobów. Aby uzyskać więcej informacji, zobacz [usługi Azure Compute](https://docs.microsoft.com/rest/api/compute/).

| Typ zasobu | Wersja interfejsu API |
|---------------------------------------------------------------|-------------|
| Zestawy dostępności | 2016-03-30 |
| Lokalizacje | 2016-03-30 |
| Lokalizacje/operations | 2016-03-30 |
| Lokalizacje/wydawcy | 2016-03-30 |
| Lokalizacje/użycia | 2016-03-30 |
| Lokalizacje/vmSizes | 2016-03-30 |
| Operacje | 2016-03-30 |
| Maszyny wirtualne | 2016-03-30 |
| Maszyny wirtualne/rozszerzenia | 2016-03-30 |
| Zestawy skali maszyn wirtualnych | 2016-03-30 |
| Zestawy skalowania maszyn wirtualnych/rozszerzenia | 2016-03-30 |
| Zestawy skalowania maszyn wirtualnych/interfejsów | 2016-03-30 |
| Maszyna wirtualna skalowanie zestawów/maszyn wirtualnych | 2016-03-30 |
| Skalowanie maszyn wirtualnych zestawów/maszyn wirtualnych/interfejsy | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Typ zasobu | Wersja interfejsu API |
|------------------|-------------|
| Dane nadzoru | 2015-04-01 |
| Zawartość nadzorowana | 2015-04-01 |
| Wyodrębnione dane nadzoru | 2015-04-01 |
| Elementy galerii | 2015-04-01 |
| Operacje | 2015-04-01 |
| Portal | 2015-04-01 |
| Wyszukiwanie | 2015-04-01 |
| Zasugeruj | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy zasobów | Wersje interfejsu API |
|--------------------|--------------------|
| Operacje | 2015-04-01 |
| Rodzaje wydarzeń | 2015-04-01 |
| Kategorie zdarzeń | 2015-04-01 |
| Definicje metryk | 2018-01-01 |
| Metryki | 2018-01-01 |
| Ustawienia diagnostyczne | 2017-05-01-preview |
| Kategorie ustawień diagnostycznych | 2017-05-01-preview |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Magazyny zarządzania kluczem, a także klucze, wpisy tajne i certyfikaty w ramach Twoich magazynów kluczy. Aby uzyskać więcej informacji, zobacz [odwołania API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/).

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Operacje | 2016-10-01 |
| Magazyny | 2016-10-01 |
| Magazyny / zasady dostępu | 2016-10-01 |
| Magazyny/klucze tajne | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Wynik wywołania usługi Operations jest reprezentacją listy dostępnych operacji chmury w sieci. Aby uzyskać więcej informacji, zobacz [interfejsu API REST operacji](https://docs.microsoft.com/rest/api/operation/).

| Typy zasobów | Wersje interfejsu API |
|---------------------------|--------------|
| Połączenia | 2015-06-15 |
| Strefy DNS | 2016-04-01 |
| Moduły równoważenia obciążenia | 2015-06-15 |
| Brama sieci lokalnej | 2015-06-15 |
| Lokalizacje | 2016-04-01 |
| Lokalizacja/operationResults | 2016-04-01 |
| Lokalizacje/operations | 2016-04-01 |
| Lokalizacje/użycia | 2016-04-01 |
| Interfejsy sieciowe | 2015-06-15 |
| Grupy zabezpieczeń sieci | 2015-06-15 |
| Operacje | 2015-06-15 |
| Publiczny adres IP | 2015-06-15 |
| Tabele tras | 2015-06-15 |
| Brama sieci wirtualnej | 2015-06-15 |
| Sieci wirtualne | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Usługa Azure Resource Manager umożliwia wdrażanie i zarządzanie nimi infrastruktura rozwiązaniach platformy Azure. Grupujesz powiązane zasoby w grupy zasobów i wdrażasz swoje zasoby za pomocą szablonów JSON. Wprowadzenie do wdrażania zasobów i zarządzania nimi przy użyciu usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Typy zasobów | Wersje interfejsu API |
|-----------------------------------------|-------------------|
| Rejestracje aplikacji | 2015-01-01 |
| Sprawdź nazwę zasobu | 2016-09-01 |
| Delegowani dostawcy | 2015-01-01 |
| Delegowani dostawcy/oferty | 2015-01-01 |
| DelegatedProviders/oferty/estimatePrice | 2015-01-01 |
| Wdrożenia | 2016-09-01 |
| Wdrożenia/operations | 2016-09-01 |
| Metadane rozszerzenia | 2015-01-01 |
| Linki | 2016-09-01 |
| Lokalizacje | 2015-01-01 |
| Oferta | 2015-01-01 |
| Operacje | 2015-01-01 |
| Dostawcy | 2017-08-01 |
| Grupy zasobów | 2016-09-01 |
| Zasoby | 2016-09-01 |
| Subskrypcje | 2016-09-01 |
| Subskrypcje/lokalizacja | 2016-09-01 |
| Wyniki operacji/subskrypcji | 2016-09-01 |
| Subskrypcje/dostawców | 2017-08-01 |
| Subskrypcjach/grupach zasobów | 2016-09-01 |
| Zasobów/resourceGroups/subskrypcji | 2016-09-01 |
| Zasobów/subskrypcji | 2016-09-01 |
| Subskrypcje/tagNames | 2016-09-01 |
| Subskrypcje/tagNames/tagValues | 2016-09-01 |
| Dzierżaw | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Storage Resource Provider (SRP) umożliwia programistyczne Zarządzanie swoim kontem magazynu i kluczami. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://docs.microsoft.com/rest/api/storagerp/).

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Sprawdź dostępność nazwy | 2016-01-01 |
| Lokalizacje | 2016-01-01 |
| Lokalizacje/przydziały | 2016-01-01 |
| Operacje | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Sposoby użycia | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Szczegóły dotyczące 2017-03-09-profile

### <a name="microsoft-authorization"></a>Autoryzacja Microsoft

| Typy zasobów | Wersje interfejsu API |
|---------------------|---------------------------------|
| Blokady | 2017-04-01 |
| Operacje | 2015-07-01 |
| Uprawnienia | 2015-07-01 |
| Przypisania zasad | 2016-12-01 (2017-06-01-preview) |
| Definicje zasad | 2016-12-01 |
| Operacje dostawcy | 2015-07-01-preview |
| Przypisania ról | 2015-07-01 |
| Definicje ról | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Typ zasobu | Wersja interfejsu API |
|---------------------------------------------------------------|-------------|
| Zestawy dostępności | 2016-03-30 |
| Lokalizacje | 2016-03-30 |
| Lokalizacje/operations | 2016-03-30 |
| Lokalizacje/wydawcy | 2016-03-30 |
| Lokalizacje/użycia | 2016-03-30 |
| Lokalizacje/vmSizes | 2016-03-30 |
| Operacje | 2016-03-30 |
| Maszyny wirtualne | 2016-03-30 |
| Maszyny wirtualne/rozszerzenia | 2016-03-30 |
| Zestawy skali maszyn wirtualnych | 2016-03-30 |
| Zestawy skalowania maszyn wirtualnych/rozszerzenia | 2016-03-30 |
| Zestawy skalowania maszyn wirtualnych/interfejsów | 2016-03-30 |
| Maszyna wirtualna skalowanie zestawów/maszyn wirtualnych | 2016-03-30 |
| Skalowanie maszyn wirtualnych zestawów/maszyn wirtualnych/interfejsy | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Typy zasobów | Wersje interfejsu API |
|---------------------------|--------------|
| Połączenia | 2015-06-15 |
| Strefy DNS | 2016-04-01 |
| Moduły równoważenia obciążenia | 2015-06-15 |
| Brama sieci lokalnej | 2015-06-15 |
| Lokalizacje | 2016-04-01 |
| Lokalizacja/operationResults | 2016-04-01 |
| Lokalizacje/operations | 2016-04-01 |
| Lokalizacje/użycia | 2016-04-01 |
| Interfejsy sieciowe | 2015-06-15 |
| Grupy zabezpieczeń sieci | 2015-06-15 |
| Operacje | 2015-06-15 |
| Publiczny adres IP | 2015-06-15 |
| Tabele tras | 2015-06-15 |
| Brama sieci wirtualnej | 2015-06-15 |
| Sieci wirtualne | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Typy zasobów | Wersje interfejsu API |
|-----------------------------------------|--------------|
| Rejestracje aplikacji | 2015-01-01 |
| Sprawdź nazwę zasobu | 2016-09-01 |
| Delegowani dostawcy | 2015-01-01 |
| Delegowani dostawcy/oferty | 2015-01-01 |
| DelegatedProviders/oferty/estimatePrice | 2015-01-01 |
| Wdrożenia | 2016-09-01 |
| Wdrożenia/operations | 2016-09-01 |
| Metadane rozszerzenia | 2015-01-01 |
| Linki | 2016-09-01 |
| Lokalizacje | 2015-01-01 |
| Oferta | 2015-01-01 |
| Operacje | 2015-01-01 |
| Dostawcy | 2017-08-01 |
| Grupy zasobów | 2016-09-01 |
| Zasoby | 2016-09-01 |
| Subskrypcje | 2016-09-01 |
| Subskrypcje/lokalizacja | 2016-09-01 |
| Wyniki operacji/subskrypcji | 2016-09-01 |
| Subskrypcje/dostawców | 2017-08-01 |
| Subskrypcjach/grupach zasobów | 2016-09-01 |
| Zasobów/resourceGroups/subskrypcji | 2016-09-01 |
| Zasobów/subskrypcji | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Subskrypcje/tagNames/tagValues | 2016-09-01 |
| Dzierżaw | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Sprawdź dostępność nazwy | 2016-01-01 |
| Lokalizacje | 2016-01-01 |
| Lokalizacje/przydziały | 2016-01-01 |
| Operacje | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Sposoby użycia | 2016-01-01 |

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)  
