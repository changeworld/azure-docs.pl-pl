---
title: Interfejs API tekstu tłumacza w wersji 2.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja referencyjna dla interfejsu API tekstu tłumacza w wersji 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242502"
---
# <a name="translator-text-api-v20"></a>Interfejs API tekstu tłumacza w wersji 2.0

> [!IMPORTANT]
> Ta wersja interfejsu API tekstu translatora została przestarzała. [Służy do wyświetlania dokumentacji dotyczącej wersji 3 interfejsu API tekstu tłumacza](v3-0-reference.md).

Wersja 2 interfejsu API tekstu tłumacza można bezproblemowo zintegrować z aplikacjami, witrynami internetowymi, narzędziami lub innymi rozwiązaniami, aby zapewnić użytkownikom wielojęzyczne środowisko. Można go używać na dowolnej platformie sprzętowej i z dowolnym systemem operacyjnym do wykonywania tłumaczeń językowych i innych zadań związanych z językiem, takich jak wykrywanie języka tekstowego i zamiana tekstu na mowę, zgodnie ze standardami branżowymi. Aby uzyskać więcej informacji, zobacz [Interfejs API tekstu tłumacza](../translator-info-overview.md).

## <a name="getting-started"></a>Wprowadzenie
Aby uzyskać dostęp do interfejsu API tekstu translatora, musisz [zarejestrować się w usłudze Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Uwierzytelnianie 
Wszystkie wywołania interfejsu API tekstu translatora wymagają klucza subskrypcji do uwierzytelniania. Interfejs API obsługuje trzy metody uwierzytelniania:

- Token dostępu. Użyj klucza subskrypcji, aby utworzyć token dostępu, wysyłając żądanie POST do usługi uwierzytelniania. Szczegółowe informacje można znaleźć w dokumentacji usługi tokenu. Przekaż token dostępu do usługi Translator `Authorization` przy `access_token` użyciu nagłówka lub parametru zapytania. Token dostępu jest prawidłowy przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i zachować przy użyciu tego samego tokenu dostępu dla powtarzających się żądań w ciągu 10 minut.
- Klucz subskrypcji używany bezpośrednio. Przekaż klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` w nagłówku dołączonym do żądania do interfejsu API tekstu translatora. Podczas korzystania z klucza subskrypcji bezpośrednio, nie trzeba wywoływać usługi uwierzytelniania tokenu, aby utworzyć token dostępu.
- [Subskrypcja wielu usług usług Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ta metoda umożliwia użycie pojedynczego klucza tajnego do uwierzytelniania żądań dla wielu usług.
Korzystając z klucza tajnego wielu usług, należy dołączyć dwa nagłówki uwierzytelniania z żądaniem. Pierwszy nagłówek przekazuje klucz tajny. Drugi nagłówek określa region skojarzony z subskrypcją:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Region jest wymagany dla subskrypcji wielodochowego interfejsu API tekstu. Wybrany region jest jedynym regionem, którego można używać do tłumaczenia tekstu podczas korzystania z klucza subskrypcji z wieloma usługami. Musi to być ten sam region wybrany podczas konfigurowania subskrypcji wielu usług w witrynie Azure portal.

Dostępne regiony `australiaeast`to `brazilsouth` `canadacentral`, `centralindia` `centraluseuap`, `eastasia` `eastus`, `eastus2` `japaneast`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus`, `westus2`, , , , , , , , , i .

Klucz subskrypcji i token dostępu są wpisami tajnymi, które powinny być ukryte w widoku.

## <a name="profanity-handling"></a>Obsługa wulgaryzmów
Zwykle usługa Translator zachowa wulgaryzmy, które są obecne w źródle. Stopień wulgaryzmów i kontekst, który sprawia, że słowa wulgarne różnią się w zależności od kultury. Tak więc stopień wulgaryzmów w języku docelowym może być zwiększony lub zmniejszony.

Jeśli chcesz zapobiec wulgaryzmom w tłumaczeniu, nawet jeśli znajduje się ono w tekście źródłowym, możesz użyć opcji filtrowania wulgaryzmów dla metod, które go obsługują. Ta opcja umożliwia wybranie, czy wulgaryzmy mają być usuwane, czy oznaczane odpowiednimi znacznikami, czy też chcesz zezwolić na wulgaryzmy w docelowych. Akceptowane wartości są `ProfanityAction` `NoAction` (domyślne), `Marked` `Deleted`i .


|WulgaryzmyAkcja    |Akcja |Przykładowe źródło (japońskie)  |Przykładowe tłumaczenie (angielski)  |
|:--|:--|:--|:--|
|NoAction ( NoAction )   |Domyślne. Tak samo jak nie ustawianie opcji. Wulgaryzmy będą przechodzić ze źródła do celu.        |中田田ドッドスドド     |On jest jackass.   |
|Oznaczone     |Wulgarne słowa będą otoczone znacznikami \<XML wulgaryzmami> i \</wulgaryzmy>.       |中田田ドッドスドド |Jest wulgaryzmem \<>jackass\</wulgaryzmy>.  |
|Usunięte    |Wulgarne słowa zostaną usunięte z wyjścia bez wymiany.     |中田田ドッドスドド |On jest.   |

    
## <a name="excluding-content-from-translation"></a>Wykluczanie treści z tłumaczenia
Podczas tłumaczenia zawartości za pomocą`contentType=text/html`tagów, takich jak HTML ( ), czasami warto wykluczyć określoną zawartość z tłumaczenia. Za pomocą tego `class=notranslate` atrybutu można określić zawartość, która powinna pozostać w oryginalnym języku. W poniższym przykładzie zawartość `div` w pierwszym elemencie nie zostanie przetłumaczona, ale zawartość w drugim `div` elemencie zostanie przetłumaczona.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>POBIERZ /Przetłumacz

### <a name="implementation-notes"></a>Uwagi o implementacji
Tłumaczy ciąg tekstowy z jednego języka na inny.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/Translate`żądania jest .

**Wartość zwrotu:** Ciąg reprezentujący przetłumaczony tekst.

Jeśli wcześniej był `AddTranslation` `AddTranslationArray` używany lub wprowadzić tłumaczenie z oceną 5 lub `Translate` wyższą dla tego samego zdania źródłowego, zwraca tylko najlepszy wybór, który jest dostępny dla systemu. "To samo zdanie źródłowe" oznacza dokładnie to samo (100% dopasowania), z wyjątkiem wielkich liter, odstępów, wartości znaczników i znaków interpunkcyjnych na końcu zdania. Jeśli żadna ocena nie jest przechowywana z oceną 5 lub wyższym, zwracanym wynikiem będzie automatyczne tłumaczenie przez usługę Microsoft Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis    |Typ parametru|typ danych|
|:--|:--|:--|:--|:--|
|Appid  |(pusty)    |Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|tekst|(pusty)   |Wymagany. Ciąg reprezentujący tekst do przetłumaczenia. Tekst nie może zawierać więcej niż 10 000 znaków.|query|ciąg|
|Z|(pusty)   |Element opcjonalny. Ciąg reprezentujący kod języka tłumaczonego tekstu. Na przykład en dla języka angielskiego.|query|ciąg|
|na|(pusty) |Wymagany. Ciąg reprezentujący kod języka do przetłumaczenia tekstu na.|query|ciąg|
|Contenttype|(pusty)    |Element opcjonalny. Format tłumaczonego tekstu. Obsługiwane formaty `text/plain` to (domyślnie) i `text/html`. Wszystkie elementy HTML muszą być dobrze ukształtowane, kompletne elementy.|query|ciąg|
|category|(pusty)   |Element opcjonalny. Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.|query|ciąg|
|Autoryzacja|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|


### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera tłumaczenia dla wielu tekstów źródłowych.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`żądania jest .

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

Elementy te `TranslateArrayRequest`znajdują się w:


* `AppId`: Wymagane. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `AppId` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .
* `From`: Opcjonalnie. Ciąg reprezentujący kod języka tłumaczonego tekstu. Jeśli to pole pozostanie puste, odpowiedź będzie zawierać wynik automatycznego wykrywania języka.
* `Options`: Opcjonalnie. Obiekt, `Options` który zawiera następujące wartości. Wszystkie są opcjonalne i domyślnie należą do najczęściej spotykanych ustawień. Określone elementy muszą być wymienione w kolejności alfabetycznej.
    - `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
    - `ContentType`: Format tłumaczonego tekstu. Obsługiwane formaty to `text/plain` `text/xml`(domyślnie), `text/html`i . Wszystkie elementy HTML muszą być dobrze ukształtowane, kompletne elementy.
    - `ProfanityAction`: Określa sposób obchodzenia się z wulgaryzmami, jak wyjaśniono wcześniej. Akceptowane wartości `NoAction` to (domyślnie), `Marked`i `Deleted`.
    - `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
    - `Uri`: Filtrowanie wyników według tego identyfikatora URI. Wartość domyślna: `all`.
    - `User`: Filtrowanie wyników przez tego użytkownika. Wartość domyślna: `all`.
* `Texts`: Wymagane. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być w tym samym języku. Suma wszystkich tłumaczonych tekstów nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy wynosi 2000.
* `To`: Wymagane. Ciąg reprezentujący kod języka do przetłumaczenia tekstu na.

Można pominąć elementy opcjonalne. Elementy, które są `TranslateArrayRequest` bezpośrednimi elementami podrzędnymi muszą być wymienione w kolejności alfabetycznej.

Metoda `TranslateArray` akceptuje `application/xml` lub `text/xml` `Content-Type`dla .

**Wartość zwrotu:** Tablica. `TranslateArrayResponse` Każdy `TranslateArrayResponse` ma następujące elementy:

* `Error`: Wskazuje błąd w przypadku wystąpienia. W przeciwnym razie ustawiono wartość null.
* `OriginalSentenceLengths`: Tablica liczby całkowitych wskazująca długość każdego zdania w tekście źródłowym. Długość tablicy wskazuje liczbę zdań.
* `TranslatedText`: Przetłumaczony tekst.
* `TranslatedSentenceLengths`: Tablica liczby całkowitych wskazująca długość każdego zdania w przetłumaczonym tekście. Długość tablicy wskazuje liczbę zdań.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zwraca tę samą zawartość co żądanie.

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
Pomyślna odpowiedź zawiera `TranslateArrayResponse` tablicę tablic w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP   |Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd. Typowe błędy obejmują: <ul><li>Element tablicy nie może być pusty.</li><li>Nieprawidłowa kategoria.</li><li>Z języka jest nieprawidłowy.</li><li>Język jest nieprawidłowy.</li><li>Żądanie zawiera zbyt wiele elementów.</li><li>Język Od nie jest obsługiwany.</li><li>Język Do nie jest obsługiwany.</li><li>Przetłumacz żądanie ma zbyt dużo danych.</li><li>Kod HTML nie jest w prawidłowym formacie.</li><li>Zbyt wiele ciągów zostały przekazane w Translate Request.</li></ul>|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera przyjazne nazwy dla języków przekazanych `languageCodes`jako parametr , `locale` zlokalizowane w przekazanym języku.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`żądania jest .

Treść żądania zawiera tablicę ciągów, która reprezentuje kody języka ISO 639-1, dla których można pobrać przyjazne nazwy. Oto przykład:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Wartość zwrotu:** Tablica ciągów zawierająca nazwy języków obsługiwane przez usługę Translator, zlokalizowana w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca nazwy języków obsługiwane przez usługę Translator, zlokalizowana w żądanym języku.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|locale|(pusty) |Wymagany. Ciąg reprezentujący jedną z następujących opcji, używany do lokalizowania nazw języków: <ul><li>Połączenie dwuliterowego kodu kultury małych liter ISO 639 skojarzonego z językiem i dwuliterowego kodu subkultury ISO 3166. <li>Kod kultury małych liter ISO 639 sam w sobie.|query|ciąg|
|Autoryzacja|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-getlanguagesfortranslate"></a>POBIERZ /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera listę kodów języków, które reprezentują języki obsługiwane przez usługę tłumaczenia.  `Translate`i `TranslateArray` może tłumaczyć między dwoma z tych języków.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`żądania jest .

**Wartość zwrotu:** Tablica ciągów zawierająca kody języków obsługiwane przez usługę Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca kody języków obsługiwane przez usługę Translator.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|Autoryzacja|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503|Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-getlanguagesforspeak"></a>POBIERZ /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera języki dostępne do syntezy mowy.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`żądania jest .

**Wartość zwrotu:** Tablica ciągów zawierająca kody języków obsługiwane do syntezy mowy przez usługę Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca kody języków obsługiwane do syntezy mowy przez usługę Translator.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|
 
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401|Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-speak"></a>GET /Mów

### <a name="implementation-notes"></a>Uwagi o implementacji
Zwraca strumień WAV lub MP3 przekazywany tekst, używany w żądanym języku.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/Speak`żądania jest .

**Wartość zwrotu:** Strumień WAV lub MP3 przekazywany tekst, używany w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

binarny

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|tekst|(pusty)   |Wymagany. Ciąg, który zawiera jedno lub więcej zdań, które mają być używane dla strumienia, w określonym języku. Tekst nie może przekraczać 2000 znaków.|query|ciąg|
|language|(pusty)   |Wymagany. Ciąg reprezentujący obsługiwany kod języka języka, w którym ma być wypowiadany tekst. Kod musi być jednym z kodów `GetLanguagesForSpeak`zwróconych metodą .|query|ciąg|
|format|(pusty)|Element opcjonalny. Ciąg określający identyfikator zawartości. Obecnie `audio/wav` i `audio/mp3` są dostępne. Wartością domyślną jest `audio/wav`.|query|ciąg|
|opcje|(pusty)    |Element opcjonalny. Ciąg określający właściwości syntetyzowanej mowy:<ul><li>`MaxQuality`i `MinSize` określić jakość sygnału audio. `MaxQuality`zapewnia najwyższą jakość. `MinSize`zapewnia najmniejszy rozmiar pliku. Wartość domyślna to `MinSize`.</li><li>`female`i `male` określić żądaną płeć głosu. Wartość domyślna to `female`. Użyj pionowego<code>\|</code>paska ( ), aby uwzględnić wiele opcji. Przykład: `MaxQuality|Male`.</li></li></ul>  |query|ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-detect"></a>POBIERZ /Wykryj

### <a name="implementation-notes"></a>Uwagi o implementacji
Identyfikuje język sekcji tekstu.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/Detect`żądania jest .

**Wartość zwrotu:** Ciąg, który zawiera dwuznakowy kod języka dla tekstu.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)  |Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|tekst|(pusty)|Wymagany. Ciąg zawierający tekst, którego język ma być zidentyfikowany. Tekst nie może przekraczać 10 000 znaków.|query|  ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key  |(pusty)    |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Uwagi o implementacji

