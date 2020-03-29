---
title: Automatyzacja funkcji Dr udziału plików StorSimple za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano kroki i najlepsze rozwiązania dotyczące tworzenia rozwiązania do odzyskiwania po awarii dla udziałów plików hostowanych w magazynie Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 650798fdb884e6494990efb533335a1dd8b4d89f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875389"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatyczne rozwiązanie do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery dla udziałów plików hostowanych w StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple to rozwiązanie magazynu w chmurze hybrydowej, które rozwiązuje złożoność danych nieustrukturyzowanych często skojarzonych z udziałami plików. StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie warstw danych w lokalnej pamięci masowej i w chmurze. Zintegrowana ochrona danych z migawkami lokalnymi i w chmurze eliminuje potrzebę rozległej infrastruktury pamięci masowej.

[Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) to usługa oparta na platformie Azure, która zapewnia funkcje odzyskiwania po awarii (DR) przez aranżowanie replikacji, pracy awaryjnej i odzyskiwania maszyn wirtualnych. Usługa Azure Site Recovery obsługuje wiele technologii replikacji, które stale replikują, chronią i bezproblemowo przełączają się w tryb fail over maszyny wirtualne i aplikacje do chmur prywatnych/publicznych lub hostowanych.

Korzystając z usługi Azure Site Recovery, replikacji maszyn wirtualnych i funkcji migawek w chmurze StorSimple, można chronić pełne środowisko serwera plików. W przypadku zakłócenia można użyć jednego kliknięcia, aby przenieść swoje udziały plików do trybu online na platformie Azure w ciągu zaledwie kilku minut.

W tym dokumencie szczegółowo wyjaśniono, jak można utworzyć rozwiązanie do odzyskiwania po awarii dla udziałów plików hostowanych w magazynie StorSimple i wykonać planowane, nieplanowane i przetestować tryb failover przy użyciu planu odzyskiwania jednym kliknięciem. W istocie pokazuje, jak można zmodyfikować plan odzyskiwania w magazynie usługi Azure Site Recovery, aby włączyć StorSimple pracy awaryjnej podczas scenariuszy awarii. Ponadto opisano obsługiwane konfiguracje i wymagania wstępne. W tym dokumencie przyjęto założenie, że znasz podstawy architektury usługi Azure Site Recovery i StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Obsługiwane opcje wdrażania usługi Azure Site Recovery
Klienci mogą wdrażać serwery plików jako serwery fizyczne lub maszyny wirtualne (VM) działające w udrowionym środowisku Hyper-V lub VMware, a następnie tworzyć udziały plików z woluminów wyrytych z magazynu StorSimple. Usługa Azure Site Recovery może chronić wdrożenia fizyczne i wirtualne w lokacji dodatkowej lub na platformie Azure. Ten dokument zawiera szczegółowe informacje o rozwiązaniu odzyskiwania po awarii z platformą Azure jako lokacji odzyskiwania dla maszyny Wirtualnej serwera plików hostowanego w programie Hyper-V i z udziałami plików w magazynie StorSimple. Inne scenariusze, w których maszyna wirtualna serwera plików znajduje się na maszynie wirtualnej VMware lub komputerze fizycznym, mogą być implementowane w podobny sposób.

## <a name="prerequisites"></a>Wymagania wstępne
Implementowanie rozwiązania do odzyskiwania po awarii jednym kliknięciem, które używa usługi Azure Site Recovery dla udziałów plików hostowanych w magazynie StorSimple, ma następujące wymagania wstępne:

   - Lokalna maszyna wirtualna serwera plików systemu Windows Server 2012 R2 hostowana na urządzeniu typu Hyper-V lub VMware lub na komputerze fizycznym
   - Urządzenie magazynu storsimple lokalnie zarejestrowane w menedżerze usługi Azure StorSimple
   - Urządzenie StorSimple Cloud Appliance utworzone w menedżerze usługi Azure StorSimple. Urządzenie może być utrzymywane w stanie wyłączenia.
   - Udziały plików hostowane na woluminach skonfigurowanych na urządzeniu magazynującym StorSimple
   - [Magazyn usług Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) utworzony w ramach subskrypcji platformy Microsoft Azure

