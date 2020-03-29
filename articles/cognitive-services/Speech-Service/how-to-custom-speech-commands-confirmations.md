---
title: 'Jak: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)'
titleSuffix: Azure Cognitive Services
description: W tym artykule, jak implementuje potwierdzenia dla polecenia w poleceniach niestandardowych.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456499"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Jak: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać potwierdzenie do polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

Kroki w następujących artykułach należy wykonać:

- [Szybki start: tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Tworzenie polecenia SetAlarm

Aby zademonstrować sprawdzanie poprawności, utwórzmy nowe polecenie umożliwiające użytkownikowi ustawienie alarmu.

1. Otwórz wcześniej utworzoną aplikację Polecenia niestandardowe w [Studio mowy](https://speech.microsoft.com/)
1. Tworzenie nowego **zestawu poleceńAlarm**
1. Dodawanie parametru o nazwie DateTime

   | Ustawienie           | Sugerowana wartość                                          | Opis                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nazwa              | DateTime                                                 | Opisowa nazwa parametru Command                                                    |
   | Wymagany          | true                                                     | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia |
   | Szablon odpowiedzi | "- O której godzinie?"                                           | Monit z prośbą o podanie wartości tego parametru, gdy nie jest znany                              |
   | Typ              | DateTime                                                 | Typ parametru, taki jak Liczba, Ciąg lub Data Ii                                      |
   | Domyślne wartości daty     | Jeśli daty brakuje użycia dzisiaj                             |                                                                                                  |
   | Ustawienia domyślne czasu     | Jeśli brakuje czasu, użyj początku dnia                      |                                                                                                  | 

1. Dodawanie przykładowych zdań
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Dodawanie reguły ukończenia w celu potwierdzenia wyniku

   | Ustawienie    | Sugerowana wartość                                         | Opis                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły  | Ustawianie alarmu                                               | Nazwa opisująca cel reguły          |
   | Akcje    | SpeechResponse - "- Ok, ustawiono alarm dla {DateTime}"       | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |

## <a name="try-it-out"></a>Testowanie

Wybierz panel Test i wypróbuj kilka interakcji.

- Wejście: Ustaw alarm na jutro w południe
- Wyjście: "Ok, alarm ustawiony na 12/06/2019 12:00:00"

- Wejście: Ustawianie alarmu
- Wyjście: "O której godzinie?"
- Wejście: 17:00
- Wyjście: "Ok, alarm ustawiony na 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Dodawanie zaawansowanych reguł do potwierdzenia

1. Dodaj regułę zaawansowaną do potwierdzenia. 

    Ta reguła poprosi użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) dla następnej tury.

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Potwierdź datę                                                                | Nazwa opisująca cel reguły          |
   | Warunki            | Wymagany parametr - DateTime                                                    | Warunki określające, kiedy reguła może być uruchamiana    |   
   | Akcje               | SpeechResponse - "- Czy na pewno chcesz ustawić alarm dla {DateTime}?"       | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |
   | Stan po wykonaniu | Poczekaj na dane wejściowe                                                                   | Stan użytkownika po kolei                  |
   | Oczekiwania          | Potwierdzenie                                                                     | Oczekiwanie na następną turę                      |

1. Dodaj regułę zaawansowaną, aby obsłużyć pomyślne potwierdzenie (użytkownik powiedział tak)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Zaakceptowane potwierdzenie                                                            | Nazwa opisująca cel reguły          |
   | Warunki            | Pomyślne Potwierdzenie & wymagany parametr - DateTime                           | Warunki określające, kiedy reguła może być uruchamiana    |   
   | Stan po wykonaniu | Gotowy do ukończenia                                                             | Stan użytkownika po zakręcie                   |

1. Dodaj regułę zaawansowaną w celu obsługi potwierdzenia odrzuconego (użytkownik powiedział nie)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Odmowa potwierdzenia                                                                   | Nazwa opisująca cel reguły          |
   | Warunki            | OdmowaConfirmacja & wymagany parametr - DateTime                               | Warunki określające, kiedy reguła może być uruchamiana    |   
   | Akcje               | ClearParameter - DateTime & SpeechResponse - "- Nie ma problemu, o której godzinie?"     | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |
   | Stan po wykonaniu | Poczekaj na dane wejściowe                                                                   | Stan użytkownika po zakręcie                   |
   | Oczekiwania          | ElicitParameters - DateTime                                                      | Oczekiwanie na następną turę                      |

## <a name="try-it-out"></a>Testowanie

Wybierz panel Test i wypróbuj kilka interakcji.

- Wejście: Ustaw alarm na jutro w południe
- Wyjście: "Czy na pewno chcesz ustawić alarm na 12.07.2019 12:00:00?"
- Wejście: Nie
- Wyjście: "Nie ma problemu, o której godzinie?"
- Wejście: 17:00
- Wyjście: "Czy na pewno chcesz ustawić alarm na 12/06/2019 17:00:00?"
- Wejście: Tak
- Wyjście: "Ok, alarm ustawiony na 12/06/2019 17:00:00"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak: Dodawanie jednoetapowej korekty do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-one-step-correction.md)