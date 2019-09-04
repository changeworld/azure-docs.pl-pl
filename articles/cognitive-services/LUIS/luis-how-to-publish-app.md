---
title: Publikowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Po zakończeniu tworzenia i testowania aplikacją usługi LUIS active był dostępny do aplikacji klienckiej, publikując je do punktu końcowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257812"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji do tymczasowego lub produkcyjnego punktu końcowego

Po zakończeniu tworzenia i testowania aplikacją usługi LUIS active był dostępny do aplikacji klienckiej, publikując je do punktu końcowego. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikowanie

1. Aby opublikować punkt końcowy, wybierz **Publikuj** w górnym rogu, kliknij prawym przyciskiem myszy panel. 

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

Na przykład dla aplikacji utworzonej w usłudze [www.Luis.AI](https://www.luis.ai), jeśli UTWORZYSZ zasób Luis w dwóch regionach, **zachodnim** i **wschodnim**, a następnie dodasz je do aplikacji jako zasoby, aplikacja zostanie opublikowana w obu regionach. Aby uzyskać więcej informacji na temat usługi LUIS regionów, zobacz [regionów](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Po wybraniu miejsca Skonfiguruj ustawienia publikowania dla:

* Analiza tonacji
* Poprawianie pisowni
* Napełnianiu mowy 

Po opublikowaniu te ustawienia są dostępne do przeglądu na stronie **ustawień publikowania** sekcji **Zarządzanie** . Można zmienić ustawienia za pomocą każdej publikacji. Jeśli anulujesz publikowanie, wszelkie zmiany wprowadzone podczas publikowania również zostaną anulowane. 

### <a name="when-your-app-is-published"></a>Po opublikowaniu aplikacji

Po pomyślnym opublikowaniu aplikacji u góry strony w przeglądarce zostanie wyświetlone powiadomienie o powodzeniu zielony. Na pasku powiadomień zielony zawiera również link do punktów końcowych. 

![Publikowanie okna podręcznego z linkiem do punktu końcowego](./media/luis-how-to-publish-app/publish-success.png)

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz łącze. Możesz również przejść do adresów URL punktów końcowych, wybierając pozycję **Zarządzaj** w górnym menu, a następnie wybierając pozycję **zasoby platformy Azure** w menu po lewej stronie. 

## <a name="sentiment-analysis"></a>Analiza tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia LUIS w celu integracji z [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) Aby przeprowadzać analizę frazy tonacji i klucz. 

Nie trzeba podać klucz analizy tekstu, a nie są pobierane opłaty rozliczeń dla tej usługi do konta platformy Azure. 

Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych. Etykieta `positive`tonacji, `neutral`i `negative` jest na obsługiwaną kulturę. Obecnie tylko język angielski obsługuje etykiety tonacji. 

Aby uzyskać więcej informacji na temat odpowiedzi JSON punktu końcowego za pomocą analizy opinii, zobacz [analizy tonacji](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Poprawianie pisowni

Poprawki pisowni są wprowadzane przed przewidywaniem LUIS użytkownika wypowiedź. W odpowiedzi można zobaczyć zmiany w oryginalnym wypowiedź, w tym pisownię.

## <a name="speech-priming"></a>Napełnianiu mowy

Speech napełnianiu to proces polegający na wysyłaniu modelu LUIS do usługi mowy przed konwersją tekstu na mowę. Dzięki temu usługa mowy może dokładniej zapewnić konwersję mowy dla modelu. Umożliwia to bot LUIS i reagowanie żądań i odpowiedzi w jednym wywołaniu przez jedno wywołanie mowy i przejęcie odpowiedzi na LUIS. Zapewnia ona mniej czasu na ogół.

## <a name="next-steps"></a>Następne kroki

* Zobacz [zarządzanie kluczami](./luis-how-to-azure-subscription.md) dodać klucze do subskrypcji platformy Azure klucz do usługi LUIS sposób ustawiania sprawdzania pisowni Bing klucza i Dołącz wszystkie intencje w wynikach.
* Zobacz [szkolenie i testowanie aplikacji](luis-interactive-test.md) instrukcje na temat testowania Twojej opublikowanej aplikacji w konsoli testów.

