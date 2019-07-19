---
title: Korelacja inteligentnego alertu w chmurze w Azure Security Center (zdarzenia) | Microsoft Docs
description: W tym temacie opisano sposób, w jaki funkcja Fusion używa korelacji alertów inteligentnych w chmurze w celu wygenerowania zdarzeń zabezpieczeń w Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295859"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelacja inteligentnego alertu w chmurze w Azure Security Center (zdarzenia)

Security Center stale analizuje obciążenia chmury hybrydowej za pomocą zaawansowanej analizy i analizy zagrożeń, aby ostrzec o złośliwych działaniach.

W miarę zwiększania się zakresu zagrożeń i konieczności wykrywania nawet niewielkiej wartości zwiększonych naruszeń, może być trudne dla analityków zabezpieczeń, aby klasyfikacja różne alerty i identyfikować faktyczny atak. Security Center ułatwia analitykom radzenie sobie z zmęczeniem alertów i diagnozowanie ataków w miarę ich występowania, przez skorelowanie różnych alertów i słabych sygnałów w zdarzenia dotyczące zabezpieczeń.

Fusion to technologia i zaplecze analityczne, które ułatwiają Security Center incydenty, dzięki czemu mogą skorelować różne alerty i sygnały kontekstowe. Fuzja działa przez przeglądanie różnych sygnałów raportowanych w ramach subskrypcji w ramach zasobów i znalezienie nierozpowszechnionych wzorców, które pokazują postęp ataków lub sygnały z udostępnionymi informacjami kontekstowymi, które wskazują na ujednoliconą procedurę odpowiedzi. dla nich wykonane.

Fusion Analytics łączy informacje o domenie zabezpieczeń z AI, aby analizować alerty, odkrywając nowe wzorce ataku w miarę ich występowania. 

Security Center korzysta z macierzy ataków MITRE do kojarzenia alertów z ich zamiarem, pomagając w prace prowadzone informacji o domenie zabezpieczeń. Ponadto, korzystając z informacji zebranych dla każdego kroku ataku, Security Center może wyrównać działanie, które wydaje się być czynnościami ataku, ale nie jest.  

Ponieważ ataki często występują między różnymi dzierżawcami, Security Center mogą łączyć algorytmy AI, aby analizować sekwencje ataków, które są zgłaszane w każdej subskrypcji, aby identyfikować je jako wzorce alertów, a nie tylko incydenty skojarzone z każdym różnych.

Podczas badania zdarzenia analitykowie często potrzebują dodatkowego kontekstu, aby dotrzeć do Verdict o charakterze zagrożenia i sposobach ich rozwiązywania. Na przykład nawet w przypadku wykrycia nietypowej sieci, bez zrozumienia, co się dzieje w sieci, lub w odniesieniu do zasobu, trudno zrozumieć, jakie akcje należy podjąć dalej. Aby pomóc, zdarzenie zabezpieczeń może obejmować artefakty, powiązane zdarzenia i informacje. Dodatkowe informacje dostępne dla zdarzeń związanych z zabezpieczeniami różnią się w zależności od typu wykrytego zagrożenia i konfiguracji środowiska. 

![Szczegóły zdarzenia dotyczącego zabezpieczeń](./media/security-center-alerts-cloud-smart/security-incident.png)

Aby lepiej zrozumieć zdarzenia związane z zabezpieczeniami, zobacz [jak obsłużyć zdarzenia zabezpieczeń w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

