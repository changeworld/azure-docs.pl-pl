---
title: Uaktualnij hostów Windows Server 2012 R2 i SCVMM skonfigurowane za pomocą usługi Azure Site Recovery do systemu Windows Server 2016
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure maszyn wirtualnych platformy Azure Stack za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: b67290f72f762331a6d699fb79aef0c0d7f9fb65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275527"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Uaktualnij hostów Windows Server 2012 R2, programu SCVMM 2012 R2 skonfigurowany za pomocą usługi Azure Site Recovery do systemu Windows Server 2016 i programie SCVMM 2016

W tym artykule dowiesz się, jak uaktualnić hostów Windows Server 2012 R2 i SCVMM 2012 R2, które są skonfigurowane przy użyciu usługi Azure Site Recovery do systemu Windows Server 2016 i programie SCVMM 2016

Usługa Site Recovery przyczynia się do Twojej ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR) strategii odzyskiwania. Usługa zapewnia, że obciążeń maszyny Wirtualnej pozostaną dostępne, gdy oczekiwano i wystąpienia nieoczekiwanych awarii.

> [!IMPORTANT]
> Podczas uaktualniania systemu Windows Server 2012 R2 hostów, które zostały już skonfigurowane do replikacji przy użyciu usługi Azure Site Recovery, należy wykonać kroki opisane w tym dokumencie. Wszystkie ścieżki alternatywnej dla uaktualnienie może spowodować stany nieobsługiwane i może spowodować przerwy w replikacji lub możliwość wykonywania pracy awaryjnej.


W tym artykule dowiesz się, jak uaktualnić następujące konfiguracje w danym środowisku:

> [!div class="checklist"]
> * **Hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM** 
> * **Hosty systemu Windows Server 2012 R2, które są zarządzane przez autonomiczny serwer programu SCVMM 2012 R2** 
> * **Hosty systemu Windows Server 2012 R2, które są zarządzane przez serwer programu SCVMM 2012 R2 o wysokiej dostępności**


## <a name="prerequisites--factors-to-consider"></a>Wymagania wstępne i czynniki do rozważenia

Przed rozpoczęciem uaktualnienia należy pamiętać o następujących:-

- Jeśli masz hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM i konfiguracji środowisku autonomicznym, nastąpi podział w replikacji przy próbie przeprowadzić uaktualnienie.
- Jeśli zaznaczono opcję "*w usłudze Active Directory w ramach rozproszonego zarządzania kluczami przechowuje Moje klucze*" podczas instalowania programu SCVMM 2012 R2 na pierwszym miejscu, uaktualnienia nie zostanie ukończone pomyślnie.

- Jeśli używasz programu System Center 2012 R2 VMM, 

    - Sprawdź informacje o bazie danych w programie VMM: **Konsola programu VMM** -> **ustawienia** -> **ogólne** -> **połączenie z bazą danych**
    - Sprawdź, konta usług używane dla usługi agenta programu System Center Virtual Machine Manager
    - Upewnij się, że masz kopię zapasową bazy danych programu VMM.
    - Zanotuj nazwę bazy danych serwera SCVMM związane. Można to zrobić, przechodząc do **konsoli VMM** -> **ustawienia** -> **ogólne** -> **połączenie z bazą danych**
    - Zanotuj identyfikator programu VMM, zarówno 2012R2 podstawowego, jak i odzyskiwania serwerów programu VMM. Identyfikator programu VMM można znaleźć z rejestru "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Sprawdź, czy możesz SCVMMs nowe, które możesz dodać do klastra ma takie same nazwy jak był wcześniej. 

- Jeśli replikujesz między dwiema lokacjami zarządza SCVMMs po obu stronach, upewnij się, uaktualnienie ze strony odzyskiwania najpierw przed przystąpieniem do uaktualniania stronie podstawowego.
  > [!WARNING]
  > Podczas uaktualniania programu SCVMM 2012 R2 w obszarze rozproszone zarządzanie kluczami umożliwia **przechowywania kluczy szyfrowania w usłudze Active Directory**. Wybierz uważnie ustawienia konta usługi i rozproszonego zarządzania kluczami. Na podstawie dokonanego wyboru, zaszyfrowane dane takie jak hasła w szablonach może nie być dostępne po uaktualnieniu i mogą potencjalnie mieć wpływ na replikację za pomocą usługi Azure Site Recovery

