---
title: Jak używać kart przy użyciu modelu uczeń konwersacji, część 2 — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać kart przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99c474978d4da1d80669505330b2dc6220d7ca5f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226638"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak używać kart (część 1 z 2)
W tym samouczku pokazano, jak dodać kartę wypełniania do bota. Będzie wyświetlany, w jaki sposób pola formularza przenieść do jednostki.

Uczeń konwersacji oczekuje, że pliki definicji karty muszą znajdować się w katalogu o nazwie "kart", który jest obecny w katalogu, w którym jest uruchomiona bota.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący 14 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-14-preview)](https://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty są elementy interfejsu użytkownika, które umożliwiają użytkownikom wybrać określoną opcję w konwersacji. 

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web należy kliknąć samouczek-14-kart-2. 

### <a name="the-card"></a>Karta

Definicja karty jest w następującej lokalizacji: C:\<installedpath\>\src\cards\shippingAddress.json.

Ta karta umożliwia zbieranie informacji o trzy pola adresu wysyłkowego: city, ulicy i stanu.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Akcje

Przygotowaliśmy trzy czynności. Jak widać poniżej pierwszej akcji jest kartą.

![](../media/tutorial14_actions.PNG)

Sprawdźmy, tworzenia karty typ akcji:

- Zwróć uwagę, adres ulicy, których typem jest Input.text i jego identyfikatora.
- Podobnie istnieje adres, miasto i menu rozwijane za pomocą Identyfikatora elementu — stan adresu.

Identyfikatory są ważne, ponieważ gdy pola są wypełniane i przesyłane, te nazwy jednostki, które będą otrzymywać te wartości w bota.

## <a name="entities"></a>Jednostki
Zdefiniowaliśmy trzech jednostek dopasowania karty, ponieważ był widoczny powyżej.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Akcje

Zdefiniowaliśmy dwie akcje.

![](../media/tutorial14_actions.PNG)

- Pierwszy jest wysyłanie karty adresu, gdzie typ akcji to karty, a z listy rozwijanej jako shippingAddress zaznaczony jest szablon.
- Drugim jest prostej akcji odczytywania adresu do wysyłki.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Okno dialogowe szkolenie

Przejdźmy teraz przez okno dialogowe nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "Cześć".
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Adres wysyłkowy".
4. Wypełnij karty, a następnie przesłać.
    - Zauważ, że teraz te wartości zostały przeniesione do pamięci jednostki. Analiza kodu nie jest wymagany, ponieważ formularz już podzielony na partycje dane wejściowe.
5. Kliknij wynik akcji.
3. Kliknij opcję "Wysyłanie $Address...".
4. Kliknij przycisk Done testowania.

![](../media/tutorial14_train_dialog.PNG)

Teraz wiesz jak pobierają wartości z karty do wypełniania pól i list rozwijanych, a przechwytywania i połączyć je w jednostkach botów.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozgałęzianie działań i cofania](./15-branching-and-undo.md)
