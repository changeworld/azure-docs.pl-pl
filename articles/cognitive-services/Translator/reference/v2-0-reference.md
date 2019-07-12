---
title: Interfejs API tekstu usługi Translator w wersji 2.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja interfejsu API tłumaczenia tekstu w wersji 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797815"
---
# <a name="translator-text-api-v20"></a>Interfejs API tekstu usługi Translator w wersji 2.0

> [!IMPORTANT]
> Ta wersja interfejsu API tłumaczenia tekstu jest przestarzała. [Wyświetl dokumentację w wersji 3 interfejsu API tłumaczenia tekstu](v3-0-reference.md).

Interfejs API tekstu usługi Translator w wersji 2 można bezproblemowo zintegrować swoje aplikacje, witryny sieci Web, narzędzia lub inne rozwiązania, aby zapewnić środowisk użytkowników w wielu językach. Umożliwia ona na dowolnej platformie sprzętu i z dowolnego systemu operacyjnego wykonać tłumaczenie z języka i inne zadania związane z językiem, takich jak wykrywanie języka tekst i zamiany tekstu na mowę, zgodnie ze standardami branżowymi. Aby uzyskać więcej informacji, zobacz [interfejsu API tłumaczenia tekstu](../translator-info-overview.md).

## <a name="getting-started"></a>Wprowadzenie
Aby uzyskać dostęp do interfejsu API tłumaczenia tekstu, należy [konta na platformie Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Authentication 
Wszystkie wywołania interfejsu API tłumaczenia tekstu wymaga klucza subskrypcji dla uwierzytelniania. Interfejs API obsługuje trzy metody uwierzytelniania:

- Token dostępu. Umożliwia tworzenie tokenu dostępu, dzięki czemu żądania POST do usługi uwierzytelniania klucz subskrypcji, do którego odwołuje się krok 9. Zobacz dokumentację usługi tokenu, aby uzyskać szczegółowe informacje. Przekaż token dostępu w usłudze Translator przy użyciu `Authorization` nagłówek lub `access_token` parametr zapytania. Token dostępu jest ważny przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i Zachowaj przy użyciu tego samego dostępu tokenu dla powtarzanych żądań w ciągu 10 minut.
- Klucz subskrypcji używany bezpośrednio. Przekaż swój klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` dołączany do żądania interfejsu API tłumaczenia tekstu nagłówka. Gdy używasz bezpośrednio klucz subskrypcji, nie trzeba wywołać usługę uwierzytelnianie przy użyciu tokenów do utworzenia tokenu dostępu.
- [Subskrypcji wielu usług Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ta metoda umożliwia przy użyciu jednego klucza tajnego do uwierzytelniania żądań do wielu usług.
Korzystając z wieloma usługami klucz tajny, trzeba uwzględnić dwa nagłówki uwierzytelniania z żądaniem. Pierwszy nagłówek przekazuje klucz tajny. Drugi nagłówek określa regionu skojarzonego z Twoją subskrypcją:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Region jest wymagane dla wielu usług subskrypcji interfejsu API tłumaczenia tekstu. Wybrany region to jedyny region, który służy do tłumaczenia tekstu, gdy używasz klucz subskrypcji wielu usług. Musi to być tym samym regionie, wybrane podczas tworzenia konta dla subskrypcji wielu usług w witrynie Azure portal.

Dostępne regiony to `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, i `westus2`.

Twoje klucz subskrypcji i dostęp do tokenu wpisy tajne, które powinny być ukryte w widoku.

## <a name="profanity-handling"></a>Obsługa niestosownych wyrażeń
Zazwyczaj usługa Translator zachowa wulgaryzmów, który znajduje się w źródle. Stopień wulgaryzmów i kontekst, który sprawia, że wyrazy obsceniczne różnią się zgodnie z kulturą. Dlatego stopień wulgaryzmów w języku docelowym można zwiększenia lub zmniejszenia.

Jeśli chcesz zapobiec wulgaryzmów w tłumaczenia, nawet wtedy, gdy znajduje się w tekście źródłowym, można użyć wulgaryzmów opcję dla metod, które go obsługują filtrowania. Opcję można wybrać, czy mają być wyświetlane wulgaryzmów usunięta lub oznaczona za pomocą odpowiednich tagów lub tego, czy chcesz zezwolić wulgaryzmów w elemencie docelowym. Akceptowane wartości `ProfanityAction` są `NoAction` (ustawienie domyślne), `Marked`, i `Deleted`.


|ProfanityAction    |Action |Przykład źródłowej (japoński)  |Przykład tłumaczenie (język angielski)  |
|:--|:--|:--|:--|
|NoAction   |Domyślne. Taka sama jak nie Ustawianie opcji. Wulgaryzmów zostaną spełnione ze źródła do docelowego.        |彼はジャッカスです。     |Jest on jackass.   |
|Oznaczone jako     |Wyrazy obsceniczne jest otoczony tagi XML \<wulgaryzmów > i \</profanity >.       |彼はジャッカスです。 |Jest on \<wulgaryzmów > jackass\</profanity >.  |
|Usunięte    |Wyrazy obsceniczne zostaną usunięte z danych wyjściowych bez zastępowania.     |彼はジャッカスです。 |Jest on.   |

    
## <a name="excluding-content-from-translation"></a>Wykluczanie zawartości tłumaczenia
Podczas translacji zawartości przy użyciu tagów, takich jak HTML (`contentType=text/html`), można też wykluczyć określonej zawartości z tłumaczenia. Można użyć atrybutu `class=notranslate` do określania zawartości, które powinny pozostać w jego oryginalnym języku. W poniższym przykładzie zawartości w pierwszym `div` element nie będzie tłumaczonego, ale zawartość w drugim `div` element, który zostanie zamienione.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET / tłumaczenie

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Tłumaczy tekst z jednego języka do innego.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Wartość zwracana:** Ciąg, który reprezentuje przetłumaczonego tekstu.

Jeśli wcześniej używano `AddTranslation` lub `AddTranslationArray` wprowadzenia tłumaczenia z oceną 5 lub nowszej, aby jednym zdaniu źródła `Translate` zwraca tylko wybierany często przez dostępnej w systemie. "W jednym zdaniu źródło" oznacza, że dokładnie tych samych (100% dopasowywania), z wyjątkiem wielkość liter, biały znak, wartości tagów i znaków interpunkcyjnych na końcu zdania. Jeśli ocena nie są przechowywane z ocena, 5 lub nowszym, zwracany wynik będzie automatycznego tłumaczenia przez Microsoft Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis    |Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid  |(pusty)    |Wymagana. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|text|(pusty)   |Wymagany. Ciąg, który reprezentuje tekstu do przetłumaczenia. Tekst nie może zawierać więcej niż 10 000 znaków.|query|ciąg|
|from|(pusty)   |Opcjonalny. Ciąg, który reprezentuje kod języka tekstu podlega translacji. Na przykład en w języku angielskim.|query|ciąg|
|na|(pusty) |Wymagana. Ciąg, który reprezentuje kod języka umożliwia tłumaczenie tekstu w.|query|ciąg|
|Typ zawartości|(pusty)    |Opcjonalny. Format tekst tłumaczony. Obsługiwane formaty to `text/plain` (ustawienie domyślne) i `text/html`. Żadne elementy HTML muszą być poprawnie sformułowany i ukończone elementy.|query|ciąg|
|category|(pusty)   |Opcjonalna. Ciąg, który zawiera kategoria tłumaczenia (domena). Wartość domyślna to `general`.|query|ciąg|
|Authorization|(pusty)  |Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|


### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera tłumaczenia dla wielu teksty źródła.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Oto format treści żądania:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Te elementy znajdują się w `TranslateArrayRequest`:


* `AppId`: Wymagana. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `AppId` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.
* `From`: Opcjonalna. Ciąg, który reprezentuje kod języka tekstu podlega translacji. Jeśli to pole pozostanie puste, odpowiedź będzie zawierać wynik automatyczne wykrywanie języka.
* `Options`: Opcjonalny. `Options` Obiekt, który zawiera następujące wartości. Są one wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.
    - `Category`: Ciąg, który zawiera kategoria tłumaczenia (domena). Wartość domyślna to `general`.
    - `ContentType`: Format tekst tłumaczony. Obsługiwane formaty to `text/plain` (ustawienie domyślne), `text/xml`, i `text/html`. Żadne elementy HTML muszą być poprawnie sformułowany i ukończone elementy.
    - `ProfanityAction`: Określa sposób obsługi profanities opisany wcześniej. Akceptowane wartości to `NoAction` (ustawienie domyślne), `Marked`, i `Deleted`.
    - `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Tej samej zawartości zostaną zwrócone w odpowiedzi.
    - `Uri`: Filtruj wyniki według tego identyfikatora URI. Wartość domyślna: `all`.
    - `User`: Filtrowanie wyników przez tego użytkownika. Wartość domyślna: `all`.
* `Texts`: Wymagana. Tablica, która zawiera tekstu do przetłumaczenia. Wszystkie ciągi musi być w tym samym języku. Suma cały tekst do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 2000.
* `To`: Wymagana. Ciąg, który reprezentuje kod języka umożliwia tłumaczenie tekstu w.

Można pominąć elementy opcjonalne. Elementy, które są bezpośrednie elementy podrzędne `TranslateArrayRequest` muszą być wymienione w kolejności alfabetycznej.

`TranslateArray` Metoda przyjmuje `application/xml` lub `text/xml` dla `Content-Type`.

**Wartość zwracana:** A `TranslateArrayResponse` tablicy. Każdy `TranslateArrayResponse` ma następujące elementy:

* `Error`: Wskazuje na błąd, jeśli wystąpi jedno. W przeciwnym razie ustawionej na wartość null.
* `OriginalSentenceLengths`: Tablica liczb całkowitych określa długość każdego zdania w tekście źródłowym. Długość tablicy wskazuje liczbę zdań.
* `TranslatedText`: Przetłumaczony tekst.
* `TranslatedSentenceLengths`: Tablica liczb całkowitych określa długość każde zdanie w przetłumaczonego tekstu. Długość tablicy wskazuje liczbę zdań.
* `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Zwraca taką samą zawartość, jako żądania.

Oto format treści odpowiedzi:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Odpowiedź oznaczająca Powodzenie zawiera tablicę `TranslateArrayResponse` tablic w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Authorization|(pusty)  |Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP   |Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie. Typowe błędy: <ul><li>Element tablicy nie może być pusta.</li><li>Nieprawidłowej kategorii.</li><li>Z języka jest nieprawidłowa.</li><li>Język jest nieprawidłowy.</li><li>Żądanie zawiera zbyt wiele elementów.</li><li>Język From nie jest obsługiwany.</li><li>Język To nie jest obsługiwany.</li><li>Przetłumacz żądania zawiera zbyt wiele danych.</li><li>HTML nie jest w poprawnym formacie.</li><li>Przetłumacz żądania przekazano zbyt wiele parametrów.</li></ul>|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames WPIS

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera przyjazne nazwy dla języków przekazany jako parametr `languageCodes`i zlokalizowane wyszukiwanie do przekazanych `locale` języka.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Treść żądania zawiera tablicę ciągów, która reprezentuje kodów ISO języka 639-1, dla którego mają zostać pobrane przyjazne nazwy. Oto przykład:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Wartość zwracana:** Tablica ciągu, który zawiera nazwy języków obsługiwanych przez usługi Translator zlokalizowany w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągu, który zawiera nazwy języki obsługiwane przez usługę w usłudze Translator zlokalizowany w żądanym języku.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|Ustawienia regionalne|(pusty) |Wymagane. Ciąg, który reprezentuje jedną z następujących pozycji, użyte w celu zlokalizowania nazwy języka: <ul><li>Kombinacja ISO 639 kultury małe dwuliterowych kodu skojarzone z językiem i ISO 3166 przeszczepiania wielkie dwuliterowych kodu. <li>ISO 639 małych liter, kultura kod samodzielnie.|query|ciąg|
|Authorization|(pusty)  |Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera listę kodów języków, reprezentujących języki obsługiwane przez usługi tłumaczenia.  `Translate` i `TranslateArray` może dokonywać translacji między dwoma z tych języków.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Wartość zwracana:** Tablica ciągu, który zawiera kodów języków obsługiwanych przez usługę w usłudze Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągu, który zawiera kodów języków obsługiwanych przez usługę w usłudze Translator.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|Authorization|(pusty)  |Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera języki dostępne dla synteza mowy.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Wartość zwracana:** Tablica ciągu, który zawiera kodów języków, obsługę synteza mowy w usłudze Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągu, który zawiera kodów języków, obsługę synteza mowy w usłudze Translator.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|
 
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401|Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-speak"></a>GET / mowy

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Zwraca WAV lub MP3 strumienia przekazanego tekstu mówionego w żądanym języku.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Wartość zwracana:** Strumień WAV lub MP3 przekazanego tekstu mówionego w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

binary

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|text|(pusty)   |Wymagana. Ciąg, który zawiera co najmniej jeden zdania wymawiane dla strumienia, w wybranym języku. Tekst nie może przekraczać 2000 znaków.|query|ciąg|
|język|(pusty)   |Wymagany. Ciąg, który reprezentuje kod obsługiwanego języka, języka, w której ma zostać mowy na tekst. Kod musi być jeden z kodów zwracany przez metodę `GetLanguagesForSpeak`.|query|ciąg|
|format|(pusty)|Opcjonalny. Ciąg określający identyfikator typu zawartości. Obecnie `audio/wav` i `audio/mp3` są dostępne. Wartość domyślna to `audio/wav`.|query|ciąg|
|options|(pusty)    |Opcjonalna. Ciąg, który określa właściwości syntezatora mowy:<ul><li>`MaxQuality` i `MinSize` określić jakość sygnału dźwiękowego. `MaxQuality` zapewnia najwyższej jakości. `MinSize` udostępnia najmniejszego możliwego rozmiaru pliku. Wartość domyślna to `MinSize`.</li><li>`female` i `male` Określ żądany płeć głosu. Wartość domyślna to `female`. Użyj pionowy pasek (<code>\|</code>) obejmujący wiele opcji. Przykład: `MaxQuality|Male`.</li></li></ul>  |query|ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-detect"></a>GET / wykrywania

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Określa język fragment tekstu.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Wartość zwracana:** Ciąg, który zawiera kod języka dwóch znaków w tekście.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)  |Wymagana. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|text|(pusty)|Wymagane. Ciąg, który zawiera tekst, którego język jest zidentyfikowanie. Tekst nie może przekraczać 10 000 znaków.|query|  ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key  |(pusty)    |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|


