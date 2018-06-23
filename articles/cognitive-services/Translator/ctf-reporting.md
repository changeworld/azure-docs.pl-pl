---
title: Tłumaczenie współpracy Microsoft Translator raportowania Framework (CTF)
description: Jak używać raportowania współpracy tłumaczenia Framework (CTF).
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: cefc630a82a56703ba4942bcad18f6e0a38b1ee5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347688"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak używać raportowania współpracy tłumaczenia Framework (CTF)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w 3.0 Translator API tekstu.

> Wspólne tłumaczeń Framework (CTF), wcześniej dostępne dla interfejsu API Translator tekstu, w wersji 2.0 została uznana za przestarzałą począwszy od 1 lutego 2018. Funkcje AddTranslation i AddTranslationArray zezwala użytkownikom na włączanie poprawek za pomocą współpracy Framework tłumaczenia. Po 31 stycznia 2018 te dwie funkcje nie zaakceptowała nowych dokumentów zdanie, a użytkownicy otrzymują komunikat o błędzie. Te funkcje zostały wycofana i nie zostaną zastąpione. 

>Podobne funkcje są dostępne w interfejsie API Centrum Translator, co umożliwia tworzenie niestandardowych tłumaczenia systemu z terminologią i stylu, i można go za pomocą Identyfikatora kategorii w interfejsie API tekst Translator wywołać. Translator koncentratora: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). Translator Centrum interfejsu API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

Interfejsu API raportowania współpracy tłumaczenia Framework (CTF) zwraca statystyki i rzeczywistej zawartości w magazynie CTF. Ten interfejs API różni się od metody GetTranslations() ponieważ go:
* Zwraca zawartość przetłumaczonego i jego łączna liczba tylko z Twojego konta (identyfikator aplikacji lub portalu Azure Marketplace konta).
* Zwraca zawartość przetłumaczonego i jego łączna liczba bez konieczności dopasowanie zdania źródła.
* Zwraca automatyczne tłumaczenie (tłumaczenia maszynowego).

## <a name="endpoint"></a>Endpoint
Punkt końcowy interfejsu API raportowania CTF jest http://api.microsofttranslator.com/v2/beta/ctfreporting.svc
                        

## <a name="methods"></a>Metody
| Name (Nazwa) |    Opis|
|:---|:---|
| GetUserTranslationCounts — metoda | Pobierz liczbę tłumaczenia, które są tworzone przez użytkownika. |
| GetUserTranslations — metoda | Pobiera tłumaczenia, które są tworzone przez użytkownika. |

Te metody umożliwiają:
* Pobierz pełny zestaw tłumaczenia użytkownika i poprawki z Identyfikatorem konta do pobrania.
* Uzyskaj listę częstych współautorów. Upewnij się, czy podano poprawną nazwę użytkownika, w AddTranslation().
* Tworzenie interfejsu użytkownika (UI), który umożliwia użytkownikom zaufanych wyświetlić wszystkie dostępne kandydatów, w razie potrzeby ograniczone do części witryny na podstawie prefiksu identyfikatora URI.

> [!NOTE]
> Obie te metody są stosunkowo powolne i drogie. Zaleca się ich używanie oszczędnie.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts — metoda

Ta metoda pobiera liczbę tłumaczenia, które są tworzone przez użytkownika. Zapewnia listę liczników tłumaczenia pogrupowane według uriPrefix, do użytkownika, minRating i maxRating parametry żądania.

**Składnia**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslationCount[]GetUserTranslationCounts(
           string appId,
           string uriPrefix,
           string from,
           string to,
           int? minRating,
           int? maxRating,
           string user, 
           string category
           DateTime? minDateUtc,
           DateTime? maxDateUtc,
           int? skip,
           int? take);
