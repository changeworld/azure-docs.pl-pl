---
title: 'Szybki start: tworzenie nowej aplikacji w portalu usługi LUIS'
description: W tym przewodniku Szybki start można utworzyć podstawowe części aplikacji, intencje i jednostki, a także przetestować z wypowiedź próbki w portalu usługi LUIS.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: f0c8f0c77f832e049dfc494f82e90edb61a8cb2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244618"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Szybki start: tworzenie nowej aplikacji w portalu usługi LUIS

W tym przewodniku Szybki start tworzysz nową aplikację w portalu usługi LUIS. Najpierw utwórz podstawowe części aplikacji, **intencje**i **encje**. Następnie przetestuj aplikację, zapewniając wypowiedź przykładowego użytkownika w interaktywnym panelu testowym, aby uzyskać przewidywaną intencję.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Tworzenie aplikacji

1. Wybierz **+ Nowa aplikacja do konwersacji** z paska narzędzi kontekstu, a następnie wybierz pozycję Nowa aplikacja do **konwersacji**.

    > [!div class="mx-imgBorder"]
    > [![Tworzenie nowej aplikacji w portalu usługi LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. W wyskakującym oknie skonfiguruj aplikację z następującymi ustawieniami, a następnie wybierz pozycję **Gotowe**.

   |Nazwa ustawienia| Wartość | Przeznaczenie|
   |--|--|--|
   |Nazwa|`myEnglishApp`|Unikatowa nazwa aplikacji usługi LUIS<br>wymagany|
   |Culture|**Polski**|Język wypowiedzi użytkowników, **en-us**<br>wymagany|
   |Opis (opcjonalnie)|`App made with LUIS Portal`|Opis aplikacji<br>optional|
   |Zasób przewidywania (opcjonalnie) |-  |Nie wybieraj. Usługa LUIS udostępnia klucz startowy do bezpłatnego użycia do tworzenia i 1000 żądań punktu końcowego przewidywania. |

   ![Wprowadzanie nowych ustawień aplikacji](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Tworzenie intencji

Po utworzeniu aplikacji usługi LUIS należy utworzyć intencje. Intencje to sposób kategoryzowanie tekstu od użytkowników. Na przykład aplikacja zasobów ludzkich może mieć dwie funkcje. Aby pomóc ludziom:

 1. Znajdź i ubiegaj się o pracę
 1. Znajdź formularze do ubiegania się o pracę

Dwie różne _intencje_ aplikacji są zgodne z następującymi intencjami:

|Intencja|Przykładowy tekst użytkownika<br>znany jako _wypowiedź_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|Znajdź formę|`Where is the job transfer form hrf-123456?`|

Aby utworzyć intencje, wykonaj następujące kroki:

1. Po utworzeniu aplikacji znajdujesz się na stronie **Intencje** w sekcji **Kompilacja.** Wybierz **pozycję Utwórz**.

   [![Wybierz pozycję Utwórz, aby utworzyć nową intencję](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Wprowadź nazwę intencji, `FindForm`a następnie wybierz pozycję **Gotowe**.

## <a name="add-an-example-utterance"></a>Dodawanie przykładowego wypowiedź

Po utworzeniu intencji należy dodać przykładowe wypowiedzi. Przykładowe wypowiedzi to tekst wprowadzany przez użytkownika w botie czatu lub innej aplikacji klienckiej. Mapują zamiar tekstu użytkownika do intencji usługi LUIS.

W tym przykładzie `FindForm` intencji aplikacji przykład wypowiedzi będą zawierać numer formularza. Aplikacja kliencka potrzebuje numeru formularza, aby spełnić żądanie użytkownika, dlatego ważne jest, aby uwzględnić go w wypowiedź.

> [!div class="mx-imgBorder"]
> [![Wprowadź przykładowe wypowiedzi dla intencji FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Dodaj następujące 15 wypowiedzi przykład `FindForm` do intencji.

|#|Przykładowe wypowiedzi|
|--|--|
|1|Szukam hrf-123456|
|2|Gdzie jest formularz hrf-234591?|
|3|hrf-345623, gdzie jest|
|4|Czy można wysłać mi hrf-345794|
|5|Czy muszę hrf-234695 ubiegać się o pracę wewnętrzną?|
|6|Czy mój menedżer musi wiedzieć, że ubiegam się o pracę w HRF-234091|
|7|Gdzie mogę wysłać hrf-234918? Czy otrzymam otrzymaną odpowiedź e-mail?|
|8|hrf-234555|
|9|Kiedy zaktualizowano hrf-234987?|
|10|Czy używam formularza hrf-876345, aby ubiegać się o stanowiska inżynierskie|
|11|Czy do mojego otwartego req została przesłana nowa wersja hrf-765234?|
|12|Czy używam hrf-234234 do pracy na arenie międzynarodowej?|
|13|hrf-234598 błąd ortograficzny|
|14|będzie hrf-234567 być edytowane dla nowych wymagań|
|15|hrf-123456, hrf-123123, hrf-234567|

Zgodnie z projektem te wypowiedzi przykład różnią się w następujący sposób:

* długość wypowiedź
* znaki interpunkcyjne
* wybór słów
* czasownik napięta (jest, był, będzie)
* kolejność słów



## <a name="create-a-regular-expression-entity"></a>Tworzenie encji wyrażenia regularnego

Aby zwrócić numer formularza w odpowiedzi przewidywania środowiska uruchomieniowego, formularz musi być oznaczony jako jednostka. Ponieważ tekst numeru formularza ma dużą strukturę, można go oznaczyć za pomocą encji wyrażenia regularnego. Utwórz encję, wykonując następujące czynności:

1. Wybierz **elementy** z menu po lewej stronie.

1. Wybierz **pozycję Utwórz** na stronie **Elementy.**

1. Wprowadź nazwę `Human Resources Form Number`, wybierz typ encji **Regex,** a następnie wybierz **przycisk Dalej**.

   ![Tworzenie encji wyrażenia regularnego](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Wprowadź wyrażenie wyrażenie regularne ( `hrf-[0-9]{6}`**RegEx**), . Ten wpis jest zgodny `hrf-`ze znakami dosłownym i pozwala na dokładnie 6 cyfr, a następnie wybierz pozycję **Utwórz**.

   ![Wprowadź wyrażenie regularne dla encji](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

Intencja **Brak** jest intencją rezerwy i nie powinna być pusta. Ten zamiar powinien zawierać jedną wypowiedź dla każdego 10 wypowiedzi przykład, które zostały dodane dla innych intencji aplikacji.

Wypowiedzi przykładowe **intencji none** powinny znajdować się poza domeną aplikacji klienckiej.

1. Wybierz **opcję Intencje** z lewego menu, a następnie wybierz **pozycję Brak** z listy intencji.

1. Dodaj następujące wypowiedzi przykład do intencji:

   |Brak intencji przykład wypowiedzi|
   |--|
   |Barking dogs are annoying (Szczekające psy są irytujące)|
   |Order a pizza for me (Zamów dla mnie pizzę)|
   |Penguins in the ocean (Pingwiny w oceanie)|

   Dla tej aplikacji te wypowiedzi przykład są poza domeną. Jeśli domena zawiera zwierzęta, żywność lub ocean, należy użyć różnych wypowiedzi przykład dla **None** intencji.

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Spójrz na jednostkę wyrażenia regularnego w wypowiedziach przykładowych

1. Sprawdź, czy encja znajduje się w intencji **FindForm,** wybierając **intencje** z lewego menu. Następnie wybierz polecenie **Znajdź formę.**

   Jednostka jest oznaczona, gdzie pojawia się w wypowiedzi przykład. Jeśli chcesz wyświetlić oryginalny tekst zamiast nazwy encji, przełącz **widok jednostek** z paska narzędzi.

   > [!div class="mx-imgBorder"]
   > [![Wszystkie przykładowe wypowiedzi oznaczone jednostkami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testowanie nowej aplikacji za pomocą interaktywnego okienka testowego

Użyj interaktywnego **okienka testowego** w portalu usługi LUIS, aby sprawdzić, czy jednostka jest wyodrębniana z nowych wypowiedzi, których aplikacja jeszcze nie widziała.

1. Wybierz **opcję Testuj** z prawego górnego menu.

1. Dodaj nową wypowiedź, a następnie naciśnij klawisz Enter:

   ```Is there a form named hrf-234098```

    Wybierz **sprawdź,** aby zobaczyć prognozy jednostek.

   > [!div class="mx-imgBorder"]
   > ![Testowanie nowej wypowiedź w okienku testu](./media/get-started-portal-build-app/test-new-utterance.png)

   Najlepszym przewidywanym zamiarem jest poprawnie **FindForm** z ponad 90% zaufania (0.977). Jednostka **Numer formularza zasobów ludzkich** jest wyodrębniany o wartości hrf-234098.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki start i nie przejście do następnego przewodnika Szybki start wybierz **pozycję Moje aplikacje** z górnego menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie aplikacji z listy i wybierz pozycję **Usuń** z paska narzędzi kontekstu nad listą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [2. Wdrażanie aplikacji](get-started-portal-deploy-app.md)
