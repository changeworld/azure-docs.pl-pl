---
title: Przykładowe scenariusze użytkowników interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Ten artykuł umożliwia wyświetlenie typowych scenariuszy integracji interfejsu API analizy tekstu z usługami i procesami.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219248"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Przykładowe scenariusze użytkowników interfejsu API analizy tekstu

Interfejs API analizy tekstu to usługa oparta na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego za pomocą tekstu. W tym artykule opisano kilka przykładowych przypadków użycia do integracji interfejsu API z rozwiązaniami i procesami biznesowymi. 

## <a name="analyze-survey-results"></a>Analizowanie wyników ankiety

Rysuj wnioski z wyników ankiety klientów i pracowników, przetwarzając nieprzetworzone odpowiedzi tekstowe przy użyciu analizy tonacji. Agreguj wyniki do analizy, działań następczych i prowadzenia działań.

![Obraz opisujący sposób przeprowadzania analizy tonacji w ankietach klientów i pracowników.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizowanie zarejestrowanych połączeń klientów przychodzących

Wyodrębnij szczegółowe informacje z wywołań obsługi klienta przy użyciu tekstu do mowy, analizy tonacji i wyodrębniania fraz kluczowych. Wyświetlaj wyniki na pulpicie nawigacyjnym usługi Power BI lub w portalu, aby lepiej zrozumieć klientów, wyróżnić trendy w obsłudze klienta i zwiększyć zaangażowanie klientów. Wysyłaj żądania interfejsu API jako partia do raportowania lub w czasie rzeczywistym do interwencji. Zobacz przykładowy kod [w usłudze GitHub](https://github.com/rlagh2/callcenteranalytics).

![Obraz opisujący sposób automatyzacji uzyskiwania informacji z połączeń obsługi klienta przy użyciu analizy tonacji](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Przetwarzanie i kategoryzowanie zdarzeń pomocy technicznej

Wyodrębnianie fraz kluczowych i rozpoznawanie encji umożliwia przetwarzanie żądań obsługi przesłanych w formacie tekstowym bez struktury. Wyodrębnione frazy i jednostki służy do kategoryzowanie żądań planowania zasobów i analizy trendów.

![Obraz opisujący sposób używania wyodrębniania fraz kluczowych i rozpoznawania encji do kategoryzowania raportów i trendów zdarzeń](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorowanie kanałów mediów społecznościowych produktu

Monitoruj opinie użytkowników na stronie twitter lub Facebook produktu. Dane te wykorzystują do analizowania nastrojów klientów w kierunku wprowadzania nowych produktów, wyodrębniania kluczowych fraz dotyczących funkcji i żądań funkcji lub rozwiązywania skarg klientów w miarę ich pojawiania się. Zobacz przykładowy [szablon usługi Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obraz opisujący sposób monitorowania opinii produktów i firm w mediach społecznościowych przy użyciu ekstrakcji kluczowych fraz](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klasyfikowanie i redagowanie dokumentów, które mają poufne informacje

Rozpoznawanie nazwanych jednostek służy do identyfikowania informacji osobistych i poufnych w dokumentach. Użyj danych, aby sklasyfikować dokumenty lub zredagować je, aby mogły być udostępniane bezpiecznie.

![Obraz opisujący sposób używania NER do wykrywania informacji osobistych oraz klasyfikowania i redagowania dokumentów](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Następne kroki

* [Czym jest interfejs API analizy tekstu?](overview.md)
* [Wysyłanie żądania do interfejsu API analizy tekstu przy użyciu biblioteki klienta](quickstarts/text-analytics-sdk.md)
