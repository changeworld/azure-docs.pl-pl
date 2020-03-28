---
title: Czym jest interfejs API analizy tekstu? - Możliwości -
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API analizy tekstu z usługi Azure Cognitive Services do analizy tonacji, wyodrębniania fraz kluczowych, wykrywania języka i rozpoznawania jednostek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78395731"
---
# <a name="what-is-the-text-analytics-api"></a>Czym jest interfejs API analizy tekstu?

Interfejs API analizy tekstu to usługa oparta na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego za pomocą tekstu nieprzetworzonego i zawiera cztery główne funkcje: analizę tonacji, wyodrębnianie fraz kluczowych, wykrywanie języka i rozpoznawanie nazwanych jednostek.

Interfejs API jest częścią usług [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), które są zbiorem algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze do wykorzystania w Twoich projektach programistycznych.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analiza tekstu może oznaczać różne rzeczy, ale w usługach Cognitive Services interfejs API analizy tekstu udostępnia cztery typy analiz, jak opisano poniżej. Tych funkcji można używać za pomocą [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)lub [biblioteki klienta.](quickstarts/text-analytics-sdk.md)

## <a name="sentiment-analysis"></a>Analiza tonacji
Skorzystaj z [analizy nastrojów,](how-tos/text-analytics-how-to-sentiment-analysis.md) aby dowiedzieć się, co klienci sądzą o Twojej marce lub temacie, analizując surowy tekst w poszukiwaniu wskazówek dotyczących pozytywnych lub negatywnych nastrojów. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację.<br /> Modele analizy są wstępnie szkolone przy użyciu rozbudowanych technologii z zakresu treści tekstu oraz naturalnego języka firmy Microsoft. W przypadku [wybranych języków](text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej.

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Automatycznie [wyodrębniaj kluczowe frazy,](how-tos/text-analytics-how-to-keyword-extraction.md) aby szybko zidentyfikować główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”.

## <a name="language-detection"></a>Wykrywanie języka
Można [wykryć, w którym języku jest napisany tekst wejściowy](how-tos/text-analytics-how-to-language-detection.md) i zgłosić jeden kod języka dla każdego dokumentu przesłanego na żądanie w wielu językach, wariantach, dialektach i niektórych językach regionalnych/kulturowych. Kod języka jest powiązany z oceną, co wskazuje siłę oceny.

## <a name="named-entity-recognition"></a>Rozpoznawanie jednostek znaku
[Identyfikowanie i klasyfikowanie encji](how-tos/text-analytics-how-to-entity-linking.md) w tekście jako osób, miejsc, organizacji, daty/godziny, ilości, wartości procentowych, walut i innych. Dobrze znane jednostki są również rozpoznawane i łączone z większą ilością informacji w Internecie.

## <a name="use-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowych kontenerów platformy Docker blisko danych można lokalnie wyodrębniać kluczowe frazy, wykrywać język i analizować tonację, korzystając z [kontenerów analizy tekstu](how-tos/text-analytics-how-to-install-containers.md).

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: przesyłasz dane do analizy i obsługujesz dane wyjściowe w swoim kodzie. Analizatory są używane tak, jak jest — bez dodatkowej konfiguracji czy dostosowywania.

1. [Tworzenie zasobu platformy Azure](../cognitive-services-apis-create-account.md) dla analizy tekstu. Następnie [pobierz klucz wygenerowany](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) do uwierzytelniania żądań.

2. [Sformułuj żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) zawierające dane jako nieprzetworzony tekst bez struktury, w formacie JSON.

3. Opublikuj żądanie do punktu końcowego ustalonego podczas rejestracji, dołączając żądany zasób: analiza tonacji, wyodrębnianie fraz kluczowych, wykrywanie języka lub rozpoznawanie nazwanych jednostek.

4. Prześlij odpowiedź strumieniowo lub przechowaj ją lokalnie. W zależności od żądania wyniki są oceną tonacji, kolekcją wyodrębnionych kluczowych fraz lub kodem języka.

Dane wyjściowe są zwracane w jednym dokumencie JSON, z wynikami dla każdego opublikowanego dokumentu tekstowego, w oparciu o identyfikator. Możesz następnie przeanalizować, zwizualizować lub sklasyfikować wyniki, przekształcając je w szczegółowe informacje umożliwiające podejmowanie działań.

Dane nie są przechowywane na koncie. Operacje wykonywane przez interfejs API analizy tekstu są bezstanowe, co oznacza, że podany tekst jest przetwarzany, a wyniki są zwracane natychmiast.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analiza tekstu dla wielu poziomów środowiska programowania

Możesz rozpocząć korzystanie z interfejsu API analizy tekstu w procesach, nawet jeśli nie masz dużego doświadczenia w programowaniu. Skorzystaj z tych samouczków, aby dowiedzieć się, jak można używać interfejsu API do analizowania tekstu na różne sposoby, aby dopasować go do poziomu środowiska. 

* Wymagane jest minimalne programowanie:
    * [Wyodrębnianie informacji w programie Excel przy użyciu analizy tekstu i automatyzacji zasilania](tutorials/extract-excel-information.md)
    * [Użyj interfejsu API analizy tekstu i przepływu MS, aby zidentyfikować tonację komentarzy w grupie Usługi Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integracja usługi Power BI z interfejsem API analizy tekstu w celu analizowania opinii klientów](tutorials/tutorial-power-bi-key-phrases.md)
* Zalecane doświadczenie programistyczne:
    * [Analiza tonacji na strumieniu danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Tworzenie aplikacji Flask do tłumaczenia tekstu, analizowania tonacji i syntezy mowy](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się [z obsługiwanymi językami w interfejsie API analizy tekstu](text-analytics-supported-languages.md) dla tej zawartości.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aktualne ograniczenie to 5120 znaków dla każdego dokumentu; jeśli chcesz przeanalizować większe dokumenty, możesz podzielić je na mniejsze części.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5120 znaków mierzonych [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)przez . |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Limit stawek będzie się różnić w zależności od warstwy cenowej.

| Warstwa          | Żądania na sekundę | Żądania na minutę |
|---------------|---------------------|---------------------|
| S / Obsługa wielodochowa | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Żądania są mierzone dla każdej funkcji analizy tekstu oddzielnie. Na przykład można wysłać maksymalną liczbę żądań dla warstwy cenowej do każdej funkcji, w tym samym czasie.      

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz, [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) aby uzyskać liczbę znaków, używasz tej samej metody, której używamy do pomiaru rozmiaru danych.

## <a name="next-steps"></a>Następne kroki

+ [Utwórz zasób platformy Azure](../cognitive-services-apis-create-account.md) dla analizy tekstu, aby uzyskać klucz i punkt końcowy dla aplikacji.

+ Użyj [przewodnika Szybki start,](quickstarts/text-analytics-sdk.md) aby rozpocząć wysyłanie wywołań interfejsu API. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu.

+ Zobacz, [co nowego w interfejsie API analizy tekstu,](whats-new.md) aby uzyskać informacje na temat nowych wersji i funkcji.

+ Kopać w nieco głębiej z tego [samouczka analizy tonacji](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) przy użyciu usługi Azure Databricks.

+ Zapoznaj się z naszą listą wpisów na blogu i więcej filmów na temat korzystania z interfejsu API analizy tekstu z innymi narzędziami i technologiami na naszej [stronie Zewnętrzne & treści społecznościowych.](text-analytics-resource-external-community.md)
