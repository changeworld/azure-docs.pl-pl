---
title: Rozwiązywanie problemów z niepowodzeniem alokacji usługi w chmurze | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z błędami alokacji podczas wdrażania usługi Cloud Services na platformie Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247542"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Rozwiązywanie problemów z błędami alokacji podczas wdrażania usługi Cloud Services na platformie Azure
## <a name="summary"></a>Podsumowanie
Podczas wdrażania wystąpień w usłudze w chmurze lub dodawania nowych wystąpień roli sieci Web lub procesu roboczego platforma Microsoft Azure przydziela zasoby obliczeniowe. Czasami mogą pojawić się błędy podczas wykonywania tych operacji, nawet przed osiągnięciem limitów subskrypcji platformy Azure. W tym artykule wyjaśniono przyczyny niektórych typowych błędów alokacji i sugeruje możliwe korygowanie. Informacje mogą być również przydatne podczas planowania wdrażania usług.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Kontekst – jak działa alokacja
Serwery w centrach danych platformy Azure są partycjonowane na klastry. W wielu klastrach podejmowana jest próba nowego żądania alokacji usługi w chmurze. Gdy pierwsze wystąpienie jest wdrażane w usłudze w chmurze (w przemieszczania lub produkcji), że usługa w chmurze zostanie przypięty do klastra. Wszelkie dalsze wdrożenia dla usługi w chmurze będą miały miejsce w tym samym klastrze. W tym artykule będziemy odnosić się do tego jako "przypięty do klastra". Wykres 1 poniżej ilustruje przypadek normalnej alokacji, która jest podejmowana w wielu klastrach; Diagram 2 ilustruje przypadek alokacji, która jest przypięta do klastra 2, ponieważ to gdzie znajduje się istniejący CS_1 usługi w chmurze.

![Diagram alokacji](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Dlaczego występuje awaria alokacji
Gdy żądanie alokacji jest przypięte do klastra, istnieje większa szansa na nieznalezienie wolnych zasobów, ponieważ dostępna pula zasobów jest ograniczona do klastra. Ponadto jeśli żądanie alokacji jest przypięte do klastra, ale żądany typ zasobu nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem, nawet jeśli klaster ma wolny zasób. Diagram 3 poniżej ilustruje przypadek, w którym przypięty alokacji nie powiedzie się, ponieważ tylko klaster kandydata nie ma wolnych zasobów. Diagram 4 ilustruje przypadek, w którym przypięta alokacja kończy się niepowodzeniem, ponieważ tylko klaster kandydata nie obsługuje żądanego rozmiaru maszyny Wirtualnej, nawet jeśli klaster ma wolne zasoby.

![Niepowodzenie przypiętej alokacji](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Rozwiązywanie problemów z błędem alokacji usług w chmurze
### <a name="error-message"></a>Komunikat o błędzie
Może zostać wyświetlony następujący komunikat o błędzie:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Typowe problemy
Poniżej przedstawiono typowe scenariusze alokacji, które powodują przypięte żądanie alokacji do pojedynczego klastra.

* Wdrażanie na miejscu przejściowym — jeśli usługa w chmurze ma wdrożenie w każdym miejscu, cała usługa w chmurze jest przypięta do określonego klastra.  Oznacza to, że jeśli wdrożenie już istnieje w miejscu produkcyjnym, nowe wdrożenie przejściowe może zostać przydzielone tylko w tym samym klastrze co używany dla miejsca produkcyjnego. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Skalowanie — dodawanie nowych wystąpień do istniejącej usługi w chmurze wymaga alokacji w tym samym klastrze.  Alokacja dla niewielkich żądań skalowania zwykle jest możliwa, ale nie zawsze. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Grupa koligacji — nowe wdrożenie do pustej usługi w chmurze mogą być przydzielane przez sieci szkieletowej w dowolnym klastrze w tym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. Wdrożenia do tej samej grupy koligacji będą podejmowane w tym samym klastrze. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.
* Sieć wirtualna grupy koligacji — starsze sieci wirtualne były powiązane z grupami koligacji zamiast regionów, a usługi w chmurze w tych sieciach wirtualnych były przypięte do klastra grupy koligacji. Wycenie w tej sieci wirtualnej zostanie podjęta próba przypiętego klastra. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.

## <a name="solutions"></a>Rozwiązania
1. Ponowne wdrożenie do nowej usługi w chmurze — to rozwiązanie może być najbardziej skuteczne, ponieważ umożliwia platformie wybór spośród wszystkich klastrów w tym regionie.

   * Wdrażanie obciążenia w nowej usłudze w chmurze  
   * Aktualizowanie rekordu CNAME lub A w celu wskazyowania ruchu w nowej usłudze w chmurze
   * Gdy zerowy ruch będzie do starej witryny, można usunąć starą usługę w chmurze. To rozwiązanie powinno spowodować zerowy czas przestoju.
2. Usuń zarówno miejsca produkcyjne, jak i tymczasowe — to rozwiązanie zachowa istniejącą nazwę DNS, ale spowoduje przestoje aplikacji.

   * Usuwanie miejsc produkcyjnych i przejściowych istniejącej usługi w chmurze, tak aby usługa w chmurze była pusta, a następnie
   * Utwórz nowe wdrożenie w istniejącej usłudze w chmurze. Spowoduje to ponowną próbę alokacji na wszystkich klastrach w regionie. Upewnij się, że usługa w chmurze nie jest powiązana z grupą koligacji.
3. Zastrzeżony adres IP — to rozwiązanie spowoduje zachowanie istniejącego adresu IP, ale spowoduje przestoje aplikacji.  

   * Tworzenie programu ReservedIP dla istniejącego wdrożenia przy użyciu programu Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Postępuj zgodnie #2 z góry, upewniając się, aby określić nowy ReservedIP w cscfg usługi.
4. Usuń grupę koligacji dla nowych wdrożeń — grupy koligacji nie są już zalecane. Wykonaj czynności opisane w punkcie 1 powyżej, aby wdrożyć nową usługę w chmurze. Upewnij się, że usługa w chmurze nie znajduje się w grupie koligacji.
5. Konwertuj na regionalną sieć wirtualną — zobacz [Jak przeprowadzić migrację z grup koligacji do regionalnej sieci wirtualnej .](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