## <a name="post-detectarray"></a>OPUBLIKUJ /DetectArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

Określa języki, w tablicy ciągów. Niezależnie od siebie wykrywa język każdego elementu tablicy poszczególnych i zwraca wynik dla każdego wiersza w tablicy.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Oto format treści żądania:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Tekst nie może przekraczać 10 000 znaków.

**Wartość zwracana:** Tablica ciągu, który zawiera kod języka dwuznakowy dla każdego wiersza w tabeli wejściowej.

Oto format treści odpowiedzi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`DetectArray` zakończyło się pomyślnie. Zwraca tablicę ciągów, która zawiera kod języka dwuznakowy dla każdego wiersza tabeli wejściowej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagana. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-addtranslation"></a>Pobierz /AddTranslation

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **Ogłoszone jako przestarzałe Uwaga:** Po 31 stycznia 2018 r. Ta metoda nie akceptują nowe zgłoszenia zdania. Otrzymasz komunikat o błędzie. Zobacz powiadomienie o zmianach wprowadzonych do współpracy Translation Framework (CTF).

Dodaje tłumaczenie do pamięci tłumaczeń.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych   |
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|OriginalText|(pusty)|Wymagany. Ciąg, który zawiera tekstu do przetłumaczenia. Maksymalna długość ciągu to 1000 znaków.|query|ciąg|
|translatedText|(pusty) |Wymagany. Ciąg zawierający tekst przetłumaczony na język docelowy. Maksymalna długość ciągu to 2000 znaków.|query|ciąg|
|from|(pusty)   |Wymagane. Ciąg, który reprezentuje kod języka oryginalnego język tekstu. Na przykład en dla języka angielskiego i "de" dla języka niemieckiego.|query|ciąg|
|na|(pusty)|Wymagany. Ciąg, który reprezentuje kod języka tłumaczenie tekstu do języka.|query|ciąg|
|rating|(pusty) |Opcjonalny. Liczba całkowita, która reprezentuje ocena jakości w ciągu. Wartość od -10 do 10. Wartość domyślna to 1.|query|integer|
|Typ zawartości|(pusty)    |Opcjonalny. Format tekst tłumaczony. Obsługiwane formaty to `text/plain` i `text/html`. Żadne elementy HTML muszą być poprawnie sformułowany i ukończone elementy.    |query|ciąg|
|category|(pusty)|Opcjonalny. Ciąg, który zawiera kategoria tłumaczenia (domena). Wartość domyślna to `general`.|query|ciąg|
|Użytkownik|(pusty)|Wymagana. Ciąg, który służy do śledzenia inicjatorem przesyłania.|query|ciąg|
|Identyfikator URI|(pusty)|Opcjonalny. Ciąg, który zawiera lokalizacji zawartości tłumaczenia.|query|ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.  |nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|410|`AddTranslation` nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-addtranslationarray"></a>OPUBLIKUJ /AddTranslationArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **Ogłoszone jako przestarzałe Uwaga:** Po 31 stycznia 2018 r. Ta metoda nie akceptują nowe zgłoszenia zdania. Otrzymasz komunikat o błędzie. Zobacz powiadomienie o zmianach wprowadzonych do współpracy Translation Framework (CTF).

