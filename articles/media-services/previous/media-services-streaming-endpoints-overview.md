---
title: Omówienie usługi Azure Media Services przesyłania strumieniowego punktu końcowego | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie usługi Azure Media Services punkty końcowe przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 7b4077747d4c9ec581496786063fc8fbe0c6b247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="streaming-endpoints-overview"></a>Omówienie punktów końcowych przesyłania strumieniowego 

## <a name="overview"></a>Przegląd

W programie Microsoft Azure Media Services (AMS), **punktu końcowego przesyłania strumieniowego** reprezentuje przesyłania strumieniowego usługa, która może dostarczać zawartość bezpośrednio do aplikacji klienckich odtwarzacza lub do Content Delivery Network (CDN) w celu dalszej dystrybucji. Usługa Media Services udostępnia również bezproblemową integrację usługi Azure CDN. Strumienia wychodzącego z usługi StreamingEndpoint może być strumień na żywo, wideo na żądanie lub pobierania progresywnego zawartości na koncie usługi Media Services. Każde konto usługi Azure Media Services zawiera domyślne StreamingEndpoint. Dodatkowe punkty końcowe mogą być tworzone w ramach konta. Istnieją dwie wersje punkty końcowe, 1.0 i 2.0. Począwszy od stycznia 2017 10, wszystkie nowo utworzonego konta usługi AMS zostaną uwzględnione w wersji 2.0 **domyślne** StreamingEndpoint. Dodatkowe punktów końcowych przesyłania strumieniowego dodanych do tego konta będą również w wersji 2.0. Ta zmiana nie ma wpływu na istniejące konta; istniejące punkty końcowe będą w wersji 1.0, a można uaktualnić do wersji 2.0. Ta zmiana będzie zmiany zachowania, rozliczeń i funkcji (Aby uzyskać więcej informacji, zobacz **przesyłania strumieniowego, typy i wersje** sekcji opisane poniżej).

