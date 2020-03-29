---
title: 'Jak: Dodawanie jednoetapowej korekty do polecenia niestandardowego (Podgląd) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniamy, jak zaimplementować jednoetapowe poprawki dla polecenia w poleceniach niestandardowych.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456455"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Jak: Dodawanie jednoetapowej korekty do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać jednoetapowe potwierdzenie do polecenia.

Korekta jednoetapowa służy do aktualizowania polecenia, które zostało właśnie ukończone.

To znaczy, jeśli po prostu skonfigurować alarm, można zmienić zdanie i zaktualizować czas alarmu.

- Wejście: Ustaw alarm na jutro w południe
- Wyjście: "Ok, alarm ustawiony na 12/06/2019 12:00:00"
- Wejście: Nie, jutro o 13:00
- Wyjście: "Ok

Należy pamiętać, że oznacza to, że jako deweloper masz mechanizm aktualizacji alarmu w aplikacji wewnętrznej bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Kroki w następujących artykułach należy wykonać:

- [Szybki start: tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
- [Jak: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Dodawanie zaawansowanych reguł dla korekty jednoetapowej 

Aby zademonstrować jednoetapową korektę, rozszerzmy polecenie **SetAlarm** utworzone w [ydarzynie jak](./how-to-custom-speech-commands-confirmations.md).
 
1. Dodaj regułę zaawansowaną, aby zaktualizować poprzedni alarm. 

    Ta reguła poprosi użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) dla następnej tury.

   | Ustawienie               | Sugerowana wartość                                                  | Opis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Aktualizacja poprzedniego alarmu                                            | Nazwa opisująca cel reguły          |
   | Warunki            | UpdateLastCommand & wymagany parametr - DateTime                | Warunki określające, kiedy reguła może być uruchamiana    |   
   | Akcje               | SpeechResponse - "- Aktualizowanie poprzedniego alarmu do {DateTime}"       | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |
   | Stan po wykonaniu | Polecenie Zakończ                                                 | Stan użytkownika po zakręcie                   |

1. Przenieś utworzoną regułę na górę reguł zaawansowanych (przewiń regułę w panelu i kliknij strzałkę w górę).
   > [!div class="mx-imgBorder"]
   > ![Dodawanie sprawdzania poprawności zakresu](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> W rzeczywistej aplikacji w akcji sekcji tej reguły można również wysłać z powrotem działania do klienta lub wywołać punkt końcowy HTTP, aby zaktualizować alarm w systemie.

## <a name="try-it-out"></a>Testowanie

Wybierz panel Test i wypróbuj kilka interakcji.

- Wejście: Ustaw alarm na jutro w południe
- Wyjście: "Czy na pewno chcesz ustawić alarm na 12.07.2019 12:00:00?"
- Wejście: Tak
- Wyjście: "Ok, alarm ustawiony na 12.07.2019 12:00:00"
- Wejście: Nie, jutro o 13:00
- Wyjście: "Aktualizacja poprzedniego alarmu do 12/07/2019 13:00:00"