Dodaje tablicę tłumaczenia do translation pamięci. Ta metoda jest wersją tablicy `AddTranslation`.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Oto format treści żądania:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Te elementy znajdują się w `AddtranslationsRequest`:

* `AppId`: Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `AppId` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.
* `From`: Wymagana. Ciąg, który zawiera kod języka języka źródłowego. Musi być jednym z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `To`: Wymagany. Ciąg, który zawiera kod języka w języku docelowym. Musi być jednym z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `Translations`: Wymagana. Tablica tłumaczeń, aby dodać do pamięci tłumaczeń. Każde tłumaczenie musi zawierać `OriginalText`, `TranslatedText`, i `Rating`. Maksymalny rozmiar każdego `OriginalText` i `TranslatedText` wynosi 1000 znaków. Suma wszystkich `OriginalText` i `TranslatedText` elementów nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 100.
* `Options`: Wymagana. Zestaw opcji, w tym `Category`, `ContentType`, `Uri`, i `User`. `User` jest wymagany. `Category`, `ContentType`, i `Uri` są opcjonalne. Musi być wymieniony określonych elementów w kolejności alfabetycznej.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`AddTranslationArray` Metody powiodło się. 

Po 31 stycznia 2018 r. zdania zgłoszenia nie będą akceptowane. Usługa udzieli odpowiedzi z kodem błędu 410.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|410    |`AddTranslation` nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-breaksentences"></a>Pobierz /BreakSentences

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Dzieli fragment tekstu na zdania i zwraca tablicę zawierającą długości każdego zdania.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Wartość zwracana:** Tablica liczb całkowitych reprezentuje długości zdania. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica liczb całkowitych reprezentuje długości zdania. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

