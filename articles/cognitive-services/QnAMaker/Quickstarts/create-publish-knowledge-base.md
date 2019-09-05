---
title: 'Szybki start: Tworzenie, uczenie i publikowanie bazy wiedzy — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Baza wiedzy QnA Maker w tym przykładzie jest tworzona na podstawie prostych często zadawanych pytań i odpowiedzi na pytania dotyczące odzyskiwania klucza funkcji BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 229956b41f7f26637e6a6aa62e341c4b06b8a429
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376363"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Szybki start: Tworzenie, szkolenie i publikowanie bazy wiedzy usługi QnA Maker

Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Makerej na podstawie prostej witryny sieci Web często zadawanych pytań i odpowiedzi na pytania dotyczące odzyskiwania klucza funkcji BitLocker.

## <a name="prerequisite"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker

1. Zaloguj się do portalu [QnAMaker.AI](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

   ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qna-maker-create-kb.png)

1. Na stronie **Tworzenie** w kroku 1 wybierz pozycję **Utwórz usługę QnA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji. Jeśli upłynie limit czasu witryny Azure Portal, wybierz w witrynie pozycję **Ponów próbę**. Po nawiązaniu połączenia zostanie wyświetlony pulpit nawigacyjny platformy Azure.

1. Po pomyślnym utworzeniu nowej usługi QnA Maker na platformie Azure wróć do strony qnamaker.ai/create. Wybierz usługę QnA Maker z listy rozwijanej w kroku 2. Jeśli utworzono nową usługę QnA Maker, pamiętaj o odświeżeniu strony.

   ![Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. W kroku 3 nazwij swoją bazę wiedzy **QNA KB**.

1. Aby dodać zawartość do bazy wiedzy, wybierz trzy typy źródeł danych. W kroku 4, w obszarze **Wypełnij bazę wiedzy** dodaj adres URL [Często zadawane pytania dotyczące odzyskiwania funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) w polu **adresu URL**.

   ![Zrzut ekranu przedstawiający Dodawanie źródeł danych](../media/qnamaker-quickstart-kb/add-datasources.png)

1. W kroku 5 wybierz pozycję **Tworzenie bazy wiedzy**.

1. Podczas QnA Maker tworzenia bazy wiedzy zostanie wyświetlone okno podręczne. Proces wyodrębniania zajmuje kilka minut, możesz więc przeczytać stronę HTML i zidentyfikować pytania i odpowiedzi.

1. Po pomyślnym utworzeniu bazy wiedzy przez QnA Maker zostanie otwarta strona **bazy wiedzy** . Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="edit-the-knowledge-base"></a>Edytowanie bazy wiedzy

1. W portalu QnA Maker w sekcji **Edycja** wybierz pozycję **Dodaj parę QNA** , aby dodać nowy wiersz do bazy wiedzy. W obszarze **Pytanie** wprowadź **Hi.** (Cześć). W obszarze **Odpowiedź** wprowadź **Hello. Pytaj o pytania funkcji BitLocker.**

    ![Zrzut ekranu przedstawiający Portal QnA Maker](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. W prawym górnym rogu wybierz pozycję **Zapisz i przeszkol**, aby zapisać wprowadzone zmiany i przeszkolić model usługi QnA Maker. Zmiany nie są przechowywane, o ile nie zostaną zapisane.

## <a name="test-the-knowledge-base"></a>Testowanie bazy wiedzy

1. W portalu QnA Maker w prawym górnym rogu wybierz pozycję **test** , aby sprawdzić, czy wprowadzone zmiany zostały wprowadzone. Wprowadź w polu `hi there`, a następnie naciśnij klawisz Enter. Powinna zostać wyświetlona odpowiedź utworzona jako reakcja.

1. Wybierz pozycję **Zbadaj**, aby bardziej szczegółowo sprawdzić odpowiedź. Okno testowe służy do testowania zmian w bazie wiedzy przed ich opublikowaniem.

    ![Zrzut ekranu przedstawiający panel testu](../media/qnamaker-quickstart-kb/inspect.png)

1. Wybierz ponownie pozycję **Test**, aby zamknąć wyskakujące okienko **Testowanie**.

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy

Po opublikowaniu bazy wiedzy zawartość pytania i odpowiedzi bazy wiedzy przechodzi z indeksu testu do indeksu produkcyjnego w usłudze Azure Search.

![Zrzut ekranu przedstawiający przemieszczenie zawartości bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. W portalu QnA Maker w menu obok pozycji **Edytuj**wybierz pozycję **Publikuj**. Aby potwierdzić, wybierz na stronie pozycję **Opublikuj**.

1. Usługa QnA Maker została teraz pomyślnie opublikowana. Możesz użyć punktu końcowego w swojej aplikacji lub kodu bota.

    ![Zrzut ekranu przedstawiający pomyślne opublikowanie](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Utwórz bota

Po opublikowaniu można utworzyć Bot na stronie **publikowania** : 

* Możesz szybko utworzyć kilka botów, wskazując tę samą bazę wiedzy dla różnych regionów lub planów cenowych dla poszczególnych botówów. 
* Jeśli potrzebujesz tylko jednego bot dla bazy wiedzy, użyj **widoku wszystkie Twoje botów w** łączu Azure Portal, aby wyświetlić listę bieżącego botów. 

Po wprowadzeniu zmian w bazie wiedzy i ponownym opublikowaniu nie trzeba podejmować dalszych działań z bot. Jest już skonfigurowana do pracy z bazą wiedzy i współpracuje ze wszystkimi przyszłymi zmianami w bazie wiedzy. Za każdym razem, gdy publikujesz bazę wiedzy, wszystkie połączone z nią botów są automatycznie aktualizowane.

1. W portalu QnA Maker na stronie **Publikowanie** wybierz pozycję **Utwórz bot**. Ten przycisk jest wyświetlany dopiero po opublikowaniu bazy wiedzy.

    ![Zrzut ekranu przedstawiający tworzenie bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Na stronie tworzenia Azure Bot Service zostanie otwarta nowa karta przeglądarki dla Azure Portal. Skonfiguruj usługę Azure bot. Aby uzyskać więcej informacji na temat tych ustawień konfiguracji, zobacz [Tworzenie QNA bot z Azure bot Service v4](../tutorials/create-qna-bot.md).
    
    * Nie należy zmieniać następujących ustawień w Azure Portal podczas tworzenia bot. Są one wstępnie wypełnione dla istniejącej bazy wiedzy: 
        * Klucz uwierzytelniania QnA
        * Plan i lokalizacja usługi App Service
        * Azure Storage
    * Bot i QnA Maker mogą korzystać z planu usługi aplikacji sieci Web, ale nie mogą udostępniać aplikacji sieci Web. Oznacza to, że **Nazwa aplikacji** musi być inna niż nazwa aplikacji użyta podczas tworzenia usługi QNA Maker. 


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](../How-To/create-knowledge-base.md)
