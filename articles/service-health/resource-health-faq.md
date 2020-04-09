---
title: Usługa Azure Resource Health – często zadawane pytania
description: Omówienie kondycji zasobów platformy Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 7459a29dca01dc186d75b4545f89068569975607
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985035"
---
# <a name="azure-resource-health-faq"></a>Usługa Azure Resource Health – często zadawane pytania
Zapoznaj się z odpowiedziami na często zadawane pytania dotyczące usługi Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Co to jest usługa Azure Resource Health?
Usługa Resource Health ułatwia diagnozowanie i uzyskanie pomocy technicznej, gdy problem z platformą Azure ma wpływ na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Do czego służy kondycja zasobów?
Po wykryciu problemu z zasobem kondycja zasobów może pomóc w zdiagnozowaniu głównej przyczyny. Zapewnia pomoc w łagodzeniu problemu i pomocy technicznej, gdy potrzebujesz więcej pomocy w zakresie problemów z usługą Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Jakie kontrole kondycji są wykonywane przez kondycję zasobów?
Kondycja zasobów wykonuje różne kontrole na podstawie [typu zasobu](resource-health-checks-resource-types.md). Kontrole te mają na celu wdrożenie trzech rodzajów problemów: 
- Zdarzenia nieplanowane, na przykład nieoczekiwane ponowne uruchomienie hosta
- Planowane zdarzenia, takie jak zaplanowane aktualizacje systemu operacyjnego hosta
- Zdarzenia wyzwalane przez akcje użytkownika, na przykład ponowne uruchomienie maszyny wirtualnej przez użytkownika

## <a name="what-does-each-of-the-health-status-mean"></a>Co oznacza każdy stan zdrowia?
Istnieją trzy różne stany zdrowia:
- Dostępne: Nie ma żadnych znanych problemów na platformie Azure, które mogłyby mieć wpływ na ten zasób
- Niedostępne: Kondycja zasobów wykryła problemy, które mają wpływ na zasób
- Nieznany: Kondycja zasobu nie może określić kondycji zasobu, ponieważ przestał otrzymywać informacje o nim. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co oznacza nieznany status? Czy coś jest nie tak z moim zasobem?
Stan kondycji jest ustawiony na nieznany, gdy kondycja zasobu przestaje odbierać informacje o określonym zasobie. Chociaż ten stan nie jest ostatecznym wskazaniem stanu zasobu, w przypadkach, gdy występują problemy, może to oznaczać, że występuje problem z platformą Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak uzyskać pomoc dotyczącą zasobu, który jest niedostępny?
Można przesłać żądanie pomocy technicznej z zasobu kondycji bloku. Nie potrzebujesz umowy pomocy technicznej z firmą Microsoft, aby otworzyć żądanie, gdy zasób jest niedostępny, ponieważ zdarzenia platformy.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Czy kondycja zasobów rozróżnia niedostępność spowodowaną problemami z platformą a czymś, co zrobiłem?
Tak, gdy zasób jest niedostępny, kondycja zasobów identyfikuje główną przyczynę w jednej z następujących kategorii: 
-   Akcja inicjowana przez użytkownika
-   Planowane wydarzenie 
-   Zdarzenie nieplanowane

