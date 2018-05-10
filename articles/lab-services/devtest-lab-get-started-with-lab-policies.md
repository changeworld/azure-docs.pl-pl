---
title: Zarządzanie zasadami podstawowego laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić niektórych podstawowych zasad (ustawienia) dla laboratorium w usłudze DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40b8fb360be7b08540e25886aaebe7f911607b6d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzanie zasadami podstawowe dla laboratorium w usłudze Azure DevTest Labs

Azure DevTest Labs umożliwia kontrolowanie kosztów i zminimalizować odpady w Twojej labs przez Zarządzanie zasadami (ustawienia) dla każdego laboratorium. W tym artykule Rozpoczynanie pracy z zasadami dowiedzieć, jak ustawić dwa najważniejszych zasad — ograniczenie liczby maszyn wirtualnych (VM), które zostały utworzone lub żądane przez pojedynczego użytkownika i konfigurując automatyczne zamykanie. Aby wyświetlić sposób ustawiania każdych zasad laboratorium, zobacz [Definiowanie zasad laboratorium w usłudze Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Uzyskiwanie dostępu do zasad laboratorium w usłudze Azure DevTest Labs
Poniższe kroki informacje pomocne przy konfigurowaniu zasad dla laboratorium w usłudze Azure DevTest Labs:

Aby wyświetlić i zmienić zasady dla laboratorium, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.

1. Z listy labs wybierz żądany laboratorium.   

1. Wybierz **konfiguracji i zasadach**.

    ![Okienko ustawień zasad](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **Konfiguracji i zasadach** okienko zawiera menu Ustawienia, które można określić. W tym artykule przedstawiono tylko ustawienia **maszyn wirtualnych dla użytkownika**, **automatyczne zamykanie**, i **Auto-start**. Aby dowiedzieć się więcej o pozostałych ustawień, zobacz [zarządzanie wszystkimi zasadami dla laboratorium w usłudze Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Zestaw maszyn wirtualnych dla użytkownika
Zasady dla **maszyn wirtualnych dla użytkownika** można określić maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przez użytkownika. Jeśli użytkownik próbuje utworzyć lub oświadczeń maszyny Wirtualnej, gdy limit użytkowników zostały spełnione, komunikat o błędzie wskazuje, że maszyna wirtualna nie może być utworzony przejęte. 

1. W laboratorium **konfiguracji i zasadach** menu, wybierz opcję **maszyn wirtualnych dla użytkownika**.
   
    ![Maszyny wirtualne na użytkownika](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wybierz **tak** Aby ograniczyć liczbę maszyn wirtualnych dla poszczególnych użytkowników. Jeśli nie chcesz ograniczyć liczbę maszyn wirtualnych dla poszczególnych użytkowników, wybierz **nr**. W przypadku wybrania **tak**, wprowadź wartość liczbową wskazującą maksymalną liczbę maszyn wirtualnych, które można utworzyć ani przejęte przez użytkownika. 

1. Wybierz **tak** Aby ograniczyć liczbę maszyn wirtualnych, które mogą używać dysków SSD (dysk SSD). Jeśli nie chcesz ograniczyć liczbę maszyn wirtualnych, które można używać dysków SSD, wybierz **nr**. W przypadku wybrania **tak**, wprowadź wartość wskazującą maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przy użyciu dysków SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-auto-shutdown"></a>Ustaw automatyczne zamykanie
Zasady automatycznego zamykania pozwala zminimalizować odpady laboratorium, umożliwiając umożliwia określenie czasu zamykania maszyn wirtualnych w tym laboratorium.

1. W laboratorium **konfiguracji i zasadach** okienku wybierz **automatyczne zamykanie**.
   
    ![Automatyczne zamykanie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wybierz **na** Aby włączyć tę zasadę, a **poza** je wyłączyć.

1. Po włączeniu tych zasad, określ czas (i strefy czasowej) do zamykania wszystkich maszyn wirtualnych w bieżącym laboratorium.

1. Określ **tak** lub **nr** opcji wysyłania powiadomień 15 minut przed upływem czasu określonego automatyczne zamykanie. Jeśli wybierzesz **tak**, wprowadź końcowy adres URL elementu webhook lub określanie miejscu powiadomienie, aby można opublikować lub wysyłane adres e-mail. Użytkownik otrzymuje powiadomienie i jest możliwość opóźnienia zamknięcia systemu.

   Aby uzyskać więcej informacji na temat elementów webhook, zobacz [tworzenia elementu webhook lub funkcja interfejsu API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wybierz pozycję **Zapisz**.

Domyślnie po włączeniu tych zasad dotyczy wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć tego ustawienia z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **automatyczne zamykanie** ustawienie.

## <a name="set-auto-start"></a>Ustaw automatyczne uruchamianie
Zasady automatycznego uruchamiania pozwala określić, kiedy mają być uruchamiane maszyny wirtualne w bieżącym laboratorium.  

1. W laboratorium **konfiguracji i zasadach** okienku wybierz **Auto-start**.
   
    ![Automatyczne uruchamianie](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wybierz **na** Aby włączyć tę zasadę, a **poza** je wyłączyć.

3. Po włączeniu tych zasad, określ z zaplanowanym czasem rozpoczęcia, strefa czasowa i dni tygodnia, którego dotyczy czas. 

4. Wybierz pozycję **Zapisz**.

Po włączeniu tych zasad nie została automatycznie zastosowana do żadnej maszyny wirtualnej w bieżącym laboratorium. Aby zastosować to ustawienie do istniejącej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **Auto-start** ustawienie.

## <a name="next-steps"></a>Kolejne kroki

- [Definiowanie zasad laboratorium w usłudze Azure DevTest Labs](devtest-lab-set-lab-policy.md) — informacje o sposobie modyfikowania innych zasad laboratorium.
