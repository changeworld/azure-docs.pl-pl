---
title: 'Szybki Start: Tworzenie KB — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Z własnej zawartości, takich jak często zadawanych pytań lub podręczników, można utworzyć usługi QnA Maker bazy wiedzy (KB). KB QnA Maker, w tym przykładzie jest tworzony z prostą stronę często zadawane pytania dotyczące odpowiedzi na pytania dotyczące klucza odzyskiwania funkcji BitLocker.
author: nitinme
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 2b8573da6cc87af39a7681fa369940fa7a0b8eb3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345237"
---
# <a name="create-train-and-publish-your-knowledge-base"></a>Tworzenie, uczenie i publikowanie bazy wiedzy

Z własnej zawartości, takich jak często zadawanych pytań lub podręczników, można utworzyć usługi QnA Maker bazy wiedzy (KB). KB QnA Maker, w tym przykładzie jest tworzony z prostą stronę często zadawane pytania dotyczące odpowiedzi na pytania dotyczące klucza odzyskiwania funkcji BitLocker.

## <a name="prerequisite"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker

1. Zaloguj się do QnAMaker.ai przy użyciu swoich poświadczeń platformy Azure.

2. W witrynie usługi QnA Maker wybierz **tworzenie bazy wiedzy**.

   ![Tworzenie nowej bazy wiedzy](../media/qna-maker-create-kb.png)

3. Na **Utwórz** strony w kroku 1, wybierz opcję **tworzenie pytań i odpowiedzi usługi**. Nastąpi przekierowanie do [witryny Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) do skonfigurowania usługi QnA Maker, w ramach subskrypcji. Jeśli upłynie limit czasu witryny Azure portal, wybierz opcję **ponownie** w witrynie. Po nawiązaniu połączenia, pojawi się pulpit nawigacyjny platformy Azure.

4. Po pomyślnym utworzeniu nowej usługi QnA Maker na platformie Azure powrócić do qnamaker.ai/create. Wybierz usługę pytań i odpowiedzi z listy rozwijanej w kroku 2. Jeśli utworzono nową usługę pytań i odpowiedzi, pamiętaj odświeżyć stronę.

   ![Wybierz usługę bazy wiedzy pytań i odpowiedzi](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. W kroku 3 nadaj nazwę wiedzy **Moje bazy wiedzy pytań i odpowiedzi przykładowe**.

6. Do dodawania zawartości do wiedzy, wybierz trzy rodzaje źródeł danych. W kroku 4, w obszarze **wypełnić wiedzy**, Dodaj [— często zadawane pytania dla odzyskiwania funkcji BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) adresu URL w **adresu URL** pole.

   ![Wybierz usługę bazy wiedzy pytań i odpowiedzi](../media/qnamaker-quickstart-kb/add-datasources.png)

7. W kroku 5 wybierz **tworzenie wiedzy**.

8. Gdy poprawka KB jest tworzony, pojawi się okno podręczne. Proces wyodrębniania zajmuje kilka minut, przeczytaj stronę HTML i zidentyfikować pytań i odpowiedzi.

9. Po pomyślnym utworzeniu KB **wiedzy** zostanie otwarta strona. Możesz edytować zawartość bazy wiedzy na tej stronie.

10. W prawym górnym rogu wybierz **pary dodawanie pytań i odpowiedzi** Aby dodać nowy wiersz w **redakcyjnych** sekcji KB. W obszarze **pytanie**, wprowadź **cześć.** W obszarze **odpowiedzi**, wprowadź **Hello. Pytaj pytań w funkcji bitlocker.**

   ![Dodaj parę pytań i odpowiedzi](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. W prawym górnym rogu wybierz **Zapisz i szkolenie** zapisać zmiany wprowadzone do nauczenia modelu usługi QnA Maker. Zmiany nie są przechowywane, chyba że są zapisywane.

   ![Zapisz i szkolenie](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. W prawym górnym rogu wybierz **Test** do przetestowania, czy zmiany wprowadzone weszło w życie. Wprowadź **cześć istnieje** w polu, a następnie naciśnij klawisz Enter. Powinien zostać wyświetlony odpowiedzi, który został utworzony w odpowiedzi.

13. Wybierz **Sprawdź** zbadać odpowiedź bardziej szczegółowo. Okna testów służy do testowania zmian w KB, zanim są publikowane.

   ![Panel testu](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Wybierz **testu** ponownie, aby zamknąć **testu** wyskakujących.

15. W menu obok **Edytuj**, wybierz opcję **Publikuj**. Aby potwierdzić, zaznacz **Publikuj** na stronie.

16. Usługa QnA Maker zostały pomyślnie opublikowane. Możesz użyć punktu końcowego w aplikacji lub kodu botów.

   ![Publikowanie](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](../How-To/create-knowledge-base.md)