integer

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)  |Wymagana. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query| ciąg|
|text|(pusty)   |Wymagane. Ciąg, który reprezentuje tekst, aby podzielić na zdania. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|język   |(pusty)    |Wymagany. Ciąg, który reprezentuje kod języka tekstu wejściowego.|query|ciąg|
|Authorization|(pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.   |nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)|Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401|Nieprawidłowe poświadczenia.|
|500|Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera tablicę tłumaczenia dla danego języka parę z aparatu MT i magazyn. `GetTranslations` różni się od `Translate` , zwraca ono wszystkie dostępne tłumaczenia.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Treść żądania zawiera opcjonalne `TranslationOptions` obiektu, który ma następujący format:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` Obiekt zawiera wartości z poniższej listy. Są one wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.

* `Category`: Ciąg, który zawiera kategoria tłumaczenia (domena). Wartość domyślna to `general`.
* `ContentType`: Jedyną obsługiwaną opcją, a wartość domyślna to `text/plain`.
* `IncludeMultipleMTAlternatives`: Flagę logiczną, aby określić, czy więcej niż jeden alternatywna ma zostać zwrócone z aparatu MT. Prawidłowe wartości to `true` i `false` (z uwzględnieniem wielkości liter). Wartość domyślna to `false`, która zwraca tylko jedno alternatywne. Ustawienie flagi `true` umożliwia tworzenie sztuczne rozwiązania alternatywne, w pełni zintegrowana z współpracy Translation Framework (CTF). Ta funkcja umożliwia zwracanie alternatywy dla zdań, które mają nie tłumaczeń w CTF, dodając sztuczne alternatywy z *n*— najlepsze listę dekodera.
    - Klasyfikacje. Klasyfikacje są stosowane następująco: 
         - Najlepsze automatycznego tłumaczenia ma klasyfikację od 5.
       - Alternatywy z CTF odzwierciedlają urząd recenzenta. Mogą należeć do zakresu od -10 do + 10.
       - Automatycznie generowane (*n*— najlepsze) alternatywnych tłumaczeń ma klasyfikację od 0 do 100 stopnia dopasowania.
    - Liczba alternatyw. Liczba zwróconych alternatywy może być możliwie jak wartość określoną w `maxTranslations`, ale niższe.
    - Pary języka. Ta funkcja nie jest dostępna w poszukiwaniu tłumaczeń między języka chińskiego uproszczonego i chińskim tradycyjnym, w dowolnym kierunku. Jest ona dostępna dla innych par językowych obsługiwanych przez Microsoft Translator.
* `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Tej samej zawartości zostaną zwrócone w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli zostanie ustawiona żadna wartość, wartość domyślna to `all`.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli zostanie ustawiona żadna wartość, wartość domyślna to `all`.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** Oto format odpowiedzi:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Ta odpowiedź zawiera `GetTranslationsResponse` element, który zawiera następujące wartości:

