---
title: Omówienie usługi Azure Media Services streamingu punktu końcowego | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie punktów końcowych przesyłania strumieniowego usługi Azure Media Services.
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
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885640"
---
# <a name="streaming-endpoints-overview"></a>Omówienie punktów końcowych przesyłania strumieniowego  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W usłudze Microsoft Azure Media Services (AMS) **punkt końcowy przesyłania strumieniowego** reprezentuje usługę przesyłania strumieniowego, która może dostarczać zawartość bezpośrednio do aplikacji odtwarzacza klienckiego lub do sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji. Usługa Media Services zapewnia również bezproblemową integrację usługi Azure CDN. Strumień wychodzący z usługi StreamingEndpoint może być transmisją na żywo, wideo na żądanie lub progresywnym pobieraniem zasobu na koncie usługi Media Services. Każde konto usługi Azure Media Services zawiera domyślny punkt przesyłania strumieniowego. Dodatkowe StreamingEndpoints można utworzyć za konto. Istnieją dwie wersje StreamingEndpoints, 1.0 i 2.0. Począwszy od 10 stycznia 2017 r., wszystkie nowo utworzone konta AMS będą zawierać **domyślny** program StreamingEndpoint w wersji 2.0. Dodatkowe punkty końcowe przesyłania strumieniowego, które można dodać do tego konta będzie również w wersji 2.0. Ta zmiana nie wpłynie na istniejące rachunki; istniejące punkty StreamingEndpoints będą w wersji 1.0 i mogą zostać uaktualnione do wersji 2.0. Dzięki tej zmianie nastąpi zachowanie, rozliczenia i zmiany funkcji (aby uzyskać więcej informacji, zobacz sekcję **Typy przesyłania strumieniowego i wersje** udokumentowane poniżej).

