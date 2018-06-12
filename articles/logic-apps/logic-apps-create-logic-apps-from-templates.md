---
title: Tworzenie przepływów pracy za pomocą szablonów - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie przepływów pracy szybsze przy użyciu szablonów aplikacji logiki
author: kevinlam1
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72aca1420d76a34f8f43f05b25d146eb743b3233
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298692"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Tworzenie przepływów pracy aplikacji logiki z wbudowane szablony

Aby zacząć szybsze tworzenie przepływów pracy, Logic Apps oferuje szablonów, które są wbudowane logikę aplikacji, które należy wykonać typowe wzorce. Określone za pomocą tych szablonów lub edytować je do danego scenariusza.

Poniżej przedstawiono niektóre kategorie szablonów:

| Typ szablonu | Opis | 
| ------------- | ----------- | 
| Szablony Enterprise chmury | Do integracji obiektów Blob platformy Azure, Dynamics CRM usługi Salesforce, okno i zawiera inne łączniki dla potrzeb chmury przedsiębiorstwa. Na przykład można użyć tych szablonów do organizowania potencjalni klienci lub kopii zapasowej danych plików firmowych. | 
| Szablony produktywność | Zwiększyć produktywność przez ustawienie przypomnienia codziennie, włączając ważne elementy robocze do listy zadań do wykonania i automatyzowanie zadań długich krok zatwierdzenia jednego użytkownika. | 
| Szablony konsumentów chmury | Integrowanie usług mediów społecznościowych, takich jak usługi Twitter, Zapas i poczty e-mail. Przydatne w przypadku wzmocnienia marketingu inicjatyw mediów społecznościowych. Te szablony obejmują również zadania, takie jak kopiowanie, chmury, co zwiększa wydajność, aby zaoszczędzić czas tradycyjnie powtarzających się zadań. | 
| Szablony pakietów integracji przedsiębiorstwa | Do konfigurowania VETER (Sprawdzanie poprawności, extract, transform, uzupełnić, trasy) potoki odbieranie X12 EDI dokumentu za pośrednictwem AS2 i Przekształcanie XML i obsługi X12, EDIFACT i AS2 wiadomości. | 
| Protokół wzorzec szablonów | Do implementowania protokołu wzorców, takich jak żądań i odpowiedzi przez HTTP i integracji między FTP i SFTP. Określone za pomocą tych szablonów, lub utworzyć na nich wzorców złożonych protokołu. | 
||| 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Aby uzyskać więcej informacji dotyczących tworzenia aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Tworzenie aplikacji logiki z szablonów

