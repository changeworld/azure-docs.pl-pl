---
title: Uaktualnianie programu Windows Server/System Center VMM 2012 R2 do systemu Windows Server 2016-Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych usługi Azure Stack za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954403"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Uaktualnianie programu Windows Server Server/System Center 2012 R2 VMM do systemu Windows Server/VMM 2016 

W tym artykule pokazano, jak uaktualnić hosty systemu Windows Server 2012 R2 & scvmm 2012 R2 skonfigurowane z usługą Azure Site Recovery do systemu Windows Server 2016 & SCVMM 2016

Odzyskiwanie witryny przyczynia się do ciągłości biznesowej i strategii odzyskiwania po awarii (BCDR). Usługa zapewnia, że obciążenia maszyny Wirtualnej pozostają dostępne, gdy wystąpią oczekiwane i nieoczekiwane awarie.

> [!IMPORTANT]
> Podczas uaktualniania hostów systemu Windows Server 2012 R2, które są już skonfigurowane do replikacji za pomocą usługi Azure Site Recovery, należy wykonać kroki wymienione w tym dokumencie. Każda alternatywna ścieżka wybrana do uaktualnienia może spowodować nieobsługiwać stanów i może spowodować przerwę w replikacji lub możliwość wykonywania pracy awaryjnej.


W tym artykule dowiesz się, jak uaktualnić następujące konfiguracje w swoim środowisku:

> [!div class="checklist"]
> * **Hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM** 
> * **Hosty systemu Windows Server 2012 R2 zarządzane przez autonomiczny serwer SCVMM 2012 R2** 
> * **Hosty systemu Windows Server 2012 R2 zarządzane przez serwer SCVMM 2012 R2 o wysokiej dostępności**


## <a name="prerequisites--factors-to-consider"></a>Warunki wstępne & czynników do rozważenia

Przed uaktualnieniem należy zwrócić uwagę na następujące kwestie: -

- Jeśli masz hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM i jest to autonomiczna konfiguracja środowiska, zostanie przerwana replikacja, jeśli spróbujesz przeprowadzić uaktualnienie.
- Jeśli podczas instalowania programu SCVMM 2012 R2 w*obszarze Zarządzanie kluczami rozproszonymi wybrano opcję "nie przechowywanie kluczy w usłudze Active Directory",* uaktualnienia nie zostaną pomyślnie zakończone.

- Jeśli korzystasz z programu System Center 2012 R2 VMM, 

    - Sprawdzanie informacji o bazie danych w programie VMM: -> **Ustawienia** ->  **konsoli programu VMM****Ogólne** -> **połączenie bazy danych**
    - Sprawdzanie kont usług używanych w usłudze Agent programu System Center Virtual Machine Manager
    - Upewnij się, że masz kopię zapasową bazy danych programu VMM.
    - Zanotuj nazwę bazy danych zaangażowanych serwerów SCVMM. Można to zrobić, przechodząc do połączenia**z ogólną** -> **bazą danych** **ustawień** ->  **konsoli** -> programu VMM
    - Zanotuj identyfikator programu VMM zarówno podstawowych, jak i odzyskiwania serwerów VMM 2012R2. Identyfikator programu VMM można znaleźć w rejestrze "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Upewnij się, że nowe scvmms, które można dodać do klastra ma takie same nazwy jak było wcześniej. 

- Jeśli replikujesz między dwiema lokacjami zarządzanymi przez scvmmy po obu stronach, przed uaktualnieniem strony podstawowej należy najpierw uaktualnić stronę odzyskiwania.
  > [!WARNING]
  > Podczas uaktualniania programu SCVMM 2012 R2 w obszarze Zarządzanie kluczami rozproszonymi wybierz opcję **przechowywania kluczy szyfrowania w usłudze Active Directory**. Wybierz ustawienia konta usługi i rozdzielone zarządzanie kluczami ostrożnie. Na podstawie wyboru zaszyfrowane dane, takie jak hasła w szablonach, mogą nie być dostępne po uaktualnieniu i mogą potencjalnie wpłynąć na replikację za pomocą usługi Azure Site Recovery