Identyfikuje języki w tablicy ciągów. Niezależnie wykrywa język każdego pojedynczego elementu tablicy i zwraca wynik dla każdego wiersza tablicy.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`żądania jest .

Oto format treści żądania:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Tekst nie może przekraczać 10 000 znaków.

**Wartość zwrotu:** Tablica ciągów zawierająca dwuznakowy kod języka dla każdego wiersza w tablicy wejściowej.

Oto format treści odpowiedzi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`DetectArray`się powiodła. Zwraca tablicę ciągów zawierającą dwuznakowy kod języka dla każdego wiersza tablicy wejściowej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-addtranslation"></a>POBIERZ /AddTranslation

### <a name="implementation-notes"></a>Uwagi o implementacji

> [!IMPORTANT]
> **Uwaga dotycząca wycofania:** Po 31 stycznia 2018 r. ta metoda nie będzie akceptować nowych zdań. Zostanie wyświetlony komunikat o błędzie. Zobacz ogłoszenie o zmianach w ramach tłumaczenia współpracy (CTF).

Dodaje tłumaczenie do pamięci tłumaczenia.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`żądania jest .

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych   |
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|originalText|(pusty)|Wymagany. Ciąg zawierający tekst do przetłumaczenia. Maksymalna długość ciągu wynosi 1000 znaków.|query|ciąg|
|translatedTekst|(pusty) |Wymagany. Ciąg zawierający tekst przetłumaczony na język docelowy. Maksymalna długość ciągu wynosi 2000 znaków.|query|ciąg|
|Z|(pusty)   |Wymagany. Ciąg reprezentujący kod języka oryginalnego języka tekstu. Na przykład en dla języka angielskiego i de dla języka niemieckiego.|query|ciąg|
|na|(pusty)|Wymagany. Ciąg reprezentujący kod języka języka do przetłumaczenia tekstu.|query|ciąg|
|rating|(pusty) |Element opcjonalny. Liczba całkowita reprezentująca klasyfikację jakości dla ciągu. Wartość wynosi od -10 do 10. Domyślnym ustawieniem jest 1.|query|liczba całkowita|
|Contenttype|(pusty)    |Element opcjonalny. Format tłumaczonego tekstu. Obsługiwane formaty są `text/plain` `text/html`i . Wszystkie elementy HTML muszą być dobrze ukształtowane, kompletne elementy.    |query|ciąg|
|category|(pusty)|Element opcjonalny. Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.|query|ciąg|
|użytkownik|(pusty)|Wymagany. Ciąg, który jest używany do śledzenia inicjatora przesyłania.|query|ciąg|
|Identyfikator uri|(pusty)|Element opcjonalny. Ciąg zawierający lokalizację zawartości tłumaczenia.|query|ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.  |nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|410|`AddTranslation`nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Uwagi o implementacji

