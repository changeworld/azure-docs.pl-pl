---
title: Usługa Azure Resource Health — często zadawane pytania | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: 03c5fb0f399e43af2584743655eeea8af4c5b2f5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924445"
---
# <a name="azure-resource-health-faq"></a>Usługa Azure Resource Health — często zadawane pytania
Dowiedz się, odpowiedzi na często zadawane pytania dotyczące usługi Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Co to jest Azure Resource Health?
Usługa Resource Health ułatwia diagnozowanie i uzyskanie pomocy technicznej, gdy problem z platformą Azure ma wpływ na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Co to jest przeznaczony Resource Health?
Po wykryciu problemu z zasobem, kondycja zasobu może pomóc zdiagnozować główną przyczynę. Zapewnia pomoc, aby rozwiązać problem i pomoc techniczna, gdy potrzebujesz więcej pomocy dotyczącej problemów z usługami platformy Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Kontrole kondycji, które są wykonywane przez Resource Health?
Funkcja kondycji zasobów sprawdza różne na podstawie [typ zasobu](resource-health-checks-resource-types.md). Te testy są przeznaczone do zaimplementowania trzy rodzaje problemów: 
- Nieplanowane zdarzenia, na przykład host nieoczekiwane ponowne uruchomienie komputera
- Planowane zdarzenia, takie jak aktualizacje zaplanowane systemu operacyjnego hosta.
- Zdarzenia wyzwalane przez działania użytkownika, na przykład użytkownika, ponowne uruchamianie maszyny wirtualnej

## <a name="what-does-each-of-the-health-status-mean"></a>Co każdy stan kondycji oznacza?
Istnieją trzy stany kondycji innego:
- Dostępne: Na platformie Azure, która może mieć wpływ na ten zasób nie ma żadnych znanych problemów
- Niedostępne: Usługa Resource health wykrył problemy mające wpływ na zasób
- Nieznane: Kondycja zasobu nie można określić kondycji zasobu, ponieważ została zatrzymana, odbieranie informacji o nim. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co oznacza nieznany stan Jest wystąpił problem z Mój zasób?
Stan kondycji ustawiono nieznany Resource Health zatrzymania odbieranie informacji dotyczących konkretnego zasobu. Gdy ten stan jest inny niż ostateczne wskazania stanu wszystkich zasobów, w przypadkach, w którym występują problemy, może to oznaczać, że występuje problem z platformy Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak uzyskać pomoc dla zasobu, który jest niedostępny
Możesz przesłać żądanie pomocy technicznej z poziomu bloku kondycja zasobu. Nie potrzebujesz umowy dotyczącej pomocy technicznej z firmą Microsoft, aby otworzyć żądanie, gdy zasób jest niedostępny ponieważ zdarzenia platformy.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Kondycja zasobu rozróżnienia między niedostępność z uwzględnieniem wielkości liter, problemów z platformą i coś, co musiałem zrobić?
Tak, gdy zasób jest niedostępny, usługa Resource Health identyfikuje główną przyczynę w jednej z tych kategorii: 
-   Akcji zainicjowanej przez użytkownika
-   Zdarzenie planowane 
-   Nieplanowane zdarzenia

