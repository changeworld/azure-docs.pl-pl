---
title: Powiadomienia dotyczące konserwacji
description: Omówienie powiadomień o konserwacji maszyn wirtualnych działających na platformie Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115683"
---
# <a name="handling-planned-maintenance-notifications"></a>Obsługa powiadomień o planowanej konserwacji

Platforma Azure jest co pewien czas aktualizowana w celu poprawy niezawodności, wydajności i zabezpieczeń infrastruktury hostowania maszyn wirtualnych. Aktualizacje to zmiany, takie jak instalowanie poprawek do środowiska hostingowego lub uaktualnianie i likwidacja sprzętu. Większość z tych aktualizacji są wykonywane bez wpływu na hostowanych maszyn wirtualnych. Istnieją jednak przypadki, w których aktualizacje mają wpływ:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure używa migracji w miejscu, aby wstrzymać maszynę wirtualną podczas aktualizacji hosta. Operacje konserwacji tych typów są stosowane domeny błędów przez domenę błędów. Postęp jest zatrzymywany w przypadku odbioru jakichkolwiek sygnałów ostrzegawczych.

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o planowanej konserwacji. Otrzymujesz przedział czasu około 35 dni, w którym możesz samodzielnie rozpocząć konserwację, gdy działa to dla Ciebie.


Planowana konserwacja, która wymaga ponownego uruchomienia jest zaplanowana w fale. Każda fala ma inny zakres (regiony).

- Fala rozpoczyna się od powiadomienia do klientów. Domyślnie powiadomienie jest wysyłane do administratora usługi i współadministratorów. Możesz dodać więcej adresatów i opcji wiadomości, takich jak wiadomości e-mail, SMS i webhook, korzystając z [alertów dziennika aktywności](../service-health/alerts-activity-log-service-notifications.md).  
- Po wyjęciu powiadomienia zostanie udostępnione *okno samoobsługowe.* W tym oknie można zbadać, które z maszyn wirtualnych są zagrożone i rozpocząć konserwację na podstawie własnych potrzeb planowania. Okno samoobsługowe jest zazwyczaj około 35 dni.
- Po oknie samoobsługowym rozpoczyna *się okno zaplanowanej konserwacji.* W pewnym momencie w tym oknie platformy Azure planuje i stosuje wymaganą konserwację do maszyny wirtualnej. 

Celem posiadania dwóch okien jest zapewnienie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny wirtualnej, wiedząc jednocześnie, kiedy platforma Azure automatycznie rozpocznie konserwację.


Za pomocą witryny Azure portal, powershell, rest api i interfejsu wiersza polecenia można zbadać okna obsługi maszyn wirtualnych i rozpocząć samoobsługową konserwację.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Czy należy rozpocząć konserwację podczas okna samoobsługowego?  

Poniższe wskazówki powinny pomóc w podjęciu decyzji, czy korzystać z tej możliwości i rozpocząć konserwację w swoim własnym czasie. 

> [!NOTE] 
> Samoobsługowa konserwacja może nie być dostępna dla wszystkich maszyn wirtualnych. Aby ustalić, czy proaktywne ponowne wdrożenie jest dostępne dla maszyny Wirtualnej, **poszukaj start teraz** w stanie konserwacji. Konserwacja samoobsługowa jest obecnie niedostępna dla usług w chmurze (rola sieci Web/proces roboczy) i sieci szkieletowej usług.


Konserwacja samoobsługowa nie jest zalecana w przypadku wdrożeń przy użyciu **zestawów dostępności**. Zestawy dostępności są już aktualizowane tylko w jednej domenie aktualizacji naraz. 

- Pozwól platformie Azure wyzwolić konserwację. W przypadku konserwacji, która wymaga ponownego uruchomienia, konserwacja zostanie wykonana domeny aktualizacji przez domenę aktualizacji. Domeny aktualizacji nie muszą otrzymywać konserwacji sekwencyjnie i że istnieje 30-minutowa przerwa między domenami aktualizacji. 
- Jeśli tymczasowa utrata niektórych pojemności (1 domena aktualizacji) jest problemem, można dodać wystąpienia w okresie konserwacji. 
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów. 

