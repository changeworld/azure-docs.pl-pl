---
title: Jak używać jednostek z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć jednostek z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348620"
---
# <a name="introduction-to-entities"></a>Wprowadzenie do jednostek

W tym samouczku wprowadza jednostki i przedstawia sposób użycia pola "Disqualifying jednostek" i "Wymaga jednostek" w akcji.

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły

W tym samouczku przedstawiono dwie typowe zastosowania jednostek.  Po pierwsze jednostek można wyodrębnić podciągów z komunikat użytkownika, np. zidentyfikowanie Miejscowość w "nowości pogody w Seattle".  Po drugie jednostek ograniczyć podczas akcje są dostępne.  W szczególności akcji można wyświetlić listę jednostki jako trwa "wymagane" lub "dyskwalifikacji":
- Akcja wymagana jednostek musi znajdować się w pamięci bot w kolejności dla akcji, które mają być dostępne
- Dyskwalifikacji jednostek musi *nie* znajdować się w pamięci bot w kolejności dla akcji, które mają być dostępne

Innych samouczków opisano inne aspekty jednostki, na przykład jednostek wbudowanych, wielowartościowych można negować jednostek, programistyczny jednostek i manipulowania jednostek w kodzie.

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź IntroToEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-entity"></a>Utwórz jednostkę

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź miasta.
3. Kliknięcie pozycji Utwórz

Uwaga typ jednostki jest "custom", ponieważ oznacza to, że jednostka może być uczony.  Istnieją jednostki wbudowanych, co oznacza, że nie można dostosować ich zachowanie — są one zawarte w samouczku innego.

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "Nie wiadomo jakim mieście ma".
3. W dyskwalifikacji jednostek wprowadź $city. Kliknij przycisk Zapisz.
    - Oznacza to, że jeśli ta jednostka jest zdefiniowany w pamięci w bot, następnie ta akcja będzie *nie* być dostępne.
2. Kliknij przycisk akcje, a następnie nową akcję w celu utworzenia drugiej akcji.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie słoneczna".
4. Wymagane jednostek Zanotuj czy Miasto jednostki został dodany automatycznie od zostało określone.
5. Klikanie pozycji Zapisz.

Teraz masz dwie akcje.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "I nie wiadomo, jakim mieście ma?"
    - Należy pamiętać, że odpowiedzi, gdy wymagane jest jednostka miasta nie można wybrać, ponieważ jednostki miasta nie jest zdefiniowany w bot w pamięci.
2. Wybierz opcję "Nie wiadomo jakim mieście ma".
4. Wprowadź "seattle". Wyróżnij seattle, a następnie kliknij przycisk miasta.
5. Kliknij przycisk wynik akcji
    - Uwaga Miasto wartość znajduje się w pamięci bot.
    - "Pogody w $city jest prawdopodobnie słoneczna" jest teraz dostępna jako odpowiedź. 
6. Wybierz opcję "Pogody w $city jest prawdopodobnie słoneczna".

Załóżmy, że użytkownik wprowadza "repeat, który". 
1. Który typ, a następnie wprowadź. Należy pamiętać, że Miasto jednostki i jej wartość w pamięci i jest dostępny.
2. Wybierz opcję "Pogody w $city jest prawdopodobnie słoneczna".

![](../media/tutorial3_entities.PNG)

Teraz utworzeniu jednostki oraz wystąpień w komunikatach użytkownika z etykietą.  Również używano obecności/braku jednostki bot w pamięci w celu sterowania podczas akcje są dostępne za pośrednictwem dyskwalifikacji akcji i jednostek wymaganych pól.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Oczekiwano jednostki](./4-expected-entity.md)
