---
title: Translator Text API V2.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja interfejsu API tekstu usługi Translator w wersji 2.0.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: 961dd277034db7e5406e671233f26b4fd8fe5f26
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527289"
---
# <a name="translator-text-api-v20"></a>Interfejs API tekstu usługi Translator w wersji 2.0

> [!IMPORTANT]
> Ta wersja interfejsu API tłumaczenia tekstu jest przestarzała. [Wyświetl dokumentację dla wersji 3 interfejsu API tłumaczenia tekstu](v3-0-reference.md).

Interfejsu API tekstu usługi Translator w wersji 2 można bezproblemowo zintegrować aplikacji, witryn sieci Web, narzędzi lub innych rozwiązań, zapewnienie środowisk użytkowników w wielu językach. Wykorzystując standardy branżowe, może służyć na dowolnej platformie sprzętu i z dowolnego systemu operacyjnego, aby wykonać tłumaczenie z języka i inne operacje związane z językiem, takich jak wykrywanie języka tekstu lub zamiany tekstu na mowę. Kliknij tutaj, aby uzyskać więcej informacji na temat interfejsu API w usłudze Translator firmy Microsoft.

## <a name="getting-started"></a>Wprowadzenie
Interfejs API tekstu usługi Translator, konieczne będzie dostęp do [konta na platformie Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autoryzacja
Wszystkie wywołania interfejsu API tłumaczenia tekstu wymaga klucza subskrypcji na potrzeby uwierzytelniania. Interfejs API obsługuje dwa tryby uwierzytelniania:

* Przy użyciu tokenu dostępu. Użyj klucz subskrypcji, do którego odwołuje się **kroku** 9, aby generowała token dostępu, dzięki czemu żądania POST do usługi autoryzacji. Zobacz dokumentację usługi tokenu, aby uzyskać szczegółowe informacje. Przekaż token dostępu w usłudze Translator przy użyciu nagłówka autoryzacji lub access_token parametr zapytania. Token dostępu jest ważny przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i Zachowaj przy użyciu tego samego dostępu tokenu dla powtarzanych żądań w ciągu tych 10 minut.

* Bezpośrednio przy użyciu klucza subskrypcji. Przekaż swój klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` nagłówka dołączone do żądania interfejsu API usługi Translator. W tym trybie nie trzeba wywoływać usługi tokenu uwierzytelniania do generowania tokenu dostępu.

Należy rozważyć swój klucz subskrypcji i token dostępu jako wpisy tajne, które powinny być ukryte w widoku.

## <a name="profanity-handling"></a>Obsługa niestosownych wyrażeń
Zwykle usługa Translator zachowa wulgaryzmów, który znajduje się w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że wyrazy obsceniczne różnią się od kultury, a w wyniku stopień wulgaryzmów w języku docelowym może być większy lub mniejsze.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, niezależnie od obecności wulgaryzmów w tekście źródłowym, można użyć wulgaryzmów opcję dla metod, które go obsługują filtrowania. Opcja można wybrać, czy użytkownik chce zobaczyć wulgaryzmów usunięta lub oznaczona za pomocą odpowiednich tagów lub nie podjęto żadnej akcji. Akceptowane wartości `ProfanityAction` są `NoAction` (ustawienie domyślne), oznaczone i `Deleted`.


|ProfanityAction    |Akcja |Przykład źródłowej (japoński)  |Przykład tłumaczenie (język angielski)  |
|:--|:--|:--|:--|
|NoAction   |Domyślne. Taka sama jak nie Ustawianie opcji. Wulgaryzmów zostaną spełnione ze źródła do docelowego.        |彼はジャッカスです。     |Jest on jackass.   |
|Oznaczone jako     |Wyrazy obsceniczne jest otoczony tagi XML \<wulgaryzmów > i \</profanity >.       |彼はジャッカスです。 |Jest on \<wulgaryzmów > jackass\</profanity >.  |
|Usunięte    |Wyrazy obsceniczne zostaną usunięte z danych wyjściowych bez zastępowania.     |彼はジャッカスです。 |Jest on.   |

    
## <a name="excluding-content-from-translation"></a>Wykluczanie zawartości tłumaczenia
Podczas tłumaczenia zawartości przy użyciu tagów, takich jak HTML (`contentType=text/html`), można też wykluczyć określonej zawartości z tłumaczenia. Mogą używać atrybutu `class=notranslate` do określania zawartości, które powinny pozostać w jego oryginalnym języku. W poniższym przykładzie zawartości w pierwszym `div` element będzie nie można przetłumaczyć podczas zawartości w drugim `div` element, który zostanie zamienione.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET / tłumaczenie

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Tłumaczy tekst z jednego języka do innego.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Wartość zwracana:** Ciąg reprezentujący przetłumaczonego tekstu.

Jeśli wcześniej używano `AddTranslation` lub `AddTranslationArray` wprowadzenia tłumaczenia z oceną 5 lub nowszej, aby jednym zdaniu źródła `Translate` zwraca tylko wybierany często przez dostępnej w systemie. "Jednym zdaniu źródło" oznacza, że dokładnie takie same (dopasowanie 100%), z wyjątkiem wielkość liter, biały znak, wartości tagów i znaków interpunkcyjnych na końcu zdania. Jeśli ocena nie jest przechowywany z oceną, 5 lub nowszym zwrócony wynik będzie automatycznego tłumaczenia przez Microsoft Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

string

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis    |Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji  |(puste)    |Wymagany. Jeśli nagłówek autoryzacji lub Ocp-Apim-Subscription-Key jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający "Bearer" + "" + "access_token".|query|string|
|tekst|(puste)   |Wymagany. Ciąg reprezentujący tekstu do przetłumaczenia. Rozmiar tekstu nie może przekraczać 10 000 znaków.|query|string|
|z|(puste)   |Opcjonalny. Ciąg reprezentujący kod języka tekstu tłumaczenia. Na przykład en w języku angielskim.|query|string|
|na|(puste) |Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.|query|string|
|contentType|(puste)    |Opcjonalny. Format tekst tłumaczony. Obsługiwane formaty to text/plain (ustawienie domyślne) i text/html. Kod HTML musi być elementem pełną, poprawnie sformułowany.|query|string|
|category|(puste)   |Opcjonalny. Ciąg zawierający kategoria tłumaczenia (domena). Wartość domyślna to "general".|query|string|
|Autoryzacja|(puste)  |Wymagane, jeśli pole appid lub nagłówek Ocp-Apim-Subscription-Key nie jest określony. Token autoryzacji:  "Bearer" + " " + "access_token".|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)  |Wymagane, jeśli pole appid lub nagłówka autoryzacji nie jest określony.|nagłówek|string|


### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `TranslateArray` metoda tłumaczenia dla wielu teksty źródła pobierania.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Format treści żądania powinna być następująca:

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

Elementy w ramach `TranslateArrayRequest` są:


* `appid`: Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.
* `from`: Opcjonalny. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu z. Jeśli pole pozostanie puste, to odpowiedź będzie zawierać wynik automatyczne wykrywanie języka.
* `options`: Opcjonalny. `Options` Obiekt zawierający wartości z poniższej listy. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.
    - `Category`: Ciąg zawierający kategoria tłumaczenia (domena). Wartość domyślna to `general`.
    - `ContentType`: Format tekst tłumaczony. Obsługiwane formaty to `text/plain` (ustawienie domyślne), `text/xml` i `text/html`. Kod HTML musi być elementem pełną, poprawnie sformułowany.
    - `ProfanityAction`: Określa sposób obsługi profanities zgodnie z powyższymi wskazówkami. Akceptowane wartości `ProfanityAction` są `NoAction` (ustawienie domyślne), `Marked` i `Deleted`.
    - `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Tę samą zawartość, zostaną zwrócone w odpowiedzi.
    - `Uri`: Filtruj wyniki według tego identyfikatora URI. Wartość domyślna: `all`.
    - `User`: Filtrowanie wyników przez tego użytkownika. Wartość domyślna: `all`.
* `texts`: Wymagany. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być tego samego języka. Suma wszystkich tekstów do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 2000.
* `to`: Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.

Elementy opcjonalne można pominąć. Elementy, które są bezpośrednimi elementami podrzędnymi TranslateArrayRequest musi być wymieniony w kolejności alfabetycznej.

Metoda TranslateArray akceptuje `application/xml` lub `text/xml` dla `Content-Type`.

**Wartość zwracana:** A `TranslateArrayResponse` tablicy. Każdy `TranslateArrayResponse` zawiera następujące elementy:

* `Error`: Wskazuje na błąd, jeśli wystąpił jeden. W przeciwnym razie ustawionej na wartość null.
* `OriginalSentenceLengths`: Tablica liczb całkowitych, wskazujący długość każde zdanie w oryginalny tekst źródłowy. Długość tablicy wskazuje liczbę zdań.
* `TranslatedText`: Przetłumaczony tekst.
* `TranslatedSentenceLengths`: Tablica liczb całkowitych, wskazujący długość każde zdanie w przetłumaczonego tekstu. Długość tablicy wskazuje liczbę zdań.
* `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Zwraca taką samą zawartość, tak jak w żądaniu.

Format treści odpowiedzi jest w następujący sposób.

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

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Odpowiedź oznaczająca Powodzenie zawiera tablicę `TranslateArrayResponse` w formacie opisanym powyżej.

string

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(pusty)) |Wymagane, jeśli pole appid lub nagłówek Ocp-Apim-Subscription-Key nie jest określony. Token autoryzacji:  "Bearer" + " " + "access_token".|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Wymagane, jeśli pole appid lub nagłówka autoryzacji nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP   |Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie. Typowe błędy: <ul><li>Element tablicy nie może być pusta</li><li>Nieprawidłowej kategorii</li><li>Z języka jest nieprawidłowa</li><li>Język jest nieprawidłowy</li><li>Żądanie zawiera zbyt wiele elementów</li><li>Język From nie jest obsługiwany.</li><li>Język To nie jest obsługiwany.</li><li>Przetłumacz żądania ma zbyt dużej ilości danych</li><li>HTML nie jest w niepoprawnym formacie.</li><li>Przekazano zbyt wiele parametrów Przetłumacz żądania</li></ul>|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames WPIS

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera przyjazne nazwy dla języków przekazany jako parametr `languageCodes`i zlokalizowanych przy użyciu języka przekazanych ustawień regionalnych.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Treść żądania zawiera reprezentujący kodów ISO 639-1 języka można pobrać przyjazne nazwy dla tablicy ciągów. Na przykład:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Wartość zwracana:** Tablica ciągów zawierająca nazwy języki obsługiwane przez usługę w usłudze Translator zlokalizowany w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągów zawierająca nazwy języki obsługiwane przez usługę w usłudze Translator zlokalizowany w żądanym języku.

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|ustawienia regionalne|(puste) |Wymagany. Ciąg reprezentujący kombinacji ISO 639 kultury małe dwuliterowych kod skojarzony z języka i kod ISO 3166 przeszczepiania wielkie dwuliterowych można lokalizować nazwy języka lub ISO 639 małych liter, kultura kod samodzielnie.|query|string|
|Autoryzacja|(puste)  |Jeśli wymagane pole appid lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)  |Jeśli wymagane pole appid lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Uzyskaj listę kodów języków reprezentujący języki, które są obsługiwane przez usługi tłumaczenia.  `Translate` i `TranslateArray` może dokonywać translacji między dwoma z tych języków.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Wartość zwracana:** Tablica ciągu, zawierający kodów języków obsługiwanych przez usługi w usłudze Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągu, zawierający kodów języków obsługiwanych przez usługi w usłudze Translator.

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|Autoryzacja|(puste)  |Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera języki, dla których jest dostępna synteza mowy.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Wartość zwracana:** Tablica ciągu, zawierający kodów języków, obsługę synteza mowy w usłudze Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągu, zawierający kodów języków, obsługę synteza mowy w usłudze Translator.

string

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|
 
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401|Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-speak"></a>GET / mowy

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Zwraca wave lub mp3 strumienia tekstu przekazanego mowy w żądanym języku.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Wartość zwracana:** Strumień wave lub mp3 przekazanego tekstu, mowy w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

dane binarne

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|tekst|(puste)   |Wymagany. Ciąg zawierający zdania lub zdania określony język wymawiane dla strumienia wave. Rozmiar tekstu, aby porozmawiać nie może przekraczać 2000 znaków.|query|string|
|language|(puste)   |Wymagany. Ciąg reprezentujący kod obsługiwanego języka, aby porozmawiać z tekstem w. Kod musi być obecne na liście kodów zwrócona przez metodę `GetLanguagesForSpeak`.|query|string|
|format|(puste)|Opcjonalny. Ciąg określający identyfikator typu zawartości. Obecnie `audio/wav` i `audio/mp3` są dostępne. Wartość domyślna to `audio/wav`.|query|string|
|opcje|(puste)    |<ul><li>Opcjonalny. Ciąg określający właściwości syntezatora mowy:<li>`MaxQuality` i `MinSize` są dostępne określić jakość sygnałów audio. Za pomocą `MaxQuality`, możesz uzyskać głosów z najwyższą jakość i `MinSize`, możesz uzyskać głosów z najmniejszego możliwego rozmiaru. Wartość domyślna to `MinSize`.</li><li>`female` i `male` są dostępne określić żądaną płeć głosu. Wartość domyślna to `female`. Użyj pionowy pasek <code>\|</code> obejmujący wiele opcji. Na przykład `MaxQuality|Male`.</li></li></ul> |query|string|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-detect"></a>GET / wykrywania

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `Detect` metodę identyfikowania języka wybranego fragment tekstu.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Wartość zwracana:** Ciąg zawierający dwuznakowy kod języka dla danego tekstu. .

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

string

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)  |Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|tekst|(puste)|Wymagany. Ciąg zawierający tekst, którego język jest zidentyfikowanie. Rozmiar tekstu nie może przekraczać 10 000 znaków.|query| string|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key  |(puste)    |Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|


## <a name="post-detectarray"></a>OPUBLIKUJ /DetectArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `DetectArray` metodę identyfikowania języka tablicy parametrów na raz. Wykonuje wykrywania niezależnie od każdego elementu tablicy poszczególnych i zwraca wynik dla każdego wiersza w tablicy.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Format treści żądania powinna być następująca.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Rozmiar tekstu nie może przekraczać 10 000 znaków.

**Wartość zwracana:** Tablica ciągów zawierająca dwuznakowy kodów języków, dla każdego wiersza tabeli wejściowej.

Format treści odpowiedzi jest w następujący sposób.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
DetectArray zakończyło się pomyślnie. Zwraca tablicę ciągów zawierających dwuznakowy kodów języków, dla każdego wiersza tabeli wejściowej.

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Jeśli wymagane `appid` nie określono pola lub nagłówka autoryzacji.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-addtranslation"></a>Pobierz /AddTranslation

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **OGŁOSZONE JAKO PRZESTARZAŁE UWAGA:** Po 31 stycznia 2018 r. Ta metoda nie będzie akceptować nowych zgłoszenia zdania i otrzymasz komunikat o błędzie. Zapoznaj się z tym ogłoszeniem o zmianach wprowadzonych do współpracy funkcji tłumaczenia.

Dodaje tłumaczenie do pamięci tłumaczeń.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

string

Typ zawartości odpowiedzi: aplikacja: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych   |
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|OriginalText|(puste)|Wymagany. Ciąg zawierający tekst do tłumaczenia z. Ciąg może się składać maksymalnie 1000 znaków.|query|string|
|translatedText|(puste) |Wymagany. Ciąg zawierający przetłumaczonego tekstu w języku docelowym. Ciąg może się składać maksymalnie 2000 znaków.|query|string|
|z|(puste)   |Wymagany. Ciąg reprezentujący kod języka tekstu tłumaczenia. EN = wersja angielska, de = niemieckiego itp...|query|string|
|na|(puste)|Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.|query|string|
|rating|(puste) |Opcjonalny. Liczba całkowita reprezentująca oceny jakości dla tych parametrów. Wartość od -10 do 10. Wartość domyślna to 1.|query|liczba całkowita|
|contentType|(puste)    |Opcjonalny. Format tekst tłumaczony. Obsługiwane formaty to "text/plain" i "text/html". Kod HTML musi być elementem pełną, poprawnie sformułowany.   |query|string|
|category|(puste)|Opcjonalny. Ciąg zawierający kategoria tłumaczenia (domena). Wartość domyślna to "general".|query|string|
|Użytkownik|(puste)|Wymagany. Ciąg używany do śledzenia inicjatorem przesyłania.|query|string|
|identyfikator URI|(puste)|Opcjonalny. Ciąg zawierający lokalizację zawartości to tłumaczenie.|query|string|
|Autoryzacja|(puste)|Jeśli wymagane pole appid lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.    |nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|410|AddTranslation nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-addtranslationarray"></a>OPUBLIKUJ /AddTranslationArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **OGŁOSZONE JAKO PRZESTARZAŁE UWAGA:** Po 31 stycznia 2018 r. Ta metoda nie będzie akceptować nowych zgłoszenia zdania i otrzymasz komunikat o błędzie. Zapoznaj się z tym ogłoszeniem o zmianach wprowadzonych do współpracy funkcji tłumaczenia.

Dodaje tablicę tłumaczenia, Dodaj więcej pamięci tłumaczeń. To jest wersja tablicy `AddTranslation`.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Format treści żądania jest w następujący sposób.

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

Elementy w elemencie AddtranslationsRequest to:

* `AppId`: Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.
* `From`: Wymagany. Ciąg zawierający kod języka języka źródłowego. Musi być jednym z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `To`: Wymagany. Ciąg zawierający kod języka w języku docelowym. Musi być jednym z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `Translations`: Wymagany. Tablica tłumaczeń, aby dodać do pamięci tłumaczeń. Każde tłumaczenie musi zawierać: originalText, translatedText i oceny. Rozmiar każdego originalText i translatedText jest ograniczone do 1000 znaków. Łączna liczba wszystkich originalText(s) i translatedText(s) nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 100.
* `Options`: Wymagany. Zestaw opcji, w tym kategorii, ContentType, identyfikator Uri i użytkownika. Użytkownik jest wymagany. Kategoria, typ zawartości i identyfikatora Uri są opcjonalne. Musi być wymieniony określonych elementów w kolejności alfabetycznej.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Metoda AddTranslationArray zakończyło się pomyślnie. Po 31 stycznia 2018 r. nie będą akceptowane zgłoszenia zdania. Usługa udzieli odpowiedzi z kodem błędu 410.

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(puste)|Wymagane, jeśli pole appid lub nagłówek Ocp-Apim-Subscription-Key nie jest określony. Token autoryzacji:  "Bearer" + " " + "access_token".|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Wymagane, jeśli pole appid lub nagłówka autoryzacji nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|410    |AddTranslation nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="get-breaksentences"></a>Pobierz /BreakSentences

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Dzieli fragment tekstu na zdania i zwraca tablicę zawierającą długości w każdym zdaniu.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Wartość zwracana:** Tablica liczb całkowitych reprezentujących długości zdania. Długość tablicy jest liczba zdania, a wartości są długość każdego zdania.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica liczb całkowitych reprezentujących długości zdania. Długość tablicy jest liczba zdania, a wartości są długość każdego zdania.

liczba całkowita

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)  |Wymagany. Jeśli nagłówek autoryzacji lub Ocp-Apim-Subscription-Key jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający "Bearer" + "" + "access_token".|query| string|
|tekst|(puste)   |Wymagany. Ciąg reprezentujący tekst, który ma podzielić na zdania. Rozmiar tekstu nie może przekraczać 10 000 znaków.|query|string|
|language   |(puste)    |Wymagany. Ciąg reprezentujący kod języka tekstu wejściowego.|query|string|
|Autoryzacja|(puste)|Wymagane, jeśli pole appid lub nagłówek Ocp-Apim-Subscription-Key nie jest określony. Token autoryzacji:  "Bearer" + " " + "access_token".    |nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)|Wymagane, jeśli pole appid lub nagłówka autoryzacji nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401|Nieprawidłowe poświadczenia|
|500|Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam przybliżony daty i godziny, żądania i odpowiedzi nagłówka X-MS-Trans-Info Identyfikatora żądania.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera tablicę tłumaczenia dla danego języka parę z aparatu MT i magazyn. GetTranslations różni się od Przetłumacz, ponieważ zwraca ono wszystkie dostępne tłumaczenia.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Treść żądania zawiera opcjonalne obiektu TranslationOptions, który ma następujący format.

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

`TranslateOptions` Obiekt zawiera wartości wymienione poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.

* `Category`: Ciąg zawierający kategoria tłumaczenia (domena). Wartość domyślna to "general".
* `ContentType`: Obsługiwana jest tylko, a wartość domyślna opcja to "text/plain".
* `IncludeMultipleMTAlternatives`: flagę logiczną, aby określić, czy więcej niż jeden alternatywy ma zostać zwrócone z aparatu MT. Prawidłowe wartości to true i false (z uwzględnieniem wielkości liter). Domyślny ma wartość FAŁSZ i zawiera tylko 1 alternatywą. Ustawienie flagi na wartość true umożliwia generowanie sztuczne alternatywy w tłumaczeniu, w pełni zintegrowane z architekturą współpracy tłumaczenia (CTF). Ta funkcja umożliwia zwracanie alternatywy dla zdań, które mają nie alternatywy w CTF, dodając sztuczne alternatywy z listy n najlepszych dekodera.
    - Klasyfikacje klasyfikacje są stosowane w następujący sposób: (1) najlepsze automatycznego tłumaczenia ma klasyfikację od 5. (2) alternatyw z CTF odzwierciedlają urzędowi recenzenta, od -10 do + 10. (3) alternatyw automatycznie generowanych tłumaczenia (n najlepszych) mają ma klasyfikację od 0 i stopnia dopasowania 100.
    - Liczba rozwiązań alternatywnych, liczba zwróconych alternatywy zależy od maxTranslations, ale może być mniejsza.
    - Kierunki ta funkcja nie jest dostępna w poszukiwaniu tłumaczeń między uproszczony i chiński tradycyjny, chiński obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
* `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Tę samą zawartość, zostaną zwrócone w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** Format odpowiedzi to w następujący sposób.

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

Obejmuje to `GetTranslationsResponse` element zawierający następujące wartości:

* `Translations`: Znaleziono tablicę dopasowań, przechowywane w obiektach TranslationMatch (patrz poniżej). Tłumaczenia mogą obejmować nieznaczne warianty oryginalny tekst (dopasowywania rozmytego). Będą sortowane tłumaczenia: 100% najpierw pasuje do dopasowania rozmyte poniżej.
* `From`: Jeśli metoda nie określiła języka From, będzie to wynikiem automatyczne wykrywanie języka. W przeciwnym razie zostanie ona nadana od języka.
* `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Zawiera taką samą wartość jak podanej w parametrze TranslateOptions.

Obiekt TranslationMatch składa się z następujących czynności:

* `Error`: Jeśli wystąpił błąd w poszukiwaniu określonego ciągu wejściowego, znajduje się kod błędu. W przeciwnym razie to pole jest puste.
* `MatchDegree`: System dopasowuje zdania wejściowe względem magazynu, w tym niedokładny dopasowań.  MatchDegree wskazuje, jak blisko tekst wejściowy pasuje oryginalny tekst, znaleziono w magazynie. Wartość zwracana zakresy z zakresu od 0 do 100, gdzie 0 oznacza nie podobieństwa i 100 jest identyczny z uwzględnieniem wielkości liter.
MatchedOriginalText: Oryginalny tekst dopasowanym dla tego wyniku. Zwracany tylko wtedy, gdy dopasowany tekst oryginalnego różnił się od tekstu wejściowego. Używane do zwracania tekst źródłowy dopasowywania rozmytego. Nie zwrócono dla wyników Microsoft Translator.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego ma klasyfikację od 5. Anonimowo podana tłumaczenia zwykle obejmuje ma klasyfikację od 1 do 4, przy jednoczesnym autorytatywnie podana tłumaczenia zwykle obejmuje ma klasyfikację od 6 do 10.
* `Count`: Liczba przypadków, gdy wybrano to tłumaczenie z tą klasyfikacją. Wartość będzie równa 0, automatycznie przetłumaczone odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
A `GetTranslationsResponse` obiekt w formacie opisanym powyżej.

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówek jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|string|
|tekst|(puste)|Wymagany. Ciąg reprezentujący tekstu do przetłumaczenia. Rozmiar tekstu nie może przekraczać 10 000 znaków.|query|string|
|z|(puste)|Wymagany. Ciąg reprezentujący kod języka tekstu tłumaczenia.|query|string|
|na |(puste)    |Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.|query|string|
|maxTranslations|(puste)|Wymagany. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.|query|liczba całkowita|
|Autoryzacja| (puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|string| nagłówek|
|OCP-Apim-Subscription-Key|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `GetTranslationsArray` metoda wielu kandydatów tłumaczenia na wiele tekstów źródła pobierania.

Identyfikator URI żądania jest `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Format treści żądania jest w następujący sposób.

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

* `AppId`: Wymagany. Jeśli nagłówek autoryzacji jest używany, pozostaw to pole puste appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.
* `From`: Wymagany. Ciąg reprezentujący kod języka tekstu tłumaczenia.
* `MaxTranslations`: Wymagany. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.
* `Options`: Opcjonalny. Obiekt opcji, który zawiera wartości wymienione poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Musi być wymieniony określonych elementów w kolejności alfabetycznej.
    - Kategoria ": Ciąg zawierający kategoria tłumaczenia (domena). Wartość domyślna to ogólne.
    - `ContentType`: Obsługiwana jest tylko, a wartość domyślna jest opcja text/plain.
    - `IncludeMultipleMTAlternatives`: flagę logiczną, aby określić, czy więcej niż jeden alternatywy ma zostać zwrócone z aparatu MT. Prawidłowe wartości to true i false (z uwzględnieniem wielkości liter). Domyślny ma wartość FAŁSZ i zawiera tylko 1 alternatywą. Ustawienie flagi na wartość true umożliwia generowanie sztuczne alternatywy w tłumaczeniu, w pełni zintegrowane z architekturą współpracy tłumaczenia (CTF). Ta funkcja umożliwia zwracanie alternatywy dla zdań, które mają nie alternatywy w CTF, dodając sztuczne alternatywy z listy n najlepszych dekodera.
        - Klasyfikacje klasyfikacje są stosowane w następujący sposób: (1) najlepsze automatycznego tłumaczenia ma klasyfikację od 5. (2) alternatyw z CTF odzwierciedlają urzędowi recenzenta, od -10 do + 10. (3) alternatyw automatycznie generowanych tłumaczenia (n najlepszych) mają ma klasyfikację od 0 i stopnia dopasowania 100.
        - Liczba rozwiązań alternatywnych, liczba zwróconych alternatywy zależy od maxTranslations, ale może być mniejsza.
        - Kierunki ta funkcja nie jest dostępna w poszukiwaniu tłumaczeń między uproszczony i chiński tradycyjny, chiński obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
* `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Tę samą zawartość, zostaną zwrócone w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `Texts`: Wymagany. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być tego samego języka. Suma wszystkich tekstów do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 10.
* `To`: Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.

Elementy opcjonalne można pominąć. Elementy, które są bezpośrednie elementy podrzędne `GetTranslationsArrayRequest` muszą być wymienione w kolejności alfabetycznej.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** Format odpowiedzi to w następujący sposób.

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

* `Translations`: Znaleziono tablicę dopasowań, przechowywane w `TranslationMatch` obiektów (patrz poniżej). Tłumaczenia mogą obejmować nieznaczne warianty oryginalny tekst (dopasowywania rozmytego). Będą sortowane tłumaczenia: 100% najpierw pasuje do dopasowania rozmyte poniżej.
* `From`: Jeśli nie określono metody `From` języka, będzie to wynikiem automatyczne wykrywanie języka. W przeciwnym razie zostanie ona nadana od języka.
* `State`: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Zawiera taką samą wartość jak podano `TranslateOptions` parametru.

`TranslationMatch` Obiekt składa się z następujących czynności:
* `Error`: Jeśli wystąpił błąd w poszukiwaniu określonego ciągu wejściowego, znajduje się kod błędu. W przeciwnym razie to pole jest puste.
* `MatchDegree`: System dopasowuje zdania wejściowe względem magazynu, w tym niedokładny dopasowań.  `MatchDegree` Wskazuje, jak blisko tekst wejściowy pasuje oryginalny tekst, znaleziono w magazynie. Wartość zwracana zakresy z zakresu od 0 do 100, gdzie 0 oznacza nie podobieństwa i 100 jest identyczny z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst dopasowanym dla tego wyniku. Zwracany tylko wtedy, gdy dopasowany tekst oryginalnego różnił się od tekstu wejściowego. Używane do zwracania tekst źródłowy dopasowywania rozmytego. Nie zwrócono dla wyników Microsoft Translator.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego ma klasyfikację od 5. Anonimowo podana tłumaczenia zwykle obejmuje ma klasyfikację od 1 do 4, przy jednoczesnym autorytatywnie podana tłumaczenia zwykle obejmuje ma klasyfikację od 6 do 10.
* `Count`: Liczba przypadków, gdy wybrano to tłumaczenie z tą klasyfikacją. Wartość będzie równa 0, automatycznie przetłumaczone odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.


### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

string

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja  |(puste)    |Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówka nie jest określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|string|
|OCP-Apim-Subscription-Key|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówka nie jest określony.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe oraz odpowiedzi szczegółowy komunikat o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżony datę i czas żądania i identyfikator żądania zawarte w nagłówku odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Ponów próbę i Daj nam znać, jeśli błąd się powtarza.|

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Migrowanie do API tekstu usługi Translator w wersji 3](../migrate-to-v3.md)










