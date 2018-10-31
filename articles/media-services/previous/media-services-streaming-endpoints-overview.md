---
title: Omówienie końcowego przesyłania strumieniowego usługi Media Services na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie usługi Azure Media Services, punkty końcowe przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: juliako
ms.openlocfilehash: 06f219b9cf7d17e80699aebc1082b14e2de45c8b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240226"
---
# <a name="streaming-endpoints-overview"></a>Omówienie punktów końcowych przesyłania strumieniowego 

## <a name="overview"></a>Przegląd

W programie Microsoft Azure Media Services (AMS), **punkt końcowy przesyłania strumieniowego** reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub aby Content Delivery Network (CDN) w celu dalszego rozpowszechniania. Media Services udostępnia również bezproblemową integrację usługi Azure CDN. Strumień wychodzący, z usługi StreamingEndpoint może być strumień na żywo, wideo na żądanie lub pobierania progresywnego elementów zawartości w ramach konta usługi Media Services. Każde konto usługi Azure Media Services obejmuje domyślne StreamingEndpoint. Dodatkowe punkty mogą być tworzone w ramach konta. Istnieją dwie wersje punkty, 1.0 i 2.0. Począwszy od stycznia 2017 10, wszystkie nowo utworzone konta usługi AMS zostaną uwzględnione w wersji 2.0 **domyślne** StreamingEndpoint. Dodatkowe punkty końcowe przesyłania strumieniowego, które dodajesz do tego konta będą również w wersji 2.0. Ta zmiana nie ma wpływu na istniejące konta; istniejące punkty będą w wersji 1.0 i mogą być uaktualniane do wersji 2.0. Dzięki tej zmianie będą zmiany zachowania, rozliczeń i funkcji (Aby uzyskać więcej informacji, zobacz **przesyłania strumieniowego, typy i wersje** sekcji opisane poniżej).

