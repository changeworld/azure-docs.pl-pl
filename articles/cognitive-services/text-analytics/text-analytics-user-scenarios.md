---
title: Przykładowe scenariusze użytkownika interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Użyj w tym artykule, aby wyświetlić niektóre typowe scenariusze dotyczące integracji interfejsu API analizy tekstu usług i procesów.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8d2aed16b5af60c44501e6d72332783a164ff91
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478391"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Przykładowe scenariusze użytkownika interfejsu API analizy tekstu

Interfejs API analizy tekstu jest oparte na chmurze usługa zapewniająca przetwarzania języka naturalnego zaawansowane nad tekstem. W tym artykule opisano przykładowe przypadki użycia interfejsu API zintegrować swoje rozwiązania biznesowe i procesów. 

## <a name="analyze-survey-results"></a>Analizuj wyniki ankiety

Uzyskuj szczegółowe dane z klientów oraz pracowników wyniki badań, od przetwarzania odpowiedzi nieprzetworzony tekst przy użyciu analizy tonacji. Agregowanie wyników analizy, należy wykonać dalsze czynności i zachęcanie engagements.

![Obraz, opisujące, jak przeprowadzić analizę tonacji na badaniach klientów oraz pracowników.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizowanie telefonów od klientów dla ruchu przychodzącego zarejestrowane

Prowadzenie analiz wywołania usług klienta przy użyciu zamiany tekstu na mowę, analizę tonacji i fraz klucza. Wyświetlić wyniki na pulpicie nawigacyjnym usługi Power BI lub portalu, aby lepiej zrozumieć klientów, wyróżnij klientów usługi trendy i zwiększania zaangażowania klientów. Wysyłanie żądań interfejsu API jako zadania wsadowego dla raportowania lub w w czasie rzeczywistym interwencji. Zobacz [przykładowego kodu](https://github.com/rlagh2/callcenteranalytics).

![Obraz opisujące sposób automatyzowania uzyskiwaniem przydatnej z działem obsługi klienta wywołania, przy użyciu analizy tonacji](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Przetwarzanie i klasyfikowanie żądania pomocy technicznej

Użyj klucza frazy i rozpoznawanie jednostek przetwarzania żądania pomocy technicznej zgłoszone w formacie tekstowym bez struktury. Użyj wyodrębnione fraz i jednostek do kategoryzowania żądania analizy trendów i planowania zasobów.

![Obraz opisujących sposób korzystania z kluczowych rozpoznawanie wyodrębniania i jednostki do kategoryzowania raporty zdarzeń i trendów](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorowanie kanałów informacyjnych w mediach społecznościowych swojego produktu

Monitoruj opinie o produkcie użytkownika w usłudze twitter swojego produktu lub stroną w serwisie Facebook. Użyj danych do przeanalizowania nastroje klientów w zakresie nowych produktów uruchamia, wyodrębnianie kluczowych fraz o funkcje i żądania funkcji lub problemy klientów adres po ich wprowadzeniu.

![Obraz opisujących sposób monitorowania produktów i firm opinii w mediach społecznościowych, przy użyciu wyodrębnianie kluczowych fraz](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest interfejs API analizy tekstu?](overview.md)
* [Wyślij żądanie za pomocą interfejsu API analizy tekstuC#](quickstarts/csharp.md)