* `Translations`: Znaleziono tablicę dopasowania, przechowywane w `TranslationMatch` obiektów (opisany w następnej sekcji). Tłumaczenia mogą obejmować nieznaczne warianty oryginalny tekst (dopasowywania rozmytego). Będą sortowane tłumaczenia: 100% najpierw dopasowuje obok dopasowania rozmyte.
* `From`: Jeśli nie określono metody `From` języka, ta wartość będzie pochodził z automatycznego wykrywania języka. W przeciwnym razie będzie określony `From` języka.
* `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Zawiera wartość podana w `TranslateOptions` parametru.

`TranslationMatch` Obiekt, który składa się z następujących wartości:

* `Error`: Kod błędu, jeśli wystąpi błąd w poszukiwaniu określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak blisko tekst wejściowy pasuje oryginalny tekst, znaleziono w magazynie. System dopasowuje zdania wejściowe względem magazynu, w tym niedokładny dopasowań. Wartość zwracana zakresy z zakresu od 0 do 100, gdzie 0 oznacza nie podobieństwa i 100 jest dopasowanie dokładne, wielkość liter.
* `MatchedOriginalText`: Oryginalny tekst dopasowanym dla tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy oryginalny dopasowany tekst został różni się od tekstu wejściowego. Służy do zwrócenia tekst źródłowy dopasowywania rozmytego. Ta wartość nie jest zwracana dla wyników Microsoft Translator.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego ma klasyfikację od 5. Anonimowo podana tłumaczenia ma zazwyczaj klasyfikacji z zakresu od 1 do 4. Tłumaczenia autorytatywnie podana zwykle obejmuje ocena od 6 do 10.
* `Count`: Liczba przypadków, gdy wybrano to tłumaczenie z tą klasyfikacją. Wartość wynosi 0 dla automatycznie przetłumaczone odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
A `GetTranslationsResponse` obiekt w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|appid|(pusty)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw `appid` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.|query|ciąg|
|text|(pusty)|Wymagane. Ciąg, który reprezentuje tekstu do przetłumaczenia. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|from|(pusty)|Wymagane. Ciąg, który reprezentuje kod języka tekstu podlega translacji.|query|ciąg|
|na |(pusty)    |Wymagany. Ciąg, który reprezentuje kod języka tłumaczenie tekstu do języka.|query|ciąg|
|maxTranslations|(pusty)|Wymagane. Liczba całkowita, która przedstawia maksymalną liczbę tłumaczeń do zwrócenia.|query|integer|
|Authorization| (pusty)|Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|ciąg|  nagłówek|
|OCP-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera wielu kandydatów tłumaczenia na wiele tekstów źródła.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Oto format treści żądania:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` obejmuje następujące elementy:

