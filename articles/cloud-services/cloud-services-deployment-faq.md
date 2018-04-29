---
title: Problemy z wdrażaniem Microsoft Azure Cloud Services często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących wdrażania usługi w chmurze Microsoft Azure.
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
ms.date: 12/12/2017
ms.author: genli
ms.openlocfilehash: effc0eb872a1f63e32c1958234c173a53c381620
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z wdrażaniem usług Azure Cloud Services: często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące problemów dotyczących wdrożenia dla [usługi w chmurze Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Można również znaleźć [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) dla informacji o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Dlaczego czasami wdrażania usługi w chmurze na miejsce przemieszczania nie powiodło się z powodu błędu alokacji zasobów Jeśli istnieje już istniejące wdrożenie w gnieździe produkcyjnym?
Jeśli usługa w chmurze ma wdrożenia w każdym miejscu, usługa w chmurze całego jest przypięta do określonego klastra. Oznacza to, że jeśli wdrożenia już istnieje w miejscu produkcyjnym, nowe wdrożenie przemieszczania może zostać przydzielone tylko w tym samym klastrze miejsca produkcji.

Błędy alokacji wystąpić, gdy klaster, w którym znajduje się usługa w chmurze nie ma wystarczającej ilości zasobów obliczeniowych fizycznych można zrealizować żądania wdrożenia.

Aby uzyskać pomoc zmniejszenia takie błędy alokacji, zobacz [błąd alokacji usługi w chmurze: rozwiązań](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Dlaczego skalowanie w i skalowania w poziomie wdrożenie usługi chmury jest czasem spowodować niepowodzenie alokacji?
Po wdrożeniu usługi w chmurze on zazwyczaj pobiera przypięty do określonego klastra. Oznacza to, że skalowanie w górę/out istniejącą usługę w chmurze należy przydzielić nowych wystąpień w tym samym klastrze. Jeśli klaster jest bliskie pojemności lub żądany rozmiar maszyny Wirtualnej/typ nie jest dostępna, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc zmniejszenia takie błędy alokacji, zobacz [błąd alokacji usługi w chmurze: rozwiązań](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Dlaczego wdrażania usługi w chmurze w grupie koligacji czasami powoduje błąd alokacji?
Nowe wdrożenie do usługi w chmurze pusty mogą zostać przydzieleni przez sieci szkieletowej w programie dowolnego klastra w danym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. Wdrożenia w tej samej grupie koligacji będą podejmowane w tym samym klastrze. Jeśli klaster jest bliskie pojemności, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc zmniejszenia takie błędy alokacji, zobacz [błąd alokacji usługi w chmurze: rozwiązań](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Dlaczego zmiana rozmiaru maszyny Wirtualnej lub Dodawanie nowej maszyny Wirtualnej do istniejącej usługi w chmurze czasami powoduje błąd alokacji?
Klastry w centrum danych mogą mieć różne konfiguracje typów maszyny (np. serii Av2 serii, D serii, Dv2 serii, serii G, H serii, itp.). Ale nie wszystkich klastrach niekoniecznie musi wszelkiego rodzaju maszyn wirtualnych. Na przykład Jeśli spróbujesz dodać serię D maszyny Wirtualnej do usługi w chmurze, która została już wdrożona w klastrze tylko do serii A wystąpią błąd alokacji. To również nastąpi próba zmiany się, że rozmiar jednostki SKU maszyny Wirtualnej (np. zmiana typu zapytania z serii A do serii D).

Aby uzyskać pomoc zmniejszenia takie błędy alokacji, zobacz [błąd alokacji usługi w chmurze: rozwiązań](cloud-services-allocation-failures.md#solutions).

Aby sprawdzić dostępne rozmiary w danym regionie, zobacz [Microsoft Azure: produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Dlaczego jakiś czas wdrażania usługi w chmurze nie powiodło się z powodu ograniczeń/przydziały/ograniczeń na subskrypcji lub usługi?
Wdrożenie usługi w chmurze może zakończyć się niepowodzeniem, jeśli zasoby, które są wymagane do przydzielenia przekracza domyślnych lub maksymalny limit przydziału, o których usługi na poziomie region/centrum danych. Aby uzyskać więcej informacji, zobacz [ogranicza usługi w chmurze](../azure-subscription-service-limits.md#cloud-services-limits).

Można także śledzić bieżącego użycia/przydziału dla Twojej subskrypcji w portalu: Azure portal = > Subskrypcje = > \<odpowiednie subskrypcji > = > "Użycia + limitu przydziału".

Można również pobrać informacji związanych z użycia/zużycie zasobów za pośrednictwem interfejsów API usługi Azure rozliczeń. Zobacz [użycia zasobów platformy Azure (wersja zapoznawcza) interfejsu API](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak zmienić rozmiar usługi w chmurze wdrożonej maszyny Wirtualnej bez jego ponowne wdrożenie?
Nie można zmienić rozmiaru maszyny Wirtualnej usługi w chmurze wdrożonej bez jego ponowne wdrożenie. Rozmiar maszyny Wirtualnej jest wbudowana w CSDEF, które można zaktualizować tylko z Wdróż go ponownie.

Aby uzyskać więcej informacji, zobacz [aktualizacji usługi w chmurze](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Dlaczego nie mogę wdrożyć usługi w chmurze za pośrednictwem interfejsów API usługi Service Management lub programu PowerShell, korzystając z konta usługi Magazyn Azure Resource Manager? 

Ponieważ usługa w chmurze jest zasobem klasycznym, które nie są bezpośrednio zgodne z modelem usługi Azure Resource Manager, nie można skojarzyć go z kontami magazynu Azure Resource Manager. Poniżej przedstawiono kilka opcji: 
 
- Wdrażanie za pośrednictwem interfejsu API REST.

    Podczas wdrażania za pomocą interfejsu API REST zarządzania usługi można pobrać wokół ograniczenia, określając adres URL SAS do magazynu obiektów blob, który będzie działać z zarówno Classic i Azure Resource Manager konto magazynu. Przeczytaj więcej na temat właściwości "PackageUrl" [tutaj](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Wdrażanie za pośrednictwem [portalu Azure](https://portal.azure.com).

    To będzie działać z [portalu Azure](https://portal.azure.com) , ponieważ wywołanie przechodzi przez serwer proxy/podkładki, która umożliwia komunikację między zasobami usługi Azure Resource Manager i model klasyczny. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Dlaczego portalu Azure wymaga mnie o podanie konta magazynu dla wdrożenia? 

W klasycznym portalu pakiet został przekazany do warstwy interfejsu API zarządzania bezpośrednio, a następnie warstwę interfejsu API tymczasowo czy umieść pakiet do konta wewnętrznego magazynu.  Ten proces powoduje problemy z wydajnością i skalowalnością, ponieważ warstwę interfejsu API nie został zaprojektowany jako usługa przekazywania plików.  W portalu Azure (modelu wdrażania usługi Resource Manager) firma Microsoft ma pominąć tymczasowe kroku wcześniejszego przekazania do warstwy interfejsu API, co w przypadku wdrożeń szybszy i bardziej niezawodny. 

Podobnie jak w przypadku koszt jest bardzo mała i we wszystkich wdrożeniach można ponownie użyć tego samego konta magazynu. Można użyć [Kalkulator magazynu koszt](https://azure.microsoft.com/pricing/calculator/#storage1) ustalić koszt do przekazania pakietu usługi (CSPKG), Pobierz CSPKG, a następnie usuń CSPKG. 
