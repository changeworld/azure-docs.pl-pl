---
title: Problemy z wdrażaniem dla często zadawane pytania dotyczące systemu Microsoft Azure Cloud Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących wdrażania usług Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 08d74f866fe28a4c424ba504795b4a22f09785ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337327"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z wdrażaniem usług Azure Cloud Services: Często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące problemów z wdrażaniem dla [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Można także zapoznać się [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) Aby uzyskać informacje o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Dlaczego wdrożenie usługi w chmurze do miejsca przejściowego, czasami kończy się niepowodzeniem z powodu błędu alokacji zasobów Jeśli jest już istniejącego wdrożenia w gnieździe produkcyjnym?
Jeśli usługa w chmurze ma wdrożenia w dowolnym miejscu, usługi w chmurze całego został przypięty do określonego klastra. Oznacza to, że jeśli wdrożenia już istnieje w miejscu produkcyjnym, nowego wdrożenia przejściowego może zostać przydzielone tylko w tym samym klastrze z miejscem produkcyjnym.

Błędy alokacji wystąpić, gdy klaster, w którym znajduje się usługa w chmurze nie ma wystarczającej ilości zasobów obliczeniowych fizyczne, do spełnienia żądania wdrożenia.

Aby uzyskać pomoc, łagodzenia takie błędy alokacji, zobacz [niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Dlaczego skalowanie w lub poza wdrażania usługi w chmurze czasami powodować niepowodzenie alokacji?
Po wdrożeniu usługi w chmurze on zazwyczaj pobiera przypięte do określonego klastra. Oznacza to, że skalowanie w górę/out istniejącej usługi w chmurze należy przydzielić nowe wystąpienia w tym samym klastrze. Jeśli zbliża się klastra lub żądanego rozmiaru maszyny Wirtualnej i typu nie jest dostępna, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc, łagodzenia takie błędy alokacji, zobacz [niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Dlaczego czasem wdrażania usługi w chmurze do grupy koligacji powoduje niepowodzenie alokacji?
Nowe wdrożenie usługi w chmurze pusty mogą zostać przydzieleni przez sieci szkieletowej w programie dowolnego klastra w danym regionie, chyba że usługi w chmurze jest przypięte do grupy koligacji. Zostanie podjęta próba wdrożenia w tej samej grupie koligacji, w tym samym klastrze. Jeśli klaster zbliża się, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc, łagodzenia takie błędy alokacji, zobacz [niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Dlaczego zmieniając rozmiar maszyny Wirtualnej lub dodawania nowej maszyny Wirtualnej do istniejącej usługi w chmurze, czasami powoduje niepowodzenie alokacji?
Klastry w centrum danych mogą mieć różne konfiguracje typów maszyny (np. serii serii Av2, seria D, seria Dv2, seria G, seria H, itp.). Jednak nie wszystkie klastry musi się wszystkie rodzaje maszyn wirtualnych. Na przykład Jeśli spróbujesz dodać serii D maszyn wirtualnych do usługi w chmurze, która została już wdrożona w klastrze tylko do serii A, wystąpią wystąpił błąd alokacji. Dzieje się tak również Jeśli spróbujesz zmienić że jednostki SKU maszyny Wirtualnej o rozmiarach (np. przełączanie z serii A z serii D).

Aby uzyskać pomoc, łagodzenia takie błędy alokacji, zobacz [niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

Aby sprawdzić dostępne rozmiary w Twoim regionie, zobacz [Microsoft Azure: Dostępność produktów według regionów](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Dlaczego jakiś czas wdrażania usługi w chmurze kończy się niepowodzeniem ze względu na ograniczenia/przydziały/ograniczenia na mojej subskrypcji lub usług?
Wdrożenie usługi w chmurze może zakończyć się niepowodzeniem, jeśli zasoby, które są wymagane do przydzielenia, która przekracza domyślnych lub maksymalny limit przydziału, o których usługi na poziomie region/centrum danych. Aby uzyskać więcej informacji, zobacz [usług Cloud Services ogranicza](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Można także śledzić bieżącego użycia/limit przydziału dla Twojej subskrypcji w portalu: Witryna Azure portal = > Subskrypcje = > \<odpowiednich subskrypcji > = > "Użycie + limitu przydziału".

Informacje o zasobach związanych z użyciem/użycie można również pobrać za pośrednictwem interfejsów API rozliczeń platformy Azure. Zobacz [użycia zasobów platformy Azure, interfejsu API (wersja zapoznawcza)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak zmienić rozmiar wdrożonej usłudze w chmurze maszyny Wirtualnej bez jej ponownego wdrażania
Nie można zmienić rozmiar maszyny Wirtualnej wdrożonej usłudze w chmurze, bez jej ponownego wdrażania. Rozmiar maszyny Wirtualnej jest wbudowana w CSDEF, w którym można aktualizować tylko z ponownego wdrażania.

Aby uzyskać więcej informacji, zobacz [sposób aktualizacji usługi w chmurze](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Dlaczego nie mogę wdrożyć usługi w chmurze za pośrednictwem interfejsów API zarządzania usługami lub programu PowerShell, korzystając z konta magazynu usługi Resource Manager platformy Azure? 

Ponieważ usługa w chmurze jest zasobem klasyczne, które nie są bezpośrednio zgodne z modelem usługi Azure Resource Manager, nie można skojarzyć go z kontami magazynu usługi Resource Manager platformy Azure. Poniżej przedstawiono kilka opcji: 
 
- Wdrażanie za pomocą interfejsu API REST.

    Podczas wdrażania za pomocą interfejsu API REST zarządzania usługami można obejść to ograniczenie, określając adres URL sygnatury dostępu Współdzielonego do magazynu obiektów blob, które będą działać przy użyciu magazynu usługi Resource Manager platformy Azure i klasycznego konta. Przeczytaj więcej na temat właściwości "PackageUrl" [tutaj](/previous-versions/azure/reference/ee460813(v=azure.100)).
  
- Wdrażanie za pomocą [witryny Azure portal](https://portal.azure.com).

    Funkcja będzie działać z [witryny Azure portal](https://portal.azure.com) jako wywołanie przechodzi przez serwer proxy/podkładki, która umożliwia komunikację między zasobami usługi Azure Resource Manager i model klasyczny. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Dlaczego witryna Azure portal wymaga mnie o podanie konta magazynu dla wdrożenia? 

W portalu klasycznym pakiet został przekazany bezpośrednio do warstwy interfejsu API zarządzania, a następnie warstwę interfejsu API tymczasowo będzie umieść pakiet do konta pamięci wewnętrznej.  Ten proces powoduje problemy z wydajnością i skalowalnością, ponieważ warstwę interfejsu API nie został zaprojektowany do usługi przekazywania plików.  W witrynie Azure portal (model wdrażania usługi Resource Manager) firma Microsoft ma pominąć pośredni krok w pierwszym przekazywania w warstwie interfejsu API skutkuje szybszy i bardziej niezawodny wdrożeń. 

Jak koszt jest bardzo mały, a następnie można ponownie użyć tego samego konta magazynu dla wszystkich wdrożeń. Możesz użyć [Kalkulator kosztów magazynowania](https://azure.microsoft.com/pricing/calculator/#storage1) ustalenie kosztów, aby przekazać pakiet usługi (CSPKG), Pobierz CSPKG, a następnie usuń CSPKG. 
