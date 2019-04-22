---
title: Co to jest interfejs API analizy tekstu? -Możliwości —
titleSuffix: Azure Cognitive Services
description: Interfejs API analizy tekstu w usłudze Azure Cognitive Services do analizy tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i łączenie podmiotów.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 054f0cf91904497b77fd08165808f9b4c381ecca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500386"
---
# <a name="what-is-text-analytics-api"></a>Co to jest interfejs API analizy tekstu?

Interfejs API analizy tekstu to oparta na chmurze usługa udostępniająca zaawansowane przetwarzanie języka naturalnego z zastosowaniem tekstu nieprzetworzonego. Obejmuje ona cztery główne funkcje: analizę tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka oraz łączenie jednostek.

Interfejs API jest częścią usług [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), które są zbiorem algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze do wykorzystania w Twoich projektach programistycznych.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analiza tekstu może mieć różne znaczenie, ale w usługach Cognitive Services, interfejs API analizy tekstu udostępnia cztery rodzaje analizy, zgodnie z poniższym opisem.

## <a name="sentiment-analysis"></a>Analiza tonacji
Użyj [analizę tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md) Aby dowiedzieć się, co klienci myślą o Twojej marki lub wybranego tematu, analizując nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację.<br /> Modele analizy są wstępnie szkolone przy użyciu rozbudowanych technologii z zakresu treści tekstu oraz naturalnego języka firmy Microsoft. W przypadku [wybranych języków](text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. Możesz użyć [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) interfejsu API lub [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) zestawu SDK.

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Automatycznie [wyodrębnianie kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md) można szybko identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. Możesz użyć [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) interfejsu API w tym miejscu lub [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) zestawu SDK.

## <a name="language-detection"></a>Wykrywanie języka
Możesz [wykryć język, który wprowadzany tekst jest zapisany w](how-tos/text-analytics-how-to-language-detection.md) i Raportowanie kodu jeden język dla każdego dokumentu Przesłano żądanie dla maksymalnie 120 języków. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. Możesz użyć [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) interfejsu API lub [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) zestawu SDK.

## <a name="entity-recognition-preview"></a>Rozpoznawanie jednostek (wersja zapoznawcza)
[Identyfikowanie i klasyfikowanie jednostek](how-tos/text-analytics-how-to-entity-linking.md) w tekście jako osobach, miejscach, organizacje, daty/godziny, ilości, wartości procentowych i waluty. Dobrze znane jednostki są również rozpoznawane i łączone z większą ilością informacji w Internecie. Możesz użyć [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) interfejsu API.

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

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analiza tekstu dla wielu programowania poziom doświadczenia

Za pomocą interfejsu API analizy tekstu w procesach, można uruchomić, nawet jeśli nie masz doświadczenie w programowaniu. Korzystanie z tych samouczków, aby dowiedzieć się, jak za pomocą interfejsu API można analizować tekstu na różne sposoby, aby dopasować poziomu Twojego doświadczenia. 

* Wymagany minimalny programowania:
    * [Użyj interfejsu API analizy tekstu i Flow MS, aby zidentyfikować tonacji komentarzy w grupie usługi Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrowanie usługi Power BI przy użyciu interfejsu API analizy tekstu, aby analizować opinie klientów](tutorials/tutorial-power-bi-key-phrases.md)
* Zaleca się środowisko programowania:
    * [Analiza tonacji na strumieniu danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Tworzenie aplikacji Flask tłumaczenie tekstu, analizowanie tonacji i syntetyzowania mowy](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się z tematem [Obsługiwane języki w interfejsie API analizy tekstu](text-analytics-supported-languages.md), aby uzyskać tę zawartość.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aktualne ograniczenie to 5120 znaków dla każdego dokumentu; jeśli chcesz przeanalizować większe dokumenty, możesz podzielić je na mniejsze części. Jeśli nadal potrzebujesz większego limitu, [skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/), abyśmy mogli omówić Twoje wymagania.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5120 znaków, mierzone przy użyciu funkcji [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Ograniczanie liczby wywołań jest 100 żądań na drugim i 1000 żądań na minutę. Można przesyłać duże ilości dokumentów w jednym wywołaniu (maksymalnie 1000 dokumentów).

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz funkcji [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) w celu obliczenia liczby znaków, korzystasz z tej samej metody, której używamy do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Kolejne kroki

+ [Zarejestruj się](how-tos/text-analytics-how-to-signup.md), aby uzyskać klucz dostępu, i sprawdź kroki związane z [wywoływaniem interfejsu API](how-tos/text-analytics-how-to-call-api.md).

+ [Szybki Start](quickstarts/csharp.md) to przewodnik po wywołaniach interfejsu API REST napisanych w języku C#. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu. Jeśli wolisz, możesz uruchomić z [Szybki Start języka Python](quickstarts/python.md) zamiast tego.

+ Szczegółowe informacje nieco dzięki temu [samouczek analizy tonacji](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) przy użyciu usługi Azure Databricks.

+ Zapoznaj się z naszą listę wpisów w blogu i więcej wideo na temat korzystania z interfejsu API analizy tekstu z innymi narzędziami i technologiami w naszym [zewnętrzne & sekcji Zawartość społeczności strony](text-analytics-resource-external-community.md).
