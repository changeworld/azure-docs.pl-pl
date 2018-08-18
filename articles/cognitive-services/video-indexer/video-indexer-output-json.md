---
title: Sprawdź dane wyjściowe usługi Azure Video Indexer | Dokumentacja firmy Microsoft
description: W tym temacie analizuje danych wyjściowych indeksatora wideo.
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
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 1

> [!Note]
> Interfejsy API wersji 1 dla indeksatora wideo są one przestarzałe i zostanie usunięte w dniu 1 sierpnia 2018. Należy rozpocząć przy użyciu interfejsów API w wersji 2 indeksatora wideo, aby uniknąć przerw w działaniu.
>
> Tworzenie za pomocą interfejsów API w wersji 2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

Gdy wywołujesz **uzyskać podziałów** interfejsu API i stan odpowiedzi jest OK, Uzyskaj szczegółowe dane wyjściowe JSON jako treść odpowiedzi. Zawartość JSON zawiera szczegółowe informacje o określonym analiz wideo w tym (transkrypcji, OCRs, osób). Szczegółowe informacje zawierają słowa kluczowe (tematy), twarzy, bloki. Każdy blok zawiera zakresy czasu transkrypcji wierszy, wiersze optyczne rozpoznawanie znaków, tonacji, twarzy i blokowanie miniatury.

