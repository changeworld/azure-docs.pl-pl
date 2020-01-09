---
title: 'Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie i testowanie aplikacji hostowanych poleceń niestandardowych.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: d8e28b88757fa7557b04ee471ede17012094bb9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446873"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć i przetestować hostowaną aplikację poleceń niestandardowych.
Aplikacja rozpozna wypowiedź, na przykład "Włącz telewizor" i odpowie przy użyciu prostego komunikatu "OK", włączając telewizor ".

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja mowy. 

Jeśli nie masz subskrypcji mowy, możesz ją utworzyć, przechodząc do programu [Speech Studio](https://speech.microsoft.com/) i wybierając pozycję **Utwórz zasób mowy**.

  > [!div class="mx-imgBorder"]
  > [![utworzyć projekt](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > W trakcie okresu zapoznawczego obsługiwana jest tylko region westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Przejdź do programu Speech Studio dla poleceń niestandardowych

1. Otwórz przeglądarkę internetową i przejdź do programu [Speech Studio](https://speech.microsoft.com/)
1. Wprowadź swoje poświadczenia, aby zalogować się do portalu

   - Widok domyślny to lista subskrypcji mowy
     > [!NOTE]
     > Jeśli nie widzisz strony Wybieranie subskrypcji, możesz tam przejść przez wybranie opcji "zasoby mowy" z menu Ustawienia na górnym pasku.

1. Wybierz swoją subskrypcję mowy, a następnie wybierz pozycję **Przejdź do Studio**
1. Wybieranie **poleceń niestandardowych (wersja zapoznawcza)**

Widok domyślny to lista utworzonych przez siebie aplikacji poleceń niestandardowych.

## <a name="create-a-custom-commands-project"></a>Tworzenie projektu poleceń niestandardowych

1. Wybierz pozycję **Nowy projekt** , aby utworzyć nowy projekt

   > [!div class="mx-imgBorder"]
   > ![utworzyć projekt](media/custom-speech-commands/create-new-project.png)

1. Wprowadź nazwę projektu i język.
1. Wybierz zasób tworzenia. Jeśli nie ma prawidłowych zasobów tworzenia, utwórz je, wybierając pozycję **Utwórz nowy zasób**.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie zasobu](media/custom-speech-commands/create-new-resource.png)

   1. Wprowadź nazwę zasobu, grupę, lokalizację i warstwę cenową.

         > [!NOTE]
         > Grupy zasobów można utworzyć, wprowadzając odpowiednią nazwę grupy zasobów w polu "Grupa zasobów". Grupa zasobów zostanie utworzona, gdy zostanie wybrana wartość **Utwórz** .

1. Kliknij przycisk **Utwórz** , aby utworzyć projekt.
1. Po utworzeniu wybierz projekt.

Widok powinien teraz być przeglądem aplikacji poleceń niestandardowych.

## <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Można zaktualizować zestaw zasobów tworzenie w oknie Nowy projekt i ustawić zasób predykcyjny używany do rozpoznawania danych wejściowych w czasie wykonywania. 

> [!NOTE]
> Należy ustawić zasób predykcyjny, zanim aplikacja zażąda prognoz wykraczających poza żądania 1 000 dostarczone przez zasób tworzenia.

> [!div class="mx-imgBorder"]
> ![ustawić zasoby LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Przejdź do okienka zasoby LUIS, wybierając pozycję **Ustawienia** w okienku po lewej stronie, a następnie pozycję **Luis zasoby** w środkowym okienku.
1. Wybierz zasób predykcyjny lub utwórz go, wybierając pozycję **Utwórz nowy zasób** .
1. Wybierz pozycję **Zapisz**.

## <a name="create-a-new-command"></a>Utwórz nowe polecenie

Teraz można utworzyć polecenie. Skorzystajmy z przykładu, który zajmie jedną wypowiedźą, `turn on the tv`i reaguje na `Ok, turning on the TV`komunikatów.

1. Utwórz nowe polecenie, wybierając ikonę `+` obok poleceń i nadaj jej nazwę `TurnOn`
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![utworzyć polecenie](media/custom-speech-commands/create-add-command.png)

Polecenie jest zestawem:

| Grupa            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Przykładowe zdania | Przykład wyrażenia długości, że użytkownik może powiedzieć, aby wyzwolić to polecenie                                                                 |
| Parametry       | Informacje wymagane do wykonania polecenia                                                                                |
| Reguły uzupełniania | Akcje, które należy wykonać, aby zrealizować polecenie. Na przykład aby odpowiedzieć użytkownikowi lub komunikować się z inną usługą sieci Web |
| Reguły zaawansowane   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji                                                              |

### <a name="add-a-sample-sentence"></a>Dodaj przykładowe zdanie

Zacznijmy od przykładowych zdań i przedstawiamy przykład tego, co użytkownik może powiedzieć:

```
turn on the tv
```

Na razie nie mamy żadnych parametrów, aby umożliwić Przechodzenie do reguł ukończenia.

### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Teraz Dodaj regułę ukończenia, aby odpowiedzieć użytkownikowi, który wskazuje, że akcja jest wykonywana.

1. Utwórz nową regułę uzupełniania, wybierając ikonę `+` obok reguły ukończenia
1. Wprowadź nazwę reguły
1. Dodawanie akcji
   1. Utwórz nową akcję odpowiedzi na mowę, wybierając ikonę `+` obok pozycji akcje i wybierz pozycję `SpeechResponse`
   1. Wprowadź odpowiedź

   > [!NOTE]
   > Zwykły tekst musi rozpoczynać się kreską. Aby uzyskać więcej informacji, przejdź [tutaj](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![utworzyć](media/custom-speech-commands/create-speech-response-action.png) odpowiedzi na mowę

1. Kliknij przycisk **Zapisz** , aby zapisać regułę

> [!div class="mx-imgBorder"]
> ![utworzyć reguły uzupełniania](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Ustawienie    | Sugerowana wartość                          | Opis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nazwa reguły  | "ConfirmationResponse"                   | Nazwa opisująca przeznaczenie reguły          |
| Warunki | Brak                                     | Warunki określające, kiedy można uruchomić regułę    |
| Akcje    | SpeechResponse "— OK, Włączanie TELEWIZORa" | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

## <a name="try-it-out"></a>Wypróbuj

Przetestuj zachowanie przy użyciu panelu rozmowa testowa.

> [!div class="mx-imgBorder"]
> Testowanie ![przy użyciu funkcji rozmowy w sieci Web](media/custom-speech-commands/create-basic-test-chat.png)

- Wpisz: "Włącz telewizor"
- Oczekiwana odpowiedź: "OK, Włączanie telewizora"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
