---
title: Konfigurowanie ustawień automatycznego zamykania dla maszyny wirtualnej w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia automatycznego zamykania maszyny wirtualnej (VM), aby maszyna wirtualna była automatycznie zamykana, gdy nie jest używana.
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
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361572"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Konfigurowanie ustawień automatycznego zamykania dla maszyny wirtualnej w Azure DevTest Labs
Azure DevTest Labs pozwala kontrolować koszt i zminimalizować liczbę odpadów w laboratoriach przez Zarządzanie zasadami (ustawieniami) dla każdego laboratorium. W tym artykule opisano sposób konfigurowania zasad Autozamykania dla konta laboratorium i konfigurowania ustawień automatycznego zamykania dla laboratorium na koncie laboratorium. Aby wyświetlić sposób ustawiania każdej zasady laboratorium, zobacz [Definiowanie zasad laboratorium w Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Zasady Autozamykania dla laboratorium
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w laboratorium. Dzięki temu można zaoszczędzić koszty z uruchomionych maszyn, które nie są używane (bezczynny). Zasady zamykania można wymusić na wszystkich maszynach wirtualnych laboratorium centralnie, ale także zaoszczędzić użytkownikom laboratorium nakłady pracy z konfigurowania harmonogramu dla poszczególnych maszyn. Ta funkcja umożliwia ustawienie zasad w harmonogramie laboratorium, rozpoczynając od oferty bez kontroli w celu uzyskania pełnej kontroli dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować te zasady, wykonując następujące czynności:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz pozycję **zasady automatycznego zamykania** w sekcji **harmonogramy** menu po lewej stronie.
3. Wybierz jedną z opcji. Poniższe sekcje zawierają więcej informacji na temat tych opcji: Zasady zestawu mają zastosowanie tylko do nowych maszyn wirtualnych utworzonych w laboratorium, a nie do istniejących maszyn wirtualnych. 

    ![Automatycznie Zamknij opcje zasad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Konfigurowanie ustawień automatycznego zamykania dla laboratorium
Zasady Autozamykania ułatwiają minimalizowanie odpadów laboratoryjnych przez umożliwienie określenia czasu, w którym maszyny wirtualne tego laboratorium zostały zamknięte.

Aby wyświetlić (i zmienić) zasady dla laboratorium, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.   
4. Wybierz pozycję **Konfiguracja i zasady**.

    ![Okienko ustawień zasad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **automatyczne zamykanie** w obszarze harmonogramy .
   
    ![Automatyczne zamykanie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Wybierz **opcję** Włącz, aby włączyć tę zasadę i **wyłączyć** ją.
7. Jeśli włączysz tę zasadę, określ czas (i strefę czasową), aby zamknąć wszystkie maszyny wirtualne w bieżącym laboratorium.
8. Wybierz opcję **tak** lub **nie** , aby można było wysłać powiadomienie 15 minut przed określonym czasem automatycznego zamknięcia. Jeśli wybierzesz opcję **tak**, wprowadź punkt końcowy URL elementu webhook lub adres e-mail określający, gdzie ma być ogłaszane lub wysyłane. Użytkownik otrzymuje powiadomienie i otrzymuje opcję opóźnienia zamknięcia. Aby uzyskać więcej informacji, zobacz sekcję [powiadomienia](#notifications) . 
9. Wybierz pozycję **Zapisz**.

    Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć to ustawienie z określonej maszyny wirtualnej, Otwórz okienko zarządzania maszyną wirtualną i zmień jego ustawienie automatycznego **zamykania** .

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Konfigurowanie ustawień automatycznego zamykania maszyny wirtualnej

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustawia harmonogram i może zrezygnować
Jeśli ta opcja zasad jest ustawiona dla laboratorium, użytkownicy mogą przesłonić lub zrezygnować z harmonogramu laboratorium. Ta opcja przyznaje użytkownikom laboratorium pełną kontrolę nad harmonogramem automatycznego zamykania maszyn wirtualnych. Użytkownicy laboratorium nie zmieniają żadnej zmiany na stronie harmonogramu automatycznego zamykania maszyny wirtualnej.

![Opcja automatycznie Zamknij zasady-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie może zrezygnować
Jeśli ta opcja zasad jest ustawiona dla laboratorium, użytkownicy mogą przesłonić harmonogram laboratorium. Nie mogą jednak zrezygnować z zasad automatycznego zamykania. Ta opcja zapewnia, że każdy komputer w laboratorium jest objęty harmonogramem automatycznego zamykania. Użytkownicy laboratorium mogą zaktualizować harmonogram automatycznego zamykania dla swoich maszyn wirtualnych i skonfigurować powiadomienia o zamknięciu.

![Opcja automatycznie Zamknij zasady-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramem ustawionym przez administratora laboratorium
Jeśli ta opcja zasad jest ustawiona dla laboratorium, użytkownicy nie mogą przesłonić ani zrezygnować z harmonogramu laboratorium. Ta opcja zapewnia administratorowi laboratorium pełną kontrolę nad harmonogramem dla każdej maszyny w laboratorium. Użytkownicy laboratorium mogą konfigurować tylko powiadomienia o automatycznym zamknięciu dla swoich maszyn wirtualnych.

![Opcja automatycznie Zamknij zasady-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Powiadomienia
Po skonfigurowaniu automatycznego wyłączania przez właściciela laboratorium powiadomienia będą wysyłane do użytkowników laboratorium 15 minut przed wyzwoleniem automatycznego wyłączenia, jeśli będzie to miało wpływ na dowolną z ich maszyn wirtualnych. Ta opcja daje użytkownikom laboratorium szansę na zapisanie pracy przed zamknięciem. Powiadomienie zawiera również linki dla każdej maszyny wirtualnej dla następujących akcji:

- Pomiń automatyczne zamykanie w tym czasie
- Odłożyć Automatyczne zamknięcie przez godzinę lub 2 godziny, aby mogły pracować nad maszyną wirtualną.

Powiadomienie jest wysyłane za pośrednictwem skonfigurowanego punktu końcowego elementu webhook lub adresu e-mail określonego przez właścicieli laboratorium w ustawieniach automatycznego zamykania. Elementy webhook umożliwiają kompilowanie lub Konfigurowanie integracji subskrybowanych przez pewne zdarzenia. Gdy jedno z tych zdarzeń zostanie wyzwolone, DevTest Labs wyśle ładunek HTTP POST do skonfigurowanego adresu URL elementu webhook. Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API platformy Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Zalecamy używanie elementów webhook, ponieważ są one szeroko obsługiwane przez różne aplikacje (na przykład zapasy czasu, Azure Logic Apps itd.) i umożliwiają implementowanie własnych metod wysyłania powiadomień. Przykład otrzymywania powiadomień o automatycznym zamknięciu z wiadomości e-mail przy użyciu Azure Logic Apps można znaleźć w temacie[Tworzenie aplikacji logiki, która odbiera powiadomienia e-mail](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Kolejne kroki
Zobacz [Zarządzanie zasadami automatycznego zamykania dla laboratorium w Azure DevTest Labs](devtest-lab-auto-shutdown.md)