> [!IMPORTANT]
> Zapoznaj się ze szczegółową dokumentacją programu SCVMM z [wymagań wstępnych](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM 
Na liście kroków wymienionych poniżej ma zastosowanie do konfiguracji użytkownika z [hosty funkcji Hyper-V na platformę Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) wykonywane, postępując zgodnie z dokumentem [samouczek](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Jak wspomniano w wymaganiach wstępnych, ta procedura dotyczy tylko scenariusz, w środowisku klastrowanym, a nie w konfiguracji autonomicznej do hosta funkcji Hyper-V.

1. Postępuj zgodnie z instrukcjami, aby wykonać [stopniowego uaktualniania klastra.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) do wykonywania procesu uaktualnienia stopniowego klastra.
2. Z każdego nowego hosta Windows Server 2016, który został wprowadzony w klastrze należy usunąć odwołanie hosta z systemem Windows Server 2012 R2 z usługi Azure Site Recovery, wykonując kroki opisane w [tutaj]. Powinna to być wybranej opróżnić & Wyklucz z klastra hosta.
3. Gdy *Update-VMVersion* polecenia została wykonana dla wszystkich maszyn wirtualnych, uaktualnienia zostały ukończone. 
4. Wykonaj kroki wymienione [tutaj](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) zarejestrować nowy host systemu Windows Server 2016 w usłudze Azure Site Recovery. Należy pamiętać, że lokacji funkcji Hyper-V jest już aktywne i wystarczy zarejestrować nowy host w klastrze. 
5.  Przejdź do witryny Azure portal i sprawdź stan kondycji replikowanych wewnątrz usługi Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uaktualnij hostów Windows Server 2012 R2 zarządzany przez autonomicznego serwera programu SCVMM 2012 R2
Przed rozpoczęciem uaktualniania hostów Windows Server 2012 R2, należy uaktualnić SCVMM 2012 R2 do SCVMM 2016. Wykonaj poniższe kroki:-

**Uaktualnić autonomiczne SCVMM 2012 R2 do SCVMM 2016**

1.  Odinstaluj usługi ASR dostawcy, przechodząc do pozycji Panel sterowania -> programy -> programy i funkcje -> Microsoft Azure Site Recovery, a następnie kliknij polecenie Odinstaluj
2. [Zachowaj bazę danych programu SCVMM i Uaktualnij system operacyjny](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. W **Dodaj Usuń programy**, wybierz opcję **VMM** > **Odinstaluj**. b. Wybierz **Usuń funkcje**, a następnie wybierz pozycję V**konsoli programu VMM i serwera zarządzania MM**. c. W **opcje bazy danych**, wybierz opcję **Zachowaj bazę danych**. d. Przejrzyj podsumowanie, a następnie kliknij przycisk **Odinstaluj**.

4. [Zainstalować program VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Uruchamianie programu SCVMM i sprawdzać stan poszczególnych hostów, w obszarze **sieci szkieletowe** kartę. Kliknij przycisk **Odśwież** uzyskać najnowszy stan. Powinien zostać wyświetlony stan jako "Wymaga uwagi". 
17. Zainstaluj najnowszą wersję [dostawcy Microsoft Azure Site Recovery](https://aka.ms/downloaddra) w środowisku SCVMM.
16. Zainstaluj najnowszą wersję [agenta usługi Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) na każdym hoście w klastrze. Odśwież, aby upewnić się, że SCVMM będzie mógł pomyślnie wykonać zapytania z hostami.

**Uaktualnij hostów Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki wymienione [tutaj](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) do wykonywania procesu uaktualnienia stopniowego klastra. 
2. Po dodaniu nowego hosta do klastra, Odśwież hosta z poziomu konsoli programu SCVMM do zainstalowania agenta programu VMM na tym hoście zaktualizowane.
3. Wykonaj *Update-VMVersion* można zaktualizować wersji maszyny Wirtualnej maszyn wirtualnych. 
4.  Przejdź do witryny Azure portal i sprawdź stan kondycji replikowanych maszyn wirtualnych w magazynie usługi Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uaktualnianie systemu Windows Server 2012 R2 hostów są zarządzane przez serwer programu SCVMM 2012 R2 o wysokiej dostępności
Przed rozpoczęciem uaktualniania hostów Windows Server 2012 R2, należy uaktualnić SCVMM 2012 R2 do SCVMM 2016. Następujące tryby uaktualnienia są obsługiwane podczas uaktualniania serwerów programu SCVMM 2012 R2 skonfigurowany za pomocą usługi Azure Site Recovery — trybu mieszanego, bez dodatkowych serwerów programu VMM i trybie mieszanym z dodatkowe serwery programu VMM.

**Uaktualnienie programu SCVMM 2012 R2 do SCVMM 2016**

1.  Odinstaluj usługi ASR dostawcy, przechodząc do pozycji Panel sterowania -> programy -> programy i funkcje -> Microsoft Azure Site Recovery, a następnie kliknij polecenie Odinstaluj
2. Wykonaj kroki wymienione [tutaj](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) oparte na tryb uaktualnienia, które chcesz wykonać.
3. Uruchom konsolę programu SCVMM i sprawdzać stan poszczególnych hostów, w obszarze **sieci szkieletowe** kartę. Kliknij przycisk **Odśwież** uzyskać najnowszy stan. Powinien zostać wyświetlony stan jako "Wymaga uwagi".
4. Zainstaluj najnowszą wersję [dostawcy Microsoft Azure Site Recovery](https://aka.ms/downloaddra) w środowisku SCVMM.
5. Najnowsza aktualizacja [agenta usługi Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) na każdym hoście w klastrze. Odśwież, aby upewnić się, że SC VMM jest w stanie pomyślnie odpytać hostów.


**Uaktualnij hostów Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki wymienione [tutaj](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) do wykonywania procesu uaktualnienia stopniowego klastra.
2. Po dodaniu nowego hosta do klastra, Odśwież hosta z poziomu konsoli programu SCVMM do zainstalowania agenta programu VMM na tym hoście zaktualizowane.
3. Wykonaj *Update-VMVersion* można zaktualizować wersji maszyny Wirtualnej maszyn wirtualnych. 
4.  Przejdź do witryny Azure portal i sprawdź stan kondycji replikowanych maszyn wirtualnych w magazynie usługi Recovery Services. 

## <a name="next-steps"></a>Kolejne kroki
Gdy jest wykonywane uaktualnienie hosty, można wykonać [testowanie trybu failover](tutorial-dr-drill-azure.md) do testowania kondycji stanu odzyskiwania po awarii i replikacja.

