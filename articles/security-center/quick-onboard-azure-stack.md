---
title: Usługa Azure Security Center Szybki Start — dołączanie usługi Azure Stack maszyn wirtualnych do usługi Security Center | Dokumentacja firmy Microsoft
description: Ten przewodnik Szybki Start przedstawia sposób aprowizacji rozszerzenia maszyny wirtualnej usługi Azure Monitor, aktualizacji i zarządzania konfiguracją na maszynach wirtualnych usługi Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
editor: ''
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 7a630acee079301b95e7e05f5c5333dd116abb68
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563798"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Szybki start:  Dołączanie maszyn wirtualnych usługi Azure Stack do usługi Security Center
Po dołączeniu subskrypcji platformy Azure możesz włączyć usługę Security Center, aby chronić maszyny wirtualne uruchomione w usłudze Azure Stack, dodając **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** rozszerzenia maszyny wirtualnej Usługa Azure Stack Portal marketplace.

Ten przewodnik Szybki Start przedstawiono sposób dodawania **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** rozszerzenie maszyny wirtualnej na maszynie wirtualnej (z systemami Linux i Windows są zarówno obsługiwane) uruchomiona w usłudze Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Musi mieć subskrypcję platformy Azure na warstwy standardowa usługi Security Center przed rozpoczęciem tego przewodnika Szybki Start. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Możesz spróbować Security Center w warstwie standardowa bezpłatnie przez 30 dni. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Wybierz swój obszar roboczy w usłudze Azure Security Center

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu platformy **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**. 

   ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
4. Wybierz kartę **Rozpoczęcie pracy**.

   ![Rozpoczęcie pracy][3]

5. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, który ma maszyny Wirtualnej stosu platformy Azure do raportu zabezpieczeń danych.

    ![Dodawanie komputera spoza platformy Azure](./media/quick-onboard-windows-computer/non-azure.png)

   **Agent bezpośredni** blok zostanie otwarty z linkiem umożliwiającym pobieranie agenta i klucze dla Identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.

   >[!NOTE]
   > NIE trzeba ręcznie Pobierz agenta. Agent zostanie zainstalowany jako rozszerzenie maszyny Wirtualnej w poniższych krokach.

6. Po prawej stronie **identyfikatora obszaru roboczego** wybierz ikonę kopiowania i wklej identyfikator do Notatnika.

7. Po prawej stronie **klucza podstawowego** wybierz ikonę kopiowania i wklej klucz do Notatnika.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Dodaj rozszerzenie maszyny wirtualnej do istniejących maszyn wirtualnych usługi Azure Stack
Teraz należy dodać **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** rozszerzenie maszyny wirtualnej do maszyn wirtualnych uruchomionych na usługi Azure Stack.

1. W nowej karcie przeglądarki Zaloguj się do Twojej **usługi Azure Stack** portalu.
2. Przejdź do **maszyn wirtualnych** wybierz maszynę wirtualną, którą chcesz chronić za pomocą usługi Security Center. Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej w usłudze Azure Stack, zobacz [ten przewodnik Szybki Start dla maszyn wirtualnych Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) lub [ten przewodnik Szybki Start dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Wybierz pozycję **Rozszerzenia**. Lista rozszerzeń maszyny wirtualnej na tej maszynie wirtualnej jest wyświetlana.
4. Kliknij przycisk **Dodaj** kartę. **Nowy zasób** bloku menu otwiera i wyświetla listę rozszerzeń dostępnych maszyny wirtualnej. 
5. Wybierz **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** rozszerzenie i kliknij przycisk **Utwórz**. **Zainstalować rozszerzenie** zostanie otwarty blok konfiguracji.

>[!NOTE]
> Jeśli nie widzisz **usługi Azure Monitor, aktualizacji i zarządzania konfiguracją** rozszerzenie wymienione w portalu marketplace, skontaktuj się z operatorem usługi Azure Stack, aby udostępnić go.

6. Na **zainstalować rozszerzenie** bloku konfiguracja, Wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)** skopiowany do Notatnika w poprzedniej procedurze.
7.  Po zakończeniu realizacji niezbędnych ustawień konfiguracji, kliknij przycisk **OK**.
8. Po ukończeniu instalacji rozszerzenia, jego stan będzie wyświetlany jako **aprowizacji Powodzenie**. Może potrwać do godziny dla maszyny wirtualnej, które pojawią się w portalu usługi Security Center.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla Windows, zobacz [komputerów Windows Połącz](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Zobacz Rozwiązywanie problemów agenta dla systemu Linux [Rozwiązywanie problemów z usługi Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Teraz możesz monitorować maszyny wirtualne platformy Azure oraz komputery spoza platformy Azure w jednym miejscu. W usłudze Security Center portalu na platformie Azure, w obszarze **obliczenia**, masz przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich zalecenia. Usługa Security Center udostępnia również żadnego procesu wykrywania dla tych komputerów w alertach zabezpieczeń.

  ![Blok Obliczanie][6]

Istnieją dwa typy ikon przedstawianych w bloku **Obliczanie**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Komputer spoza platformy Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure (maszyny wirtualne platformy Azure Stack pojawi się w tej grupie)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebny, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu usługi Azure Stack.

Aby usunąć rozszerzenie:

1. Otwórz **portalu usługi Azure Stack**.
2. Przejdź do **maszyn wirtualnych** stronie, wybierz maszynę wirtualną, z którego chcesz usunąć rozszerzenie.
3. Wybierz **rozszerzenia**, zaznacz rozszerzenie **Microsoft.EnterpriseCloud.Monitoring**.
4. Kliknij pozycję **Odinstaluj**i Potwierdź wybór, klikając przycisk **tak**.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start aprowizowano rozszerzenia usługi Azure Monitor, aktualizacji i zarządzania konfiguracją na maszynę wirtualną działającą w usłudze Azure Stack. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
