---
title: Wersje interfejsu API dostawcy zasobów obsługiwane przez profile w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o wersji usługi Azure Resource Manager obsługiwane przez profil w stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: ee4321b905396f78e7dad9248b9e377dad250a13
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Wersje interfejsu API dostawcy zasobów obsługiwane przez profile w stosie Azure

Dla każdego profilu interfejsu API używanych przez stos Azure w tym artykule można znaleźć dostawcy zasobów i numery wersji. W tabelach w tym artykule przedstawiono wersje obsługiwanych wersji interfejsu API profilów i każdy dostawca zasobów. Każdy dostawca zasobów zawiera zestaw typów zasobów i numerów wersji.

Profil interfejsu API używa trzech konwencji nazewnictwa:
 - najnowsza
 - Rrrr mm-dd hybrydowego
 - rrrr mm-dd profilu

Opis profilów interfejsu API oraz wersji release okresach stosu Azure można znaleźć [profile wersji Zarządzanie interfejsu API w stosie Azure](azure-stack-version-profiles.md).

> [!Note]  
> **Najnowsze** profilu interfejsu API zawiera najnowszą wersję interfejsu API dostawcy zasobu, a nie znajduje się w tym artykule.

## <a name="overview-of-2018--03-01-hybrid"></a>Omówienie 2018-03-01-hybrydowego

| Dostawca zasobów | wersja interfejsu API |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Brama sieci VPN będzie 2017-03-01 |
| Microsoft.Storage (płaszczyzna danych) | 2017-04-17 |
| Microsoft.Storage (płaszczyzna sterowania) | 2016-01-01 |
| Firmy Microsoft. Sieć Web | 2016-08-01<br>czyli r (od tej chwili) na platformie Azure |
| Microsoft.KeyVault | 2016-10-01 (nie zmieniając) |
| Microsoft.Resources (usługi Azure Resource Manager SAM) | 2016-02-01 |
| Microsoft.Authorization (operacje zasad) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zasady | 2016-10-01 |
| Zasoby | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Subskrypcje | 2016-10-01 |

Aby uzyskać więcej listę wersji dla każdego typu zasobu dla dostawców w profilu interfejsu api, zobacz [szczegóły 2018-03-01-hybrydowego](#details-for-the-2018-03-01-hybrid) profilu.

## <a name="overview-of-2017-03-09-profile"></a>Omówienie 2017-03-09-profilu

| Dostawca zasobów | wersja interfejsu API |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (płaszczyzna danych) | 2015-04-05  |
| Microsoft.Storage (płaszczyzna sterowania) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Nie zmieniając) |
| Microsoft.Resources<br>(Usługa azure Resource Manager SAM) | 2016-02-01 |
| Microsoft.Authorization<Br>(operacje zasad) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zasady | 2015-10-01-preview |
| Zasoby | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Subskrypcje | 2016-06-1 |