Możesz użyć **uzyskać podziałów** interfejsu API do uzyskania pełnego podział filmu wideo jako JSON zawartości.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Można również wizualnie sprawdzić insights podsumowań wideo, naciskając klawisz **Odtwórz** przycisk film wideo, w portalu usługi Video Indexer. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie informacji szczegółowych wideo](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule sprawdza, czy zawartość JSON zwróconą przez **uzyskać podziałów** interfejsu API. Może być przydatne w przypadku przeglądu [pojęcia](video-indexer-concepts.md) artykułu.

> [!NOTE]
> Czas wygaśnięcia tokenów dostępu w Video Indexer jest jedną godzinę.

## <a name="root-elements"></a>Elementy główne

Atrybut | Opis
---|---
id|Identyfikator ten film wideo. Na przykład 63c6d532ff.
partycja|Partycji logicznej, który użytkownik może określić w przekazywania, aby wyszukać ją później.
name|Nazwa filmu wideo. Na przykład usługa Azure Monitor.
description|Opis filmu wideo. Na przykład "John Kemnetz łączy Scott Hanselman, aby zaprezentować sposób sfinalizowana danych monitorowania platformy Azure z usługą Azure Monitor."
userName|Twórca filmu wideo. Na przykład witryny Channel9 wideo.
CreateTime |Godzina utworzenia. Na przykład 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Film wideo może mieć jedną z następujących trybów: **prywatnej**, **publicznych**. **Publiczne** — film wideo jest widoczny dla wszystkich użytkowników w swoje konto i każda osoba, która zawiera link do filmu wideo. **Prywatne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie.
isOwned|Wartość true, jeśli bieżący użytkownik jest właścicielem filmu wideo. W przeciwnym razie wartość false.  
isBase|Wartość true, jeśli podział jest na podstawie źródła wideo. Wartość false, jeśli jest podział listy odtwarzania, które pochodzą z innego podział.
durationInSeconds|Czas trwania klipu wideo.
summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedInsights).
podział|Może zawierać jeden lub więcej [podziałów](#breakdowns)
Społecznościowych|Zawiera jeden **społecznościowych** element, który opisuje liczbę polubień i widoki filmu wideo.

## <a name="summarizedinsights"></a>summarizedInsights

W tej sekcji przedstawiono podsumowanie szczegółowych danych.

Atrybut | Opis
---|---
name|Nazwa filmu wideo. Na przykład usługa Azure Monitor.
shortId|Identyfikator filmu wideo. Na przykład 63c6d532ff.
privacyMode|Podział usługi może mieć jedną z następujących trybów: **prywatnej**, **publicznych**. **Publiczne** — film wideo jest widoczny dla wszystkich użytkowników w swoje konto i każda osoba, która zawiera link do filmu wideo. **Prywatne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie.
czas trwania|Zawiera jeden czas trwania, opisujący godzina wystąpienia w szczegółowe informacje. Czas trwania to w ciągu kilku sekund.
thumbnailUrl|Miniatura wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Należy zauważyć, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
twarzy|Może zawierać jeden lub więcej [twarzy](#faces)
Tematy|Może zawierać jeden lub więcej [— tematy](#topics)
opinie|Może zawierać jeden lub więcej [tonacji](#sentiments)
audioEffects| Może zawierać jeden lub więcej [audioEffects](#audioEffects)
marek| Może zawierać zero lub więcej [marki](#brands)
Statystyki|Aby uzyskać więcej informacji, zobacz [statystyk](#Statistics)
.
### <a name="statistics"></a>Statystyki

|Name (Nazwa)|Opis|
|---|---|
|CorrespondenceCount|Liczba korelacji w trakcie filmu wideo.|
|WordCount|Liczba słów na osoby mówiącej.|
|SpeakerNumberOfFragments|Ilość fragmenty osoby mówiącej ma w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższy produktu monolog osoby mówiącej. Jeśli osoby mówiącej silences wewnątrz produktu monolog jest dołączony. Wyciszenia na początku i końcu produktu monolog zostaną usunięte.| 
|SpeakerTalkToListenRatio|Obliczanie opiera się na czas spędzony na produktu monolog osoby mówiącej (bez wyciszenia pomiędzy) podzielony przez łącznego czasu filmu wideo. Czas jest zaokrąglana do trzeciego punktu dziesiętnego.|

## <a name="breakdowns"></a>podział

W tej sekcji przedstawiono szczegółowe informacje o szczegółowe informacje.

Atrybut | Opis
---|---
id|Identyfikator podział. Na przykład 63c6d532ff.
state|Stan przetwarzania identyfikator danej podział. Może być jedną z następujących: przekazano, przetwarzania, przetwarzania, nie powiodło się.
processingProgress|Postęp. Na przykład 10%.
externalId| ExternalId można ustawić podczas przekazywania. Na przykład "4f9c3500-eca7-4ab3-987e-a745017af698." Można będzie później wyszukiwać dla filmów wideo według tego identyfikatora zewnętrznego.
externalUrl|Możesz ustawić externalUrl podczas przekazywania. 
metadane|Podczas przekazywania, można ustawić metadanych. 
szczegółowe informacje|Może zawierać jeden lub więcej [insights](#insights)
thumbnailUrl|Miniatura wideo pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Należy zauważyć, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
publishedUrl|Adres URL opublikowany. Na przykład "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|Token elementu nośnego
sourceLanguage|Język źródłowy. Obsługiwane są następujące: chińskim, angielskiej, francuska, niemiecki, włoski, japoński, portugalski, rosyjski, hiszpański.
Język|Język tej transkrypcji.

## <a name="insights"></a>szczegółowe informacje

Atrybut | Opis 
---|---
transcriptBlocks|Może zawierać jeden lub więcej [transcriptBlocks](#transcriptBlocks)
Tematy|Może zawierać jeden lub więcej [— tematy](#topics)
twarzy|Może zawierać jeden lub więcej [twarzy](#faces)
Uczestnicy|Może zawierać jeden lub więcej [uczestników](#participants)
contentModeration|Może zawierać jeden [contentModeration](#contentModeration)
audioEffectsCategories|Może zawierać jeden lub więcej [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>twarzy

### <a name="summarizedinsights"></a>summarizedInsights

**twarze** wyświetlanych w obszarze **summarizedInsights**, Pokaż podsumowanie każdej twarzy w filmie wideo.

Atrybut | Opis 
---|---
id|Identyfikator osoby. Na przykład 11775.
shortId|Krótki identyfikator. Ponieważ listy odtwarzania może pochodzić od kilku podziałów, ten identyfikator jest potrzebny do dowiedzieć się, której podział źródła każdej twarzy.  
name|Jeśli zostanie rozpoznany twarz, jest dodawany nazwisko osoby. Na przykład "Scott Hanselman." Jeśli twarz jest nieznany, zostanie dodany "Nieznany #". 
description|Jeśli twarz zostanie rozpoznany, opis jest wypełniana na podstawie wyszukiwania interfejsu API Bing. W przeciwnym razie jest opis **null**.
tytuł|Jeśli twarz zostanie rozpoznany, opis jest wypełniana na podstawie wyszukiwania interfejsu API Bing. W przeciwnym razie jest tytuł **null**.
thumbnailFullUrl|Face Miniatura pełny adres URL. Na przykład "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Należy zauważyć, że jeśli wideo jest prywatny, adres URL zawiera token dostępu jedną godzinę. Po upływie godziny, adres URL nie będzie już prawidłowy i należy uzyskać podział ponownie, podając nowy adres url lub wywołać GetAccessToken uzyskać nowy token dostępu i ręcznie utworzyć pełny adres url ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken] ").
wygląd|Może zawierać jeden lub więcej [wygląd](#appearances)
seenDuration|Jak długo twarzy występowało (w sekundach).
seenDurationRatio|Obecność względem czas trwania wideo (0 – 1).

### <a name="breakdown-insights"></a>podział szczegółowych informacji

**twarze** wyświetlanych w obszarze **podziałów**, opisz szczegóły każdej twarzy w filmie wideo.

Atrybut | Opis 
---|---
id|Identyfikator osoby. Na przykład 11775.
bingId|
name|Jeśli zostanie rozpoznany twarz, jest dodawany nazwisko osoby. Na przykład "Scott Hanselman". Jeśli twarz jest nieznany, zostanie dodany "Nieznany #". 
thumbnailId|Na przykład 616468f0-1636-4efa-94e7-262f2e575059.
description|Jeśli twarz zostanie rozpoznany, opis jest wypełniana na podstawie wyszukiwania interfejsu API Bing. W przeciwnym razie jest opis **null**.
tytuł|Jeśli twarz zostanie rozpoznany, opis jest wypełniana na podstawie wyszukiwania interfejsu API Bing. W przeciwnym razie jest tytuł **null**.
imageUrl|Ten adres URL wskazuje obraz, który jest pobierany ze źródła wideo.  
zaufania|Współczynnik ufności (większa jest lepiej).
knownPersonId|Identyfikator znanych osób (na przykład, osobistości). Jeśli osoba nie jest znany, identyfikator zawiera zera. Na przykład e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>Tematy

### <a name="summarizedinsights"></a>summarizedInsights

**Tematy dotyczące** wyświetlanych w obszarze **summarizedInsights**, Pokaż podsumowanie każdego tematu znalezione w trakcie filmu wideo.

Atrybut | Opis 
---|---
name|Nazwa tematu (np. "Azure"). 
wygląd|Może zawierać jeden lub więcej [wygląd](#appearances).
isTranscript|Wartość true, jeśli znaleziono w wykazie. FAŁSZ, jeśli znaleziono w optyczne rozpoznawanie znaków.

### <a name="breakdown-insights"></a>podział szczegółowych informacji

**Tematy dotyczące** wyświetlanych w obszarze **podziałów**, opisz szczegóły każdego tematu znalezione w trakcie filmu wideo.

|Atrybut | Opis |
|---|---|
|id|Identyfikator unikatowy tematu.|
|name|Nazwa tematu.|
|stem|Ten atrybut nie jest obecnie używany.|
|wyrazy|Ten atrybut nie jest obecnie używany.|
|Ranga|Istotności (większa jest lepiej).|

## <a name="sentiments"></a>opinie

Atrybut | Opis
---|---
sentimentKey| Obecnie obsługiwane są następujące tonacji: dodatnią, neutralne i ujemne. 
wygląd|Może zawierać jeden lub więcej [wygląd](#appearances)|.
seenDurationRatio|Obecność względem czas trwania wideo (0 – 1).

## <a name="audioeffects"></a>audioEffects

Atrybut | Opis 
---|---
audioEffectKey| Prawidłowe wartości to: HandClaps mowy, wyciszenia,.
wygląd|Może zawierać jeden lub więcej [wygląd](#appearances)
seenDurationRatio|Obecność względem czas trwania wideo (0 – 1).
seenDuration|Jak długo audio efekt był obecny (w sekundach).

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
name|Nazwa uczestnika. Na przykład osoby mówiącej nr 1.
adres URL zdjęcia|**Adres URL zdjęcia** atrybutu jest zarezerwowana do użytku w przyszłości.

## <a name="contentmoderation"></a>contentModeration

Atrybut | Opis 
---|---
adultClassifierValue|Poziom ufności, że plik wideo ma treści dla dorosłych.
racyClassifierValue|Poziom ufności, że plik wideo ma zawartości erotycznej.
bannedWordsCount|Liczba wyrazów wulgaryzmów. 
bannedWordsRatio|Stosunek wulgaryzmów słów od całkowitej liczby słów.
reviewRecommended|Wartość logiczna wskazująca, jeśli wideo należy ręcznie przejrzeć.
isAdult|Wartości logiczna wskazująca, jeśli wideo jest określana jako osoba dorosła wideo po ręcznego przeglądu.

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
sentimentIds|**SentimentIds** atrybutu jest zarezerwowana do użytku w przyszłości.
thumbnailIds|**ThumbnailIds** atrybutu jest zarezerwowana do użytku w przyszłości.
wskaźniki nastrojów klientów|Wskaźniki nastrojów klientów w bloku (0 – 1, dodatnia lub ujemna).
twarzy|Może zawierać jeden lub więcej [twarzy](#faces).
ocrs|Może zawierać jeden lub więcej [ocrs](#ocrs).
audioEffectInstances|Może zawierać jeden lub więcej [audioEffectInstances](#audioEffectInstances).
sceny|Może zawierać jeden lub więcej [sceny](#scenes).
Adnotacje|Może zawierać zero lub więcej [adnotacje](#annotations).

## <a name="ocrs"></a>ocrs

Opisuje, w jakim punkcie w filmie wideo odnaleziono zawartości tekstowej. 

Atrybut | Opis 
---|---
timeRange|Zakres czasu w oryginalnego filmu wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz wykonać jedną godzinę wideo i tylko 1 w wierszu polecenia z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
wiersze|Może zawierać jeden lub więcej [wierszy](#lines).

## <a name="lines"></a>wiersze

### <a name="transcriptblocks"></a>transcriptBlocks

**wiersze** wyświetlanych w obszarze **transcriptBlocks**, opisz wiersze zapisy znalezione w trakcie filmu wideo.

Atrybut | Opis 
---|---
id| Identyfikator wiersza.
timeRange|Zakres czasu w oryginalnego filmu wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz wykonać jedną godzinę wideo i tylko 1 w wierszu polecenia z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
participantID| Identyfikator osoby mówiącej tego wiersza.
tekst| Transkrypcji.
isIncluded| W przypadku awarii podstawowej zawsze true. Pochodne listy odtwarzania, wiersze, które zostały uwzględnione w źródle, wideo, ustawiono isIncluded = true. Wszystkie pozostałe wiersze mają wartość false.

### <a name="ocrs"></a>ocrs

**wiersze** wyświetlanych w obszarze **ocrs**, opisz wiersze OCRs znalezione w trakcie filmu wideo.

Atrybut | Opis 
---|---
id|Identyfikator optyczne rozpoznawanie znaków.
Szerokość|Ten atrybut nie jest obecnie używany.
Wysokość|Ten atrybut nie jest obecnie używany.
Język|Język optyczne rozpoznawanie znaków.
Kolumna TextData|Optyczne rozpoznawanie znaków tekstu.
zaufania|Współczynnik ufności (większa jest lepiej).

## <a name="scenes"></a>sceny

Atrybut | Opis 
---|---
id|Identyfikator sceny.
timeRange|Zawiera jeden **timeRange**.
klatki kluczowej|Czas ramki kluczowe.
zrzuty|Może zawierać jeden lub więcej [zrzuty](#shots).

## <a name="shots"></a>zrzuty

Atrybut | Opis 
---|---
id||Zrzut identyfikatora.
timeRange|Zawiera jeden **timeRange**.
klatki kluczowej|Czas ramki kluczowe.

## <a name="audioeffectinstances"></a>audioEffectInstances

Atrybut | Opis 
---|---
type|Indeks zdarzenia audio: śmiech = 1, HandClaps = 2, muzyka = 3, mowy = 4, wyciszenia = 5
zakresy|Może zawierać jeden lub więcej [zakresów](#ranges).

## <a name="ranges"></a>zakresy

**zakresy** wyświetlanych w obszarze **audioEffectInstances**, opisz efekty dźwiękowe w tych zakresach.

Atrybut | Opis 
---|---
timeRange|Zakres czasu w oryginalnego filmu wideo.
adjustedTimeRange|AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz wykonać jedną godzinę wideo i użyć tylko jeden wiersz z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.

## <a name="annotations"></a>Adnotacje

Zwraca tagi, w oparciu o rozpoznawalnych obiektów, istot żywych, scenerii, akcje i wzorce visual.

|Atrybut|Opis|
|---|---|
|id|Identyfikator adnotacji.|
|Name (Nazwa)|Nazwa adnotacji (na przykład osoba, Athletic grę, Black ramki).|
|Wygląd|Może zawierać jeden lub więcej wystąpień.|

## <a name="brands"></a>marek

Firmy i produkt nazw marek wykryte w zamiana mowy na tekst transkrypcji i/lub optyczne rozpoznawanie znaków w wideo. Nie dotyczy to visual rozpoznawanie marek lub wykrywania logo.

Atrybut | Opis
---|---
id | Identyfikator marki.
name | Nazwa marki z usługi Bing lub dostosowanych marki.  
wikiId | Sufiks adresu url wikipedia marki. Na przykład "Target_Corporation" jest to sufiks [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Dostępne w Wikipedii adresu url, jeśli istnieje. Na przykład [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
zaufania | Wartość ufności wykrywacz marki Video Indexer (0 – 1).
description | Opis marki wyodrębnione z Wikipedia. 
wygląd | Może zawierać jeden lub więcej wystąpień.
seenDuration | Obecność względem czas trwania wideo (0 – 1).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o sposobie tworzenia własnych podział, zobacz [wyświetlanie i edytowanie informacji szczegółowych indeksatora wideo](video-indexer-view-edit.md).

Aby uzyskać informacje o sposobie osadzanie widżetów w aplikacji, zobacz [widżetów osadzić indeksatora wideo we własnych aplikacjach](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Zobacz także

[Interfejsu API indeksatora wideo](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Omówienie indeksatora wideo](video-indexer-overview.md)

