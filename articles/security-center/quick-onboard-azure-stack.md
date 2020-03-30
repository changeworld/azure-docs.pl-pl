---
title: Wbudowane maszyny wirtualne usługi Azure Stack do usługi Azure Security Center
description: Ten przewodnik Szybki start pokazuje, jak aprowizować rozszerzenie maszyny wirtualnej usługi Azure Monitor, Update and Configuration Management na maszynach wirtualnych usługi Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686527"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Szybki start: dołączanie maszyn wirtualnych usługi Azure Stack do centrum zabezpieczeń
Po załączeniu subskrypcji platformy Azure można włączyć usługę Security Center, aby chronić maszyny wirtualne uruchomione w usłudze Azure Stack, dodając rozszerzenie maszyny wirtualnej **usługi Azure Monitor, Update and Configuration Management** z portalu Azure Stack marketplace.

Ten przewodnik Szybki start pokazuje, jak dodać rozszerzenie maszyny wirtualnej **usługi Azure Monitor, Update and Configuration Management** na maszynie wirtualnej (obsługiwane są zarówno systemy Linux, jak i Windows) uruchomione w usłudze Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Przed uruchomieniem tego przewodnika Szybki start należy mieć subskrypcję platformy Azure w warstwie Standardowa usługi Security Center. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Możesz wypróbować warstwę Security Center Standard bez żadnych kosztów przez 30 dni. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Wybieranie obszaru roboczego w usłudze Azure Security Center

1. Zaloguj się do [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu **Microsoft Azure** wybierz pozycję **Centrum zabezpieczeń**. Zostanie otwarte okno **Security Center — Przegląd**. 

   ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
4. Wybierz kartę **Rozpoczęcie pracy**.

   ![Wprowadzenie][3]

5. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, do którego chcesz, aby maszyna wirtualna usługi Azure Stack zgłaszała dane zabezpieczeń.

    ![Dodawanie komputera spoza platformy Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Otwiera się blok **Agent bezpośredni** z łączem do pobierania agenta i kluczy identyfikatora obszaru roboczego do użycia w konfiguracji agenta.

   >[!NOTE]
   > NIE trzeba ręcznie pobierać agenta. Agent zostanie zainstalowany jako rozszerzenie maszyny Wirtualnej w poniższych krokach.

6. Po prawej stronie **identyfikatora obszaru roboczego** wybierz ikonę kopiowania i wklej identyfikator do Notatnika.

7. Po prawej stronie **klucza podstawowego** wybierz ikonę kopiowania i wklej klucz do Notatnika.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Dodawanie rozszerzenia maszyny wirtualnej do istniejących maszyn wirtualnych usługi Azure Stack
Teraz należy dodać rozszerzenie maszyny wirtualnej **usługi Azure Monitor, Update and Configuration Management** do maszyn wirtualnych uruchomionych w usłudze Azure Stack.

1. Na nowej karcie przeglądarki zaloguj się do portalu **usługi Azure Stack.**
2. Przejdź do strony **Maszyny wirtualne,** wybierz maszynę wirtualną, którą chcesz chronić za pomocą usługi Security Center. Aby uzyskać informacje na temat tworzenia maszyny wirtualnej w usłudze Azure Stack, zobacz [ten szybki start dla maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) lub ten szybki start dla maszyn [wirtualnych systemu Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
4. Kliknij kartę **Dodaj.** Zostanie otwarty blok menu **Nowy zasób** i zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych. 
5. Wybierz rozszerzenie **Azure Monitor, Update and Configuration Management** i kliknij przycisk **Utwórz**. Zostanie otwarty blok konfiguracji **rozszerzenia instalacji.**

>[!NOTE]
> Jeśli nie widzisz rozszerzenia **Usługi Azure Monitor, update i configuration management** na liście w witrynie marketplace, skontaktuj się z operatorem usługi Azure Stack, aby go udostępnić.

6. W bloku konfiguracji **rozszerzenia instalacji** wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy),** które zostały skopiowane do Notatnika w poprzedniej procedurze.
7. Po zakończeniu dostarczania niezbędnych ustawień konfiguracyjnych kliknij przycisk **OK**.
8. Po zakończeniu instalacji rozszerzenia jego stan będzie pokazywał się jako **Aprowizowanie powiodło się**. Może upłynąć do jednej godziny, aby maszyna wirtualna pojawiła się w portalu Centrum zabezpieczeń.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla systemu Windows, zobacz [Łączenie komputerów z systemem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Aby zapoznać się z problemami z agentami w systemie Linux w systemie Linux, zobacz [Rozwiązywanie problemów z agentem systemu Linux usługi Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Teraz możesz monitorować maszyny wirtualne platformy Azure oraz komputery spoza platformy Azure w jednym miejscu. W witrynie Security Center portal na platformie Azure w obszarze **Obliczenia**masz przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich zaleceniami. Usługa Security Center zawiera również wszystkie wykrywanie tych komputerów w alertach zabezpieczeń.

  ![Blok Obliczanie][6]

Istnieją dwa typy ikon przedstawianych w bloku **Obliczanie**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Komputer spoza platformy Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure (maszyny wirtualne usługi Azure Stack będą wyświetlane w tej grupie)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebne, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu usługi Azure Stack.

Aby usunąć rozszerzenie:

1. Otwórz **portal azure stack**.
2. Przejdź do strony **Maszyny wirtualne,** wybierz maszynę wirtualną, z której chcesz usunąć rozszerzenie.
3. Wybierz **rozszerzenia**, wybierz rozszerzenie **Microsoft.EnterpriseCloud.Monitoring**.
4. Kliknij **przycisk Odinstaluj**i potwierdź wybór, klikając **przycisk Tak**.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start zaaprowizowano rozszerzenie Usługi Azure Monitor, update and Configuration Management na maszynie wirtualnej uruchomionej w usłudze Azure Stack. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
