---
title: Rozwiązania jednostek w modelu Conversation Learner — Cognitive Services platformy Azure | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać metod rozpoznawania jednostek w Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704041"
---
# <a name="entity-resolvers"></a>Rozwiązania jednostek

W tym samouczku pokazano, jak używać metod rozpoznawania jednostek w Conversation Learner

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący elementów rozpoznawania jednostek — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Typ mechanizmu rozwiązywania konfliktów jest opcjonalną właściwością jednostek niestandardowych.
- Funkcje rozpoznawania jednostek wykorzystują możliwości wstępnie przeszkolonych aparatów rozpoznawania jednostek w LUIS, aby zapewnić dodatkowe szczegóły i przejrzystość dla jednostki niestandardowej.

## <a name="steps"></a>Kroki

Uruchom stronę główną w interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz pozycję **Nowy model**.
2. Wprowadź **jednostki rozpoznawania** **nazw**.
3. Wybierz pozycję **Utwórz**.

### <a name="create-a-pair-of-entities"></a>Tworzenie pary jednostek

1. Wybierz pozycję **jednostki** w lewym panelu, a następnie pozycję **Nowa jednostka**.
2. Wprowadź **wyjazd** dla **nazwy jednostki**.
3. Wybierz pozycję **datetimeV2** dla **typu mechanizmu rozwiązywania konfliktów**.
4. Wybierz pozycję **Utwórz**. Odrzuć podręczny komunikat, wybierając **przycisk OK**.
5. Powtórz kroki 1-4, aby utworzyć drugą jednostkę o nazwie **Return** z typem programu rozpoznawania **datetimeV2** .

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Tworzenie pary akcji

1. W lewym panelu wybierz pozycję **Akcje** , a następnie pozycję **Nowa akcja**.
2. Wprowadź **$Departure i wróć do $Return** na potrzeby **odpowiedzi bot...**
    - WAŻNE — podczas wpisywania w $ [EntityName] należy nacisnąć klawisz ENTER lub kliknąć jednostkę na liście rozwijanej, w przeciwnym razie Conversation Learner będzie to tekst zamiast jednostki.
    - Zwróć uwagę, że pole **wymagane jednostki** również pobierze te jednostki i nie będzie można ich usunąć. Zapobiega to udostępnieniu tej akcji do momentu obecności obu wymaganych jednostek.
3. Wybierz pozycję **Utwórz**.
4. Wybierz pozycję **Nowa akcja** , aby utworzyć drugą akcję.
5. Określ **, Kiedy planujesz podróż?** w przypadku **odpowiedzi bot...**
6. Wprowadź **wyjazd** i **Wróć** do niekwalifikujących się **jednostek**. Poinformuj nas o tym, że bot nie podejmuje tego działania, jeśli jedna z tych jednostek zawiera wartość.
7. Wybierz pozycję **Utwórz**.

![](../media/T09_actions.png)

### <a name="training"></a>Szkolenia

1. Obejrzyj **szkolenie: [status]** w lewym górnym rogu, aby **zakończyć**.
    - Możesz kliknąć link "Odśwież", jeśli trwa zbyt długo.
    - Stan szkolenia "ukończono" jest niezbędny, aby nasze resolvery jednostek działały podczas uczenia modelu.

2. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie kliknij przycisk "okno dialogowe nowego uczenia".
3. Wpisz w pierwszym użytkowniku wypowiedź, "książka ja lot". 
4. Kliknij przycisk "akcje oceny".
5. Wybierz odpowiedź "Kiedy planujesz podróż?".
6. W miarę jak użytkownik reaguje na ", pozostawiając jutro i zwracając niedzielę w następnym tygodniu".
    - Zwróć uwagę, jak Conversation Learner wykrył dwie jednostki "wcześniej przeszkolony" w tym użytkowniku.
7. W panelu "wykrywanie jednostek" Wybierz tekst "jutro" i oznacz go jako "Wyrusz"
8. Oznacz również tekst "Niedziela w następnym tygodniu" jako "return"
9. Kliknij przycisk "akcje oceny".
    - Zwróć uwagę, jak okienko "pamięć" zawiera daty wyjazdu i powrotu.
    - Umieść kursor nad każdym z nich i obserwuj, jak jednostki są obiektami dat, które wyraźnie przechwytują rzeczywistą datę kalendarzową zamiast "Niedziela" lub "jutro".
10. Wybierz "opuszczasz..." Bot odpowiedź.
11. Kliknij przycisk "Zapisz".

![](../media/T09_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jednostki wyliczające](./tutorial-enum-set-entity.md)
