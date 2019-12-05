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
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815820"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć i przetestować hostowaną aplikację poleceń niestandardowych.
Aplikacja rozpozna wypowiedź, na przykład "Włącz telewizor" i odpowie przy użyciu prostego komunikatu "OK", włączając telewizor ".

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja mowy. [Wypróbuj bezpłatnie usługę mowy](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > W trakcie okresu zapoznawczego dla kluczy subskrypcji jest obsługiwany tylko region westus2.

- Klucz tworzenia [Language Understanding](https://www.luis.ai/home) (Luis):
  1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com)
  1. Wybierz pozycję Utwórz zasób
  1. Wyszukaj i wybierz [Language Understanding](https://aka.ms/sc-luis-all)
  1. Wybieranie opcji tworzenia w opcjach tworzenia
  1. Po wdrożeniu zasobu przejdź do zasobu i skopiuj klucz z sekcji szybki start lub klucze

      > [!div class="mx-imgBorder"]
      > ![utworzyć zasobu tworzenia](media/custom-speech-commands/resources-lu-authoring.png)

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
   > ![utworzyć nowy projekt](media/custom-speech-commands/create-new-project.png)

1. Wprowadź nazwę projektu i język, a następnie wybierz pozycję **dalej** , aby kontynuować.
1. Wprowadź klucz autorstwa LUIS
1. Po utworzeniu wybierz projekt

Widok powinien teraz być przeglądem aplikacji poleceń niestandardowych.

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

> [!div class="mx-imgBorder"]
> ![utworzyć reguły uzupełniania](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Ustawienie    | Sugerowana wartość                        | Opis                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Nazwa reguły  | "ConfirmationResponse"                 | Nazwa opisująca przeznaczenie reguły          |
| Warunki | Brak                                   | Warunki określające, kiedy można uruchomić regułę    |
| Akcje    | SpeechResponse "OK", włączając telewizor " | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

## <a name="try-it-out"></a>Wypróbuj

Przetestuj zachowanie przy użyciu panelu rozmowa testowa.

> [!div class="mx-imgBorder"]
> Test ![z usługą webchat](media/custom-speech-commands/create-basic-test-chat.png)

- Wpisz: "Włącz telewizor"
- Oczekiwana odpowiedź: "OK, Włączanie telewizora"

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
