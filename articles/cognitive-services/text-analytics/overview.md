---
title: Przegląd interfejsu API Analytics tekstu — kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Tekst analizy interfejsu API Azure kognitywnych usługi analizy wskaźniki nastrojów klientów, wyodrębniania klucza frazy i wykrywania języka.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349412"
---
# <a name="what-is-text-analytics-api-version-20"></a>Co to jest tekst analizy interfejsu API w wersji 2.0?

Interfejsu API z analizy tekstu to usługa oparta na chmurze, która udostępnia zaawansowane języka naturalnego przetwarzania za pośrednictwem nieprzetworzony tekst i obejmuje cztery główne funkcje: analiza wskaźniki nastrojów klientów, wyodrębniania klucza frazy, wykrywania języka i łączenie jednostki.

Interfejs API nie jest obsługiwana przez zasobów w [kognitywnych usług firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/), Kolekcja uczenie maszynowe i algorytmy AI w chmurze, łatwo dostępne w rozwoju projektów.

## <a name="capabilities-in-text-analytics"></a>Możliwości w Analiza tekstu

Analiza tekstu może mieć różne znaczenie, ale w usługach kognitywnych interfejs API Analytics tekst zawiera cztery typy analizy zgodnie z opisem w poniższej tabeli.

| Operacje| Opis | Interfejsy API |
|-----------|-------------|------|
|[**Analiza wskaźniki nastrojów klientów**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Dowiedz się, klienci traktować marki lub tematu, analizując nieprzetworzony tekst do wskazówek dotyczących dodatnie lub ujemne wskaźniki nastrojów klientów. Ten interfejs API zwraca wynik wskaźniki nastrojów klientów od 0 do 1 dla każdego dokumentu, gdzie 1 to najbardziej dodatnią.<br /> Modele analizy są pretrained przy użyciu szeroką gamę treści tekstu i języka naturalnego technologii firmy Microsoft. Aby uzyskać [wybrane języki](text-analytics-supported-languages.md), interfejsu API można analizować i wynik nieprzetworzony tekst, który podasz, bezpośrednio zwracania wyników, aby aplikacja wywołująca. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Wyrażenie klucza wyodrębniania**](how-tos/text-analytics-how-to-keyword-extraction.md) | Wyodrębnij automatycznie fraz klucza w celu szybkiego identyfikowania głównych punktów. Na przykład w przypadku tekstu wejściowego "żywności został wyborna i wystąpiły cudowne personelu", interfejsu API zwraca głównych punktach wystąpienia: "żywności" i "cudowne pracownicy".  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Wykrywanie języka**](how-tos/text-analytics-how-to-language-detection.md) | Maksymalnie 120 języków wykrywa język, dla którego wejściowy tekst jest pisany w i raportują jednego języka kodu dla każdego dokumentu przesyłane na żądanie. Kod języka jest skojarzone z wynikiem wskazujący siły wynik. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Jednostki konsolidacji (wersja zapoznawcza)**](how-tos/text-analytics-how-to-entity-linking.md) | Zidentyfikuj dobrze znanych jednostek w tekst i łącza do dodatkowych informacji w sieci web. Łączenie jednostki rozpoznaje i pozwala odróżnić od siebie, gdy termin jest używany jako jeden oddzielnie odróżnienia jednostek, zleceń i innych formularzy programu word. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: przesyłanie danych do analizy i obsługi danych wyjściowych w kodzie. Analizatory są używane jako — jest bez dodatkowej konfiguracji lub dostosowania.

1. [Zarejestruj się](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dla [klucz dostępu](how-tos/text-analytics-how-to-access-key.md). Klucz muszą być przekazywane na każdym żądaniu.

2. [Sformułować żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) zawierające dane jako nieprzetworzone tekstu bez struktury, w formacie JSON.

3. Post żądania do punktu końcowego ustanowiony podczas tworzenia konta, dodanie do żądanego zasobu: analizy wskaźniki nastrojów klientów, wyodrębniania klucza frazy, wykrywania języka lub identyfikator jednostki.

4. Strumień lub magazynu odpowiedzi lokalnie. W zależności od żądania wyniki są wynikiem wskaźniki nastrojów klientów, Kolekcja wyodrębnionego fraz klucza lub kod języka.

Dane wyjściowe są zwracane jako pojedynczego dokumentu JSON z wynikami dla każdego dokumentu tekst, który można opublikować na podstawie identyfikatora. Można następnie analizować, wizualizacji lub kategoryzowanie wyniki do przydatnych wyników analiz.

Dane nie są przechowywane na koncie. Operacje wykonywane przez interfejs API z analizy tekstu są bezstanowych, co oznacza, tekst, który podasz jest przetwarzane i są zwracane natychmiast.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

W tej sekcji został przeniesiony do oddzielnym artykule celu lepszej odnajdywania. Zapoznaj się [obsługiwanych języków w interfejsie API Analytics tekst](text-analytics-supported-languages.md) dla tej zawartości.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API z analizy tekstu akceptować dane pierwotne tekstu. Bieżący limit to 5000 znaków dla każdego dokumentu. Jeśli potrzebujesz przeanalizować większe dokumenty, można je podzielić na mniejsze fragmenty. Jeśli nadal potrzebujesz wyższy limit [skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/) tak, aby omówiono wymagania.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5000 znaków mierzony przez `String.Length`. |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Limit szybkości jest 100 wywołania na minutę. Należy pamiętać, że mogą przesyłać duże ilości dokumentów w pojedynczym wywołaniu (maksymalnie 1000 dokumentów).

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejsu API z analizy tekstu korzysta z reprezentacji tekstowej i obliczeń liczby znaków kodowania Unicode. Z nie zauważalnego różnice w liczba znaków w formacie UTF-8 i UTF-16 można przesłać żądania. Punkty Unicode są używane jako heurystyki znaków i zostały uznane za równorzędne w celach limity danych analizy tekstu. Jeśli używasz `String.Length` uzyskać liczba znaków, są przy użyciu tej samej metody będziemy używać do mierzenia rozmiar danych.

## <a name="next-steps"></a>Kolejne kroki

Najpierw spróbuj [pokaz interaktywny](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Można wkleić wprowadzanie tekstu (maksymalna liczba znaków 5000), aby wykryć języka (maksymalnie 120), obliczania wyniku wskaźniki nastrojów klientów lub Wyodrębnij fraz klucza. Nie konieczności tworzenia konta.

Gdy wszystko będzie gotowe do bezpośrednio wywołać interfejs API:

+ [Zarejestruj się](how-tos/text-analytics-how-to-signup.md) dostępu do klucza i przejrzyj kroki dla [wywołanie interfejsu API](how-tos/text-analytics-how-to-call-api.md).

+ [Szybki Start](quickstarts/csharp.md) jest przewodnik interfejsu API REST wywołuje napisane w języku C#. Dowiedz się, jak przesyłanie tekstu, wybierz analizy i wyświetlić wyniki z minimalnym kodu.

+ [Dokumentacji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) zawiera dokumentacja techniczna dotycząca interfejsów API. Dokumentację obsługuje wywołania embedded, dzięki czemu można wywołać interfejsu API z każdej strony dokumentacji.

+ [Zewnętrzne & zawartość społeczności](text-analytics-resource-external-community.md) zawiera listę blogach i klipy wideo pokazuje sposób użycia Analiza tekstu z innych technologii i narzędzi.

## <a name="see-also"></a>Zobacz także

 [Kognitywnych stronę dokumentacji usług](https://docs.microsoft.com/azure/cognitive-services/)
