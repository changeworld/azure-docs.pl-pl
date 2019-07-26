---
title: Scenariusz dotyczący rozliczeń i kosztów zarządzania kosztami platformy Azure | Microsoft Docs
description: Dowiedz się, jak zamknąć maszyny wirtualne na podstawie określonych progów budżetu przy użyciu usługi Azure Automation.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443476"
---
# <a name="manage-costs-with-azure-budgets"></a>Zarządzanie kosztami przy użyciu budżetów platformy Azure

Kontrola kosztów jest istotnym składnikiem do maksymalizowania wartości inwestycji w chmurę. Istnieje kilka scenariuszy, w których widoczność kosztów, raportowanie i aranżacja oparta na kosztach ma kluczowe znaczenie dla kontynuowania operacji. [Interfejsy api Azure Cost Management](https://docs.microsoft.com/rest/api/consumption/) udostępniają zestaw interfejsów API do obsługi każdego z tych scenariuszy. Interfejsy API zapewniają szczegółowe informacje dotyczące użycia, co pozwala na wyświetlanie szczegółowych kosztów na poziomie wystąpienia.

Budżety są często używane jako część kontroli kosztów. Budżety mogą być objęte zakresem na platformie Azure. Na przykład możesz zawęzić widok budżetu na podstawie subskrypcji, grup zasobów lub kolekcji zasobów. Oprócz korzystania z interfejsu API budżetów do powiadamiania użytkownika za pośrednictwem poczty e-mail, gdy osiągnięty jest próg budżetu, można użyć [grup akcji Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) , aby wyzwolić zorganizowany zestaw akcji w wyniku zdarzenia budżetu.

Typowy scenariusz budżetów dla klienta działającego niekrytycznego obciążenia może wystąpić, gdy chcą zarządzać budżetem, a także uzyskać dostęp do przewidywalnego kosztu podczas przeglądania faktury miesięcznej. Ten scenariusz wymaga pewnego kosztu aranżacji zasobów, które są częścią środowiska platformy Azure. W tym scenariuszu jest ustawiony miesięczny budżet $1000 dla subskrypcji. Ponadto Progi powiadomień są ustawiane tak, aby wyzwalać kilka aranżacji. Ten scenariusz rozpoczyna się od wartości progowej kosztów 80%, co spowoduje zatrzymanie wszystkich maszyn wirtualnych w **grupie zasobów**. Następnie w progu kosztu 100% wszystkie wystąpienia maszyn wirtualnych zostaną zatrzymane.
Aby skonfigurować ten scenariusz, wykonaj następujące czynności, wykonując czynności opisane w poszczególnych sekcjach tego samouczka.

Te działania zawarte w tym samouczku umożliwiają:

- Utwórz Azure Automation element Runbook, aby zatrzymać maszyny wirtualne za pomocą elementów webhook.
- Utwórz aplikację logiki platformy Azure, która ma być wyzwalana na podstawie wartości progu budżetu i Wywołaj element Runbook z właściwymi parametrami.
- Utwórz Azure Monitor grupę akcji, która zostanie skonfigurowana do wyzwalania aplikacji logiki platformy Azure, gdy zostanie osiągnięty próg budżetu.
- Utwórz budżet platformy Azure z żądanymi progami i połącz go z grupą akcji.

## <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu Runbook Azure Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) to usługa, która umożliwia tworzenie skryptów większości zadań związanych z zarządzaniem zasobami i wykonywanie tych zadań zgodnie z harmonogramem lub na żądanie. W ramach tego scenariusza utworzysz [Azure Automation element Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) , który będzie używany do zatrzymywania maszyn wirtualnych. W celu skompilowania tego scenariusza będziesz używać graficznego elementu Runbook [maszyn wirtualnych platformy Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z [galerii](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) . Importując ten element Runbook do konta platformy Azure i publikując go, będzie można zatrzymać maszyny wirtualne po osiągnięciu progu budżetu.

### <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
2. Kliknij przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu systemu Azure.
3. Wybierz kolejno pozycje **Narzędzia** > do zarządzania**Automatyzacja**.
   > [!NOTE]
   > Jeśli nie masz konta platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
4. Wprowadź informacje o koncie. W obszarze **Utwórz konto Uruchom jako platformy Azure**wybierz pozycję **tak** , aby automatycznie włączyć ustawienia, które są konieczne do uproszczenia uwierzytelniania na platformie Azure.
5. Po zakończeniu kliknij przycisk **Utwórz**, aby rozpocząć wdrażanie konta usługi Automation.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Zaimportuj element Runbook Zatrzymaj maszynę wirtualną platformy Azure v2

Korzystając z [Azure Automation elementu Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types), zaimportuj graficzny element Runbook [maszyn wirtualnych platformy Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) z galerii.

1.  Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń konta Azure.
2.  Otwórz konto usługi Automation, wybierając pozycję **wszystkie konta usługi** > **Automation**. Następnie wybierz swoje konto usługi Automation.
3.  Kliknij pozycję **Galeria elementów Runbook** w sekcji **Automatyzacja procesu** .
4.  Ustaw **Źródło galerii** na **Centrum skryptów** , a następnie wybierz **przycisk OK**.
5.  Znajdź i zaznacz element Galeria [Zatrzymaj maszyny wirtualne platformy Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) w ramach Azure Portal.
6.  Kliknij przycisk **Importuj** , aby wyświetlić blok **Importuj** , a następnie wybierz pozycję **OK**. Zostanie wyświetlony blok przegląd elementów Runbook.
7.  Gdy element Runbook ukończy proces importowania, wybierz opcję **Edytuj** , aby wyświetlić graficzny Edytor elementów Runbook i opcję publikowania.

    ![Azure — edytowanie graficznego elementu Runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Kliknij przycisk **Publikuj** , aby opublikować element Runbook, a następnie wybierz opcję **tak** po wyświetleniu monitu. Opublikowanie elementu Runbook powoduje zastąpienie istniejącej wersji opublikowanej wersją roboczą. W tym przypadku nie masz opublikowanej wersji, ponieważ element Runbook został utworzony.

    Aby uzyskać więcej informacji o publikowaniu elementu Runbook, zobacz [Tworzenie graficznego elementu Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Tworzenie elementów webhook dla elementu Runbook

Przy użyciu graficznego elementu Runbook programu [Zatrzymaj maszynę wirtualną platformy Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) utworzysz dwa elementy webhook, aby uruchomić element runbook w Azure Automation za pośrednictwem pojedynczego żądania HTTP. Pierwszy element webhook wywoła element Runbook w 80% próg budżetu z nazwą grupy zasobów jako parametrem, umożliwiając zatrzymanie opcjonalnych maszyn wirtualnych. Następnie drugi element webhook wywoła element Runbook bez parametrów (o 100%), który zatrzyma wszystkie pozostałe wystąpienia maszyn wirtualnych.

1. Na stronie **elementy Runbook** w [Azure Portal](https://portal.azure.com/)kliknij element Runbook **StopAzureV2Vm** , który wyświetla blok przegląd elementu Runbook.
2. Kliknij pozycję **element webhook** w górnej części strony, aby otworzyć blok **Dodawanie elementu webhook** .
3. Kliknij pozycję **Utwórz nowy element webhook** , aby otworzyć blok **Utwórz nowy element webhook** .
4. Ustaw **nazwę** elementu webhook na opcjonalną . Właściwość **Enabled** musi mieć **wartość yes**. Nie  trzeba zmieniać wartości wygaśnie. Aby uzyskać więcej informacji na temat właściwości elementu webhook, zobacz [szczegóły](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook)elementu webhook.
5. Obok wartości adresu URL kliknij ikonę kopiowania, aby skopiować adres URL elementu webhook.
   > [!IMPORTANT]
   > Zapisz adres URL elementu webhook o nazwie **Optional** w bezpiecznym miejscu. Będziesz używać adresu URL w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook nie można ponownie wyświetlić ani pobrać adresu URL.
6. Kliknij przycisk **OK** , aby utworzyć nowy element webhook.
7. Kliknij przycisk **Konfiguruj parametry i Uruchom ustawienia** , aby wyświetlić wartości parametrów dla elementu Runbook.
   > [!NOTE]
   > Jeśli element Runbook ma obowiązkowe parametry, nie można utworzyć elementu webhook, chyba że zostaną podane wartości.
8. Kliknij przycisk **OK** , aby zaakceptować wartości parametrów elementu webhook.
9. Kliknij przycisk **Utwórz** , aby utworzyć element webhook.
10. Następnie postępuj zgodnie z powyższymi krokami, aby utworzyć drugi element webhook o nazwie **Complete**.
    > [!IMPORTANT]
    > Pamiętaj, aby zapisać adresy URL elementu webhook do użycia w dalszej części tego samouczka. Ze względów bezpieczeństwa po utworzeniu elementu webhook nie można ponownie wyświetlić ani pobrać adresu URL.

Teraz należy mieć dwie skonfigurowane elementy webhook, które są dostępne przy użyciu zapisanych adresów URL.

![Elementy webhook — opcjonalne i kompletne](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Teraz możesz wykonać Azure Automation konfigurację. Możesz przetestować elementy webhook z prostym testem post, aby sprawdzić, czy element webhook działa. Następnie musisz utworzyć aplikację logiki dla aranżacji.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Tworzenie aplikacji logiki platformy Azure dla aranżacji

Logic Apps pomóc w tworzeniu, planowaniu i automatyzowaniu procesów jako przepływów pracy, dzięki czemu można zintegrować aplikacje, dane, systemy i usługi między przedsiębiorstwami lub organizacjami. W tym scenariuszu utworzona [aplikacja logiki](https://docs.microsoft.com/azure/logic-apps/) wykona nieco więcej niż tylko wywołanie utworzonego elementu webhook usługi Automation.

Można skonfigurować budżety, aby wyzwolić powiadomienie po spełnieniu określonego progu. Można podać wiele progów, które mają być powiadamiane, a aplikacja logiki będzie demonstrować możliwość wykonywania różnych akcji na podstawie progu spełnione. W tym przykładzie zostanie skonfigurowany scenariusz, w którym uzyskasz kilka powiadomień, pierwsze powiadomienie jest dostępne po osiągnięciu 80% budżetu, a drugie powiadomienie, gdy 100% budżetu został osiągnięty. Aplikacja logiki zostanie użyta do zamknięcia wszystkich maszyn wirtualnych w grupie zasobów. Po pierwsze próg **opcjonalny** zostanie osiągnięty o 80%, a następnie drugi próg zostanie osiągnięty, gdy wszystkie maszyny wirtualne w subskrypcji zostaną zamknięte.

Aplikacje logiki umożliwiają dostarczenie przykładowego schematu dla wyzwalacza HTTP, ale wymaga ustawienia nagłówka **Content-Type** . Ponieważ Grupa akcji nie ma niestandardowych nagłówków dla elementu webhook, należy przeanalizować ładunek w osobnym kroku. Zostanie użyta akcja **Analizuj** i przekazanie jej przy użyciu przykładowego ładunku.

### <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Aplikacja logiki wykona kilka akcji. Poniższa lista zawiera zestaw akcji, które zostaną wykonane przez aplikację logiki:
- Rozpoznaje po odebraniu żądania HTTP
- Przeanalizuj dane w formacie JSON w celu określenia wartości progowej, która została osiągnięta
- Użyj instrukcji warunkowej, aby sprawdzić, czy kwota progu osiągnęła 80% lub więcej zakresu budżetu, ale nie więcej niż 100%.
    - Jeśli ta kwota progowa została osiągnięta, Wyślij wpis HTTP przy użyciu elementu webhook o nazwie **Optional**. Ta akcja spowoduje zamknięcie maszyn wirtualnych w grupie "opcjonalna".
- Użyj instrukcji warunkowej, aby sprawdzić, czy kwota progu osiągnęła lub przekroczyła 100% wartości budżetu.
    - Jeśli osiągnięto wartość progową, Wyślij wpis HTTP przy użyciu elementu webhook o nazwie **Complete**. Ta akcja spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

Następujące kroki są niezbędne do utworzenia aplikacji logiki, która będzie wykonywać powyższe czynności:

1.  W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.

    ![Azure — Wybieranie zasobu aplikacji logiki](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  W bloku **Tworzenie aplikacji logiki** Podaj szczegóły potrzebne do utworzenia aplikacji logiki, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

    ![Azure — Tworzenie aplikacji logiki](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Po wdrożeniu aplikacji logiki przez platformę Azure zostanie otwarty **projektant Logic Apps** i zostanie wyświetlony blok z wprowadzeniem wideo i najczęściej używanymi wyzwalaczami.

### <a name="add-a-trigger"></a>Dodaj wyzwalacz

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy. akcje to wszystkie kroki wykonywane po aktywowaniu wyzwalacza.

1.  W obszarze **Szablony** bloku **Logic Apps Designer** wybierz pozycję **pusta aplikacja logiki**.
2.  Dodaj [wyzwalacz](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) , wprowadzając "żądanie HTTP" w polu wyszukiwania **Logic Apps projektanta** , aby znaleźć i wybrać wyzwalacz o nazwie **Request — po odebraniu żądania HTTP**.

    ![Azure-Logic App-wyzwalacz http](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Wybierz pozycję **nowy krok** > **Dodaj akcję**.

    ![Azure — nowy krok — Dodawanie akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Wyszukaj ciąg "Analizuj kod JSON" w polu wyszukiwania programu **Logic Apps Designer** , aby znaleźć i wybrać [akcję](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **JSON analizowanie danych** .

    ![Azure-Logic App — Dodaj akcję JSON analizy](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Wprowadź "ładunek" jako nazwę **zawartości** dla ładunku JSON analizy lub Użyj tagu "Body" z zawartości dynamicznej.
6.  Wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu** w polu **Analiza JSON** .

    ![Azure-Logic App — Użyj przykładowych danych JSON w celu wygenerowania schematu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Wklej następujący przykładowy ładunek JSON do pola tekstowego:`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Pole tekstowe będzie wyglądać następująco:

    ![Azure-Logic App — przykładowy ładunek JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Kliknij przycisk **Gotowe**.

### <a name="add-the-first-conditional-action"></a>Dodaj pierwszą akcję warunkową

Użyj instrukcji warunkowej, aby sprawdzić, czy kwota progu osiągnęła 80% lub więcej zakresu budżetu, ale nie więcej niż 100%. Jeśli ta kwota progowa została osiągnięta, Wyślij wpis HTTP przy użyciu elementu webhook o nazwie **Optional**. Ta akcja spowoduje wyłączenie maszyn wirtualnych w grupie **opcjonalnej** .

1.  Wybierz pozycję **nowy krok** > **Dodaj warunek**.

    ![Aplikacja logiki Azure — Dodawanie warunku](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  W **warunku** kliknij pole tekstowe zawierające **Wybierz wartość** , aby wyświetlić listę dostępnych wartości.

    ![Azure — aplikacja logiki — pole warunku](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Kliknij pozycję **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń:`float()`

    ![Azure — wyrażenie aplikacji logiki — zmiennoprzecinkowe](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Wybierz pozycję **zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić wyrażenie kompletne.

    Wyrażenie będzie następujące:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wybierz **przycisk OK** , aby ustawić wyrażenie.
6.  Opcja Select **jest większa lub równa wartości** w polu listy rozwijanej **warunek**.
7.  W polu **Wybierz wartość** wprowadź `.8`warunek.

    ![Azure-Logic App-float Expression z wartością](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Kliknij przycisk **Dodaj** > **Dodaj wiersz** w polu warunek, aby dodać dodatkową część warunku.
9.  W **warunku** kliknij pole tekstowe zawierające **Wybierz wartość**.
10. Kliknij pozycję **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń:`float()`
11. Wybierz pozycję **zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić wyrażenie kompletne.
12. Wybierz **przycisk OK** , aby ustawić wyrażenie.
13. Opcja Wybierz **jest mniejsza niż** w polu listy rozwijanej **warunek**.
14. W polu **Wybierz wartość** wprowadź `1`warunek.

    ![Azure-Logic App-float Expression z wartością](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. W polu **Jeśli prawda** wybierz pozycję **Dodaj akcję**. Zostanie dodana akcja POST protokołu HTTP, która spowoduje wyłączenie opcjonalnych maszyn wirtualnych.

    ![Azure — aplikacja logiki — Dodawanie akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Wprowadź wartość **http** , aby wyszukać akcję http i wybrać akcję **http – http** .

    ![Azure-Logic App — Dodawanie akcji HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Wybierz pozycję **post** jako wartość **metody** .
18. Wprowadź adres URL elementu webhook o nazwie **Optional** , który został utworzony wcześniej w tym samouczku jako wartość **identyfikatora URI** .

    ![Azure-Logic App — identyfikator URI akcji HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Wybierz pozycję **Dodaj akcję** w polu **Jeśli prawda** . Dodasz akcję poczty e-mail, która spowoduje wysłanie wiadomości e-mail z powiadomieniem odbiorcy o wyłączeniu opcjonalnych maszyn wirtualnych.
20. Wyszukaj ciąg "Wyślij wiadomość e-mail" i wybierz akcję *Wyślij wiadomość e-mail* na podstawie używanej usługi poczty e-mail.

    ![Azure-Logic App — akcja wysyłania wiadomości e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**. W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail. Usługa Logic Apps utworzy połączenie z kontem e-mail.

    Musisz zezwolić aplikacji logiki na dostęp do informacji e-mail.

    ![Azure-Logic App-informacje o dostępie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Dodaj **do**, **temat**i tekst **treści** wiadomości e-mail, która powiadamia odbiorcę o wyłączeniu opcjonalnych maszyn wirtualnych. Użyj wartości **budżetname** i zawartość dynamiczna **NotificationThresholdAmount** , aby wypełnić pola temat i treść.

    ![Azure-Logic App — Szczegóły wiadomości E-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Dodaj drugą akcję warunkową

Użyj instrukcji warunkowej, aby sprawdzić, czy kwota progu osiągnęła lub przekroczyła 100% wartości budżetu. Jeśli osiągnięto wartość progową, Wyślij wpis HTTP przy użyciu elementu webhook o nazwie **Complete**. Ta akcja spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

1.  Wybierz pozycję **nowy krok** > **Dodaj warunek**.

    ![Azure — aplikacja logiki — Dodawanie akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  W **warunku** kliknij pole tekstowe zawierające **Wybierz wartość** , aby wyświetlić listę dostępnych wartości.
3.  Kliknij pozycję **wyrażenie** w górnej części listy i wprowadź następujące wyrażenie w edytorze wyrażeń:`float()`
4.  Wybierz pozycję **zawartość dynamiczna**, umieść kursor wewnątrz nawiasu () i wybierz pozycję **NotificationThresholdAmount** z listy, aby wypełnić wyrażenie kompletne.

    Wyrażenie będzie następujące:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Wybierz **przycisk OK** , aby ustawić wyrażenie.
6.  Opcja Select **jest większa lub równa wartości** w polu listy rozwijanej **warunek**.
7.  W **polu Wybierz wartość** wprowadź `1`warunek.

    ![Azure-Logic App-Ustawianie wartości warunku](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  W polu **Jeśli prawda** wybierz pozycję **Dodaj akcję**. Zostanie dodana akcja POST protokołu HTTP, która spowoduje zamknięcie wszystkich pozostałych maszyn wirtualnych.

    ![Azure — aplikacja logiki — Dodawanie akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Wprowadź wartość **http** , aby wyszukać akcję http i wybrać akcję **http – http** .
10. Wybierz pozycję **post** jako wartość **metody** .
11. Wprowadź adres URL elementu webhook o nazwie **Complete** utworzonego wcześniej w tym samouczku jako wartość **identyfikatora URI** .

    ![Azure — aplikacja logiki — Dodawanie akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Wybierz pozycję **Dodaj akcję** w polu **Jeśli prawda** . Dodasz akcję poczty e-mail, która spowoduje wysłanie wiadomości e-mail z powiadomieniem odbiorcy o wyłączeniu pozostałych maszyn wirtualnych.
13. Wyszukaj ciąg "Wyślij wiadomość e-mail" i wybierz akcję *Wyślij wiadomość e-mail* na podstawie używanej usługi poczty e-mail.
14. Dodaj **do**, **temat**i tekst **treści** wiadomości e-mail, która powiadamia odbiorcę o wyłączeniu opcjonalnych maszyn wirtualnych. Użyj wartości **budżetname** i zawartość dynamiczna **NotificationThresholdAmount** , aby wypełnić pola temat i treść.

    ![Azure-Logic App — Szczegóły wysyłania wiadomości e-mail](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Kliknij pozycję **Zapisz** w górnej części bloku **projektanta aplikacji logiki** .

### <a name="logic-app-summary"></a>Podsumowanie aplikacji logiki

Oto, jak Twoja aplikacja logiki będzie wyglądać po zakończeniu. W najbardziej typowych scenariuszach, w których nie jest potrzebna jakakolwiek aranżacja oparta na progach, można bezpośrednio wywołać skrypt automatyzacji od **monitora** i pominąć krok **aplikacji logiki** .

   ![Azure — aplikacja logiki — widok kompletny](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Po zapisaniu aplikacji logiki został wygenerowany adres URL, który będzie można wywołać. Ten adres URL zostanie użyty w następnej sekcji tego samouczka.

## <a name="create-an-azure-monitor-action-group"></a>Tworzenie grupy akcji Azure Monitor

Grupa akcji to kolekcja preferencji powiadomień zdefiniowanych przez użytkownika. Po wyzwoleniu alertu określona grupa akcji może odebrać alert przez powiadomienie. Alert platformy Azure aktywnie zgłasza powiadomienie na podstawie określonych warunków i zapewnia możliwość podjęcia działania. Alert może używać danych z wielu źródeł, w tym metryk i dzienników.

Grupy akcji to jedyny punkt końcowy, który zostanie zintegrowany z budżetem. Możesz skonfigurować powiadomienia w wielu kanałach, ale w tym scenariuszu zobaczysz aplikację logiki utworzoną wcześniej w tym samouczku.

### <a name="create-an-action-group-in-azure-monitor"></a>Tworzenie grupy akcji w Azure Monitor

Po utworzeniu grupy akcji należy wskazać aplikację logiki utworzoną wcześniej w tym samouczku.

1.  Jeśli jeszcze nie zalogowano się do [Azure Portal](https://portal.azure.com/), zaloguj się i wybierz pozycję **wszystkie usługi** > **monitor**.
2.  Wybierz pozycję **grupy akcji** z sekcji **ustawienie** .
3.  Wybierz pozycję **Dodaj grupę akcji** z bloku **grupy akcji** .
4.  Dodaj i sprawdź następujące elementy:
    - Nazwa grupy akcji
    - Krótka nazwa
    - Subscription
    - Resource group

    ![Azure — aplikacja logiki — Dodaj grupę akcji](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  W okienku **Dodaj grupę akcji** Dodaj akcję LogicApp. Nazwij akcję **budżet-BudgetLA**. W okienku **aplikacja logiki** wybierz subskrypcję i  **grupę zasobów**. Następnie wybierz **aplikację logiki** utworzoną wcześniej w tym samouczku.
6.  Kliknij przycisk **OK** , aby ustawić aplikację logiki. Następnie wybierz przycisk **OK** w okienku **Dodaj grupę akcji** , aby utworzyć grupę akcji.

Wszystkie składniki pomocnicze potrzebne do efektywnego organizowania budżetu są gotowe. Teraz wystarczy utworzyć budżet i skonfigurować go tak, aby korzystał z utworzonej grupy akcji.

## <a name="create-the-azure-budget"></a>Tworzenie budżetu platformy Azure

Budżet można utworzyć w Azure Portal przy użyciu [funkcji budżet](../cost-management/tutorial-acm-create-budgets.md) w programie Cost Management. Można też utworzyć budżet przy użyciu interfejsów API REST, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Poniższa procedura używa interfejsu API REST. Przed wywołaniem interfejsu API REST wymagany jest Token autoryzacji. Aby utworzyć token autoryzacji, można użyć projektu [ARMClient](https://github.com/projectkudu/ARMClient) . **ARMClient** umożliwia samodzielną identyfikację w Azure Resource Manager i uzyskanie tokenu wywołującego interfejsy API.

### <a name="create-an-authentication-token"></a>Tworzenie tokenu uwierzytelniania

1.  Przejdź do projektu [ARMClient](https://github.com/projectkudu/ARMClient) w witrynie GitHub.
2.  Sklonuj repozytorium, aby uzyskać kopię lokalną.
3.  Otwórz projekt w programie Visual Studio i skompiluj go.
4.  Gdy kompilacja zakończy się pomyślnie, plik wykonywalny powinien znajdować się w folderze *\bin\debug* .
5.  Uruchom ARMClient. Otwórz wiersz polecenia i przejdź do folderu *\bin\debug* w katalogu głównym projektu.
6.  Aby zalogować się i uwierzytelnić, wprowadź następujące polecenie w wierszu polecenia:<br>
    `ARMClient login prod`
7.  Skopiuj **Identyfikator GUID subskrypcji** z danych wyjściowych.
8.  Aby skopiować Token autoryzacji do schowka, wprowadź następujące polecenie w wierszu polecenia, ale pamiętaj, aby użyć skopiowanego identyfikatora subskrypcji z powyższego kroku: <br>
    `ARMClient token <subscription GUID from previous step>`

    Po ukończeniu kroku powyżej zobaczysz następujące elementy:<br>
    **Pomyślnie skopiowano token do Schowka.**
9.  Zapisz token, który ma być używany na potrzeby kroków w następnej sekcji tego samouczka.

### <a name="create-the-budget"></a>Utwórz budżet

Następnie skonfigurujesz program **pocztowy** do tworzenia budżetu, wywołując interfejsy API REST użycia platformy Azure. Poster to środowisko deweloperskie interfejsu API. Pliki środowiska i kolekcji zostaną zaimportowane do programu Poster. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują interfejsy API REST użycia platformy Azure. Plik środowiska zawiera zmienne, które są używane przez kolekcję.

1.  Pobierz i Otwórz [klienta REST](https://www.getpostman.com/) programu Poster w celu wykonania interfejsów API REST.
2.  W programie Poster Utwórz nowe żądanie.

    ![Poster — Utwórz nowe żądanie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Zapisz nowe żądanie jako kolekcję, tak aby nowe żądanie nie miało nic na nim.

    ![Poster — Zapisz nowe żądanie](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Zmień żądanie z `Get` `Put` na akcję.
5.  Zmodyfikuj następujący adres URL, zastępując `{subscriptionId}` go **identyfikatorem subskrypcji** użytym w poprzedniej sekcji tego samouczka. Ponadto zmodyfikuj adres URL, tak aby zawierał wartość "SampleBudget", dla `{budgetName}`:`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Wybierz kartę **nagłówki** w programie Poster.
7.  Dodaj nowy **klucz** o nazwie "Autoryzacja".
8.  Ustaw **wartość** na token, który został utworzony przy użyciu ArmClient na końcu ostatniej sekcji.
9.  Wybierz kartę **treść** w programie Poster.
10. Wybierz opcję **pierwotnego** przycisku.
11. W polu tekstowym wklej do poniższego przykładowej definicji budżetu, należy jednak zastąpić parametry **subskrypcji**, **budżetuname**i **actiongroupname** identyfikatorem subskrypcji, unikatową nazwą budżetu i nazwą grupy akcji utworzono zarówno w adresie URL, jak i w treści żądania:

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
12. Naciśnij przycisk **Wyślij** , aby wysłać żądanie.

Masz teraz wszystkie elementy potrzebne do wywołania [interfejsu API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets). Dokumentacja interfejsu API budżetów zawiera dodatkowe szczegóły dotyczące konkretnych żądań, w tym następujące:
    - **budżetname** — obsługiwane są wiele budżetów.  Nazwy budżetów muszą być unikatowe.
    - **Kategoria** — musi być **kosztem** lub **zużyciem**. Interfejs API obsługuje zarówno budżety kosztów, jak i użycia.
    - **timeGrain** — miesięczny, kwartalny lub roczny budżet. Kwota jest resetowana na koniec okresu.
    - **filtry** — Filtry umożliwiają zawężenie budżetu do określonego zestawu zasobów w ramach wybranego zakresu. Na przykład filtr może być zbiorem grup zasobów dla budżetu poziomu subskrypcji.
    - **powiadomienia** — określa szczegóły i progi powiadomień. Można skonfigurować wiele progów i podać adres e-mail lub grupę akcji, aby otrzymać powiadomienie.

## <a name="summary"></a>Podsumowanie

Postępując zgodnie z tym samouczkiem, przedstawiono następujące informacje:
- Jak utworzyć element Runbook Azure Automation, aby zatrzymać maszyny wirtualne.
- Jak utworzyć aplikację logiki platformy Azure, która jest wyzwalana na podstawie wartości progowych budżetu i wywoływać powiązane elementy Runbook z właściwymi parametrami.
- Jak utworzyć Azure Monitor grupę akcji, która zostanie skonfigurowana do wyzwalania aplikacji logiki platformy Azure, gdy zostanie spełniony próg budżetu.
- Jak utworzyć budżet platformy Azure z żądanymi progami i połączyć go z grupą akcji.

Teraz masz w pełni funkcjonalny budżet dla subskrypcji, która spowoduje wyłączenie maszyn wirtualnych po osiągnięciu skonfigurowanych progów budżetu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat scenariuszy rozliczania na platformie Azure, zobacz scenariusze dotyczące rozliczeń [i zarządzania kosztami](billing-cost-management-automation-scenarios.md).
