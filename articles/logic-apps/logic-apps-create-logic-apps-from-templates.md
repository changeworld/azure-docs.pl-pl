---
title: Tworzenie przepływów pracy za pomocą szablonów — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie przepływów pracy, szybciej przy użyciu szablonów aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459697"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Tworzenie przepływów pracy aplikacji logiki z wbudowanych szablonów

Aby rozpocząć pracę, szybsze tworzenie przepływów pracy, Logic Apps oferuje szablonów, które są aplikacje logiki ze wstępnie utworzonych, które należy wykonać często używane wzorce. Korzystanie z tych szablonów, zgodnie z postanowieniami lub Edytuj ich odpowiednio do scenariusza.

Poniżej przedstawiono niektóre kategorie szablonu:

| Typ szablonu | Opis | 
| ------------- | ----------- | 
| Szablony chmury przedsiębiorstwa | Do integrowania obiektów Blob platformy Azure, Dynamics CRM, Salesforce, Box i zawiera inne łączniki potrzeby chmurą przedsiębiorstwa. Na przykład można użyć tych szablonów do organizowania potencjalni klienci i kopii zapasowej danych plików firmowych. | 
| Szablony zwiększające produktywność użytkowników | Zwiększyć produktywność, ustawiając codzienne przypomnienia, włączając ważne elementy robocze do listy zadań do wykonania i automatyzację długich zadań w dół, aby krok zatwierdzenia pojedynczego użytkownika. | 
| Szablony chmury konsumentów | Integrowanie usług mediów społecznościowych, takich jak Twitter, Slack i poczty e-mail. Przydatne do wzmocnienia mediów społecznościowych, marketing inicjatyw. Te szablony są także zadań, takich jak kopiowanie, chmury, które zwiększają produktywność, zaoszczędzić czas tradycyjnie powtarzających się zadań. | 
| Szablony pakietów integracji przedsiębiorstw | Do konfigurowania VETER (Sprawdzanie poprawności, wyodrębnianie, przekształcanie, wzbogacanie, trasy) potoków odbieranie X12 dokumentu EDI, AS2 i Przekształcanie XML oraz obsługa X12, EDIFACT i komunikatów AS2. | 
| Protokół wzorzec szablonów | Do implementowania protokołu wzorców, takich jak odpowiedź na żądanie za pośrednictwem protokołu HTTP i integracji między FTP i SFTP. Zgodnie z postanowieniami za pomocą tych szablonów lub kompilacji na nich protokołu złożonych wzorców. | 
||| 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Aby uzyskać więcej informacji dotyczących tworzenia aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Tworzenie aplikacji logiki z szablonów

