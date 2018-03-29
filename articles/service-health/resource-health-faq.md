---
title: Kondycja zasobów platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Przegląd kondycji zasobów platformy Azure
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: BernardoAMunoz
ms.openlocfilehash: 2a0758f5db381f721120cbc1c018a491c018aa8d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-resource-health-faq"></a>Kondycja zasobów platformy Azure — często zadawane pytania
Informacje o odpowiedzi na często zadawane pytania dotyczące kondycji zasobów platformy Azure.

## <a name="what-is-azure-resource-health"></a>Co to jest kondycja zasobów Azure?
Usługa Resource Health ułatwia diagnozowanie i uzyskanie pomocy technicznej, gdy problem z platformą Azure ma wpływ na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Kondycja zasobów przeznaczenie?
Po wykryto problem z zasobem, kondycja zasobu może ułatwić diagnozowanie głównej przyczyny. Zapewnia pomoc, aby ograniczyć problem i pomocy technicznej, gdy potrzebujesz więcej pomocy dotyczącej problemów dotyczących usługi Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Sprawdza, jakie kondycji są wykonywane przez kondycja zasobów?
Kondycja zasobów sprawdza różne na podstawie [typu zasobu](resource-health-checks-resource-types.md). Te sprawdzenia zostały zaprojektowane do zaimplementowania trzy rodzaje problemów: 
- Nieplanowane zdarzenia, na przykład host nieoczekiwane ponowne uruchomienie komputera
- Planowane zdarzeń, takich jak aktualizacje zaplanowane systemu operacyjnego hosta
- Zdarzenia wyzwolone przez akcje użytkownika, na przykład użytkownik ponowny rozruch maszyny wirtualnej

## <a name="what-does-each-of-the-health-status-mean"></a>Co każdy stan kondycji oznacza?
Istnieją trzy stany kondycji różne:
- Niedostępne: Nie ma żadnych znanych problemów, na platformie Azure, które mogą mieć wpływ na tego zasobu
- Niedostępne: Kondycja zasobów wykrył problemy, które wpływają na zasób
- Nieznany: Kondycja zasobu nie można określić kondycji zasobu, ponieważ została zatrzymana, odbieranie informacji ich dotyczących. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co oznacza nieznany stan? Coś jest problem z mojej zasobów?
Stan kondycji ustawiono nieznany po zatrzymaniu kondycja zasobów odbieranie informacji na temat określonego zasobu. Gdy ten stan nie jest ostatecznym wskazanie stanu typu zasobu, w przypadkach, w których występują problemy, może to oznaczać, że występuje problem z platformy Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak uzyskać pomoc dla zasobu, która jest niedostępna
Można przesłać żądanie obsługi z bloku kondycja zasobów. Nie są umowy pomocy technicznej z firmą Microsoft otwarcia żądania, gdy zasób jest niedostępne ponieważ zdarzenia platformy.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Kondycja zasobów odróżnienie niedostępności z uwzględnieniem wielkości liter problemami platformy i coś jak?
Tak, gdy zasób jest niedostępny, kondycja zasobu identyfikuje przyczynę w jednej z tych kategorii: 
-   Akcji zainicjowanej przez użytkownika
-   Zdarzenie planowane 
-   Nieplanowane zdarzenia

