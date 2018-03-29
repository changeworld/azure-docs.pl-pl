---
title: Przegląd kondycji zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Przegląd kondycji zasobów platformy Azure
services: Resource health
documentationcenter: ''
author: shawntabrizi
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: shawn.tabrizi
ms.openlocfilehash: 99e996f182aac774f2e2565d87fd0debaba1b2d1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-resource-health-overview"></a>Przegląd kondycji zasobów platformy Azure
 
Kondycja zasobów Azure ułatwia diagnozowanie i uzyskać pomoc techniczną, gdy usługa Azure problem dotyczy Twoich zasobów. Jego informacje o kondycji bieżących i starszych zasobów. I zapewnia pomocy technicznej, aby rozwiązywać problemy.

Podczas gdy [stan Azure](https://status.azure.com) informuje o problemach usługi, które mają wpływ na szerokiego zakresu klientów platformy Azure, kondycja zasobów umożliwia spersonalizowane pulpit nawigacyjny kondycji zasobów. Kondycja zasobów zawiera wszystkie godziny, które zasoby były niedostępne w przeszłości z powodu problemów z usługi Azure. Następnie to proste można łatwiej zrozumieć, jeśli zostało naruszone umowy dotyczącej poziomu usług. 

## <a name="resource-definition-and-health-assessment"></a>Ocena definicji i kondycja zasobów
Zasób jest konkretnym wystąpieniem usługi Azure: na przykład maszynę wirtualną, aplikacji sieci web lub bazy danych SQL.

Kondycja zasobów polega na sygnały emitowane przez różne usługi platformy Azure do oceny, czy zasób jest uszkodzony lub nie. Jeśli zasób jest zła, kondycja zasobu analizuje dodatkowych informacji w celu ustalenia źródła problemu. Identyfikuje również akcje, które jest zbyt firmy Microsoft, aby rozwiązać ten problem, lub akcje, które można podjąć w celu rozwiązania przyczynę problemu. 

Więcej informacji na temat sposobu kondycji jest oceniane, zapoznaj się z pełną listą typów zasobów i kontroli kondycji w [kondycja zasobów Azure](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stan kondycji
Kondycja zasobu jest wyświetlana jako jeden z następujących stanów.

### <a name="available"></a>Dostępna
Stan **dostępne** oznacza, że usługa nie wykryto żadnych zdarzeń, które mają wpływ na kondycję zasobu. W przypadkach, gdy zasób odzyskał sprawność po nieplanowanych przestojów w ostatnich 24 godzinach, zobacz **ostatnio rozwiązane** powiadomień.

![Stan "Dostępne" na maszynie wirtualnej "Niedawno rozpoznane" powiadomienie](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niedostępny
Stan **niedostępny** oznacza, że usługa wykryła trwającą platformy lub zdarzenia z systemem innym niż platformy, która wpływa na kondycję zasobu.

#### <a name="platform-events"></a>Zdarzenia platformy
Zdarzenia platformy są wyzwalane przez kilka składników infrastruktury platformy Azure. Obejmują one zaplanowane akcje (na przykład zaplanowanej konserwacji) oraz nieoczekiwanego zdarzenia (na przykład host nieplanowane ponowny rozruch).

Kondycja zasobów zawiera dodatkowe szczegóły dotyczące zdarzenia i proces odzyskiwania. Umożliwia on również się z pomocą techniczną, nawet jeśli nie masz aktywnych pomocy technicznej umowy firmy Microsoft.

![Stan "Niedostępny" dla maszyny wirtualnej z powodu zdarzenia platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Zdarzenia non platformy
Zdarzenia platformy nie są wyzwalane przez działania użytkowników. Przykłady są zatrzymanie maszyny wirtualnej lub osiągnięciu maksymalnej liczby połączeń z pamięci podręcznej Redis.

![Stan "Niedostępny" dla maszyny wirtualnej z powodu zdarzenia z systemem innym niż platformy](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Brak informacji
Stan kondycji **nieznany** wskazuje, że kondycja zasobu nie otrzymał informacji o tego zasobu na więcej niż 10 minut. Mimo że ten stan nie jest oznaczenie ostateczne stanu zasobu, jest punkt ważnych danych w proces rozwiązywania problemów.

Jeśli zasób działa zgodnie z oczekiwaniami, stan zasobu zmieni się na **dostępne** po kilku minutach.

Jeśli występują problemy z zasobem, **nieznany** stan kondycji mogą wskazywać, że zdarzenia w platformie wpływ na zasób.

![Stan "Nieznany" dla maszyny wirtualnej](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Obniżono poziom
Stan kondycji **obniżony** wskazuje, że zasób ma wykryto utrata wydajności, chociaż jest nadal dostępny do użycia.
Różne zasoby ma swoje własne kryteria po określają, że zasób ma obniżoną.

![Stan "Obniżony" dla maszyny wirtualnej](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Nieprawidłowy stan raportowania
Jeśli uważasz, że bieżący stan kondycji jest nieprawidłowa, użytkownik może Daj nam znać, wybierając **zgłoszenia stanu kondycji niepoprawne**. W przypadkach, gdy Azure problem ma wpływ na możesz, firma Microsoft zachęca do się z pomocą techniczną od zasobów. 

![Pole do przesyłania informacji o niepoprawnym stanie](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informacje historyczne
Można uzyskać dostęp do 14 dni historii kondycji w **Historia kondycji** sekcji kondycja zasobów. 

![Lista zdarzeń kondycja zasobów w ciągu ostatnich dwóch tygodni](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Wprowadzenie
Aby otworzyć kondycja zasobów dla jednego zasobu:
1.  Zaloguj się do Portalu Azure.
2.  Przejdź do zasobu.
3.  W menu zasobów w lewym okienku wybierz **kondycja zasobów**.

![Kondycja zasobów otwarcie przy pomocy widoku zasobów](./media/resource-health-overview/from-resource-blade.png)

Kondycja zasobów można także przejść, wybierając **wszystkie usługi** i wpisując **kondycja zasobów** w polu tekstowym filtru. W **Pomoc i obsługa techniczna** okienku wybierz [kondycja zasobów](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otwieranie kondycja zasobów z "Wszystkie usługi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Kolejne kroki

Wyewidencjonuj tych zasobów, aby dowiedzieć się więcej na temat kondycji zasobów:
-  [Typy zasobów i kondycji sprawdza w kondycji zasobów platformy Azure](resource-health-checks-resource-types.md)
-  [Często zadawane pytania dotyczące kondycji zasobów platformy Azure](resource-health-faq.md)




