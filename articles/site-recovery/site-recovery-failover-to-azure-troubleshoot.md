---
title: Rozwiązywanie problemów z błędami pracy awaryjnej platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych błędów podczas pracy w przypadku awarii platformy Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 54e44a12f593d2074eefe5b2ff890863db3199f7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478956"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Rozwiązywanie problemów z błędami podczas awarii maszyny wirtualnej VMware lub komputera fizycznego na platformie Azure

Może pojawić się jeden z następujących błędów podczas wykonywania pracy awaryjnej maszyny wirtualnej na platformie Azure. Aby rozwiązać problem, należy użyć opisanych kroków dla każdego warunku błędu.

## <a name="failover-failed-with-error-id-28031"></a>Przewija się w błąd z identyfikatorem błędu 28031

Usługa Site Recovery nie może utworzyć maszyny wirtualnej awaryjnej w trybie failed na platformie Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającej ilości przydziału do utworzenia maszyny wirtualnej: możesz sprawdzić dostępny przydział, przechodząc do subskrypcji -> Użycie + przydziały. Można otworzyć [nowe żądanie pomocy technicznej,](https://aka.ms/getazuresupport) aby zwiększyć przydział.

* Próbujesz przewinąć maszyn wirtualnych trybu failover o różnych rodzinach w tym samym zestawie dostępności. Upewnij się, że wybierzesz rodzinę tego samego rozmiaru dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmień rozmiar, przechodząc do ustawień obliczeniowych i sieci maszyny wirtualnej, a następnie ponów próbę przegrupowania w tryb failover.

* Istnieje zasada dotycząca subskrypcji, która uniemożliwia utworzenie maszyny wirtualnej. Zmień zasady, aby umożliwić utworzenie maszyny wirtualnej, a następnie ponów próbę przeczekania w trybie failover.

## <a name="failover-failed-with-error-id-28092"></a>Przewija się w błąd z identyfikatorem błędu 28092

Usługa Site Recovery nie może utworzyć interfejsu sieciowego dla maszyny wirtualnej, która uległa awarii. Upewnij się, że masz wystarczającą ilość przydziału do tworzenia interfejsów sieciowych w subskrypcji. Możesz sprawdzić dostępny przydział, przechodząc do subskrypcji -> Użycie + przydziały. Można otworzyć [nowe żądanie pomocy technicznej,](https://aka.ms/getazuresupport) aby zwiększyć przydział. Jeśli masz wystarczający przydział, może to być sporadyczny problem, spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzał nawet po ponownych próbach, pozostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Przewija się w błąd z identyfikatorem błędu 70038

Usługa Site Recovery nie może utworzyć maszyny wirtualnej w trybie failed na platformie Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualna, która jest wymagana do utworzenia maszyny wirtualnej nie istnieje. Utwórz sieć wirtualną zgodnie z ustawieniami obliczeniowymi i sieciowymi maszyny wirtualnej lub zmodyfikuj to ustawienie do sieci wirtualnej, która już istnieje, a następnie ponów próbę przeżyj proces pracy awaryjnej.

## <a name="failover-failed-with-error-id-170010"></a>Przewija się w błąd z identyfikatorem błędu 170010

Usługa Site Recovery nie może utworzyć maszyny wirtualnej awaryjnej w trybie failed na platformie Azure. Może się zdarzyć, ponieważ wewnętrzne działanie nawodnienia nie powiodło się dla lokalnej maszyny wirtualnej.

Aby wywołać dowolną maszynę na platformie Azure, środowisko platformy Azure wymaga, aby niektóre sterowniki były w stanie rozruchu, a usługi, takie jak DHCP, były w stanie automatycznego uruchamiania. W ten sposób aktywność nawodnienia w czasie pracy awaryjnej konwertuje typ **uruchamiania sterowników atapi, intelide, storflt, vmbus i storvsc** do rozruchu. Konwertuje również typ uruchamiania kilku usług, takich jak DHCP, na autostart. To działanie może zakończyć się niepowodzeniem z powodu problemów specyficznych dla środowiska. 

Aby ręcznie zmienić typ uruchamiania sterowników systemu **operacyjnego gościa systemu Windows,** wykonaj poniższe czynności:

1. [Pobierz](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) skrypt bez nawodnienia i uruchom go w następujący sposób. Ten skrypt sprawdza, czy maszyna wirtualna wymaga nawodnienia.

    `.\Script-no-hydration.ps1`

    Daje następujący wynik, jeśli wymagane jest nawodnienie:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    W przypadku, gdy maszyna wirtualna spełnia wymagania dotyczące braku nawodnienia, skrypt da wynik "Ten system spełnia wymagania dotyczące braku nawodnienia". W takim przypadku wszystkie sterowniki i usługi są w stanie, zgodnie z wymaganiami platformy Azure i nawodnienie na maszynie Wirtualnej nie jest wymagane.

2. Uruchom skrypt zestawu bez nawodnienia w następujący sposób, jeśli maszyna wirtualna nie spełnia wymagań dotyczących braku nawodnienia.

    `.\Script-no-hydration.ps1 -set`
    
    Spowoduje to konwersję typu uruchamiania sterowników i da wynik jak poniżej:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nie można połączyć/RDP/SSH do maszyny wirtualnej awaryjnej z powodu wyszarzone go przycisku Połącz na maszynie wirtualnej

Jeśli przycisk **Połącz** na maszynie wirtualnej po awarii na platformie Azure jest wyszarzony i nie masz połączenia z platformą Azure za pośrednictwem połączenia sieci VPN z trasą ekspresową lub siecią WEB lokacja lokacja,

1. Przejdź do aplikacji Sieć **maszyny** > **wirtualnej**, kliknij nazwę wymaganego interfejsu sieciowego.  ![interfejs sieciowy](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Przejdź do **konfiguracji ip,** a następnie kliknij pole nazwy wymaganej konfiguracji IP. ![Konfiguracje IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Aby włączyć publiczny adres IP, kliknij **pozycję Włącz**. ![Włącz adres IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kliknij **pozycję Skonfiguruj wymagane ustawienia** > **Utwórz nowy**. ![Tworzenie nowych](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Wprowadź nazwę adresu publicznego, wybierz domyślne opcje dla **jednostki SKU** i **przypisania,** a następnie kliknij przycisk **OK**.
6. Teraz, aby zapisać wprowadzone zmiany, kliknij przycisk **Zapisz**.
7. Zamknij panele i przejdź do sekcji **Przegląd** maszyny wirtualnej, aby połączyć/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nie można połączyć/RDP/SSH - dostępny przycisk VM Connect

Jeśli przycisk **Połącz** na maszynie wirtualnej awaryjnej na platformie Azure jest dostępny (nie wyszarzony), sprawdź **diagnostykę rozruchu** na maszynie wirtualnej i sprawdź, czy nie ma błędów wymienionych w [tym artykule](../virtual-machines/windows/boot-diagnostics.md).

1. Jeśli maszyna wirtualna nie została uruchomiona, spróbuj wykonać niepowodzenie do starszego punktu odzyskiwania.
2. Jeśli aplikacja wewnątrz maszyny wirtualnej nie jest w górę, spróbuj w trybie awaryjnym do punktu odzyskiwania spójne z aplikacją.
3. Jeśli maszyna wirtualna jest przyłączona do domeny, upewnij się, że kontroler domeny działa dokładnie. Można to zrobić, wykonując poniższe kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci.

    b.  Upewnij się, że jest w stanie dołączyć do tej samej domeny, w której oczekuje się, że maszyna wirtualna po awarii pojawi się.

    d. Jeśli kontroler domeny **nie** działa dokładnie, spróbuj zalogować się do maszyny wirtualnej po awarii przy użyciu konta administratora lokalnego.
4. Jeśli używasz niestandardowego serwera DNS, upewnij się, że jest on osiągalny. Można to zrobić, wykonując poniższe kroki:

    a. Utwórz nową maszynę wirtualną w tej samej sieci i

    b. Sprawdź, czy maszyna wirtualna jest w stanie wykonać rozpoznawanie nazw przy użyciu niestandardowego serwera DNS

>[!Note]
>Włączenie dowolnego ustawienia innego niż diagnostyka rozruchowa wymagałoby zainstalowania agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej przed trybem failover

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Nie można otworzyć konsoli szeregowej po przewijaniu awaryjnym komputera opartego na interfejsie UEFI na platformie Azure

Jeśli możesz połączyć się z urządzeniem przy użyciu protokołu RDP, ale nie możesz otworzyć konsoli szeregowej, wykonaj poniższe czynności:

* Jeśli system operacyjny komputera to Red Hat lub Oracle Linux 7.*/8.0, uruchom następujące polecenie na maszynie wirtualnej platformy Azure trybu failover z uprawnieniami administratora. Uruchom ponownie maszynę wirtualną po poleceniu.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Jeśli system operacyjny komputera jest CentOS 7.*, uruchom następujące polecenie na maszynie wirtualnej platformy Azure trybu failover z uprawnieniami głównymi. Uruchom ponownie maszynę wirtualną po poleceniu.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Komunikat nieoczekiwanego zamknięcia systemu (identyfikator zdarzenia 6008)

Podczas uruchamiania maszyny Wirtualnej systemu Windows po pracy awaryjnej, jeśli zostanie wyświetlony komunikat nieoczekiwanego zamknięcia na odzyskanej maszynie wirtualnej, oznacza to, że stan zamknięcia maszyny Wirtualnej nie został przechwycony w punkcie odzyskiwania używanym do pracy awaryjnej. Dzieje się tak, gdy można odzyskać do punktu, gdy maszyna wirtualna nie została całkowicie zamknięta.

Zwykle nie jest to powodem do niepokoju i zwykle mogą być ignorowane dla nieplanowanych pracy awaryjnej. Jeśli planowane jest przejście w błąd, upewnij się, że maszyna wirtualna jest poprawnie zamknięty przed przeminiętym w błąd i zapewnić wystarczającą ilość czasu na oczekujące dane replikacji lokalnie, które mają być wysyłane do platformy Azure. Następnie użyj **opcji Najnowsze** na [ekranie trybu failover,](site-recovery-failover.md#run-a-failover) aby wszystkie oczekujące dane na platformie Azure były przetwarzane w punkcie odzyskiwania, który jest następnie używany do pracy awaryjnej maszyny Wirtualnej.

## <a name="unable-to-select-the-datastore"></a>Nie można wybrać magazynu danych

Ten problem jest wskazany, gdy nie można wyświetlić magazynu danych na platformie Azure portalu podczas próby ponownego przeceniania maszyny wirtualnej, która doświadczyła pracy awaryjnej. Dzieje się tak, ponieważ główny obiekt docelowy nie jest rozpoznawany jako maszyna wirtualna w obszarze vCenters dodany do usługi Azure Site Recovery.

Aby uzyskać więcej informacji na temat ponownego przeczesywania maszyny wirtualnej, zobacz [Ponowne przekazywanie i przywracanie komputerów awaryjnych do lokacji lokalnej po przeczesaniu pracy awaryjnej na platformie Azure.](vmware-azure-reprotect.md)

Aby rozwiązać ten problem:

Ręcznie utwórz główny cel w centrum vCenter, który zarządza komputerem źródłowym. Magazyn danych będzie dostępny po następnej operacji odnajdywania i odświeżania sieci szkieletowej vCenter.

> [!Note]
> 
> Operacje odnajdywania i odświeżania sieci szkieletowej mogą potrwać do 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Rejestracja docelowych wzorca linuksa z CS kończy się niepowodzeniem z błędem TLS 35 

Rejestracja obiektu docelowego wzorca usługi Azure Site Recovery master z serwerem konfiguracji kończy się niepowodzeniem z powodu włączenia uwierzytelnionego serwera proxy w głównym obiekcie docelowym. 
 
Ten błąd jest wskazywany przez następujące ciągi w dzienniku instalacji: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Aby rozwiązać ten problem:
 
1. Na maszynie wirtualnej serwera konfiguracji otwórz wiersz polecenia i sprawdź ustawienia serwera proxy za pomocą następujących poleceń:

    echa /etc/środowiska $http_proxy echo $https_proxy 

2. Jeśli na wyjściu poprzednich poleceń wykaże się, że zdefiniowane są ustawienia http_proxy lub https_proxy, użyj jednej z następujących metod, aby odblokować komunikację głównego obiektu docelowego z serwerem konfiguracji:
   
   - Pobierz [narzędzie PsExec](https://aka.ms/PsExec).
   - Użyj tego narzędzia, aby uzyskać dostęp do kontekstu użytkownika systemu i określić, czy adres serwera proxy jest skonfigurowany. 
   - Jeśli serwer proxy jest skonfigurowany, otwórz IE w kontekście użytkownika systemu za pomocą narzędzia PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Aby upewnić się, że główny serwer docelowy może komunikować się z serwerem konfiguracji:
  
     - Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć adres IP serwera głównego docelowego za pośrednictwem serwera proxy.   
     Lub
     - Wyłącz serwer proxy na serwerze głównym. 


## <a name="next-steps"></a>Następne kroki
- Rozwiązywanie problemów [z połączeniem protokołu RDP z maszyną wirtualną systemu Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Rozwiązywanie problemów [z połączeniem SSH z maszyną wirtualną z systemem Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Jeśli potrzebujesz więcej pomocy, opublikuj zapytanie na [forum odzyskiwania witryny](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub zostaw komentarz na końcu tego dokumentu. Mamy aktywną społeczność, która powinna być w stanie ci pomóc.
