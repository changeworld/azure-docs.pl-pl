---
title: Framework współpracy tłumaczenia (CTF) raportowanie — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak używać raportowania współpracy Translation Framework (CTF).
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 178747ffddbadb06751ce0db7d16701c3cea7416
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712818"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak korzystać z raportowania na platformie CTF (Collaborative Translation Framework)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 z interfejsu API tłumaczenia tekstu.
> 
> Współpracy tłumaczenia Framework (CTF), wcześniej dostępne dla V2.0 interfejsu API tłumaczenia tekstu, została zakończona, począwszy od 1 lutego 2018 r. Funkcje AddTranslation i AddTranslationArray umożliwiają użytkownikom włączanie poprawek za pomocą współpracy Framework tłumaczenia. Po 31 stycznia 2018 r. te dwie funkcje nie zaakceptował nowe zgłoszenia zdania, a użytkownicy otrzymają komunikat o błędzie. Te funkcje zostały wycofane i nie podlegają wymianie.

Interfejs API raportowania współpracy Translation Framework (CTF) zwraca statystyki i rzeczywistej zawartości w magazynie CTF. Ten interfejs API różni się od metody GetTranslations() ponieważ go:
* Zwraca tłumaczonej zawartości i jego łączna liczba tylko z Twojego konta (appId lub konto w portalu Azure Marketplace).
* Zwraca tłumaczonej zawartości i jego łączna liczba bez dopasowania zdania źródła.
* Nie zwraca automatycznego tłumaczenia (tłumaczeniem maszynowym).

## <a name="endpoint"></a>Endpoint
Punkt końcowy interfejsu API raportowania CTF jest https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Name (Nazwa) |    Opis|
|:---|:---|
| GetUserTranslationCounts Method | Pobierz liczniki tłumaczenia, które są tworzone przez użytkownika. |
| GetUserTranslations Method | Pobiera tłumaczenia, które są tworzone przez użytkownika. |

Te metody umożliwiają:
* Pobierz kompletny zestaw tłumaczenia użytkownika i poprawek w ramach swojego Identyfikatora konta do pobrania.
* Uzyskaj listę częstych współautorów. Upewnij się, czy prawidłowa nazwa użytkownika znajduje się w AddTranslation().
* Tworzenie interfejsu użytkownika (UI), który umożliwia użytkownikom zaufanego Zobacz wszystkich dostępnych kandydatów, jeśli to konieczne, ograniczony do części witryny, na podstawie prefiksu identyfikatora URI.

> [!NOTE]
> Obie metody są stosunkowo powolne i kosztowne. Zalecane jest, aby ich używać oszczędnie.

## <a name="getusertranslationcounts-method"></a>Metoda GetUserTranslationCounts

Ta metoda pobiera liczbę tłumaczeń, które są tworzone przez użytkownika. Zapewnia listy liczb tłumaczenia w rozbiciu na uriPrefix, od, do użytkownika, minRating i maxRating parametry żądania.

**Składnia**

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
| appId | **Wymagane** Jeśli nagłówek autoryzacji jest używany, pozostaw to pole puste appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| uriPrefix | **Opcjonalnie** ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| z | **Opcjonalnie** ciąg reprezentujący kod języka tekstu tłumaczenia. |
| na | **Opcjonalnie** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.|
| minRating| **Opcjonalnie** wartość całkowitą reprezentującą klasyfikację minimalnej jakości przetłumaczonego tekstu. Prawidłowa wartość to zakresu od -10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalnie** wartość całkowitą reprezentującą klasyfikację maksymalna jakości przetłumaczonego tekstu. Prawidłowa wartość to zakresu od -10 do 10. Wartość domyślna to 1.|
| Użytkownik | **Opcjonalnie** ciąg, który jest używany do filtrowania wyników oparte na inicjatorem przesyłania. |
| category| **Opcjonalnie** ciąg zawierający kategorii lub domeny tłumaczenia. Ten parametr obsługuje tylko domyślną opcję ogólne.|
| minDateUtc| **Opcjonalnie** Data, od kiedy chcesz pobrać tłumaczenia. Data musi przypadać w formacie UTC. |
| maxDateUtc| **Opcjonalnie** daty do kiedy chcesz pobrać tłumaczenia. Data musi przypadać w formacie UTC. |
| pomiń| **Opcjonalnie** liczbę wyników, które chcesz przejść na stronę. Na przykład chcąc pomijania wierszy pierwszych 20 wyników i widokiem z 21 rekordu wyników, należy określić wartość 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| Wypełnij | **Opcjonalnie** liczbę wyników, które mają zostać pobrane. Maksymalna liczba każdego żądania wynosi 100. Wartość domyślna to 100.|