W portalu akcji zainicjowanej przez użytkownika są wyświetlane, za pomocą ikony powiadomień niebieski, podczas planowanych i nieplanowanych zdarzeń są wyświetlane przy użyciu czerwona ikona ostrzeżenia. Szczegółowe informacje znajdują się w [Przegląd kondycji zasobów](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Za pomocą narzędzi do monitorowania można zintegrować Resource Health?
Kondycja zasobu ma [pomocy technicznej w wersji zapoznawczej](resource-health-alert-arm-template-guide.md) dla alertów opartych na dzienniku aktywności. Dziennik aktywności alerty użyj [grup akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) powiadomienie użytkowników, czy alert został wywołany. Grupy akcji obsługują różne kanały powiadomień, takie jak wiadomości e-mail, wiadomości SMS, element webhook i akcje ITSM.

## <a name="where-do-i-find-resource-health"></a>Gdzie znaleźć Resource Health?
Po zalogowaniu się do witryny Azure portal istnieje wiele sposobów, możesz uzyskać dostęp Resource Health:
- Przejdź do zasobu. W obszarze nawigacji po lewej stronie, wybierz **Resource health**
- Przejdź do bloku usługi Azure Service Health.  W obszarze nawigacji po lewej stronie, wybierz **Resource health**.
- Otwórz **Pomoc i obsługa techniczna** bloku, wybierając znak zapytania w prawym górnym rogu portalu, a następnie wybierając **Pomoc i obsługa techniczna**. Po otwarciu bloku wybierz **Resource health**

Aby uzyskać informacje o kondycji zasobów umożliwia także interfejsu API kondycji zasobu.

## <a name="is-resource-health-available-for-all-resource-types"></a>Usługa Resource Health jest dostępna dla wszystkich typów zasobów?
Lista kontroli kondycji i typy zasobów, obsługiwane w usłudze Resource Health można znaleźć [tutaj](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co należy zrobić, jeśli mój zasób wyświetlana jest dostępna, ale sądzę, że nie jest?"
Sprawdzanie kondycji zasobu, tuż poniżej stan kondycji można kliknąć **raportu niepoprawny stan kondycji**. Przed przesłaniem raportu, masz możliwość udostępniające dodatkowe szczegóły dotyczące dlaczego uważasz, że bieżący stan kondycji jest nieprawidłowy.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Jest dostępna dla wszystkich regionów świadczenia usługi Azure Resource Health? 
Usługa Resource health jest dostępny w wszystkich regionach geograficznych systemu Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Jak jest inny niż stan usługi Azure lub pulpit nawigacyjny kondycji usługi Resource Health?
Dostarczonych przez usługę Resource Health jest bardziej szczegółowe niż dostarczanych przez stan platformy Azure lub pulpit nawigacyjny kondycji usługi.

Natomiast [stanu platformy Azure](https://status.azure.com) i pulpit nawigacyjny kondycji usługi informują o problemach usługi, które wpływają na szeroką gamę klientów (na przykład region platformy Azure), kondycja zasobów przedstawia bardziej szczegółowego zdarzenia, które mają zastosowanie tylko do określonego zasobu. Na przykład jeśli host nieoczekiwanie uruchamiany ponownie, kondycja zasobu alerty tylko tych klientów, w których maszyny wirtualne zostały uruchomione na tym hoście.

Należy zauważyć, że do zapewnienia pełną widoczność zdarzeń wpływających na zasoby, Resource Health udostępnia również zdarzeń publikowanych na pulpicie nawigacyjnym kondycji usług.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Należy aktywować kondycja zasobu dla każdego zasobu?
Nie, informacje o kondycji jest dostępny dla wszystkich typów zasobów dostępnych w usłudze Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Potrzebujemy umożliwiające kondycja zasobu dla mojej organizacji?
Nie.  Funkcja kondycji zasobów platformy Azure jest dostępna w witrynie Azure portal, bez żadnych wymagań dotyczących instalacji.

## <a name="is-resource-health-available-free-of-charge"></a>Jest dostępna usługa Resource Health bezpłatnie?
Tak.  Funkcja kondycji zasobów platformy Azure jest bezpłatne.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Co to są zalecenia, które oferuje usługa Resource Health?
Oparte na stanie kondycji, Resource Health zapewnia zaleceń w celu skrócenia czasu spędzony rozwiązywania problemów. Na dostępne zasoby wystąpić fokus zalecenia dotyczące sposobu rozwiązywania typowych problemów klientów. Jeśli zasób jest niedostępny z powodu nieplanowanego zdarzenia dotyczącego platformy Azure, fokus będzie na wspomaganie, podczas i po zakończeniu procesu odzyskiwania. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat kondycji zasobu:
-  [Omówienie usługi Azure Resource Health](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne w usłudze Azure Resource Health](resource-health-checks-resource-types.md)