> [!IMPORTANT]
> **Uwaga dotycząca wycofania:** Po 31 stycznia 2018 r. ta metoda nie będzie akceptować nowych zdań. Zostanie wyświetlony komunikat o błędzie. Zobacz ogłoszenie o zmianach w ramach tłumaczenia współpracy (CTF).

Dodaje tablicę tłumaczeń do pamięci tłumaczeniowej. Ta metoda jest wersją tablicową programu `AddTranslation`.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`żądania jest .

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

Elementy te `AddtranslationsRequest`znajdują się w:

* `AppId`: Wymagane. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `AppId` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .
* `From`: Wymagane. Ciąg, który zawiera kod języka źródłowego. Musi być jednym z języków `GetLanguagesForTranslate` zwróconych przez metodę.
* `To`: Wymagane. Ciąg, który zawiera kod języka docelowego języka docelowego. Musi być jednym z języków `GetLanguagesForTranslate` zwróconych przez metodę.
* `Translations`: Wymagane. Tablica tłumaczeń do dodania do pamięci tłumaczeniowej. Każde tłumaczenie `OriginalText`musi `TranslatedText`zawierać `Rating`, i . Maksymalny rozmiar `OriginalText` każdego `TranslatedText` z nich wynosi 1000 znaków. Suma wszystkich `OriginalText` `TranslatedText` elementów nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy wynosi 100.
* `Options`: Wymagane. Zestaw opcji, w `Category` `ContentType`tym `Uri`, `User`, i . Ciąg `User` jest wymagany. `Category`, `ContentType`i `Uri` są opcjonalne. Określone elementy muszą być wymienione w kolejności alfabetycznej.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`AddTranslationArray`metoda powiodła się. 

Po 31 stycznia 2018 r. wnioski z wyroku nie będą przyjmowane. Usługa odpowie kodem błędu 410.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|410    |`AddTranslation`nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503|Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Uwagi o implementacji
Dzieli część tekstu na zdania i zwraca tablicę zawierającą długości każdego zdania.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`żądania jest .

