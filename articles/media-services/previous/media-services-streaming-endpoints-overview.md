---
title: Omówienie końcowego przesyłania strumieniowego usługi Media Services na platformie Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie usługi Azure Media Services, punkty końcowe przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a45e2af6f2cb9c105c084585a03a6de615fa1397
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64573041"
---
# <a name="streaming-endpoints-overview"></a>Omówienie punktów końcowych przesyłania strumieniowego  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

W programie Microsoft Azure Media Services (AMS), **punkt końcowy przesyłania strumieniowego** reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub aby Content Delivery Network (CDN) w celu dalszego rozpowszechniania. Media Services udostępnia również bezproblemową integrację usługi Azure CDN. Strumień wychodzący, z usługi StreamingEndpoint może być strumień na żywo, wideo na żądanie lub pobierania progresywnego elementów zawartości w ramach konta usługi Media Services. Każde konto usługi Azure Media Services obejmuje domyślne StreamingEndpoint. Dodatkowe punkty mogą być tworzone w ramach konta. Istnieją dwie wersje punkty, 1.0 i 2.0. Począwszy od stycznia 2017 10, wszystkie nowo utworzone konta usługi AMS zostaną uwzględnione w wersji 2.0 **domyślne** StreamingEndpoint. Dodatkowe punkty końcowe przesyłania strumieniowego, które dodajesz do tego konta będą również w wersji 2.0. Ta zmiana nie ma wpływu na istniejące konta; istniejące punkty będą w wersji 1.0 i mogą być uaktualniane do wersji 2.0. Dzięki tej zmianie będą zmiany zachowania, rozliczeń i funkcji (Aby uzyskać więcej informacji, zobacz **przesyłania strumieniowego, typy i wersje** sekcji opisane poniżej).

Usługa Azure Media Services dodane następujące właściwości do jednostki punkt końcowy przesyłania strumieniowego: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Aby uzyskać szczegółowe omówienie tych właściwości, zobacz [to](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Po utworzeniu konta usługi Azure Media Services domyślny standardowy punkt końcowy przesyłania strumieniowego jest tworzony w **zatrzymane** stanu. Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego. W zależności od dostępności usługi Azure CDN w regionie docelowym, domyślnie nowo utworzony domyślny punkt końcowy przesyłania strumieniowego obejmowały "StandardVerizon" sieć CDN integracji dostawcy. 
                
> [!NOTE]
> Integracja z usługą Azure CDN można wyłączyć, przed rozpoczęciem punkt końcowy przesyłania strumieniowego. `hostname` i adres URL przesyłania strumieniowego pozostaje taki sam, czy włączyć sieć CDN.

Ten temat zawiera omówienie główne funkcje, które są dostarczane przez punkty końcowe przesyłania strumieniowego.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Dla domyślnego punktu końcowego: `{AccountName}.streaming.mediaservices.windows.net`

Aby uzyskać wszystkie dodatkowe punkty końcowe: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Przesyłanie strumieniowe, typy i wersje

### <a name="standardpremium-types-version-20"></a>Typy standardowa/Premium (wersja 2.0)

Począwszy od stycznia 2017 wersję usługi Media Services, istnieją dwa typy przesyłania strumieniowego: **Standardowa** (wersja zapoznawcza) i **Premium**. Te typy są częścią wersji punktu końcowego przesyłania strumieniowego "2.0".


|Typ|Opis|
|--------|--------|  
|**Standardowa**|Domyślny punkt końcowy przesyłania strumieniowego jest **standardowa** wpisz, można zmienić, dostosowując jednostek przesyłania strumieniowego typu Premium.|
|**Premium** |Ta opcja jest odpowiednia dla profesjonalnych scenariusze, które wymagają wyższej skali lub formantu. Przenieś do **Premium** typu przez dostosowywanie jednostek przesyłania strumieniowego.<br/>Dedykowany punkty końcowe przesyłania strumieniowego na żywo w izolowanym środowisku, a nie konkurują o zasoby.|

Klienci, którzy chcą do dostarczania zawartości do dużej liczby odbiorców w Internecie zalecamy, aby włączyć usługi CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać więcej informacji, zobacz [porównania przesyłania strumieniowego typy](#comparing-streaming-types) tej sekcji.

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

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Rozliczenia|
|--------------|----------|-----------------|-----------------|-----------------|
|Wdrożenie klasyczne|1.0|0|Nie dotyczy|Wolne|
|Standardowy punkt końcowy przesyłania strumieniowego (wersja zapoznawcza)|2.0|0|Tak|Płatne|
|Jednostki przesyłania strumieniowego Premium|1.0|>0|Tak|Płatne|
|Jednostki przesyłania strumieniowego Premium|2.0|>0|Tak|Płatne|

### <a name="features"></a>Funkcje

Cecha|Standardowa (Standard)|Premium
---|---|---
Pierwsze 15 dni wolne <sup>1</sup>| Tak |Nie
Przepływność |Do 600 MB/s i zapewnia znacznie większą przepływność skuteczne stosowania sieci CDN.|200 MB/s dla przesyłania strumieniowego (SU) jednostki. Zapewnia znacznie większą przepływność skuteczne, stosowania sieci CDN.
CDN|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Yes|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatyczne jest skalowany w górę docelowe przepływności.|Dodatkowe jednostki przesyłania strumieniowego.
Host filtrowanie/G20/Custom IP <sup>2</sup>|Yes|Yes
Pobierania progresywnego|Yes|Yes
Zalecane użycie |Zalecane w przypadku większość przesyłania strumieniowego scenariuszy.|Użycie Professional. 

<sup>1</sup> bezpłatnej wersji próbnej ma zastosowanie tylko do konta usług media nowo utworzony i domyślnego punktu końcowego przesyłania strumieniowego.<br/>
<sup>2</sup> warunkiem bezpośrednio na punkt końcowy przesyłania strumieniowego nie włączono usługę CDN w punkcie końcowym.<br/>

Aby uzyskać informacje o umowie SLA, zobacz [cennik i umowy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migracja między typami

Od | Do | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa (Standard)|Należy na uczestnictwo
Wdrożenie klasyczne|Premium| Skala (dodatkowe jednostki przesyłania strumieniowego)
Standard/Premium|Wdrożenie klasyczne|Nie są dostępne (Jeśli punkt końcowy przesyłania strumieniowego w wersji 1.0. Istnieje możliwość zmiany do klasycznego modelu z ustawieniem scaleunits "0")
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

