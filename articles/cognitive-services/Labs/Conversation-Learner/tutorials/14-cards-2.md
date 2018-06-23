---
title: Jak używać kart z aplikacją uczeń konwersacji, część 2 - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać kart z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 254f0953fd3e281a35857e69d9795e3decebf45d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348593"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak używać kart (część 1 z 2)
W tym samouczku przedstawiono sposób dodawania karty wypełniania do Twojej bot. Zostaną wyświetlone, jak przenieść pola formularza do jednostek.

Uczeń konwersacji oczekuje jako znajdujący się w katalogu o nazwie "karty", który znajduje się w katalogu, w którym jest uruchomiony bot karty plików definicji.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty są elementy interfejsu użytkownika, które umożliwiają użytkownikowi na wybranie opcji w konwersacji. 

### <a name="open-the-demo"></a>Otwórz pokaz

Na liście aplikacji w interfejsie użytkownika sieci web kliknij samouczek-14-kart-2. 

### <a name="the-card"></a>Karta

Definicja karty jest w następującej lokalizacji: C:\<installedpath\>\src\cards\shippingAddress.json.

Ta karta służy do zbierania trzy pola Adres wysyłkowy: Miasto, ulica i stanu.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Akcje

Utworzono trzy czynności. Jak widać poniżej pierwszą akcją jest kartą.

![](../media/tutorial14_actions.PNG)

Zobaczmy, jak został utworzony typ akcji karty:

- Zwróć uwagę, Ulica adresu, których typem jest Input.text i jej identyfikatora.
- Podobnie jest adres miejscowość i lista rozwijana z Identyfikatora elementu-stan adresu.

Identyfikatory są ważne, gdy pola są wypełnione i przesłane, te są nazwy jednostek, które mają otrzymywać te wartości w bot.

## <a name="entities"></a>Jednostki
Zdefiniowaniu trzy jednostki dopasowania karty jako widzieliśmy powyżej.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Akcje

Firma Microsoft zostały zdefiniowane dwie akcje.

![](../media/tutorial14_actions.PNG)

- Pierwsza to wysyłanie karty adres, gdzie typ akcji to karty, a wybrano szablon z listy rozwijanej jako shippingAddress.
- Drugim jest proste akcji odczytywania adres wysyłkowy.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Okno dialogowe pociągu

Przejdźmy nauczania okna dialogowego.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "hi".
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać "Adres wysyłkowy".
4. Wypełnij karty i przesyłania.
    - Należy zauważyć, że te wartości teraz zostały przeniesione do pamięci jednostki. Analizy nie jest niezbędne, jak formularz już na partycje danych wejściowych.
5. Kliknij przycisk wynik akcji.
3. Kliknij opcję "Wysyłanie $Address...".
4. Kliknij przycisk Done testowania.

![](../media/tutorial14_train_dialog.PNG)

Jak można pobrać wartości z karta, która zawiera interaktywnych pola i listę rozwijaną, a w celu przechwycenia i połączyć je w bot jednostek ma teraz widoczna.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozgałęzianie i Cofnij](./15-branching-and-undo.md)