**Wartość zwrotu:** Tablica liczby całkowitej reprezentująca długości zdań. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica liczby całkowitej reprezentująca długości zdań. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

liczba całkowita

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)  |Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query| ciąg|
|tekst|(pusty)   |Wymagany. Ciąg reprezentujący tekst do podziału na zdania. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|language   |(pusty)    |Wymagany. Ciąg reprezentujący kod języka tekstu wejściowego.|query|ciąg|
|Autoryzacja|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.   |nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401|Nieprawidłowe poświadczenia.|
|500|Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503|Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera tablicę tłumaczeń dla danej pary języków ze sklepu i aparatu MT. `GetTranslations`różni się `Translate` tym, że zwraca wszystkie dostępne tłumaczenia.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`żądania jest .

Treść żądania zawiera obiekt `TranslationOptions` opcjonalny, który ma ten format:

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

Obiekt `TranslateOptions` zawiera wartości na poniższej liście. Wszystkie są opcjonalne i domyślnie należą do najczęściej spotykanych ustawień. Określone elementy muszą być wymienione w kolejności alfabetycznej.

* `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
* `ContentType`: Jedyną obsługiwaną opcją i `text/plain`wartością domyślną jest .
* `IncludeMultipleMTAlternatives`: Flaga logiczna określająca, czy z aparatu MT powinna zostać zwrócona więcej niż jedna alternatywa. Prawidłowe `true` wartości `false` są i (wielkość liter). Wartość domyślna to `false`, która zwraca tylko jedną alternatywę. Ustawienie flagi `true` w celu stworzenia sztucznych alternatyw, w pełni zintegrowanych z ramami tłumaczenia współpracy (CTF). Funkcja umożliwia zwracanie alternatyw dla zdań, które nie mają tłumaczeń w CTF, dodając sztuczne alternatywy z *n*-best listy dekodera.
    - Oceny. Oceny są stosowane w ten sposób: 
         - Najlepsze tłumaczenie automatyczne ma ocenę 5.
       - Alternatywy z CTF odzwierciedlają uprawnienia recenzenta. Wahają się one od -10 do +10.
       - Automatycznie generowane *(n*-najlepsze) alternatywy tłumaczenia mają ocenę 0 i stopień dopasowania 100.
    - Liczba alternatyw. Liczba zwróconych alternatyw może być tak wysoka, `maxTranslations`jak wartość określona w , ale może być niższa.
    - Pary językowe. Ta funkcja nie jest dostępna w przypadku tłumaczeń między chińskim uproszczonym a chińskim tradycyjnym w obu kierunkach. Jest on dostępny dla wszystkich innych par językowych obsługiwanych przez usługę Microsoft Translator.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* `Uri`: Filtrowanie wyników według tego identyfikatora URI. Jeśli żadna wartość nie `all`jest ustawiona, wartością domyślną jest .
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli żadna wartość nie `all`jest ustawiona, wartością domyślną jest .

