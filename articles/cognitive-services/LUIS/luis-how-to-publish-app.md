---
title: Publikowanie aplikacji — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Po zakończeniu tworzenia i testowania aktywnej aplikacji usługi LUIS, udostępnić ją aplikacji klienckiej, publikując go w punkcie końcowym.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053434"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji w punkcie końcowym przejściowym lub produkcyjnym

Po zakończeniu tworzenia, szkolenia i testowania aktywnej aplikacji usługi LUIS, udostępnić ją do aplikacji klienckiej, publikując go w punkcie końcowym. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publikowanie

1. Aby opublikować w punkcie końcowym, wybierz **pozycję Publikuj** w prawym górnym panelu. 

    ![Przycisk Publikowania w prawym górnym, prawym pasku nawigacyjnym](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Wybierz ustawienia opublikowanego punktu końcowego prognozowania, a następnie wybierz pozycję **Publikuj**.

    ![Wybierz pozycję Ustawienia publikowania, a następnie wybierz przycisk Publikuj](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Miejsca do publikowania

Wybierz odpowiednie gniazdo, gdy pojawi się wyskakujące okno: 

* Przygotowanie
* Produkcja 

Przy użyciu obu gniazd publikowania, dzięki temu można mieć dwie różne wersje aplikacji dostępne w opublikowanych punktów końcowych lub tej samej wersji na dwóch różnych punktów końcowych. 

### <a name="publishing-regions"></a>Regiony publikowania

Aplikacja jest publikowana we wszystkich regionach skojarzonych z zasobami punktu końcowego przewidywania usługi LUIS dodanymi w portalu usługi LUIS ze strony **Zarządzanie zasobami** -> **[platformy Azure.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Na przykład dla aplikacji utworzonej na [www.luis.ai](https://www.luis.ai), jeśli utworzysz zasób usługi LUIS w dwóch regionach, **westus** i **eastus**, i dodać je do aplikacji jako zasoby, aplikacja jest publikowana w obu regionach. Aby uzyskać więcej informacji na temat regionów usługi LUIS, zobacz [Regiony](luis-reference-regions.md).

> [!TIP]
> Istnieją 3 regiony tworzenia. Musisz jest autor w regionie, w których chcesz opublikować. Jeśli chcesz opublikować we wszystkich regionach, musisz zarządzać procesem tworzenia i wynikowym przeszkolonym modelem we wszystkich 3 regionach tworzenia. 


## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Po wybraniu gniazda należy skonfigurować ustawienia publikowania dla:

* Analiza tonacji
* Korekta pisowni — tylko punkt końcowy prognozowania wersji 2
* Gruntowanie mowy 

Po opublikowaniu te ustawienia są dostępne do sprawdzenia na stronie **Zarządzanie ustawieniami** **publikowania** w sekcji. Ustawienia można zmieniać przy każdym publikowaniu. Jeśli anulujesz publikację, wszelkie zmiany wprowadzone podczas publikowania również zostaną anulowane. 

### <a name="when-your-app-is-published"></a>Po opublikowaniu aplikacji

Po pomyślnym opublikowaniu aplikacji w górnej części przeglądarki pojawi się powiadomienie o powodach. Powiadomienie zawiera również łącze do punktów końcowych. 

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz łącze. Możesz również uzyskać widok na adresy URL punktów końcowych, wybierając pozycję **Zarządzaj** w górnym menu, a następnie wybierz pozycję **Zasoby platformy Azure** w menu po lewej stronie. 

## <a name="sentiment-analysis"></a>Analiza tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia integrowanie usługi LUIS z [analizą tekstu w](https://azure.microsoft.com/services/cognitive-services/text-analytics/) celu zapewnienia analizy tonacji i kluczowych fraz. 

Nie trzeba podawać klucz analizy tekstu i nie ma żadnych opłat rozliczeniowych za tę usługę do konta platformy Azure. 

Dane o nastrojach to wynik od 1 do 0 wskazujący pozytywny (bliżej 1) lub ujemny (bliżej 0) sentyment danych. Etykieta `positive`sentymentu `neutral`, `negative` i jest na obsługiwane kultury. Obecnie tylko język angielski obsługuje etykiety tonacji. 

Aby uzyskać więcej informacji na temat odpowiedzi punktu końcowego JSON z analizą tonacji, zobacz [Analiza tonacji](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Korekta pisowni

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Poprawki pisowni są dokonywane przed przewidywanie wypowiedź użytkownika usługi LUIS. W odpowiedzi można zobaczyć wszelkie zmiany oryginalnego wypowiedź, w tym pisownię.

## <a name="speech-priming"></a>Gruntowanie mowy

Tworzenie witryny rozpoznawania mowy to proces wysyłania modelu usługi LUIS do usług mowy przed konwersją tekstu na mowę. Dzięki temu usługa mowy, aby zapewnić konwersji mowy dokładniej dla modelu. Dzięki temu bot mowy i usługi LUIS żądań i odpowiedzi w jednym wywołaniu, wykonując jedno wywołanie mowy i powrót odpowiedzi usługi LUIS. Zapewnia mniejsze opóźnienie ogólnej.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Zarządzanie kluczami,](./luis-how-to-azure-subscription.md) aby dodać klucze subskrypcji platformy Azure do usługi LUIS i jak ustawić klucz sprawdzania pisowni Bing i uwzględnić wszystkie intencje w wynikach.
* Zobacz [Szkolenie i przetestuj aplikację,](luis-interactive-test.md) aby uzyskać instrukcje dotyczące testowania opublikowanej aplikacji w konsoli testowej.

