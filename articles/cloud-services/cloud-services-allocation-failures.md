---
title: Rozwiązywanie problemów z przydziałem usługi w chmurze | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędami alokacji podczas wdrażania usługi Cloud Services na platformie Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 33d017d0e09e9b288b0514e85c8865f83a8a2fa1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984120"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Rozwiązywanie problemów z błędami alokacji podczas wdrażania usługi Cloud Services na platformie Azure
## <a name="summary"></a>Podsumowanie
Podczas wdrażania wystąpienia usługi w chmurze lub dodać nową sieć web lub wystąpień roli procesu roboczego, Microsoft Azure przydziela zasoby obliczeniowe. Czasami może pojawić błędy podczas wykonywania tych operacji nawet zanim przejdziesz limity subskrypcji platformy Azure. W tym artykule opisano przyczyny niektórych typowych błędów alokacji i sugeruje możliwe korygowania. Informacje również mogą być przydatne podczas planowania wdrożenia usługi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Tło — jak działa alokacji
Serwery w centrach danych platformy Azure są dzielone w klastrach. Nowe żądanie alokacji usługi chmury nastąpiła w wielu klastrach. Podczas wdrażania usługi w chmurze (w albo tymczasowym czy produkcyjnym), który usługa w chmurze początkowo pobiera przypięty do klastra. Wszystkie dodatkowe wdrożenia usługi w chmurze nastąpi w tym samym klastrze. W tym artykule firma Microsoft będzie odwoływać się do tego, jak "przypięty do klastra". Poniższy diagram 1 przedstawiono w przypadku normalnych alokacji, która nastąpiła w wielu klastrach; Diagram 2 przedstawia to alokacji który zawiera przypięta do klastra 2, ponieważ jest to, gdzie jest hostowana istniejących CS_1 usługi w chmurze.

![Diagram alokacji](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Dlaczego sytuacji błąd alokacji
Żądanie alokacji jest przypięta do klastra, ma wyższy stopień można znaleźć zwolnić zasoby, ponieważ pula zasobów dostępny jest ograniczona do klastra. Ponadto jeśli żądanie alokacji jest przypięta do klastra, ale typ zasobu, która miała nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem nawet wtedy, gdy klaster ma bezpłatnego zasobu. Poniższy diagram 3 ilustruje przypadek, w których alokacji przypiętych nie powiedzie się, ponieważ klaster tylko kandydujące nie ma zwolnić zasoby. Diagram 4 przedstawia przypadek, w którym przypiętych alokacji nie działa, ponieważ klastra tylko kandydata nie obsługuje żądany rozmiar maszyny Wirtualnej, nawet jeśli klaster ma zwolnić zasoby.

![Błąd alokacji przypiętych](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Rozwiązywanie problemów z błędami alokacji dla usług w chmurze
### <a name="error-message"></a>Komunikat o błędzie
Może zostać wyświetlony następujący komunikat o błędzie:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Typowe problemy
Poniżej przedstawiono typowe scenariusze alokacji, które powodują żądanie alokacji przypięty do jednego klastra.

* Wdrażanie do miejsca przemieszczania — Jeśli usługa w chmurze ma wdrożenia w każdym miejscu, następnie usługa w chmurze całego jest przypięta do określonego klastra.  Oznacza to, że jeśli wdrożenia już istnieje w miejscu produkcyjnym, następnie nowe wdrożenie przemieszczania może zostać przydzielone tylko w tym samym klastrze miejsca produkcji. Jeśli klaster jest bliskie pojemności, żądanie może zakończyć się niepowodzeniem.
* Skalowanie — Dodawanie nowych wystąpień do istniejącej usługi w chmurze należy przydzielić w tym samym klastrze.  Zazwyczaj można przydzielić pomniejszonego skalowanie żądania, ale nie zawsze. Jeśli klaster jest bliskie pojemności, żądanie może zakończyć się niepowodzeniem.
* Grupa koligacji — nowe wdrożenie do usługi w chmurze pusty mogą zostać przydzieleni przez sieci szkieletowej w programie dowolnego klastra w danym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. Wdrożenia w tej samej grupie koligacji będą podejmowane w tym samym klastrze. Jeśli klaster jest bliskie pojemności, żądanie może zakończyć się niepowodzeniem.
* Grupy koligacji vNet - starszych sieci wirtualnych zostały powiązane grup koligacji zamiast regionów, a usługi w chmurze w tych sieciach wirtualnych czy przypięty do klastra grupy koligacji. Sieć wirtualna tego typu wdrożenia będą podejmowane w klastrze przypięty. Jeśli klaster jest bliskie pojemności, żądanie może zakończyć się niepowodzeniem.

## <a name="solutions"></a>Rozwiązania
1. Wdrożenie nowej usługi w chmurze — to rozwiązanie jest może być najbardziej popularnych, który zezwala platformy wybierz ze wszystkich klastrów w tym regionie.

   * Wdrażanie obciążenie na nową usługę w chmurze  
   * Zaktualizuj CNAME lub rekord, aby przesyłały ruch do nowej usługi w chmurze
   * Po zero ruch będzie starej lokacji, można usunąć starego usługi w chmurze. To rozwiązanie powinno spowodować przestojów.
2. Usuń zarówno produkcyjne i przejściowe miejsc — to rozwiązanie zachowa istniejącą nazwę DNS, ale spowoduje, że przestój do aplikacji.

   * Usuń produkcyjne i przejściowe miejsc istniejącą usługę w chmurze, dzięki czemu usługa w chmurze jest pusta, a następnie
   * Utwórz nowe wdrożenie w istniejącej usłudze w chmurze. To spowoduje próbę ponownego alokacji na wszystkich klastrach w regionie. Upewnij się, że usługa w chmurze nie są powiązane z grupą koligacji.
3. Zastrzeżony adres IP — to rozwiązanie zachowa IP istniejącego rozwiązania, ale spowoduje, że przestój do aplikacji.  

   * Tworzenie zastrzeżonego adresu IP istniejącego wdrożenia przy użyciu programu Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Wykonaj #2 powyższego, upewniając się określić nowy zastrzeżony adres IP w CSCFG usługi.
4. Usuń grupę koligacji dla nowych wdrożeń - grup koligacji nie są zalecane. Wykonaj kroki #1 powyżej, aby wdrożyć nową usługę w chmurze. Upewnij się, usługa w chmurze nie jest w grupie koligacji.
5. Konwertuj na regionalną sieć wirtualną — zobacz [jak przeprowadzić migrację z grup koligacji do regionalną sieć wirtualną (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
