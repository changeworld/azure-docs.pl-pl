---
title: Zarządzanie zasadami autoshutdown w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Informacje o sposobie ustawiania zasad autoshutdown laboratorium, tak aby maszyny wirtualne są automatycznie zamykane, gdy nie są używane.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873993"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzanie zasadami autoshutdown laboratorium Azure DevTest Labs
Usługa Azure DevTest Labs umożliwia decydują o koszcie i zminimalizować straty w laboratorium, Zarządzanie zasadami (ustawienia) w każdym środowisku laboratoryjnym. W tym artykule przedstawiono sposób konfigurowania zasad autoshutdown dla konta laboratorium i skonfiguruj ustawienia autoshutdown dla laboratorium w ramach konta laboratorium. Aby wyświetlić sposób ustawiania każdych zasad laboratorium, zobacz [Definiowanie zasad laboratorium Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Ustawienie automatycznego zamykania zasad dla laboratorium
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w środowisku laboratoryjnym. Dzięki temu można zmniejszyć koszty z uruchomiono (Bezczynność) maszyn, które nie są używane. Można wymusić zasady zamykanie na wszystkich maszyn wirtualnych laboratorium centralnie, ale także zapisać użytkownicy laboratorium nakład pracy od skonfigurowania harmonogramu dla poszczególnych maszyn. Ta funkcja pozwala ustawić zasady w dogodnym laboratorium od oferty nie kontroli Pełna kontrola dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować te zasady, wykonując następujące czynności:

1. Na stronie głównej dla swojego laboratorium wybierz **konfiguracji i zasad**.
2. Wybierz **automatyczne zamknięcie zasad** w **harmonogramy** części menu po lewej stronie.
3. Wybierz jedną z opcji. Poniższe sekcje zapewniają więcej szczegółów na temat tych opcji: Ustawianie zasad ma zastosowanie tylko do nowych maszyn wirtualnych utworzonych w laboratorium, a nie już istniejących maszyn wirtualnych. 

    ![Automatycznie Zamknij opcje zasad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Konfigurowanie automatycznego zamykania ustawień
Zasady autoshutdown ułatwia minimalizowanie strat laboratorium, umożliwiając umożliwia określenie czasu zamykania maszyn wirtualnych w tym laboratorium.

Aby wyświetlić i zmienić zasad dla laboratorium, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.   
4. Wybierz **konfiguracji i zasad**.

    ![Okienko ustawień zasad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. W laboratorium **konfiguracji i zasad** okienku wybierz **automatycznego zamykania** w obszarze **harmonogramy**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.
7. Włączenie tych zasad, określ czas (i strefy czasowej) do zamykania wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym.
8. Określ **tak** lub **nie** dla opcji wysłać powiadomienie z 15 minut przed chwilą autoshutdown określony. Jeśli wybierzesz **tak**wprowadź punkt końcowy adres URL elementu webhook lub określenie, gdzie chcesz, aby powiadomienia mają być opublikowane lub wysyłane adres e-mail. Użytkownik odbiera powiadomienie i znajduje się opcja opóźnienie zamknięcia systemu. Aby uzyskać więcej informacji, zobacz [powiadomienia](#notifications) sekcji. 
9. Wybierz pozycję **Zapisz**.

    Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym. Aby usunąć ustawienie z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **Autoshutdown** ustawienie.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustawia harmonogram i zrezygnować z niego
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium można zastąpić, lub zrezygnować z harmonogramu laboratorium. Tej opcji użytkownicy laboratorium przyznaje pełną kontrolę nad harmonogramem zamykania automatycznie ich maszyn wirtualnych. Użytkownicy laboratorium Zobacz nie zmieni się ich maszyn wirtualnych automatycznego zamykania harmonogram strony.

![Automatycznie Zamknij opcja zasad — 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie można zrezygnować
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium mogą przesłaniać harmonogram laboratorium. Jednak nie mogą zrezygnować z automatycznego zamykania zasad. Ta opcja zapewnia się, że do każdej maszyny w środowisku laboratoryjnym w ramach automatycznego zamykania harmonogramu. Użytkownicy laboratorium można zaktualizować automatycznego zamykania harmonogramu ich maszyn wirtualnych i Konfigurowanie powiadomień zamykanie systemu.

![Automatycznie Zamknij opcja zasad - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramu ustalonego przez administratora laboratorium
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium nie można zastąpić, lub zrezygnować z harmonogramu laboratorium. Ta opcja oferuje administratora laboratorium pełną kontrolę na harmonogram dla każdej maszyny w środowisku laboratoryjnym. Użytkownicy laboratorium można skonfigurować tylko automatyczne zamknięcie powiadomienia dla swoich maszyn wirtualnych.

![Automatycznie Zamknij opcja zasad — 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Powiadomienia
Po autoshutdown przez właściciela laboratorium, powiadomienia będą wysyłane do użytkowników lab 15 minut przed autoshutdown wyzwolony, jeśli będzie mieć wpływ na wszystkich swoich maszyn wirtualnych. Ta opcja umożliwia użytkownikom laboratorium szansę, aby zapisać swoją pracę, przed zamknięciem. Powiadomienie zawiera także łącza dla każdej maszyny Wirtualnej dla następujących czynności:

- Pomiń autoshutdown za ten czas
- Odłóż autoshutdown na godzinę lub 2 godziny, dzięki czemu można kontynuować pracę na maszynie Wirtualnej.

Powiadomienie jest wysyłane za pośrednictwem punktu końcowego punktu zaczepienia skonfigurowany w sieci web lub adres e-mail określony przez właścicieli laboratorium w ustawieniach autoshutdown. Elementy Webhook pozwalają na tworzenie lub Konfigurowanie integracji, które subskrybują wystąpienia określonych zdarzeń. Po wyzwoleniu jedno z tych zdarzeń, usługa DevTest Labs wyśle ładunku żądania HTTP POST do skonfigurowanego adresu URL elementu webhook. Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API usługi Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Firma Microsoft zaleca Użyj haków sieci web, ponieważ często są obsługiwane przez różne aplikacje (na przykład, Slack, Azure Logic Apps i itp.) i pozwala na implementowanie własny sposób wysyłania powiadomień. Na przykład ten artykule przedstawiono sposób uzyskiwania powiadomień autoshutdown z wiadomości e-mail przy użyciu usługi Azure Logic Apps. Po pierwsze możemy szybko przejść przez podstawowe czynności, aby umożliwić autoshutdown powiadomień w środowisku laboratoryjnym.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Tworzenie aplikacji logiki, która odbiera powiadomienia e-mail
[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) udostępnia wiele łączników poza pole, które można łatwo zintegrować usługę z innych klientów, takich jak usługi Office 365 i twitter. Na wysokim poziomie kroki, aby skonfigurować aplikację logiki dla powiadomień pocztą e-mail można podzielić na cztery fazy: 

- Tworzenie aplikacji logiki. 
- Skonfiguruj wbudowany szablon.
- Integracja z klienta poczty e-mail
- Pobierz adres URL elementu Webhook.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki
Aby rozpocząć pracę, należy utworzyć aplikację logiki w ramach subskrypcji platformy Azure za pomocą poniższej procedury:

1. Wybierz **+ Utwórz zasób** w menu po lewej stronie wybierz **integracji**i wybierz **aplikacji logiki**. 

    ![Nowe menu aplikacji logiki](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na **aplikacji logiki — tworzenie** strony, wykonaj następujące kroki: 
    1. Wprowadź **nazwa** dla aplikacji logiki.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Utwórz nową **grupy zasobów** lub wybierz istniejącą grupę zasobów. 
    4. Wybierz **lokalizacji** dla aplikacji logiki. 

        ![Nowej aplikacji logiki — ustawienia](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. W **powiadomienia**, wybierz opcję **przejdź do zasobu** powiadomienie. 

    ![Przejdź do zasobu](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Wybierz **Projektant aplikacji logiki** w obszarze **narzędzia wdrażania** kategorii.

    ![Zaznacz żądania/odpowiedzi HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na **odpowiedź na żądanie HTTP** wybierz opcję **za pomocą tego szablonu**. 

    ![Wybierz opcję Użyj tej opcji, szablon](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Skopiuj następujące dane JSON do **schemat JSON treści żądania** sekcji: 

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
7. Wybierz **+ nowy krok** w Projektancie i wykonaj następujące czynności:
    1. Wyszukaj **Office 365 Outlook — Wyślij wiadomość e-mail**. 
    2. Wybierz **Wyślij wiadomość e-mail** z **akcje**. 
    
        ![Wyślij wiadomość e-mail — opcja](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Wybierz **Zaloguj** do logowania się do swojego konta e-mail. 
    4. Wybierz **na** pola, a następnie wybierz właściciela.
    5. Wybierz **podmiotu**i wprowadź temat powiadomienia w wiadomości e-mail. Na przykład: "Zamknięcia vmName maszyny w środowisku laboratoryjnym: labName."
    6. Wybierz **treści**i zdefiniuj treść powiadomienia e-mail. Na przykład: "vmName zaplanowano zamknięcie za 15 minut. Pominąć to zamknięcie, klikając pozycję: ADRES URL. Opóźnienie zamykania na godzinę: delayUrl60. Opóźnienie zamykania przez 2 godziny: delayUrl120. "

        ![Schemat JSON treści żądania](./media/devtest-lab-auto-shutdown/email-options.png)
1. Wybierz pozycję **Zapisz** na pasku narzędzi. Teraz możesz skopiować **HTTP POST URL**. Wybierz przycisk kopiowania, aby skopiować adres URL do Schowka. 

    ![Adres URL elementu WebHook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak ustawić wszystkie zasady, zobacz [Definiowanie zasad laboratorium Azure DevTest Labs](devtest-lab-set-lab-policy.md).
