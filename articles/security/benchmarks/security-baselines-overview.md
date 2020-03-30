---
title: Omówienie usługi Azure Security Benchmark
description: Omówienie testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616446"
---
# <a name="overview-of-azure-security-baselines"></a>Omówienie planów bazowych zabezpieczeń platformy Azure

Usługi Azure Security Baselines pomagają zwiększyć bezpieczeństwo naszych produktów dzięki udoskonalonym narzędziom, funkcjom śledzenia i zabezpieczeń oraz zapewniają spójne środowisko podczas zabezpieczania środowiska.

Usługi podstawowe zabezpieczeń usługi Azure koncentrują się na obszarach kontroli skoncentrowanych na chmurze. Te formanty są zgodne ze znanymi wskaźnikami zabezpieczeń, takimi jak te opisane przez Centrum Bezpieczeństwa Internetowego (CIS). Nasze punkty bazowe zawierają wskazówki dotyczące obszarów kontroli wymienionych w [usłudze Azure Security Benchmark.](overview.md)

Każde zalecenie zawiera następujące informacje:
- **Identyfikator platformy Azure:** identyfikator wzorca zabezpieczeń platformy Azure, który odpowiada zaleceniu.
- **Zalecenie:** Po bezpośrednio po identyfikatorze platformy Azure zalecenie zawiera opis wysokiego poziomu formantu.
- **Wytyczne**: Uzasadnienie zalecenia i linki do wytycznych dotyczących sposobu jego wdrożenia. Jeśli zalecenie jest obsługiwane przez usługę Azure Security Center, te informacje również zostaną wyświetlone.
- **Odpowiedzialność**: Kto jest odpowiedzialny za wdrożenie kontroli. Możliwe scenariusze to odpowiedzialność klienta, odpowiedzialność firmy Microsoft lub współdzielona odpowiedzialność.
- **Monitorowanie usługi Azure Security Center:** Czy formant jest monitorowany przez usługę Azure Security Center z łączem do odwołania.

Wszystkie zalecenia, w tym zalecenia, które nie mają zastosowania do tej konkretnej usługi, są zawarte w linii bazowej, aby zapewnić pełny obraz tego, jak usługa Azure Security Benchmark odnosi się do każdej usługi. Czasami mogą istnieć formanty, które nie mają zastosowania z różnych powodów — na przykład formanty IaaS/compute-centric (takie jak formanty specyficzne dla zarządzania konfiguracją systemu operacyjnego) mogą nie mieć zastosowania do usług PaaS.
