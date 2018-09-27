---
title: Rozpoczynanie pracy z usługą Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Subskrybowanie usługi Custom Speech i połącz działania usługi subskrypcji platformy Azure do uczenia modelu i wdrożenie.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: ae72edd626bd91dea7cd2812a3ef821b905f59a4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225246"
---
# <a name="get-started-with-custom-speech-service"></a>Rozpoczynanie pracy z usługą Custom Speech Service

Zapoznaj się z najważniejszych funkcji Custom Speech Service i Dowiedz się, jak tworzenie, wdrażanie i używanie akustyczne i językowe modeli do potrzeb aplikacji. Bardziej szczegółową dokumentację i instrukcje krok po kroku można znaleźć po zarejestrowaniu się w portalu niestandardowych usług przetwarzania mowy.

## <a name="samples"></a>Przykłady  
Znajduje się przykład dobre rozwiązanie, które oferujemy Ci rozpoczęcie pracy, który można znaleźć [tutaj](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Wymagania wstępne  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Subskrybowanie usługi Custom Speech Service i Uzyskaj klucz subskrypcji
Przed gry z powyższych przykładu, należy zasubskrybować Custom Speech Service i uzyskać subskrypcji z klucza, zobacz [subskrypcje](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) lub wyjaśnienia [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Zarówno klucz podstawowy i pomocniczy może służyć w ramach tego samouczka. Upewnij się, że stosowanie najlepszych rozwiązań w zakresie utrzymania klucz tajny klucza interfejsu API i bezpieczne.

### <a name="get-the-client-library-and-example"></a>Pobieranie klienta, biblioteki i przykładowe
Możesz ją pobrać bibliotekę klienta oraz przykład za pośrednictwem [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Plik zip pobranego musi zostać wyodrębniona do wybranego folderu, wielu użytkowników, wybierz folder programu Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Tworzenie niestandardowych modeli akustycznych
Aby dostosować model akustyczny do określonej domeny, to zbiór danych mowy jest wymagany. Ta kolekcja składa się z zestawu plików dźwiękowych z danymi mowy oraz pliku tekstowego z transkrypcjami wszystkich plików dźwiękowych. Dane audio powinna być reprezentatywna dla scenariusza, w której chcesz użyć aparatu rozpoznawania

Na przykład: Jeśli chcesz lepiej rozpoznawanie mowy w środowisku fabryki generujące dużo alertów, pliki audio powinna składać się z osób wypowiedzi w fabryce generujące dużo alertów.
Jeśli interesuje Cię Optymalizacja wydajności dla pojedynczej osoby mówiącej, może np. Czy chcesz także wszystkie rozmowy Fireside FDR firmy, a następnie pliki audio powinna składać się z wielu przykładów tylko tej osoby mówiącej.

Szczegółowy opis można znaleźć w sposób utworzyć niestandardowy model akustyczny [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Tworzenie modelu języka niestandardowego
Procedura tworzenia modelu języka niestandardowego jest podobnie do procesu tworzenia model akustyczny, z wyjątkiem nie ma żadnych danych audio tylko tekst. Tekst powinien składać się z wielu przykładów zapytań lub wypowiedzi oczekujesz, że użytkownicy mówią lub logowali się użytkownicy informujący o tym (lub wpisując) w aplikacji.

Szczegółowy opis można znaleźć na temat sposobu tworzenia modelu języka niestandardowego [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Tworzenie niestandardowego punktu końcowego mowy na tekst
Po utworzeniu niestandardowych modeli akustycznych i/lub modeli językowych, może zostać wdrożony w niestandardowy punkt końcowy rozpoznawania mowy na tekst. Aby utworzyć nowy niestandardowy punkt końcowy, w menu "CRI" w górnej części strony kliknij pozycję "Wdrożenia". Spowoduje to przejście do tabeli o nazwie "Wdrożenia" bieżący niestandardowe punkty końcowe. Jeśli jeszcze nie utworzono żadnych punktów końcowych, tabela jest pusta. Bieżące ustawienia regionalne są odzwierciedlone w tytule tabeli. Jeśli chcesz utworzyć wdrożenie w innym języku, wybierz polecenie "Zmień ustawienia regionalne". Dodatkowe informacje na temat obsługiwanych języków można znaleźć w sekcji na zmienianie ustawień regionalnych.

Szczegółowy opis można znaleźć o tym, jak utworzyć punkt końcowy niestandardowego tekstu mowy [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Przy użyciu punktu końcowego niestandardowa zamiana mowy
Żądania mogą być wysyłane w punkcie końcowym zamiany mowy na tekst CRI w bardzo podobny sposób jako domyślny punkt końcowy rozpoznawania mowy usług Azure Cognitive Services. Należy pamiętać, że te punkty końcowe są funkcjonalnie identyczny domyślne punkty końcowe interfejsu API rozpoznawania mowy. W związku z tym te same funkcje dostępne za pośrednictwem biblioteki klienta lub interfejsu API REST dla interfejsu API rozpoznawania mowy jest również dostępna dla niestandardowego punktu końcowego.

Szczegółowy opis można znaleźć na temat korzystania z niestandardowego punktu końcowego mowy na tekst [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Należy pamiętać, że punkty końcowe utworzone za pomocą CRI może przetwarzać różną liczbę jednoczesnych żądań w zależności od warstwy, czy subskrypcja jest skojarzona z. Jeśli wymagane są rozpoznawań więcej niż ta, ich zwróci kod błędu 429 (zbyt wiele żądań). Aby uzyskać więcej informacji można znaleźć [informacje o cenach](https://www.microsoft.com/cognitive-services/en-us/pricing). Ponadto jest miesięczny limit żądań bezpłatnej wersji usługi. W przypadkach, możesz uzyskać dostęp do punktu końcowego usługi w ramach warstwy bezpłatna powyżej miesięczny limit przydziału usługi zwraca kod błędu 403 Zabroniony.

Usługa przyjęto założenie, że audio są przesyłane w czasie rzeczywistym. Jeśli zostanie wysłany szybciej, żądania będą uznawane za uruchomienie do momentu upłynął jego czas trwania w czasie rzeczywistym.

* [Omówienie](cognitive-services-custom-speech-home.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
