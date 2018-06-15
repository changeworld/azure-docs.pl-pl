---
title: Sprawdź, czy wyjście indeksatora wideo Azure | Dokumentacja firmy Microsoft
description: W tym temacie sprawdza, czy dane wyjściowe indeksatora wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349877"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Sprawdź, czy wyjście wideo indeksatora produkowane przez interfejs API v1

> [!Note]
> Interfejsy API V1 indeksatora wideo są teraz przestarzałe i zostanie usunięte w dniu 1 sierpnia 2018. Należy rozpocząć przy użyciu interfejsów API w wersji 2 indeksatora wideo, aby uniknąć zakłóceń.
>
> Aby opracować z interfejsami API v2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

Podczas wywoływania **uzyskać podziały** interfejsu API i stan odpowiedzi jest prawidłowy, uzyskać szczegółowe dane wyjściowe JSON jako treść odpowiedzi. Zawartość JSON zawiera szczegółowe informacje o określonym wideo szczegółowych danych w tym (wykaz, OCRs, osoby). Szczegóły obejmują słowa kluczowe (tematy), kroje, bloki. Każdy blok obejmuje przedziałów czasu, linie wykaz, linie Rozpoznawania, opinie, kroje i blokowanie miniatur.

Można użyć **uzyskać podziały** interfejsu API, aby uzyskać pełny podział wideo jako JSON zawartości.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Można również wizualnie sprawdzić insights podsumowań wideo, naciskając klawisz **odtwarzanie** przycisk wideo w portalu wideo indeksatora. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie wideo insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule sprawdza, czy zawartość JSON zwrócony przez **uzyskać podziały** interfejsu API. Może być przydatne przejrzeć [pojęcia](video-indexer-concepts.md) artykułu.

> [!NOTE]
> Wygaśnięcie wszystkich tokenów dostępu indeksatora wideo to jedna godzina.

## <a name="root-elements"></a>Elementy główne

