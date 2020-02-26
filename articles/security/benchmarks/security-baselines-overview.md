---
title: Omówienie testu porównawczego zabezpieczeń platformy Azure
description: Omówienie testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2114d9ecff5ee49c63d737cf13278fe45f1f9f73
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589963"
---
# <a name="overview-of-azure-security-baselines"></a>Przegląd podstaw zabezpieczeń platformy Azure

Linie bazowe zabezpieczeń platformy Azure pomagają zwiększyć bezpieczeństwo naszych produktów dzięki udoskonalonym funkcjom narzędzi, śledzeniu i zabezpieczeń oraz zapewnianiu spójnego środowiska w przypadku zabezpieczania środowiska.

Linie bazowe zabezpieczeń usługi platformy Azure koncentrują się na obszarach formantów skoncentrowanych na chmurze. Te kontrolki są spójne z dobrze znanymi testami zabezpieczeń, takimi jak opisane w centrum dla zabezpieczeń Internetu (CIS). Nasze linie bazowe zapewniają wskazówki dotyczące obszarów kontroli wymienionych w [teście zabezpieczeń Azure](overview.md).

Każde zalecenie zawiera następujące informacje:
- **Identyfikator platformy Azure**: Identyfikator testu porównawczego zabezpieczeń platformy Azure, który odpowiada zalecenia.
- **Zalecenie**: bezpośrednio po identyfikatorze platformy Azure zalecenie zawiera ogólny opis formantu.
- **Wskazówki**: uzasadnienie zalecenia i linki do wskazówek dotyczących sposobu ich implementacji. Jeśli zalecenie jest obsługiwane przez Azure Security Center, te informacje będą również wyświetlane.
- **Odpowiedzialność**: kto jest odpowiedzialny za implementację kontroli. Możliwe scenariusze to odpowiedzialność klienta, odpowiedzialność firmy Microsoft lub odpowiedzialność za udział.
- **Azure Security Center monitorowania**: czy formant jest monitorowany przez Azure Security Center, z linkiem do odwołania.

Wszystkie zalecenia, w tym zalecenia, które nie mają zastosowania do tej konkretnej usługi, znajdują się w linii bazowej, aby zapewnić kompletną wersję testu porównawczego zabezpieczeń platformy Azure dla każdej usługi. Czasami mogą to być kontrolki, które nie mają zastosowania z różnych powodów — na przykład formanty skoncentrowane na IaaS/COMPUTE (takie jak kontrolki specyficzne dla zarządzania konfiguracją systemu operacyjnego) mogą nie być stosowane do usług PaaS Services.
