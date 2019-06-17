---
title: Co to jest Azure Service Health?
description: Spersonalizowane informacje na temat aplikacji Azure wpływu problemów z obecnym i przyszłym usługi platformy Azure i konserwacji.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079576"
---
# <a name="what-is-azure-service-health"></a>Co to jest Azure Service Health?

Platforma Azure oferuje zestaw środowisk, aby informować Cię informować o kondycji zasobów w chmurze. Informacje te obejmują bieżących i przyszłych problemów, takich jak usługa wpływu na zdarzenia planowanej konserwacji i innych zmian, które mogą mieć wpływ na dostępność usługi.

Usługa Azure Service Health składa się z trzech oddzielnych mniejszych usług.

[Stan usługi Azure](azure-status-overview.md) informuje Cię o przerwy w działaniu usługi na platformie Azure na  **[strony stanu platformy Azure](https://status.azure.com)** . Strona jest globalny wgląd w kondycję wszystkich usług platformy Azure we wszystkich regionach platformy Azure. Na stronie stanu jest wartościowa dokumentacja ułatwiająca związaną ze zdarzeniami przy użyciu rozległego wpływu, ale zdecydowanie zaleca się że bieżący użytkownicy platformy Azure korzystać z usługi Azure service health, aby aktualne informacje na temat zdarzeń platformy Azure i konserwacji.

[Usługa Azure service health](service-health-overview.md) zapewnia spersonalizowany widok kondycji usług platformy Azure i korzystania z regionów. Jest to najlepsze miejsce do sprawdzania komunikatów mających wpływ na usługi o przestojach i działaniach planowanej konserwacji oraz innych porad dotyczących kondycji, ponieważ uwierzytelnione środowisko usługi Azure Service Health zna obecnie używane usługi i zasoby. Najlepszym sposobem używania usługi Service Health jest skonfigurowanie alertów, aby otrzymywać powiadomienia za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów z usługą, planowanej konserwacji lub innych zmian, które mogą mieć wpływ na używane usługi platformy Azure i regiony.

[Usługa Azure resource health](resource-health-overview.md) zawiera informacje o kondycji zasobów w chmurze indywidualnych, takie jak określonego wystąpienia maszyny wirtualnej. Za pomocą usługi Azure Monitor można również skonfigurować alerty informujące o zmianach dostępności zasobów w chmurze. Usługa Azure Resource Health wraz z powiadomieniami w usłudze Azure Monitor pomogą Ci w uzyskiwaniu aktualnych informacji na temat dostępności zasobów minuta po minucie i szybko ocenić, czy problem jest spowodowany problemem po Twojej stronie czy jest związany ze zdarzeniem platformy Azure.

Razem te środowiska umożliwiają kompleksowy wgląd w kondycję platformy Azure, na poziomie szczegółowości najbardziej odpowiednim dla Ciebie.

**Obejrzyj przegląd strony stanu platformy Azure, Azure Service Health i usługi Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