> [!NOTE]
> Parametry żądania skip i take Włącz podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslationCount**. Każdy UserTranslationCount zawiera następujące elementy:

| Pole | Opis |
|:---|:---|
| Licznik| Liczba wyników, które są pobierane|
| Od | Język źródłowy|
| Ocena| Ocena, która jest stosowana przez osoby przesyłającej w wywołaniu metody AddTranslation()|
| Do| Język docelowy|
| Identyfikator URI| Identyfikator URI stosowany w wywołaniu metody AddTranslation()|
| Użytkownik| Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr "**maxDateUtc**"musi być większa lub równa"**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP za pośrednictwem przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania będzie ograniczony w 10 000 znaków.</li><li>Co godzinę i dzienny limit przydziału ograniczyć liczbę znaków, które będzie akceptować interfejsu API w usłudze Translator firmy Microsoft.</li></ul>|
| TranslateApiException | Identyfikator aplikacji znajduje się nad limitu przydziału.| Identyfikator aplikacji przekroczony przydział godzinowo lub dziennie.|

> [!NOTE]
> Limit przydziału zostanie dostosowana do zapewnienia sprawiedliwe wszystkim użytkownikom usługi.

**Wyświetl przykłady kodu na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metoda GetUserTranslations

Ta metoda pobiera tłumaczenia, które są tworzone przez użytkownika. Zapewnia ona tłumaczenia pogrupowane według uriPrefix, znajduje się na pozycji, użytkownika i minRating i maxRating parametry żądania.

**Składnia**

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
| appId | **Wymagane** Jeśli nagłówek autoryzacji jest używany, pozostaw to pole puste appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| uriPrefix| **Opcjonalnie** ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| z| **Opcjonalnie** ciąg reprezentujący kod języka tekstu tłumaczenia.|
| na| **Opcjonalnie** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w.|
| minRating| **Opcjonalnie** wartość całkowitą reprezentującą klasyfikację minimalnej jakości przetłumaczonego tekstu. Prawidłowa wartość to zakresu od -10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalnie** wartość całkowitą reprezentującą klasyfikację maksymalna jakości przetłumaczonego tekstu. Prawidłowa wartość to zakresu od -10 do 10. Wartość domyślna to 1.|
| Użytkownik| **Opcjonalnie. Ciąg, który jest używany do filtrowania wyników, w oparciu o inicjatorem przesyłania**|
| category| **Opcjonalnie** ciąg zawierający kategorii lub domeny tłumaczenia. Ten parametr obsługuje tylko domyślną opcję ogólne.|
| minDateUtc| **Opcjonalnie** Data, od kiedy chcesz pobrać tłumaczenia. Data musi przypadać w formacie UTC.|
| maxDateUtc| **Opcjonalnie** daty do kiedy chcesz pobrać tłumaczenia. Data musi przypadać w formacie UTC.|
| pomiń| **Opcjonalnie** liczbę wyników, które chcesz przejść na stronę. Na przykład chcąc pomijania wierszy pierwszych 20 wyników i widokiem z 21 rekordu wyników, należy określić wartość 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| Wypełnij| **Opcjonalnie** liczbę wyników, które mają zostać pobrane. Maksymalna liczba każdego żądania wynosi 100. Wartością domyślną jest 50.|

> [!NOTE]
> Parametry żądania skip i take Włącz podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslation**. Każdy UserTranslation zawiera następujące elementy:

| Pole | Opis |
|:---|:---|
| CreatedDateUtc| Data utworzenia przy użyciu AddTranslation()|
| Od| Język źródłowy|
| OriginalText| Tekst języka źródłowego, który jest używany podczas wysyłania żądania|
|Ocena |Ocena, która jest stosowana przez osoby przesyłającej w wywołaniu metody AddTranslation()|
|Do|    Język docelowy|
|TranslatedText|    Tłumaczenie przesłane w wywołaniu metody AddTranslation()|
|Identyfikator URI|   Identyfikator URI stosowany w wywołaniu metody AddTranslation()|
|Użytkownik   |Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr "**maxDateUtc**"musi być większa lub równa"**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP za pośrednictwem przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania będzie ograniczony w 10 000 znaków.</li><li>Co godzinę i dzienny limit przydziału ograniczyć liczbę znaków, które będzie akceptować interfejsu API w usłudze Translator firmy Microsoft.</li></ul>|
| TranslateApiException | Identyfikator aplikacji znajduje się nad limitu przydziału.| Identyfikator aplikacji przekroczony przydział godzinowo lub dziennie.|

> [!NOTE]
> Limit przydziału zostanie dostosowana do zapewnienia sprawiedliwe wszystkim użytkownikom usługi.

**Wyświetl przykłady kodu na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
