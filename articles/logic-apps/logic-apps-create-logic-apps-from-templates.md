---
title: Szybsze tworzenie przepływów pracy aplikacji logiki przy użyciu wstępnie utworzonych szablonów
description: Szybkie tworzenie przepływów pracy aplikacji logiki przy użyciu wstępnie utworzonych szablonów dostarczanych przez usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905113"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Tworzenie przepływów pracy aplikacji logiki na podstawie wbudowanych szablonów

Aby szybciej rozpocząć tworzenie przepływów pracy, aplikacje logiki udostępnia szablony, które są wstępnie utworzone aplikacje logiki, które są zgodne z powszechnie używanych wzorców. Użyj tych szablonów, jak podano lub edytować je, aby dopasować je do scenariusza.

Oto kilka kategorii szablonów:

| Typ szablonu | Opis | 
| ------------- | ----------- | 
| Szablony chmury przedsiębiorstwa | Do integracji azure blob, Dynamics CRM, Salesforce, Box i zawiera inne łączniki dla potrzeb chmury przedsiębiorstwa. Na przykład można użyć tych szablonów do organizowania potencjalnych klientów biznesowych lub kopii zapasowej danych plików firmowych. | 
| Szablony osobistej produktywności | Zwiększ produktywność osobistą, ustawiając codzienne przypomnienia, przekształcając ważne elementy robocze w listy zadań do wykonania i automatyzując długie zadania do jednego kroku zatwierdzania przez użytkownika. | 
| Szablony chmury konsumenckiej | Do integracji usług mediów społecznościowych, takich jak Twitter, Slack i e-mail. Przydatne do wzmacniania inicjatyw marketingowych w mediach społecznościowych. Szablony te obejmują również zadania, takie jak kopiowanie w chmurze, co zwiększa produktywność, oszczędzając czas na tradycyjnie powtarzalnych zadaniach. | 
| Szablony pakietów integracji przedsiębiorstwa | Do konfigurowania potoków VETER (sprawdzania poprawności, wyodrębniania, przekształcania, wzbogacania, marszruty), odbierania dokumentu EDI X12 za pomocą as2 i przekształcania w format XML oraz obsługi komunikatów X12, EDIFACT i AS2. | 
| Szablony wzorców protokołu | Do implementowania wzorców protokołów, takich jak odpowiedź na żądanie za pośrednictwem protokołu HTTP i integracji między FTP i SFTP. Użyj tych szablonów, jak podano, lub skompilować je dla złożonych wzorców protokołu. | 
||| 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Tworzenie aplikacji logiki przy użyciu szablonów