W portalu akcji zainicjowanej przez użytkownika są wyświetlane przy użyciu ikony powiadomień niebieski, podczas planowanych lub nieplanowanych zdarzenia są wyświetlane przy użyciu czerwona ikona ostrzeżenia. Bardziej szczegółowe informacje znajdują się w [Przegląd kondycji zasobów](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Czy można zintegrować z narzędzi do monitorowania kondycji zasobów?
Kondycja zasobów to usługa pomaga zdiagnozować i rozwiązać problemy dotyczące usługi Azure, które mają wpływ na zasoby. Za pomocą interfejsu API kondycji zasobów można uzyskać programistycznie stan kondycji, zaleca się metryki służą do monitorowania zasobów. Gdy zostanie wykryty problem, kondycja zasobu pomaga określić przyczynę i przeprowadza Cię przez działania w celu ich rozwiązania. Odwiedź stronę [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) Aby dowiedzieć się więcej na temat wykorzystania metryk do sprawdzenia zasobów.

## <a name="where-do-i-find-resource-health"></a>Gdzie znaleźć kondycja zasobów?
Po zalogowaniu się do portalu Azure, istnieje wiele sposobów, kondycja zasobu można uzyskać dostęp:
- Przejdź do zasobu. W obszarze nawigacji po lewej stronie, wybierz **kondycja zasobów**
- Przejdź do bloku Azure Monitor.  W obszarze nawigacji po lewej stronie, wybierz **kondycja zasobów**.
- Otwórz **Pomoc i obsługa techniczna** bloku wybranie znak zapytania w prawym górnym rogu portalu, a następnie wybierając **Pomoc i obsługa techniczna**. Po otwarciu bloku wybierz **kondycja zasobów**

Aby uzyskać informacje o kondycji zasobów umożliwia także interfejsu API kondycji zasobów.

## <a name="is-resource-health-available-for-all-resource-types"></a>Kondycja zasobu jest dostępna dla wszystkich typów zasobów?
Lista kontroli kondycji i typów zasobów, które są obsługiwane w ramach kondycji zasobów można znaleźć [tutaj](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co należy zrobić, jeśli mojego zasobów jest wyświetlany dostępny, ale I uważają, że nie jest?"
Sprawdzanie kondycji zasobu, bezpośrednio w stan kondycji można kliknąć **zgłoszenia stanu kondycji niepoprawne**. Przed przesłaniem raport, użytkownik może dostarczać dodatkowych szczegółów na dlaczego uważasz, że bieżący stan kondycji jest nieprawidłowy.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Kondycja zasobu jest dostępna dla wszystkich regionów platformy Azure? 
Kondycja zasobu jest dostępna w we wszystkich obszarach geograficznych Azure, z wyjątkiem następujących regionach:
- Administracja USA — Wirginia
- Administracja USA — Iowa
- US DoD — wschodnie stany
- US DoD — środkowe stany
- Niemcy Środkowe
- Niemcy Północno-Wschodnie

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Jak kondycja zasobu jest inny niż stan Azure lub pulpit nawigacyjny kondycji usługi
Informacje dostarczane przez kondycja zasobu jest bardziej szczegółowy niż dostarczanych przez stan Azure lub pulpit nawigacyjny kondycji usługi.

Podczas gdy [Azure stanu](https://status.azure.com) i na pulpicie nawigacyjnym kondycji usługi informuje o problemach usługi, które mają wpływ na szerokiego zakresu klientów (na przykład region platformy Azure), kondycja zasobów przedstawia bardziej szczegółowego zdarzenia, które mają zastosowanie tylko w określonego zasobu. Na przykład jeśli host nieoczekiwanie uruchamiany ponownie, kondycja zasobu alerty tylko tych klientów, w której maszyny wirtualne zostały uruchomione na tym hoście.

Ważne jest, aby Zwróć uwagę, że zapewnienie widoczne zdarzeń wpływu na zasoby, kondycja zasobów udostępnia również zdarzenia opublikowane w pulpicie nawigacyjnym kondycji usługi.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Należy uaktywnić kondycja zasobów dla każdego zasobu?
Nie, informacje o kondycji są dostępne dla wszystkich typów zasobów dostępne za pośrednictwem kondycja zasobów. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Potrzebujemy umożliwiające kondycja zasobów dla mojej organizacji?
Nie.  Kondycja zasobów platformy Azure są dostępne w portalu Azure, bez żadnych wymagań dotyczących instalacji.

## <a name="is-resource-health-available-free-of-charge"></a>Jest kondycja zasobu jest dostępny bezpłatnie?
Tak.  Kondycja zasobów platformy Azure jest bezpłatne.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Jakie są zalecenia, które udostępnia kondycja zasobów?
Oparte na stanie kondycji, kondycja zasobów udostępnia zalecenia w celu skrócenia czasu spędzony rozwiązywania problemów. Na dostępne zasoby wystąpić fokus zalecenia dotyczące rozwiązania typowych problemów klientów. Jeśli zasób jest niedostępny z powodu Azure nieplanowanego zdarzenia, fokus będą realizowane w pomocy, należy podczas i po zakończeniu procesu odzyskiwania. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat kondycji zasobów:
-  [Przegląd kondycji zasobów platformy Azure](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne w usłudze Azure Resource Health](resource-health-checks-resource-types.md)