* `AppId`: Wymagana. Jeśli `Authorization` nagłówek jest używany, pozostaw `AppId` pole puste. W przeciwnym razie zawierają ciąg, który zawiera `"Bearer" + " " + "access_token"`.
* `From`: Wymagany. Ciąg, który reprezentuje kod języka tekstu podlega translacji.
* `MaxTranslations`: Wymagane. Liczba całkowita, która przedstawia maksymalną liczbę tłumaczeń do zwrócenia.
* `Options`: Opcjonalny. `Options` Obiekt, który zawiera następujące wartości. Są one wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.
    - `Category`: Ciąg, który zawiera kategoria tłumaczenia (domena). Wartość domyślna to `general`.
    - `ContentType`: Jedyną obsługiwaną opcją, a wartość domyślna to `text/plain`.
    - `IncludeMultipleMTAlternatives`: Flagę logiczną, aby określić, czy więcej niż jeden alternatywna ma zostać zwrócone z aparatu MT. Prawidłowe wartości to `true` i `false` (z uwzględnieniem wielkości liter). Wartość domyślna to `false`, która zwraca tylko jedno alternatywne. Ustawienie flagi `true` umożliwia generowanie sztuczne alternatywy w tłumaczeniu, w pełni zintegrowana z współpracy Framework tłumaczenia (CTF). Ta funkcja umożliwia zwracanie alternatywy dla zdań, które mają nie alternatywy w CTF, dodając sztuczne alternatywy z *n*— najlepsze listę dekodera.
        - Klasyfikacje, klasyfikacje są stosowane w następujący sposób:
          - Najlepsze automatycznego tłumaczenia ma klasyfikację od 5.
          - Alternatywy z CTF odzwierciedlają urząd recenzenta. Mogą należeć do zakresu od -10 do + 10.
          - Automatycznie generowane (*n*— najlepsze) alternatywnych tłumaczeń ma klasyfikację od 0 do 100 stopnia dopasowania.
        - Liczba alternatyw. Liczba zwróconych alternatywy może być możliwie jak wartość określoną w `maxTranslations`, ale niższe.
        - Pary języka. Ta funkcja nie jest dostępna w poszukiwaniu tłumaczeń między języka chińskiego uproszczonego i chińskim tradycyjnym, w dowolnym kierunku. Jest ona dostępna dla innych par językowych obsługiwanych przez Microsoft Translator.
