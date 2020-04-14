---
title: Zarządzanie zasadami automatycznego zasłaniania w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak ustawić zasady autoshutdown dla laboratorium, tak aby maszyny wirtualne były automatycznie zamykane, gdy nie są używane.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270914"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurowanie automatycznego usuwania maszyn wirtualnych w laboratorium i obliczaniu w laboratoriach devtest azure

W tym artykule wyjaśniono, jak skonfigurować ustawienia autoshutdown dla maszyn wirtualnych w laboratorium w DevTest Labs i maszyn wirtualnych obliczeniowych. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurowanie automatycznego usuwania maszyn wirtualnych w laboratorium (DevTest Labs)
Laboratorium devtest platformy Azure umożliwia kontrolowanie kosztów i minimalizowanie odpadów w laboratoriach, zarządzając zasadami (ustawieniami) dla każdego laboratorium. W tym artykule pokazano, jak skonfigurować zasady autoshutdown dla konta laboratorium i skonfigurować ustawienia autoshutdown dla laboratorium na koncie laboratorium. Aby zobaczyć, jak ustawić wszystkie zasady laboratoryjne, zobacz [Definiowanie zasad laboratoryjnych w laboratoriach Azure DevTest Labs.](devtest-lab-set-lab-policy.md)  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Ustawianie zasad automatycznego zamykania dla laboratorium
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w laboratorium. W ten sposób można zaoszczędzić koszty z uruchomionych maszyn, które nie są używane (bezczynny). Zasady zamykania można wymusić na wszystkich maszynach wirtualnych w laboratorium centralnie, ale także zapisać użytkownikom laboratorium wysiłek z konfigurowania harmonogramu dla ich poszczególnych komputerów. Ta funkcja umożliwia ustawienie zasad w harmonogramie laboratorium, począwszy od oferowania żadnej kontroli do pełnej kontroli, dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować tę zasadę, wykonując następujące kroki:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz **pozycję Zasady automatycznego zamykania** w sekcji **Harmonogramy** w menu po lewej stronie.
3. Wybierz jedną z opcji. W poniższych sekcjach podano więcej szczegółów na temat tych opcji: Zasady zestawu dotyczą tylko nowych maszyn wirtualnych utworzonych w laboratorium, a nie już istniejących maszyn wirtualnych. 

    ![Automatyczne zamykanie opcji zasad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurowanie ustawień automatycznego zamykania
Zasady autoshutdown pomaga zminimalizować straty w laboratorium, umożliwiając określenie czasu, przez który maszyny wirtualne tego laboratorium są zamykane.

Aby wyświetlić (i zmienić) zasady dla laboratorium, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.   
4. Wybierz **opcję Konfiguracja i zasady**.

    ![Okienko ustawień zasad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Automatyczne zamykanie w** obszarze **Harmonogramy**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. **Wybierz włącz,** aby włączyć tę zasadę, i **Wyłącz,** aby ją wyłączyć.
7. Jeśli ta zasada zostanie włączona, określ czas (i strefę czasową) do zamknięcia wszystkich maszyn wirtualnych w bieżącym laboratorium.
8. Określ **tak** lub **nie** dla opcji wysyłania powiadomień 30 minut przed określonym czasem automatycznego zasnąć. Jeśli wybierzesz **opcję Tak,** wpisz punkt końcowy adresu URL elementu webhook lub adres e-mail, określając miejsce, w którym ma zostać opublikowane lub wysłane powiadomienie. Użytkownik otrzymuje powiadomienie i ma możliwość opóźnienia zamknięcia systemu. Aby uzyskać więcej informacji, zobacz sekcję [Powiadomienia.](#notifications) 
9. Wybierz **pozycję Zapisz**.

    Domyślnie po włączeniu ta zasada ma zastosowanie do wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć to ustawienie z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego ustawienie **Autoshutdown.**
    
> [!NOTE]
> Jeśli zaktualizujesz harmonogram automatycznego wyłączania dla laboratorium lub określonej maszyny wirtualnej laboratorium w ciągu 30 minut od bieżącego zaplanowanego czasu, zaktualizowany czas zamykania będzie miał zastosowanie do harmonogramu następnego dnia. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustala harmonogram i może zrezygnować
Jeśli ustawisz laboratorium do tej zasady, użytkownicy laboratorium można zastąpić lub zrezygnować z harmonogramu laboratorium. Ta opcja zapewnia użytkownikom laboratorium pełną kontrolę nad harmonogramem automatycznego zamykania ich maszyn wirtualnych. Użytkownicy laboratorium nie widzą żadnych zmian na stronie harmonogramu automatycznego zamykania maszyn wirtualnych.

![Opcja automatycznego wyłączania - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie może zrezygnować
Jeśli ustawisz laboratorium do tej zasady, użytkownicy laboratorium można zastąpić harmonogram laboratorium. Nie mogą jednak zrezygnować z zasad automatycznego zamykania. Ta opcja zapewnia, że każdy komputer w laboratorium jest w harmonogramie automatycznego zamykania. Użytkownicy laboratorium mogą aktualizować harmonogram automatycznego zamykania swoich maszyn wirtualnych i konfigurowania powiadomień o zamykaniu.

![Opcja automatycznego wyłączania - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramem ustawionym przez administratora laboratorium
Jeśli ustawisz laboratorium na tę zasadę, użytkownicy laboratorium nie mogą zastąpić ani zrezygnować z harmonogramu laboratorium. Ta opcja oferuje administratorowi laboratorium pełną kontrolę zgodnie z harmonogramem dla każdego komputera w laboratorium. Użytkownicy laboratorium mogą konfigurowania tylko powiadomień o automatycznym zamykaniu dla swoich maszyn wirtualnych.

![Opcja automatycznego wyłączania - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Powiadomienia
Po autoshutdown skonfigurowane przez właściciela laboratorium, powiadomienia zostaną wysłane do użytkowników laboratorium 30 minut przed autoshutdown wyzwalane, jeśli którykolwiek z ich maszyn wirtualnych będzie dotyczyć. Ta opcja daje użytkownikom laboratorium możliwość zapisania swojej pracy przed zamknięciem. Powiadomienie zawiera również łącza dla każdej maszyny Wirtualnej dla następujących akcji:

- Pomiń autoshutdown na ten czas
- Odłożyć autoshutdown na godzinę lub 2 godziny, tak aby mogli kontynuować pracę na maszynie Wirtualnej.

Powiadomienie jest wysyłane za pośrednictwem skonfigurowanego punktu końcowego haka sieci web lub adresu e-mail określonego przez właścicieli laboratorium w ustawieniach autoshutdown. Webhooks umożliwiają tworzenie lub konfigurowanie integracji, które subskrybują określone zdarzenia. Po wyzwoleniu jednego z tych zdarzeń DevTest Labs wyśle ładunek HTTP POST do skonfigurowanego adresu URL elementu webhook. Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub funkcji platformy Azure interfejsu API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Firma Microsoft zaleca, aby używać haków sieci web, ponieważ są one szeroko obsługiwane przez różne aplikacje (na przykład Slack, Azure Logic Apps i tak dalej.) i umożliwia implementację własnego sposobu wysyłania powiadomień. Na przykład w tym artykule otrzymasz informacje o automatycznym wystyniu z wiadomości e-mail przy użyciu aplikacji Azure Logic Apps. Najpierw szybko przejdźmy przez podstawowe kroki, aby włączyć automatyczne powiadomienie o automatycznym wskrzeszaniu w laboratorium.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Tworzenie aplikacji logiki, która odbiera powiadomienia e-mail
[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) udostępnia wiele gotowych łączników, które ułatwiają integrację usługi z innymi klientami, takimi jak Office 365 i twitter. Na wysokim poziomie kroki konfigurowania aplikacji logiki dla powiadomień e-mail można podzielić na cztery fazy: 

- Tworzenie aplikacji logiki. 
- Skonfiguruj wbudowany szablon.
- Integracja z klientem poczty e-mail
- Pobierz adres URL elementu webhook.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
Aby rozpocząć, utwórz aplikację logiki w subskrypcji platformy Azure, wykonując następujące kroki:

1. Wybierz **+ Utwórz zasób** w menu po lewej stronie, wybierz **pozycję Integracja**i wybierz pozycję Aplikacja **logiki**. 

    ![Nowe menu aplikacji logiki](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na aplikacji **logiki — tworzenie** strony, wykonaj następujące kroki: 
    1. Wprowadź **nazwę** aplikacji logiki.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Utwórz nową **grupę zasobów** lub wybierz istniejącą grupę zasobów. 
    4. Wybierz **lokalizację** aplikacji logiki. 

        ![Nowa aplikacja logiki - ustawienia](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. W **powiadomieniach**wybierz **pozycję Przejdź do zasobu** w powiadomieniu. 

    ![Przechodzenie do zasobu](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Wybierz **pozycję Projektant aplikacji logiki** w kategorii Narzędzia **wdrażania.**

    ![Wybierz żądanie/odpowiedź HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na stronie **Żądanie-Odpowiedź HTTP** wybierz pozycję **Użyj tego szablonu**. 

    ![Wybierz opcję Użyj tego szablonu](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Skopiuj następujący JSON do sekcji **Schemat JSON treści żądania:** 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Schemat JSON treści żądania](./media/devtest-lab-auto-shutdown/request-json.png)
7. Wybierz **+ Nowy krok** w projektancie i wykonaj następujące kroki:
    1. Wyszukiwanie **w usłudze Office 365 Outlook — wysyłanie wiadomości e-mail**. 
    2. Wybierz **pozycję Wyślij wiadomość e-mail** z **akcji**. 
    
        ![Opcja Wyślij wiadomość e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Wybierz **pozycję Zaloguj się,** aby zalogować się na swoje konto e-mail. 
    4. Wybierz pole **DO** i wybierz właściciela.
    5. Wybierz **TEMAT**i wprowadź temat powiadomienia e-mail. Na przykład: "Zamknięcie maszyny vmName dla laboratorium: labName."
    6. Wybierz **pozycję BODY**i zdefiniuj zawartość treści dla powiadomień e-mail. Na przykład: "vmName jest zaplanowane do zamknięcia w ciągu 15 minut. Pomiń to zamknięcie, klikając: adres URL. Opóźnij zamknięcie na godzinę: delayUrl60. Opóźnij wyłączenie o 2 godziny: opóźnienieUrl120."

        ![Schemat JSON treści żądania](./media/devtest-lab-auto-shutdown/email-options.png)
1. Wybierz pozycję **Zapisz** na pasku narzędzi. Teraz możesz skopiować **adres URL HTTP POST**. Wybierz przycisk kopiowania, aby skopiować adres URL do schowka. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurowanie automatycznego usuwania maszyn wirtualnych

1. Na stronie **Maszyna wirtualna** wybierz pozycję **Automatyczne zamykanie** w menu po lewej stronie w sekcji **Operacje.** 
2. Na stronie **Automatyczne zamykanie** wybierz pozycję **Włącz,** aby włączyć tę zasadę, i **wyłącz** ją, aby ją wyłączyć.
3. Jeśli ta zasada zostanie włączona, określ **czas** (i **strefę czasową),** w którym maszyna wirtualna powinna zostać zamknięta.
4. Określ **tak** lub **nie** dla opcji wysyłania powiadomień 30 minut przed określonym czasem automatycznego zasnąć. Jeśli wybierzesz **opcję Tak,** wpisz punkt końcowy adresu URL elementu webhook lub adres e-mail, określając miejsce, w którym ma zostać opublikowane lub wysłane powiadomienie. Użytkownik otrzymuje powiadomienie i ma możliwość opóźnienia zamknięcia systemu. Aby uzyskać więcej informacji, zobacz sekcję [Powiadomienia.](#notifications) 
9. Wybierz **pozycję Zapisz**.

    ![Konfigurowanie automatycznego usuwania dla maszyny Wirtualnej obliczeniowej](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Wyświetlanie dzienników aktywności dla aktualizacji automatycznego zamykania
Po zaktualizowaniu ustawienia autoshutdown, zobaczysz działania rejestrowane w dzienniku aktywności dla maszyny Wirtualnej. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do strony głównej maszyny Wirtualnej.
2. Z lewego menu wybierz **pozycję Dziennik aktywności.** 
3. Usuń **zasób: mycomputevm** z filtrów.
3. Upewnij się, że w dzienniku działań jest widoczna operacja **Dodaj lub modyfikuj harmonogramy.** Jeśli go nie widzisz, poczekaj na jakiś czas i odśwież dziennik aktywności.

    ![Wpis dziennika aktywności](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Wybierz operację **Dodaj lub modyfikuj harmonogramy,** aby wyświetlić następujące informacje na stronie **Podsumowanie:**

    - Nazwa operacji (Dodawanie lub modyfikowanie harmonogramów)
    - Data i godzina, o której zostało zaktualizowane ustawienie autoshutdown.
    - Adres e-mail użytkownika, który zaktualizował to ustawienie. 

        ![Podsumowanie wpisu dziennika aktywności](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Przełącz się na kartę **Historia zmian** na stronie Dodawanie **lub modyfikowanie harmonogramów,** zobaczysz historię zmian dla tego ustawienia. W poniższym przykładzie czas zamknięcia został zmieniony z 19:00 na 18:00 w dniu 10 kwietnia 2020 r. o 15:18:47 EST. A ustawienie zostało wyłączone o 15:25:09 EST. 

    ![Dziennik aktywności — historia zmian](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Aby wyświetlić więcej szczegółów dotyczących operacji, przełącz się na kartę **JSON** na stronie **Dodawanie lub modyfikowanie harmonogramów.**

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak ustawić wszystkie zasady, zobacz [Definiowanie zasad laboratoryjnych w laboratoriach Azure DevTest Labs](devtest-lab-set-lab-policy.md).