Usługa Azure Media Services dodała następujące właściwości do jednostki punktu końcowego przesyłania strumieniowego: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Aby uzyskać szczegółowe omówienie tych właściwości, zobacz [ten](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Podczas tworzenia konta usługi Azure Media Services domyślny standardowy punkt końcowy przesyłania strumieniowego jest tworzony dla Ciebie w stanie **Zatrzymane.** Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego. W zależności od dostępności usługi Azure CDN w regionie docelowym, domyślnie nowo utworzony domyślny punkt końcowy przesyłania strumieniowego obejmuje również integrację dostawcy sieci CDN StandardVerizon.Depending on the Azure CDN availability in the targeted region, default newly created default streaming endpoint also includes "StandardVerizon" CDN provider integration. 
                
> [!NOTE]
> Integracja usługi Azure CDN można wyłączyć przed uruchomieniem punktu końcowego przesyłania strumieniowego. Adres `hostname` URL i przesyłania strumieniowego pozostają takie same, niezależnie od tego, czy włączysz sieć CDN.

W tym temacie przedstawiono omówienie głównych funkcji, które są dostarczane przez punkty końcowe przesyłania strumieniowego.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Dla domyślnego punktu końcowego:`{AccountName}.streaming.mediaservices.windows.net`

W przypadku dodatkowych punktów końcowych:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Typy i wersje przesyłania strumieniowego

### <a name="standardpremium-types-version-20"></a>Typy standardowe/premium (wersja 2.0)

Począwszy od wersji programu Media Services ze stycznia 2017 r., masz dwa typy przesyłania strumieniowego: **Standardowy** (wersja zapoznawcza) i **Premium**. Te typy są częścią wersji punktu końcowego przesyłania strumieniowego "2.0".


|Typ|Opis|
|--------|--------|  
|**Standardowa**|Domyślny punkt końcowy przesyłania strumieniowego jest typu **standardowego,** można zmienić na typ Premium przez dostosowanie jednostek przesyłania strumieniowego.|
|**Premium** |Ta opcja jest odpowiednia dla profesjonalnych scenariuszy, które wymagają większej skali lub kontroli. Przenieś do typu **Premium,** dostosowując jednostki przesyłania strumieniowego.<br/>Dedykowane punkty końcowe przesyłania strumieniowego są dostępne w odizolowanym środowisku i nie konkurują o zasoby.|

W przypadku klientów, którzy chcą dostarczać zawartość do dużych odbiorców internetu, zaleca się włączenie sieci CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać bardziej szczegółowe informacje, zobacz [porównanie typów przesyłania strumieniowego](#comparing-streaming-types) po sekcji.

### <a name="classic-type-version-10"></a>Typ klasyczny (wersja 1.0)

Dla użytkowników, którzy utworzyli konta USŁUGI AMS przed wydaniem z 10 stycznia 2017 r., masz **klasyczny** typ punktu końcowego przesyłania strumieniowego. Ten typ jest częścią wersji punktu końcowego przesyłania strumieniowego "1.0".

Jeśli **twoja wersja "1.0"** streaming punkt końcowy ma >= 1 premium jednostek strumieniowych (SU), będzie to punkt końcowy przesyłania strumieniowego w wersji premium i zapewni wszystkie funkcje AMS (podobnie jak **typ Standard/Premium)** bez żadnych dodatkowych kroków konfiguracji.

>[!NOTE]
>**Klasyczne** punkty końcowe przesyłania strumieniowego (wersja "1.0" i 0 SU), zapewnia ograniczone funkcje i nie zawiera umowy SLA. Zaleca się migrację do typu **standardowego,** aby uzyskać lepsze środowisko i używać funkcji, takich jak dynamiczne pakowanie lub szyfrowanie i inne funkcje pochodzące z typu **standardowego.** Aby przeprowadzić migrację do typu **standardowego,** przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz pozycję Zapisz się **do standardu**. Aby uzyskać więcej informacji na temat migracji, zobacz sekcję [migracji.](#migration-between-types)
>
>Uważaj, że ta operacja nie może zostać wycofana i ma wpływ na ceny.
>
 
## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

### <a name="versions"></a>Wersje

|Typ|StreamingEndpointVersion|Jednostki skalne|CDN|Rozliczenia|
|--------------|----------|-----------------|-----------------|-----------------|
|Wdrożenie klasyczne|1.0|0|Nie dotyczy|Bezpłatna|
|Standardowy punkt końcowy przesyłania strumieniowego (wersja zapoznawcza)|2.0|0|Tak|Wypłacane|
|Jednostki strumieniowe premium|1.0|>0|Tak|Wypłacane|
|Jednostki strumieniowe premium|2.0|>0|Tak|Wypłacane|

### <a name="features"></a>Funkcje

Funkcja|Standardowa|Premium
---|---|---
Przepływność |Do 600 Mb/s i może zapewnić znacznie wyższą efektywną przepustowość, gdy używana jest sieć CDN.|200 Mb/s na jednostkę przesyłania strumieniowego (SU). Może zapewnić znacznie wyższą efektywną przepustowość, gdy używana jest sieć CDN.
CDN|Usługa Azure CDN, usługa CDN innej firmy lub sieć CDN.|Usługa Azure CDN, usługa CDN innej firmy lub sieć CDN.
Rozliczenia są naliczane proporcjonalnie| Dzienna|Dzienna
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie skaluje się do docelowej przepływności.|Dodatkowe jednostki przesyłania strumieniowego.
Filtrowanie IP/G20/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane dla większości scenariuszy przesyłania strumieniowego.|Profesjonalne użytkowanie. 

<sup>1</sup> Używane tylko bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

Aby uzyskać informacje o umowy SLA, zobacz [Cennik i umowy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migracja między typami

Z | Do | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa|Musisz wyrazić zgodę
Wdrożenie klasyczne|Premium| Skala (dodatkowe jednostki przesyłania strumieniowego)
Standard/Premium|Wdrożenie klasyczne|Niedostępne(Jeśli wersja punktu końcowego przesyłania strumieniowego jest 1.0. Dozwolone jest przejście na klasyczne z ustawieniem jednostek skalnych na "0")
Standard (z/bez CDN)|Premium w tych samych konfiguracjach|Dozwolone w stanie **started.** (za pośrednictwem portalu Azure)
Premium (z/bez CDN)|Standard z tymi samymi konfiguracjami|Dozwolone w stanie **uruchomionym** (za pośrednictwem witryny Azure portal)
Standard (z/bez CDN)|Premium z różnymi configami|Dozwolone w stanie **zatrzymania** (za pośrednictwem witryny Azure portal). Niedozwolone w stanie uruchomienia.
Premium (z/bez CDN)|Standard z różnymi configami|Dozwolone w stanie **zatrzymania** (za pośrednictwem witryny Azure portal). Niedozwolone w stanie uruchomienia.
Wersja 1.0 z SU >= 1 z CDN|Standard/Premium bez CDN|Dozwolone w stanie **zatrzymania.** Niedozwolone w stanie **uruchomionym.**
Wersja 1.0 z SU >= 1 z CDN|Standard z/bez CDN|Dozwolone w stanie **zatrzymania.** Niedozwolone w stanie **uruchomionym.** Wersja 1.0 CDN zostanie usunięta, a nowa utworzona i uruchomiona.
Wersja 1.0 z SU >= 1 z CDN|Premium z/bez CDN|Dozwolone w stanie **zatrzymania.** Niedozwolone w stanie **uruchomionym.** Klasyczna sieć CDN zostanie usunięta, a nowa zostanie utworzona i uruchomiona.

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

