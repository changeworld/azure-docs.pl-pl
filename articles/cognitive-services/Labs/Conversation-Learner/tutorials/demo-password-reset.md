---
title: Demonstracja Conversation Learner model, Resetowanie hasła Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak utworzyć model Conversation Learner demonstracyjnej.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703389"
---
# <a name="demo-password-reset"></a>Pokaz: Resetowanie hasła
W tym samouczku przedstawiono prostą pomoc techniczną bot, która może ułatwić resetowanie haseł obsługiwane przez Conversation Learner. Model bot może poznać nieuproszczone przepływy okien dialogowych i sekwencje wielostopniowe, w tym klasy spoza domeny. Zadanie można wykonać bez kodu lub jednostek.

## <a name="video"></a>Połączenia wideo

[![Podgląd hasła demonstracyjnego](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga uruchomienia bot resetowania hasła

    npm run demo-password

### <a name="open-the-demo"></a>Otwórz demonstrację

Na liście model interfejsu użytkownika sieci Web kliknij pozycję samouczek wersja przykładowa resetowania hasła. 

### <a name="actions"></a>Akcje

Model zawiera zestaw akcji zaprojektowanych w celu ułatwienia użytkownikom rozwiązywania typowych problemów z hasłem.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Okna dialogowe szkoleń

Model zawiera również kilka okien dialogowych uczenia, w tym niektóre z nich, które pokazują, że wystąpiło szkolenie z klasy domeny. Na przykład użytkownicy, którzy mogą zażądać takich wskazówek. Przykładowa bota została przeszkolony na kilka w celach demonstracyjnych, a po prostu odpowiada. Lista istniejących okien dialogowych uczenia znajduje się w obszarze "okna dialogowe szkolenia" w lewym panelu.

![](../media/tutorial_pw_reset_entities.PNG)

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "hasło" zostało utracone.
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "czy jest to konto lokalne czy konto Microsoft?".
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "konto lokalne"
6. Kliknij przycisk "akcje oceny".
7. Wybierz odpowiedź ("która wersja systemu Windows jest dostępna?")
8. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz w "Windows XP"
9. Kliknij przycisk "akcje oceny".
10. Kliknij przycisk "+ Akcja".
11. W odpowiedzi "bot"... " Wpisz "Rozwiązanie: Jak zresetować hasło w systemie Windows XP...
12. Kliknij przycisk "Utwórz".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Okna dialogowe szkoleń dla scenariuszy niezwiązanych z domeną

1. Na panelu po lewej stronie kliknij pozycję "szkolenie" okna dialogowego, a następnie w oknie dialogowym uczenie "zabawki sklepy".
2. W panelu rozmowa kliknij pozycję "zabawki sklepy" wypowiedź.
3. W polu "Dodaj alternatywne dane wejściowe..." Wpisz ciąg "wyszukiwanie w sieci Web" i naciśnij klawisz ENTER.
4. W polu "Dodaj alternatywne dane wejściowe..." Wpisz "rezerwacja na samolot" i naciśnij klawisz ENTER.
5. Kliknij przycisk "Zapisz zmiany".
6. Kliknij przycisk "Zapisz Edytuj".
7. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie przycisk "nowe okno dialogowe rejestrowania".
8. W panelu rozmowy, gdzie mówi "wpisz wiadomość...", wpisz "nie mogę znaleźć mojego hasła"
9. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "konto Microsoft"
10. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Dziękujemy"
11. Kliknij przycisk "gotowe do testowania".
12. Kliknij okno dialogowe "nie mogę znaleźć mojego hasła" w widoku siatki.
13. W panelu rozmowa kliknij niepoprawnie renderowane rozwiązanie: Jak zresetować odpowiedź na koncie Microsoft.
14. Kliknij przycisk "+ Akcja".
15. W odpowiedzi "bot"... " Wpisz "Jesteś Witaj"
16. Kliknij przycisk "Utwórz".
17. Kliknij przycisk "Zapisz jako okno dialogowe".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demonstracja — zamówienie Pizza](./demo-pizza-order.md)
