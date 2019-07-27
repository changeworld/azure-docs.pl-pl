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
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: fdbdb2cd13d131019c4230aa369af31240d25f0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563653"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji do tymczasowego lub produkcyjnego punktu końcowego

Po zakończeniu tworzenia i testowania aplikacją usługi LUIS active był dostępny do aplikacji klienckiej, publikując je do punktu końcowego. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikowanie

Aby opublikować punkt końcowy, wybierz **Publikuj** w górnym rogu, kliknij prawym przyciskiem myszy panel. 

![Pasek nawigacji górnej, prawej](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Wybierz prawidłowe miejsce, gdy jest wyświetlana w oknie podręcznym: przejściowych lub produkcyjnych. Korzystając z dwóch miejsc publikowania, dzięki temu można mieć dwie różne wersje z punktami końcowymi opublikowane lub tej samej wersji na dwa różne punkty końcowe. 

Aplikacja została opublikowana do wszystkich regionów skojarzonych z zasobami usługi LUIS dodane w portalu usługi LUIS. Na przykład aplikacja utworzona na [www.luis.ai](https://www.luis.ai), jeśli utworzysz zasób usługi LUIS w **westus** i dodać go do aplikacji jako zasób, aplikacja zostanie opublikowana w danym regionie. Aby uzyskać więcej informacji na temat usługi LUIS regionów, zobacz [regionów](luis-reference-regions.md).
 
![Publikowanie okna podręcznego](./media/luis-how-to-publish-app/publish-pop-up.png)

Po pomyślnym opublikowaniu aplikacji u góry strony w przeglądarce zostanie wyświetlone powiadomienie o powodzeniu zielony. Na pasku powiadomień zielony zawiera również link do punktów końcowych. 

![Publikowanie okna podręcznego z linkiem do punktu końcowego](./media/luis-how-to-publish-app/publish-success.png)

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz łącze. Można także uzyskać adresy URL punktu końcowego, wybierając **Zarządzaj** w górnym menu, a następnie zaznacz **kluczy i punktów końcowych** w menu po lewej stronie. 

## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Konfigurowanie ustawień publikowania, wybierając **Zarządzaj** w górnym rogu, kliknij prawym przyciskiem myszy nawigacji, a następnie wybierając **ustawień publikowania**. 

![Ustawienia publikowania](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publikuj po włączeniu analizy tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia LUIS w celu integracji z [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) Aby przeprowadzać analizę frazy tonacji i klucz. 

Nie trzeba podać klucz analizy tekstu, a nie są pobierane opłaty rozliczeń dla tej usługi do konta platformy Azure. Jeśli zaznaczysz to ustawienie, jest trwały. 

Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych. Etykieta `positive`tonacji, `neutral`i `negative` jest na obsługiwaną kulturę. Obecnie tylko język angielski obsługuje etykiety tonacji. 

Aby uzyskać więcej informacji na temat odpowiedzi JSON punktu końcowego za pomocą analizy opinii, zobacz [analizy tonacji](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [zarządzanie kluczami](./luis-how-to-azure-subscription.md) dodać klucze do subskrypcji platformy Azure klucz do usługi LUIS sposób ustawiania sprawdzania pisowni Bing klucza i Dołącz wszystkie intencje w wynikach.
* Zobacz [szkolenie i testowanie aplikacji](luis-interactive-test.md) instrukcje na temat testowania Twojej opublikowanej aplikacji w konsoli testów.