**Nie używaj** samoobsługowej konserwacji w następujących scenariuszach: 
- Jeśli często zamykasz maszyny wirtualne, ręcznie, używając DevTest Labs, korzystając z automatycznego zamykania lub zgodnie z harmonogramem, może to przywrócić stan konserwacji i w związku z tym spowodować dodatkowe przestoje.
- Na krótkotrwałych maszyn wirtualnych, które wiesz, zostaną usunięte przed końcem fali konserwacji. 
- Dla obciążeń o dużym stanie przechowywanych na dysku lokalnym (efemeryczny), który jest pożądany do utrzymania po aktualizacji. 
- W przypadkach, gdy często można zmienić rozmiar maszyny Wirtualnej, ponieważ może przywrócić stan konserwacji. 
- Jeśli przyjęto zaplanowane zdarzenia, które umożliwiają proaktywne tryb failover lub wdzięczne zamknięcie obciążenia, 15 minut przed rozpoczęciem zamykania konserwacji

**Użyj** samoobsługowej konserwacji, jeśli planujesz nieprzerwane uruchamianie maszyny Wirtualnej podczas fazy zaplanowanej konserwacji i nie ma zastosowania żadna z wyżej wymienionych przeciwwskazań. 

Najlepiej jest korzystać z samoobsługowej konserwacji w następujących przypadkach:
- Musisz poinformować o dokładnym oknie konserwacji do zarządzania lub klienta końcowego. 
- Należy zakończyć konserwację w określonym terminie. 
- Należy kontrolować sekwencję konserwacji, na przykład wielowarstwowej aplikacji, aby zagwarantować bezpieczne odzyskiwanie.
- Ponad 30 minut czasu odzyskiwania maszyny Wirtualnej jest potrzebne między dwiema domenami aktualizacji (UDs). Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację na maszynach wirtualnych jedną domenę aktualizacji (UD) naraz.


## <a name="faq"></a>Najczęściej zadawane pytania


**Pyt.: Dlaczego musisz teraz ponownie uruchomić moje maszyny wirtualne?**

**Odp.:** Podczas gdy większość aktualizacji i uaktualnień do platformy Azure nie mają wpływu na dostępność maszyny wirtualnej, istnieją przypadki, w których nie możemy uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zgromadziliśmy kilka zmian, które wymagają od nas ponownego uruchomienia naszych serwerów, co spowoduje ponowne uruchomienie maszyn wirtualnych.

**Pyt.: Jeśli przestrzegam zaleceń dotyczących wysokiej dostępności przy użyciu zestawu dostępności, czy jestem bezpieczny?**

**Odp.:** Maszyny wirtualne wdrożone w zestawie dostępności lub zestawach skalowania maszyny wirtualnej mają pojęcie Aktualizuj domeny (UD). Podczas wykonywania konserwacji platforma Azure honoruje ograniczenie UD i nie będzie ponownie uruchamiać maszyn wirtualnych z różnych UD (w ramach tego samego zestawu dostępności).  Platforma Azure czeka również co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [Dostępność maszyn wirtualnych na platformie Azure](./linux/availability.md).

**P: Jak uzyskać powiadomienie o planowanej konserwacji?**

**Odp.:** Planowana fala konserwacji rozpoczyna się od ustawienia harmonogramu na co najmniej jeden region platformy Azure. Wkrótce potem powiadomienie e-mail jest wysyłane do administratorów subskrypcji (jedna wiadomość e-mail na subskrypcję). Dodatkowe kanały i adresaci dla tego powiadomienia można skonfigurować przy użyciu alertów dziennika aktywności. W przypadku wdrożenia maszyny wirtualnej w regionie, w którym planowana konserwacja jest już zaplanowana, nie otrzymasz powiadomienia, ale raczej trzeba sprawdzić stan konserwacji maszyny wirtualnej.

**P: Nie widzę żadnych oznak planowanej konserwacji w portalu, programie Powershell lub cli. Co jest nie tak?**

**Odp.:** Informacje związane z planowaną konserwacją są dostępne podczas planowanej fali konserwacji tylko dla maszyn wirtualnych, które będą miały na nią wpływ. Innymi słowy, jeśli nie widzisz danych, może to być, że fala konserwacji została już ukończona (lub nie została uruchomiona) lub że maszyna wirtualna jest już hostowana na zaktualizowanym serwerze.

**Pyt.: Czy istnieje sposób, aby wiedzieć dokładnie, kiedy moja maszyna wirtualna będzie mieć wpływ?**

