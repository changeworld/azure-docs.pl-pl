---
title: Rozwiązujący jednostki w modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak korzystać z rozpoznawania nazw jednostki w uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7b5f5645a8b5d1d993f87d18503fe270f42c5de5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390029"
---
# <a name="entity-resolvers"></a>Mechanizmy rozpoznawania elementów jednostki

W tym samouczku pokazano, jak używać mechanizmów rozpoznawania jednostek w uczeń konwersacji

## <a name="video"></a>Połączenia wideo

[![Jednostki rozwiązujący samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że ogólne samouczek Bot działa

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Typ mechanizmu rozpoznawania jest opcjonalna właściwość jednostki niestandardowe.
- Rozwiązujący jednostki wykorzystywać możliwości wstępnie przeszkolonych jednostki aparatów rozpoznawania w LUIS, aby podać dodatkowe szczegóły i przejrzystości dla jednostki niestandardowe.

## <a name="steps"></a>Kroki

Rozpocznij na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-model"></a>Tworzenie modelu

1. Wybierz **nowy Model**.
2. Wprowadź **rozwiązujący jednostki** dla **nazwa**.
3. Wybierz pozycję **Utwórz**.

### <a name="create-a-pair-of-entities"></a>Tworzenie pary jednostek

1. Wybierz **jednostek** w lewym panelu, a następnie **Nowa jednostka**.
2. Wprowadź **wyjścia** dla **nazwa jednostki**.
3. Wybierz **datetimeV2** dla **typ mechanizmu rozpoznawania**.
4. Wybierz pozycję **Utwórz**. Odrzuć informacyjne okno podręczne, wybierając **OK**.
5. Powtórz kroki 1 – 4, aby utworzyć drugi jednostkę o nazwie **zwracają** z **datetimeV2** typ mechanizmu rozpoznawania.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Tworzenie pary akcji

1. Wybierz **akcje** w lewym panelu, a następnie **nowa akcja**.
2. Wprowadź **są pozostawienia na $departure i na $return** dla **odpowiedzi Botów...** .
    - WAŻNE — podczas wpisywania w $[entityName] musisz trafień enter lub kliknij jednostkę, w menu rozwijanym w przeciwnym razie uczeń konwersacji będzie należy wziąć pod uwagę tego działania jako tekst, a nie jednostki.
    - Należy zauważyć, że **wymaganych jednostek** pola otrzymają również te jednostki i nie można usunąć. Ta akcja staje się dostępne, dopóki oba wymaganych jednostek znajdują się w ten sposób.
3. Wybierz pozycję **Utwórz**.
4. Wybierz **nowa akcja** do utworzenia drugiej akcji.
5. Wprowadź **kiedy są planowane do pokonania?** dla **odpowiedzi Botów...** .
6. Wprowadź **wyjścia** i **zwracają** dla **dyskwalifikacji jednostek**. Wie, nasze Bot nie wykonywała tę akcję w przypadku dowolnego z tych jednostek zawiera wartość.
7. Wybierz pozycję **Utwórz**.

![](../media/T09_actions.png)

### <a name="training"></a>Szkolenia

1. Obejrzyj **szkolenia: [Status]** w lewym górnym rogu, aby uzyskać **Ukończono**.
    - Możesz kliknąć link "Odśwież", jeśli trwa to zbyt długo.
    - Szkolenie stan "Completed" to naszym rozwiązujący jednostki pracy po uczenie modelu.

2. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie kliknij przycisk "Nowy Train Dialog".
3. Typ w pierwszym wypowiedź użytkownika, "Zarezerwuj mnie lot". 
4. Kliknij przycisk "Wynik akcje".
5. Wybierz odpowiedź "Kiedy są planowane do pokonania?".
6. Jako użytkownik należy odpowiedzieć, "opuszczania jutro i zwracanie niedziela w następnym tygodniu".
    - Zwróć uwagę, jak uczeń konwersacji wykrył, że włączenie dwie jednostki "Wstępnie przeszkolonych daty" tego użytkownika.
7. W panelu "Jednostki wykrywania" "jutro" Zaznacz tekst i przypisuj jej etykiety jako "wyjścia"
8. Opisać tekst "Niedziela w następnym tygodniu" jako "Wróć"
9. Kliknij przycisk "Wynik akcje".
    - Zwróć uwagę, jak okienko "Pamięć" zawiera wyjścia i zwrócenie daty.
    - Umieść kursor nad każdym z nich i obserwuj, jak jednostki są obiekty daty, które wyraźnie przechwytywania datę w kalendarzu rzeczywiste zamiast "Niedziela" lub "jutro".
10. Wybierz pozycję "Opuszczasz na..." Bot odpowiedź.
11. Kliknij przycisk "Zapisz".

![](../media/T09_training.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne danych wejściowych](./10-alternative-inputs.md)
