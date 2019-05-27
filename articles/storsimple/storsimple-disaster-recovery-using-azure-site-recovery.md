---
title: Automatyzowanie fileshare StorSimple odzyskiwania po awarii przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano kroki i najlepsze rozwiązania dotyczące tworzenia rozwiązania do odzyskiwania po awarii, udziałów plików w serwisie Microsoft Azure StorSimple do magazynowania.
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
ms.author: vidarmsft
ms.openlocfilehash: 11ff7066019654ce2771bce242f3431d10da44ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150666"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Zautomatyzowane rozwiązanie odzyskiwania po awarii przy użyciu usługi Azure Site Recovery dla udziałów plików w serwisie StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd
Usługa Microsoft Azure StorSimple to hybrydowe rozwiązanie magazynu w chmurze odnoszący się do złożoności danych bez struktury, zwykle powiązanych ze udziałów plików. Usługa StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie warstwy danych przez Magazyn lokalny oraz Magazyn w chmurze. Zintegrowana ochrona danych z lokalnego oraz migawki w chmurze, eliminuje potrzebę stosowania sprawling infrastruktury magazynu.

[Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) to usługa oparta na platformie Azure, która oferuje możliwości odzyskiwania po awarii poprzez organizowanie replikacji, trybu failover i odzyskiwanie maszyn wirtualnych. Usługa Azure Site Recovery obsługuje wiele technologii replikacji stale replikować, ochronę i bezproblemowo w tryb failover maszyny wirtualne i aplikacje do publicznego i prywatnego lub hostowanej chmury.

Za pomocą usługi Azure Site Recovery, replikacja maszyny wirtualnej i możliwości migawki w chmurze StorSimple, można chronić środowisko serwera całego pliku. W przypadku przerwania działania można użyć jednym kliknięciem do trybu online udziałów plików na platformie Azure w ciągu kilku minut.

W tym dokumencie wyjaśniono szczegółowo, jak można utworzyć rozwiązanie odzyskiwania po awarii dla udziałów plików w serwisie magazynu StorSimple i wykonywać planowane, nieplanowane i testy trybu failover za pomocą planu odzyskiwania jednego kliknięcia. W zasadzie pokazuje, jak można zmodyfikować planu odzyskiwania w magazynie usługi Azure Site Recovery w taki sposób, aby włączyć StorSimple przejścia w tryb failover podczas scenariuszy po awarii. Ponadto opisano obsługiwane konfiguracje i wymagania wstępne. W tym dokumencie przyjęto założenie, że znasz podstawy architektury usługi Azure Site Recovery i urządzeniu StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Obsługiwane opcje wdrażania usługi Azure Site Recovery
Klienci mogą wdrażania serwerów plików jako serwerów fizycznych lub maszyn wirtualnych (VM) uruchomionej na funkcji Hyper-V lub VMware, a następnie utwórz udziały plików z woluminów używać poza magazynu StorSimple. Usługa Azure Site Recovery umożliwia ochronę wdrożeń fizycznych i wirtualnych do dodatkowej lokacji lub na platformie Azure. W tym dokumencie opisano szczegóły rozwiązania odzyskiwania po awarii z platformy Azure jako lokacji odzyskiwania dla serwera plików, które hostowana maszyna wirtualna funkcji Hyper-v i udziałów plików magazynu StorSimple. Podobnie można zaimplementować inne scenariusze, w których serwer plików maszyny Wirtualnej znajduje się na Maszynę wirtualną VMware lub komputera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne
Implementowanie rozwiązania odzyskiwania po awarii w jednym kliknięciem, korzystającą z usługi Azure Site Recovery dla udziałów plików magazynu StorSimple w serwisie ma następujące wymagania wstępne:

   - Lokalny serwer systemu Windows Server 2012 R2 pliku, który hostowana maszyna wirtualna funkcji Hyper-V lub VMware lub komputera fizycznego
   - Usługa StorSimple urządzenia lokalnego magazynu zarejestrowane w usłudze Azure StorSimple manager
   - Urządzenie StorSimple w chmurze utworzone w usłudze Azure StorSimple manager. Urządzenie może znajdować się w stanie wyłączenia.
   - Udziały plików hostowane na woluminach skonfigurowane na urządzeniu magazynu StorSimple
   - [Magazyn usługi w usłudze Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) utworzone w ramach subskrypcji Microsoft Azure