* `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Tej samej zawartości zostaną zwrócone w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli zostanie ustawiona żadna wartość, wartość domyślna to `all`.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli zostanie ustawiona żadna wartość, wartość domyślna to `all`.
* `Texts`: Wymagana. Tablica, która zawiera tekstu do przetłumaczenia. Wszystkie ciągi musi być w tym samym języku. Suma cały tekst do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 10.
* `To`: Wymagane. Ciąg, który reprezentuje kod języka tłumaczenie tekstu do języka.

Można pominąć elementy opcjonalne. Elementy, które są bezpośrednie elementy podrzędne `GetTranslationsArrayRequest` muszą być wymienione w kolejności alfabetycznej.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** Oto format odpowiedzi:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Każdy `GetTranslationsResponse` element zawiera następujące wartości:

* `Translations`: Znaleziono tablicę dopasowania, przechowywane w `TranslationMatch` obiektów (opisany w następnej sekcji). Tłumaczenia mogą obejmować nieznaczne warianty oryginalny tekst (dopasowywania rozmytego). Będą sortowane tłumaczenia: 100% najpierw dopasowuje obok dopasowania rozmyte.
* `From`: Jeśli nie określono metody `From` języka, ta wartość będzie pochodził z automatycznego wykrywania języka. W przeciwnym razie będzie określony `From` języka.
* `State`: Stan użytkownika, aby skorelować żądań i odpowiedzi. Zawiera wartość podana w `TranslateOptions` parametru.

`TranslationMatch` Obiekt zawiera następujące wartości:
* `Error`: Kod błędu, jeśli wystąpi błąd w poszukiwaniu określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak blisko tekst wejściowy pasuje oryginalny tekst, znaleziono w magazynie. System dopasowuje zdania wejściowe względem magazynu, w tym niedokładny dopasowań. Wartość zwracana zakresy z zakresu od 0 do 100, gdzie 0 oznacza nie podobieństwa i 100 jest dopasowanie dokładne, wielkość liter.
* `MatchedOriginalText`: Oryginalny tekst dopasowanym dla tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy oryginalny dopasowany tekst został różni się od tekstu wejściowego. Służy do zwrócenia tekst źródłowy dopasowywania rozmytego. Ta wartość nie jest zwracana dla wyników Microsoft Translator.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego ma klasyfikację od 5. Anonimowo podana tłumaczenia ma zazwyczaj klasyfikacji z zakresu od 1 do 4. Autorytatywnie podana tłumaczenia zawierają zwykle ma klasyfikację od 6 do 10.
* `Count`: Liczba przypadków, gdy wybrano to tłumaczenie z tą klasyfikacją. Wartość wynosi 0 dla automatycznie przetłumaczone odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.


### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Authorization  |(pusty)    |Wymagane, jeśli obie `appid` pola i `Ocp-Apim-Subscription-Key` nagłówka są puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli obie `appid` pola i `Authorization` nagłówka są puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie do API tekstu usługi Translator w wersji 3](../migrate-to-v3.md)


