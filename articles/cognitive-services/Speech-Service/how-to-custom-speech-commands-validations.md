---
title: 'Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego (wersja zapoznawcza)'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak dodać walidacje do parametru w poleceniach niestandardowych.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: c89c388f919ca95a331d1d406f5b1776c127ebad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446915"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać walidacje do parametrów i monitów o korektę.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

- [Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Utwórz polecenie settemperaturę

Aby zademonstrować walidacje, Utwórzmy nowe polecenie umożliwiające użytkownikowi ustawianie temperatury.

1. Otwieranie wcześniej utworzonych aplikacji poleceń niestandardowych w programie [Speech Studio](https://speech.microsoft.com/)
1. Utwórz nowe polecenie **Settemperaturę**
1. Dodaj parametr dla temperatury docelowej
1. Dodawanie walidacji parametru temperatury
   > [!div class="mx-imgBorder"]
   > ![dodać walidację zakresu](media/custom-speech-commands/validations-add-temperature.png)

   | Ustawienie           | Sugerowana wartość                                          | Opis                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nazwa              | Temperatura                                              | Opisowa nazwa parametru polecenia                                                    |
   | Wymagane          | true                                                     | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
   | Szablon odpowiedzi | "— Jakiej temperatury chcesz?".                     | Monit o podanie wartości tego parametru, gdy nie jest on znany                              |
   | Typ              | Liczba                                                   | Typ parametru, taki jak Number, String lub Data Time                                      |
   | Sprawdzanie poprawności        | Wartość minimalna: 60, wartość maksymalna: 80                             | Dla parametrów liczbowych dozwolony zakres wartości parametru                             |
   | Szablon odpowiedzi | "— Niestety, można ustawić tylko między 60 i 80 stopni"      | Monituj o podanie zaktualizowanej wartości, Jeśli weryfikacja nie powiedzie się                                       |

1. Dodaj przykładowe zdania

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Dodawanie reguły uzupełniania w celu potwierdzenia wyniku

   | Ustawienie    | Sugerowana wartość                                           | Opis                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły  | Komunikat z potwierdzeniem                                      | Nazwa opisująca przeznaczenie reguły          |
   | Warunki | Wymagana temperatura parametru                          | Warunki określające, kiedy można uruchomić regułę    |
   | Akcje    | SpeechResponse-"-OK, ustawienia do {temperatura} stopni" | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

> [!TIP]
> Ten przykład używa odpowiedzi mowy, aby potwierdzić wynik. Aby zapoznać się z przykładami dotyczącymi kończenia polecenia z akcją klienta, zobacz: [jak: zrealizować polecenia na kliencie przy użyciu zestawu Speech SDK (wersja zapoznawcza)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Wypróbuj

Wybierz panel testowania i wypróbuj kilka interakcji.

- Dane wejściowe: Ustaw temperaturę na 72 stopni
- Wynik: "OK, ustawienie na 72 stopni"

- Dane wejściowe: Ustaw temperaturę na 45 stopni
- Wynik: "Niestety, można ustawić tylko między 60 i 80 stopni"
- Dane wejściowe: Zwiększ do 72 stopni.
- Wynik: "OK, ustawienie na 72 stopni"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)