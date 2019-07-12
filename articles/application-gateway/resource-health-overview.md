---
title: Omówienie usługi Azure Application Gateway Resource Health
description: W tym artykule przedstawiono omówienie funkcji kondycji zasobów w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659504"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Omówienie usługi Azure Application Gateway Resource Health

[Kondycja zasobów Azure](../service-health/resource-health-overview.md) pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z usług platformy Azure ma wpływ na Twoje zasoby. Informowanie o bieżącej i przeszłej kondycji Twoich zasobów. I udostępnia pomocy technicznej, aby pomóc Ci rozwiązać problemy.

W usłudze Application Gateway Resource Health opiera się na sygnały emitowane przez bramę do oceny, czy jest uszkodzony lub nie. Jeśli brama jest w złej kondycji, Resource Health analizuje dodatkowych informacji do ustalenia źródła problemu. Identyfikuje również akcje, które zajmuje firmy Microsoft lub co można zrobić, aby rozwiązać ten problem.

Więcej informacji na temat sposobu kondycji jest oceniana, przejrzyj pełną listę typów zasobów i kontroli kondycji w [usługi Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Stan kondycji bramy Application Gateway jest wyświetlany jako jeden z następujących stanów:

## <a name="available"></a>Dostępne

**Dostępne** stan oznacza, że usługa nie wykryto wszelkie zdarzenia, które mają wpływ na kondycję tego zasobu. Zobaczysz **ostatnio rozwiązane** powiadomień w przypadkach, gdy brama odzyskał sprawność nieplanowane przestoje w ciągu ostatnich 24 godzin.

![Kondycja dostępności](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Niedostępny

**Unavailable** stan oznacza, że usługa wykryła trwającą platformy lub zdarzenia inne niż platformy, która wpływa na kondycji bramy.

### <a name="platform-events"></a>Zdarzenia platformy

Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowanych akcji (na przykład planowanej konserwacji), jak i nieoczekiwane zdarzenia (na przykład nieplanowana Akcja ponownego uruchomienia hosta).

Usługa Resource Health zapewnia dodatkowe szczegóły dotyczące zdarzenia oraz proces odzyskiwania. Umożliwia on również się z pomocą techniczną, nawet jeśli nie masz aktywnych Microsoft umowę o pomocy technicznej.

![Stan niedostępny](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Nieznane

**Nieznany** stan kondycji wskazuje kondycja zasobu nie odebrał informacji na temat bramy na więcej niż 10 minut. Ten stan nie jest ostateczną wskazanie stan bramy. Ale to ważny punkt danych w proces rozwiązywania problemów.

Jeśli brama działa zgodnie z oczekiwaniami, stan zmieni się na **dostępne** po kilku minutach.

Jeśli występują problemy, **nieznany** stan kondycji mogą wskazywać, że zdarzenie na platformie ma wpływ na bramę.

![Nieznany stan](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Obniżono poziom

**Obniżony** stan kondycji oznacza bramy utratę wydajności, mimo że nadal dostępne do użycia.

![Stan degrated](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozwiązywania problemów z zaporą aplikacji sieci Web (WAF) aplikacji bramy, zobacz [Rozwiązywanie problemów z aplikacji sieci Web zapory (WAF) w usłudze Azure Application Gateway](web-application-firewall-troubleshoot.md).