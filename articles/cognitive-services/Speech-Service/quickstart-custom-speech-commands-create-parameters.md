---
title: 'Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule dodasz parametry do aplikacji Polecenia niestandardowe.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348526"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Szybki start: tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)

W [poprzednim artykule](./quickstart-custom-speech-commands-create-new.md)utworzyliśmy nowy projekt poleceń niestandardowych, aby reagować na polecenia bez parametrów.

W tym artykule rozszerzymy tę aplikację o parametry, dzięki czemu może obsługiwać włączanie i wyłączanie wielu urządzeń.

## <a name="create-parameters"></a>Tworzenie parametrów

1. Otwórz projekt, [który utworzyliśmy wcześniej](./quickstart-custom-speech-commands-create-new.md)
1. Ponieważ polecenie będzie teraz obsługiwać i wyłączać, zmień nazwę polecenia na "TurnOnOff"
   - Umieść wskaźnik myszy na nazwie polecenia i wybierz ikonę edycji, aby zmienić nazwę
1. Tworzenie nowego parametru reprezentującego, czy użytkownik chce włączyć lub wyłączyć urządzenie
   - Wybierz `+` ikonę obok sekcji Parametry

   > [!div class="mx-imgBorder"]
   > ![Utwórz parametr](media/custom-speech-commands/create-on-off-parameter.png)

   | Ustawienie            | Sugerowana wartość     | Opis                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nazwa               | Onoff               | Opisowa nazwa parametru                                                                     |
   | Ma charakter globalny          | unchecked           | Pole wyboru wskazujące, czy wartość tego parametru jest globalnie stosowana do wszystkich poleceń w projekcie |
   | Wymagany           | checked             | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia          |
   | Szablon odpowiedzi  | "- Włączanie czy wyłączanie?"      | Monit z prośbą o podanie wartości tego parametru, gdy nie jest znany                                       |
   | Typ               | Ciąg              | Typ parametru, taki jak Liczba, Ciąg lub Data Ii                                               |
   | Konfigurowanie      | Lista ciągów         | W przypadku ciągów lista ciągów ogranicza dane wejściowe do zestawu możliwych wartości                                      |
   | Wartości listy ciągów | wł., wył.             | W przypadku parametru Lista ciągów zestaw możliwych wartości i ich synonimy                                |

   - Następnie ponownie `+` wybierz ikonę, aby dodać drugi parametr reprezentujący nazwę urządzeń. W tym przykładzie telewizor i wentylator

   | Ustawienie            | Sugerowana wartość       | Opis                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nazwa               | PodmiotUrządzenia         | Opisowa nazwa parametru                                                                     |
   | Ma charakter globalny          | unchecked             | Pole wyboru wskazujące, czy wartość tego parametru jest globalnie stosowana do wszystkich poleceń w projekcie |
   | Wymagany           | checked               | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia          |
   | Szablon odpowiedzi  | "- Które urządzenie?"     | Monit z prośbą o podanie wartości tego parametru, gdy nie jest znany                                       |
   | Typ               | Ciąg                | Typ parametru, taki jak Liczba, Ciąg lub Data Ii                                               |
   | Konfigurowanie      | Lista ciągów           | W przypadku ciągów lista ciągów ogranicza dane wejściowe do zestawu możliwych wartości                                      |
   | Wartości listy ciągów | tv, wentylator               | W przypadku parametru Lista ciągów zestaw możliwych wartości i ich synonimy                                |
   | Synonimy (tv)      | telewizja, telly     | Opcjonalne synonimy dla każdej możliwej wartości parametru listy ciągów                                      |

## <a name="add-sample-sentences"></a>Dodawanie przykładowych zdań

W parametrach warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Przykład:

1. Pełna informacja o parametrach -`"turn {OnOff} the {SubjectDevice}"`
1. Informacje o parametrach częściowych -`"turn it {OnOff}"`
1. Brak informacji o parametrach -`"turn something"`

Przykładowe zdania z różnymi ilościami informacji umożliwiają aplikacji Polecenia niestandardowe rozpoznawanie zarówno rozdzielczości jednego strzału, jak i rozdzielczości wielu zawracania z informacjami częściowymi.

Mając to na uwadze, edytuj przykładowe zdania, aby użyć parametrów zgodnie z poniższymi informacjami.

> [!TIP]
> W edytorze przykład zdań użyj nawiasów klamrowych, aby odwołać się do parametrów. - `turn {OnOff} the {SubjectDevice}`Użyj uzupełnienia karty, aby odwołać się do wcześniej utworzonych parametrów.

> [!div class="mx-imgBorder"]
> ![Przykładowe zdania z parametrami](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Dodawanie parametrów do reguły ukończenia

Zmodyfikuj regułę ukończenia utworzoną w [poprzednim przewodniku Szybki start:](./quickstart-custom-speech-commands-create-new.md)

1. Dodaj nowy Warunek i wybierz wymagany parametr. Wybierz `OnOff` zarówno i`SubjectDevice`
1. Edytuj akcję Odpowiedź mowy `OnOff` `SubjectDevice`do użycia i:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Testowanie

Otwórz panel Czat testowy i wypróbuj kilka interakcji.

- Wejście: wyłącz telewizor
- Wyjście: Ok, wyłączenie telewizora

- Wejście: wyłącz telewizor
- Wyjście: Ok, wyłączenie telewizora

- Wejście: wyłącz go
- Wyjście: Które urządzenie?
- Wejście: telewizor
- Wyjście: Ok, wyłączenie telewizora

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: używanie niestandardowych poleceń z niestandardowym głosem (wersja zapoznawcza)](./quickstart-custom-speech-commands-select-custom-voice.md)