```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli nagłówek uwierzytelnienia jest używany, pozostaw puste pole appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| uriPrefix | **Opcjonalne** ciąg zawierający Prefiks URI tłumaczenia.|
| z | **Opcjonalne** ciąg reprezentujący kod języka tłumaczenie tekstu. |
| na | **Opcjonalne** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.|
| minRating| **Opcjonalne** wartość całkowitą reprezentującą klasyfikację minimalnej jakości tłumaczenia tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** wartość całkowitą reprezentującą klasyfikacji maksymalna tłumaczenia tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| Użytkownik | **Opcjonalne** ciąg, który służy do filtrowania wyników w oparciu inicjatorem przesyłania. |
| category| **Opcjonalne** ciąg zawierający kategorii lub domeny tłumaczenia. Ten parametr obsługuje tylko opcję domyślną ogólne.|
| minDateUtc| **Opcjonalne** Data, od kiedy chcesz pobrać tłumaczenia. Data musi być w formacie UTC. |
| maxDateUtc| **Opcjonalne** daty do kiedy chcesz pobrać tłumaczenia. Data musi być w formacie UTC. |
| Pomiń| **Opcjonalne** liczba wyników, które chcesz przejść na stronę. Na przykład jeśli chcesz Pomiń 20 pierwszych wierszy wyników i widoku z 21 rekordu wyników, należy określić 20 dla tego parametru. Wartością domyślną tego parametru jest 0.|
| podejmij | **Opcjonalne** liczba wyników, które ma zostać pobrane. Maksymalna liczba wszystkich żądań to 100. Wartość domyślna to 100.|

> [!NOTE]
> Parametry żądania skip i take Włącz podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslationCount**. Każdy UserTranslationCount ma następujące elementy:

| Pole | Opis |
|:---|:---|
| Licznik| Liczba wyników, które są pobierane|
| Z | Języka źródłowego|
| Klasyfikacja| Klasyfikacja jest stosowany przez przesyłający w wywołaniu metody AddTranslation()|
| Do| Języka docelowego|
| Identyfikator URI| Identyfikator URI stosowane w wywołaniu metody AddTranslation()|
| Użytkownik| Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr "**maxDateUtc**'musi być większa lub równa'**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP należy za pośrednictwem przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania będzie ograniczona w 10 000 znaków.</li><li>Co godzinę i dzienny limit przydziału Ogranicz liczbę znaków, które będzie akceptować Microsoft Translator API.</li></ul>|
| TranslateApiException | Identyfikator AppId jest za pośrednictwem przydziału.| Identyfikator aplikacji przekroczenie przydziału godzinowo lub dziennie.|

> [!NOTE]
> Limit przydziału będzie Dostosuj, aby zapewnić sprawiedliwe przydzielanie zasobów wszystkim użytkownikom usługi.

**Przykłady kodu widoku GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations — metoda

Ta metoda pobiera tłumaczenia, które są tworzone przez użytkownika. Zapewnia ona tłumaczeń pogrupowane według uriPrefix, użytkowników i minRating i maxRating parametry żądania.

**Składnia**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslation[] GetUserTranslations (
            string appId,
            string uriPrefix,
            string from,
            string to,
            int? minRating,
            int? maxRating,
            string user, 
            string category
            DateTime? minDateUtc,
            DateTime? maxDateUtc,
            int? skip,
            int? take); 
```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli nagłówek uwierzytelnienia jest używany, pozostaw puste pole appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| uriPrefix| **Opcjonalne** ciąg zawierający Prefiks URI tłumaczenia.|
| z| **Opcjonalne** ciąg reprezentujący kod języka tłumaczenie tekstu.|
| na| **Opcjonalne** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do.|
| minRating| **Opcjonalne** wartość całkowitą reprezentującą klasyfikację minimalnej jakości tłumaczenia tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** wartość całkowitą reprezentującą klasyfikacji maksymalna tłumaczenia tekstu. Prawidłowa wartość wynosi od -10 do 10. Wartość domyślna to 1.|
| Użytkownik| **Opcjonalne. Ciąg, który służy do filtrowania wyników oparte na inicjatorem przesyłania**|
| category| **Opcjonalne** ciąg zawierający kategorii lub domeny tłumaczenia. Ten parametr obsługuje tylko opcję domyślną ogólne.| 
| minDateUtc| **Opcjonalne** Data, od kiedy chcesz pobrać tłumaczenia. Data musi być w formacie UTC.| 
| maxDateUtc| **Opcjonalne** daty do kiedy chcesz pobrać tłumaczenia. Data musi być w formacie UTC.|
| Pomiń| **Opcjonalne** liczba wyników, które chcesz przejść na stronę. Na przykład jeśli chcesz Pomiń 20 pierwszych wierszy wyników i widoku z 21 rekordu wyników, należy określić 20 dla tego parametru. Wartością domyślną tego parametru jest 0.|
| podejmij| **Opcjonalne** liczba wyników, które ma zostać pobrane. Maksymalna liczba wszystkich żądań to 100. Wartością domyślną jest 50.|

> [!NOTE]
> Parametry żądania skip i take Włącz podział na strony dla dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslation**. Każdy UserTranslation ma następujące elementy:

| Pole | Opis |
|:---|:---|
| CreatedDateUtc| Data utworzenia przy użyciu AddTranslation()|
| Z| Języka źródłowego|
| OriginalText| Tekst języka źródłowego, który jest używany podczas przesyłania żądania|
|Klasyfikacja |Klasyfikacja jest stosowany przez przesyłający w wywołaniu metody AddTranslation()|
|Do|    Języka docelowego|
|TranslatedText|    Tłumaczenie przesłane w wywołaniu metody AddTranslation()|
|Identyfikator URI|   Identyfikator URI stosowane w wywołaniu metody AddTranslation()|
|Użytkownik   |Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Komunikat | Warunki |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr "**maxDateUtc**'musi być większa lub równa'**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP należy za pośrednictwem przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania będzie ograniczona w 10 000 znaków.</li><li>Co godzinę i dzienny limit przydziału Ogranicz liczbę znaków, które będzie akceptować Microsoft Translator API.</li></ul>|
| TranslateApiException | Identyfikator AppId jest za pośrednictwem przydziału.| Identyfikator aplikacji przekroczenie przydziału godzinowo lub dziennie.|

> [!NOTE]
> Limit przydziału będzie Dostosuj, aby zapewnić sprawiedliwe przydzielanie zasobów wszystkim użytkownikom usługi.

**Przykłady kodu widoku GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)


















