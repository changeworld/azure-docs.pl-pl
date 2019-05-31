---
title: Pokaz uczeń konwersacji modelu resetowania haseł — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model uczeń konwersacji pokaz.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 15aa3a8346087908cf77f1f68db916cc2c184448
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389870"
---
# <a name="demo-password-reset"></a>Pokaz: Resetowanie hasła
W tym samouczku przedstawiono bot proste pomocy technicznej, pomagające w operacji resetowania hasła, obsługiwane przez uczeń konwersacji. Model bot znajdziesz przepływy nietrywialnymi okna dialogowego i Włącz wielu sekwencji, w tym klasy poza domeną. Zadanie można wykonać bez kodu lub jednostki.

## <a name="video"></a>Połączenia wideo

[![Pokaz hasła w wersji zapoznawczej](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że bot resetowania hasła jest uruchomiony

    npm run demo-password

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij resetowania hasła pokaz samouczka. 

### <a name="actions"></a>Akcje

Model zawiera zestaw działania mające na celu pomóc użytkownikom w rozwiązywaniu typowych problemów związanych z hasłami.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych

Model zawiera także kilka Train okien dialogowych, łącznie z niektórych funkcji, które pokazują poza szkolenia klasy domeny. Na przykład użytkownicy, którzy mogą zażądać, takich jak dojazdu. Bot przykładowe wyszkoliła został na kilka dla celów demonstracyjnych i po prostu współpracuje poprzez podanie jego "nie może pomóc korzystając z niego." Lista istniejących okien dialogowych Train znajduje się w folderze "Okien dialogowych Train" w panelu po lewej stronie.

![](../media/tutorial_pw_reset_entities.PNG)

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "i utraty hasła".
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Jest to, że dla konta lokalnego lub konta Microsoft?"
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "lokalne konto."
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "wersji systemu Windows masz?"
8. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "windows xp"
9. Kliknij przycisk "Wynik akcje".
10. Kliknij przycisk "+ akcji" przycisk.
11. W "Botów odpowiedzi...", wpisz "rozwiązania: Jak zresetować hasło w systemie Windows XP..."
12. Kliknij przycisk "Utwórz".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Okna dialogowe szkolenia w scenariuszach poza domeną

1. Na lewym panelu, kliknij przycisk "Okien dialogowych Train", a następnie istniejących "zabawkami magazynów" Train okna dialogowego.
2. W panelu rozmowy kliknij wypowiedź "zabawki stores".
3. W polu "Dodaj alternatywne dane wejściowe..." typu "Wyszukiwanie w sieci web" i naciśnij klawisz należy wprowadzić.
4. W polu "Dodaj alternatywne dane wejściowe..." typu "rezerwacji lotów" i naciśnij klawisz należy wprowadzić.
5. Kliknij przycisk "Zapisz zmiany".
6. Kliknij przycisk "Edit Zapisz".
7. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie przycisk "Nowy dziennik Dialog".
8. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "i nie można odnaleźć moje hasło"
9. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Konto Microsoft"
10. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Dziękujemy"
11. Kliknij przycisk "Testowanie gotowe".
12. Kliknij okno Dziennik "i nie można odnaleźć hasła" z widoku siatki.
13. W panelu rozmowy kliknij niepoprawnie renderowany "rozwiązania: Jak zresetować hasło do konta Microsoft"odpowiedzi.
14. Kliknij przycisk "+ akcji" przycisk.
15. W "Botów odpowiedzi..." pola, wpisz "Zachęcamy"
16. Kliknij przycisk "Utwórz".
17. Kliknij przycisk "Zapisz jako Train Dialog".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — kolejność pizza](./demo-pizza-order.md)
