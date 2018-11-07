---
title: Sposób użycia jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak i używanie jednostek z modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4b1c2d9390890618a9aa61eb425fbd132917f414
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229052"
---
# <a name="introduction-to-entities"></a>Wprowadzenie do jednostek

W tym samouczku wprowadza jednostki, a ilustruje sposób używania pola "Disqualifying jednostki" i "Required jednostki" w akcji.

## <a name="video"></a>Połączenia wideo

[![Samouczek 3 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-03-preview)](https://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Wymagania

Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

W tym samouczku przedstawiono dwie typowe zastosowania jednostek.  Po pierwsze jednostki można wyodrębnić podciągi z wiadomości do użytkownika, np. zidentyfikowanie miasta w "co to jest pogody w Seattle?".  Jeśli akcje są dostępne po drugie, ograniczyć jednostek.  W szczególności akcji można wyświetlić listę jednostki są "required" lub "dyskwalifikacji":
- Akcja wymaganych jednostek musi znajdować się w pamięci botów w kolejności dla akcji, które mają być dostępne
- Dyskwalifikacji jednostki musi *nie* znajdować się w pamięci botów w kolejności dla akcji, które mają być dostępne

Inne samouczki dotyczą innych aspektów jednostek, takich jak wstępnie utworzone jednostki, wielowartościowych można negować jednostek, programowe jednostek i manipulowania jednostek w kodzie.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź IntroToEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu Wprowadź miasto.
3. Kliknięcie pozycji Utwórz

> [!NOTE]
> Typ jednostki jest "custom" — oznacza to, że jednostki może być uczony.  Istnieją wstępnie utworzone jednostki, co oznacza, że nie można go zmienić ich zachowania — zostały one omówione w innym samouczku.

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi wpisz "Nie wiem, jakim mieście ma".
3. W jednostkach dyskwalifikacji wprowadź $city. Kliknij pozycję Zapisz.
    - Oznacza to, że jeśli ta jednostka jest zdefiniowany w pamięci botów, następnie ta akcja spowoduje *nie* być dostępne.
2. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie sunny".
4. W jednostkach wymagane Miasto jednostki został dodany automatycznie od zostało określone.
5. Klikanie pozycji Zapisz.

Masz teraz dwie akcje.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Nie wiem, jakim mieście ma?"
    - Nie można wybrać odpowiedź, gdy jednostka Miasto jest wymagane, ponieważ jednostka miasta nie jest zdefiniowany w pamięci botów.
2. Wybierz opcję "Nie wiem, jakim mieście ma".
4. Wprowadź "seattle". Wyróżnij seattle, a następnie kliknij miasta.
5. Kliknij wynik akcji
    - Wartości City znajduje się w pamięci botów.
    - "Pogody w $city jest prawdopodobnie sunny" jest teraz dostępna w odpowiedzi. 
6. Wybierz pozycję "Pogody w $city jest prawdopodobnie sunny".

Załóżmy, że użytkownik musi wprowadzić "repeat, który". 
1. Typu, a następnie wprowadź. Miasto jednostki i jego wartość jest w pamięci.
2. Wybierz pozycję "Pogody w $city jest prawdopodobnie sunny".

![](../media/tutorial3_entities.PNG)

Masz teraz utworzone jednostki i etykietą wystąpień w komunikatach użytkownika.  Również użyty obecności/Brak jednostki botów w pamięci w celu sterowania podczas akcje są dostępne, przy użyciu pól wymaganych jednostek i dyskwalifikacji akcji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Oczekiwanej jednostki](./4-expected-entity.md)
