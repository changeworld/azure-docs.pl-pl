---
title: Raportowanie ram tłumaczenia współpracy (CTF) — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Jak korzystać z raportowania programu Collaborative Translation Framework (CTF).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595942"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak korzystać z raportowania na platformie CTF (Collaborative Translation Framework)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 interfejsu API tekstu translatora.
> 
> Struktura tłumaczeń współpracy (CTF), wcześniej dostępna dla wersji 2.0 interfejsu API tekstu tłumacza, została przestarzała od 1 lutego 2018 r. AddTranslation i AddTranslationArray funkcje pozwalają użytkownikom włączyć poprawki za pośrednictwem collaborative translation framework. Po 31 stycznia 2018 r. te dwie funkcje nie zaakceptowały nowych zdań, a użytkownicy otrzymają komunikat o błędzie. Funkcje te zostały wycofane i nie zostaną zastąpione.

Interfejs API raportowania współpracy (CTF) raportowania zwraca statystyki i rzeczywistą zawartość w magazynie CTF. Ten interfejs API różni się od metody GetTranslations(), ponieważ:
* Zwraca przetłumaczoną zawartość i jej całkowitą liczbę tylko z twojego konta (appId lub konta portalu Azure Marketplace).
* Zwraca przetłumaczoną zawartość i jej całkowitą liczbę bez konieczności dopasowania zdania źródłowego.
* Nie zwraca automatycznego tłumaczenia (tłumaczenia maszynowego).

## <a name="endpoint"></a>Endpoint
Punktem końcowym interfejsu API raportowania CTF jesthttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Nazwa |    Opis|
|:---|:---|
| Metoda GetUserTranslationCounts | Pobierz liczby tłumaczeń, które są tworzone przez użytkownika. |
| Metoda GetUserTranslations | Pobiera tłumaczenia, które są tworzone przez użytkownika. |

Te metody umożliwiają:
* Pobierz pełny zestaw tłumaczeń i poprawek użytkowników w ramach identyfikatora konta do pobrania.
* Uzyskaj listę częstych współautorów. Upewnij się, że w addtranslation().
* Tworzenie interfejsu użytkownika ,, który umożliwia zaufanym użytkownikom, aby zobaczyć wszystkich dostępnych kandydatów, w razie potrzeby ograniczone do części witryny, na podstawie prefiksu URI.

> [!NOTE]
> Obie metody są stosunkowo powolne i drogie. Zaleca się stosowanie ich oszczędnie.

## <a name="getusertranslationcounts-method"></a>Metoda GetUserTranslationCounts

Ta metoda pobiera liczbę tłumaczeń, które są tworzone przez użytkownika. Zawiera listę liczników tłumaczeń pogrupowanych według parametrów żądania uriPrefix, od, do użytkownika, minRating i maxRating parametrów żądania.

**Składni**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli używany jest nagłówek Autoryzacja, pozostaw pole appid puste, a w przeciwnym razie określ ciąg zawierający token "Nadźwig" + " + dostęp.|
| uriPrefix (Poprawek uriPrefix) | **Opcjonalnie** Ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| Z | **Opcjonalnie** Ciąg reprezentujący kod języka tekstu tłumaczenia. |
| na | **Opcjonalnie** Ciąg reprezentujący kod języka do przetłumaczenia tekstu na.|
| minRating| **Opcjonalnie** Wartość całkowita reprezentująca minimalną ocenę jakości dla przetłumaczonego tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| maxRating ( maxRating )| **Opcjonalnie** Wartość całkowita reprezentująca maksymalną ocenę jakości dla przetłumaczonego tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| użytkownik | **Opcjonalnie** Ciąg, który jest używany do filtrowania wyniku na podstawie inicjatora przesyłania. |
| category| **Opcjonalnie** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję ogólną.|
| minDateUtc| **Opcjonalnie** Data od momentu pobrania tłumaczeń. Data musi być w formacie UTC. |
| maxDateUtc| **Opcjonalnie** Data do momentu pobrania tłumaczeń. Data musi być w formacie UTC. |
| Pomiń| **Opcjonalnie** Liczba wyników, które chcesz pominąć na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widoku z rekordu 21 wynik, określ 20 dla tego parametru. Wartość domyślna dla tego parametru to 0.|
| wziąć | **Opcjonalnie** Liczba wyników, które chcesz pobrać. Maksymalna liczba każdego żądania wynosi 100. Wartość domyślna to 100.|

