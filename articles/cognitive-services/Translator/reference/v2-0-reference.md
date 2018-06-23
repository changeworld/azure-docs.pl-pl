---
title: Dokumentacja V2.0 tekst interfejsu API Microsoft Translator | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Dokumentacji interfejsu API w wersji 2.0 Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349384"
---
# <a name="translator-text-api-v20"></a>Translatora v2.0 tekst interfejsu API

> [!IMPORTANT]
> Ta wersja interfejsu API tekst Translator jest przestarzała. [Dokumentacja v3 interfejsu API tekst Translator](v3-0-reference.md).

Interfejs API Microsoft Translator V2 tekst można bezproblemowo zintegrować aplikacji, witryn sieci Web, narzędzia lub innych rozwiązań w celu udostępnienia możliwości użytkowników obsługi wielu języków. Wykorzystanie standardy branżowe, może służyć na dowolnej platformie sprzętu i z dowolnego systemu operacyjnego można wykonywać tłumaczenie z języka i inne operacje związane z językiem, takie jak wykrywania języka tekst lub tekst na mowę. Kliknij tutaj, aby uzyskać więcej informacji o interfejsie API Translator firmy Microsoft.

## <a name="getting-started"></a>Wprowadzenie
Translator tekst interfejs API Microsoft konieczne będzie dostęp do [tworzenia konta platformy Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autoryzacja
Wszystkie wywołania interfejsu API usługi Microsoft Translator tekst wymagają klucza subskrypcji do uwierzytelniania. Interfejs API obsługuje dwa tryby uwierzytelniania:

* Przy użyciu tokenu dostępu. Za pomocą klawisza subskrypcji, do którego odwołuje się **krok** 9 do generowania tokenu dostępu poprzez wysłanie żądania POST do usługi autoryzacji. Zobacz szczegółowe informacje można znaleźć w dokumentacji usługi tokenu. Przekaż token dostępu z usługą Translator przy użyciu nagłówek autoryzacji lub parametr zapytania ' access_token '. Token dostępu jest ważny przez 10 minut. Uzyskać nowy token dostępu co 10 minut, a także zapewnić za pomocą dostępu do tego samego tokenu do ponownego żądania w ciągu tych 10 minut.

* Bezpośrednio przy użyciu klucza subskrypcji. Przekazać swój klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` dołączany do żądania interfejsu API Translator nagłówka. W tym trybie nie trzeba wywołać usługę token uwierzytelniania do generowania tokenu dostępu.

Jako klucze tajne, które powinny być ukrywane, należy rozważyć klucz subskrypcji i tokenu dostępu.

## <a name="profanity-handling"></a>Obsługa niestosownych wyrażeń
Zazwyczaj usługa translatora zachowują niestosownych wyrażeń, który znajduje się w źródle w tłumaczenia. Stopień niestosownych wyrażeń i kontekst sprawia, że słowa profanujących różnią się od kultury, a w związku z tym stopień niestosownych wyrażeń w języku docelowym może być rozszerzone lub zmniejszenie.

Jeśli chcesz uniknąć pobierania niestosownych wyrażeń w tłumaczeniu, niezależnie od obecności niestosownych wyrażeń w tekst źródłowy służy niestosownych wyrażeń, filtrowania opcji dla metod, które ją obsługują. Opcję można wybrać, czy mają być wyświetlane niestosownych wyrażeń usunięty lub oznaczonych odpowiednich tagów lub nie podjęto żadnej akcji. Zaakceptowane wartości `ProfanityAction` są `NoAction` (ustawienie domyślne), oznaczone i `Deleted`.


|ProfanityAction    |Akcja |Przykład źródła (wersja japońska)  |Przykład tłumaczenia (angielski)  |
|:--|:--|:--|:--|
|NoAction   |Domyślne. Taka sama jak nie ustawienie opcji. Niestosownych wyrażeń przechodzą ze źródła do docelowego.        |彼はジャッカスです。     |Jest on jackass.   |
|Oznaczone     |Wyrazy profanujących będzie otoczona tagi XML <profanity> i </profanity>.     |彼はジャッカスです。 |Jest on <profanity>jackass</profanity>.    |
|Usunięte    |Wyrazy profanujących zostaną usunięte z danych wyjściowych bez zastępowania.     |彼はジャッカスです。 |Jest on.   |

    
## <a name="excluding-content-from-translation"></a>Wykluczanie zawartości tłumaczenia
Podczas tłumaczenia zawartości za pomocą tagów, takich jak HTML (`contentType=text/html`), można też wykluczyć określone z tłumaczenia. Można użyć atrybutu `class=notranslate` do określenia zawartości, które powinny pozostać w oryginalnej wersji językowej. W poniższym przykładzie zawartości wewnątrz pierwszy `div` element zostanie nie można przetłumaczyć podczas zawartości w ciągu sekundy `div` element zostanie zamieniona.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>Pobierz / tłumaczenia

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Wykonuje translację ciąg tekstowy z jednego języka do innego.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Wartość zwracana:** ciąg reprezentujący przetłumaczony tekst.

Jeśli poprzednio korzystano `AddTranslation` lub `AddTranslationArray` wprowadzenia tłumaczenia z klasyfikacją 5 lub nowszego dla tego samego źródła zdania `Translate` zwraca tylko najwyższego pozycję, która jest dostępna w systemie. "Tego samego źródła zdania" oznacza dokładnie takie same (100% zgodny), z wyjątkiem użycia wielkich liter, biały znak wartości tagów i znaków interpunkcyjnych na końcu zdania. Jeśli ocena nie jest przechowywany z klasyfikacją 5 lub nowszym zwrócony wynik będzie automatyczne tłumaczenie przez Microsoft Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis    |Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji  |(puste)    |Wymagany. Jeśli używany jest nagłówek autoryzacji lub Ocp-Apim-subskrypcji-klucza, pozostaw puste pole appid else obejmują ciąg zawierający "Bearer" + "" + "' access_token '".|query|ciąg|
|tekst|(puste)   |Wymagany. Ciąg reprezentujący tekst do tłumaczenia. Rozmiar tekstu nie może przekroczyć 10 000 znaków.|query|ciąg|
|z|(puste)   |Opcjonalny. Ciąg reprezentujący kod języka tłumaczenie tekstu. Na przykład en dla języka angielskiego.|query|ciąg|
|na|(puste) |Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.|query|ciąg|
|Typ zawartości|(puste)    |Opcjonalny. Format tekstu tłumaczonym. Obsługiwane formaty to zwykły tekst (ustawienie domyślne) i tekst i html. Kod HTML musi być elementem pełną, poprawnie sformułowany.|query|ciąg|
|category|(puste)   |Opcjonalny. Ciąg zawierający kategorii (domena) tłumaczenia. Wartość domyślna to "Ogólne".|query|ciąg|
|Autoryzacja|(puste)  |Wymagane, jeśli pole appid lub nagłówka Ocp-Apim-subskrypcji — klucz nie jest określony. Token autoryzacji: "Bearer" + "" + "' access_token '".|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)  |Wymagane, jeśli pole appid lub nagłówek autoryzacji nie jest określony.|nagłówek|ciąg|


### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `TranslateArray` metoda pobierania tłumaczenia dla wielu teksty źródła.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Format treści żądania powinien być następujący:

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

Elementy wewnątrz `TranslateArrayRequest` są:


* `appid`: Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.
* `from`: Opcjonalne. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu z. Jeśli pole pozostanie puste, to odpowiedź będzie zawierać wynik automatycznego wykrywania języka.
* `options`: Opcjonalne. `Options` Obiektu, który zawiera wartości wymienionych poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Określonych elementów musi być wymienione w kolejności alfabetycznej.
    - `Category`: Ciąg zawierający kategorii (domena) tłumaczenia. Domyślnie `general`.
    - `ContentType`: Format tekstu tłumaczonym. Obsługiwane formaty to `text/plain` (ustawienie domyślne), `text/xml` i `text/html`. Kod HTML musi być elementem pełną, poprawnie sformułowany.
    - `ProfanityAction`: Określa sposób obsługi profanities zgodnie z powyższymi wskazówkami. Zaakceptowane wartości `ProfanityAction` są `NoAction` (ustawienie domyślne), `Marked` i `Deleted`.
    - `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Tę samą zawartość zostanie zwrócony w odpowiedzi.
    - `Uri`: Filtrowanie wyników przez ten identyfikator URI. Wartość domyślna: `all`.
    - `User`: Filtrowanie wyników przez tego użytkownika. Wartość domyślna: `all`.
* `texts`: Wymagane. Tablica zawierająca teksty translacji. Wszystkie ciągi muszą być tego samego języka. Suma wszystkich teksty do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 2000.
* `to`: Wymagane. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.

Opcjonalne elementy można pominąć. Elementy, które są bezpośrednimi elementami podrzędnymi TranslateArrayRequest musi być wymienione w kolejności alfabetycznej.

Metoda TranslateArray przyjmuje `application/xml` lub `text/xml` dla `Content-Type`.

**Wartość zwracana:** A `TranslateArrayResponse` tablicy. Każdy `TranslateArrayResponse` ma następujące elementy:

* `Error`: Wskazuje błąd, jeśli wystąpił jeden. W przeciwnym razie wartość null.
* `OriginalSentenceLengths`: Tablica wartości całkowitych wskazujących długość każdej zdanie w oryginalny tekst źródłowy. Długość tablicy wskazuje liczbę zdań.
* `TranslatedText`: Przetłumaczony tekst.
* `TranslatedSentenceLengths`: Tablica wartości całkowitych wskazujących długość każdej zdanie w przetłumaczony tekst. Długość tablicy wskazuje liczbę zdań.
* `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Zwraca tę samą zawartość w żądaniu.

Format treść odpowiedzi jest następująca:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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
Odpowiedź oznaczająca Powodzenie zawiera tablicę `TranslateArrayResponse` w formacie opisane powyżej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(pusta)) |Wymagane, jeśli pole appid lub nagłówka Ocp-Apim-subskrypcji — klucz nie jest określony. Token autoryzacji: "Bearer" + "" + "' access_token '".|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Wymagane, jeśli pole appid lub nagłówek autoryzacji nie jest określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP   |Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie. Typowe błędy: <ul><li>Element tablicy nie może być pusta</li><li>Nieprawidłowy kategorii</li><li>Z języka jest nieprawidłowa</li><li>Język jest nieprawidłowy</li><li>Żądanie zawiera zbyt wiele elementów</li><li>Język From nie jest obsługiwany.</li><li>Języka aby nie jest obsługiwana.</li><li>Przetłumacz żądania ma za dużo danych</li><li>HTML nie znajduje się w poprawnym formacie</li><li>Przekazano zbyt wiele parametrów Przetłumacz żądania</li></ul>|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera przyjazne nazwy dla języków przekazany jako parametr `languageCodes`i zlokalizowany przy użyciu języka przekazany ustawień regionalnych.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Treść żądania zawiera reprezentujący kody 639 1 języka ISO można pobrać przyjazne nazwy dla tablicy ciągów. Na przykład:

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Wartość zwracana:** tablica ciągów zawierająca nazwy języki obsługiwane przez usługę Translator w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągów zawierająca nazwy języki obsługiwane przez usługę Translator w żądanym języku.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|Ustawienia regionalne|(puste) |Wymagany. Ciąg reprezentujący kombinację ISO 639 kultury małe dwuliterowych kod związane z językiem i kod przeszczepiania wielkie dwuliterowych normy ISO 3166 do zlokalizowania nazwy języka lub ISO 639 małe kultury kod samodzielnie.|query|ciąg|
|Autoryzacja|(puste)  |Jeśli wymagane pole appid lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)  |Jeśli wymagane pole appid lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-getlanguagesfortranslate"></a>Pobierz /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Uzyskaj listę kodów języków reprezentujący języki, które są obsługiwane przez usługi tłumaczenia.  `Translate` i `TranslateArray` może dokonywać translacji między dwoma tych języków.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Wartość zwracana:** tablica ciągów zawierająca kodów języków obsługiwanych przez usługi translatora.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągów zawierająca kodów języków obsługiwanych przez usługi translatora.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|Autoryzacja|(puste)  |Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-getlanguagesforspeak"></a>Pobierz /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera języki, dla których jest dostępna synteza mowy.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Wartość zwracana:** tablica ciągów zawierająca kodów języków obsługuje syntezy mowy usługi translatora.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica ciągów zawierająca kodów języków obsługuje syntezy mowy usługi translatora.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|
 
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401|Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-speak"></a>Pobierz / mowy

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Zwraca wave lub mp3 strumienia tekstu przekazany w jest używany w żądanym języku.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Wartość zwracana:** wave lub mp3 strumienia tekstu przekazany w jest używany w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

dane binarne

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|tekst|(puste)   |Wymagany. Ciąg zawierający zdania lub zdań określonego języka wymawiane dla strumienia typu wave. Rozmiar tekstu do mowy nie może przekraczać 2000 znaków.|query|ciąg|
|język|(puste)   |Wymagany. Ciąg reprezentujący kod obsługiwanego języka Aby skontaktować się z tekstem w. Kod musi być obecny na liście kodów zwróconych przez metodę `GetLanguagesForSpeak`.|query|ciąg|
|Format|(puste)|Opcjonalny. Ciąg określający identyfikator typu zawartości. Obecnie `audio/wav` i `audio/mp3` są dostępne. Wartość domyślna to `audio/wav`.|query|ciąg|
|opcje|(puste)    |<ul><li>Opcjonalny. Ciąg określający właściwości syntezatora mowy:<li>`MaxQuality` i `MinSize` są dostępne określić jakość sygnałów audio. Z `MaxQuality`, możesz uzyskać głosów z najwyższą jakość i `MinSize`, możesz uzyskać głosów z najmniejszy rozmiar. Domyślnie jest `MinSize`.</li><li>`female` i `male` są dostępne określić żądaną płci głosu. Wartość domyślna to `female`. Użyj pionowy pasek "|` to include multiple options. For example  `MaxQuality|Męskiego ".</li></li></ul> |query|ciąg|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-detect"></a>Pobierz / wykrywania

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `Detect` metodę identyfikowania język wybrany fragment tekstu.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Wartość zwracana:** ciąg zawierający dwa znakowy kod języka dla danego tekstu. .

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)  |Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|tekst|(puste)|Wymagany. Ciąg zawierający tekst, którego język jest zidentyfikowanie. Rozmiar tekstu nie może przekroczyć 10 000 znaków.|query| ciąg|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz  |(puste)    |Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `DetectArray` metodę identyfikowania język tablicy parametrów na raz. Wykonuje niezależne wykrywanie każdy element tablicy poszczególnych i zwraca wynik dla każdego wiersza tabeli.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Powinien być następujący formacie treści żądania.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Rozmiar tekstu nie może przekroczyć 10 000 znaków.

**Wartość zwracana:** tablica ciągów zawierająca języka dwóch znaków kodów dla każdego wiersza wartości tablicy wejściowej.

Format treść odpowiedzi jest następująca:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
DetectArray zakończyło się pomyślnie. Zwraca tablicę ciągów zawierających znak dwóch kodów języków dla każdego wiersza wartości tablicy wejściowej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|Autoryzacja|(puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Jeśli wymagane `appid` nie określono pola lub nagłówek autoryzacji.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-addtranslation"></a>Pobierz /AddTranslation

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **AMORTYZACJA Uwaga:** po 31 stycznia 2018 ta metoda nie przyjmuje nowych dokumentów zdania, a zostanie wyświetlony komunikat o błędzie. Zapoznaj się z tego powiadomienia o zmianach funkcje współpracy.

Dodaje tłumaczenie do tłumaczenia pamięci.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

ciąg

Typ zawartości odpowiedzi: aplikacji: xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych   |
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|OriginalText|(puste)|Wymagany. Ciąg zawierający tekst, który wykonuje z. Ten ciąg może zawierać maksymalnie 1000 znaków.|query|ciąg|
|TranslatedText|(puste) |Wymagany. Ciąg zawierający przetłumaczone tekstu w języku docelowym. Ten ciąg może zawierać maksymalnie 2000 znaków.|query|ciąg|
|z|(puste)   |Wymagany. Ciąg reprezentujący kod języka tłumaczenie tekstu. EN = angielski, de = niemiecki itp...|query|ciąg|
|na|(puste)|Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.|query|ciąg|
|rating|(puste) |Opcjonalny. Liczba całkowita reprezentująca ocena jakości dla tych parametrów. Wartości od -10 do 10. Wartość domyślna to 1.|query|liczba całkowita|
|Typ zawartości|(puste)    |Opcjonalny. Format tekstu tłumaczonym. Obsługiwane formaty to "text/plain" i "text/html". Kod HTML musi być elementem pełną, poprawnie sformułowany.   |query|ciąg|
|category|(puste)|Opcjonalny. Ciąg zawierający kategorii (domena) tłumaczenia. Wartość domyślna to "Ogólne".|query|ciąg|
|Użytkownik|(puste)|Wymagany. Ciąg używany do śledzenia inicjatorem przesyłania.|query|ciąg|
|identyfikator URI|(puste)|Opcjonalny. Ciąg zawierający lokalizacji zawartości tego tłumaczenia.|query|ciąg|
|Autoryzacja|(puste)|Jeśli wymagane pole appid lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.    |nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|410|AddTranslation nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji

> [!IMPORTANT]
> **AMORTYZACJA Uwaga:** po 31 stycznia 2018 ta metoda nie przyjmuje nowych dokumentów zdania, a zostanie wyświetlony komunikat o błędzie. Zapoznaj się z tego powiadomienia o zmianach funkcje współpracy.

Dodaje tablicę tłumaczenia, Dodaj więcej pamięci tłumaczenia. To jest wersja tablicy `AddTranslation`.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Format treści żądania ma następującą składnię.

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

Elementy w elemencie AddtranslationsRequest są:

* `AppId`: Wymagane. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.
* `From`: Wymagane. Ciąg zawierający kod języka języka źródłowego. Musi to być jeden z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `To`: Wymagane. Ciąg zawierający kod języka w języku docelowym. Musi to być jeden z języków zwrócony przez `GetLanguagesForTranslate` metody.
* `Translations`: Wymagane. Tablica tłumaczeń można dodać do tłumaczenia pamięci. Każdy tłumaczenia musi zawierać: originalText, translatedText i klasyfikacji. Rozmiar każdej originalText i translatedText jest ograniczona do 1000 znaków. Suma originalText(s) i translatedText(s) nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 100.
* `Options`: Wymagane. Zestaw opcji, w tym kategorii, ContentType identyfikatora Uri i użytkownika. Użytkownika jest wymagane. Kategoria, ContentType oraz identyfikatora Uri są opcjonalne. Określonych elementów musi być wymienione w kolejności alfabetycznej.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Metoda AddTranslationArray zakończyło się pomyślnie. Po 31 stycznia 2018 zdań przesłanych nie będą akceptowane. Usługa będzie odpowiadać z kodem błędu 410.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|(puste)|Wymagane, jeśli pole appid lub nagłówka Ocp-Apim-subskrypcji — klucz nie jest określony. Token autoryzacji: "Bearer" + "" + "' access_token '".|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Wymagane, jeśli pole appid lub nagłówek autoryzacji nie jest określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|410    |AddTranslation nie jest już obsługiwana.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="get-breaksentences"></a>Pobierz /BreakSentences

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Dzieli fragment tekstu na zdań i zwraca tablicę zawierającą długości w każdym zdaniu.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Wartość zwracana:** tablica liczb całkowitych reprezentujący długości zdań. Długość tablicy jest liczbę zdań i wartości są długość każdego zdania.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
Tablica liczb całkowitych reprezentujący długości zdań. Długość tablicy jest liczbę zdań i wartości są długość każdego zdania.

liczba całkowita

Typ zawartości odpowiedzi: aplikacja/xml 

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)  |Wymagany. Jeśli używany jest nagłówek autoryzacji lub Ocp-Apim-subskrypcji-klucza, pozostaw puste pole appid else obejmują ciąg zawierający "Bearer" + "" + "' access_token '".|query| ciąg|
|tekst|(puste)   |Wymagany. Ciąg reprezentujący tekst, który ma podzielić na zdań. Rozmiar tekstu nie może przekroczyć 10 000 znaków.|query|ciąg|
|język   |(puste)    |Wymagany. Ciąg reprezentujący kod języka wprowadzania tekstu.|query|ciąg|
|Autoryzacja|(puste)|Wymagane, jeśli pole appid lub nagłówka Ocp-Apim-subskrypcji — klucz nie jest określony. Token autoryzacji: "Bearer" + "" + "' access_token '".    |nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Wymagane, jeśli pole appid lub nagłówek autoryzacji nie jest określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401|Nieprawidłowe poświadczenia|
|500|Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj przybliżoną datę i godzinę, żądania i identyfikator żądania odpowiedzi nagłówka X-MS-transakcji — informacje.|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Pobiera tablicę tłumaczenia dla danego języka pary z aparatu MT i magazynu. GetTranslations różni się od Przetłumacz, ponieważ zwraca ono wszystkie dostępne tłumaczenia.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

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

`TranslateOptions` Obiektu zawiera wartości wymienionych poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Określonych elementów musi być wymienione w kolejności alfabetycznej.

* `Category`: Ciąg zawierający kategorii (domena) tłumaczenia. Wartość domyślna to "Ogólne".
* `ContentType`: To jedyny obsługiwany, a wartość domyślna jest opcja "text/plain".
* `IncludeMultipleMTAlternatives`: Flaga wartości logicznej, aby określić, czy alternatyw więcej niż jeden powinny być zwrócone z aparatu MT. Prawidłowe wartości to PRAWDA i FAŁSZ (z uwzględnieniem wielkości liter). Domyślna ma wartość false i zawiera tylko 1 alternatywne. Ustawienie flagi na wartość true umożliwia generowanie alternatyw sztuczne w tłumaczeniu, w pełni zintegrowana z architekturą współpracy tłumaczenia (CTF). Funkcja umożliwia zwracanie alternatyw zdania, które mają nie alternatyw w CTF, dodając sztuczne alternatyw z listy najlepszych n dekodera.
    - Klasyfikacje klasyfikacje są stosowane w następujący sposób: 1) najlepsze automatyczne tłumaczenie ma klasyfikację 5. 2) alternatywami z CTF odzwierciedlają urząd recenzenta, od -10 do + 10. 3) alternatywami automatycznie generowanych tłumaczenia (n najlepszy) mają klasyfikacji 0 i stopień dopasowania 100.
    - Liczba rozwiązań alternatywnych, liczba zwróconych alternatyw zależy od maxTranslations, ale może być mniejsza.
    - Kierunki ta funkcja nie jest dostępny do tłumaczenia między uproszczony i chiński tradycyjny, obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
* `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Tę samą zawartość zostanie zwrócony w odpowiedzi.
* `Uri`: Filtrowanie wyników przez ten identyfikator URI. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** format odpowiedzi ma następującą składnię.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Dotyczy to również `GetTranslationsResponse` elementu zawierającego następujące wartości:

* `Translations`: Tablica odpowiada znaleziono przechowywanych w obiekty TranslationMatch (patrz poniżej). Tłumaczenia mogą obejmować nieznaczne wariantów oryginalny tekst (dopasowywania rozmytego). Tłumaczeń będą sortowane: 100% zgodny najpierw rozmytego dopasowań poniżej.
* `From`: Jeśli metoda nie określono język From, będzie wynik automatycznego wykrywania języka. W przeciwnym razie zostanie nadana od języka.
* `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Zawiera tę samą wartość, jak podano w parametrze TranslateOptions.

Obiekt TranslationMatch składa się z następujących czynności:

* `Error`: Jeśli wystąpił błąd dla określonego ciągu wejściowego, znajduje się kod błędu. W przeciwnym razie pole jest puste.
* `MatchDegree`: System pasuje wejściowe zdań przed magazyn, w tym niedokładnymi dopasowaniami.  MatchDegree wskazuje, jak blisko tekstu wejściowego odpowiada oryginalny tekst został znaleziony w magazynie. Wartość zwracana zakresów z zakresu od 0 do 100, gdzie 0 to nie podobieństwa i 100 jest identyczna z uwzględnieniem wielkości liter.
MatchedOriginalText: Oryginalny tekst, który został uzyskany dla tego wyniku. Zwracany tylko wtedy, jeśli dopasowane oryginalny tekst różnił się od tekstu wejściowego. Używane do zwracania tekst źródłowy dopasowywania rozmytego. Nie są zwracane wyniki Translator firmy Microsoft.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego będzie miał co najmniej 5. Anonimowo podana tłumaczeń zwykle obejmuje ocenę 1-4 podczas tłumaczenia autorytatywnie podana zwykle obejmuje klasyfikację od 6 do 10.
* `Count`Liczba wybranych tłumaczenie z tą klasyfikacją. Wartość będzie równa 0 dla automatycznie przetłumaczonego odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)
A `GetTranslationsResponse` obiektu w formacie opisane powyżej.

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Identyfikator aplikacji|(puste)|Wymagany. Jeśli `Authorization` lub `Ocp-Apim-Subscription-Key` nagłówka jest używany, pozostaw puste pole appid else zawierają ciąg zawierający `"Bearer" + " " + "access_token"`.|query|ciąg|
|tekst|(puste)|Wymagany. Ciąg reprezentujący tekst do tłumaczenia. Rozmiar tekstu nie może przekroczyć 10 000 znaków.|query|ciąg|
|z|(puste)|Wymagany. Ciąg reprezentujący kod języka tłumaczenie tekstu.|query|ciąg|
|na |(puste)    |Wymagany. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.|query|ciąg|
|maxTranslations|(puste)|Wymagany. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.|query|liczba całkowita|
|Autoryzacja| (puste)|Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|ciąg| nagłówek|
|OCP-Apim subskrypcji — klawisz|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Uwagi dotyczące implementacji
Użyj `GetTranslationsArray` metoda pobierania wielu kandydatów translacji na wiele tekstów źródła.

Identyfikator URI jest żądanie `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Format treści żądania ma następującą składnię.

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

* `AppId`: Wymagane. Jeśli używany jest nagłówek autoryzacji, pozostaw puste pole appid obejmują else zawierające ciąg `"Bearer" + " " + "access_token"`.
* `From`: Wymagane. Ciąg reprezentujący kod języka tłumaczenie tekstu.
* `MaxTranslations`: Wymagane. Liczba całkowita reprezentująca maksymalną liczbę tłumaczeń do zwrócenia.
* `Options`: Opcjonalne. Opcje obiektu, który zawiera wartości wymienionych poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia. Określonych elementów musi być wymienione w kolejności alfabetycznej.
    - Kategoria ": ciąg zawierający kategorii (domena) tłumaczenia. Wartość domyślna to ogólne.
    - `ContentType`: Jedynym obsługiwanym, a wartość domyślna opcja to zwykły tekst.
    - `IncludeMultipleMTAlternatives`: Flaga wartości logicznej, aby określić, czy alternatyw więcej niż jeden powinny być zwrócone z aparatu MT. Prawidłowe wartości to PRAWDA i FAŁSZ (z uwzględnieniem wielkości liter). Domyślna ma wartość false i zawiera tylko 1 alternatywne. Ustawienie flagi na wartość true umożliwia generowanie alternatyw sztuczne w tłumaczeniu, w pełni zintegrowana z architekturą współpracy tłumaczenia (CTF). Funkcja umożliwia zwracanie alternatyw zdania, które mają nie alternatyw w CTF, dodając sztuczne alternatyw z listy najlepszych n dekodera.
        - Klasyfikacje klasyfikacje są stosowane w następujący sposób: 1) najlepsze automatyczne tłumaczenie ma klasyfikację 5. 2) alternatywami z CTF odzwierciedlają urząd recenzenta, od -10 do + 10. 3) alternatywami automatycznie generowanych tłumaczenia (n najlepszy) mają klasyfikacji 0 i stopień dopasowania 100.
        - Liczba rozwiązań alternatywnych, liczba zwróconych alternatyw zależy od maxTranslations, ale może być mniejsza.
        - Kierunki ta funkcja nie jest dostępny do tłumaczenia między uproszczony i chiński tradycyjny, obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
