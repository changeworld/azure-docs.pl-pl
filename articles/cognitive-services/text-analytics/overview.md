---
title: Co to jest interfejs API analizy tekstu? Możliwość
titleSuffix: Azure Cognitive Services
description: Użyj interfejs API analizy tekstu z usługi Azure Cognitive Services do analizy tonacji, wyodrębniania kluczowych fraz, wykrywania języka i rozpoznawania jednostek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: f84d980dd01d1e9f3ffcc00d73f712211524cb42
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667641"
---
# <a name="what-is-text-analytics-api"></a>Co to jest interfejs API analizy tekstu?

Interfejs API analizy tekstu to usługa oparta na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego w przypadku nieprzetworzonego tekstu i obejmuje cztery główne funkcje: tonacji Analysis, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek.

Interfejs API jest częścią usług [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), które są zbiorem algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze do wykorzystania w Twoich projektach programistycznych.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analiza tekstu może oznaczać różne elementy, ale w Cognitive Services interfejs API analizy tekstu oferuje cztery typy analiz, jak opisano poniżej.

## <a name="sentiment-analysis"></a>Analiza tonacji
Użyj [analizy tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md) , aby dowiedzieć się, co klienci uważają za swoją markę lub temat, analizując nieprzetworzony tekst w celu uzyskania wskazówek dotyczących pozytywnych lub negatywnych tonacji. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację.<br /> Modele analizy są wstępnie szkolone przy użyciu rozbudowanych technologii z zakresu treści tekstu oraz naturalnego języka firmy Microsoft. W przypadku [wybranych języków](text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. Można użyć interfejsu API [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) lub zestawu [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Automatycznie [Wyodrębniaj kluczowe frazy](how-tos/text-analytics-how-to-keyword-extraction.md) , aby szybko identyfikować główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. Możesz użyć interfejsu API [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) tutaj lub zestawu [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="language-detection"></a>Wykrywanie języka
Możesz [wykryć język, w którym wprowadzany jest tekst wejściowy](how-tos/text-analytics-how-to-language-detection.md) , i zgłosić jeden kod języka dla każdego dokumentu przesłanego na żądanie w szerokim zakresie języków, wariantów, dialektach i niektórych językach regionalnych/kulturowych. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. Można użyć interfejsu API [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) lub zestawu [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych
[Zidentyfikuj i Kategoryzuj jednostki](how-tos/text-analytics-how-to-entity-linking.md) w tekście jako osoby, miejsca, organizacje, daty/godziny, ilości, wartości procentowe, waluty i nie tylko. Dobrze znane jednostki są również rozpoznawane i łączone z większą ilością informacji w Internecie. Można użyć interfejsu API [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) .

## <a name="use-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowych kontenerów platformy Docker blisko danych można lokalnie wyodrębniać kluczowe frazy, wykrywać język i analizować tonację, korzystając z [kontenerów analizy tekstu](how-tos/text-analytics-how-to-install-containers.md).

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: przesyłasz dane do analizy i obsługujesz dane wyjściowe w swoim kodzie. Analizatory są używane tak, jak jest — bez dodatkowej konfiguracji czy dostosowywania.

1. [Utwórz konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), aby uzyskać [klucz dostępu](how-tos/text-analytics-how-to-access-key.md). Klucz musi być przekazywany w każdym żądaniu.

2. [Sformułuj żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) zawierające dane jako nieprzetworzony tekst bez struktury, w formacie JSON.

3. Opublikuj żądanie do punktu końcowego utworzonego podczas rejestracji, załączając żądany zasób: analizę tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka lub identyfikację jednostek.

4. Prześlij odpowiedź strumieniowo lub przechowaj ją lokalnie. W zależności od żądania wyniki są oceną tonacji, kolekcją wyodrębnionych kluczowych fraz lub kodem języka.

Dane wyjściowe są zwracane w jednym dokumencie JSON, z wynikami dla każdego opublikowanego dokumentu tekstowego, w oparciu o identyfikator. Możesz następnie przeanalizować, zwizualizować lub sklasyfikować wyniki, przekształcając je w szczegółowe informacje umożliwiające podejmowanie działań.

Dane nie są przechowywane na koncie. Operacje wykonywane przez interfejs API analizy tekstu są bezstanowe, co oznacza, że podany tekst jest przetwarzany, a wyniki są zwracane natychmiast.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>analiza tekstu dla wielu poziomów doświadczenia programistycznego

Możesz rozpocząć korzystanie z interfejs API analizy tekstu w swoich procesach, nawet jeśli nie masz dużo doświadczenia w programowaniu. Skorzystaj z tych samouczków, aby dowiedzieć się, jak za pomocą interfejsu API analizować tekst na różne sposoby, aby dopasować go do poziomu środowiska. 

* Wymagane jest minimalne programowanie:
    * [Użyj interfejs API analizy tekstu i usługi MS Flow, aby zidentyfikować tonacji komentarzy w grupie usługi Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integruj Power BI z interfejs API analizy tekstu, aby analizować Opinie klientów](tutorials/tutorial-power-bi-key-phrases.md)
* Zalecane środowisko programistyczne:
    * [Analiza tonacji na strumieniu danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i synteza mowy](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się z tematem [Obsługiwane języki w interfejsie API analizy tekstu](text-analytics-supported-languages.md), aby uzyskać tę zawartość.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aktualne ograniczenie to 5120 znaków dla każdego dokumentu; jeśli chcesz przeanalizować większe dokumenty, możesz podzielić je na mniejsze części. Jeśli nadal potrzebujesz większego limitu, [skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/), abyśmy mogli omówić Twoje wymagania.

| Limit | Value |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5120 znaków, mierzone przy użyciu funkcji [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Twoje limity szybkości różnią się w zależności od warstwy cenowej.

| Warstwa          | Liczba żądań na sekundę | Liczba żądań na minutę |
|---------------|---------------------|---------------------|
| Wiele usług | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Żądania są mierzone osobno dla każdej funkcji analiza tekstu. Na przykład można wysłać maksymalną liczbę żądań dla warstwy cenowej do każdej funkcji w tym samym czasie.      

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz funkcji [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) w celu obliczenia liczby znaków, korzystasz z tej samej metody, której używamy do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Następne kroki

+ [Utwórz zasób platformy Azure](../cognitive-services-apis-create-account.md) dla analiza tekstu, aby uzyskać klucz i punkt końcowy dla aplikacji.

+ [Szybki Start](quickstarts/csharp.md) to przewodnik po wywołaniach interfejsu API REST napisanych w języku C#. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu. Jeśli wolisz, możesz zacząć korzystać z [przewodnika Szybki](quickstarts/python.md) Start dla języka Python.

+ Aby uzyskać informacje o nowych wersjach i funkcjach [, zobacz co nowego w interfejs API analizy tekstu](whats-new.md) .

+ Dig w nieco bardziej szczegółowym samouczku dotyczącym [analizy tonacji](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) przy użyciu Azure Databricks.

+ Zapoznaj się z naszą listą wpisów w blogu i Dowiedz się więcej na temat korzystania z interfejs API analizy tekstu z innymi narzędziami i technologiami na naszej [stronie zawartości społeczności zewnętrznej &](text-analytics-resource-external-community.md).
