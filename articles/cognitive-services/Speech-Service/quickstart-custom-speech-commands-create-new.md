---
title: 'Szybki start: tworzenie niestandardowego polecenia (podgląd) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule można utworzyć i przetestować hostowane polecenia niestandardowe aplikacji.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155591"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Szybki start: tworzenie polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć i przetestować hostowane polecenia niestandardowe aplikacji.
Aplikacja rozpozna wypowiedź w stylu "włącz telewizor" i odpowie prostym komunikatem "Ok, włączanie telewizora".

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja mowy.

Jeśli nie masz subskrypcji mowy, możesz ją utworzyć, przechodząc do [Studia mowy](https://speech.microsoft.com/) i wybierając pozycję **Utwórz zasób mowy.**

  > [!div class="mx-imgBorder"]
  > [![Tworzenie projektu](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Podczas podglądu obsługiwany jest tylko region westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Przejdź do studia mowy dla poleceń niestandardowych

1. Otwieranie przeglądarki internetowej i przechodzenie do [studia mowy](https://speech.microsoft.com/)
1. Wprowadź poświadczenia, aby zalogować się do portalu

   - Domyślnym widokiem jest lista subskrypcji mowy
     > [!NOTE]
     > Jeśli nie widzisz strony wybierz subskrypcję, możesz tam nawigować, wybierając "Zasoby mowy" z menu ustawień na górnym pasku.

1. Wybierz subskrypcję mowy, a następnie wybierz pozycję **Przejdź do Studio**
1. Wybieranie **poleceń niestandardowych (wersja zapoznawcza)**

Widok domyślny to lista utworzonych aplikacji polecenia niestandardowe.

## <a name="create-a-custom-commands-project"></a>Tworzenie projektu polecenia niestandardowe

1. Wybierz **pozycję Nowy projekt,** aby utworzyć nowy projekt

   > [!div class="mx-imgBorder"]
   > ![Tworzenie projektu](media/custom-speech-commands/create-new-project.png)

1. Wprowadź nazwę i język projektu.
1. Wybierz zasób autorski. Jeśli nie ma prawidłowych zasobów tworzenia, utwórz je, wybierając **pozycję Utwórz nowy zasób**.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie zasobu](media/custom-speech-commands/create-new-resource.png)

   1. Wprowadź nazwę zasobu, grupę, lokalizację i warstwę cenową.

         > [!NOTE]
         > Grupy zasobów można tworzyć, wprowadzając żądaną nazwę grupy zasobów w polu "Grupa zasobów". Grupa zasobów zostanie utworzona po wybraniu opcji **Utwórz.**

1. Kliknij **przycisk Utwórz,** aby utworzyć projekt.
1. Po utworzeniu wybierz projekt.

Widok powinien być teraz przegląd aplikacji polecenia niestandardowe.

## <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów usługi LUIS (opcjonalnie)

Można zaktualizować zestaw zasobów autora w nowym oknie projektu i ustawić zasób przewidywania używany do rozpoznawania danych wejściowych w czasie wykonywania.

> [!NOTE]
> Należy ustawić zasób przewidywania, zanim aplikacja żąda prognoz poza 1000 żądań dostarczonych przez zasób autora.

> [!div class="mx-imgBorder"]
> ![Ustawianie zasobów usługi LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Przejdź do okienka Zasoby usługi LUIS, wybierając **ustawienia** z lewego okienka, a następnie **zasoby usługi LUIS** z okienka środkowego.
1. Wybierz zasób prognozowania lub utwórz go, wybierając **pozycję Utwórz nowy zasób**
1. Wybierz pozycję **Zapisz**.

## <a name="create-a-new-command"></a>Tworzenie nowego polecenia

Teraz możesz utworzyć polecenie. Użyjmy przykładu, `turn on the tv`który zajmie jedną wypowiedź i odpowiej komunikatem `Ok, turning on the TV`.

1. Utwórz nowe polecenie, `+` zaznaczając ikonę obok poleceń i nadając mu nazwę`TurnOn`
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Tworzenie polecenia](media/custom-speech-commands/create-add-command.png)

Polecenie to zestaw:

| Grupa            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Przykładowe zdania | Przykładowe wypowiedzi, które użytkownik może powiedzieć, aby wyzwolić to polecenie                                                                 |
| Parametry       | Informacje wymagane do wykonania polecenia                                                                                |
| Zasady ukończenia | Działania, które należy podjąć w celu wypełnienia polecenia. Na przykład, aby odpowiedzieć użytkownikowi lub komunikować się z inną usługą sieci web |
| Zaawansowane reguły   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji                                                              |

### <a name="add-a-sample-sentence"></a>Dodawanie przykładowego zdania

Zacznijmy od przykładowych zdań i podajmy przykład tego, co użytkownik może powiedzieć:

```
turn on the tv
```

Na razie nie mamy żadnych parametrów, więc możemy przejść do reguł ukończenia.

### <a name="add-a-completion-rule"></a>Dodawanie reguły ukończenia

Teraz dodaj regułę ukończenia, aby odpowiedzieć użytkownikowi wskazując, że akcja jest podejmowana.

1. Utwórz nową regułę ukończenia, wybierając ikonę `+` obok reguł ukończenia
1. Wprowadź nazwę reguły
1. Dodawanie akcji
   1. Utwórz nową akcję odpowiedzi mowy, zaznaczając ikonę `+` obok pozycji Akcje i wybierając pozycję`SpeechResponse`
   1. Wprowadź odpowiedź

   > [!NOTE]
   > Zwykły tekst musi zaczynać się od kreski. Aby uzyskać więcej informacji, przejdź [tutaj](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Tworzenie odpowiedzi mowy](media/custom-speech-commands/create-speech-response-action.png)

1. Kliknij **przycisk Zapisz,** aby zapisać regułę

> [!div class="mx-imgBorder"]
> ![Tworzenie reguły uzupełniania](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Ustawienie    | Sugerowana wartość                          | Opis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nazwa reguły  | "ConfirmationResponse"                   | Nazwa opisująca cel reguły          |
| Warunki | Brak                                     | Warunki określające, kiedy reguła może być uruchamiana    |
| Akcje    | SpeechResponse "- Ok, włączanie telewizora" | Działanie, które należy podjąć, gdy warunek reguły jest spełniony |

## <a name="try-it-out"></a>Testowanie

Przetestuj zachowanie za pomocą panelu czatu testowego.

> [!div class="mx-imgBorder"]
> ![Testowanie za pomocą czatu internetowego](media/custom-speech-commands/create-basic-test-chat.png)

- Wpisujesz: "włącz telewizor"
- Oczekiwana odpowiedź: "Ok, włączanie telewizora"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
