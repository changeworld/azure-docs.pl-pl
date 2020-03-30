---
title: Omówienie kondycji zasobów bramy aplikacji platformy Azure
description: Ten artykuł zawiera omówienie funkcji kondycji zasobów usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659504"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Omówienie kondycji zasobów bramy aplikacji platformy Azure

Usługa [Azure Resource Health](../service-health/resource-health-overview.md) pomaga diagnozować i uzyskać pomoc techniczną, gdy problem z usługą platformy Azure wpłynie na Twoje zasoby. Informuje o bieżącej i przeszłej kondycji twoich zasobów. Zapewnia również pomoc techniczną, która pomoże Ci złagodzić problemy.

W przypadku bramy aplikacji kondycja zasobów opiera się na sygnałach emitowanych przez bramę, aby ocenić, czy jest w dobrej kondycji, czy nie. Jeśli brama jest w złej kondycji, kondycja zasobów analizuje dodatkowe informacje w celu określenia źródła problemu. Identyfikuje również akcje podejmowane przez firmę Microsoft lub co można zrobić, aby rozwiązać problem.

Aby uzyskać dodatkowe informacje na temat sposobu oceny kondycji, przejrzyj pełną listę typów zasobów i kontroli kondycji w [usłudze Azure Resource Health.](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)


Stan kondycji bramy aplikacji jest wyświetlany jako jeden z następujących stanów:

## <a name="available"></a>Dostępne

Dostępny **Available** stan oznacza, że usługa nie wykryła żadnych zdarzeń, które wpływają na kondycję zasobu. Powiadomienie **o niedawno rozwiązanym** ujrzysz w przypadkach, gdy brama odzyskała czas nieplanowanych przestojów w ciągu ostatnich 24 godzin.

![Dostępny stan zdrowia](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Niedostępny

Stan **Niedostępne** oznacza, że usługa wykryła trwające zdarzenie platformy lub nieplatformowe, które wpływa na kondycję bramy.

### <a name="platform-events"></a>Wydarzenia platformy

Zdarzenia platformy są wyzwalane przez wiele składników infrastruktury platformy Azure. Obejmują one zarówno zaplanowane akcje (na przykład planowaną konserwację), jak i nieoczekiwane zdarzenia (na przykład nieplanowany ponowny rozruch hosta).

Kondycja zasobów zawiera dodatkowe szczegóły dotyczące zdarzenia i procesu odzyskiwania. Umożliwia również skontaktowanie się z pomocą techniczną, nawet jeśli nie masz aktywnej umowy pomocy technicznej firmy Microsoft.

![Stan niedostępności](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Nieznane

**Stan nieznany** wskazuje, że kondycja zasobu nie otrzymała informacji o bramie przez więcej niż 10 minut. Ten stan nie jest ostatecznym wskazaniem stanu bramy. Ale jest to ważny punkt danych w procesie rozwiązywania problemów.

Jeśli brama działa zgodnie z oczekiwaniami, stan zmieni się **na Dostępne** po kilku minutach.

Jeśli występują problemy, **nieznany** stan kondycji może sugerować, że zdarzenie na platformie ma wpływ na bramę.

![Nieznany stan](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Obniżona wydajność

Stan **obniżona kondycja** wskazuje, że brama wykryła utratę wydajności, chociaż jest nadal dostępna do użycia.

![Stan zdejmowany](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozwiązywania problemów z Zaporą aplikacji bramy aplikacji (WAF), zobacz [Rozwiązywanie problemów z zaporą aplikacji sieci Web (WAF) dla bramy aplikacji platformy Azure](web-application-firewall-troubleshoot.md).