1. Jeśli jeszcze nie, zaloguj się do [witryny Azure portal](https://portal.azure.com "witryny Azure portal").

2. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Azure Portal, Nowy, Integracja w przedsiębiorstwie, Aplikacja logiki](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Utwórz własną aplikację logiki z ustawieniami podanymi w tabeli pod tą ilustracją:

   ![Podawanie szczegółów aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | *Twoja nazwa aplikacji logiki* | Podaj unikatową nazwę aplikacji logiki. | 
   | **Subskrypcja** | *nazwa_Twojej_susbkrypcji_Azure* | Wybierz subskrypcję platformy Azure, której chcesz użyć. | 
   | **Grupa zasobów** | *nazwa_Twojej_grupy_zasobów_Azure* | Utwórz lub wybierz [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) dla tej aplikacji logiki i organizowania wszystkie zasoby skojarzone z tą aplikacją. | 
   | **Lokalizacja** | *region_Twojego_centrum_danych_Azure* | Wybierz region centrum danych do wdrożenia swojej aplikacji logiki, na przykład Zachodnie stany USA. | 
   | **Log Analytics** | **Wyłącz** (ustawienie domyślne) lub **na** | Włącz [rejestrowania diagnostycznego](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) aplikacji logiki za pomocą [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md). Wymaga się, że masz już obszar roboczy usługi Log Analytics. | 
   |||| 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. Dzięki temu aplikacja logiki automatycznie pojawi się na pulpicie nawigacyjnym platformy Azure i zostanie otwarta po wdrożeniu. Wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz przypinać aplikacji logiki, to aby można było kontynuować, musisz ją ręcznie znaleźć i otworzyć po wdrożeniu.

   Gdy platforma Azure wdroży aplikację logiki, zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo. 
   Pod wideo znajdziesz szablony typowych wzorców aplikacji logiki. 

5. Przewiń poza wprowadzenie wideo i typowe wyzwalacze do **szablony**. Wybierz szablon wbudowanych. Na przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Aby utworzyć aplikację logiki od podstaw, wybierz **pusta aplikacja logiki**.

   Po wybraniu wstępnie utworzonych szablonów, można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybierz szablon wstępnie](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Aby kontynuować z wybranego szablonu, wybierz **za pomocą tego szablonu**. 

7. Oparte na łączniki w szablonie, zostanie wyświetlony monit o wykonaj jedną z następujących czynności:

   * Zaloguj się przy użyciu poświadczeń do systemów lub usług, które są przywoływane przez szablon.

   * Tworzenie połączeń dla dowolnej usługi lub systemy dołączane do szablonu. Aby utworzyć połączenie, podaj nazwę połączenia, a jeśli to konieczne, wybierz zasób, którego chcesz używać. 

   * Jeśli skonfigurowano już tych połączeń, wybierz opcję **Kontynuuj**.

   Na przykład:

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Gdy wszystko będzie gotowe, aplikacja logiki zostanie otwarty i pojawia się w Projektancie aplikacji logiki.

   > [!TIP]
   > Aby powrócić do podglądu szablonu, wybierz **szablony** na pasku narzędzi projektanta. Ta akcja odrzuca wszystkie niezapisane zmiany, aby potwierdzić żądanie pojawi się komunikat ostrzegawczy.

8. Kontynuuj tworzenie aplikacji logiki.

   > [!NOTE] 
   > Wiele szablonów obejmują łączników, które mogą już mieć wstępnie wymaganych właściwości. Jednak niektóre szablony mogą nadal wymagać, podaj wartości, można było prawidłowo wdrożyć aplikację logiki. Jeśli zostanie podjęta próba wdrożenia nie kończą działania brakujące pola właściwości, otrzymasz komunikat o błędzie. 

## <a name="update-logic-apps-with-templates"></a>Aktualizowanie aplikacji logiki przy użyciu szablonów

1. W [witryny Azure portal](https://portal.azure.com "witryny Azure portal"), Znajdź i Otwórz swoją aplikację logiki w tym projektancie aplikacji logiki.

2. Na pasku narzędzi Projektanta wybierz **szablony**. Ta akcja odrzuca wszystkie niezapisane zmiany, tak aby można było stwierdzić, czy chcesz kontynuować pojawi się komunikat ostrzegawczy. Aby upewnić się, wybierz **OK**. Na przykład:

   ![Wybierz pozycję "Templates"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Przewiń poza wprowadzenie wideo i typowe wyzwalacze do **szablony**. Wybierz szablon wbudowanych. Na przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Po wybraniu wstępnie utworzonych szablonów, można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybierz szablon wstępnie](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Aby kontynuować z wybranego szablonu, wybierz **za pomocą tego szablonu**. 

5. Oparte na łączniki w szablonie, zostanie wyświetlony monit o wykonaj jedną z następujących czynności:

   * Zaloguj się przy użyciu poświadczeń do systemów lub usług, które są przywoływane przez szablon.

   * Tworzenie połączeń dla dowolnej usługi lub systemy dołączane do szablonu. Aby utworzyć połączenie, podaj nazwę połączenia, a jeśli to konieczne, wybierz zasób, którego chcesz używać. 

   * Jeśli skonfigurowano już tych połączeń, wybierz opcję **Kontynuuj**.

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Twoja aplikacja logiki teraz otwiera i pojawia się w Projektancie aplikacji logiki.

8. Kontynuuj tworzenie aplikacji logiki. 

   > [!TIP]
   > Jeśli nie zostały zapisane zmiany, możesz odrzucić swoją pracę i powrócić do poprzedniej aplikacji logiki. Na pasku narzędzi Projektanta wybierz **odrzucić**.

> [!NOTE] 
> Wiele szablonów obejmują łączników, które może mieć już wstępnie wypełnione wymaganych właściwości. Jednak niektóre szablony mogą nadal wymagać, podaj wartości, można było prawidłowo wdrożyć aplikację logiki. Jeśli zostanie podjęta próba wdrożenia nie kończą działania brakujące pola właściwości, otrzymasz komunikat o błędzie.

## <a name="deploy-logic-apps-built-from-templates"></a>Wdrażanie aplikacji logiki utworzone za pomocą szablonów

Po wprowadzeniu zmian do szablonu, można zapisać zmiany. Ta akcja automatycznie publikuje aplikację logiki.

Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

![Zapisywanie i publikowanie aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat tworzenia aplikacji logiki za pomocą przykłady, scenariusze, historie i przewodniki.

> [!div class="nextstepaction"]
> [Przejrzyj przykłady aplikacji logiki, scenariusze i przewodniki](../logic-apps/logic-apps-examples-and-scenarios.md)