Wniosek `Content-Type` powinien `text/xml`być .

**Wartość zwrotu:** Oto format odpowiedzi:

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

Ta odpowiedź `GetTranslationsResponse` zawiera element, który zawiera następujące wartości:

* `Translations`: Tablica znalezionych dopasowań, przechowywana w `TranslationMatch` obiektach (opisana w poniższej sekcji). Tłumaczenia mogą zawierać niewielkie warianty oryginalnego tekstu (dopasowywanie rozmyte). Tłumaczenia zostaną posortowane: 100% meczów najpierw, rozmyte mecze obok.
* `From`: Jeśli metoda nie określa `From` języka, ta wartość będzie pochodzić z automatycznego wykrywania języka. W przeciwnym razie będzie `From` to określony język.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zawiera wartość podana `TranslateOptions` w parametrze.

Obiekt `TranslationMatch` składa się z następujących wartości:

* `Error`: Kod błędu, jeśli wystąpi błąd dla określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak ściśle tekst wejściowy jest zgodny z oryginalnym tekstem znalezionym w sklepie. System dopasowuje zdania wejściowe do magazynu, w tym niedokładne dopasowania. Zwracana wartość waha się od 0 do 100, gdzie 0 nie jest podobieństwo i 100 jest dokładne dopasowanie z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst, który został dopasowany do tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy dopasowany tekst oryginalny różnił się od tekstu wejściowego. Służy do zwracania tekstu źródłowego rozmytego dopasowania. Ta wartość nie jest zwracana dla wyników usługi Microsoft Translator.
* `Rating`: Wskazuje autorytet osoby podejmującej decyzję o jakości. Wyniki tłumaczenia maszynowego mają ocenę 5. Anonimowo dostarczone tłumaczenia mają zazwyczaj ocenę od 1 do 4. Autorytatywnie dostarczone tłumaczenia będą miały zazwyczaj ocenę od 6 do 10.
* `Count`: Liczba wybranych tłumaczeń z tą oceną. Wartość jest 0 dla automatycznie przetłumaczonej odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Obiekt `GetTranslationsResponse` w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Appid|(pusty)|Wymagany. Jeśli `Authorization` używany `Ocp-Apim-Subscription-Key` jest nagłówek lub `appid` nagłówek, pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .|query|ciąg|
|tekst|(pusty)|Wymagany. Ciąg reprezentujący tekst do przetłumaczenia. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|Z|(pusty)|Wymagany. Ciąg reprezentujący kod języka tłumaczonego tekstu.|query|ciąg|
|na |(pusty)    |Wymagany. Ciąg reprezentujący kod języka języka do przetłumaczenia tekstu.|query|ciąg|
|maxTranslations (maks.|(pusty)|Wymagany. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.|query|liczba całkowita|
|Autoryzacja| (pusty)|Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste. Token autoryzacji: `"Bearer" + " " + "access_token"`.|ciąg|  nagłówek|
|Ocp-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503|Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera wielu kandydatów do tłumaczenia dla wielu tekstów źródłowych.

Identyfikator URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`żądania jest .

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

`GetTranslationsArrayRequest`obejmuje następujące elementy:

* `AppId`: Wymagane. Jeśli `Authorization` nagłówek jest używany, `AppId` pozostaw pole puste. W przeciwnym razie dołącz `"Bearer" + " " + "access_token"`ciąg zawierający plik .
* `From`: Wymagane. Ciąg reprezentujący kod języka tłumaczonego tekstu.
* `MaxTranslations`: Wymagane. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.
* `Options`: Opcjonalnie. Obiekt, `Options` który zawiera następujące wartości. Wszystkie są opcjonalne i domyślnie należą do najczęściej spotykanych ustawień. Określone elementy muszą być wymienione w kolejności alfabetycznej.
    - `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
    - `ContentType`: Jedyną obsługiwaną opcją i `text/plain`wartością domyślną jest .
    - `IncludeMultipleMTAlternatives`: Flaga logiczna określająca, czy z aparatu MT powinna zostać zwrócona więcej niż jedna alternatywa. Prawidłowe `true` wartości `false` są i (wielkość liter). Wartość domyślna to `false`, która zwraca tylko jedną alternatywę. Ustawienie flagi `true` w celu umożliwienia generowania sztucznych alternatyw w tłumaczeniu, w pełni zintegrowanych z ramą tłumaczeń współpracy (CTF). Funkcja umożliwia zwracanie alternatyw dla zdań, które nie mają alternatyw w CTF, dodając sztuczne alternatywy z *n*-best listy dekodera.
        - Oceny Oceny są stosowane w ten sposób:
          - Najlepsze tłumaczenie automatyczne ma ocenę 5.
          - Alternatywy z CTF odzwierciedlają uprawnienia recenzenta. Wahają się one od -10 do +10.
          - Automatycznie generowane *(n*-najlepsze) alternatywy tłumaczenia mają ocenę 0 i stopień dopasowania 100.
        - Liczba alternatyw. Liczba zwróconych alternatyw może być tak wysoka, `maxTranslations`jak wartość określona w , ale może być niższa.
        - Pary językowe. Ta funkcja nie jest dostępna w przypadku tłumaczeń między chińskim uproszczonym a chińskim tradycyjnym w obu kierunkach. Jest on dostępny dla wszystkich innych par językowych obsługiwanych przez usługę Microsoft Translator.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* `Uri`: Filtrowanie wyników według tego identyfikatora URI. Jeśli żadna wartość nie `all`jest ustawiona, wartością domyślną jest .
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli żadna wartość nie `all`jest ustawiona, wartością domyślną jest .
* `Texts`: Wymagane. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być w tym samym języku. Suma wszystkich tłumaczonych tekstów nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy wynosi 10.
* `To`: Wymagane. Ciąg reprezentujący kod języka języka do przetłumaczenia tekstu.

Można pominąć elementy opcjonalne. Elementy, które są `GetTranslationsArrayRequest` bezpośrednimi elementami podrzędnymi muszą być wymienione w kolejności alfabetycznej.

Wniosek `Content-Type` powinien `text/xml`być .

**Wartość zwrotu:** Oto format odpowiedzi:

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

* `Translations`: Tablica znalezionych dopasowań, przechowywana w `TranslationMatch` obiektach (opisana w poniższej sekcji). Tłumaczenia mogą zawierać niewielkie warianty oryginalnego tekstu (dopasowywanie rozmyte). Tłumaczenia zostaną posortowane: 100% meczów najpierw, rozmyte mecze obok.
* `From`: Jeśli metoda nie określa `From` języka, ta wartość będzie pochodzić z automatycznego wykrywania języka. W przeciwnym razie będzie `From` to określony język.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zawiera wartość podana `TranslateOptions` w parametrze.

Obiekt `TranslationMatch` zawiera następujące wartości:
* `Error`: Kod błędu, jeśli wystąpi błąd dla określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak ściśle tekst wejściowy jest zgodny z oryginalnym tekstem znalezionym w sklepie. System dopasowuje zdania wejściowe do magazynu, w tym niedokładne dopasowania. Zwracana wartość waha się od 0 do 100, gdzie 0 nie jest podobieństwo i 100 jest dokładne dopasowanie z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst, który został dopasowany do tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy dopasowany tekst oryginalny różnił się od tekstu wejściowego. Służy do zwracania tekstu źródłowego rozmytego dopasowania. Ta wartość nie jest zwracana dla wyników usługi Microsoft Translator.
* `Rating`: Wskazuje autorytet osoby podejmującej decyzję o jakości. Wyniki tłumaczenia maszynowego mają ocenę 5. Anonimowo dostarczone tłumaczenia mają zazwyczaj ocenę od 1 do 4. Autorytatywnie dostarczone tłumaczenia mają zazwyczaj ocenę od 6 do 10.
* `Count`: Liczba wybranych tłumaczeń z tą oceną. Wartość jest 0 dla automatycznie przetłumaczonej odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.


### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja  |(pusty)    |Wymagane, jeśli `appid` zarówno pole, jak i `Ocp-Apim-Subscription-Key` nagłówek pozostają puste.  Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|Ocp-Apim-Subscription-Key|(pusty)  |Wymagane, jeśli `appid` zarówno pole, jak i `Authorization` nagłówek pozostają puste.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Zła prośba. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzał, poinformuj nas o tym. Proszę podać nam przybliżoną datę & godzinie żądania oraz identyfikator żądania zawarty w `X-MS-Trans-Info`nagłówku odpowiedzi.|
|503    |Usługa tymczasowo niedostępna. Ponów próbę i daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie do interfejsu API tekstu tłumacza w wersji 3](../migrate-to-v3.md)