W portalu akcje inicjowane przez użytkownika są wyświetlane przy użyciu niebieskiej ikony powiadomień, podczas gdy zdarzenia planowane i nieplanowane są wyświetlane przy użyciu czerwonej ikony ostrzeżenia. Więcej szczegółów znajduje się w [omówienie kondycji zasobów](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Czy mogę zintegrować kondycję zasobów z narzędziami monitorowania?
Kondycja zasobów ma [obsługę w wersji zapoznawczej](resource-health-alert-arm-template-guide.md) alertów opartych na dzienniku aktywności. Alerty dziennika [aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) używają grup akcji do powiadamiania użytkowników o wyzwoleniu alertu. Grupy akcji obsługują różne kanały powiadomień, takie jak akcje poczty e-mail, SMS, webhook i ITSM.

## <a name="where-do-i-find-resource-health"></a>Gdzie znajdę kondycję zasobów?
Po zalogowaniu się do witryny Azure portal istnieje wiele sposobów uzyskiwania dostępu do kondycji zasobów:
- Przejdź do zasobu. W nawigacji po lewej stronie wybierz pozycję **Kondycja zasobów**
- Przejdź do bloku Usługi Azure Service Health.  W nawigacji po lewej stronie wybierz pozycję **Kondycja zasobów**.
- Otwórz blok **Pomoc + Wsparcie,** zaznaczając znak zapytania w prawym górnym rogu portalu, a następnie wybierając **pomoc + wsparcie**. Po otwarciu bloku wybierz **pozycję Kondycja zasobów**

Można również użyć interfejsu API kondycji zasobów, aby uzyskać informacje o kondycji zasobów.

## <a name="is-resource-health-available-for-all-resource-types"></a>Czy kondycja zasobów jest dostępna dla wszystkich typów zasobów?
Listę kontroli kondycji i typów zasobów obsługiwanych przez kondycję zasobów można znaleźć [tutaj](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co należy zrobić, jeśli mój zasób jest wyświetlany, ale uważam, że nie jest?"
Podczas sprawdzania kondycji zasobu, bezpośrednio w stanie kondycji można kliknąć **zgłoś niepoprawny stan kondycji**. Przed przesłaniem raportu masz możliwość podania dodatkowych informacji na temat tego, dlaczego uważasz, że bieżący stan kondycji jest nieprawidłowy.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Czy kondycja zasobów jest dostępna dla wszystkich regionów platformy Azure? 
Kondycja zasobów jest dostępna we wszystkich lokalizacjach geograficznych platformy Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Czym różni się kondycja zasobów od stanu platformy Azure lub pulpitu nawigacyjnego kondycji usługi?
Informacje dostarczane przez kondycję zasobów są bardziej szczegółowe niż informacje dostarczane przez stan platformy Azure lub pulpit nawigacyjny kondycji usługi.

Podczas gdy [stan platformy Azure](https://status.azure.com) i pulpit nawigacyjny kondycji usługi informują o problemach z usługą, które wpływają na szeroki zestaw klientów (na przykład region platformy Azure), kondycja zasobów udostępnia bardziej szczegółowe zdarzenia, które są istotne tylko dla określonego zasobu. Na przykład jeśli host nieoczekiwanie uruchomi się ponownie, kondycja zasobów alarmuje tylko tych klientów, których maszyny wirtualne były uruchomione na tym hoście.

Należy zauważyć, że aby zapewnić pełną widoczność zdarzeń wpływających na zasoby, Kondycja zasobów również powierzchnie zdarzeń opublikowanych na pulpicie nawigacyjnym kondycji usługi.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Czy muszę aktywować kondycję zasobów dla każdego zasobu?
Nie, informacje o kondycji są dostępne dla wszystkich typów zasobów dostępnych za pośrednictwem kondycji zasobów. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Czy musimy włączyć kondycję zasobów dla mojej organizacji?
Nie.  Usługa Azure Resource Health jest dostępna w witrynie Azure portal bez żadnych wymagań dotyczących instalacji.

## <a name="is-resource-health-available-free-of-charge"></a>Czy kondycja zasobów jest dostępna bezpłatnie?
Tak.  Usługa Azure Resource Health jest bezpłatna.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Jakie są zalecenia, które zapewnia kondycja zasobów?
Na podstawie stanu kondycji kondycji zasobów zapewnia zalecenia w celu skrócenia czasu spędzonego na rozwiązywaniu problemów. W przypadku dostępnych zasobów zalecenia koncentrują się na tym, jak rozwiązać najczęstsze problemy napotykane przez klientów. Jeśli zasób jest niedostępny z powodu nieplanowanego zdarzenia platformy Azure, fokus będzie na pomoc w trakcie i po procesie odzyskiwania. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o kondycji zasobów:
-  [Omówienie kondycji zasobów platformy Azure](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne za pośrednictwem usługi Azure Resource Health](resource-health-checks-resource-types.md)
