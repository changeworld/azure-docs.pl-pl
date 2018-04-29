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
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: db01df21c95ee41197344cec719f1c2ab2dfc2ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Wersje interfejsu API dostawcy zasobów obsługiwane przez profile w stosie Azure

Dostawcy zasobów platformy Azure udostępnia zasoby można wdrażać i zarządzać nimi za pomocą Menedżera zasobów Azure. Każdy dostawca oferuje operacji do pracy z zasobami. Niektóre typowe dostawców zasobów obejmują Microsoft.Compute, która dostarcza maszyn wirtualnych, Microsoft.Storage, która dostarcza zasobów konta magazynu, i Microsoft.Web, która dostarcza zasobów związanych z aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

W poniższej tabeli, dla każdego dostawcy zasobów przedstawiono obsługiwaną wersję z wersją interfejsu API stosu Azure przy użyciu profilów.

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

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Zarządzanie kluczem magazynów oraz kluczy, kluczy tajnych i certyfikaty w sieci magazynów kluczy. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST magazynu kluczy Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Typy zasobów | Wersje interfejsu API |
|------------------|--------------------|
| Lokalizacje | 2017-02-01-preview |
| Lokalizacje/przydziałów | 2017-02-01-preview |

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

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)  
