---
title: 'Szybki start: tworzenie, szkolenie i publikowanie bazy wiedzy - QnA Maker'
description: Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Maker na prostej stronie faq, aby odpowiedzieć na pytania QnA Maker.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: b1f80006e2dc72b5b623f4c29c093c734dc1efea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220579"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Szybki start: tworzenie, szkolenie i publikowanie bazy wiedzy programu QnA Maker

Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Maker na prostej stronie faq, aby odpowiedzieć na pytania QnA Maker.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
> * [Zasób](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker utworzony w witrynie Azure portal. Zapamiętaj identyfikator usługi Azure Active Directory, subskrypcję, nazwę zasobu QnA wybraną podczas tworzenia zasobu.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Stwórz swoją pierwszą bazę wiedzy QnA Maker

1. Zaloguj się do portalu [QnAMaker.ai](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

1. Na stronie **Tworzenie** pomiń **krok 1,** jeśli masz już zasób QnA Maker.

    Jeśli zasób nie został jeszcze utworzony, wybierz pozycję **Utwórz usługę QnA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji. Zapamiętaj identyfikator usługi Azure Active Directory, subskrypcję, nazwę zasobu QnA wybraną podczas tworzenia zasobu.

    Po zakończeniu tworzenia zasobu w witrynie Azure portal, wróć do portalu QnA Maker, odśwież stronę przeglądarki i przejdź do **kroku 2**.

1. W **kroku 3**wybierz usługę Active Directory, subskrypcję, usługę (zasób) i język dla wszystkich baz wiedzy utworzonych w usłudze.

   ![Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. W **kroku 3**, nazwij swoją bazę wiedzy **My Sample QnA KB**.

1. W **kroku 4**skonfiguruj ustawienia za pomocą poniższej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz ekstrakcję wielu kolejek z plików URL, pdf lub docx.**|Zaznaczone|
    |**Domyślny tekst odpowiedzi**| `Quickstart - default answer not found.`|
    |**+ Dodaj adres URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-czat**|Wybierz **profesjonalny**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją bazę wiedzy .**

    Proces wyodrębniania zajmuje kilka chwil, aby przeczytać dokument i zidentyfikować pytania i odpowiedzi.

    Po pomyślnym utworzenie bazy wiedzy programu QnA Maker zostanie otwarta strona **bazy wiedzy.** Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="add-a-new-question-and-answer-set"></a>Dodawanie nowego zestawu pytań i odpowiedzi

1. W portalu QnA Maker na stronie **Edycja** wybierz pozycję **+ Dodaj parę QnA** z paska narzędzi kontekstu.
1. Dodaj następujące pytanie:

    `How many Azure services are used by a knowledge base?`

1. Dodaj odpowiedź sformatowaną za pomocą _znacznika:_

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Dodaj pytanie jako tekst i odpowiedź sformatowaną znacznikami.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Symbol `*`znaczników, jest używany dla punktorów. Jest `\n` używany dla nowej linii.

    Na stronie **Edytuj** pojawi się znacznik. Po użyciu panelu **testowego** później zostanie wyświetlony znacznik oznaczający prawidłowo.

## <a name="save-and-train"></a>Zapisywanie i szkolenie

W prawym górnym rogu wybierz **pozycję Zapisz i trenuj,** aby zapisać zmiany i wytrenować program QnA Maker . Zmiany nie są przechowywane, o ile nie zostaną zapisane.

## <a name="test-the-knowledge-base"></a>Testowanie bazy wiedzy

1. W portalu QnA Maker w prawym górnym rogu wybierz **pozycję Testuj,** aby sprawdzić, czy wprowadzone zmiany zostały wprowadzone.
1. Wprowadź przykładową kwerendę użytkownika w polach tekstowych.

    `How many Azure services are used by a knowledge base?`

    ![ Wprowadź przykładową kwerendę użytkownika w polach tekstowych. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Wybierz pozycję **Zbadaj**, aby bardziej szczegółowo sprawdzić odpowiedź. Okno testowe służy do testowania zmian w bazie wiedzy przed opublikowaniem bazy wiedzy.

1. Wybierz **przycisk Testuj** ponownie, aby zamknąć panel **Test.**

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy

Podczas publikowania bazy wiedzy zawartość bazy wiedzy przenosi `test` się z `prod` indeksu do indeksu w wyszukiwarce platformy Azure.

![Zrzut ekranu przedstawiający przenoszenie zawartości bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. W portalu QnA Maker wybierz pozycję **Publikuj**. Aby potwierdzić, wybierz na stronie pozycję **Opublikuj**.

    Usługa QnA Maker została teraz pomyślnie opublikowana. Możesz użyć punktu końcowego w swojej aplikacji lub kodu bota.

    ![Zrzut ekranu przedstawiający pomyślne publikowanie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Tworzenie bota

Po opublikowaniu można utworzyć bota na stronie **Publikowania:**

* Można szybko utworzyć kilka botów, wszystkie wskazując na tę samą bazę wiedzy dla różnych regionów lub plany cenowe dla poszczególnych botów.
* Jeśli chcesz tylko jeden bot dla bazy wiedzy, użyj **Wyświetl wszystkie boty na stronie Azure portal** link, aby wyświetlić listę bieżących botów.

Po wprowadzeniu zmian w bazie wiedzy i ponownemu opublikowaniu nie trzeba podejmować dalszych działań z botem. Jest już skonfigurowany do pracy z bazą wiedzy i współpracuje ze wszystkimi przyszłymi zmianami w bazie wiedzy. Za każdym razem, gdy publikujesz bazę wiedzy, wszystkie podłączone do niej boty są automatycznie aktualizowane.

1. W portalu QnA Maker na stronie **Publikowanie** wybierz pozycję **Utwórz bota**. Ten przycisk pojawia się dopiero po opublikowaniu bazy wiedzy.

    ![Zrzut ekranu przedstawiający tworzenie bota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Zostanie otwarta nowa karta przeglądarki dla witryny Azure portal ze stroną tworzenia usługi Azure Bot Service. Konfigurowanie usługi azure bot. Bot i QnA Maker mogą udostępniać plan usługi aplikacji sieci web, ale nie mogą udostępniać aplikacji sieci web. Oznacza to, że **nazwa aplikacji** dla bota musi się różnić od nazwy aplikacji dla usługi QnA Maker.

    * **Zrobić**
        * Zmień dojście bota - jeśli nie jest unikatowy.
        * Wybierz język SDK. Po utworzeniu bota można pobrać kod do lokalnego środowiska programistycznego i kontynuować proces programowania.
    * **Zakazy**
        * zmienić następujące ustawienia w witrynie Azure portal podczas tworzenia bota. Są one wstępnie wypełnione dla istniejącej bazy wiedzy:
           * Klawisz Auth QnA
           * Plan usługi aplikacji i lokalizacja


1. Po utworzeniu bota otwórz zasób **usługi Bot.**
1. W obszarze **Zarządzanie botami**wybierz pozycję **Testuj w czacie internetowym**.
1. W wierszu czatu **wpisz wiadomość**, wprowadź:

    `Azure services?`

    Czat bot odpowiada odpowiedzią z bazy wiedzy.

    ![Wprowadź zapytanie użytkownika do testowego czatu internetowego.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Co udało ci się osiągnąć?

Utworzono nową bazę wiedzy, dodano publiczny adres URL do bazy wiedzy, dodano własny zestaw QnA, przeszkolony, przetestowany i opublikowane bazy wiedzy.

Po opublikowaniu bazy wiedzy utworzono bota i przetestowano bota.

Wszystko to zostało osiągnięte w ciągu kilku minut bez konieczności pisania kodu lub czyszczenia zawartości.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie kontynuujesz następnego przewodnika Szybki start, usuń zasoby platformy QnA Maker i Bot w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie pytań za pomocą metadanych](add-question-metadata-portal.md)

Więcej informacji:

* [Format znaczników w odpowiedziach](../reference-markdown-format.md)
* [Źródła danych](../concepts/knowledge-base.md)programu QnA Maker .


