---
title: Publikowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Po zakończeniu kompilowania i testowania aktywnej aplikacji LUIS Udostępnij ją aplikacji klienckiej, publikując ją w punkcie końcowym.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491328"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji do tymczasowego lub produkcyjnego punktu końcowego

Po zakończeniu kompilowania i testowania aktywnej aplikacji LUIS Udostępnij ją aplikacji klienckiej, publikując ją w punkcie końcowym. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikowanie

1. Aby opublikować w punkcie końcowym, wybierz pozycję **Publikuj** w prawym górnym panelu. 

    ![Przycisk Publikuj na górze, prawy pasek nawigacyjny](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Wybierz ustawienia dla opublikowanego punktu końcowego przewidywania, a następnie wybierz pozycję **Publikuj**.

    ![Wybierz pozycję Publikuj ustawienia, a następnie wybierz przycisk Publikuj.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publikowanie miejsc

Zaznacz odpowiednie miejsce po wyświetleniu okna podręcznego: 

* Przemieszczania.
* Narzędzi. 

Korzystając z obu miejsc publikowania, można w ten sposób mieć dwie różne wersje aplikacji dostępnych w opublikowanych punktach końcowych lub w tej samej wersji w dwóch różnych punktach końcowych. 

### <a name="publishing-regions"></a>Publikowanie regionów

Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami punktu końcowego przewidywania LUIS dodanych w portalu LUIS. 

Na przykład dla aplikacji utworzonej w usłudze [www.Luis.AI](https://www.luis.ai), jeśli UTWORZYSZ zasób Luis w dwóch regionach, **zachodnim** i **wschodnim**, a następnie dodasz je do aplikacji jako zasoby, aplikacja zostanie opublikowana w obu regionach. Aby uzyskać więcej informacji na temat regionów LUIS, zobacz [regiony](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Po wybraniu miejsca Skonfiguruj ustawienia publikowania dla:

* Analiza tonacji
* Poprawianie pisowni
* Napełnianiu mowy 

Po opublikowaniu te ustawienia są dostępne do przeglądu na stronie **ustawień publikowania** sekcji **Zarządzanie** . Można zmienić ustawienia za pomocą każdej publikacji. Jeśli anulujesz publikowanie, wszelkie zmiany wprowadzone podczas publikowania również zostaną anulowane. 

### <a name="when-your-app-is-published"></a>Po opublikowaniu aplikacji

Po pomyślnym opublikowaniu aplikacji w górnej części przeglądarki pojawia się zielone powiadomienie o powodzeniu. Zielony pasek powiadomień zawiera również link do punktów końcowych. 

![Publikowanie okna podręcznego z linkiem do punktu końcowego](./media/luis-how-to-publish-app/publish-success.png)

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz link. Możesz również przejść do adresów URL punktów końcowych, wybierając pozycję **Zarządzaj** w górnym menu, a następnie wybierając pozycję **zasoby platformy Azure** w menu po lewej stronie. 

## <a name="sentiment-analysis"></a>Analiza tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia integrację LUIS z [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) w celu zapewnienia analizy tonacji i kluczowych fraz. 

Nie musisz podawać klucza analiza tekstu i nie ma opłat za rozliczenie dla tej usługi na Twoje konto platformy Azure. 

Dane tonacji to wynik z zakresu od 1 do 0 wskazujący dodatnie (bliżej 1) lub ujemne (bliżej 0) tonacji danych. Etykieta tonacji `positive`, `neutral`i `negative` jest na obsługiwaną kulturę. Obecnie tylko język angielski obsługuje etykiety tonacji. 

Aby uzyskać więcej informacji na temat odpowiedzi punktu końcowego JSON z analizą tonacji, zobacz [tonacji Analysis](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Poprawianie pisowni

Poprawki pisowni są wprowadzane przed przewidywaniem LUIS użytkownika wypowiedź. W odpowiedzi można zobaczyć zmiany w oryginalnym wypowiedź, w tym pisownię.

## <a name="speech-priming"></a>Napełnianiu mowy

Speech napełnianiu to proces polegający na wysyłaniu modelu LUIS do usługi mowy przed konwersją tekstu na mowę. Dzięki temu usługa mowy może dokładniej zapewnić konwersję mowy dla modelu. Umożliwia to bot LUIS i reagowanie żądań i odpowiedzi w jednym wywołaniu przez jedno wywołanie mowy i przejęcie odpowiedzi na LUIS. Zapewnia ona mniej czasu na ogół.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Zarządzanie](./luis-how-to-azure-subscription.md) kluczami, aby dodać klucze do klucza subskrypcji platformy Azure do Luis i jak ustawić klucz sprawdzanie pisowni Bing i uwzględnić wszystkie intencje w wynikach.
* Aby uzyskać instrukcje dotyczące testowania opublikowanej aplikacji w konsoli testowej, zobacz temat [uczenie i testowanie aplikacji](luis-interactive-test.md) .

