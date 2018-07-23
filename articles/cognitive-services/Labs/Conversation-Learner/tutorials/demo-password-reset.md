---
title: Pokaz uczeń konwersacji modelu resetowania haseł — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji pokaz.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170637"
---
# <a name="demo-password-reset"></a>Pokaz: Resetowanie hasła
Ten pokaz ilustruje bot proste pomocy technicznej, pomagające w operacji resetowania hasła. 

Pokazuje, jak uczeń konwersacji można dowiedzieć się więcej przepływów nietrywialnymi okna dialogowego, Włącz wielu sekwencji znaków, łącznie z klasą poza domeną. Tej prezentacji nie używa żadnych kodu lub jednostki.

## <a name="video"></a>Połączenia wideo

[![Pokaz hasła w wersji zapoznawczej](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że bot resetowania hasła jest uruchomiony

    npm run demo-password

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij resetowania hasła pokaz samouczka. 

### <a name="actions"></a>Akcje

Utworzyliśmy zestaw akcji, gdy użytkownik szuka pomocy przy użyciu swojego hasła, w tym rozwiązań.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych

Istnieje wiele okien dialogowych szkolenia. Dostępne są również pokazy poza klasą domeny — na przykład użytkownik żądania, takie jak "dojazdu" są spoza domeny. bot podano przykłady kilka poza żądania w domenie i mogą odpowiadać za pomocą "I nie będzie mogła pomóc korzystając z niego."

![](../media/tutorial_pw_reset_entities.PNG)

Na przykład Wypróbujmy sesji nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "Utratą hasła".
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Jest to, że dla konta lokalnego lub konta Microsoft?"
4. Wprowadź "Konto".
5. Kliknij wynik akcji.
3. Kliknij, aby wybrać "wersji systemu Windows masz?"
4. Wprowadź "systemu Windows 8'.
5. Kliknij wynik akcji.
6. Wybierz pozycję "rozwiązania: jak zresetować hasło w systemie Windows 8."
4. Kliknij przycisk Gotowe, nauczania.

Poniżej przedstawiono, jak bot znajdziesz klasę poza domeną.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "wyszukiwania w sieci web".
    - Jest to przykład klasy poza domeną. 
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać "Niestety, I nie będzie mogła pomóc, korzystając z niego".
    - Należy zauważyć, że wynik dla tej opcji jest aktualnie niska. Ale po nieco więcej nauczania, wynik będzie wyższa.
4. Kliknij przycisk Gotowe, nauczania.

Teraz wiesz, jak utworzyć pokaz podstawowa pomoc techniczna i jak znajdziesz do rozwiązania, a także obsługiwać poza przykładowe zapytania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — kolejność pizza](./demo-pizza-order.md)