**Odp.:** Podczas ustawiania harmonogramu definiujemy przedział czasu kilkudniowy. Jednak dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie jest nieznany. Klienci, którzy chcieliby poznać dokładny czas dla swoich maszyn wirtualnych, mogą używać [zaplanowanych zdarzeń](./linux/scheduled-events.md) i zapytań z poziomu maszyny wirtualnej i otrzymywać 15-minutowe powiadomienie przed ponownym uruchomieniem maszyny Wirtualnej.

**Pyt.: Jak długo zajmie Ci ponowne uruchomienie mojej maszyny wirtualnej?**

**Odp.:**  W zależności od rozmiaru maszyny Wirtualnej ponowne uruchomienie może potrwać do kilku minut podczas okna samoobsługowej konserwacji. Podczas uruchamiania zainicjowane platformy Azure w oknie zaplanowanej konserwacji, ponowne uruchomienie zwykle zajmie około 25 minut. Należy zauważyć, że w przypadku korzystania z usług w chmurze (rola sieci Web/procesu roboczego), zestawy skalowania maszyny wirtualnej lub zestawy dostępności, zostanie podany 30 minut między każdą grupą maszyn wirtualnych (UD) podczas zaplanowanej konserwacji okna.

**Pyt.: Jakie środowisko jest w przypadku zestawów skalowania maszyny wirtualnej?**

**Odp.:** Planowana konserwacja jest teraz dostępna dla zestawów skalowania maszyny wirtualnej. Instrukcje dotyczące inicjowania samoobsługowej konserwacji można znaleźć w dokumencie [planowanej konserwacji zestawów skalowania maszyny wirtualnej.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**Pyt.: Jakie środowisko jest w przypadku usług w chmurze (rola sieci Web/pracownika) i sieci szkieletowej usług?**

**Odp.:** Podczas gdy na te platformy ma wpływ planowana konserwacja, klienci korzystający z tych platform są uważane za bezpieczne, biorąc pod uwagę, że tylko maszyny wirtualne w jednej domenie uaktualnienia (UD) będą miały wpływ w danym momencie. Konserwacja samoobsługowa jest obecnie niedostępna dla usług w chmurze (rola sieci Web/proces roboczy) i sieci szkieletowej usług.

**P: Nie widzę żadnych informacji o konserwacji na moich maszynach wirtualnych. Co poszło nie tak?**

**Odp.:** Istnieje kilka powodów, dla których nie widzisz żadnych informacji o konserwacji na maszynach wirtualnych:
1.  Używasz subskrypcji oznaczonej jako wewnętrzna firma Microsoft.
2.  Maszyny wirtualne nie są zaplanowane do konserwacji. Może się okazać, że fala konserwacji została zakończona, anulowana lub zmodyfikowana, aby maszyny wirtualne nie były już przez nią dotknięte.
3.  Nie masz kolumny **Konserwacja** dodane do widoku listy maszyny Wirtualnej. Podczas gdy dodaliśmy tę kolumnę do widoku domyślnego, klienci, którzy skonfigurowali wyświetlanie kolumn innych niż domyślne, muszą ręcznie dodać kolumnę **Konserwacja** do widoku listy maszyn wirtualnych.

**P: Moja maszyna wirtualna jest zaplanowana do konserwacji po raz drugi. Dlaczego?**

**Odp.:** Istnieje kilka przypadków użycia, w których zostanie wyświetlony maszyny Wirtualnej zaplanowane do konserwacji po zakończeniu konserwacji ponownego rozmieszczenia:
1.  Anulowaliśmy falę konserwacji i uruchomiliśmy ją ponownie z innym ładunkiem. Może to być, że wykryliśmy uszkodzony ładunek i po prostu musimy wdrożyć dodatkowy ładunek.
2.  Maszyna wirtualna została *wyleciona* do innego węzła z powodu usterki sprzętowej.
3.  Wybrano, aby zatrzymać (cofnięto przydział) i ponownie uruchomić maszynę wirtualną.
4.  Automatyczne **zamykanie** jest włączone dla maszyny Wirtualnej.



## <a name="next-steps"></a>Następne kroki

Można obsługiwać planowaną konserwację przy użyciu [interfejsu wiersza polecenia platformy Azure,](maintenance-notifications-cli.md) [programu Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu.](maintenance-notifications-portal.md)

