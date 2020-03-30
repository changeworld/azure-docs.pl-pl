---
title: Problemy z wdrażaniem usług w chmurze platformy Microsoft Azure – często zadawane pytania| Dokumenty firmy Microsoft
description: W tym artykule wymieniono często zadawane pytania dotyczące wdrażania usług w chmurze platformy Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980628"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z wdrażaniem usług w chmurze azure: często zadawane pytania (często zadawane pytania)

Ten artykuł zawiera często zadawane pytania dotyczące problemów z wdrażaniem [usług w chmurze platformy Microsoft Azure.](https://azure.microsoft.com/services/cloud-services) Informacje o rozmiarach można również zapoznać się ze [stroną Rozmiar maszyny Wirtualnej usług w chmurze.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Dlaczego wdrażanie usługi w chmurze w miejscu przejściowym czasami kończy się niepowodzeniem z błędem alokacji zasobów, jeśli istnieje już istniejące wdrożenie w miejscu produkcyjnym?
Jeśli usługa w chmurze ma wdrożenie w każdym miejscu, cała usługa w chmurze jest przypięta do określonego klastra. Oznacza to, że jeśli wdrożenie już istnieje w miejscu produkcyjnym, nowe wdrożenie przejściowe można przydzielić tylko w tym samym klastrze co gniazdo produkcyjne.

Błędy alokacji występują, gdy klaster, w którym znajduje się usługa w chmurze, nie ma wystarczających fizycznych zasobów obliczeniowych, aby spełnić żądanie wdrożenia.

Aby uzyskać pomoc dotyczącą łagodzenia takich błędów alokacji, zobacz [Błąd alokacji usługi w chmurze: rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Dlaczego skalowanie w górę lub skalowanie w poziomie wdrożenia usługi w chmurze czasami powoduje niepowodzenie alokacji?
Po wdrożeniu usługi w chmurze zwykle jest przypięta do określonego klastra. Oznacza to skalowanie w górę/na zewnątrz istniejącej usługi w chmurze musi przydzielić nowe wystąpienia w tym samym klastrze. Jeśli klaster zbliża się do pojemności lub żądany rozmiar/typ maszyny Wirtualnej nie jest dostępny, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc dotyczącą łagodzenia takich błędów alokacji, zobacz [Błąd alokacji usługi w chmurze: rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Dlaczego wdrażanie usługi w chmurze w grupie koligacji czasami powoduje niepowodzenie alokacji?
Nowe wdrożenie do pustej usługi w chmurze mogą być przydzielane przez sieci szkieletowej w dowolnym klastrze w tym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. Wdrożenia do tej samej grupy koligacji będą podejmowane w tym samym klastrze. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc dotyczącą łagodzenia takich błędów alokacji, zobacz [Błąd alokacji usługi w chmurze: rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Dlaczego zmiana rozmiaru maszyny Wirtualnej lub dodanie nowej maszyny Wirtualnej do istniejącej usługi w chmurze czasami powoduje niepowodzenie alokacji?
Klastry w centrum danych mogą mieć różne konfiguracje typów maszyn (na przykład serie A, seria Av2, seria D, seria Dv2, seria G, seria H, seria H itp.). Ale nie wszystkie klastry koniecznie mają wszystkie rodzaje maszyn wirtualnych. Na przykład jeśli spróbujesz dodać maszynę wirtualną z serii D do usługi w chmurze, która jest już wdrożona w klastrze tylko do serii, wystąpi błąd alokacji. Stanie się tak również, jeśli spróbujesz zmienić rozmiary jednostek SKU maszyn wirtualnych (na przykład przejście z serii A na serię D).

Aby uzyskać pomoc dotyczącą łagodzenia takich błędów alokacji, zobacz [Błąd alokacji usługi w chmurze: rozwiązania](cloud-services-allocation-failures.md#solutions).

Aby sprawdzić rozmiary dostępne w Twoim regionie, zobacz [Microsoft Azure: Produkty dostępne według regionu](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Dlaczego wdrożenie usługi w chmurze kiedyś nie powiedzie się z powodu limitów/przydziałów/ograniczeń w mojej subskrypcji lub usłudze?
Wdrożenie usługi w chmurze może zakończyć się niepowodzeniem, jeśli zasoby, które muszą zostać przydzielone, przekraczają domyślny lub maksymalny przydział dozwolony dla usługi na poziomie regionu/centrum danych. Aby uzyskać więcej informacji, zobacz [limity usług w chmurze](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Można również śledzić bieżące użycie/przydział subskrypcji w portalu: Azure portal => Subskrypcje => \<odpowiednie> subskrypcji => "Użycie + przydział".

Informacje dotyczące użycia/zużycia zasobów można również pobrać za pośrednictwem interfejsów API rozliczeń platformy Azure. Zobacz [Interfejs API użycia zasobów platformy Azure (Wersja zapoznawcza)](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak zmienić rozmiar wdrożonej maszyny Wirtualnej usługi w chmurze bez ponownego wdrażania?
Nie można zmienić rozmiaru maszyny Wirtualnej wdrożonej usługi w chmurze bez ponownego wdrożenia. Rozmiar maszyny Wirtualnej jest wbudowany w CSDEF, który można zaktualizować tylko za pomocą ponownego rozmieszczenia.

Aby uzyskać więcej informacji, zobacz [Jak zaktualizować usługę w chmurze](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Dlaczego nie mogę wdrożyć usług w chmurze za pośrednictwem interfejsów API zarządzania usługami lub programu PowerShell podczas korzystania z konta usługi Azure Resource Storage? 

Ponieważ usługa w chmurze jest zasobem klasycznym, który nie jest bezpośrednio zgodny z modelem usługi Azure Resource Manager, nie można skojarzyć go z kontami usługi Azure Resource Manager Storage. Oto kilka opcji: 

- Wdrażanie za pośrednictwem interfejsu API REST.

    Podczas wdrażania za pośrednictwem interfejsu API REST zarządzania usługami można obejść ograniczenie, określając adres URL sygnatury dostępu Współdzielonego do magazynu obiektów blob, który będzie działał zarówno z kontem usługi Classic, jak i Usługi Azure Resource Manager Storage. Przeczytaj więcej o właściwości "PackageUrl" [tutaj](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Wdrażanie za pośrednictwem [witryny Azure portal](https://portal.azure.com).

    Będzie to działać z [witryny Azure portal,](https://portal.azure.com) jak wywołanie przechodzi przez serwer proxy/podkładka, która umożliwia komunikację między usługi Azure Resource Manager i zasobów klasycznych. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Dlaczego witryna Azure portal wymaga ode mnie udostępnienia konta magazynu do wdrożenia?

W klasycznym portalu pakiet został przekazany bezpośrednio do warstwy interfejsu API zarządzania, a następnie warstwa interfejsu API tymczasowo umieścić pakiet na koncie magazynu wewnętrznego.  Ten proces powoduje problemy z wydajnością i skalowalnością, ponieważ warstwa interfejsu API nie została zaprojektowana jako usługa przekazywania plików.  W witrynie Azure portal (model wdrażania usługi Resource Manager) ominieśmy tymczasowy krok pierwszego przekazywania do warstwy interfejsu API, co powoduje szybsze i bardziej niezawodne wdrożenia.

Jeśli chodzi o koszt, jest bardzo mały i można ponownie użyć tego samego konta magazynu we wszystkich wdrożeniach. [Kalkulator kosztów magazynowania](https://azure.microsoft.com/pricing/calculator/#storage1) służy do określenia kosztu przekazania pakietu usług (CSPKG), pobrania CSPKG, a następnie usunięcia CSPKG.