Ponadto, począwszy od wersji 2.15 (wydany w styczniu 2017 roku), usługi Azure Media Services dodano następujące właściwości do jednostki punkt końcowy przesyłania strumieniowego: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Aby uzyskać szczegółowe omówienie tych właściwości, zobacz [to](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Po utworzeniu konta usługi Azure Media Services domyślny standardowy punkt końcowy przesyłania strumieniowego jest tworzony w **zatrzymane** stanu. Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego. W zależności od dostępności usługi Azure CDN w regionie docelowym, domyślnie nowo utworzony domyślny punkt końcowy przesyłania strumieniowego obejmowały "StandardVerizon" sieć CDN integracji dostawcy. 

>[!NOTE]
>Integracja z usługą Azure CDN można wyłączyć, przed rozpoczęciem punkt końcowy przesyłania strumieniowego.

Ten temat zawiera omówienie główne funkcje, które są dostarczane przez punkty końcowe przesyłania strumieniowego.

## <a name="streaming-types-and-versions"></a>Przesyłanie strumieniowe, typy i wersje

### <a name="standardpremium-types-version-20"></a>Typy standardowa/Premium (wersja 2.0)

Począwszy od stycznia 2017 wersję usługi Media Services, istnieją dwa typy przesyłania strumieniowego: **standardowa** i **Premium**. Te typy są częścią wersji punktu końcowego przesyłania strumieniowego "2.0".

Typ|Opis
---|---
**Standardowa**|Jest to opcja domyślna, która będzie działać w przypadku większości scenariuszy.<br/>Po wybraniu tej opcji Pobierz umowę SLA/limited, pierwsze 15 dni, po uruchomieniu punktu końcowego przesyłania strumieniowego jest bezpłatna.<br/>Jeśli tworzysz więcej niż jedną punkty końcowe, przesyłania strumieniowego tylko pierwszy z nich jest bezpłatna przez pierwsze 15 dni, inne są rozliczane zaraz po ich uruchomieniu. <br/>Należy pamiętać, że bezpłatnej wersji próbnej ma zastosowanie tylko do konta usługi media nowo utworzony i domyślny punkt końcowy przesyłania strumieniowego. Istniejące punkty końcowe przesyłania strumieniowego i dodatkowo utworzone punkty końcowe przesyłania strumieniowego nie zawiera bezpłatny okres próbny jeszcze są uaktualniane do wersji 2.0 lub są one tworzone w wersji 2.0.
**Premium**|Ta opcja jest odpowiednia dla profesjonalnych scenariusze, które wymagają wyższej skali lub formantu.<br/>Zmiennej umowy SLA, który jest oparty na premium przesyłania strumieniowego (SU) jednostki nabyte miejsce w dedykowanej punkty końcowe przesyłania strumieniowego na żywo w izolowanym środowisku, a nie konkurują o zasoby.

Aby uzyskać więcej informacji, zobacz **porównania przesyłania strumieniowego typy** tej sekcji.

### <a name="classic-type-version-10"></a>Typ klasyczny (w wersji 1.0)

Dla użytkowników, którzy utworzyli konta usługi AMS przed wydaniem stycznia 2017 10, masz **klasycznego** typ punktu końcowego przesyłania strumieniowego. Ten typ jest częścią wersji punktu końcowego przesyłania strumieniowego "1.0".

Jeśli Twoje **wersji "1.0"** punkt końcowy przesyłania strumieniowego jest > = 1 jednostki (SU) przesyłania strumieniowego premium będzie punktu końcowego przesyłania strumieniowego premium i zapewnia wszystkie funkcje usługi AMS (podobnie jak w przypadku **standardowa/Premium** typu) bez żadnych dodatkowych czynności konfiguracyjnych.

>[!NOTE]
>**Klasyczne** punkty końcowe przesyłania strumieniowego (w wersji "1.0" i 0 SU), zapewnia ograniczone funkcje i nie ma umów SLA. Zaleca się migrację do **standardowa** typu wygodniejsze i korzystać z funkcji, takich jak dynamiczne tworzenie pakietów i szyfrowania i inne funkcje, które są dostarczane z **standardowa** typu. Przeprowadzić migrację do **standardowa** typu, przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz **zgłoszenie zgody na uczestnictwo w usłudze Standard**. Aby uzyskać więcej informacji na temat migracji, zobacz [migracji](#migration-between-types) sekcji.
>
>Należy pamiętać, że tej operacji nie można wycofać i ma wpływ na cenę.
>
 
## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

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
Bezpłatne pierwsze 15 dni| Yes |Nie
Przepływność |Do 600 MB/s, gdy nie jest używana sieć CDN systemu Azure. Skaluje się przy użyciu usługi CDN.|200 MB/s dla przesyłania strumieniowego (SU) jednostki. Skaluje się przy użyciu usługi CDN.
Umowa SLA | 99.9|Dostępność na poziomie 99,9 (200 MB/s na SU).
CDN|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Yes|Yes
Dynamiczne tworzenie pakietów|Yes|Yes
Skalowanie|Automatyczne jest skalowany w górę docelowe przepływności.|Dodatkowe jednostki przesyłania strumieniowego
IP filtrowanie/G20/niestandardowego hosta|Yes|Yes
Pobierania progresywnego|Yes|Yes
Zalecane użycie |Zalecane w przypadku większość przesyłania strumieniowego scenariuszy.|Użycie Professional.<br/>Jeśli Twoim zdaniem mogą mieć potrzeb poza Standard. Skontaktuj się z nami (amsstreaming@microsoft.com) Jeśli oczekujesz, rozmiar współbieżnych odbiorców większych niż 50 000 osób przeglądających.


## <a name="migration-between-types"></a>Migracja między typami

Z | Do | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa (Standard)|Należy na uczestnictwo
Wdrożenie klasyczne|Premium| Skala (dodatkowe jednostki przesyłania strumieniowego)
Standardowa/Premium|Wdrożenie klasyczne|Nie są dostępne (Jeśli punkt końcowy przesyłania strumieniowego w wersji 1.0. Istnieje możliwość zmiany do klasycznego modelu z ustawieniem scaleunits "0")
Standardowa (lub bez sieci CDN)|Premium z tej samej konfiguracji|Dozwolone w **pracę** stanu. (za pośrednictwem witryny Azure portal)
Premium (lub bez sieci CDN)|Standardowa przy użyciu tej samej konfiguracji|Dozwolone w **pracę** stanu (za pośrednictwem witryny Azure portal)
Standardowa (lub bez sieci CDN)|Premium z różnych konfiguracji|Dozwolone w **zatrzymana** stanu (za pośrednictwem witryny Azure portal). Nie są dozwolone w stanie uruchomienia.
Premium (lub bez sieci CDN)|Standard z różnych konfiguracji|Dozwolone w **zatrzymana** stanu (za pośrednictwem witryny Azure portal). Nie są dozwolone w stanie uruchomienia.
W wersji 1.0 za pomocą polecenia SU > = 1 sieci CDN|Standardowa/Premium nie sieci CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **pracę** stanu.
W wersji 1.0 za pomocą polecenia SU > = 1 sieci CDN|Standardowa/bez usługi CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **pracę** stanu. W wersji 1.0 usługi CDN będą usunięte, a nowe jeden utworzona i uruchomiona.
W wersji 1.0 za pomocą polecenia SU > = 1 sieci CDN|Premium/bez usługi CDN|Dozwolone w **zatrzymana** stanu. Nie są dozwolone w **pracę** stanu. Klasyczne usługi CDN będą usunięte, a nowe jeden utworzona i uruchomiona.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

