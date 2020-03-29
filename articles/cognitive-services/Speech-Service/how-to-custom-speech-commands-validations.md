---
title: 'Jak: Dodawanie weryfikacji do parametrów polecenia niestandardowego (wersja zapoznawcza)'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniamy, jak dodać sprawdzanie poprawności do parametru w poleceniach niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156458"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Jak: Dodawanie weryfikacji do parametrów polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać sprawdzanie poprawności do parametrów i monit o korektę.

## <a name="prerequisites"></a>Wymagania wstępne

Kroki w następujących artykułach należy wykonać:

- [Szybki start: tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Tworzenie polecenia SetTemperature

Aby zademonstrować sprawdzanie poprawności, utwórzmy nowe polecenie umożliwiające użytkownikowi ustawienie temperatury.

1. Otwórz wcześniej utworzoną aplikację Polecenia niestandardowe w [Studio mowy](https://speech.microsoft.com/)
1. Tworzenie nowej **konfiguracji** poleceń
1. Dodawanie parametru dla temperatury docelowej
1. Dodawanie sprawdzania poprawności parametru temperatury
   > [!div class="mx-imgBorder"]
   > ![Dodawanie sprawdzania poprawności zakresu](media/custom-speech-commands/validations-add-temperature.png)

   | Ustawienie           | Sugerowana wartość                                          | Opis                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nazwa              | Temperatura                                              | Opisowa nazwa parametru Command                                                    |
   | Wymagany          | true                                                     | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia |
   | Szablon odpowiedzi | "- Jaka temperatura chcesz?"                     | Monit z prośbą o podanie wartości tego parametru, gdy nie jest znany                              |
   | Typ              | Liczba                                                   | Typ parametru, taki jak Liczba, Ciąg lub Data Ii                                      |
   | Sprawdzanie poprawności        | Wartość min: 60, wartość maksymalna: 80                             | W przypadku parametrów Number dozwolony zakres wartości parametru                             |
   | Szablon odpowiedzi | "- Niestety, mogę ustawić tylko między 60 a 80 stopni"      | Monituj o podanie zaktualizowanej wartości, jeśli sprawdzanie poprawności nie powiedzie się                                       |

1. Dodawanie przykładowych zdań

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Dodawanie reguły ukończenia w celu potwierdzenia wyniku

   | Ustawienie    | Sugerowana wartość                                           | Opis                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły  | Komunikat potwierdzający                                      | Nazwa opisująca cel reguły          |
   | Warunki | Wymagany parametr - temperatura                          | Warunki określające, kiedy reguła może być uruchamiana    |
   | Akcje    | SpeechResponse - "- Ok, ustawienie {Temperature} stopni" | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |

> [!TIP]
> W tym przykładzie użyto odpowiedzi mowy, aby potwierdzić wynik. Przykłady dotyczące ukończenia polecenia za pomocą akcji klienta zobacz: [Jak: Wypełnianie poleceń na kliencie za pomocą sdk mowy (wersja zapoznawcza)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Testowanie

Wybierz panel Test i wypróbuj kilka interakcji.

- Wejście: Ustaw temperaturę na 72 stopni
- Wyjście: "Ok, ustawienie do 72 stopni"

- Wejście: Ustaw temperaturę na 45 stopni
- Wyjście: "Niestety, mogę ustawić tylko od 60 do 80 stopni"
- Wejście: zrób to 72 stopni zamiast
- Wyjście: "Ok, ustawienie do 72 stopni"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)
