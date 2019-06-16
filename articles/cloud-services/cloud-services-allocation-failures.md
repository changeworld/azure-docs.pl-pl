---
title: Rozwiązywanie problemów z błędami alokacji usługi w chmurze | Dokumentacja firmy Microsoft
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
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d1f24c3661a23496d1873f12ce46083bf5258269
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61435512"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Rozwiązywanie problemów z błędami alokacji podczas wdrażania usługi Cloud Services na platformie Azure
## <a name="summary"></a>Podsumowanie
Podczas wdrażania wystąpienia usługi w chmurze lub Dodaj nową sieć web lub wystąpienia roli procesu roboczego, Microsoft Azure przydziela zasoby obliczeniowe. Od czasu do czasu mogą występować błędy, podczas wykonywania tych operacji, nawet w przypadku, zanim osiągną limity subskrypcji platformy Azure. W tym artykule opisano przyczyny niektórych typowych błędów alokacji i sugeruje możliwe rozwiązania. Informacje również mogą być przydatne podczas planowania wdrożenia usługi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Tło — jak działa alokacji
Serwery w centrach danych platformy Azure są partycjonowane na klastry. Nowe żądanie alokacji usługi cloud zostanie podjęta w wielu klastrach. Podczas wdrażania pierwszego wystąpienia usługi w chmurze (w tymczasowym lub produkcyjnym), który usługa w chmurze pobiera przypięte do klastra. Wszelkie dalsze wdrożenia usługi w chmurze będzie miało miejsce, w tym samym klastrze. W tym artykule firma Microsoft będzie można się odwoływać do tego jako "przypięte do klastra". 1 poniżej diagramie przypadku normalnych alokacji, która zostanie podjęta w wielu klastrach; Diagram 2 ilustruje przypadek alokacji, został przypięty do klastra 2, ponieważ hostujące istniejących CS_1 usługi w chmurze.

![Diagram alokacji](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Dlaczego alokacji awaria
Gdy żądanie alokacji zostanie przypięty do klastra ma wyższe prawdopodobieństwo, których nie można odnaleźć bezpłatnych zasobów, ponieważ pula dostępnych zasobów jest ograniczona do klastra. Ponadto żądania alokacji jest przypięta do klastra, ale typ zasobu, której szukasz nie jest obsługiwana przez ten klaster, żądanie zakończy się niepowodzeniem nawet wtedy, gdy klaster jest bezpłatnym zasobem. Diagram 3 poniżej przedstawia przypadek, w którym przypiętych alokacja nie powiedzie się, ponieważ klaster tylko kandydujące nie ma bezpłatnymi zasobami. Diagram 4 ilustruje przypadek, gdzie przypiętych alokacji nie działa, ponieważ klaster tylko Release candidate nie obsługuje żądanego rozmiaru maszyny Wirtualnej, mimo że klaster ma bezpłatnymi zasobami.

![Niepowodzenie alokacji przypięte](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Rozwiązywanie problemów z błędami alokacji usługi cloud Services
### <a name="error-message"></a>Komunikat o błędzie
Może zostać wyświetlony następujący komunikat o błędzie:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Typowe problemy
Poniżej przedstawiono typowe scenariusze alokacji, powodujące wysłanie żądania alokacji do można przypiąć do jednego klastra.

* Wdrażanie do miejsca przejściowego — Jeśli usługa w chmurze ma wdrożenia w dowolnym miejscu, następnie usługi w chmurze całego został przypięty do określonego klastra.  Oznacza to, że jeśli wdrożenie już istnieje w miejscu produkcyjnym, nowe wdrożenie przejściowe może zostać przydzielone tylko w tym samym klastrze co używany dla miejsca produkcyjnego. Jeśli klaster zbliża się, żądanie może zakończyć się niepowodzeniem.
* Skalowanie — Dodawanie nowych wystąpień do istniejącej usługi w chmurze należy przydzielić w tym samym klastrze.  Alokacja dla niewielkich żądań skalowania zwykle jest możliwa, ale nie zawsze. Jeśli klaster zbliża się, żądanie może zakończyć się niepowodzeniem.
* Grupa koligacji — nowe wdrożenie usługi w chmurze pusty mogą zostać przydzieleni przez sieci szkieletowej w programie dowolnego klastra w danym regionie, chyba że usługi w chmurze jest przypięte do grupy koligacji. Zostanie podjęta próba wdrożenia w tej samej grupie koligacji, w tym samym klastrze. Jeśli klaster zbliża się, żądanie może zakończyć się niepowodzeniem.
* Grupa koligacji vNet - starszych sieci wirtualnych były powiązane z grup koligacji zamiast regionów i usług w chmurze w tych sieciach wirtualnych będzie można przypiąć do klastra grupy koligacji. Sieć wirtualna tego typu wdrożenia zostanie podjęta w klastrze przypięty. Jeśli klaster zbliża się, żądanie może zakończyć się niepowodzeniem.

## <a name="solutions"></a>Rozwiązania
1. Ponowne wdrożenie nowej usługi w chmurze — to rozwiązanie jest może być najbardziej popularnych, ponieważ pozwala ono platformy wybrać ze wszystkich klastrów w tym regionie.

   * Wdrażanie obciążeń do nowej usługi w chmurze  
   * Zaktualizuj rekord CNAME lub rekordu, aby przesyłały ruch do nowej usługi w chmurze
   * Po zero ruch przechodzi do starej witryny, można usunąć starego usługi w chmurze. To rozwiązanie powinno pociągnąć za sobą przestojów.
2. Usuń produkcyjne oraz przejściowe miejsce — to rozwiązanie będzie zachować istniejącą nazwę DNS, ale spowoduje, że przestojów aplikacji.

   * Usuń produkcyjne i przejściowe miejsc istniejącej usługi w chmurze, tak aby usługi w chmurze jest pusta, a następnie
   * Utwórz nowe wdrożenie w istniejącej usługi w chmurze. Spowoduje to ponowne podjęcie próby alokacji na wszystkich klastrach w regionie. Upewnij się, że usługa w chmurze nie jest związany z grupy koligacji.
3. Zastrzeżony adres IP — to rozwiązanie zachowa istniejące IP adresów, ale spowoduje, że przestojów aplikacji.  

   * Tworzenie zastrzeżonego adresu IP dla istniejącego wdrożenia przy użyciu programu Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Postępuj zgodnie z #2 powyższej, upewniając się określić nowy zastrzeżony adres IP w pliku CSCFG tej usługi.
4. Usuń grupę koligacji dla nowych wdrożeń - grupy koligacji są już zalecane. Wykonaj kroki, aby #1 powyżej, aby wdrożyć nową usługę w chmurze. Upewnij się, usługa w chmurze nie znajduje się w grupie koligacji.
5. Konwertuj na regionalnej sieci wirtualnej — zobacz [jak przeprowadzić migrację z grupy koligacji do regionalnej sieci wirtualnej (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
