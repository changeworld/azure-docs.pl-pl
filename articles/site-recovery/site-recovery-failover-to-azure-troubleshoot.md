---
title: Rozwiązywanie problemów z trybu failover do błędów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów w przypadku przechodzenia w tryb failover na platformie Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 2156ee6cf27ecfa32b19ad5bbef7549e99c3f7ef
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492859"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Rozwiązywanie problemów podczas przechodzenia w tryb failover maszyny Wirtualnej VMware lub maszyny fizyczne na platformie Azure

Może pojawić się jeden z następujących błędów podczas wykonywania pracy w trybie failover maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, należy użyć opisane kroki poszczególnych warunków wystąpienia błędu.

## <a name="failover-failed-with-error-id-28031"></a>Tryb failover nie powiodło się z Identyfikator błędu 28031

Usługa Site Recovery nie był w stanie niepowodzenia tworzenia maszyny wirtualnej platformy Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego limitu przydziału, które są dostępne do utworzenia maszyny wirtualnej: Dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) Aby zwiększyć limit przydziału.

* Próbujesz trybu failover maszyny wirtualne rozmiar różnych rodzin w tym samym zestawie dostępności. Upewnij się, wybierz ten sam rodzina rozmiarów dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmiana rozmiaru, przechodząc do ustawień obliczenia i sieć maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

* Brak zasad w ramach subskrypcji, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady aby umożliwić tworzenie maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

## <a name="failover-failed-with-error-id-28092"></a>Tryb failover nie powiodło się z Identyfikator błędu 28092

