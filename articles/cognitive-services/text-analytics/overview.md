---
title: Co to jest analiza tekstu?
titleSuffix: Azure Cognitive Services
description: Analiza tekstu w usłudze Azure Cognitive Services na potrzeby analizy tonacji, wyodrębniania kluczowych fraz, wykrywania języka oraz łączenia jednostek.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: overview
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: fcc10723b2fb750b099f99999de01ab699d5d5e8
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887772"
---
# <a name="what-is-text-analytics"></a>Co to jest analiza tekstu?

Interfejs API analizy tekstu to oparta na chmurze usługa udostępniająca zaawansowane przetwarzanie języka naturalnego z zastosowaniem tekstu nieprzetworzonego. Obejmuje ona cztery główne funkcje: analizę tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka oraz łączenie jednostek.

Interfejs API jest wspierany przez zasoby w usługach [Microsoft Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), tj. zbioru algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze, który można łatwo wykorzystać w swoich projektach programistycznych.

## <a name="capabilities-in-text-analytics"></a>Możliwości analizy tekstu

Analiza tekstu może oznaczać różne rzeczy, ale w usługach Cognitive Services interfejs API analizy tekstu zapewnia cztery rodzaje analizy, zgodnie z opisem w następującej tabeli.

| Operacje| Opis | Interfejsy API |
|-----------|-------------|------|
|[**Analiza tonacji**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Dowiedz się, co klienci myślą o Twojej marce lub danym temacie, analizując nieprzetworzony tekst pod kątem wskazówek dotyczących pozytywnych i negatywnych tonacji. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację.<br /> Modele analizy są wstępnie szkolone przy użyciu rozbudowanych technologii z zakresu treści tekstu oraz naturalnego języka firmy Microsoft. W przypadku [wybranych języków](text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Wyodrębnianie kluczowych fraz**](how-tos/text-analytics-how-to-keyword-extraction.md) | Automatycznie wyodrębniaj kluczowe frazy, aby szybko identyfikować główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”.  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Wykrywanie języka**](how-tos/text-analytics-how-to-language-detection.md) | Wykrywaj język wprowadzanego tekstu oraz zgłaszaj jeden kod języka dla każdego dokumentu przesłanego w żądaniu dla nawet 120 języków. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Rozpoznawanie jednostek (wersja zapoznawcza)**](how-tos/text-analytics-how-to-entity-linking.md) | Identyfikuj i klasyfikuj jednostki w tekście jako osoby, miejsca, organizacje, datę/godzinę, ilości, wartości procentowe, waluty i inne. Dobrze znane jednostki są również rozpoznawane i łączone z większą ilością informacji w Internecie. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: przesyłasz dane do analizy i obsługujesz dane wyjściowe w swoim kodzie. Analizatory są używane tak, jak jest — bez dodatkowej konfiguracji czy dostosowywania.

1. [Utwórz konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), aby uzyskać [klucz dostępu](how-tos/text-analytics-how-to-access-key.md). Klucz musi być przekazywany w każdym żądaniu.

2. [Sformułuj żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) zawierające dane jako nieprzetworzony tekst bez struktury, w formacie JSON.

3. Opublikuj żądanie do punktu końcowego utworzonego podczas rejestracji, załączając żądany zasób: analizę tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka lub identyfikację jednostek.

4. Prześlij odpowiedź strumieniowo lub przechowaj ją lokalnie. W zależności od żądania wyniki są oceną tonacji, kolekcją wyodrębnionych kluczowych fraz lub kodem języka.

Dane wyjściowe są zwracane w jednym dokumencie JSON, z wynikami dla każdego opublikowanego dokumentu tekstowego, w oparciu o identyfikator. Możesz następnie przeanalizować, zwizualizować lub sklasyfikować wyniki, przekształcając je w szczegółowe informacje umożliwiające podejmowanie działań.

Dane nie są przechowywane na koncie. Operacje wykonywane przez interfejs API analizy tekstu są bezstanowe, co oznacza, że podany tekst jest przetwarzany, a wyniki są zwracane natychmiast.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się z tematem [Obsługiwane języki w interfejsie API analizy tekstu](text-analytics-supported-languages.md), aby uzyskać tę zawartość.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aktualne ograniczenie to 5000 znaków dla każdego dokumentu; jeśli chcesz przeanalizować większe dokumenty, możesz podzielić je na mniejsze części. Jeśli nadal potrzebujesz większego limitu, [skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/), abyśmy mogli omówić Twoje wymagania.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5000 znaków, mierzone przy użyciu funkcji `String.Length`. |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Limit szybkości to 100 wywołań na minutę. Należy pamiętać, że można przesłać dużą liczbę dokumentów w jednym wywołaniu (maksymalnie 1000 dokumentów).

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz funkcji `String.Length` w celu obliczenia liczby znaków, korzystasz z tej samej metody, której używamy do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj [interaktywną demonstrację](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Możesz wkleić tekst (maksymalnie 5000 znaków), aby wykryć język (do 120 języków), obliczyć ocenę tonacji lub wyodrębnić kluczowe frazy. Rejestracja nie jest wymagana.

Gdy wszystko będzie gotowe, aby wywołać interfejs API bezpośrednio:

+ [Zarejestruj się](how-tos/text-analytics-how-to-signup.md), aby uzyskać klucz dostępu, i sprawdź kroki związane z [wywoływaniem interfejsu API](how-tos/text-analytics-how-to-call-api.md).

+ [Szybki Start](quickstarts/csharp.md) to przewodnik po wywołaniach interfejsu API REST napisanych w języku C#. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu.

+ [Dokumentacja interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) zawiera dokumentację techniczną interfejsów API. Dokumentacja obsługuje wywołania osadzone, więc możesz wywołać interfejs API z każdej strony dokumentacji.

+ [Zawartość zewnętrzna i zawartość społeczności](text-analytics-resource-external-community.md) zapewnia listę wpisów w blogu oraz filmów przedstawiających sposób użycia analizy tekstu z innymi narzędziami i technologiami.

## <a name="see-also"></a>Zobacz też

 [Strona dokumentacji usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
