---
title: What is Azure Service Health? (Co to jest usługa Azure Service Health?)
description: Spersonalizowane informacje o tym, jak twoje aplikacje platformy Azure są dotknięte bieżącymi i przyszłymi problemami z usługami platformy Azure i konserwacją.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77654090"
---
# <a name="what-is-azure-service-health"></a>What is Azure Service Health? (Co to jest usługa Azure Service Health?)

Platforma Azure oferuje pakiet środowisk, które informują Cię o kondycji zasobów w chmurze. Informacje te obejmują bieżące i nadchodzące problemy, takie jak zdarzenia wpływające na usługi, planowana konserwacja i inne zmiany, które mogą mieć wpływ na dostępność.

Usługa Azure Service Health to połączenie trzech oddzielnych mniejszych usług.

[Stan platformy Azure](azure-status-overview.md) informuje o awariach usługi na platformie Azure na **[stronie Stan platformy Azure](https://status.azure.com)**. Strona jest globalnym widokiem kondycji wszystkich usług platformy Azure we wszystkich regionach platformy Azure. Strona stanu jest dobrym punktem odniesienia dla incydentów o powszechnym wpływie, ale zdecydowanie zaleca się, aby obecni użytkownicy platformy Azure korzystali ze kondycji usługi platformy Azure, aby być na bieżąco z zdarzeniami i konserwacją platformy Azure.

[Kondycja usługi platformy Azure](service-health-overview.md) zapewnia spersonalizowany widok kondycji usług platformy Azure i regionów, których używasz. Jest to najlepsze miejsce do sprawdzania komunikatów mających wpływ na usługi o przestojach i działaniach planowanej konserwacji oraz innych porad dotyczących kondycji, ponieważ uwierzytelnione środowisko usługi Azure Service Health zna obecnie używane usługi i zasoby. Najlepszym sposobem używania usługi Service Health jest skonfigurowanie alertów, aby otrzymywać powiadomienia za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów z usługą, planowanej konserwacji lub innych zmian, które mogą mieć wpływ na używane usługi platformy Azure i regiony.

[Kondycja zasobów platformy Azure](resource-health-overview.md) zawiera informacje o kondycji poszczególnych zasobów w chmurze, takich jak wystąpienie określonej maszyny wirtualnej. Za pomocą usługi Azure Monitor można również skonfigurować alerty informujące o zmianach dostępności zasobów w chmurze. Usługa Azure Resource Health wraz z powiadomieniami w usłudze Azure Monitor pomogą Ci w uzyskiwaniu aktualnych informacji na temat dostępności zasobów minuta po minucie i szybko ocenić, czy problem jest spowodowany problemem po Twojej stronie czy jest związany ze zdarzeniem platformy Azure.

Razem te środowiska umożliwiają kompleksowy wgląd w kondycję platformy Azure, na poziomie szczegółowości najbardziej odpowiednim dla Ciebie.

**Zobacz omówienie strony stanu platformy Azure, usługi Azure Service Health i usługi Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]