Ponadto, jeśli lokacji odzyskiwania jest platforma Azure, uruchom [narzędzie oceny gotowości maszyn wirtualnych platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usługi Azure Site Recovery services.

Aby uniknąć opóźnienia problemy (które może prowadzić do wyższych kosztów), upewnij się, tworzenia usługi Storsimple w chmurze, konto usługi automation i magazynu konta lub kont w tym samym regionie.

## <a name="enable-dr-for-storsimple-file-shares"></a>Włącz odzyskiwanie po awarii dla udziałów plików usługi StorSimple
Każdy składnik w lokalnym środowisku musi być chronione, aby włączyć pełną replikację i odzyskiwanie. W tej sekcji opisano sposób:
    
   - Konfigurowanie replikacji usługi Active Directory i DNS (opcjonalne)
   - Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
   - Włączanie ochrony woluminów StorSimple
   - Konfigurowanie sieci

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurowanie replikacji usługi Active Directory i DNS (opcjonalne)
Jeśli chcesz chronić maszyn uruchomione usługi Active Directory i DNS, tak aby były dostępne w lokacji odzyskiwania po awarii, musisz jawnie je chronić (tak, aby na serwerach plików są dostępne po włączeniu trybu failover przy użyciu uwierzytelniania). Dostępne są dwie opcje zalecane, zależnie od stopnia złożoności klienta w środowisku lokalnym.

#### <a name="option-1"></a>Opcja 1
Jeśli klient ma małej liczby aplikacji, pojedynczy kontroler domeny dla całej lokacji lokalnej i będzie można przejść w tryb failover całej lokacji, a firma Microsoft zaleca używanie replikacji usługi Azure Site Recovery można replikować maszyny kontrolera domeny do pomocniczego, a następnie Witryna (dotyczy to zarówno lokacja lokacja i lokacja na platformie Azure).

#### <a name="option-2"></a>Opcja 2
Jeśli klient ma dużą liczbę aplikacji, jest uruchomiona w lesie usługi Active Directory i będzie można przejść w tryb failover kilka aplikacji w czasie, a następnie zaleca się skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (dodatkowej lokacji lub na platformie Azure).

Zapoznaj się [rozwiązanie automatycznego odzyskiwania po awarii dla usługi Active Directory i DNS przy użyciu usługi Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) instrukcje podczas udostępniania kontrolera domeny w lokacji odzyskiwania po awarii. Dla pozostałej w tym dokumencie przyjęto założenie, że kontroler domeny jest dostępny w lokacji odzyskiwania po awarii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
Ten krok wymaga, aby przygotować środowisko serwera plików w środowisku lokalnym, Utwórz i przygotuj magazyn usługi Azure Site Recovery i włączenia ochrony plików maszyny wirtualnej.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Aby przygotować środowisko serwera plików w środowisku lokalnym
1. Ustaw **Kontrola konta użytkownika** do **nigdy nie Powiadamiaj**. Jest to wymagane, tak aby skryptów usługi Azure automation umożliwia łączenie obiektów docelowych iSCSI, po włączeniu trybu failover przez usługę Azure Site Recovery.
   
   1. Naciśnij klawisze Windows + Q, a następnie wyszukaj **UAC**.  
   1. Wybierz **ustawienia kontroli konta użytkownika Zmień**.  
   1. Przeciągnij pasek w dół w kierunku **nigdy nie Powiadamiaj**.  
   1. Kliknij przycisk **OK** , a następnie wybierz **tak** po wyświetleniu monitu.  
   
      ![Ustawienia kontroli konta użytkownika](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Zainstaluj agenta maszyny Wirtualnej na każdym serwerze plików maszyn wirtualnych. Jest to wymagane, aby można było uruchomić skrypty automatyzacji platformy Azure w trybie maszyn wirtualnych.
   
   1. [Pobierz agenta](https://aka.ms/vmagentwin) do `C:\\Users\\<username>\\Downloads`.
   1. Otwórz program Windows PowerShell w trybie administratora (Uruchom jako Administrator), a następnie wprowadź następujące polecenie, aby przejść do lokalizacji pobierania:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Nazwa pliku, mogą ulec zmianie w zależności od wersji.
      
1. Kliknij przycisk **Dalej**.
1. Zaakceptuj **postanowienia Umowy** a następnie kliknij przycisk **dalej**.
1. Kliknij przycisk **Zakończ**.
1. Tworzenie udziałów plików, przy użyciu woluminów używać poza magazynu StorSimple. Aby uzyskać więcej informacji, zobacz [usługi StorSimple Manager umożliwia zarządzanie woluminami](storsimple-manage-volumes.md).
   
   1. Na maszynach wirtualnych w środowisku lokalnym, naciśnij klawisze Windows + Q, a następnie wyszukaj **iSCSI**.
   1. Wybierz **inicjatora iSCSI**.
   1. Wybierz **konfiguracji** kartę i skopiuj nazwę inicjatora.
   1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
   1. Wybierz **StorSimple** , a następnie wybierz pozycję usługi StorSimple Manager, który zawiera urządzenia fizycznego.
   1. Tworzenie kontenerów woluminów, a następnie utwórz woluminy. (Te woluminy są udziałów plików na serwerze plików maszyn wirtualnych). Skopiuj nazwę inicjatora i nadaj nazwę odpowiednie dla rekordów kontroli dostępu, podczas tworzenia woluminów.
   1. Wybierz **Konfiguruj** kartę i zanotuj adres IP urządzenia w dół.
   1. Na maszynach wirtualnych w środowisku lokalnym, przejdź do **inicjatora iSCSI** ponownie i wprowadź adres IP w sekcji szybkie połączenie. Kliknij przycisk **szybkie połączenie** (urządzenie powinno być teraz połączone).
   1. Otwórz w witrynie Azure portal i wybierz **woluminów i urządzeń** kartę. Kliknij przycisk **automatycznie skonfigurować**. Powinna zostać wyświetlona woluminu, który został utworzony.
   1. W portalu, wybierz **urządzeń** , a następnie wybierz pozycję **utworzyć nowe urządzenie wirtualne.** (Tego urządzenia wirtualnego będzie używane, jeśli wystąpi przejścia w tryb failover). To nowe urządzenie wirtualne mogą znajdować się w trybie offline, aby uniknąć dodatkowych kosztów. Aby móc urządzenia wirtualnego w trybie offline, przejdź do **maszyn wirtualnych** sekcji w portalu i zamknij go.
   1. Wróć do lokalnych maszyn wirtualnych i Otwórz przystawkę Zarządzanie dyskami (naciśnij klawisze Windows + X, a następnie wybierz pozycję **przystawki Zarządzanie dyskami**).
   1. Można zauważyć pewne dodatkowe dyski (w zależności od liczby woluminów, które zostały utworzone). Kliknij prawym przyciskiem myszy pierwszy z nich, wybierz opcję **Zainicjuj dysk**i wybierz **OK**. Kliknij prawym przyciskiem myszy **Unallocated** zaznacz **nowy wolumin prosty**, a następnie przypisać jej literę dysku i Zakończ pracę kreatora.
   1. Powtórz krok l dla wszystkich dysków. Wszystkie dyski będą teraz widoczne na **ten komputer** w Eksploratorze Windows.
   1. Do tworzenia udziałów plików na tych woluminach za pomocą roli usługi plików i magazynowania.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Aby utworzyć i przygotuj magazyn usługi Azure Site Recovery
Zapoznaj się [dokumentacji usługi Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) rozpocząć pracę z usługą Azure Site Recovery przed rozpoczęciem ochrony serwera plików maszyny Wirtualnej.

#### <a name="to-enable-protection"></a>Aby włączyć ochronę
1. Zakończ połączenie elementy docelowe iSCSI z lokalnych maszyn wirtualnych, które chcesz chronić za pomocą usługi Azure Site Recovery:
   
   1. Naciśnij klawisze Windows + Q, a następnie wyszukaj **iSCSI**.
   1. Wybierz **Konfigurowanie inicjatora iSCSI**.
   1. Odłącz urządzenia StorSimple, którą wcześniej połączenia. Alternatywnie, można wyłączyć serwera plików przez kilka minut, podczas włączania ochrony.
      
   > [!NOTE]
   > Spowoduje to udziałów plików jest tymczasowo niedostępna.
   
1. [Włączyć ochronę maszyny wirtualnej](../site-recovery/site-recovery-hyper-v-site-to-azure.md) serwera plików maszyny Wirtualnej z portalu usługi Azure Site Recovery.
1. Po rozpoczęciu synchronizacji początkowej, należy ponownie element docelowy. Przejdź do inicjatora iSCSI, wybierz urządzenia StorSimple i kliknij polecenie **Connect**.
1. Po ukończeniu synchronizacji i stan maszyny Wirtualnej jest **chronione**, wybierz maszynę Wirtualną, wybierz **Konfiguruj** kartę, a następnie odpowiednio zaktualizować sieci maszyny Wirtualnej (jest to sieć, nieudane za pośrednictwem Maszyny wirtualne będą częścią). Jeśli sieć nie jest wyświetlane, oznacza to, czy synchronizacji nadal trwa.

### <a name="enable-protection-of-storsimple-volumes"></a>Włączanie ochrony woluminów StorSimple
Jeśli nie wybrano **Włącz domyślnego tworzenia kopii zapasowej dla tego woluminu** opcja dla woluminów StorSimple, przejdź do **zasady tworzenia kopii zapasowych** Menedżer StorSimple usługi, a następnie utwórz odpowiednie zasady kopii zapasowych dla wszystkie woluminy. Firma Microsoft zaleca, Ustaw częstotliwość wykonywania kopii zapasowych na cel punktu odzyskiwania (RPO), który chcesz wyświetlić dla aplikacji.

### <a name="configure-the-network"></a>Konfigurowanie sieci
Dla serwera plików maszyny Wirtualnej skonfigurować ustawienia sieciowe w usłudze Azure Site Recovery, tak aby sieci maszyny Wirtualnej są dołączone do odpowiedniej sieci odzyskiwania po awarii po włączeniu trybu failover.

Możesz wybrać maszynę Wirtualną w **zreplikowane elementy** kartę, aby skonfigurować ustawienia sieci, jak pokazano na poniższej ilustracji.

![Obliczenia i sieć](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
W usłudze ASR w celu zautomatyzowania procesu pracy awaryjnej udziałów plików, można utworzyć planu odzyskiwania. W przypadku przerwy w działaniu można można przywołać udziałów plików w ciągu kilku minut za pomocą jednego kliknięcia. Aby włączyć taką automatyzację, konieczne będzie konto usługi Azure automation.

#### <a name="to-create-an-automation-account"></a>Aby utworzyć konto usługi Automation
1. Przejdź do witryny Azure portal &gt; **automatyzacji** sekcji.
1. Kliknij przycisk **+ Dodaj** przycisku otwiera poniżej bloku.
   
   ![Dodawanie konta usługi Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nazwa — wprowadzanie nowego konta usługi automation
   - Subskrypcja — Wybierz subskrypcję
   - Zasób grupy — Utwórz nowy/wybierz istniejącą grupę zasobów
   - Lokalizacja — wybierz lokalizację i zapewnić jej w tym samym/region geograficzny w którym zostały utworzone urządzenie StorSimple w chmurze i kont magazynu.
   - Utwórz konto Uruchom jako platformy Azure — wybierz **tak** opcji.
   
1. Przejdź do konta usługi Automation, kliknij przycisk **elementów Runbook** &gt; **Przeglądaj galerię** Aby zaimportować wszystkie wymagane elementy runbook do konta usługi automation.
1. Dodaj następujące elementy runbook, wyszukując **odzyskiwania po awarii** tagu w galerii:
   
   - Czyszczenie woluminów StorSimple po pracy awaryjnej testu (suma)
   - Kontenery woluminów StorSimple trybu failover
   - Po włączeniu trybu failover należy instalować woluminy na urządzeniu StorSimple
   - Odinstaluj rozszerzenie skryptu niestandardowego maszyny Wirtualnej platformy Azure
   - Uruchom urządzenie wirtualne StorSimple
   
      ![Przeglądaj galerię](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Opublikuj wszystkie skrypty, wybierając element runbook w ramach konta usługi automation, a następnie kliknij przycisk **Edytuj** &gt; **Publikuj** i następnie **tak** w komunikacie weryfikacyjnym. Po wykonaniu tego kroku **elementów Runbook** pojawi się zakładka w następujący sposób:
   
   ![Elementy Runbook](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na koncie usługi automation kliknij **zmienne** &gt; **Dodaj zmienną** i dodaj następujące zmienne. Można zaszyfrować te zasoby. Te zmienne są określonego planu odzyskiwania. Plan odzyskiwania, który zostanie utworzony w następnym kroku, nazwa będzie plan testu, a następnie zmiennych powinny być StorSimRegKey plan testu, AzureSubscriptionName plan testu i tak dalej.

   - **BaseUrl**: Adres url usługi Resource Manager dla chmury platformy Azure. Pobieranie przy użyciu **Get AzEnvironment | Nazwa Select-Object, ResourceManagerUrl** polecenia cmdlet.
   - *RecoveryPlanName***-ResourceGroupName**: Grupa usługi Resource Manager, zawierającej zasób usługi StorSimple.
   - * RecoveryPlanName ***- ManagerName**: Zasób usługi StorSimple, który ma urządzenia StorSimple.
   - *RecoveryPlanName***-DeviceName**: Urządzenie StorSimple, który ma być przełączone w tryb failover.
   - *RecoveryPlanName***-DeviceIpAddress**: Adres IP urządzenia (ten znajduje się w **urządzeń** kartę sekcji Menedżer urządzeń StorSimple &gt; **ustawienia** &gt; **sieci** &gt; **Ustawienia DNS** grupy).
   - *RecoveryPlanName***-VolumeContainers**: Ciąg rozdzielony przecinkami, kontenerów woluminów na urządzeniu, które muszą zostać przełączone w tryb failover; na przykład: volcon1 volcon2, volcon3.
   - *RecoveryPlanName***-TargetDeviceName**: Urządzenie StorSimple w chmurze na którym kontenery znajdują się w tryb failover.
   - *RecoveryPlanName***-TargetDeviceIpAddress**: Adres IP urządzenia docelowego (ten znajduje się w **maszyny wirtualnej** sekcji &gt; **ustawienia** grupy &gt; **sieć** karty).
   - *RecoveryPlanName***-StorageAccountName**: Nazwa konta magazynu, w którym przechowywane będą skryptu (który ma do uruchamiania na nieudane przez maszynę Wirtualną). Może to być konto magazynu, które ma miejsce do przechowywania skryptu tymczasowo.
   - *RecoveryPlanName***-StorageAccountKey**: Klucz dostępu dla powyższego konta magazynu.
   - * RecoveryPlanName ***- VMGUIDS**: Podczas ochrony maszyn wirtualnych, usługi Azure Site Recovery przypisuje każda maszyna wirtualna Unikatowy identyfikator, który zawiera szczegółowe informacje o trybie tryb failover maszyny Wirtualnej. Aby uzyskać VMGUID, wybierz **usługi Recovery Services** kartę, a następnie kliknij przycisk **chronionego elementu** &gt; **grup ochrony** &gt;  **Maszyny** &gt; **właściwości**. Jeśli masz wiele maszyn wirtualnych, Dodaj identyfikator GUID w faktycznej jako ciąg rozdzielonych przecinkami.

     Na przykład, jeśli nazwa planu odzyskiwania jest fileServerpredayRP wówczas **zmienne**, **połączeń** i **certyfikaty** karta powinna wyglądać następująco, po dodaniu wszystkie zasoby.

      ![Zasoby](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Przekazywanie modułu Runbook serii StorSimple 8000 na koncie usługi Automation. Użyj poniższych kroków, aby dodać moduł:
   
   1. Otwórz program powershell, Utwórz nowy folder i zmień katalog na folder.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Pobierz interfejs wiersza polecenia nuget, w tym samym folderze, w Step1.
      Różne wersje programu nuget.exe są dostępne na [nuget pobiera](https://www.nuget.org/downloads). Każdy link pobierania wskazuje bezpośrednio na pliku .exe, więc Pamiętaj kliknij prawym przyciskiem myszy, a następnie zapisz plik na komputer zamiast uruchomienie jej w przeglądarce.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Pobierz zestaw SDK zależne
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Utwórz moduł usługi Azure Automation Runbook do zarządzania urządzeniami StorSimple 8000 Series. Użyj poniższych poleceń, aby utworzyć plik zip modułu usługi Automation.
         
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
         
   1. Importuj plik zip modułu usługi Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) utworzoną w kroku. To może odbywać się przez wybranie konta usługi Automation, kliknij przycisk **modułów** w obszarze udostępnionych zasobów, a następnie kliknij przycisk **Dodaj moduł**.
   
   Po zaimportowaniu modułu serii StorSimple 8000 **modułów** karta powinna wyglądać następująco:
   
      ![Moduły](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Przejdź do **usługi Recovery Services** sekcji i wybierz utworzony wcześniej magazyn usługi Azure Site Recovery.
1. Wybierz **plany odzyskiwania (Usługa Site Recovery)** opcję **Zarządzaj** grupy i Utwórz nowy plan odzyskiwania w następujący sposób:
   
   - Kliknij przycisk **+ planu odzyskiwania** przycisku otwiera poniżej bloku.
      
      ![Tworzenie planu odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Wprowadź nazwę planu odzyskiwania, wybierz źródło & docelowa wdrażania wartości modelu.
   
   - Wybierz maszyny wirtualne z grupy ochrony, który chcesz uwzględnić w planie odzyskiwania, a następnie kliknij przycisk **OK** przycisku.
   
   - Wybierz wcześniej utworzony plan odzyskiwania, kliknij przycisk **Dostosuj** przycisk, aby otworzyć widoku dostosowywania planu odzyskiwania.
   
   - Kliknij prawym przyciskiem myszy **zamykanie wszystkich grup** i kliknij przycisk **Dodaj akcję wstępnie**.
   
   - Otwiera Wstaw blok akcję, wprowadź nazwę, wybierz **strona podstawowa** opcji podczas uruchamiania opcji, wybierz konto usługi Automation (w którym dodano elementy runbook), a następnie wybierz **trybu Failover — StorSimple — — kontenery woluminów**  elementu runbook.
   
   - Kliknij prawym przyciskiem myszy **Grupa 1: Rozpocznij** i kliknij przycisk **Dodaj chronione elementy** opcji, a następnie wybierz maszyny wirtualne, które mają być chronione w planie odzyskiwania i kliknij **Ok** przycisku. Opcjonalnie, jeśli jest on już wybrane maszyny wirtualne.
   
   - Kliknij prawym przyciskiem myszy **Grupa 1: Rozpocznij** i kliknij przycisk **wpis akcji** opcji, a następnie dodaj następujące skrypty:  
      
      - Start--urządzenie wirtualne StorSimple — elementu runbook  
      - Niepowodzenie elementu runbook przez — StorSimple — — kontenery woluminów  
      - Element runbook instalacji woluminów po failover  
      - Odinstaluj — — rozszerzenia niestandardowego skryptu — elementu runbook  
        
   - Dodawanie akcji ręcznej po powyżej 4 skrypty w tym samym **Grupa 1: Kroki po operacji** sekcji. Ta akcja jest punkt, w którym możesz sprawdzić, czy wszystko działa poprawnie. Ta akcja musi zostać dodany tylko jako część testu trybu failover (dlatego tylko wybrane **testowy tryb Failover** wyboru).
    
   - Po wykonaniu akcji ręcznej, Dodaj **oczyszczania** skryptu, używając tej samej procedury, który był używany przez inne elementy runbook. **Zapisz** planu odzyskiwania.
    
   > [!NOTE]
   > Podczas uruchamiania testu trybu failover, należy sprawdzić wszystko na etapie akcji ręcznej, ponieważ woluminów StorSimple, które ma zostać sklonowany na urządzeniu docelowym zostaną usunięte w ramach procesu czyszczenia po wykonaniu akcji ręcznej.
       
      ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Wykonaj test trybu failover
Zapoznaj się [rozwiązanie odzyskiwania po awarii Active Directory](../site-recovery/site-recovery-active-directory.md) Przewodnik uzupełniający po dla określonego zagadnienia dotyczące usługi Active Directory podczas testowania trybu failover. Instalator lokalnej nie zakłócony na wszystkich, po wystąpieniu test trybu failover. Woluminy StorSimple, które zostały dołączone do lokalnej maszyny Wirtualnej są klonowane do urządzenia StorSimple w chmurze na platformie Azure. Maszyny Wirtualnej do celów testowych zostaną podwyższone na platformie Azure i sklonowany woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-the-test-failover"></a>Aby wykonać test trybu failover
1. W witrynie Azure portal wybierz magazyn usługi Site Recovery.
1. Kliknij plan odzyskiwania utworzone dla serwera plików maszyny Wirtualnej.
1. Kliknij przycisk **testowanie trybu Failover**.
1. Wybierz sieć wirtualną platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu przejścia do trybu failover.
   
   ![Uruchom tryb Failover](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postępy, klikając na maszynę Wirtualną i otwierając jej właściwości lub na **zadanie trybu failover testowe** w nazwie magazynu &gt; **zadania** &gt; **zadania usługi Site Recovery**.
1. Po zakończeniu pracy w trybie failover, należy także mogli zobaczyć repliki Azure maszyny są wyświetlane w witrynie Azure portal &gt; **maszyn wirtualnych**. Możesz wykonać swojej operacji sprawdzania poprawności.
1. Po operacje sprawdzania poprawności będzie gotowe, kliknij przycisk **pełne sprawdzanie poprawności**. Spowoduje to usunięcie woluminów StorSimple i zamknij urządzenie StorSimple w chmurze.
1. Gdy wszystko będzie gotowe, kliknij przycisk **wyczyść test pracy awaryjnej** w planie odzyskiwania. Uwagi dotyczące rejestrowania i zapisz wszelkie obserwacje związane z testowym trybem failover. Spowoduje to usunięcie maszyny wirtualnej, które zostały utworzone podczas testowania trybu failover.

## <a name="perform-a-planned-failover"></a>Realizacja planowanego trybu failover
   Podczas planowanego trybu failover lokalnego pliku serwera, z którym maszyna wirtualna jest zamknięcie i w chmurze, w których wykonywana jest migawka kopii zapasowych woluminów na urządzeniu StorSimple. Woluminy StorSimple są przełączone w tryb failover urządzenia wirtualnego, maszyna wirtualna repliki jest przenoszone na platformie Azure, a woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-planned-failover"></a>Aby wykonać planowanego trybu failover
1. W witrynie Azure portal wybierz **usługi Recovery services** magazynu &gt; **planów odzyskiwania (Usługa Site Recovery)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
1. W bloku planu odzyskiwania, kliknij **więcej** &gt; **zaplanowanym powrocie po awarii**.

   ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Na **potwierdzić planowanego trybu Failover** bloku Wybierz źródła i docelowych lokalizacji i wybierz docelową sieć, a następnie kliknij ikonę wyboru ✓ można uruchomić procesu pracy awaryjnej.
1. Po utworzeniu maszyn wirtualnych repliki, są one w zatwierdzeniu, stan oczekiwania. Kliknij przycisk **zatwierdzenia** do zatwierdzenia pracy w trybie failover.
1. Po zakończeniu replikacji maszyn wirtualnych jest uruchomiona w dodatkowej lokalizacji.

## <a name="perform-a-failover"></a>Przejścia w tryb failover
Podczas nieplanowany tryb failover woluminów StorSimple są przełączone w tryb failover urządzenia wirtualnego maszyny Wirtualnej repliki zostanie wyświetlona na platformie Azure i woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-failover"></a>Do pracy w trybie Failover
1. W witrynie Azure portal wybierz **usługi Recovery services** magazynu &gt; **planów odzyskiwania (Usługa Site Recovery)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
1. W bloku planu odzyskiwania, kliknij **więcej** &gt; **trybu Failover**.
1. Na **Potwierdź tryb Failover** bloku wybierz źródło i lokalizacje docelowe.
1. Wybierz **zamknąć maszyny wirtualne i zsynchronizuj najnowsze dane** do określenia, czy usługa Site Recovery należy spróbować zamknąć chronionej maszyny wirtualnej i synchronizacji danych, dzięki czemu najnowsza wersja danych zostaną przełączone w tryb failover.
1. Po przejściu w tryb failover maszyny wirtualne znajdują się w zatwierdzeniu, stan oczekiwania. Kliknij przycisk **zatwierdzenia** do zatwierdzenia pracy w trybie failover.


## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii
Podczas powrotu po awarii kontenery woluminów StorSimple są Failover powrót do urządzenia fizycznego, po jest wykonywana kopia zapasowa.

#### <a name="to-perform-a-failback"></a>Aby wykonać powrotu po awarii
1. W witrynie Azure portal wybierz **usługi Recovery services** magazynu &gt; **planów odzyskiwania (Usługa Site Recovery)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
1. W bloku planu odzyskiwania, kliknij **więcej** &gt; **planowanego trybu Failover**.
1. Wybierz lokalizacje źródłowe i docelowe, wybierz odpowiednią synchronizację danych i opcje tworzenia maszyny Wirtualnej.
1. Kliknij przycisk **OK** przycisk, aby rozpocząć proces powrotu po awarii.
   
   ![Uruchom powrót po awarii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="capacity-planning-and-readiness-assessment"></a>Pojemności planowanie i przygotowanie oceny
#### <a name="hyper-v-site"></a>Lokacja funkcji Hyper-V
Użyj [narzędzia planisty wydajności użytkownika](https://www.microsoft.com/download/details.aspx?id=39057) do projektowania serwera, magazynowania i infrastruktury sieci w środowisku funkcji Hyper-V replica.

#### <a name="azure"></a>Azure
Możesz uruchomić [narzędzie oceny gotowości maszyn wirtualnych platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usług Azure Site Recovery. Narzędzie oceny gotowości sprawdza, czy konfiguracje maszyn wirtualnych i ostrzega, gdy konfiguracje są niezgodne z platformą Azure. Na przykład go generuje ostrzeżenie, jeśli dysk C: jest większy niż 127 GB.

Planowanie pojemności składa się z co najmniej dwie ważne procesy:

   - Mapowanie lokalnych maszyn wirtualnych funkcji Hyper-V do rozmiarów maszyn wirtualnych platformy Azure (na przykład A6 A7, A8 i A9).
   - Określanie wymaganej przepustowości Internetu.

## <a name="limitations"></a>Ograniczenia
- Obecnie tylko 1 urządzenia StorSimple można przełączone w tryb failover (pojedynczego urządzenia StorSimple w chmurze). Scenariusz serwera plików, który obejmuje kilka urządzeń StorSimple nie jest jeszcze obsługiwana.
- Jeśli wystąpi błąd podczas włączania ochrony dla maszyny Wirtualnej, upewnij się, że zostały odłączone obiektów docelowych iSCSI.
- Wszystkie kontenery woluminów, które zostały zgrupowane razem ze względu na zasady tworzenia kopii zapasowych, obejmujące kontenerów woluminów nie powiedzie się za pośrednictwem ze sobą.
- Wszystkie woluminy w kontenery woluminów, które zostały wybrane zostaną przełączone w tryb failover.
- Woluminy, które dodać do więcej niż 64 TB, który nie może za pośrednictwem, ponieważ maksymalna pojemność jednej urządzenia StorSimple w chmurze jest 64 TB.
- Jeśli planowane lub nieplanowane trybu failover nie powiedzie się i maszyny wirtualne są tworzone na platformie Azure, następnie nie usuwaj maszyny wirtualne. Zamiast tego podczas powrotu po awarii. Jeśli usuniesz maszyny wirtualne następnie lokalnych maszyn wirtualnych nie można włączyć ponownie.
- Po przejściu w tryb failover Jeśli nie jest możliwe zobaczyć woluminy, przejdź do maszyn wirtualnych, otwórz przystawkę Zarządzanie dyskami, ponownie przeskanować dyski, a następnie przełącz je online.
- W niektórych przypadkach litery dysku w lokacji odzyskiwania po awarii może być różni się od litery w środowisku lokalnym. W takiej sytuacji należy ręcznie rozwiązać ten problem, po zakończeniu pracy w trybie failover.
- Limit czasu zadania trybu failover: Skrypt StorSimple przekroczy limit czasu, jeśli tryb failover kontenery woluminów zajmuje więcej czasu niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut).
- Limit czasu zadania tworzenia kopii zapasowej: Limit czasu skryptu StorSimple, jeśli tworzenie kopii zapasowych woluminów zajmuje więcej czasu niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut).
   
  > [!IMPORTANT]
  > Ręcznie uruchomić tworzenie kopii zapasowej w witrynie Azure portal, a następnie ponownie uruchom planu odzyskiwania.
   
- Sklonuj limit czasu zadania: Limit czasu skryptu StorSimple, jeśli Klonowanie woluminów zajmuje więcej czasu niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut).
- Błąd synchronizacji: StorSimple skrypty występuje informujący o tym, że kopie zapasowe zakończyły się niepowodzeniem, mimo że kopii zapasowej zakończy się w portalu. Możliwą przyczyną tego może być, że urządzenie StorSimple czas może być zsynchronizowany z aktualną godziną w strefie czasowej.
   
  > [!IMPORTANT]
  > Synchronizacja czasu urządzenia z aktualną godziną w strefie czasowej.
   
- Błąd trybu failover urządzenia: Skrypt StorSimple może zakończyć się niepowodzeniem, jeśli istnieje urządzenie w tryb failover, gdy działa w planie odzyskiwania.
   
  > [!IMPORTANT]
  > Po zakończeniu pracy w trybie failover urządzenia, należy ponownie uruchomić planu odzyskiwania.


## <a name="summary"></a>Podsumowanie
Za pomocą usługi Azure Site Recovery, możesz utworzyć plan odzyskiwania pełne, automatyczne po awarii dla serwera plików maszyny Wirtualnej po udostępnieniu udziałów plików w serwisie magazynu StorSimple. Możesz zainicjować trybu failover w ciągu kilku sekund z dowolnego miejsca w przypadku przerwy w działaniu i Pobierz aplikację w ciągu kilku minut.
