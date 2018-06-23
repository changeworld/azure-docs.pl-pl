---
title: Rozpoczynanie pracy z usługą mowy niestandardowe na platformie Azure | Dokumentacja firmy Microsoft
description: Zasubskrybować usługę mowy niestandardowe, a następnie połącz z subskrypcją platformy Azure w celu nauczenia modelu i wdrożenie działania usługi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347113"
---
# <a name="get-started-with-custom-speech-service"></a>Rozpoczynanie pracy z usługą mowy niestandardowych

Eksploruj najważniejszych funkcji usługi mowy niestandardowe i Dowiedz się, jak tworzenie, wdrażanie i używanie modeli akustycznego i język do potrzeb aplikacji. Po zarejestrowaniu się w portalu usług mowy niestandardowych można znaleźć bardziej szczegółową dokumentację i instrukcje krok po kroku.

## <a name="samples"></a>Przykłady  
Znajduje się przykład nieuprzywilejowany dostarczające umożliwiające przechodząc, który można znaleźć [tutaj](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Wymagania wstępne  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Subskrybowanie niestandardowej usługi rozpoznawania mowy i uzyskiwanie klucza subskrypcji
Przed gry z powyższych przykładzie, należy subskrybować niestandardowe usługi mowy i uzyskiwanie klucza, zobacz subskrypcji [subskrypcje](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) lub wykonaj wyjaśnienia [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). W tym samouczku można zarówno klucza podstawowego i pomocniczego. Upewnij się, że należy stosować najlepsze rozwiązania dla zachowania klucz tajny klucza interfejsu API i zabezpieczenia.

### <a name="get-the-client-library-and-example"></a>Pobierz klienta biblioteki i przykładowe
Możesz pobrać biblioteki klienta i przykład za pomocą [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Plik zip pobranego ma zostać wyodrębniony do wybranego folderu, w przypadku wielu użytkowników wybierz folder, w programie Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Tworzenie niestandardowych modelu akustycznego
Aby dostosować modelu akustycznego do domeny, to zbiór danych mowy jest wymagana. Ta kolekcja składa się z zestawu plików audio mowy danych i plik tekstowy z transcriptions każdego pliku audio. Dane audio powinna być reprezentatywna dla tego scenariusza, w której chcesz użyć aparatu rozpoznawania

Na przykład: Jeśli chcesz rozpoznawania mowy w środowisku fabryki zakłócenia, pliki dźwiękowe powinien składać się z osób w zakłócenia fabryki.
Jeśli interesuje Cię optymalizacji wydajności dla pojedynczego prelegenta, np. chcesz wykonać transkrypcji wszystkie jego FDR Fireside rozmowy, a następnie wiele przykładów tylko tej osoby mówiącej powinien zawierać pliki audio.

Szczegółowy opis można znaleźć w sposobie tworzenia niestandardowych modelu akustycznego [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Tworzenie modelu języków niestandardowych
Procedurę tworzenia modelu języków niestandardowych jest podobne do tworzenia modelu akustycznego, z wyjątkiem nie ma żadnych danych audio tylko tekst. Tekst powinien zawierać wiele przykładowych zapytań lub zniesławiających wprowadzanym przez użytkowników, aby podać lub są zalogowani użytkownicy informacją (lub wpisując) w aplikacji.

Szczegółowy opis można znaleźć na temat tworzenia niestandardowych języka modelu [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Tworzenie niestandardowych endpoint mowy na tekst
Po utworzeniu niestandardowych modeli akustycznego i/lub języka modeli można można wdrożyć w niestandardowych endpoint mowy na tekst. Aby utworzyć nowy punkt końcowy niestandardowe, kliknij przycisk "Wdrożenia" z menu "CRI" w górnej części strony. Powoduje przejście do tabeli o nazwie "Wdrożenia" bieżący niestandardowe punkty końcowe. Jeśli jeszcze nie utworzono żadnych punktów końcowych, tabela jest pusta. Bieżące ustawienia regionalne znajduje odzwierciedlenie w tytule tabeli. Jeśli chcesz utworzyć wdrożenie w innym języku, kliknij "Zmień ustawienia regionalne". Dodatkowe informacje na temat obsługiwanych języków można znaleźć w sekcji na zmienianie ustawień regionalnych.

Szczegółowy opis znajduje się na temat tworzenia punktu końcowego niestandardowego tekstu mowy [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Przy użyciu punktu końcowego mowy niestandardowych
Żądania mogą być wysyłane do punktu końcowego mowy na tekst CRI w sposób bardzo podobne jako domyślny punkt końcowy mowy kognitywnych usług firmy Microsoft. Należy zauważyć, że te punkty końcowe funkcjonalnie takie same jak domyślne punkty końcowe interfejsu API mowy. W związku z tym te same funkcje dostępne za pośrednictwem interfejsu API REST dla interfejsu API mowy lub biblioteka klienta jest również dostępny dla niestandardowych punktu końcowego.

Szczegółowy opis znajduje się na temat korzystania z niestandardowych endpoint mowy na tekst [tutaj](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Należy pamiętać, że punkty końcowe utworzony za pośrednictwem CRI może przetwarzać różne liczby równoczesnych żądań w zależności od warstwy, że subskrypcja jest skojarzona z. Jeżeli wyróżnienia więcej niż są wymagane, ich zwróci kod błędu 429 (zbyt wiele żądań). Aby uzyskać więcej informacji można znaleźć [uzyskać informacje o cenach](https://www.microsoft.com/cognitive-services/en-us/pricing). Ponadto jest przydział miesięczny żądań dla warstwy bezpłatna. W przypadkach, możesz uzyskać dostępu do punktu końcowego w warstwie bezpłatna powyżej miesięcznego limitu przydziału usługi zwraca kod błędu 403 Zabroniony.

Usługa przyjęto założenie, że audio są przesyłane w czasie rzeczywistym. Jeśli jest wysyłane szybciej, żądanie zostanie uwzględniony działać do chwili upłynął jej czas trwania w czasie rzeczywistym.

* [Omówienie](cognitive-services-custom-speech-home.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
