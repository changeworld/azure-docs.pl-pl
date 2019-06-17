---
title: Rozliczenia platformy Azure i kosztów zarządzania budżetu scenariusza | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure automation do wyłączania maszyn wirtualnych na podstawie progów określonych budżetu.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: erikre
ms.openlocfilehash: 4bf76ac0bdd59764815f18a40a3e243d7cf9d920
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617389"
---
# <a name="manage-costs-with-azure-budgets"></a>Zarządzanie kosztami przy użyciu budżetów platformy Azure

Kontrola kosztów to kluczowy składnik do jednoczesnej maksymalizacji wartości inwestycji w chmurze. Istnieje kilka scenariuszy, gdzie są krytyczne dla działalności biznesowej ciągłej widoczności kosztów, raportowanie i aranżacji w oparciu o koszty. [Interfejsy API zarządzania platformy Azure koszt](https://docs.microsoft.com/rest/api/consumption/) zawierają zestaw interfejsów API do obsługi każdego z tych scenariuszy. Interfejsy API zapewniają szczegóły użycia, co pozwala wyświetlić szczegółową wystąpienia poziomu koszty.

Budżetów są często używane w ramach kontroli kosztów. Można zmniejszyć zakres budżet na platformie Azure. Na przykład można zawęzić Widok budżetu na podstawie subskrypcji, grupy zasobów lub kolekcję zasobów. Oprócz używania budżetów interfejsu API, aby otrzymywać powiadomienia, za pośrednictwem poczty e-mail po osiągnięciu progu budżetu, można użyć [grup akcji usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) do wyzwolenia zorganizowane zestawu akcji w wyniku zdarzenia budżetu.

Typowy scenariusz budżetów dla klienta, zastosowania niekrytyczne obciążenie może wystąpić, gdy ich chcesz zarządzać budżetem, a także uzyskać przewidywalną koszt podczas przeglądania miesięcznej fakturze. Ten scenariusz wymaga pewnych aranżacji na podstawie kosztów zasobów, które są częścią środowiska platformy Azure. W tym scenariuszu ustawiono miesięcznego budżetu 1000 USD dla subskrypcji. Ponadto progi powiadomień można ustawić wyzwalanie kilku mechanizmów. Ten scenariusz rozpoczyna się od progu 80% kosztów, które spowoduje zatrzymanie wszystkich maszyn wirtualnych w grupie zasobów **opcjonalnie**. Następnie na próg 100% kosztów, zostaną zatrzymane wszystkie wystąpienia maszyn wirtualnych.
Można skonfigurować w tym scenariuszu, postępując zgodnie z krokami opisanymi w każdej sekcji tego samouczka zostaną wykonane następujące akcje.

Te akcje uwzględnione w tym samouczku umożliwiają:

- Tworzenie elementu Runbook usługi Azure Automation do zatrzymywania maszyn wirtualnych przy użyciu elementów webhook.
- Tworzenie aplikacji logiki platformy Azure, aby być wyzwalane na podstawie wartości próg budżetu i wywoływanie elementu runbook z parametrami prawo.
- Utwórz grupę akcji usługi Azure Monitor, która zostanie skonfigurowana w celu wyzwolenia aplikacji logiki platformy Azure, po osiągnięciu progu budżetu.
- Tworzenie budżetu na platformie Azure przy użyciu odpowiednie progi i połączenie go do grupy akcji.

## <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu Runbook usługi Azure Automation

[Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) to usługa, która pozwala na większość zadań zarządzania systemem zasobu skryptu i uruchamiać te zadania zaplanowane lub na żądanie. W ramach tego scenariusza, utworzysz [elementu runbook usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types) który będzie używany do zatrzymywania maszyn wirtualnych. Użyjesz [zatrzymać V2 maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) graficznego elementu runbook z [galerii](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) do tworzenia tego scenariusza. Importowanie tego elementu runbook do Twojego konta platformy Azure i publikując ją, można do zatrzymywania maszyn wirtualnych, po osiągnięciu progu budżetu.

### <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
2. Kliknij przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu platformy Azure.
3. Wybierz **narzędzia do zarządzania** > **automatyzacji**.
   > [!NOTE]
   > Jeśli nie masz konta platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
4. Wprowadź informacje o Twoim koncie. Dla **Tworzenie konta Uruchom jako platformy**, wybierz **tak** można automatycznie włączyć ustawienia potrzebne upraszczające uwierzytelnianie na platformie Azure.
5. Po zakończeniu kliknij przycisk **Utwórz**, aby rozpocząć wdrażanie konta usługi Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importowanie elementu runbook zatrzymać V2 maszyn wirtualnych platformy Azure

Za pomocą [elementu runbook usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-types), zaimportuj [zatrzymać V2 maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) graficznego elementu runbook z galerii.

1.  Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
2.  Otwórz konto usługi Automation, wybierając **wszystkich usług** > **kont usługi Automation**. Następnie wybierz swoje konto usługi Automation.
3.  Kliknij przycisk **Galeria elementów Runbook** z **automatyzacji procesów** sekcji.
4.  Ustaw **źródło galerii** do **Centrum skryptów** i wybierz **OK**.
5.  Znajdź i zaznacz [zatrzymać V2 maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) elementu galerii w witrynie Azure portal.
6.  Kliknij przycisk **importu** przycisk, aby wyświetlić **importu** bloku, a następnie wybierz pozycję **OK**. Zostanie wyświetlony blok Przegląd elementu runbook.
7.  Po ukończeniu procesu importowania elementu runbook, zaznacz **Edytuj** do wyświetlenia opcji edytora i publikowanie graficznego elementu runbook.

    ![Azure — Edytuj graficzny element runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Kliknij przycisk **Publikuj** przycisk, aby opublikować element runbook, a następnie wybierz pozycję **tak** po wyświetleniu monitu. Podczas publikowania elementu runbook, zastąpienie istniejącej opublikowanej wersji z wersją roboczą. W tym przypadku masz nie opublikowanej wersji, ponieważ utworzono element runbook.

    Aby uzyskać więcej informacji na temat publikowania elementu runbook, zobacz [tworzenie graficznego elementu runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Utwórz elementy webhook dla elementu runbook

Za pomocą [zatrzymać V2 maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) graficznego elementu runbook, spowoduje utworzenie dwóch elementów Webhook, aby uruchomić element runbook w usłudze Azure Automation za pośrednictwem pojedynczego żądania HTTP. Pierwszy element webhook wywoła elementu runbook na próg 80% budżetu, nazwą grupy zasobów jako parametru, dzięki czemu można zatrzymać opcjonalne maszyn wirtualnych. Drugi element webhook będzie następnie wywołać element runbook bez parametrów (w 100%), które zostanie zatrzymane wszystkie pozostałe wystąpienia maszyn wirtualnych.

1. Z **elementów Runbook** strony w [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **StopAzureV2Vm** elementu runbook, który zawiera blok przeglądu elementu runbook.
2. Kliknij przycisk **elementu Webhook** w górnej części strony Aby otworzyć **Dodaj element Webhook** bloku.
3. Kliknij przycisk **Utwórz nowy element webhook** otworzyć **Utwórz nowy element webhook** bloku.
4. Ustaw **nazwa** elementu Webhook do **opcjonalne**. **Włączone** właściwość musi być **tak**. **Expires** wartości nie muszą zostać zmienione. Aby uzyskać więcej informacji na temat właściwości elementu Webhook, zobacz [szczegóły elementu webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Obok wartości adresu URL kliknij ikonę kopiowania, aby skopiować adres URL elementu webhook.
   > [!IMPORTANT]
   > Zapisz adres URL elementu webhook o nazwie **opcjonalnie** w bezpiecznym miejscu. Adres URL będzie używać w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook, nie można wyświetlić lub ponownie pobrać adresu URL.
6. Kliknij przycisk **OK** do utworzenia nowego elementu webhook.
7. Kliknij przycisk **skonfigurować parametry i parametrów uruchomieniowych** zobrazit parametr wartości dla elementu runbook.
   > [!NOTE]
   > Jeśli element runbook ma parametry obowiązkowe, następnie nie jest możliwe do utworzenia elementu webhook, chyba że wartości są podane.
8. Kliknij przycisk **OK** zaakceptować wartości parametrów elementu webhook.
9. Kliknij przycisk **Utwórz** tworzenia elementu webhook.
10. Następnie postępuj zgodnie z instrukcjami powyżej, aby utworzyć drugiego elementu webhook o nazwie **Complete**.
    > [!IMPORTANT]
    > Pamiętaj zapisać zarówno adresy URL elementu webhook, w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook, nie można wyświetlić lub ponownie pobrać adresu URL.

Teraz masz dwa skonfigurowanych elementów webhook, które są każdego dostępne adresy URL, które zostały zapisane za pośrednictwem.

![Elementy Webhook - opcjonalne i zakończenie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Teraz wszystko z konfiguracją usługi Azure Automation. Możesz przetestować elementów webhook z prosty test narzędzia Postman, aby sprawdzić, czy działa elementu webhook. Następnie należy utworzyć aplikację logiki do aranżacji.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Tworzenie aplikacji logiki platformy Azure do aranżacji

Aplikacje logiki ułatwiają tworzenie, planowanie i automatyzowanie procesów jako przepływy pracy, aby można było integrować aplikacje, dane, systemy i usługi między przedsiębiorstwami i organizacjami. W tym scenariuszu [aplikacji logiki](https://docs.microsoft.com/azure/logic-apps/) tworzenie będzie zrobić trochę więcej niż tylko wywołania element webhook usługi automation został utworzony.

Budżetów można skonfigurować tak, aby wyzwolić powiadomienie po osiągnięciu określonego progu. Możesz dostarczyć wiele progi, aby otrzymywać powiadomienia o, a aplikacja logiki zademonstruje możliwości służących do wykonywania różnych działań na podstawie progu spełnione. W tym przykładzie ustawisz zrealizować scenariusz, gdzie uzyskać kilka powiadomień, jest gdy osiągnął 80% budżetu, a drugie powiadomienie po 100% budżetu zostanie osiągnięty pierwszy powiadomień. Aplikacja logiki będzie służyć do zamykania wszystkich maszyn wirtualnych w grupie zasobów. Po pierwsze, **opcjonalnie** próg zostanie osiągnięta 80%, a następnie drugie próg zostanie podjęta, gdzie spowoduje wyłączenie wszystkich maszyn wirtualnych w subskrypcji.

Aplikacje logiki umożliwiają zapewniają próbki schematu dla wyzwalacza HTTP, ale wymagają ustawienia **Content-Type** nagłówka. Ponieważ grupy akcji nie ma nagłówki niestandardowe dla elementu webhook, należy przeanalizować się ładunek w osobnym kroku. Użyjesz **przeanalizować** akcji i udostępniać przykładowy ładunek.

### <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Aplikacja logiki będzie wykonywać kilka czynności. Poniższa lista zawiera ogólny zestaw akcji, które uruchomi aplikację logiki:
- Rozpoznaje po odebraniu żądania HTTP
- Analizowania przekazana w danych JSON, aby określić wartość progowa, który został osiągnięty
- Użyj instrukcji warunkowej, aby sprawdzić, czy wartości progowej osiągnął 80% lub więcej zakresu budżetu, ale nie jest większa niż lub równa 100%.
    - Jeśli osiągnięty tego progu, wysłać metodę POST protokołu HTTP przy użyciu elementu webhook o nazwie **opcjonalnie**. Ta akcja spowoduje wyłączenie maszyny wirtualne w grupie "Opcjonalny".
- Użyj instrukcji warunkowej, aby sprawdzić, czy wartości progowej ma osiągnięto lub Przekroczono 100% wartości budżetu.
    - Jeśli osiągnięty wartości progowej, Wyślij metodę POST protokołu HTTP przy użyciu elementu webhook o nazwie **Complete**. Ta akcja spowoduje wyłączenie wszystkich pozostałych maszyn wirtualnych.

Aby utworzyć aplikację logiki, która będzie wykonaj powyższe kroki potrzebne są następujące czynności:

1.  W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.

    ![Azure — wybierz zasobu aplikacji logiki](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  W **tworzenie aplikacji logiki** bloku, zapewniają szczegółowe informacje, należy utworzyć aplikację logiki, wybierz opcję **Przypnij do pulpitu nawigacyjnego**i kliknij przycisk **Utwórz**.

    ![Azure — tworzenie aplikacji logiki](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Gdy platforma Azure wdroży aplikację logiki **Projektant aplikacji logiki** otwiera i przedstawiono bloku z wprowadzającym wideo i najczęściej używanych wyzwalaczy.

### <a name="add-a-trigger"></a>Dodawanie wyzwalacza

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy. akcje to wszystkie kroki wykonywane po aktywowaniu wyzwalacza.

1.  W obszarze **szablony** z **Projektant aplikacji logiki** bloku wybierz **pusta aplikacja logiki**.
2.  Dodaj [wyzwalacza](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) , wprowadzając "żądania http" w **Projektant aplikacji logiki** pola wyszukiwania, aby znaleźć i wybrać wyzwalacza o nazwie **żądania — zostanie odebrane żądanie HTTP podczas**.

    ![Azure — aplikacji logiki — wyzwalacz Http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Wybierz **nowy krok** > **Dodaj akcję**.

    ![Azure — nowy krok — Dodaj akcję](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Wyszukaj "Przeanalizuj dane JSON" w **Projektant aplikacji logiki** pola wyszukiwania, aby znaleźć i wybrać **operacje na danych — Przeanalizuj dane JSON** [akcji](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Azure — aplikacji logiki — Dodaj przeanalizować JSON akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Wprowadź "Ładunek" jako **zawartości** nazw dla ładunku Przeanalizuj dane JSON lub użyj tagu "Treść" z zawartości dynamicznej.
6.  Wybierz **Użyj przykładowego ładunku do wygenerowania schematu** opcji **Przeanalizuj dane JSON** pole.

    ![Użyj platformy Azure — aplikacji logiki — przykładowych danych JSON do wygenerowania schematu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Wklej następujący przykładowy ładunek JSON do pola tekstowego: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Pola tekstowego pojawi się następujący:

    ![Azure — aplikacji logiki — przykładowy ładunek JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Kliknij przycisk **Gotowe**.

### <a name="add-the-first-conditional-action"></a>Dodaj pierwszą akcją warunkowe

Użyj instrukcji warunkowej, aby sprawdzić, czy wartości progowej osiągnął 80% lub więcej zakresu budżetu, ale nie jest większa niż lub równa 100%. Jeśli osiągnięty tego progu, wysłać metodę POST protokołu HTTP przy użyciu elementu webhook o nazwie **opcjonalnie**. Ta akcja spowoduje wyłączenie maszyny wirtualne w **opcjonalnie** grupy.

1.  Wybierz **nowy krok** > **Dodaj warunek**.

    ![Azure — aplikacji logiki — Dodaj warunek](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  W **warunek** kliknij pole tekstowe, zawierające **wybierz wartość** Aby wyświetlić listę dostępnych wartości.

    ![Pola warunku - aplikacji logiki — Azure](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Kliknij przycisk **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`

    ![Azure — aplikacji logiki — Float wyrażenia](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Wybierz **zawartości dynamicznej**, umieść kursor wewnątrz (nawias) i wybierz **NotificationThresholdAmount** z listy, aby wypełnić Dokończ wyrażenie.

    Wyrażenie będzie następujący:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wybierz **OK** można ustawić wyrażenia.
6.  Wybierz **jest większa niż lub równa** w polu listy rozwijanej **warunek**.
7.  W **wybierz wartość** warunku wprowadź `.8`.

    ![Azure — aplikacji logiki — wyrażenie Float z wartością](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Kliknij przycisk **Dodaj** > **Dodaj wiersz** w polu warunek, aby dodać dodatkową część warunku.
9.  W **warunek** kliknij pole tekstowe, zawierające **wybierz wartość**.
10. Kliknij przycisk **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`
11. Wybierz **zawartości dynamicznej**, umieść kursor wewnątrz (nawias) i wybierz **NotificationThresholdAmount** z listy, aby wypełnić Dokończ wyrażenie.
12. Wybierz **OK** można ustawić wyrażenia.
13. Wybierz **jest mniejsza niż** w polu listy rozwijanej **warunek**.
14. W **wybierz wartość** warunku wprowadź `1`.

    ![Azure — aplikacji logiki — wyrażenie Float z wartością](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. W **w przypadku opcji true** wybierz opcję **Dodaj akcję**. Dodasz akcję POST protokołu HTTP, która spowoduje wyłączenie opcjonalne maszyn wirtualnych.

    ![Azure — aplikacji logiki — Dodaj akcję](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Wprowadź **HTTP** do wyszukiwania dla akcji HTTP i wybierania **HTTP — HTTP** akcji.

    ![Akcja HTTP Dodaj - aplikacji logiki — Azure](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Wybierz **wpis** jako dla **metoda** wartość.
18. Wprowadź adres URL elementu webhook o nazwie **opcjonalnie** utworzonego wcześniej w tym samouczku jako **Uri** wartość.

    ![Azure — aplikacji logiki — Akcja HTTP identyfikatora URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Wybierz **Dodaj akcję** w **w przypadku opcji true** pole. Doda akcji w wiadomości e-mail, która będzie wysyłać wiadomość e-mail z powiadomieniem odbiorcy, który opcjonalnie maszyn wirtualnych została zamknięta.
20. Wyszukaj frazę "Wyślij wiadomość e-mail" i wybierz *Wyślij wiadomość e-mail* akcji na podstawie usługi poczty e-mail, możesz użyć.

    ![Azure — aplikacji logiki — Akcja Wyślij w wiadomość e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**. W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail. Usługa Logic Apps utworzy połączenie z kontem e-mail.

    Musisz zezwolić na aplikację logiki, aby uzyskać dostęp do informacji w wiadomości e-mail.

    ![Azure — aplikacji logiki — dostęp do powiadomienia](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Dodaj **do**, **podmiotu**, i **treści** tekst wiadomości e-mail, która powiadamia odbiorcy, który opcjonalnie maszyn wirtualnych została zamknięta. Użyj **BudgetName** i **NotificationThresholdAmount** zawartości dynamicznej, aby wypełnić pola temat i treść.

    ![Azure — aplikacji logiki — szczegóły wiadomości E-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Dodaj drugą akcję warunkowe

Użyj instrukcji warunkowej, aby sprawdzić, czy wartości progowej ma osiągnięto lub Przekroczono 100% wartości budżetu. Jeśli osiągnięty wartości progowej, Wyślij metodę POST protokołu HTTP przy użyciu elementu webhook o nazwie **Complete**. Ta akcja spowoduje wyłączenie wszystkich pozostałych maszyn wirtualnych.

1.  Wybierz **nowy krok** > **Dodaj warunek**.

    ![Azure — aplikacji logiki — Dodaj akcję](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  W **warunek** kliknij pole tekstowe, zawierające **wybierz wartość** Aby wyświetlić listę dostępnych wartości.
3.  Kliknij przycisk **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń: `float()`
4.  Wybierz **zawartości dynamicznej**, umieść kursor wewnątrz (nawias) i wybierz **NotificationThresholdAmount** z listy, aby wypełnić Dokończ wyrażenie.

    Wyrażenie będzie następujący:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wybierz **OK** można ustawić wyrażenia.
6.  Wybierz **jest większa niż lub równa** w polu listy rozwijanej **warunek**.
7.  W **wybierz pole wartości** warunku wprowadź `1`.

    ![Wartość warunku Set - aplikacji logiki — Azure](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  W **w przypadku opcji true** wybierz opcję **Dodaj akcję**. Dodasz akcję POST protokołu HTTP, która spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

    ![Azure — aplikacji logiki — Dodaj akcję](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Wprowadź **HTTP** do wyszukiwania dla akcji HTTP i wybierania **HTTP — HTTP** akcji.
10. Wybierz **wpis** jako dla **metoda** wartość.
11. Wprowadź adres URL elementu webhook o nazwie **Complete** utworzonego wcześniej w tym samouczku jako **Uri** wartość.

    ![Azure — aplikacji logiki — Dodaj akcję](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Wybierz **Dodaj akcję** w **w przypadku opcji true** pole. Doda akcji w wiadomości e-mail, która będzie wysyłać wiadomość e-mail z powiadomieniem adresata, która została zamknięta pozostałych maszyn wirtualnych.
13. Wyszukaj frazę "Wyślij wiadomość e-mail" i wybierz *Wyślij wiadomość e-mail* akcji na podstawie usługi poczty e-mail, możesz użyć.
14. Dodaj **do**, **podmiotu**, i **treści** tekst wiadomości e-mail, która powiadamia odbiorcy, który opcjonalnie maszyn wirtualnych została zamknięta. Użyj **BudgetName** i **NotificationThresholdAmount** zawartości dynamicznej, aby wypełnić pola temat i treść.

    ![Szczegóły wiadomości e-mail z platformy Azure — aplikacji logiki — Wyślij](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Kliknij przycisk **Zapisz** w górnej części **Projektant aplikacji logiki** bloku.

### <a name="logic-app-summary"></a>Podsumowanie aplikacji logiki

Oto, jak Twoja aplikacja logiki wygląda po wykonaniu tych czynności. W najbardziej podstawowym scenariuszy, w których nie potrzebujesz żadnych aranżacji oparte na wartościach progowych, można bezpośrednio wywołać skrypt automatyzacji z **Monitor** i pominąć **aplikacji logiki** kroku.

   ![Azure — aplikacji logiki — pełny przegląd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Po zapisaniu aplikacji logiki, można wywołać adres URL został wygenerowany. Użyjesz tego adresu URL w następnej sekcji tego samouczka.

## <a name="create-an-azure-monitor-action-group"></a>Utwórz grupę usługi Azure Monitor

Grupy akcji to zbiór preferencje powiadamiania, zdefiniowanych przez użytkownika. Po wyzwoleniu alertu grupy określonej akcji mogą otrzymać alert przy powiadomienia. Alert usługi Azure aktywnie zgłasza powiadomienia na podstawie określonych warunków i zapewnia możliwość podejmowania działań. Alert może wykorzystywać dane z wielu źródeł, w tym metryk i dzienników.

Grupy akcji są tylko punkt końcowy, który będą integrować się z Twojego budżetu. Można skonfigurować powiadomienia w wielu kanałów, ale w tym scenariuszu, który koncentruje się na aplikację logiki utworzonej we wcześniejszej części tego samouczka.

### <a name="create-an-action-group-in-azure-monitor"></a>Utwórz grupę akcji w usłudze Azure Monitor

Podczas tworzenia grupy akcji będzie wskazywać na aplikację logiki, który został utworzony we wcześniejszej części tego samouczka.

1.  Jeśli nie jesteś już zalogowany [witryny Azure portal](https://portal.azure.com/), logowanie i wybieranie **wszystkich usług** > **Monitor**.
2.  Wybierz **grup akcji** z **ustawienie** sekcji.
3.  Wybierz **Dodawanie grupy akcji** z **grup akcji** bloku.
4.  Dodaj i sprawdź następujące elementy:
    - Nazwa grupy akcji
    - Krótka nazwa
    - Subskrypcja
    - Grupa zasobów

    ![Azure — aplikacji logiki — Dodawanie grupy akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  W ramach **Dodaj grupę akcji** okienko, Dodaj akcję programu LogicApp. Nazwa akcji **BudgetLA budżetu**. W **aplikacji logiki** okienku wybierz **subskrypcji** i **grupy zasobów**. Następnie wybierz **aplikacji logiki** utworzonego wcześniej w tym samouczku.
6.  Kliknij przycisk **OK** można ustawić aplikacji logiki. Następnie wybierz **OK** w **Dodaj grupę akcji** okienko, aby utworzyć grupę akcji.

To wszystko ze wszystkimi składnikami obsługi wymagane do skutecznego organizowania Twojego budżetu. Teraz wszystko, co należy zrobić to tworzenie budżetu i skonfigurować go do używania utworzonej grupy akcji.

## <a name="create-the-azure-budget"></a>Tworzenie budżetu na platformie Azure

Można utworzyć budżetu w usłudze Azure portal przy użyciu [funkcji budżetu](../cost-management/tutorial-acm-create-budgets.md) w Cost Management. Alternatywnie można utworzyć przy użyciu interfejsów API REST, poleceń cmdlet programu Powershell, budżetu lub użyć interfejsu wiersza polecenia. W poniższej procedurze użyto interfejsu API REST. Przed wywołaniem interfejsu API REST, konieczne będzie token autoryzacji. Aby utworzyć token autoryzacji, można użyć [ARMClient](https://github.com/projectkudu/ARMClient) projektu. **ARMClient** pozwala na uwierzytelnianie siebie do usługi Azure Resource Manager i uzyskiwanie tokenu służącego do wywoływania interfejsów API.

### <a name="create-an-authentication-token"></a>Tworzenie tokenu uwierzytelniania

1.  Przejdź do [ARMClient](https://github.com/projectkudu/ARMClient) projektu w usłudze GitHub.
2.  Sklonuj repozytorium w celu uzyskania kopii lokalnej.
3.  Otwórz projekt w programie Visual Studio i skompiluj je.
4.  Gdy kompilacja zakończy się pomyślnie, plik wykonywalny powinien znajdować się w *\bin\debug* folderu.
5.  Uruchom ARMClient. Otwórz wiersz polecenia i przejdź do *\bin\debug* foldery w katalogu głównym projektu.
6.  Aby zalogować się i uwierzytelnianie, wprowadź następujące polecenie w wierszu polecenia:<br>
    `ARMClient login prod`
7.  Kopiuj **identyfikator guid subskrypcji** z danych wyjściowych.
8.  Aby skopiować token autoryzacji do Schowka, wprowadź następujące polecenie w wierszu polecenia, ale się, że identyfikator subskrypcji skopiowany w kroku powyżej: <br>
    `ARMClient token <subscription GUID from previous step>`

    Po zakończeniu kroku zobaczysz następujące czynności:<br>
    **Token została pomyślnie skopiowana do Schowka.**
9.  Zapisz token, który ma być używany dla czynności opisane w następnej sekcji tego samouczka.

### <a name="create-the-budget"></a>Tworzenie budżetu

Następnie należy skonfigurować **Postman** do utworzenia budżetu przez wywołanie interfejsów API REST użycia platformy Azure. Postman to środowisko programistyczne interfejsu API. Pliki środowiska i kolekcji zostaną zaimportowane do narzędzia Postman. Kolekcja zawiera definicje pogrupowanych żądań HTTP przetwarzanych wywoływać interfejsy API REST użycia platformy Azure. Plik środowisko zawiera zmienne, które są używane przez kolekcję.

1.  Pobierz i Otwórz [klienta Postman REST](https://www.getpostman.com/) do wykonania interfejsów API REST.
2.  W narzędziu Postman Utwórz nowe żądanie.

    ![Postman — Utwórz nowe żądanie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Nowe żądanie w kolekcji, zapisać nowe żądanie nie ma nic w nim.

    ![Zapisywanie narzędzia postman — nowe żądanie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Żądanie od zmiany `Get` do `Put` akcji.
5.  Zmodyfikuj następujący adres URL, zastępując `{subscriptionId}` z **identyfikator subskrypcji** użytej w poprzedniej sekcji tego samouczka. Zmodyfikuj także adres URL, aby uwzględnić "SampleBudget" jako wartość `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Wybierz **nagłówki** karcie w ramach narzędzia Postman.
7.  Dodaj nową **klucz** o nazwie "Autoryzacja".
8.  Ustaw **wartość** do tokenu, który został utworzony przy użyciu ArmClient na końcu ostatniej sekcji.
9.  Wybierz **treści** karcie w ramach narzędzia Postman.
10. Wybierz **pierwotne** przycisku opcji.
11. W polu tekstowym, Wklej poniżej przykładowa definicja budżetu, jednak należy zastąpić **subscriptionid**, **budgetname**, i **actiongroupname** parametrów za pomocą usługi Identyfikator subskrypcji, unikatową nazwę Twojego budżetu i nazwa grupy akcji utworzone w treści żądania i adres URL:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Naciśnij klawisz **wysyłania** Aby wysłać żądanie.

Masz teraz wszystkie elementy, które są potrzebne do wywoływania [budżetu interfejsu API](https://docs.microsoft.com/rest/api/consumption/budgets). Dokumentacja interfejsu API budżetów ma dodatkowe szczegóły na określone żądania, w tym następujące:
    - **budgetName** -kilka budżetów są obsługiwane.  Budżet nazwy muszą być unikatowe.
    - **Kategoria** -musi być albo **koszt** lub **użycia**. Interfejs API obsługuje budżetów kosztów i użycia.
    - **ziarna czasu** — miesięczna, co kwartał lub budżetu. Kwota resetuje na końcu okresu.
    - **filtry** — filtry umożliwiają zawęzić budżetu do określonego zestawu zasobów w ramach wybranego zakresu. Na przykład filtr może być zbiór grup zasobów dla budżetu poziomu subskrypcji.
    - **powiadomienia** — określa szczegóły powiadomień i progów. Można skonfigurować wiele progów i podaj adres e-mail lub grupę akcji, aby otrzymać powiadomienie.

## <a name="summary"></a>Podsumowanie

W ramach tego samouczka wiesz:
- Jak utworzyć element Runbook usługi Azure Automation do zatrzymywania maszyn wirtualnych.
- Tworzenie aplikacji logiki platformy Azure, która jest wyzwalana oparte na wartości progowe budżetu i wywoływania elementu runbook powiązanych z parametrami prawo.
- Jak utworzyć grupę akcji monitora platformy Azure, który będzie został skonfigurowany do wyzwolenia aplikacji logiki platformy Azure, po osiągnięciu progu budżetu.
- Jak utworzyć budżetu na platformie Azure za pomocą odpowiednie progi i połączenie go do grupy akcji.

Jest teraz dostępna w pełni funkcjonalnego budżetu dla Twojej subskrypcji, która zostanie wyłączone swoje maszyny wirtualne po przejściu do Ciebie warunkami skonfigurowanego budżetu.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat rozliczeń scenariuszami na platformie Azure, zobacz [Pomoc dotycząca rozliczeń i scenariuszach automatyzacji zarządzania kosztami](billing-cost-management-automation-scenarios.md).