Atrybut | Opis
---|---
id|Identyfikator ten film. Na przykład 63c6d532ff.
partycja|Partycji logicznej, którą użytkownik może określić w przekazywania w celu wyszukania go później.
name|Nazwa obrazu wideo. Na przykład Azure Monitor.
description|Opis filmu wideo. Na przykład "Jan Kemnetz łączy którym Scott Hanselman pokazują, jak odblokować zasilania Azure danych monitorowania z Monitorem systemu Azure."
userName|Twórca wideo. Na przykład Channel9 wideo.
CreateTime |Godzina utworzenia. Na przykład 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Wideo może mieć jeden z następujących trybów: **prywatnej**, **publicznego**. **Publiczny** -wideo jest widoczny dla wszystkich użytkowników, konta i każda osoba, która zawiera link do wideo. **Prywatne** -wideo jest widoczny dla wszystkich osób w ramach Twojego konta.
isOwned|Wartość true, jeśli bieżący użytkownik jest właścicielem wideo. W przeciwnym razie wartość false.  
isBase|Wartość true, jeśli podział jest oparty na źródła wideo. Wartość false, jeśli podział jest lista, która pochodzi z innego podziału.
durationInSeconds|Czas trwania wideo.
summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedInsights).
podział|Może zawierać jeden lub więcej [awarii](#breakdowns)
Społecznościowych|Zawiera jeden **społecznościowych** element, który określa liczbę pochodzące z widokami wideo.

## <a name="summarizedinsights"></a>summarizedInsights

W tej sekcji przedstawiono podsumowanie szczegółowych danych.

Atrybut | Opis
---|---
name|Nazwa obrazu wideo. Na przykład Azure Monitor.
shortId|Identyfikator wideo. Na przykład 63c6d532ff.
privacyMode|Podział użytkownika może mieć jeden z następujących trybów: **prywatnej**, **publicznego**. **Publiczny** -wideo jest widoczny dla wszystkich użytkowników, konta i każda osoba, która zawiera link do wideo. **Prywatne** -wideo jest widoczny dla wszystkich osób w ramach Twojego konta.
czas trwania|Zawiera jeden czas trwania, który opisuje godzina wystąpienia szczegółowe informacje. Czas trwania jest w sekundach.
thumbnailUrl|Miniatur wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Zwróć uwagę, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
kroje|Może zawierać jeden lub więcej [powierzchni](#faces)
— Tematy|Może zawierać jeden lub więcej [— tematy](#topics)
opinie|Może zawierać jeden lub więcej [opinie](#sentiments)
audioEffects| Może zawierać jeden lub więcej [audioEffects](#audioEffects)
marek| Może zawierać zero lub więcej [marki](#brands)
Statystyki|Aby uzyskać więcej informacji, zobacz [statystyk](#Statistics)
.
### <a name="statistics"></a>Statystyki

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w wideo.|
|WordCount|Liczba słów na prelegenta.|
|SpeakerNumberOfFragments|Ilość fragmenty prelegenta ma w pliku wideo.|
|SpeakerLongestMonolog|Najdłuższy monolog prelegenta. Jeśli prelegenta ma silences wewnątrz monolog jest dołączony. Cisza na początku i końcu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczenia są oparte na czas spędzony na monolog osoby mówiącej (bez wyciszenia między) podzielony przez całkowity czas wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|

## <a name="breakdowns"></a>podział

W tej sekcji przedstawiono szczegóły szczegółowych danych.

Atrybut | Opis
---|---
id|Identyfikator podział. Na przykład 63c6d532ff.
state|Stan przetwarzania identyfikator danego podział. Może być jedną z następujących: przekazano, przetwarzanie, przetworzone, nie powiodło się.
processingProgress|Postęp. Na przykład 10%.
externalId| ExternalId można ustawić podczas przekazywania. Na przykład "4f9c3500-eca7-4ab3-987e-a745017af698." Później można wyszukiwać filmy wideo według tego identyfikatora zewnętrznego.
zewnętrznego adresu URL|Podczas przekazywania można ustawić zewnętrznego adresu URL. 
metadane|Możesz ustawić metadanych podczas przekazywania. 
szczegółowe informacje|Może zawierać jeden lub więcej [insights](#insights)
thumbnailUrl|Miniatur wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Zwróć uwagę, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
publishedUrl|Opublikowany adres URL. Na przykład "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|Token elementu nośnego
sourceLanguage|Języka źródłowego. Obsługiwane są następujące: chiński, angielskim, francuskim, niemieckim, włoski, japoński, portugalski, rosyjski, hiszpański.
Język|Język zapis.

## <a name="insights"></a>szczegółowe informacje

Atrybut | Opis 
---|---
transcriptBlocks|Może zawierać jeden lub więcej [transcriptBlocks](#transcriptBlocks)
— Tematy|Może zawierać jeden lub więcej [— tematy](#topics)
kroje|Może zawierać jeden lub więcej [powierzchni](#faces)
Uczestnicy|Może zawierać jeden lub więcej [uczestników](#participants)
contentModeration|Może zawierać jeden [contentModeration](#contentModeration)
audioEffectsCategories|Może zawierać jeden lub więcej [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>kroje

### <a name="summarizedinsights"></a>summarizedInsights

**kroje** pojawiających się w obszarze **summarizedInsights**, Pokaż podsumowanie każdej powierzchni znaleziono wideo.

Atrybut | Opis 
---|---
id|Identyfikator osoby. Na przykład 11775.
shortId|Krótki identyfikatora. Ponieważ listy odtwarzania mogą wynikać z kilku awarii, ten identyfikator jest potrzebny do sprawdzić, której podział pochodzenia każdej powierzchni.  
name|Jeśli zostanie rozpoznana krój, jest dodawany nazwisko osoby. Na przykład "Scott Hanselman." Jeśli kroju jest nieznany, "# Nieznany" zostanie dodany. 
description|Jeśli zostanie rozpoznana krój, opis zostanie wypełniona oparte na wyszukiwania usługi Bing interfejsu API. W przeciwnym razie jest opis **null**.
tytuł|Jeśli zostanie rozpoznana krój, opis zostanie wypełniona oparte na wyszukiwania usługi Bing interfejsu API. W przeciwnym razie jest tytuł **null**.
thumbnailFullUrl|Miniatury krój pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Zwróć uwagę, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
wygląd|Może zawierać jeden lub więcej [wyglądu](#appearances)
seenDuration|Dla jak długo powierzchni było widoczne (w sekundach).
seenDurationRatio|Obecność względem wideo czas trwania (0-1).

### <a name="breakdown-insights"></a>podział insights

**kroje** pojawiających się w obszarze **podziały**, opisano szczegółowe informacje o każdej powierzchni znaleziono wideo.

Atrybut | Opis 
---|---
id|Identyfikator osoby. Na przykład 11775.
bingId|
name|Jeśli zostanie rozpoznana krój, jest dodawany nazwisko osoby. Na przykład "Scott Hanselman". Jeśli kroju jest nieznany, "# Nieznany" zostanie dodany. 
thumbnailId|Na przykład 616468f0-1636-4efa-94e7-262f2e575059.
description|Jeśli zostanie rozpoznana krój, opis zostanie wypełniona oparte na wyszukiwania usługi Bing interfejsu API. W przeciwnym razie jest opis **null**.
tytuł|Jeśli zostanie rozpoznana krój, opis zostanie wypełniona oparte na wyszukiwania usługi Bing interfejsu API. W przeciwnym razie jest tytuł **null**.
ImageUrl|Ten adres URL wskazuje obrazu, który pochodzi ze źródła wideo.  
Zaufania|Wynik zaufania (wyższy jest lepszym rozwiązaniem).
knownPersonId|Identyfikator osoby znane (na przykład renomy). Jeśli osoba nie jest znany, identyfikator zawiera zera. Na przykład e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>— Tematy

### <a name="summarizedinsights"></a>summarizedInsights

**tematy** pojawiających się w obszarze **summarizedInsights**, Pokaż podsumowanie znaleziono wideo każdego tematu.

Atrybut | Opis 
---|---
name|Nazwa tematu (na przykład "Azure"). 
wygląd|Może zawierać jeden lub więcej [wygląd](#appearances).
isTranscript|Wartość true, jeśli znaleziono w wykazie. Wartość false, jeśli znaleziono w Rozpoznawania.

### <a name="breakdown-insights"></a>podział insights

**tematy** pojawiających się w obszarze **podziały**, opisano szczegóły dotyczące każdego tematu w wideo.

|Atrybut | Opis |
|---|---|
|id|Identyfikator unikatowy tematu.|
|name|Nazwa tematu.|
|Trzon|Ten atrybut nie jest obecnie używany.|
|słowa|Ten atrybut nie jest obecnie używany.|
|Ranga|Oceny przydatności (wyższy jest lepszym rozwiązaniem).|

## <a name="sentiments"></a>opinie

Atrybut | Opis
---|---
sentimentKey| Obecnie są obsługiwane następujące opinie: dodatnią, neutralnego, ujemne. 
wygląd|Może zawierać jeden lub więcej [wyglądu](#appearances)|.
seenDurationRatio|Obecność względem wideo czas trwania (0-1).

## <a name="audioeffects"></a>audioEffects

Atrybut | Opis 
---|---
audioEffectKey| Prawidłowe wartości to: HandClaps mowy, wyciszenia,.
wygląd|Może zawierać jeden lub więcej [wyglądu](#appearances)
seenDurationRatio|Obecność względem wideo czas trwania (0-1).
seenDuration|Jak długo audio efekt była obecna (w sekundach).

## <a name="appearances"></a>wygląd

Atrybut | Opis 
---|---
startTime| Wartość godziny.
endTime|Wartość godziny.
startSeconds| Wartość godziny.
endSeconds| Wartość godziny.

## <a name="participants"></a>Uczestnicy

Atrybut | Opis 
---|---
id|Identyfikator uczestnika.
name|Nazwa tego uczestnika. Na przykład osoby mówiącej nr 1.
adres URL zdjęcia|**Adres URL zdjęcia** atrybutu jest zarezerwowany do użytku w przyszłości.

## <a name="contentmoderation"></a>contentModeration

Atrybut | Opis 
---|---
adultClassifierValue|Poziom pewności, że plik wideo ma zawartość dla dorosłych.
racyClassifierValue|Poziom pewności, że plik wideo ma luksusowych zawartość.
bannedWordsCount|Liczba wyrazów niestosownych wyrażeń. 
bannedWordsRatio|Stosunek wyrazów niestosownych wyrażeń z całkowitą liczbę słów.
reviewRecommended|Wartość logiczna określająca, czy wideo należy ręcznie sprawdzić.
isAdult|Wartości logiczna wskazująca, jeśli wideo jest traktowany jako osoba dorosła wideo po ręczne przeglądu.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Atrybut | Opis 
---|---
type|Identyfikator kategorii.
key|Jedną z następujących: HandClaps mowy, wyciszenia,. 

## <a name="transcriptblocks"></a>transcriptBlocks

Atrybut | Opis
---|---
id|Identyfikator bloku.
wiersze|Może zawierać jeden lub więcej [wierszy](#lines)
sentimentIds|**SentimentIds** atrybutu jest zarezerwowany do użytku w przyszłości.
thumbnailIds|**ThumbnailIds** atrybutu jest zarezerwowany do użytku w przyszłości.
Wskaźniki nastrojów klientów|Wskaźniki nastrojów klientów w bloku (0-1, ujemna dodatnią).
kroje|Może zawierać jeden lub więcej [kroje](#faces).
ocrs|Może zawierać jeden lub więcej [ocrs](#ocrs).
audioEffectInstances|Może zawierać jeden lub więcej [audioEffectInstances](#audioEffectInstances).
sceny|Może zawierać jeden lub więcej [sceny](#scenes).
Adnotacje|Może zawierać zero lub więcej [adnotacje](#annotations).

## <a name="ocrs"></a>ocrs

Opisuje, w momencie co wideo, znaleziono zawartości tekstowej. 

Atrybut | Opis 
---|---
timeRange|Zakres czasu w oryginalnym wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania z różnych wierszy różnych plików wideo, można wykonać jedną godzinę wideo i korzystać tylko 1 wiersz z niego, na przykład 10:00 – 10:15. W takim przypadku należy listy odtwarzania o 1 wiersz, gdy przedział czasu jest 15-10:10:00, ale adjustedTimeRange to 00:00-00:15.
wiersze|Może zawierać jeden lub więcej [wierszy](#lines).

## <a name="lines"></a>wiersze

### <a name="transcriptblocks"></a>transcriptBlocks

**wiersze** pojawiających się w obszarze **transcriptBlocks**, opisano wierszy zapisy w wideo.

Atrybut | Opis 
---|---
id| Identyfikator wiersza.
timeRange|Zakres czasu w oryginalnym wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania z różnych wierszy różnych plików wideo, można wykonać jedną godzinę wideo i korzystać tylko 1 wiersz z niego, na przykład 10:00 – 10:15. W takim przypadku należy listy odtwarzania o 1 wiersz, gdy przedział czasu jest 15-10:10:00, ale adjustedTimeRange to 00:00-00:15.
participantID| Identyfikator osoby mówiącej tego wiersza.
tekst| Zapis.
isIncluded| W przypadku awarii podstawowego zawsze wartość true. Pochodne listy odtwarzania, wiersze, które zostały uwzględnione w źródle wideo, są ustawione na isIncluded = true. Wszystkie pozostałe wiersze są wartość false.

### <a name="ocrs"></a>ocrs

**wiersze** pojawiających się w obszarze **ocrs**, opisano wierszy OCRs znaleziono wideo.

Atrybut | Opis 
---|---
id|Identyfikator Rozpoznawania.
Szerokość|Ten atrybut nie jest obecnie używany.
Wysokość|Ten atrybut nie jest obecnie używany.
Język|Język Rozpoznawania.
TextData|Tekst Rozpoznawania.
Zaufania|Wynik zaufania (wyższy jest lepszym rozwiązaniem).

## <a name="scenes"></a>sceny

Atrybut | Opis 
---|---
id|Identyfikator sceny.
timeRange|Zawiera jeden **timeRange**.
klatki kluczowej|Czas ramki klucza.
zrzuty|Może zawierać jeden lub więcej [zrzuty](#shots).

## <a name="shots"></a>zrzuty

Atrybut | Opis 
---|---
id||Zrzut identyfikatora.
timeRange|Zawiera jeden **timeRange**.
klatki kluczowej|Czas ramki klucza.

## <a name="audioeffectinstances"></a>audioEffectInstances

Atrybut | Opis 
---|---
type|Indeks audio zdarzeń: śmiech = 1, HandClaps = 2, muzyka = 3, mowy = 4, wyciszenia = 5
zakresy|Może zawierać jeden lub więcej [zakresy](#ranges).

## <a name="ranges"></a>zakresy

**zakresy** pojawiających się w obszarze **audioEffectInstances**, opisano efekty audio w tych zakresów.

Atrybut | Opis 
---|---
timeRange|Zakres czasu w oryginalnym wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania z różnych wierszy różnych plików wideo, można wykonać jedną godzinę wideo i korzystać tylko jeden wiersz z niego, na przykład 10:00 – 10:15. W takim przypadku należy listy odtwarzania o 1 wiersz, gdy przedział czasu jest 15-10:10:00, ale adjustedTimeRange to 00:00-00:15.

## <a name="annotations"></a>Adnotacje

Zwraca tagi na podstawie rozpoznawalnych obiektów, istot życia, dekoracje, akcje i wzorce visual.

|Atrybut|Opis|
|---|---|
|id|Identyfikator adnotacji.|
|Name (Nazwa)|Nazwa adnotacji (na przykład osoba, Athletic grę, czarne ramki).|
|Wygląd|Może zawierać jeden lub więcej wystąpień.|

## <a name="brands"></a>marek

Działalności biznesowej i produktu firmowe wykryte w wykaz tekstu i/lub wideo Rozpoznawania mowy. Nie ma visual rozpoznawania marek lub wykrywania logo.

Atrybut | Opis
---|---
id | Identyfikator marki.
name | Nazwa dostosowanego marki lub marki z usługi Bing.  
wikiId | Sufiks adresu URL wikipedia marki. Na przykład "Target_Corporation" jest to sufiks [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Produkt firmy Wikipedia adres url, jeśli istnieje. Na przykład [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
Zaufania | Wartość zaufania detektora brand indeksatora wideo (0-1).
description | Opis elementu marki wyodrębniony z Wikipedia. 
wygląd | Może zawierać jeden lub więcej wystąpień.
seenDuration | Obecność względem wideo czas trwania (0-1).

## <a name="next-steps"></a>Kolejne kroki

Informacje o sposobie tworzenia własnych podziału, zobacz [widoku i edytować indeksator wideo insights](video-indexer-view-edit.md).

Aby dowiedzieć się, jak osadzić elementy widget w aplikacji, zobacz [elementy widget indeksatora osadzania wideo do aplikacji](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Zobacz także

[Indeksator wideo interfejsu API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Film poglądowy dotyczący indeksatora](video-indexer-overview.md)