* `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Tę samą zawartość zostanie zwrócony w odpowiedzi.
* `Uri`: Filtrowanie wyników przez ten identyfikator URI. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `User`: Filtrowanie wyników przez tego użytkownika. Jeśli wartość nie jest ustawiona, wartość domyślna to wszystko.
* `Texts`: Wymagane. Tablica zawierająca teksty translacji. Wszystkie ciągi muszą być tego samego języka. Suma wszystkich teksty do tłumaczenia nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy wynosi 10.
* `To`: Wymagane. Ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.

Opcjonalne elementy można pominąć. Elementy, które są bezpośrednimi elementami podrzędnymi `GetTranslationsArrayRequest` musi być wymienione w kolejności alfabetycznej.

Żądanie `Content-Type` powinien być `text/xml`.

**Wartość zwracana:** format odpowiedzi ma następującą składnię.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

* `Translations`: Znaleziono tablica dopasowań, przechowywane w `TranslationMatch` obiektów (patrz poniżej). Tłumaczenia mogą obejmować nieznaczne wariantów oryginalny tekst (dopasowywania rozmytego). Tłumaczeń będą sortowane: 100% zgodny najpierw rozmytego dopasowań poniżej.
* `From`: Jeśli nie określono metody `From` języka, będzie to działanie automatyczne wykrywanie języka. W przeciwnym razie zostanie nadana od języka.
* `State`: Stan użytkownik ułatwia korelowanie żądania i odpowiedzi. Zawiera tę samą wartość, jak podano w `TranslateOptions` parametru.

`TranslationMatch` Obiekt składa się z następujących czynności:
* `Error`: Jeśli wystąpił błąd dla określonego ciągu wejściowego, znajduje się kod błędu. W przeciwnym razie pole jest puste.
* `MatchDegree`: System pasuje wejściowe zdań przed magazyn, w tym niedokładnymi dopasowaniami.  `MatchDegree` Wskazuje, jak blisko tekstu wejściowego odpowiada oryginalny tekst został znaleziony w magazynie. Wartość zwracana zakresów z zakresu od 0 do 100, gdzie 0 to nie podobieństwa i 100 jest identyczna z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst, który został uzyskany dla tego wyniku. Zwracany tylko wtedy, jeśli dopasowane oryginalny tekst różnił się od tekstu wejściowego. Używane do zwracania tekst źródłowy dopasowywania rozmytego. Nie są zwracane wyniki Translator firmy Microsoft.
* `Rating`: Wskazuje urząd osoby dokonującej decyzji jakości. Wyniki tłumaczenia maszynowego będzie miał co najmniej 5. Anonimowo podana tłumaczeń zwykle obejmuje ocenę 1-4 podczas tłumaczenia autorytatywnie podana zwykle obejmuje klasyfikację od 6 do 10.
* `Count`Liczba wybranych tłumaczenie z tą klasyfikacją. Wartość będzie równa 0 dla automatycznie przetłumaczonego odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.


### <a name="response-class-status-200"></a>Klasa odpowiedzi (stanu 200)

ciąg

Typ zawartości odpowiedzi: aplikacja/xml
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja  |(puste)    |Jeśli wymagane `appid` pola lub `Ocp-Apim-Subscription-Key` nagłówek nie został określony. Token autoryzacji: `"Bearer" + " " + "access_token"`.|nagłówek|ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)  |Jeśli wymagane `appid` pola lub `Authorization` nagłówek nie został określony.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i odpowiedzi szczegółowe informacje o błędzie.|
|401    |Nieprawidłowe poświadczenia|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, Daj nam znać. Podaj nam z przybliżonej Data i godzina żądania i identyfikator żądania nagłówka odpowiedzi `X-MS-Trans-Info`.|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie i Daj nam znać, jeśli błąd będzie się powtarzał.|

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Migrowanie do tekstu Translator v3 interfejsu API](../migrate-to-v3.md)