Aby uzyskać więcej listę wersji dla każdego typu zasobu dla dostawców w profilu interfejsu api, zobacz [szczegóły 2017-03-09-profilu](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Szczegóły dotyczące 2018-03-01-hybrydowego

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Kontrola dostępu oparta na rolach umożliwia zarządzanie działania, które użytkownicy w organizacji można wykonać na zasobach. Ten zestaw operacji umożliwia definiowanie ról, Przypisz role do użytkowników lub grup i uzyskać informacje na temat uprawnień. Aby uzyskać więcej informacji, zobacz [autoryzacji](https://docs.microsoft.com/rest/api/authorization/).

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
| Subskrypcje delegowanego dostawcy | 2015-06-01 - preview |
| Agreguje delegowanego użycia | 2015-06-01 - preview |
| Spędzają oszacowanie zasobów | 2015-06-01-preview |
| Operacje | 2015-06-01 - preview |
| Agreguje użycia subskrybenta | 2015-06-01 - preview |
| Agregacje użycia | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Obliczeniowe interfejsów API usługi Azure umożliwiają programowy dostęp do maszyn wirtualnych i ich obsługi zasobów. Aby uzyskać więcej informacji, zobacz [rozwiązań usługi obliczenia Azure](https://docs.microsoft.com/rest/api/compute/).

| Typ zasobu | Wersja interfejsu API |
|---------------------------------------------------------------|-------------|
| Zestawy dostępności | 2016-03-30 |
| Lokalizacje | 2016-03-30 |
| Lokalizacje działań | 2016-03-30 |
| Lokalizacje/wydawcy | 2016-03-30 |
| Lokalizacje/użycia | 2016-03-30 |
| Lokalizacje/vmSizes | 2016-03-30 |
| Operacje | 2016-03-30 |
| Maszyny wirtualne | 2016-03-30 |
| Maszyny wirtualne/rozszerzenia | 2016-03-30 |
| Zestawy skali maszyn wirtualnych | 2016-03-30 |
| Zestawy skalowania maszyny wirtualnej/rozszerzenia | 2016-03-30 |
| Zestawy skalowania maszyny wirtualnej/interfejsów | 2016-03-30 |
| Maszyn wirtualnych/zestawów skali maszyny wirtualnej | 2016-03-30 |
| Ustawia/virtualMachines/elementów Networkinterface skali maszyny wirtualne | 2016-03-30 |

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
| Reguły alertów | 2016-03-01 |
| Kategorie zdarzeń | 2017-03-01-preview |
| Rodzaje wydarzeń | 2017-03-01-preview |
| Definicje metryk | 2016-03-01 |
| Operacje | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Zarządzanie kluczem magazynów oraz kluczy, kluczy tajnych i certyfikaty w sieci magazynów kluczy. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST magazynu kluczy Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Operacje | 2016-10-01 |
| magazynów | 2016-10-01 |
| Magazyny / zasady dostępu | 2016-10-01 |
| Magazyny/klucze tajne | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Wynik wywołania operacji jest reprezentację lista dostępnych operacji chmury w sieci. Aby uzyskać więcej informacji, zobacz [operacji interfejsu API REST](https://docs.microsoft.com/rest/api/operation/).

| Typy zasobów | Wersje interfejsu API |
|---------------------------|--------------|
| Połączenia | 2015-06-15 |
| Strefy DNS | 2016-04-01 |
| Moduły równoważenia obciążenia | 2015-06-15 |
| Brama sieci lokalnej | 2015-06-15 |
| Lokalizacje | 2016-04-01 |
| Lokalizacja/operationResults | 2016-04-01 |
| Lokalizacje działań | 2016-04-01 |
| Lokalizacje/użycia | 2016-04-01 |
| Interfejsy sieciowe | 2015-06-15 |
| Grupy zabezpieczeń sieci | 2015-06-15 |
| Operacje | 2015-06-15 |
| Publiczny adres IP | 2015-06-15 |
| Tabele tras | 2015-06-15 |
| Brama sieci wirtualnej | 2015-06-15 |
| Sieci wirtualne | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Usługa Azure Resource Manager umożliwia wdrażanie i zarządzanie nimi infrastruktury dla rozwiązań do platformy Azure. Organizowanie powiązanych zasobów w grupach zasobów i wdrażanie zasobów z szablony JSON. Aby obejrzeć wprowadzenie do wdrażania i zarządzania zasobami za pomocą Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Typy zasobów | Wersje interfejsu API |
|-----------------------------------------|-------------------|
| Rejestracji aplikacji | 2015-01-01 |
| Sprawdź nazwę zasobu | 2015-012016-09-01 |
| Delegowane dostawców | 2015-01-01 |
| Delegowane dostawców/oferty | 2015-01-01 |
| EstimatePrice-DelegatedProviders/oferty | 2015-01-01 |
| Wdrożenia | 2016-0209-01 |
| Wdrożenia/operations | 2016-0209-01 |
| Metadane rozszerzenia | 2015-01-01 |
| Linki | 2015-012016-09-01 |
| Lokalizacje | 2015-01-01 |
| Oferta | 2015-01-01 |
| Operacje | 2015-01-01 |
| Dostawcy | 2015-012017-08-01 |
| Grupy zasobów | 2015-012016-09-01 |
| Zasoby | 2015-012016-09-01 |
| Subskrypcje | 2015-012016-09-01 |
| Subskrypcje/lokalizacji | 2015-012016-09-01 |
| Wyniki subskrypcji na operację | 2015-012016-09-01 |
| Subskrypcje/dostawców | 2015-012017-08-01 |
| Subskrypcji lub grupy zasobów | 2015-012016-09-01 |
| Subskrypcje/resourceGroups/zasobów | 2015-012016-09-01 |
| Subskrypcje/zasobów. | 2015-012016-09-01 |
| Subskrypcje/tagNames | 2016-0609-01 |
| Subskrypcje/tagNames/tagValues | 2016-0609-01 |
| Dzierżawcy | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Dostawcy zasobów magazynu (SRP) umożliwia zarządzanie konta magazynu i klucze programowo. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST dostawcy zasobów magazynu Azure](https://docs.microsoft.com/rest/api/storagerp/).

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Sprawdź dostępność nazwy | 2016-01-01 |
| Lokalizacje | 2016-01-01 |
| Lokalizacje/przydziałów | 2016-01-01 |
| Operacje | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Użycia | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Szczegóły dotyczące 2017-03-09-profilu

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
| Lokalizacje działań | 2016-03-30 |
| Lokalizacje/wydawcy | 2016-03-30 |
| Lokalizacje/użycia | 2016-03-30 |
| Lokalizacje/vmSizes | 2016-03-30 |
| Operacje | 2016-03-30 |
| Maszyny wirtualne | 2016-03-30 |
| Maszyny wirtualne/rozszerzenia | 2016-03-30 |
| Zestawy skali maszyn wirtualnych | 2016-03-30 |
| Zestawy skalowania maszyny wirtualnej/rozszerzenia | 2016-03-30 |
| Zestawy skalowania maszyny wirtualnej/interfejsów | 2016-03-30 |
| Maszyn wirtualnych/zestawów skali maszyny wirtualnej | 2016-03-30 |
| Ustawia/virtualMachines/elementów Networkinterface skali maszyny wirtualne | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Typy zasobów | Wersje interfejsu API |
|---------------------------|--------------|
| Połączenia | 2015-06-15 |
| Strefy DNS | 2016-04-01 |
| Moduły równoważenia obciążenia | 2015-06-15 |
| Brama sieci lokalnej | 2015-06-15 |
| Lokalizacje | 2016-04-01 |
| Lokalizacja/operationResults | 2016-04-01 |
| Lokalizacje działań | 2016-04-01 |
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
| Rejestracji aplikacji | 2015-01-01 |
| Sprawdź nazwę zasobu | 2016-09-01 |
| Delegowane dostawców | 2015-01-01 |
| Delegowane dostawców/oferty | 2015-01-01 |
| EstimatePrice-DelegatedProviders/oferty | 2015-01-01 |
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
| Subskrypcje/lokalizacji | 2016-09-01 |
| Wyniki subskrypcji na operację | 2016-09-01 |
| Subskrypcje/dostawców | 2017-08-01 |
| Subskrypcji lub grupy zasobów | 2016-09-01 |
| Subskrypcje/resourceGroups/zasobów | 2016-09-01 |
| Subskrypcje/zasobów. | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Subskrypcje/tagNames/tagValues | 2016-09-01 |
| Dzierżawcy | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Typy zasobów | Wersje interfejsu API |
|-------------------------|--------------|
| Sprawdź dostępność nazwy | 2016-01-01 |
| Lokalizacje | 2016-01-01 |
| Lokalizacje/przydziałów | 2016-01-01 |
| Operacje | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Użycia | 2016-01-01 |

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)  
