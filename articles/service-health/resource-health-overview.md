---
title: Omówienie usługi Azure Resource Health | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 11/16/2018
ms.openlocfilehash: d2a77e831290aa1ee0fcb6d4addf8f6e90786d52
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119856"
---
# <a name="azure-resource-health-overview"></a>Omówienie usługi Azure Resource Health
 
Usługa Azure Resource Health pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z usług platformy Azure ma wpływ na Twoje zasoby. Informowanie o bieżącej i przeszłej kondycji Twoich zasobów. I udostępnia pomocy technicznej, aby pomóc Ci rozwiązać problemy.

Natomiast [stanu platformy Azure](https://status.azure.com) informuje o problemach usługi, które wpływają na szeroką gamę klientów platformy Azure, usługa Resource Health zapewnia spersonalizowanego pulpitu nawigacyjnego kondycji Twoich zasobów. Usługa Resource Health pokazuje wszystkie momenty w czasie, gdy zasoby były w przeszłości niedostępne z powodu problemów z usługami platformy Azure. Następnie jest prosty dowiedzieć się, jeśli został naruszony, zwiększyłaby umowy SLA. 

## <a name="resource-definition-and-health-assessment"></a>Ocena definicji i kondycji zasobów
Zasób jest konkretnego wystąpienia usługi platformy Azure: na przykład maszynę wirtualną, aplikacji sieci web lub bazy danych SQL.

Usługa Resource Health opiera się na sygnały wyemitowane przez różne usługi platformy Azure do oceny, czy zasób jest w dobrej kondycji lub też nie. Jeśli zasób jest w złej kondycji, Resource Health analizuje dodatkowych informacji do ustalenia źródła problemu. Identyfikuje również akcje, które zajmuje firmy Microsoft, aby rozwiązać ten problem, lub akcje, które można wykonać, aby zająć się przyczyną problemu. 

Więcej informacji na temat sposobu kondycji jest oceniana, przejrzyj pełną listę typów zasobów i kontroli kondycji w [usługi Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stan kondycji
Kondycja zasobu jest wyświetlana jako jeden z następujących stanów.

### <a name="available"></a>Dostępne
Stan **dostępne** oznacza, że usługa nie wykryto wszelkie zdarzenia, które mają wpływ na kondycję tego zasobu. W przypadkach, w której zasób odzyskał sprawność nieplanowane przestoje w ciągu ostatnich 24 godzin, zostanie wyświetlony **ostatnio rozwiązane** powiadomień.

![Stan "Dostępne" dla maszyny wirtualnej z powiadomieniem "Niedawno został rozwiązany problem"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niedostępny
Stan **Unavailable** oznacza, że usługa wykrył platformy ciągłego lub zdarzenia inne niż platformy, która wpływa na kondycji zasobu.

#### <a name="platform-events"></a>Zdarzenia platformy
Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowanych akcji (na przykład planowanej konserwacji), jak i nieoczekiwane zdarzenia (na przykład nieplanowana Akcja ponownego uruchomienia hosta).

Usługa Resource Health zapewnia dodatkowe szczegóły dotyczące zdarzenia oraz proces odzyskiwania. Umożliwia on również się z pomocą techniczną, nawet jeśli nie masz aktywnych Microsoft umowę o pomocy technicznej.

![Stan "Niedostępne" dla maszyny wirtualnej z powodu zdarzenia platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Zdarzenia inne niż platformy
Zdarzenia inne niż platformy są wyzwalane przez działania użytkowników. Przykłady są zatrzymywanie maszyny wirtualnej lub osiągnięcia maksymalną liczbę połączeń do usługi Azure Cache dla usługi Redis.

![Stan "Niedostępne" dla maszyny wirtualnej ze względu na to zdarzenie-platform](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Nieznana
Stan kondycji **nieznany** wskazuje, że kondycja zasobu nie odebrał informacji na temat tego zasobu na więcej niż 10 minut. Mimo że ten stan nie jest ostateczną wskazanie stan zasobu, to ważny punkt danych w proces rozwiązywania problemów.

Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na **dostępne** po kilku minutach.

Jeśli występują problemy z zasobem, **nieznany** stan kondycji mogą wskazywać, że zdarzenie na platformie ma wpływ na zasób.

![Stan "Nieznany" dla maszyny wirtualnej](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Obniżono poziom
Stan kondycji **obniżony** oznacza, że zasób utrata wydajności, mimo że nadal dostępne do użycia.
Różne zasoby mają swoje własne kryteria po określają, czy zasób działa nieprawidłowo.

![Stan "Obniżony" dla maszyny wirtualnej](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Niepoprawny stan raportowania
Jeśli uważasz, że bieżący stan kondycji jest niepoprawny, możesz dać nam znać, wybierając **raportu niepoprawny stan kondycji**. W przypadkach, w którym problemu z platformą Azure ma wpływ na możesz, firma Microsoft zachęca do skontaktuj się z działem pomocy technicznej firmy kondycji zasobu. 

![Pole do przesyłania informacji na temat niepoprawny stan](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informacje historyczne
Możesz uzyskać dostęp do 14 dni historii kondycji w **Historia kondycji** sekcji kondycji zasobu. 

![Lista zdarzeń kondycji zasobów w ciągu ostatnich dwóch tygodni](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Wprowadzenie
Aby otworzyć kondycja zasobu dla jednego zasobu:
1.  Zaloguj się do Portalu Azure.
2.  Przejdź do zasobu.
3.  W menu zasobów w okienku po lewej stronie wybierz **Resource health**.

![Otwieranie Resource Health przy pomocy widoku zasobów](./media/resource-health-overview/from-resource-blade.png)

Można również przejść Resource Health, wybierając **wszystkich usług** i wpisując **kondycja zasobu** w polu tekstowym filtru. W **Pomoc i obsługa techniczna** okienku wybierz [Resource health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otwieranie kondycja zasobu z "Wszystkie usługi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z tych zasobów, aby dowiedzieć się więcej o usłudze Resource Health:
-  [Typy zasobów i kontrole kondycji w usłudze Azure Resource Health](resource-health-checks-resource-types.md)
-  [Często zadawane pytania dotyczące usługi Azure Resource Health](resource-health-faq.md)