1. Jeśli jeszcze tego nie zrobiłeś, zaloguj się do [witryny Azure portal](https://portal.azure.com "Portal Azure").

2. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Aplikacji logiki****integracji** > przedsiębiorstwa .

   ![Azure Portal, Nowy, Integracja w przedsiębiorstwie, Aplikacja logiki](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Utwórz własną aplikację logiki z ustawieniami podanymi w tabeli pod tą ilustracją:

   ![Podawanie szczegółów aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | *Twoja nazwa aplikacji logiki* | Podaj unikatową nazwę aplikacji logiki. | 
   | **Subskrypcja** | *nazwa_Twojej_susbkrypcji_Azure* | Wybierz subskrypcję platformy Azure, której chcesz użyć. | 
   | **Grupa zasobów** | *nazwa_Twojej_grupy_zasobów_Azure* | Utwórz lub wybierz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) dla tej aplikacji logiki i zorganizować wszystkie zasoby skojarzone z tą aplikacją. | 
   | **Lokalizacja** | *region_Twojego_centrum_danych_Azure* | Wybierz region centrum danych do wdrożenia swojej aplikacji logiki, na przykład Zachodnie stany USA. | 
   | **Analiza dzienników** | **Wył.,** (domyślnie) lub **Włączone** | Skonfiguruj [rejestrowanie diagnostyczne](../logic-apps/monitor-logic-apps-log-analytics.md) dla aplikacji logiki przy użyciu [dzienników usługi Azure Monitor](../log-analytics/log-analytics-overview.md). Wymaga, aby masz już obszar roboczy usługi Log Analytics. | 
   |||| 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. Dzięki temu aplikacja logiki automatycznie pojawi się na pulpicie nawigacyjnym platformy Azure i zostanie otwarta po wdrożeniu. Wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz przypinać aplikacji logiki, to aby można było kontynuować, musisz ją ręcznie znaleźć i otworzyć po wdrożeniu.

   Gdy platforma Azure wdroży aplikację logiki, zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo. 
   Pod wideo znajdziesz szablony typowych wzorców aplikacji logiki. 

5. Przewiń obok wprowadzającego wideo i typowych wyzwalaczy do **szablonów**. Wybierz wstępnie utworzony szablon. Przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Aby utworzyć aplikację logiki od podstaw, wybierz **pustą aplikację logiki**.

   Po wybraniu wstępnie utworzonego szablonu można wyświetlić więcej informacji o tym szablonie. 
   Przykład:

   ![Wybieranie wstępnie utworzonego szablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Aby kontynuować korzystanie z wybranego szablonu, wybierz pozycję **Użyj tego szablonu**. 

7. Na podstawie łączników w szablonie zostanie wyświetlony monit o wykonanie dowolnego z następujących kroków:

   * Zaloguj się przy użyciu poświadczeń do systemów lub usług, do których odwołuje się szablon.

   * Tworzenie połączeń dla wszystkich usług lub systemów, do których odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia i w razie potrzeby wybierz zasób, którego chcesz użyć. 

   * Jeśli te połączenia zostały już skonfigurowane, wybierz pozycję **Kontynuuj**.

   Przykład:

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Po zakończeniu aplikacja logiki zostanie otwarta i pojawi się w Projektancie aplikacji logiki.

   > [!TIP]
   > Aby powrócić do przeglądarki **szablonów,** wybierz pozycję Szablony na pasku narzędzi projektanta. Ta akcja odrzuca wszelkie niezapisane zmiany, więc pojawi się komunikat ostrzegawczy potwierdzający twoje żądanie.

8. Kontynuuj tworzenie aplikacji logiki.

   > [!NOTE] 
   > Wiele szablonów zawiera łączniki, które mogą już wstępnie wypełnić wymagane właściwości. Jednak niektóre szablony mogą nadal wymagać podania wartości przed prawidłowym wdrożeniem aplikacji logiki. Jeśli spróbujesz wdrożyć bez ukończenia brakujących pól właściwości, zostanie wyświetlony komunikat o błędzie. 

## <a name="update-logic-apps-with-templates"></a>Aktualizowanie aplikacji logiki za pomocą szablonów

1. W [witrynie Azure portal](https://portal.azure.com "Portal Azure")znajdź i otwórz aplikację logiki w projektancie aplikacji logiki.

2. Na pasku narzędzi projektanta wybierz pozycję **Szablony**. Ta akcja odrzuca wszelkie niezapisane zmiany, więc pojawi się komunikat ostrzegawczy, dzięki czemu można potwierdzić, że chcesz kontynuować. Aby potwierdzić, wybierz **przycisk OK**. Przykład:

   ![Wybierz "Szablony"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Przewiń obok wprowadzającego wideo i typowych wyzwalaczy do **szablonów**. Wybierz wstępnie utworzony szablon. Przykład:

   ![Wybieranie szablonu aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Po wybraniu wstępnie utworzonego szablonu można wyświetlić więcej informacji o tym szablonie. 
   Przykład:

   ![Wybieranie wstępnie utworzonego szablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Aby kontynuować korzystanie z wybranego szablonu, wybierz pozycję **Użyj tego szablonu**. 

5. Na podstawie łączników w szablonie zostanie wyświetlony monit o wykonanie dowolnego z następujących kroków:

   * Zaloguj się przy użyciu poświadczeń do systemów lub usług, do których odwołuje się szablon.

   * Tworzenie połączeń dla wszystkich usług lub systemów, do których odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia i w razie potrzeby wybierz zasób, którego chcesz użyć. 

   * Jeśli te połączenia zostały już skonfigurowane, wybierz pozycję **Kontynuuj**.

   ![Tworzenie połączeń](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Aplikacja logiki zostanie otwarta i pojawi się w Projektancie aplikacji logiki.

8. Kontynuuj tworzenie aplikacji logiki. 

   > [!TIP]
   > Jeśli zmiany nie zostały zapisane, możesz odrzucić pracę i powrócić do poprzedniej aplikacji logiki. Na pasku narzędzi projektanta wybierz pozycję **Odrzuć**.

> [!NOTE] 
> Wiele szablonów zawiera łączniki, które mogły już wstępnie wypełnione wymagane właściwości. Jednak niektóre szablony mogą nadal wymagać podania wartości przed prawidłowym wdrożeniem aplikacji logiki. Jeśli spróbujesz wdrożyć bez ukończenia brakujących pól właściwości, zostanie wyświetlony komunikat o błędzie.

## <a name="deploy-logic-apps-built-from-templates"></a>Wdrażanie aplikacji logicznych utworzonych na podstawie szablonów

Po wniesieniu zmian do szablonu można zapisać zmiany. Ta akcja również automatycznie publikuje aplikację logiki.

Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

![Zapisywanie i publikowanie aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tworzeniu aplikacji logiki za pomocą przykładów, scenariuszy, wątków klientów i instruktajnów.

> [!div class="nextstepaction"]
> [Przejrzyj przykłady aplikacji logiki, scenariusze i wskazówki](../logic-apps/logic-apps-examples-and-scenarios.md)