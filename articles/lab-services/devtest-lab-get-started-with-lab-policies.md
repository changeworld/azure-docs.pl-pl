---
title: Zarządzanie podstawowymi zasadami laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić niektóre podstawowe zasady (ustawienia) dla laboratorium w usłudze DevTest Labs
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
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773846"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzanie podstawowe zasadami dla laboratorium w usłudze Azure DevTest Labs

Usługa Azure DevTest Labs umożliwia decydują o koszcie i zminimalizować straty w laboratorium, Zarządzanie zasadami (ustawienia) w każdym środowisku laboratoryjnym. W tym artykule Rozpoczynanie pracy z zasadami, jak ustawić dwóch najważniejszych zasad — ograniczenie liczby maszyn wirtualnych (VM), które mogą być tworzone lub żądane przez pojedynczego użytkownika i konfigurując automatycznego zamykania. Aby wyświetlić sposób ustawiania każdych zasad laboratorium, zobacz [Definiowanie zasad laboratorium Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Uzyskiwanie dostępu do zasady laboratorium Azure DevTest Labs
Poniższe kroki prowadzą przez proces konfigurowania zasad dla laboratorium w usłudze Azure DevTest Labs:

Aby wyświetlić i zmienić zasad dla laboratorium, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.   

1. Wybierz **konfiguracji i zasad**.

    ![Okienko ustawień zasad](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **Konfiguracji i zasad** okienko zawiera menu Ustawienia, które można określić. W tym artykule opisano ustawienia tylko dla **maszyn wirtualnych na użytkownika**, **automatycznego zamykania**, i **Auto-start**. Aby dowiedzieć się więcej o pozostałych ustawieniach, zobacz [zarządzanie wszystkimi zasadami dla laboratorium w usłudze Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Zestaw maszyn wirtualnych na użytkownika
Zasady dla **maszyn wirtualnych na użytkownika** można określić maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przez użytkownika. Jeśli użytkownik próbuje utworzyć lub oświadczeń maszyny Wirtualnej, po osiągnięciu limitu użytkownik, komunikat o błędzie wskazuje, że maszyna wirtualna nie może być utworzony zgłoszone. 

1. W laboratorium **konfiguracji i zasad** menu, wybierz opcję **maszyn wirtualnych na użytkownika**.
   
    ![Maszyny wirtualne na użytkownika](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wybierz **tak** ograniczyć liczbę maszyn wirtualnych na użytkownika. Jeśli chcesz ograniczyć liczbę maszyn wirtualnych na użytkownika, wybierz opcję **nie**. Jeśli wybierzesz **tak**, podaj wartość liczbową wskazującą maksymalną liczbę maszyn wirtualnych, które mogą być tworzone lub zgłoszone przez użytkownika. 

1. Wybierz **tak** Aby ograniczyć liczbę maszyn wirtualnych używających dysków SSD (dysk SSD). Jeśli nie chcesz ograniczyć liczbę maszyn wirtualnych, które można używać dysków SSD, wybierz **nie**. Jeśli wybierzesz **tak**, wprowadź wartość wskazującą maksymalną liczbę maszyn wirtualnych, które mogą być tworzone przy użyciu dysków SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-auto-shutdown"></a>Zestaw automatycznego zamykania
Zasady automatycznego zamykania pomaga zminimalizować straty laboratorium, umożliwiając umożliwia określenie czasu zamykania maszyn wirtualnych w tym laboratorium.

1. W laboratorium **konfiguracji i zasad** okienku wybierz **automatycznego zamykania**.
   
    ![Automatyczne zamykanie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.

1. Włączenie tych zasad, określ czas (i strefy czasowej) do zamykania wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym.

1. Określ **tak** lub **nie** dla opcji wysłać powiadomienie z 15 minut przed chwilą określonego automatycznego zamykania. Jeśli wybierzesz **tak**wprowadź punkt końcowy adres URL elementu webhook lub określenie, gdzie chcesz, aby powiadomienia mają być opublikowane lub wysyłane adres e-mail. Użytkownik odbiera powiadomienie i znajduje się opcja opóźnienie zamknięcia systemu.

   Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API usługi Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wybierz pozycję **Zapisz**.

Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym. Aby usunąć ustawienie z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **automatycznego zamykania** ustawienie.

## <a name="set-auto-start"></a>Zestaw do automatycznego uruchamiania
Zasady automatycznego uruchamiania pozwala określić, gdy maszyny wirtualne w bieżącym środowisku laboratoryjnym, które mają być uruchamiane.  

1. W laboratorium **konfiguracji i zasad** okienku wybierz **Auto-start**.
   
    ![Automatyczne uruchamianie](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.

3. Włączenie tych zasad, należy określić zaplanowany czas rozpoczęcia, strefę czasową i dni tygodnia, do którego odnosi się czas. 

4. Wybierz pozycję **Zapisz**.

Po włączeniu tych zasad nie są automatycznie stosowane do maszyn wirtualnych w bieżącym środowisku laboratoryjnym. Aby zastosować to ustawienie do istniejącej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **Auto-start** ustawienie.

## <a name="next-steps"></a>Kolejne kroki

- [Definiowanie zasad laboratorium Azure DevTest Labs](devtest-lab-set-lab-policy.md) — Dowiedz się, jak zmodyfikować inne zasady laboratorium.
