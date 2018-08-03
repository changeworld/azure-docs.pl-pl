---
title: Co to jest usługa mowy (wersja zapoznawcza)?
description: 'Usługa rozpoznawania mowy, częścią firmy Microsoft Cognitive Services, łączy w sobie kilka usług mowy platformy Azure, które były wcześniej dostępne oddzielnie: rozpoznawania mowy Bing (wchodzących w skład rozpoznawania mowy i zamiany tekstu na mowę), niestandardowe mowy i tłumaczenie mowy.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445638"
---
# <a name="what-is-the-speech-service-preview"></a>Co to jest usługa mowy (wersja zapoznawcza)?

Usługa rozpoznawania mowy jest obsługiwane przez technologie używane w innych produktów firmy Microsoft, w tym Cortana i Microsoft Office.  Tej samej usługi jest dostępny dla każdego klienta jako usługi cognitive Services. 

> [!NOTE]
> Usługa rozpoznawania mowy jest obecnie w publicznej wersji zapoznawczej. Wróć tutaj regularne aktualizacje i dokumentację, próbki kodu dodatkowe i inne.

Z jedną subskrypcją usługa mowy daje deweloperom prosty sposób, aby zapewnić ich aplikacje zaawansowanych włączony mowy funkcji. Aplikacje można teraz funkcji poleceń głosowych, transkrypcji, funkcją dyktowania, synteza mowy i tłumaczenia.

## <a name="speech-service-features"></a>Funkcje usługi rozpoznawania mowy

|Funkcja|Opis|
|-|-|
|[Zamiany mowy na tekst](speech-to-text.md)| Transcribes strumieni audio na tekst, który aplikacja może akceptować jako dane wejściowe. Integruje się również z [usługi Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) ma być intencji użytkownika z wypowiedzi.|
|[Zamiana tekstu na mowę](text-to-speech.md)| Konwertuje zwykłego tekstu do naturalnym brzmiącą mowę, na dostarczane do aplikacji w pliku audio. Wiele głosów, różnicowanie płci lub akcent, są dostępne dla wielu języków. |
|[Tłumaczenie mowy](speech-translation.md)| Może służyć do translacji strumienie audio niemal w czasie rzeczywistym lub do przetwarzania mowy zarejestrowane. |
|[Zestaw Speech Devices SDK](speech-devices-sdk.md)| Wraz z wprowadzeniem ujednolicone usługi mowy firmy Microsoft i jej partnerzy oferują platforma zintegrowane sprzętu i oprogramowania, zoptymalizowana pod kątem tworzenia urządzenia obsługujące rozpoznawanie mowy |

## <a name="using-the-speech-service"></a>Przy użyciu usługi mowy

Usługa rozpoznawania mowy jest udostępniane na dwa sposoby. [Zestaw SDK](speech-sdk.md) ukrywa szczegóły protokołów sieciowych. [Interfejsu API REST](rest-apis.md) współdziała z dowolnego języka programowania, ale nie oferuje wszystkie funkcje oferowane przez zestaw SDK.

|<br>Metoda|Mowa<br>do tekstu|Tekst<br>Mowa|Mowa<br>Tłumaczenie|<br>Opis|
|-|-|-|-|-|
|[Zestawy SDK](speech-sdk.md)|Yes|Nie|Yes|Biblioteki dla określonych języków programowania, które upraszczają proces tworzenia.|
|[REST](rest-apis.md)|Yes|Yes|Nie|Prosty oparty na protokole HTTP interfejs API, która ułatwia dodawanie funkcji rozpoznawania mowy do aplikacji.|

## <a name="next-steps"></a>Kolejne kroki

Pobierz klucz subskrypcji wersji próbnej usługi mowy.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