Usługa Site Recovery nie mogła utworzyć interfejsu sieciowego dla nieudane przez maszynę wirtualną. Upewnij się, że masz wystarczającego limitu przydziału, które są dostępne w celu utworzenia interfejsów sieciowych w ramach subskrypcji. Dostępny limit przydziału można sprawdzić, przechodząc do subskrypcji -> użycie i przydziały. Możesz otworzyć [nowe żądanie pomocy technicznej](https://aka.ms/getazuresupport) Aby zwiększyć limit przydziału. Jeśli masz wystarczającego limitu przydziału, a następnie może to być tymczasowy wysyłania, spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występować po ponawianiu prób, pozostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Tryb failover nie powiodło się z Identyfikator błędu 70038

Usługa Site Recovery nie mógł utworzyć nie powiodło się za pośrednictwem klasycznej maszyny wirtualnej na platformie Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualną, która jest wymagana dla maszyny wirtualnej, która ma zostać utworzony nie istnieje. Tworzenie sieci wirtualnej, zgodnie z postanowieniami w obszarze ustawień obliczenia i sieć maszyny wirtualnej lub zmodyfikuj ustawienia sieci wirtualnej, która już istnieje, a następnie ponów próbę pracy awaryjnej.

## <a name="failover-failed-with-error-id-170010"></a>Tryb failover nie powiodło się z 170010 Identyfikator błędu

Usługa Site Recovery nie był w stanie niepowodzenia tworzenia maszyny wirtualnej platformy Azure. Może się zdarzyć, ponieważ wewnętrzny działania wypełniania nie powiodła się dla maszyny wirtualnej w środowisku lokalnym.

Aby wyświetlić wszystkie maszyny na platformie Azure, w środowisku systemu Azure wymaga niektóre sterowniki rozruchowe są uruchamiane stanu i usług, takich jak DHCP, będzie w stanie automatyczne uruchamianie. W związku z tym, działanie wypełniania w momencie przejścia w tryb failover konwertuje typ uruchomienia **sterowniki atapi, intelide, storflt, vmbus i storvsc** do ekranu startowego rozruchu. Konwertuje również typ uruchomienia kilku usług, takich jak DHCP, automatyczne uruchamianie. To działanie może zakończyć się niepowodzeniem ze względu na problemy dotyczące określonego środowiska. 

Aby ręcznie zmienić typ uruchamiania sterowników dla **systemie operacyjnym gościa Windows**, wykonaj poniższe kroki:

1. [Pobierz](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) nie wypełniania skrypt i uruchom go jako zgodny. Ten skrypt sprawdza, czy maszyna wirtualna wymaga wypełniania zasobami.

    `.\Script-no-hydration.ps1`

    Jeśli wymagana jest wypełniania, daje następujące wyniki:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    W przypadku, gdy maszyna wirtualna spełnia wymagania nie wypełniania, skrypt da wynik, "ten system spełnia wymagania nie wypełniania". W takim przypadku wszystkie sterowniki i usługi są w stanie zgodnie z wymaganiami platformy Azure i wypełniania na maszynie Wirtualnej nie jest wymagana.

2. Uruchom skrypt nie wypełniania zestawu w następujący sposób, jeśli maszyna wirtualna nie spełnia wymagań nie wypełniania.

    `.\Script-no-hydration.ps1 -set`
    
    To zostaną przekonwertowane na typ uruchomienia sterowników i daje wynik, takich jak poniżej:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH nieudane przez maszynę wirtualną ze względu na szary przycisk Połącz na maszynie wirtualnej

Jeśli **Connect** przycisku w trybie Failover maszyny Wirtualnej na platformie Azure jest wyszarzona i nie masz połączenia Expressroute lub sieci VPN typu lokacja-lokacja połączenie, następnie na platformie Azure

1. Przejdź do **maszyny wirtualnej** > **sieć**, kliknij nazwę interfejsu sieciowego wymagane.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do **konfiguracje adresów Ip**, następnie kliknij pole nazwy wymaganych konfiguracji adresu IP. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publiczny adres IP, kliknij **Włącz**. ![Włączyć adresu IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kliknij pozycję **Skonfiguruj wymagane ustawienia** > **Utwórz nową**. ![Tworzenie nowego elementu](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę publicznego adresu, wybierz domyślne opcje dotyczące **jednostki SKU** i **przypisania**, następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać zmiany, kliknij **Zapisz**.
7. Zamknięcie paneli i przejdź do **Przegląd** części maszyny wirtualnej do połączenia RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nie można nawiązać połączenia ani protokołu RDP/SSH - dostępne przycisku Połącz z maszyną Wirtualną

Jeśli **Connect** przycisku w trybie Failover maszyny Wirtualnej na platformie Azure jest dostępna (nie szary), a następnie sprawdź **diagnostykę rozruchu** na maszynie wirtualnej i sprawdź błędy wymienione w [w tym artykule](../virtual-machines/windows/boot-diagnostics.md).

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj przejść w tryb failover do starszego punktu odzyskiwania.
2. Jeśli aplikacja wewnątrz maszyny wirtualnej nie jest się, spróbuj przejść w tryb failover do punktu odzyskiwania spójnego na poziomie aplikacji.
3. Jeśli maszyna wirtualna jest przyłączona do domeny, a następnie upewnij się, że kontroler domeny działa dokładnie. Można to zrobić, postępując poniżej podane kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci.

    b.  Upewnij się, że jest mógł dołączyć do tej samej domeny, na którym nieudane przez maszynę wirtualną oczekuje się, co pozwoli uzyskać.

    c. Jeśli kontroler domeny jest **nie** działa prawidłowo, spróbuj zalogowanie się na nieudane przez maszynę wirtualną przy użyciu konta administratora lokalnego.
4. Jeśli używasz niestandardowego serwera DNS, upewnij się, że jest dostępny. Można to zrobić, postępując poniżej podane kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci i

    b. Sprawdź, czy maszyna wirtualna jest możliwość nazwa rozpoznawanie przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie dowolnego ustawienia innego niż Diagnostyka rozruchu wymaga agenta maszyny Wirtualnej platformy Azure do zainstalowania na maszynie wirtualnej przed włączeniem trybu failover

## <a name="unexpected-shutdown-message-event-id-6008"></a>Nieoczekiwane zamknięcie wiadomości (6008 identyfikator zdarzenia)

W przypadku uruchamiania systemu Windows maszyny Wirtualnej po przejściu w tryb failover, jeśli zostanie wyświetlony komunikat nieoczekiwanego zamknięcia w odzyskanej maszyny Wirtualnej, oznacza to, stan zamknięcia maszyny Wirtualnej nie została przechwycona w punkcie odzyskiwania do trybu failover. Dzieje się tak, podczas odzyskiwania do punktu, gdy maszyna wirtualna ma nie została w pełni zamknięta.

Zwykle nie jest przyczyną problemu i zazwyczaj można zignorować dla niezaplanowanych operacji Failover. Jeśli jest planowane przełączenie w tryb failover, upewnij się, że maszyna wirtualna jest zamknięta prawidłowo przed trybu failover i zapewnić wystarczającą ilość czasu do czasu replikacji danych w sieci lokalnej do wysłania do platformy Azure. Następnie użyj **najnowsze** opcja [ekranu trybu Failover](site-recovery-failover.md#run-a-failover) tak, aby wszystkie oczekujące dane na platformie Azure są przetwarzane na punkt odzyskiwania, który jest następnie używany na potrzeby trybu failover maszyny Wirtualnej.

## <a name="unable-to-select-the-datastore"></a>Nie można wybrać magazyn danych

Ten problem jest wskazywany, gdy nie są widoczne w portalu w magazynie danych na platformie Azure, podczas próby ponownego włączenia ochrony maszyny wirtualnej, która napotkała przejścia w tryb failover. Jest to spowodowane Master target nie został rozpoznany jako maszynę wirtualną w ramach vCenters dodane do usługi Azure Site Recovery.

Aby uzyskać więcej informacji na temat ponowne włączanie ochrony maszyny wirtualnej, zobacz [ponowne włączanie ochrony i zakończyć się niepowodzeniem maszynami wstecz do lokacji lokalnej po włączeniu trybu failover na platformie Azure](vmware-azure-reprotect.md).

Aby rozwiązać ten problem:

Ręczne tworzenie Master target w programie vCenter, który zarządza maszyna źródłowa. Magazyn danych będzie dostępna po następny operacjach vCenter odnajdywania i Odśwież sieci szkieletowej.

> [!Note]
> 
> Operacje odnajdywania i Odśwież sieci szkieletowej może potrwać do 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Linux główny element docelowy rejestracji CS zakończy się niepowodzeniem z powodu błędu protokołu SSL 35 

Rejestracja usługi Azure Site Recovery Master Target z serwerem konfiguracji kończy się niepowodzeniem z powodu serwera Proxy uwierzytelniony, włączane na główny element docelowy. 
 
Ten błąd jest wskazywany przez następujące ciągi w dzienniku instalacji: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Aby rozwiązać ten problem:
 
1. Na serwerze konfiguracji maszyny Wirtualnej Otwórz wiersz polecenia i sprawdź ustawienia serwera proxy, za pomocą następujących poleceń:

    cat /etc/environment echo $że echo $https_proxy 

2. Jeśli dane wyjściowe z poprzedniego polecenia pokazują, że albo https_proxy ustawień zdefiniowanych, użyj jednej z następujących metod można odblokować główny element docelowy komunikacji z serwerem konfiguracji:
   
   - Pobierz [narzędzia PsExec](https://aka.ms/PsExec).
   - Narzędzie dostępu do kontekstu użytkownika systemu i sprawdzenie, czy adres serwera proxy jest skonfigurowany. 
   - Jeśli skonfigurowano serwer proxy, należy uruchomić program IE w kontekście użytkownika systemu za pomocą narzędzia PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Aby upewnić się, że główny serwer docelowy może komunikować się z serwerem konfiguracji:
  
     - Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć adres IP serwera głównego serwera docelowego za pośrednictwem serwera proxy.   
     Lub
     - Wyłącz serwer proxy na głównym serwerze docelowym. 


## <a name="next-steps"></a>Kolejne kroki
- Rozwiązywanie problemów z [połączenia RDP z maszyną Wirtualną Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Rozwiązywanie problemów z [połączenie SSH z maszyną Wirtualną systemu Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Jeśli potrzebujesz więcej pomocy, na następnie publikują zapytania [forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub pozostaw komentarz na końcu tego dokumentu. Mamy aktywnej społeczności użytkowników, które powinno być możliwe pomóc.