> [!IMPORTANT]
> Zapoznaj się ze szczegółową dokumentacją SCVMM [prerequisites](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hosty systemu Windows Server 2012 R2, które nie są zarządzane przez program SCVMM 
Lista kroków wymienionych poniżej dotyczy konfiguracji użytkownika z [hostów funkcji Hyper-V na platformę Azure,](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) wykonanej zgodnie z tym [samouczkiem](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Jak wspomniano w wymaganiach wstępnych, te kroki dotyczą tylko scenariusza środowiska klastrowanego, a nie w autonomicznej konfiguracji hosta funkcji Hyper-V.

1. Wykonaj kroki, aby wykonać [uaktualnienie klastra stopniowego.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) , aby wykonać proces uaktualniania klastra stopniowego.
2. Przy każdym nowym hoście systemu Windows Server 2016 wprowadzonym w klastrze usuń odwołanie do hosta systemu Windows Server 2012 R2 z usługi Azure Site Recovery, wykonując kroki wymienione [tutaj]. Powinien to być host wybrany do opróżnienia & eksmisji z klastra.
3. Po wykonaniu polecenia *Update-VMVersion* dla wszystkich maszyn wirtualnych uaktualnienia zostały zakończone. 
4. Użyj kroków wymienionych [w tym miejscu,](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) aby zarejestrować nowy host systemu Windows Server 2016 w usłudze Azure Site Recovery. Należy pamiętać, że witryna funkcji Hyper-V jest już aktywna i wystarczy zarejestrować nowego hosta w klastrze. 
5.  Przejdź do witryny Azure portal i sprawdź stan stanu replikacji w usługach odzyskiwania

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Uaktualnianie hostów systemu Windows Server 2012 R2 zarządzanych przez autonomiczny serwer SCVMM 2012 R2
Przed uaktualnieniem hostów systemu Windows Sever 2012 R2 należy uaktualnić program SCVMM 2012 R2 do programu SCVMM 2016. Wykonaj poniższe kroki:-

**Uaktualnienie samodzielnego scvmm 2012 R2 do SCVMM 2016**

1.  Odinstaluj dostawcę ASR, przechodząc do Panelu sterowania -> Programy -> programy i funkcje ->Microsoft Azure Site Recovery , i kliknij przycisk Odinstaluj
2. [Zachowaj bazę danych SCVMM i uaktualnij system operacyjny](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. W **obszarze Dodaj programy usuń**wybierz pozycję **VMM** > **Uninstall**. b. Wybierz **pozycję Usuń funkcje**, a następnie wybierz pozycję V**MM management Server i VMM Console**. d. W **obszarze Opcje bazy danych**wybierz pozycję **Zachowaj bazę danych**. d. Przejrzyj podsumowanie i kliknij przycisk **Odinstaluj**.

4. [Instalowanie programu VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Uruchom scvmm i sprawdź stan każdego hosta na karcie **Tkaniny.** Kliknij **przycisk Odśwież,** aby uzyskać najnowszy stan. Powinieneś zobaczyć stan jako "Wymaga uwagi". 
17. Zainstaluj najnowszego [dostawcę odzyskiwania witryny platformy Microsoft Azure](https://aka.ms/downloaddra) w programie SCVMM.
16. Zainstaluj najnowszy [agent usługi odzyskiwania platformy Microsoft Azure (MARS)](https://aka.ms/latestmarsagent) na każdym hoście klastra. Odśwież, aby upewnić się, że SCVMM jest w stanie pomyślnie zbadać hosty.

**Uaktualnianie hostów systemu Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki wymienione [w tym miejscu,](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) aby wykonać proces uaktualniania klastra stopniowego. 
2. Po dodaniu nowego hosta do klastra odśwież hosta z konsoli SCVMM, aby zainstalować agenta programu VMM na tym zaktualizowanym hoście.
3. Wykonaj *Update-VMVersion,* aby zaktualizować wersje maszyn wirtualnych maszyn wirtualnych. 
4.  Przejdź do witryny Azure portal i sprawdź stan stanu replikacji maszyn wirtualnych w magazynie usług odzyskiwania. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Uaktualnianie hostów systemu Windows Server 2012 R2 jest zarządzane przez serwer SCVMM 2012 R2 o wysokiej dostępności
Przed uaktualnieniem hostów systemu Windows Sever 2012 R2 należy uaktualnić program SCVMM 2012 R2 do programu SCVMM 2016. Następujące tryby uaktualniania są obsługiwane podczas uaktualniania serwerów SCVMM 2012 R2 skonfigurowanych za pomocą usługi Azure Site Recovery — tryb mieszany bez dodatkowych serwerów programu VMM & trybie mieszanym z dodatkowymi serwerami programu VMM.

**Aktualizacja SCVMM 2012 R2 do SCVMM 2016**

1.  Odinstaluj dostawcę ASR, przechodząc do Panelu sterowania -> Programy -> programy i funkcje ->Microsoft Azure Site Recovery , i kliknij przycisk Odinstaluj
2. Wykonaj kroki wymienione [w tym miejscu](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) w oparciu o tryb uaktualnienia, który chcesz wykonać.
3. Uruchom konsolę SCVMM i sprawdź stan każdego hosta na karcie **Sieci szkieletowe.** Kliknij **przycisk Odśwież,** aby uzyskać najnowszy stan. Powinieneś zobaczyć stan jako "Wymaga uwagi".
4. Zainstaluj najnowszego [dostawcę odzyskiwania witryny platformy Microsoft Azure](https://aka.ms/downloaddra) w programie SCVMM.
5. Zaktualizuj najnowszy [agent usługi odzyskiwania platformy Microsoft Azure (MARS)](https://aka.ms/latestmarsagent) na każdym hoście klastra. Odśwież, aby upewnić się, że sc VMM jest w stanie pomyślnie zapytania hostów.


**Uaktualnianie hostów systemu Windows Server 2012 R2 do systemu Windows Server 2016**

1. Wykonaj kroki wymienione [w tym miejscu,](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) aby wykonać proces uaktualniania klastra stopniowego.
2. Po dodaniu nowego hosta do klastra odśwież hosta z konsoli SCVMM, aby zainstalować agenta programu VMM na tym zaktualizowanym hoście.
3. Wykonaj *Update-VMVersion,* aby zaktualizować wersje maszyn wirtualnych maszyn wirtualnych. 
4.  Przejdź do witryny Azure portal i sprawdź stan stanu replikacji maszyn wirtualnych w magazynie usług odzyskiwania. 

## <a name="next-steps"></a>Następne kroki
Po wykonaniu uaktualnienia hostów można wykonać [test pracy awaryjnej,](tutorial-dr-drill-azure.md) aby przetestować kondycję replikacji i stanu odzyskiwania po awarii.

