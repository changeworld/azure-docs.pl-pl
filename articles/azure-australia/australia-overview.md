---
title: Co to jest Azure Australia? | Microsoft Docs
description: Wskazówki dotyczące konfigurowania platformy Azure w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: Galey801
ms.service: azure-australia
ms.topic: overview
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a6bd4a8747ba4aa592914fc970acd756803dd15e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570880"
---
# <a name="what-is-azure-australia"></a>Co to jest Azure Australia?

W 2014 platforma Azure została uruchomiona w Australii z dwoma regionami: Australia Wschodnia (Sydney) i Australia Południowo-Wschodnia (Melbourne). W kwietniu 2018 zostały uruchomione dwa nowe regiony platformy Azure znajdujące się w Canberra — Australia Środkowa i Australia Środkowa 2. Regiony Australii Środkowej i Australii środkowej 2 są przeznaczone do celów zaspokajania potrzeb instytucji rządowych i krytycznych infrastruktury krajowej oraz oferują wyspecjalizowaną łączność i elastyczność, dzięki czemu możesz lokalizować systemy obok chmury, z poziomu zabezpieczenia i odporność są oczekiwane tylko w przypadku sieci sklasyfikowanych przez tajne. Azure Australia to platforma do cyfrowej transformacji instytucji rządowych i krytycznych infrastruktury krajowej, a jedyną chmurą o znaczeniu krytycznym dostępną w Australii przeznaczonym dla potrzeb.

Istnieją konkretne australijskie wymagania rządowe dotyczące łączenia się z, wykorzystywania i współpracy w [Microsoft Azure Australii](https://azure.microsoft.com/global-infrastructure/australia/) dla australijskich danych i systemów rządowych. Zasoby na tej stronie zawierają również ogólne wskazówki dotyczące wszystkich klientów, którzy mają określony fokus w zakresie bezpiecznej konfiguracji i działania.

Zapoznaj się z informacjami na stronie Australia [portalu zaufania usługi firmy Microsoft](https://aka.ms/au-irap) , aby uzyskać aktualne informacje na temat ocen (IRAP), certyfikacji i włączenia zakwalifikowanych informacji o ZaCloud Services świadczeniu o usłudze Azure Australia (CCSL). Na stronie Australii znajdziesz również inne porady firmy Microsoft właściwe dla instytucji rządowych i kluczowych dla dostawców infrastruktury.

## <a name="principles-for-securing-customer-data-in-azure-australia"></a>Zasady zabezpieczania danych klienta w Australii platformy Azure

Usługa Azure Australia udostępnia szereg funkcji i usług, których można użyć do tworzenia rozwiązań w chmurze w celu spełnienia potrzebnych danych. Zgodne rozwiązanie klienta nie jest więcej niż efektywną implementacją wbudowanych możliwości platformy Azure w Australii, która jest powiązana z pełną zasadą bezpieczeństwa danych.

W przypadku hostowania rozwiązania w programie Azure Australia firma Microsoft obsługuje wiele z tych wymagań na poziomie infrastruktury chmurowej.

Na poniższym diagramie przedstawiono model z kompleksową obroną platformy Azure. Na przykład firma Microsoft oferuje podstawową infrastrukturę chmurową DDoS, wraz z możliwościami klienta, takimi jak urządzenia zabezpieczeń lub usługi w warstwie Premium DDoS, dla potrzeb aplikacji specyficznych dla klientów.

![tekst alternatywny](media/defenceindepth.png)

W tych artykułach przedstawiono podstawowe zasady zabezpieczania usług i aplikacji oraz wskazówki i najlepsze rozwiązania dotyczące stosowania tych zasad. Innymi słowy, w jaki sposób klienci powinni korzystać z usługi Azure Australia w celu spełnienia obowiązków i obowiązków, które są wymagane w przypadku rozwiązania, które obsługuje dane osobowe i informacje klasyfikowane od instytucji rządowych.

Istnieją dwie kategorie dokumentacji dla instytucji rządowych Australii przemieszczonych na platformie Azure.

## <a name="security-in-azure-australia"></a>Bezpieczeństwo w programie Azure Australia

Tożsamość, kontrola dostępu oparta na rolach, ochrona danych za pośrednictwem szyfrowania i zarządzania prawami oraz skuteczna kontrola i sterowanie konfiguracją to kluczowe elementy, które należy wdrożyć. W tej sekcji znajduje się szereg artykułów objaśniających wbudowane możliwości platformy Azure i ich powiązania z istotnymi środkami ISM i ASD 8.

Dostęp do tych artykułów można uzyskać za pomocą menu w obszarze *koncepcje — > zabezpieczenia w programie Azure Australia*.

## <a name="gateways-in-azure-australia"></a>Bramy w Australii platformy Azure

Kolejną czynnością kluczową dla instytucji rządowych jest ustanowienie funkcji zabezpieczeń obwodowych. Te funkcje są nazywane bezpiecznymi bramami internetowymi (SIG) i w przypadku korzystania z platformy Azure jest odpowiedzialny za zapewnienie, że te zabezpieczenia są stosowane. Firma Microsoft nie obsługuje SIG; jednak przez połączenie naszych usług sieciowych usługi Edge, które chronią wszystkich klientów, oraz określonych usług wdrożonych w środowisku platformy Azure można korzystać z równoważnej możliwości.

Dostęp do tych artykułów można uzyskać za pomocą menu w obszarze *koncepcje — > bramy w Australii platformy Azure*.

## <a name="next-steps"></a>Następne kroki

* Jeśli fokus klucza zabezpiecza dane na platformie Azure, Zacznij od [zabezpieczeń danych](secure-your-data.md)
* Jeśli Twój klucz koncentruje się na tworzeniu bramy na platformie Azure, Zacznij od [inspekcji, rejestrowania i widoczności bramy](gateway-log-audit-visibility.md).