> [!NOTE]
> Parametry pomijania i żądania umożliwiają podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslationCount**. Każdy UserTranslationCount ma następujące elementy:

| Pole | Opis |
|:---|:---|
| Liczba| Liczba pobieranych wyników|
| Z | Język źródłowy|
| Klasyfikacja| Ocena stosowana przez osoby przesyłającego w wywołaniu metody AddTranslation()|
| Do| Język docelowy|
| Identyfikator uri| Identyfikator URI zastosowany w wywołaniu metody AddTranslation()|
| Użytkownik| Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| Kreślenie argumentów | Parametr '**maxDateUtc**' musi być większy lub równy '**minDateUtc**'.| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP jest ponad przydział.| <ul><li>Zostanie osiągnięty limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Przydział godzinowy i dzienny ograniczają liczbę znaków akceptowanych przez interfejs API usługi Microsoft Translator.</li></ul>|
| TranslateApiException | AppId jest ponad przydział.| Identyfikator aplikacji przekroczył przydział godzinowy lub dzienny.|

> [!NOTE]
> Kontyngent zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Zobacz przykłady kodu na GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metoda GetUserTranslations

Ta metoda pobiera tłumaczenia, które są tworzone przez użytkownika. Zapewnia tłumaczenia pogrupowane według uriPrefix, od, do, użytkownika i minRating i maxRating parametrów żądania.

**Składni**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli używany jest nagłówek Autoryzacja, pozostaw pole appid puste, a w przeciwnym razie określ ciąg zawierający token "Nadźwig" + " + dostęp.|
| uriPrefix (Poprawek uriPrefix)| **Opcjonalnie** Ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| Z| **Opcjonalnie** Ciąg reprezentujący kod języka tekstu tłumaczenia.|
| na| **Opcjonalnie** Ciąg reprezentujący kod języka do przetłumaczenia tekstu na.|
| minRating| **Opcjonalnie** Wartość całkowita reprezentująca minimalną ocenę jakości dla przetłumaczonego tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| maxRating ( maxRating )| **Opcjonalnie** Wartość całkowita reprezentująca maksymalną ocenę jakości dla przetłumaczonego tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| użytkownik| **Opcjonalne. Ciąg używany do filtrowania wyniku na podstawie inicjatora przesłania**|
| category| **Opcjonalnie** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję ogólną.|
| minDateUtc| **Opcjonalnie** Data od momentu pobrania tłumaczeń. Data musi być w formacie UTC.|
| maxDateUtc| **Opcjonalnie** Data do momentu pobrania tłumaczeń. Data musi być w formacie UTC.|
| Pomiń| **Opcjonalnie** Liczba wyników, które chcesz pominąć na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widoku z rekordu 21 wynik, określ 20 dla tego parametru. Wartość domyślna dla tego parametru to 0.|
| wziąć| **Opcjonalnie** Liczba wyników, które chcesz pobrać. Maksymalna liczba każdego żądania wynosi 100. Wartość domyślna to 50.|

> [!NOTE]
> Parametry pomijania i żądania umożliwiają podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslation**. Każdy UserTranslation ma następujące elementy:

| Pole | Opis |
|:---|:---|
| UtworzonoDateUtc| Data utworzenia wpisu przy użyciu addtranslation()|
| Z| Język źródłowy|
| OriginalText (Tekst oryginalny)| Tekst języka źródłowego używany podczas przesyłania żądania|
|Klasyfikacja |Ocena stosowana przez osoby przesyłającego w wywołaniu metody AddTranslation()|
|Do|    Język docelowy|
|Przetłumaczonytekst|    Tłumaczenie przesłane w wywołaniu metody AddTranslation()|
|Identyfikator uri|   Identyfikator URI zastosowany w wywołaniu metody AddTranslation()|
|Użytkownik   |Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| Kreślenie argumentów | Parametr '**maxDateUtc**' musi być większy lub równy '**minDateUtc**'.| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP jest ponad przydział.| <ul><li>Zostanie osiągnięty limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Przydział godzinowy i dzienny ograniczają liczbę znaków akceptowanych przez interfejs API usługi Microsoft Translator.</li></ul>|
| TranslateApiException | AppId jest ponad przydział.| Identyfikator aplikacji przekroczył przydział godzinowy lub dzienny.|

> [!NOTE]
> Kontyngent zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Zobacz przykłady kodu na GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
