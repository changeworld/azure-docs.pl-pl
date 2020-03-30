---
title: Omówienie kondycji zasobów platformy Azure
description: Omówienie kondycji zasobów platformy Azure
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159255"
---
# <a name="resource-health-overview"></a>Omówienie kondycji zasobów
 
Usługa Azure Resource Health pomaga diagnozować i utrzymywki obsługi problemów z usługą, które wpływają na zasoby platformy Azure. Raportuje bieżącą i przeszłą kondycję twoich zasobów.

[Raporty o stanie platformy Azure](https://status.azure.com) dotyczące problemów z usługami, które wpływają na szeroki zestaw klientów platformy Azure. Kondycja zasobów zapewnia spersonalizowany pulpit nawigacyjny kondycji zasobów. Kondycja zasobu pokazuje wszystkie czasy, że zasoby były niedostępne z powodu problemów z usługą Platformy Azure. Te dane ułatwiają sprawdzenie, czy umowy SLA została naruszona.

## <a name="resource-definition-and-health-assessment"></a>Definicja zasobów i ocena stanu zdrowia

*Zasób* jest określonym wystąpieniem usługi platformy Azure, takiej jak maszyna wirtualna, aplikacja sieci web lub baza danych SQL. Kondycja zasobów opiera się na sygnałach z różnych usług platformy Azure, aby ocenić, czy zasób jest w dobrej kondycji. Jeśli zasób jest w złej kondycji, kondycja zasobów analizuje dodatkowe informacje w celu określenia źródła problemu. Raportuje również o działaniach podejmowanych przez firmę Microsoft w celu rozwiązania problemu i identyfikuje rzeczy, które można wykonać, aby go rozwiązać.

Aby uzyskać więcej informacji na temat sposobu oceny kondycji, zobacz listę typów zasobów i kontroli kondycji w [usłudze Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stan kondycji

Kondycja zasobu jest wyświetlana jako jeden z następujących stanów.

### <a name="available"></a>Dostępne

*Dostępne* oznacza, że nie wykryto żadnych zdarzeń, które wpływają na kondycję zasobu. W przypadkach, gdy zasób odzyskane z nieplanowanych przestojów w ciągu ostatnich 24 godzin, zobaczysz powiadomienie "Ostatnio rozwiązany".

![Stan *Dostępne* dla maszyny wirtualnej z powiadomieniem "Ostatnio rozwiązany"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niedostępny

*Niedostępne* oznacza, że usługa wykryła trwające zdarzenie platformy lub nieplatformowe, które wpływa na kondycję zasobu.

#### <a name="platform-events"></a>Wydarzenia platformy

Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowane akcje (na przykład planowaną konserwację), jak i nieoczekiwane zdarzenia (na przykład nieplanowany ponowny rozruch hosta lub zdegradowany sprzęt hosta, który ma zakończyć się niepowodzeniem po określonym przedziale czasu).

Kondycja zasobów zawiera dodatkowe szczegóły dotyczące zdarzenia i procesu odzyskiwania. Umożliwia również skontaktowanie się z pomocą techniczną firmy Microsoft, nawet jeśli nie masz aktywnej umowy dotyczącej pomocy technicznej.

![Stan *Niedostępne* dla maszyny wirtualnej z powodu zdarzenia platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Wydarzenia nieplatformowe

Zdarzenia spoza platformy są wyzwalane przez akcje użytkownika. Przykłady obejmują zatrzymanie maszyny wirtualnej lub osiągnięcie maksymalnej liczby połączeń z pamięcią podręczną Azure dla firmy Redis.

![Stan "Niedostępne" dla maszyny wirtualnej z powodu zdarzenia nienaplatformowego](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Nieznane

*Nieznany* oznacza, że kondycja zasobów nie otrzymała informacji o zasobie przez więcej niż 10 minut. Mimo że ten stan nie jest ostatecznym wskazaniem stanu zasobu, jest to ważny punkt danych do rozwiązywania problemów.

Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na *Dostępne* po kilku minutach.

Jeśli wystąpią problemy z zasobem, *nieznany* stan kondycji może oznaczać, że zdarzenie na platformie ma wpływ na zasób.

![Stan *Nieznany* dla maszyny wirtualnej](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Obniżona wydajność

*Obniżona* oznacza, że zasób wykrył utratę wydajności, chociaż jest nadal dostępny do użycia.

Różne zasoby mają własne kryteria, kiedy zgłaszają, że są one zdegradowane.

![Stan *Zdegradowany* dla maszyny wirtualnej](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Zgłaszanie nieprawidłowego stanu

Jeśli uważasz, że bieżący stan kondycji jest nieprawidłowy, możesz nam powiedzieć, wybierając **pozycję Zgłoś niepoprawny stan zdrowia**. W przypadkach, gdy problem z platformą Azure ma wpływ na Ciebie, zachęcamy do skontaktowania się z pomocą techniczną z kondycji zasobów.

![Formularz do przesyłania informacji o nieprawidłowym stanie](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informacje o historii

Dostęp do maksymalnie 30 dni historii można uzyskać w sekcji **Historia kondycji** kondycji zasobów.

![Lista zdarzeń kondycji zasobów w ciągu ostatnich dwóch tygodni](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Wprowadzenie

Aby otworzyć kondycję zasobu dla jednego zasobu:

1. Zaloguj się do Portalu Azure.
2. Przejdź do zasobu.
3. W menu zasobów w lewym okienku wybierz pozycję **Kondycja zasobów**.

![Otwieranie kondycji zasobów z widoku zasobu](./media/resource-health-overview/from-resource-blade.png)

Dostęp do kondycji zasobów można również uzyskać, zaznaczając **pozycję Wszystkie usługi** i wpisując **kondycję zasobów** w polu tekstowym filtru. W okienku **Pomoc + obsługa** wybierz pozycję [Kondycja zasobów](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otwieranie kondycji zasobów z "Wszystkie usługi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi odwołaniami, aby dowiedzieć się więcej o kondycji zasobów:
-  [Typy zasobów i kontrole kondycji w kondycji zasobów platformy Azure](resource-health-checks-resource-types.md)
-  [Często zadawane pytania dotyczące kondycji zasobów platformy Azure](resource-health-faq.md)