1. Jeśli nie jest jeszcze, zaloguj się do [portalu Azure](https://portal.azure.com "portalu Azure").

2. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

   ![Azure Portal, Nowy, Integracja w przedsiębiorstwie, Aplikacja logiki](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Utwórz własną aplikację logiki z ustawieniami podanymi w tabeli pod tą ilustracją:

   ![Podawanie szczegółów aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Ustawienie | Wartość | Opis | 
   | ------- | ----- | ----------- | 
   | **Nazwa** | *Twoja nazwa aplikacji logiki* | Podaj unikatową nazwę aplikacji logiki. | 
   | **Subskrypcja** | *nazwa_Twojej_susbkrypcji_Azure* | Wybierz subskrypcję platformy Azure, której chcesz użyć. | 
   | **Grupa zasobów** | *nazwa_Twojej_grupy_zasobów_Azure* | Utwórz lub wybierz [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) dla tej aplikacji logiki i zorganizować wszystkie zasoby skojarzone z tą aplikacją. | 
   | **Lokalizacja** | *region_Twojego_centrum_danych_Azure* | Wybierz region centrum danych do wdrożenia swojej aplikacji logiki, na przykład Zachodnie stany USA. | 
   | **Log Analytics** | **Wyłącz** (ustawienie domyślne) lub **na** | Włącz [rejestrowania diagnostycznego](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) aplikacji logiki za pośrednictwem [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Wymaga już obszaru roboczego analizy dzienników. | 
   |||| 

4. Gdy wszystko będzie gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. Dzięki temu aplikacja logiki automatycznie pojawi się na pulpicie nawigacyjnym platformy Azure i zostanie otwarta po wdrożeniu. Wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz przypinać aplikacji logiki, to aby można było kontynuować, musisz ją ręcznie znaleźć i otworzyć po wdrożeniu.

   Gdy platforma Azure wdroży aplikację logiki, zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo. 
   Pod wideo znajdziesz szablony typowych wzorców aplikacji logiki. 

5. Przewijania poza wyzwalaczy wideo i wspólne wprowadzenie do **szablony**. Wybierz szablon wbudowane. Na przykład:

   ![Wybierz szablon aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Aby utworzyć aplikację logiki od początku, wybierz **pustą aplikację logiki**.

   Po wybraniu szablonu wbudowane można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybierz szablon wbudowane](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Aby kontynuować z wybranego szablonu, wybierz **użyć tego szablonu**. 

7. W oparciu o łączników w szablonie, zostanie wyświetlony monit o wykonaj jedną z następujących czynności:

   * Zaloguj się przy użyciu poświadczeń do systemów i usług, do których odwołuje się szablon.

   * Utwórz połączenia dla żadnych usług ani systemów odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia, a w razie potrzeby wybierz zasób, którego chcesz używać. 

   * Jeśli już skonfigurowane te połączenia wybierz **Kontynuuj**.

   Na przykład:

   ![Utwórz połączenia](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Gdy wszystko będzie gotowe, aplikację logiki otwiera i zostanie wyświetlony w Projektancie aplikacji logiki.

   > [!TIP]
   > Aby powrócić do podglądu szablonu, wybierz **szablony** na pasku narzędzi projektanta. Ta akcja spowoduje odrzucenie wszystkich niezapisanych zmian, zostanie wyświetlone ostrzeżenie o potwierdzenie żądania.

8. Kontynuuj tworzenie aplikacji logiki.

   > [!NOTE] 
   > Wiele szablonów obejmują łączniki, które mogą już mieć wstępnie wymaganych właściwości. Jednak w niektórych szablonach Podaj wartości, zanim będzie można prawidłowo wdrożyć aplikację logiki nadal mogą wymagać. Jeśli próbujesz wdrożyć przerywając brakujące pola właściwości, otrzymasz komunikat o błędzie. 

## <a name="update-logic-apps-with-templates"></a>Aktualizowanie aplikacji logiki z szablonami

1. W [portalu Azure](https://portal.azure.com "portalu Azure"), znajdowanie i otwieranie aplikacji logiki w tym projektancie aplikacji logiki.

2. Na pasku narzędzi projektanta, wybierz **szablony**. Ta akcja spowoduje odrzucenie wszystkich niezapisanych zmian, pojawi się ostrzeżenie, można potwierdzić, czy chcesz kontynuować. Aby upewnić się, wybierz **OK**. Na przykład:

   ![Wybierz pozycję "Szablony"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Przewijania poza wyzwalaczy wideo i wspólne wprowadzenie do **szablony**. Wybierz szablon wbudowane. Na przykład:

   ![Wybierz szablon aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Po wybraniu szablonu wbudowane można wyświetlić więcej informacji na temat tego szablonu. 
   Na przykład:

   ![Wybierz szablon wbudowane](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Aby kontynuować z wybranego szablonu, wybierz **użyć tego szablonu**. 

5. W oparciu o łączników w szablonie, zostanie wyświetlony monit o wykonaj jedną z następujących czynności:

   * Zaloguj się przy użyciu poświadczeń do systemów i usług, do których odwołuje się szablon.

   * Utwórz połączenia dla żadnych usług ani systemów odwołuje się szablon. Aby utworzyć połączenie, podaj nazwę połączenia, a w razie potrzeby wybierz zasób, którego chcesz używać. 

   * Jeśli już skonfigurowane te połączenia wybierz **Kontynuuj**.

   ![Utwórz połączenia](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Teraz aplikację logiki otwiera i zostanie wyświetlony w Projektancie aplikacji logiki.

8. Kontynuuj tworzenie aplikacji logiki. 

   > [!TIP]
   > Jeśli zmiany nie zostały zapisane, można odrzucić pracy i wrócić do poprzedniej aplikacji logiki. Na pasku narzędzi projektanta, wybierz **odrzucić**.

> [!NOTE] 
> Wiele szablonów obejmują łączniki, które może być już wstępnie wypełnione wymagane właściwości. Jednak w niektórych szablonach Podaj wartości, zanim będzie można prawidłowo wdrożyć aplikację logiki nadal mogą wymagać. Jeśli próbujesz wdrożyć przerywając brakujące pola właściwości, otrzymasz komunikat o błędzie.

## <a name="deploy-logic-apps-built-from-templates"></a>Wdrażanie aplikacji logiki skompilowanych za pomocą szablonów

Po wprowadzeniu zmian do szablonu, możesz zapisać zmiany. Ta akcja publikuje również automatycznie aplikacji logiki.

Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

![Zapisz i publikowanie aplikacji logiki](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat tworzenia aplikacji logiki za pośrednictwem przykłady, scenariusze wątki klienta i wskazówki.

> [!div class="nextstepaction"]
> [Przejrzyj przykłady aplikacji logiki, scenariusze i wskazówki](../logic-apps/logic-apps-examples-and-scenarios.md)