Ponadto, począwszy od wersji 2.15 (wydane w stycznia 2017 r), usługi Azure Media Services dodane następujące właściwości jednostki przesyłania strumieniowego punktu końcowego: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Aby uzyskać szczegółowe informacje o tych właściwości, zobacz [to](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Podczas tworzenia konta usługi Azure Media Services domyślnie standardowego punktu końcowego przesyłania strumieniowego jest tworzony w **zatrzymane** stanu. Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego. W zależności od dostępności sieci CDN w warstwie Azure w docelowym regionie, domyślnie nowo utworzony domyślny punktu końcowego przesyłania strumieniowego obejmuje również "StandardVerizon" CDN integracji dostawcy. 

>[!NOTE]
>Integracja z usługą Azure CDN, można wyłączyć przed rozpoczęciem punktu końcowego przesyłania strumieniowego.

Ten temat zawiera omówienie głównych funkcji, które są udostępniane przez punkty końcowe przesyłania strumieniowego.

## <a name="streaming-types-and-versions"></a>Typy i wersje przesyłania strumieniowego

### <a name="standardpremium-types-version-20"></a>Typy standardowe/Premium (wersja 2.0)

Począwszy od wersji stycznia 2017 Media Services, istnieją dwa typy przesyłania strumieniowego: **standardowe** i **Premium**. Te typy są częścią wersji punktu końcowego przesyłania strumieniowego "2.0".

Typ|Opis
---|---
**Standardowa**|To jest opcją domyślną, która będzie działać w większości scenariuszy.<br/>Po wybraniu tej opcji Pobierz/limited SLA, pierwszych 15 dni, po uruchomieniu punktu końcowego przesyłania strumieniowego wolnego.<br/>W przypadku utworzenia więcej niż jedna punktów końcowych, tylko pierwsza z nich jest bezpłatna dla pierwszych 15 dni, inne są rozliczane, jak je uruchomić. <br/>Należy pamiętać, że bezpłatnej wersji próbnej dotyczy tylko konta usług media nowo utworzona i domyślnego punktu końcowego przesyłania strumieniowego. Istniejące punkty końcowe przesyłania strumieniowego i dodatkowo utworzony punktów końcowych przesyłania strumieniowego nie zawiera bezpłatny okres próbny nawet zostaną uaktualnione do wersji 2.0 lub zostały utworzone w wersji 2.0.
**Premium**|Ta opcja jest odpowiednia dla specjalistów scenariusze, które wymagają większej skali lub kontrolki.<br/>Zmiennej umowy SLA, oparty na premium przesyłania strumieniowego pojemność jednostki (SU) zakupionych dedykowanych punktów końcowych przesyłania strumieniowego na żywo w izolowanym środowisku i nie powodowały konfliktu dla zasobów.

Aby uzyskać szczegółowe informacje, zobacz **porównania przesyłania strumieniowego typy** następujących sekcji.

### <a name="classic-type-version-10"></a>Typ klasyczny (w wersji 1.0)

Dla użytkowników, którzy utworzone konta usługi AMS przed wydaniem 10 stycznia 2017 r, masz **klasycznego** typ punktu końcowego przesyłania strumieniowego. Ten typ jest częścią przesyłania strumieniowego punktu końcowego wersji "1.0".

Jeśli Twoje **wersji "1.0"** punktu końcowego przesyłania strumieniowego ma > = 1 — wersja premium ("SU"), jednostki przesyłania strumieniowego będzie premium punktu końcowego przesyłania strumieniowego i zapewnia wszystkie funkcje AMS (podobnie jak **Standard/Premium** typu) bez żadnych dodatkowych czynności konfiguracyjnych.

>[!NOTE]
>**Klasycznym** punkty końcowe przesyłania strumieniowego (w wersji "1.0" i 0 SU), udostępnia funkcje ograniczona i nie zawiera umowy dotyczącej poziomu usług. Zaleca się do migracji do **standardowe** typu, aby uzyskać lepsze środowisko pracy i do używania funkcji dynamicznego tworzenia pakietów lub szyfrowania i inne funkcje, które pochodzą z **standardowe** typu. Aby przeprowadzić migrację do **standardowe** typu, przejdź do [portalu Azure](https://portal.azure.com/) i wybierz **zgłoszenie zgody na uczestnictwo w ze standardem**. Aby uzyskać więcej informacji na temat migracji, zobacz [migracji](#migration-between-types) sekcji.
>
>Należy pamiętać, że ta operacja nie może zostać wycofana i ma wpływ cenową.
>
 
## <a name="comparing-streaming-types"></a>Porównanie typów przesyłania strumieniowego

### <a name="versions"></a>Wersje

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Rozliczenia|Umowa SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Wdrożenie klasyczne|1.0|0|Nie dotyczy|Bezpłatna|Nie dotyczy|
|Standardowy punkt końcowy przesyłania strumieniowego|2.0|0|Yes|Płatne|Yes|
|Jednostki przesyłania strumieniowego Premium|1.0|>0|Yes|Płatne|Yes|
|Jednostki przesyłania strumieniowego Premium|2.0|>0|Yes|Płatne|Yes|

### <a name="features"></a>Funkcje

Cecha|Standardowa (Standard)|Premium
---|---|---
Pierwszy 15 dni wolne| Yes |Nie
Przepływność |Do 600 MB/s, gdy nie jest używana usługa Azure CDN. Można skalować usługi CDN.|200 MB/s na jednostka (SU). Można skalować usługi CDN.
Umowa SLA | 99.9|99,9 (200 MB/s na SU).
CDN|Usługi Azure CDN, innej sieci CDN lub nie CDN.|Usługi Azure CDN, innej sieci CDN lub nie CDN.
Karta jest proporcjonalna| Codziennie|Codziennie
Szyfrowanie dynamiczne|Yes|Yes
Dynamiczne tworzenie pakietów|Yes|Yes
Skalowanie|Automatycznie skalować do docelowej przepływności.|Dodatkowe jednostki przesyłania strumieniowego
IP filtrowania/G20/niestandardowy host|Yes|Yes
Pobierania progresywnego|Yes|Yes
Zalecane użycie |Zalecane w przypadku większość przesyłania strumieniowego scenariuszy.|Użycie Professional.<br/>Jeśli uważasz, że masz potrzeb poza Standard. Skontaktuj się z nami (amsstreaming@microsoft.com) Jeśli rozmiar równoczesnych odbiorców większych niż 50 000 przeglądarki.


## <a name="migration-between-types"></a>Migracja między typami

Od | Do | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa (Standard)|Należy wybrać
Wdrożenie klasyczne|Premium| Skala (dodatkowe jednostki przesyłania strumieniowego)
Standard/Premium|Wdrożenie klasyczne|Niedostępne (jeśli punktu końcowego przesyłania strumieniowego w wersji 1.0. Istnieje możliwość zmiany klasyczne z ustawień scaleunits "0")
Standard (lub bez CDN)|Premium z tej samej konfiguracji|Dozwolone w **uruchomiona** stanu. (za pośrednictwem portalu Azure)
Premium (lub bez CDN)|Standard z tej samej konfiguracji|Dozwolone w **uruchomiona** stanu (za pośrednictwem portalu Azure)
Standard (lub bez CDN)|Premium z różnych konfiguracji|Dozwolone w **zatrzymana** stanu (za pośrednictwem portalu Azure). Nie są dozwolone w stanie uruchomienia.
Premium (lub bez CDN)|Standard z różnych konfiguracji|Dozwolone w **zatrzymana** stanu (za pośrednictwem portalu Azure). Nie są dozwolone w stanie uruchomienia.
W wersji 1.0 z SU > = 1 z sieci CDN|Standard/Premium z nie CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **uruchomiona** stanu.
W wersji 1.0 z SU > = 1 z sieci CDN|Standard lub bez CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **uruchomiona** stanu. W wersji 1.0 CDN będzie jednej usunięta, a nowe utworzone i uruchomione.
W wersji 1.0 z SU > = 1 z sieci CDN|Premium lub bez CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **uruchomiona** stanu. Klasyczne sieci CDN będzie jednej usunięta, a nowe utworzone i uruchomione.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