Ponadto jeśli platforma Azure jest twoją witryną odzyskiwania, uruchom [narzędzie oceny gotowości maszyny wirtualnej platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usługami azure site recovery.

Aby uniknąć problemów z opóźnieniami (które mogą spowodować wyższe koszty), upewnij się, że utworzysz urządzenie StorSimple Cloud Appliance, konto automatyzacji i konta magazynu w tym samym regionie.

## <a name="enable-dr-for-storsimple-file-shares"></a>Włącz udziały plików DR dla storsimple
Każdy składnik środowiska lokalnego musi być chroniony, aby umożliwić pełną replikację i odzyskiwanie. W tej sekcji opisano, jak:
    
   - Konfigurowanie usługi Active Directory i replikacji DNS (opcjonalnie)
   - Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
   - Włącz ochronę woluminów StorSimple
   - Konfigurowanie sieci

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurowanie usługi Active Directory i replikacji DNS (opcjonalnie)
Jeśli chcesz chronić maszyny z usługą Active Directory i DNS, tak aby były dostępne w witrynie odzyskiwania po awarii, należy je jawnie chronić (tak, aby serwery plików były dostępne po przejściu w tryb failover z uwierzytelnianiem). Istnieją dwie zalecane opcje oparte na złożoności środowiska lokalnego klienta.

#### <a name="option-1"></a>Opcja 1
Jeśli klient ma niewielką liczbę aplikacji, jeden kontroler domeny dla całej lokacji lokalnej i będzie w pełni znajdować się w trybie awarii w całej lokacji, zalecamy użycie replikacji usługi Azure Site Recovery w celu replikacji komputera kontrolera domeny do komputera pomocniczego (dotyczy to zarówno lokacji na platformie lokacji, jak i platformy Azure).

#### <a name="option-2"></a>Opcja 2
Jeśli klient ma dużą liczbę aplikacji, działa w lesie usługi Active Directory i będzie działać w trybie awaryjnym przez kilka aplikacji naraz, zaleca się skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

Instrukcje dotyczące udostępniania kontrolera domeny w witrynie odzyskiwania odzyskiwania po awarii w witrynie odzyskiwania po awarii można znaleźć w [rozwiązaniu Zautomatyzowane rozwiązania odzyskiwania po awarii dla usługi Active Directory i systemu DNS korzystającej z](../site-recovery/site-recovery-active-directory.md) usługi Azure Site Recovery. W pozostałej części tego dokumentu zakładamy, że kontroler domeny jest dostępny w witrynie odzyskiwania po awarii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
Ten krok wymaga przygotowania lokalnego środowiska serwera plików, utworzenia i przygotowania magazynu usługi Azure Site Recovery oraz włączenia ochrony plików maszyny Wirtualnej.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Aby przygotować środowisko lokalnego serwera plików
1. Ustaw **formant konta użytkownika** tak, aby **nigdy nie powiadamiał**. Jest to wymagane, aby można było używać skryptów automatyzacji platformy Azure do łączenia obiektów docelowych iSCSI po przełączeniu w błąd przez usługę Azure Site Recovery.
   
   1. Naciśnij klawisz Windows +Q i wyszukaj **system kontroli konta użytkownika**.  
   1. Wybierz **pozycję Zmień ustawienia kontroli konta użytkownika**.  
   1. Przeciągnij pasek na dół w kierunku **Nigdy nie powiadamiaj**.  
   1. Po wyświetleniu monitu kliknij **przycisk OK,** a następnie wybierz pozycję **Tak.**  
   
      ![Ustawienia kontroli konta użytkownika](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Zainstaluj agenta maszyn wirtualnych na każdej z maszyn wirtualnych serwera plików. Jest to wymagane, aby można było uruchamiać skrypty automatyzacji platformy Azure na maszynach wirtualnych po awarii.
   
   1. [Pobierz agenta](https://aka.ms/vmagentwin) do `C:\\Users\\<username>\\Downloads`.
   1. Otwórz program Windows PowerShell w trybie administratora (Uruchom jako administrator), a następnie wprowadź następujące polecenie, aby przejść do lokalizacji pobierania:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Nazwa pliku może ulec zmianie w zależności od wersji.
      
1. Kliknij przycisk **alej**.
1. Zaakceptuj **Warunki umowy,** a następnie kliknij przycisk **Dalej**.
1. Kliknij przycisk **Zakończ**.
1. Tworzenie udziałów plików przy użyciu woluminów wyrytych z magazynu StorSimple. Aby uzyskać więcej informacji, zobacz [Zarządzanie woluminami za pomocą usługi StorSimple Manager](storsimple-manage-volumes.md).
   
   1. Na lokalnych maszynach wirtualnych naciśnij klawisz Windows +Q i wyszukaj **iSCSI**.
   1. Wybierz **inicjatora iSCSI**.
   1. Wybierz kartę **Konfiguracja** i skopiuj nazwę inicjatora.
   1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
   1. Wybierz kartę **StorSimple,** a następnie wybierz usługę StorSimple Manager zawierającą urządzenie fizyczne.
   1. Utwórz kontenery woluminów, a następnie utwórz woluminy. (Woluminy te są przeznaczone dla udziałów plików na maszynach wirtualnych serwera plików). Skopiuj nazwę inicjatora i nadaj odpowiednią nazwę rekordom kontroli dostępu podczas tworzenia woluminów.
   1. Wybierz kartę **Konfiguruj** i zanotuj adres IP urządzenia.
   1. Na lokalnych maszynach wirtualnych ponownie przejdź do **inicjatora iSCSI** i wprowadź adres IP w sekcji Szybkie połączenie. Kliknij **przycisk Szybkie połączenie** (urządzenie powinno być teraz podłączone).
   1. Otwórz witrynę Azure portal i wybierz kartę **Auto Configure** **Woluminy i urządzenia.** Utworzony wolumin powinien być wyświetlany.
   1. W portalu wybierz kartę **Urządzenia,** a następnie wybierz pozycję **Utwórz nowe urządzenie wirtualne.** (To urządzenie wirtualne będzie używane w przypadku pracy awaryjnej). To nowe urządzenie wirtualne może być przechowywane w stanie offline, aby uniknąć dodatkowych kosztów. Aby przetraktować urządzenie wirtualne w trybie offline, przejdź do sekcji **Maszyny wirtualne** w portalu i zamknij ją.
   1. Wróć do lokalnych maszyn wirtualnych i otwórz okno Zarządzanie dyskami (naciśnij klawisz Windows + X i wybierz pozycję **Zarządzanie dyskami).**
   1. Zauważysz kilka dodatkowych dysków (w zależności od liczby utworzonych woluminów). Kliknij prawym przyciskiem myszy pierwszy, wybierz polecenie **Inicjuj dysk**i wybierz przycisk **OK**. Kliknij prawym przyciskiem myszy sekcję **Nieprzydzielone,** wybierz pozycję **Nowy wolumin prosty**, przypisz mu literę dysku i zakończ kreatora.
   1. Powtórz krok l dla wszystkich dysków. Teraz wszystkie dyski na **tym komputerze** można teraz zobaczyć w Eksploratorze Windows.
   1. Użyj roli Usługi plików i magazynowania, aby utworzyć udziały plików na tych woluminach.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Aby utworzyć i przygotować magazyn usługi Azure Site Recovery
Zapoznaj się z [dokumentacją usługi Azure Site Recovery,](../site-recovery/site-recovery-hyper-v-site-to-azure.md) aby rozpocząć korzystanie z usługi Azure Site Recovery przed ochroną maszyny Wirtualnej serwera plików.

#### <a name="to-enable-protection"></a>Aby włączyć ochronę
1. Odłącz obiekty docelowe iSCSI od lokalnych maszyn wirtualnych, które chcesz chronić za pomocą usługi Azure Site Recovery:
   
   1. Naciśnij klawisz Windows + Q i wyszukaj **iSCSI**.
   1. Wybierz **pozycję Konfigurowanie inicjatora iSCSI**.
   1. Odłącz urządzenie StorSimple, które zostało wcześniej podłączone. Alternatywnie można wyłączyć serwer plików na kilka minut po włączeniu ochrony.
      
   > [!NOTE]
   > Spowoduje to, że udziały plików będą tymczasowo niedostępne.
   
1. [Włącz ochronę maszyny wirtualnej](../site-recovery/site-recovery-hyper-v-site-to-azure.md) maszyny wirtualnej serwera plików z portalu azure site recovery.
1. Po rozpoczęciu synchronizacji początkowej można ponownie połączyć obiekt docelowy. Przejdź do inicjatora iSCSI, wybierz urządzenie StorSimple i kliknij przycisk **Połącz**.
1. Po zakończeniu synchronizacji i stan maszyny Wirtualnej jest **chroniony**, wybierz maszynę wirtualną, wybierz kartę **Konfiguruj** i odpowiednio zaktualizuj sieć maszyny Wirtualnej (jest to sieć, w skład których będą częścią maszyn wirtualnych, które nie powiodły się). Jeśli sieć nie jest chyliła się, oznacza to, że synchronizacja nadal trwa.

### <a name="enable-protection-of-storsimple-volumes"></a>Włącz ochronę woluminów StorSimple
Jeśli nie wybrano opcji **Włącz domyślną kopię zapasową dla tego woluminu** dla woluminów StorSimple, przejdź do **funkcji Zasady tworzenia kopii zapasowych** w usłudze StorSimple Manager i utwórz odpowiednią zasadę tworzenia kopii zapasowych dla wszystkich woluminów. Zaleca się ustawienie częstotliwości kopii zapasowych do celu punktu odzyskiwania (RPO), który chcesz zobaczyć dla aplikacji.

### <a name="configure-the-network"></a>Konfigurowanie sieci
W przypadku maszyny Wirtualnej serwera plików należy skonfigurować ustawienia sieciowe w usłudze Azure Site Recovery, tak aby sieci maszyn wirtualnych były podłączone do poprawnej sieci odzyskiwania po awarii po przełączeniu awaryjnym.

Można wybrać maszynę wirtualną na karcie **Elementy replikowane,** aby skonfigurować ustawienia sieciowe, jak pokazano na poniższej ilustracji.

![Obliczenia i sieć](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Można utworzyć plan odzyskiwania w ASR, aby zautomatyzować proces pracy awaryjnej udziałów plików. Jeśli wystąpi zakłócenie, można przenieść udziały plików w górę w ciągu kilku minut za pomocą jednego kliknięcia. Aby włączyć tę automatyzację, musisz konto automatyzacji platformy Azure.

#### <a name="to-create-an-automation-account"></a>Aby utworzyć konto automatyzacji
1. Przejdź do sekcji &gt; **Automatyzacja** portalu Azure.
1. Kliknij **+ Dodaj** przycisk, otwiera się poniżej bloku.
   
   ![Dodawanie konta usługi Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nazwa — wprowadzanie nowego konta automatyzacji
   - Subskrypcja — wybierz subskrypcję
   - Grupa zasobów — tworzenie nowych/wybieranie istniejącej grupy zasobów
   - Lokalizacja — wybierz lokalizację, zachowaj ją w tym samym regionie geograficznym/regionie, w którym utworzono storsimple urządzenia w chmurze i konta magazynu.
   - Utwórz konto Azure Run As — wybierz opcję **Tak.**
   
1. Przejdź do konta Automatyzacja, kliknij pozycję **Galeria przeglądania** **książek runbook,** &gt; aby zaimportować wszystkie wymagane elementy runbook do konta automatyzacji.
1. Dodaj następujące elementy runbook, znajdując tag **odzyskiwania po awarii** w galerii:
   
   - Oczyszczanie woluminów StorSimple po przejściu testów awaryjnych (TFO)
   - Kontenery woluminów StorProste po awarii
   - Instalowanie woluminów na urządzeniu StorSimple po przemijanym powiodu po awarii
   - Odinstalowywanie rozszerzenia skryptu niestandardowego na maszynie Wirtualnej platformy Azure
   - Uruchom urządzenie wirtualne StorSimple
   
      ![Przeglądaj galerię](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Opublikuj wszystkie skrypty, wybierając projekt runbook na koncie automatyzacji i kliknij pozycję **Edytuj** &gt; **publikowanie,** a następnie **tak** w komunikacie weryfikacyjnym. Po tym kroku karta **Runbooks** pojawi się w następujący sposób:
   
   ![Elementy Runbook](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na koncie automatyzacji kliknij pozycję **Zmienne** &gt; **Dodaj zmienną** i dodaj następujące zmienne. Można zaszyfrować te zasoby. Te zmienne są specyficzne dla planu odzyskiwania. Jeśli plan odzyskiwania, który zostanie utworzony w następnym kroku, nazwa jest TestPlan, a następnie zmienne powinny być TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName i tak dalej.

   - **BaseUrl:** Adres URL Menedżera zasobów dla chmury platformy Azure. Korzystaj z **get-azenvironment | Nazwa obiektu Select, polecenie cmdlet ResourceManagerUrl.**
   - _RecoveryPlanName_**-ResourceGroupName**: Grupa Menedżera zasobów, która ma zasób StorSimple.
   - _RecoveryPlanName_**-ManagerName:** Zasób StorSimple, który ma urządzenie StorSimple.
   - _RecoveryPlanName_**-DeviceName:** Urządzenie StorSimple, które musi zostać przejęte awaryjnie.
   - _RecoveryPlanName_**-DeviceIpAddress**: Adres IP urządzenia (można go znaleźć na karcie **Urządzenia** &gt; w sekcji StorSimple Device Manager sekcji **Ustawienia** &gt; **sieciowe** &gt; **ustawienia DNS).**
   - _RecoveryPlanName_**-VolumeContainers:** Oddzielony przecinkami ciąg kontenerów woluminów znajdujących się na urządzeniu, które muszą zostać przejęte awaryjnie; na przykład: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName:** Urządzenie w chmurze StorSimple, na którym kontenery mają zostać przejęte awaryjnie.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: Adres IP urządzenia docelowego (można go znaleźć &gt; na karcie **Grupa ustawień** maszyny &gt; **wirtualnej** **Sieć).**
   - _RecoveryPlanName_**-StorageAccountName:** Nazwa konta magazynu, w którym skrypt (który ma być uruchomiony na maszynie wirtualnej po awarii) będą przechowywane. Może to być dowolne konto magazynu, które ma trochę miejsca do tymczasowego przechowywania skryptu.
   - _RecoveryPlanName_**-StorageAccountKey:** Klucz dostępu dla powyższego konta magazynu.
   - _RecoveryPlanName_**-VMGUIDS:** Po ochronie maszyny Wirtualnej usługa Azure Site Recovery przypisuje każdej maszynie wirtualnej unikatowy identyfikator, który podaje szczegóły awarii maszyny wirtualnej. Aby uzyskać urządzenie wirtualne, wybierz kartę Usługi **odzyskiwania** i kliknij pozycję Właściwości **maszyn do grup ochrony** &gt; **Machines** &gt; **przed towarami** &gt; **chronionymi**. Jeśli masz wiele maszyn wirtualnych, dodaj identyfikatory GUID jako ciąg oddzielony przecinkami.

     Na przykład, jeśli nazwa planu odzyskiwania to fileServerpredayRP, po dodaniu wszystkich zasobów powinna pojawić się karta **Zmienne**, **Połączenia** i **Certyfikaty** w następujący sposób.

      ![Elementy zawartości](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Przekaż moduł Runbook serii StorSimple 8000 na swoje konto automatyzacji. Użyj poniższych kroków, aby dodać moduł:
   
   1. Otwórz program Powershell, utwórz nowy folder & zmień katalog na folder.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Pobierz nuget CLI w tym samym folderze w Kroku1.
      Różne wersje nuget.exe są dostępne na [nuget pliki do pobrania](https://www.nuget.org/downloads). Każdy link do pobrania wskazuje bezpośrednio na plik .exe, więc pamiętaj, aby kliknąć prawym przyciskiem myszy i zapisać plik na komputerze, zamiast uruchamiać go z przeglądarki.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Pobierz zależny sdk
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Utwórz moduł systemu runbook usługi Azure Automation do zarządzania urządzeniami storsimple z serii 8000. Poniższe polecenia można użyć do utworzenia pliku zip modułu automatyzacji.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Zaimportuj plik zip modułu usługi Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) utworzony w powyższym kroku. Można to zrobić, wybierając konto automatyzacji, kliknij pozycję **Moduły** w obszarze ZASOBY UDOSTĘPNIONE, a następnie kliknij przycisk **Dodaj moduł**.
   
   Po zaimportowaniu modułu serii StorSimple 8000 karta **Moduły** powinna być wyświetlana w następujący sposób:
   
      ![Moduły](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Przejdź do sekcji **Usługi odzyskiwania** i wybierz magazyn usługi Azure Site Recovery, który został utworzony wcześniej.
1. Wybierz opcję **Plany odzyskiwania (Odzyskiwanie witryny)** w grupie **Zarządzaj** i utwórz nowy plan odzyskiwania w następujący sposób:
   
   - Kliknij **przycisk + Odzyskaj plan,** otwiera się poniżej ostrza.
      
      ![Tworzenie planu odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Wprowadź nazwę planu odzyskiwania, wybierz pozycję Źródło, Wartość docelowa & wartości modelu wdrożenia.
   
   - Wybierz maszyny wirtualne z grupy ochrony, którą chcesz uwzględnić w planie odzyskiwania, i kliknij przycisk **OK.**
   
   - Wybierz plan odzyskiwania utworzony wcześniej, kliknij przycisk **Dostosuj,** aby otworzyć widok dostosowywania planu odzyskiwania.
   
   - Kliknij prawym przyciskiem myszy **pozycję Zamknięcie wszystkich grup** i kliknij polecenie Dodaj działanie przed **.**
   
   - Otwiera blok Wstaw akcję, wprowadź nazwę, wybierz opcję **Strona podstawowa** w opcji Gdzie uruchomić, wybierz pozycję Konto automatyzacji (w którym dodano elementy runbook), a następnie wybierz runbook **failover-StorSimple-Volume-Containers.**
   
   - Kliknij prawym przyciskiem myszy **grupę 1: Start** i kliknij pozycję **Dodaj elementy chronione,** a następnie wybierz maszyny wirtualne, które mają być chronione w planie odzyskiwania, i kliknij przycisk **Ok.** Opcjonalnie, jeśli są już wybrane maszyny wirtualne.
   
   - Kliknij prawym przyciskiem myszy **grupę 1: Start** i kliknij opcję **Opublikuj akcję,** a następnie dodaj wszystkie następujące skrypty:  
      
      - Runbook Start-StorSimple-Virtual-Appliance  
      - Runbook kontenerów woluminu woluminu w trybie fail over-StorSimple  
      - Element runbook instalacji po pracy awaryjnej  
      - Odinstaluj-niestandardowy skrypt-rozszerzenie runbook  
        
   - Dodaj akcję ręczną po powyższych 4 skryptach w tej samej **sekcji Grupa 1: Post-steps.** Ta akcja jest punktem, w którym można sprawdzić, czy wszystko działa poprawnie. Ta akcja musi zostać dodana tylko jako część testowego trybu failover (więc zaznacz tylko pole wyboru **Test failover).**
    
   - Po akcji ręcznej dodaj skrypt **Oczyszczania** przy użyciu tej samej procedury, która została użyta dla innych żyła. **Zapisz** plan odzyskiwania.
    
   > [!NOTE]
   > Podczas uruchamiania testu pracy awaryjnej, należy sprawdzić wszystko w kroku akcji ręcznej, ponieważ StorSimple woluminów, które zostały sklonowane na urządzeniu docelowym zostaną usunięte w ramach oczyszczania po wykonaniu akcji ręcznej.
       
      ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Wykonywanie testu trybu failover
Zapoznaj się z przewodnikiem towarzyszącym [rozwiązania odzyskiwania po awarii usługi Active Directory,](../site-recovery/site-recovery-active-directory.md) aby uzyskać informacje dotyczące specyfiki usługi Active Directory podczas pracy awaryjnej testu. Instalacja lokalna nie jest w ogóle zakłócona, gdy następuje przewijanie pracy awaryjnej testu. Woluminy StorSimple, które zostały dołączone do lokalnej maszyny Wirtualnej są klonowane do urządzenia StorSimple Cloud Appliance na platformie Azure. Maszyna wirtualna do celów testowych jest wychowywana na platformie Azure, a sklonowane woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-the-test-failover"></a>Aby wykonać test pracy awaryjnej
1. W witrynie Azure portal wybierz magazyn odzyskiwania witryny.
1. Kliknij plan odzyskiwania utworzony dla maszyny Wirtualnej serwera plików.
1. Kliknij **pozycję Testuj przewijanie awaryjne**.
1. Wybierz sieć wirtualną platformy Azure, z którą będą połączone maszyny wirtualne platformy Azure po zakończeniu pracy awaryjnej.
   
   ![Uruchamianie trybu failover](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Postęp można śledzić, klikając maszynę wirtualną, aby otworzyć jej właściwości, lub &gt; w **zadaniu testowania trybu failover** w **zadaniach odzyskiwania witryny zadaniami odzyskiwania witryny**zadaniami usługi zadania odzyskiwania **witryny zadaniami.** &gt;
1. Po zakończeniu pracy awaryjnej powinien być również widoczny, że replika &gt; komputera platformy Azure pojawi się w **maszynach wirtualnych**portalu Azure. Można wykonać sprawdzanie poprawności.
1. Po zakończeniu sprawdzania poprawności kliknij przycisk **Zakończ sprawdzanie poprawności**. Spowoduje to usunięcie woluminów StorSimple i zamknięcie urządzenia StorSimple Cloud Appliance.
1. Gdy już to zrobisz, kliknij przycisk Zapoznał się **z planem** odzyskiwania. W obszarze Uwagi zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover. Spowoduje to usunięcie maszyny wirtualnej, które zostały utworzone podczas pracy awaryjnej testu.

## <a name="perform-a-planned-failover"></a>Wykonywanie planowanej pracy awaryjnej
   Podczas planowanej pracy awaryjnej lokalnego serwera plików VM jest bezpiecznie zamykany i migawka kopii zapasowej w chmurze woluminów na urządzeniu StorSimple jest pobierana. Woluminy StorSimple są awaryjne na urządzeniu wirtualnym, replika maszyny Wirtualnej jest donoszona na platformie Azure, a woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-planned-failover"></a>Aby wykonać planowaną funkcję failover
1. W witrynie Azure **Recovery services** portal &gt; wybierz pozycję Plany odzyskiwania magazynu usług odzyskiwania **(Site Recovery)** &gt; **recoveryplan_name** utworzone dla maszyny Wirtualnej serwera plików.
1. Na bloku Plan odzyskiwania kliknij **pozycję Więcej** &gt; **planowanych trybu failover**.  

   ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na **bloku Potwierdź planowane trybu failover** wybierz lokalizacje źródłowe i docelowe i wybierz sieć docelową i kliknij ikonę wyboru ✓ aby rozpocząć proces pracy awaryjnej.
1. Po utworzeniu maszyn wirtualnych repliki są one w stanie oczekiwania zatwierdzenia. Kliknij **przycisk Zaśliwij,** aby zatwierdzić przetwórt pracy awaryjnej.
1. Po zakończeniu replikacji maszyny wirtualne uruchamiają się w lokalizacji pomocniczej.

## <a name="perform-a-failover"></a>Wykonywanie pracy awaryjnej
Podczas nieplanowanego trybu failover woluminy StorSimple są po awarii na urządzeniu wirtualnym, replika maszyny Wirtualnej zostanie doprowadzony na platformie Azure, a woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-failover"></a>Aby wykonać funkcję failover
1. W witrynie Azure **Recovery services** portal &gt; wybierz pozycję Plany odzyskiwania magazynu usług odzyskiwania **(Site Recovery)** &gt; **recoveryplan_name** utworzone dla maszyny Wirtualnej serwera plików.
1. Na bloku planu odzyskiwania kliknij **pozycję Więcej** &gt; **trybu failover**.  
1. Na **bloku Potwierdź powiększe,** wybierz lokalizacje źródłowe i docelowe.
1. Wybierz **pozycję Zamknij maszyny wirtualne i zsynchronizuj najnowsze dane,** aby określić, że usługa Site Recovery powinna próbować zamknąć chroniona maszyna wirtualna i zsynchronizować dane, tak aby najnowsza wersja danych została przejęta awaryjnie.
1. Po przewijeniu w trybie failover maszyny wirtualne są w stanie oczekiwania. Kliknij **przycisk Zaśliwij,** aby zatwierdzić przetwórt pracy awaryjnej.


## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii
Podczas powrotu po awarii storsimple kontenerów woluminu są awaryjne z powrotem do urządzenia fizycznego po wykonaniu kopii zapasowej.

#### <a name="to-perform-a-failback"></a>Aby wykonać powrót po awarii
1. W witrynie Azure **Recovery services** portal &gt; wybierz pozycję Plany odzyskiwania magazynu usług odzyskiwania **(Site Recovery)** &gt; **recoveryplan_name** utworzone dla maszyny Wirtualnej serwera plików.
1. Na bloku Plan odzyskiwania kliknij **pozycję Więcej** &gt; **planowanych trybu failover**.  
1. Wybierz lokalizacje źródłowe i docelowe, wybierz odpowiednie opcje synchronizacji danych i tworzenia maszyn wirtualnych.
1. Kliknij przycisk **OK,** aby rozpocząć proces powrotu po awarii.
   
   ![Uruchamianie powrotu po awarii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="capacity-planning-and-readiness-assessment"></a>Planowanie zdolności przepustowej i ocena gotowości
#### <a name="hyper-v-site"></a>Lokacja funkcji Hyper-V
Narzędzie [Do planowania pojemności użytkownika](https://www.microsoft.com/download/details.aspx?id=39057) służy do projektowania infrastruktury serwera, magazynu i sieci dla środowiska replik hyper-V.

#### <a name="azure"></a>Azure
Narzędzie oceny [gotowości maszyny wirtualnej platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) można uruchomić na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usługami azure site recovery. Narzędzie oceny gotowości sprawdza konfiguracje maszyn wirtualnych i ostrzega, gdy konfiguracje są niezgodne z platformą Azure. Na przykład wydaje ostrzeżenie, jeśli dysk C: jest większy niż 127 GB.

Planowanie zdolności produkcyjnych składa się z co najmniej dwóch ważnych procesów:

   - Mapowanie lokalnych maszyn wirtualnych funkcji Hyper V na rozmiary maszyn wirtualnych platformy Azure (takich jak A6, A7, A8 i A9).
   - Określanie wymaganej przepustowości Internetu.

## <a name="limitations"></a>Ograniczenia
- Obecnie tylko 1 urządzenie StorSimple można przejechać awaryjnie (do jednego urządzenia StorSimple Cloud Appliance). Scenariusz serwera plików, który obejmuje kilka urządzeń StorSimple nie jest jeszcze obsługiwany.
- Jeśli podczas włączania ochrony maszyny Wirtualnej pojawia się błąd, upewnij się, że obiekty docelowe iSCSI zostały rozłączone.
- Wszystkie kontenery woluminów, które zostały zgrupowane ze względu na zasady tworzenia kopii zapasowych obejmujące kontenery woluminów zostaną połączone w stan faila.
- Wszystkie woluminy w wybranych kontenerach woluminów zostaną przejęte awaryjnie.
- Woluminów, które sumują się do więcej niż 64 TB nie można pozycyjnie, ponieważ maksymalna pojemność jednego urządzenia StorSimple Cloud Appliance wynosi 64 TB.
- Jeśli planowane/nieplanowane trybu failover nie powiedzie się, a maszyny wirtualne są tworzone na platformie Azure, a następnie nie czyścić maszyn wirtualnych. Zamiast tego wykonaj powrót po awarii. Jeśli usuniesz maszyny wirtualne, nie można ponownie włączyć lokalnych maszyn wirtualnych.
- Po przełączeniu w tryb failover, jeśli nie są w stanie zobaczyć woluminów, przejdź do maszyn wirtualnych, otwórz zarządzanie dyskami, ponownie przeskanuj dyski, a następnie przewiń je do trybu online.
- W niektórych przypadkach litery dysków w lokacji odzyskiwania po awarii mogą być inne niż litery lokalne. W takim przypadku należy ręcznie rozwiązać problem po zakończeniu pracy awaryjnej.
- Limit czasu zadania pracy w trybie failover: StorSimple skrypt upotrzyma czas, jeśli praca awaryjna kontenerów woluminów zajmuje więcej czasu niż limit usługi Azure Site Recovery na skrypt (obecnie 120 minut).
- Limit czasu zadania kopii zapasowej: Limit czasu skryptu StorSimple, jeśli kopia zapasowa woluminów zajmuje więcej czasu niż limit odzyskiwania usługi Azure site recovery na skrypt (obecnie 120 minut).
   
  > [!IMPORTANT]
  > Uruchom kopię zapasową ręcznie z witryny Azure portal, a następnie ponownie uruchom plan odzyskiwania.
   
- Limit czasu zadania klonowania: Limit czasu skryptu StorSimple, jeśli klonowanie woluminów zajmuje więcej czasu niż limit odzyskiwania usługi Azure site recovery na skrypt (obecnie 120 minut).
- Błąd synchronizacji czasu: Skrypty StorSimple błędy, mówiąc, że kopie zapasowe nie powiodły się, mimo że kopia zapasowa zakończyła się pomyślnie w portalu. Możliwą przyczyną może być to, że storsimple urządzenia czas może być niezsynchronizowany z bieżącego czasu w strefie czasowej.
   
  > [!IMPORTANT]
  > Zsynchronizowanie czasu urządzenia z bieżącym czasem w strefie czasowej.
   
- Błąd pracy awaryjnej urządzenia: Skrypt StorSimple może zakończyć się niepowodzeniem, jeśli urządzenie jest w trybie failover, gdy jest uruchomiony plan odzyskiwania.
   
  > [!IMPORTANT]
  > Uruchom ponownie plan odzyskiwania po zakończeniu pracy awaryjnej urządzenia.


## <a name="summary"></a>Podsumowanie
Korzystając z usługi Azure Site Recovery, można utworzyć kompletny zautomatyzowany plan odzyskiwania po awarii dla maszyny Wirtualnej serwera plików, która ma udziały plików hostowane w magazynie StorSimple. Możesz zainicjować przewijanie awaryjne w ciągu kilku sekund z dowolnego miejsca w przypadku zakłócenia i uruchomić aplikację w ciągu kilku minut.
