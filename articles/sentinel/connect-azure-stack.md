---
title: Wbudowane maszyny wirtualne usługi Azure Stack do usługi Azure Sentinel | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak aprowizować rozszerzenie maszyny wirtualnej usługi Azure Monitor, Update i Configuration Management na maszynach wirtualnych usługi Azure Stack i rozpocząć monitorowanie ich za pomocą usługi Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588522"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Łączenie maszyn wirtualnych usługi Azure Stack z usługą Azure Sentinel




Dzięki usłudze Azure Sentinel możesz monitorować maszyny wirtualne uruchomione na platformie Azure i usłudze Azure Stack w jednym miejscu. Aby na pokładzie maszyn usługi Azure Stack do usługi Azure Sentinel, należy najpierw dodać rozszerzenie maszyny wirtualnej do istniejących maszyn wirtualnych usługi Azure Stack. 

Po podłączeniu komputerów usługi Azure Stack wybierz z galerii pulpitów nawigacyjnych, które powierzchni szczegółowe informacje na podstawie danych. Te pulpity nawigacyjne można łatwo dostosować do twoich potrzeb.



## <a name="add-the-virtual-machine-extension"></a>Dodawanie rozszerzenia maszyny wirtualnej 

Dodaj rozszerzenie maszyny wirtualnej **usługi Azure Monitor, Update i Configuration Management** do maszyn wirtualnych uruchomionych w usłudze Azure Stack. 

1. Na nowej karcie przeglądarki zaloguj się do [portalu usługi Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Przejdź do strony **Maszyny wirtualne,** wybierz maszynę wirtualną, którą chcesz chronić za pomocą usługi Azure Sentinel. Aby uzyskać informacje dotyczące tworzenia maszyny wirtualnej w usłudze Azure Stack, zobacz [Tworzenie maszyny wirtualnej dla systemu Windows za pomocą portalu Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) lub Tworzenie maszyny [wirtualnej serwera systemu Linux przy użyciu portalu Azure Stack.](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
4. Kliknij kartę **Dodaj.** Zostanie otwarty blok menu **Nowy zasób** i zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych. 
5. Wybierz rozszerzenie **Azure Monitor, Update i Configuration Management** i kliknij przycisk **Utwórz**. Zostanie otwarte okno konfiguracji **rozszerzenia instalacji.**

   ![Ustawienia zarządzania monitorem, aktualizacjami i konfiguracjami usługi Azure](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Jeśli nie widzisz rozszerzenia **Usługi Azure Monitor, update i configuration management** wymienione w portalu marketplace, skontaktuj się z operatorem usługi Azure Stack, aby go udostępnić.

6. W menu Azure Sentinel wybierz **pozycję Ustawienia obszaru roboczego,** po którym następuje **zaawansowane,** a następnie skopiuj **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy).** 
1. W oknie **rozszerzenia instalacji usługi** Azure Stack wklej je we wskazanych polach i kliknij przycisk **OK**.
1. Po zakończeniu instalacji rozszerzenia jego stan jest wyświetlany jako **Aprowizowanie powiodło się**. Może upłynąć do jednej godziny dla maszyny wirtualnej do wyświetlenia w witrynie Azure Sentinel portal.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla systemu Windows, zobacz [Łączenie komputerów z systemem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Aby zapoznać się z problemami z agentami w systemie Linux w systemie Linux, zobacz [Rozwiązywanie problemów z agentem systemu Linux usługi Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

W witrynie Azure Sentinel portal na platformie Azure w obszarze **Maszyny wirtualne**masz przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich stanem. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebne, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu usługi Azure Stack.

Aby usunąć rozszerzenie:

1. Otwórz **portal azure stack**.
2. Przejdź do strony **Maszyny wirtualne,** wybierz maszynę wirtualną, z której chcesz usunąć rozszerzenie.
3. Wybierz **rozszerzenia**, wybierz rozszerzenie **Microsoft.EnterpriseCloud.Monitoring**.
4. Kliknij **przycisk Odinstaluj**i potwierdź swój wybór.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane z [urządzeń common error format](connect-common-event-format.md) do usługi Azure Sentinel.
