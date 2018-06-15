---
title: Automatyzowanie StorSimple udziału plików odzyskiwania po awarii z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano kroki i najlepsze rozwiązania dotyczące tworzenia rozwiązanie odzyskiwania po awarii dla udziałów plików hostowane na magazynu Microsoft Azure StorSimple.
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
ms.openlocfilehash: e60cc83f49f9e0d0f878d7f49333f1be34ce54a6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2017
ms.locfileid: "23890880"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Zautomatyzowane rozwiązanie odzyskiwania po awarii dla udziałów plików hostowane na StorSimple przy użyciu usługi Azure Site Recovery
## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple jest rozwiązania magazynu hybrydowego chmury, którego dotyczy złożoności danych niestrukturalnych zwykle powiązanych ze udziałów plików. StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego, i automatycznie warstwy danych w lokalnej pamięci masowej i magazynu w chmurze. Zintegrowana ochrony danych z lokalnego i w chmurze migawek, eliminuje potrzebę stosowania sprawling infrastruktury magazynu.

[Usługa Azure Site Recovery](../site-recovery/site-recovery-overview.md) to usługa bazujących na platformie Azure, która zapewnia możliwości odzyskiwanie po awarii poprzez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych. Usługa Azure Site Recovery obsługuje wiele technologii replikacji spójnie replikacji, ochronę i bezproblemowo w tryb failover maszyn wirtualnych i aplikacji do publicznego i prywatnego lub hostowanej chmury.

Za pomocą usługi Azure Site Recovery, replikację maszyny wirtualnej i tworzenie migawek chmury StorSimple, można chronić środowisko serwera całego pliku. W przypadku przerw w działaniu można użyć jednego kliknięcia można wyświetlić z udziałów plików w tryb online na platformie Azure w ciągu kilku minut.

Tego dokumentu szczegółowo opisano sposób można utworzyć rozwiązanie odzyskiwania po awarii dla Twojego udziały plików hostowane na magazynie StorSimple i wykonać planowane, nieplanowane i testy trybu failover przy użyciu planu odzyskiwania jednym kliknięciem. W zasadzie pokazuje, jak można zmodyfikować planu odzyskiwania w magazynie usługi Azure Site Recovery, aby włączyć tryb failover StorSimple w scenariuszach po awarii. Ponadto opisano obsługiwane konfiguracje i wymagania wstępne. Tym dokumencie przyjęto założenie, że czytelnik zna podstawowe informacje dotyczące architektury usługi Azure Site Recovery i StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Obsługiwane opcje wdrażania usługi Azure Site Recovery
Klienci mogą wdrażania serwerów plików jako serwerów fizycznych lub maszynach wirtualnych (VM) działające w funkcji Hyper-V lub programu VMware, a następnie utwórz udziałów plików z woluminów używać poza magazynu StorSimple. Usługa Azure Site Recovery może chronić fizycznych i wirtualnych wdrożeń do dodatkowej lokacji lub na platformie Azure. W tym dokumencie opisano szczegóły rozwiązanie odzyskiwania po awarii z platformy Azure jako lokacja odzyskiwania dla serwera plików, maszyna wirtualna hostowana w ramach funkcji Hyper-V i udziałów plików w magazynie StorSimple. Podobnie można stosować inne scenariusze, w których serwer plików maszyny Wirtualnej jest na maszynie Wirtualnej VMware lub komputera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne
Implementowanie rozwiązania odzyskiwania po awarii jednego kliknięcia, które używa usługi Azure Site Recovery udziały plików hostowane na magazynie StorSimple ma następujące wymagania wstępne:

* Lokalnego serwera z systemem Windows Server 2012 R2 pliku, maszyna wirtualna hostowana w funkcji Hyper-V lub programu VMware lub komputera fizycznego
* StorSimple urządzenia lokalnie zarejestrowana w usłudze Menedżer Azure StorSimple
* Urządzenia StorSimple chmury, utworzone w Menedżerze Azure StorSimple. Urządzenia mogą znajdować się w stanie wyłączenia.
* Udziały plików hostowane na woluminach skonfigurowane na urządzeniu magazynu StorSimple
* [Magazynu usługi Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) utworzone w ramach subskrypcji Microsoft Azure

Ponadto Azure w przypadku odzyskiwania lokacji, uruchom [narzędzie oceny gotowości maszyny wirtualnej Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi Azure i usługi Azure Site Recovery services.

Aby uniknąć problemów (co może spowodować zwiększenie kosztów), upewnij się, że utworzenie Twojego urządzenia chmury StorSimple, konto usługi Automatyzacja i magazynu opóźnienia konta lub kont w tym samym regionie.

## <a name="enable-dr-for-storsimple-file-shares"></a>Włącz odzyskiwania po awarii dla udziałów plików StorSimple
Każdego składnika środowiska lokalnego musi być chroniony, aby umożliwić dokończenie replikacji i odzyskiwania. W tej sekcji opisano sposób:

* Konfigurowanie replikacji usługi Active Directory i DNS (opcjonalny)
* Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
* Włączanie ochrony woluminów StorSimple
* Konfigurowanie sieci

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurowanie replikacji usługi Active Directory i DNS (opcjonalny)
Jeśli chcesz chronić na komputerach z systemem usługi Active Directory i DNS, tak aby były dostępne w lokacji odzyskiwania po awarii, musisz jawnie je chronić (tak, aby serwery plików są dostępne po z uwierzytelnianiem w tryb failover). Dostępne są dwie opcje zalecane, zależnie od stopnia złożoności klienta w środowisku lokalnym.

#### <a name="option-1"></a>Opcja 1
Jeśli klient ma małej liczby aplikacji, jednego kontrolera domeny dla całej lokalnej lokacji i awarii w całej lokacji, wówczas zalecamy replikację do dodatkowej maszyny kontrolera domeny przy użyciu replikacji usługi Azure Site Recovery Witryna (dotyczy to zarówno do lokacji i lokacji do platformy Azure).

#### <a name="option-2"></a>Opcja 2
Jeśli klient ma dużą liczbę aplikacji, działa lasu usługi Active Directory i spowoduje niepowodzenie przez kilka aplikacji w czasie, a następnie zalecamy skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (dodatkowej lokacji lub na platformie Azure).

Zapoznaj się [rozwiązania automatycznego odzyskiwania po awarii dla usługi Active Directory i DNS za pomocą usługi Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) instrukcje podczas udostępniania kontrolera domeny w lokacji odzyskiwania po awarii. W pozostałej części tego dokumentu przyjęto założenie, że kontroler domeny jest dostępny w witrynie odzyskiwania po awarii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Użyj usługi Azure Site Recovery, aby włączyć ochronę maszyny Wirtualnej serwera plików
Ten krok wymaga, aby przygotować środowisko serwera lokalnego pliku, Utwórz przygotowanie magazynie usługi Azure Site Recovery i włącz ochronę plików maszyny wirtualnej.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Aby przygotować środowisko serwera plików lokalnych
1. Ustaw **Kontrola konta użytkownika** do **nigdy nie Powiadamiaj**. Jest to wymagane, dzięki czemu skryptów automatyzacji Azure umożliwia łączenie obiektów docelowych iSCSI po trybu failover za pomocą usługi Azure Site Recovery.

   1. Naciśnij klawisze Windows + Q i wyszukaj **UAC**.
   2. Wybierz **ustawienia kontroli konta użytkownika zmiany**.
   3. Przeciągnij pasek do dołu **nigdy nie Powiadamiaj**.
   4. Kliknij przycisk **OK** , a następnie wybierz **tak** po wyświetleniu monitu.

      ![Ustawienia kontroli konta użytkownika](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. Zainstaluj agenta maszyny Wirtualnej na każdym serwerze plików maszyn wirtualnych. Jest to wymagane, co umożliwia uruchamianie skryptów automatyzacji Azure na nieudane przez maszyny wirtualne.

   1. [Pobierz agenta](http://aka.ms/vmagentwin) do `C:\\Users\\<username>\\Downloads`.
   2. Otwórz program Windows PowerShell w trybie administratora (Uruchom jako Administrator), a następnie wprowadź następujące polecenie, aby przejść do lokalizacji pobierania:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > Nazwa pliku mogą ulec zmianie w zależności od wersji.
      >
      >
3. Kliknij przycisk **Dalej**.
4. Zaakceptuj **postanowienia Umowy** , a następnie kliknij przycisk **dalej**.
5. Kliknij przycisk **Zakończ**.
6. Tworzenie udziałów plików za pomocą woluminów używać poza magazynu StorSimple. Aby uzyskać więcej informacji, zobacz [usługi Menedżer StorSimple umożliwia zarządzanie woluminami](storsimple-manage-volumes.md).

   1. Na lokalnych maszyn wirtualnych, naciśnij klawisze Windows + Q, a następnie wyszukaj **iSCSI**.
   2. Wybierz **inicjatora iSCSI**.
   3. Wybierz **konfiguracji** karcie i skopiuj Nazwa inicjatora.
   4. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
   5. Wybierz **StorSimple** karcie, a następnie wybierz usługę Menedżer StorSimple, który zawiera urządzenia fizycznego.
   6. Utwórz wolumin kontenerów, a następnie utwórz woluminy. (Te woluminy dotyczą udziałów plików na serwerze plików maszyn wirtualnych). Skopiuj Nazwa inicjatora i zapewniają odpowiednią nazwę rekordy kontroli dostępu, podczas tworzenia woluminu.
   7. Wybierz **Konfiguruj** kartę i zanotuj adres IP urządzenia, w dół.
   8. Na lokalnych maszyn wirtualnych, przejdź do **inicjatora iSCSI** ponownie, a następnie wprowadź adres IP w sekcji szybkie połączenie. Kliknij przycisk **szybkie połączenie** (urządzenie powinno być teraz połączone).
   9. Otwórz Azure portalu i wybierz pozycję **woluminów i urządzeń** kartę. Kliknij przycisk **automatycznie skonfigurować**. Wolumin, który został utworzony, powinny być wyświetlane.
   10. W portalu, wybierz **urządzeń** karcie, a następnie wybierz **utworzyć nowe urządzenie wirtualne.** (Będzie można używać tego urządzenia wirtualnego, jeśli do pracy awaryjnej). Tym nowym urządzeniu wirtualnym można przechowywać w trybie offline, aby uniknąć dodatkowych kosztów. Urządzenie wirtualne przełączenia do trybu offline, przejdź do **maszyn wirtualnych** sekcji w portalu i go zamknąć.
   11. Wróć do maszyn wirtualnych lokalnie i Otwórz przystawkę Zarządzanie dyskami (naciśnij klawisz systemu Windows + X i wybierz **Zarządzanie dyskami**).
   12. Można zauważyć pewne dodatkowe dysków (w zależności od liczby woluminów, które utworzono). Kliknij prawym przyciskiem myszy pierwszy z nich, zaznacz **Inicjowanie dysku**i wybierz **OK**. Kliknij prawym przyciskiem myszy **Unallocated** zaznacz **nowy wolumin prosty**, a następnie przypisać jej literę dysku i zakończyć pracę kreatora.
   13. Powtórz krok l dla wszystkich dysków. Wszystkie dyski będą teraz widoczne na **ten komputer** w Eksploratorze Windows.
   14. Do tworzenia udziałów plików na tych woluminach za pomocą roli usługi plików i magazynowania.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Do tworzenia i przygotowywania magazynie usługi Azure Site Recovery
Zapoznaj się [dokumentacji usługi Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) rozpocząć pracę z usługą Azure Site Recovery przed rozpoczęciem ochrony serwera plików maszyny Wirtualnej.

#### <a name="to-enable-protection"></a>Aby włączyć ochronę
1. Rozłączanie docelowych iSCSI z lokalnych maszyn wirtualnych, które chcesz chronić za pomocą usługi Azure Site Recovery:

   1. Naciśnij klawisze Windows + Q i wyszukaj **iSCSI**.
   2. Wybierz **Konfigurowanie inicjatora iSCSI**.
   3. Odłącz urządzenia StorSimple, którą wcześniej połączenia. Alternatywnie możesz wyłączyć serwer plików, kilka minut po włączeniu ochrony.

   > [!NOTE]
   > Spowoduje to udziałów plików jest tymczasowo niedostępna.
   >
   >
2. [Włącz ochronę maszyny wirtualnej](../site-recovery/site-recovery-hyper-v-site-to-azure.md) serwera plików maszyny Wirtualnej w portalu usługi Azure Site Recovery.
3. Po początkowej synchronizacji można ponownie element docelowy. Przejdź do inicjatora iSCSI, wybierz urządzenie StorSimple, a następnie kliknij przycisk **Connect**.
4. Po ukończeniu synchronizacji i stan maszyny Wirtualnej jest **chronione**, wybierz maszynę Wirtualną, wybierz **Konfiguruj** , a następnie odpowiednio zaktualizować sieci maszyny wirtualnej (to jest sieć który nieudane przez Wirtualne będą częścią). Sieć nie widać, oznacza, że synchronizacja nadal trwa.

### <a name="enable-protection-of-storsimple-volumes"></a>Włączanie ochrony woluminów StorSimple
Jeśli nie wybrano **włączyć domyślnego tworzenia kopii zapasowej dla tego woluminu** opcja dla woluminów StorSimple, przejdź do **zasad tworzenia kopii zapasowych** Menedżer StorSimple usługi, a następnie utwórz odpowiednie zasady tworzenia kopii zapasowej wszystkie woluminy. Firma Microsoft zaleca, aby ustawić częstotliwość tworzenia kopii zapasowych na cel punktu odzyskiwania (RPO), który chcesz wyświetlić dla aplikacji.

### <a name="configure-the-network"></a>Konfigurowanie sieci
Dla serwera plików maszyny Wirtualnej Skonfiguruj ustawienia sieciowe w usłudze Azure Site Recovery, aby wszystkie sieci maszyny Wirtualnej są dołączone do odpowiedniej sieci DR po pracy awaryjnej.

Możesz wybrać maszynę Wirtualną w **elementy replikowane** kartę, aby skonfigurować ustawienia sieciowe, jak pokazano na poniższej ilustracji.

![Obliczenia i sieć](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
W usłudze ASR do automatyzacji procesu pracy awaryjnej udziałów plików, można utworzyć planu odzyskiwania. W przypadku zakłóceń przełączeniem udziały plików w ciągu kilku minut za pomocą jednego kliknięcia. Aby włączyć ten automatyzacji, konieczne będzie konto usługi Automatyzacja Azure.

#### <a name="to-create-an-automation-account"></a>Aby utworzyć konto usługi Automatyzacja
1. Przejdź do portalu Azure &gt; **automatyzacji** sekcji.
2. Kliknij przycisk **+ Dodaj** przycisku, otwiera poniżej bloku.

   ![Dodawanie konta usługi Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Name — wprowadź nowe konto automatyzacji
   * Subskrypcja — Wybierz subskrypcję
   * Resource group - Utwórz nowe/wybierz istniejącą grupę zasobów
   * Lokalizacja — wybierz lokalizację, przechowuj go w tym samym/region geograficzny w której utworzono urządzenia chmury StorSimple i konta magazynu.
   * Utwórz konto Uruchom jako platformy Azure — wybierz **tak** opcji.

3. Przejdź do konta automatyzacji, kliknij przycisk **Runbook** &gt; **galerii przeglądania** Aby zaimportować wszystkie wymagane elementy runbook do konta automatyzacji.
4. Dodaj następujące elementy runbook znajdując **odzyskiwania po awarii** tag w galerii:

   * Wyczyść woluminów StorSimple po Test pracy awaryjnej (TFO)
   * Kontenery woluminów StorSimple trybu failover
   * Zainstalować woluminów na urządzeniu StorSimple po pracy awaryjnej
   * Odinstaluj niestandardowego rozszerzenia skryptu na maszynie Wirtualnej Azure
   * Uruchom urządzenia wirtualnego StorSimple

     ![Przeglądaj galerii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. Publikuj wszystkie skrypty wybierając konto automatyzacji elementu runbook, a następnie kliknij przycisk **Edytuj** &gt; **publikowania** , a następnie **tak** w komunikacie weryfikacyjnym. Po wykonaniu tego kroku **elementów Runbook** karta pojawi się w następujący sposób:

    ![Elementy Runbook](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. W ramach konta automatyzacji, kliknij przycisk **zmienne** &gt; **dodać zmienną** i dodaj następujące zmienne. Można zaszyfrować te zasoby. Te zmienne są określone planu odzyskiwania. Jeśli plan odzyskiwania, co spowoduje utworzenie w następnym kroku nazwa jest TestPlan, a następnie zmiennych powinny być StorSimRegKey TestPlan, AzureSubscriptionName TestPlan i tak dalej.

   * **BaseUrl**: adres url Menedżera zasobów Azure chmury. Pobieranie przy użyciu **Get-AzureRmEnvironment | Nazwa Select-Object, ResourceManagerUrl** polecenia cmdlet.
   * *RecoveryPlanName***- ResourceGroupName**: grupy Resource Manager, która ma zasobów StorSimple.
   * *RecoveryPlanName***- ManagerName**: StorSimple zasobu, który urządzenia StorSimple.
   * *RecoveryPlanName***- DeviceName**: urządzenia StorSimple, który ma w tryb pracy awaryjnej.
   * *RecoveryPlanName***- DeviceIpAddress**: adres IP urządzenia (znajdują się w **urządzeń** kartę sekcji Menedżera urządzeń StorSimple &gt; **Ustawienia** &gt; **sieci** &gt; **ustawienia DNS** grupy).
   * *RecoveryPlanName***- VolumeContainers**: przecinkami ciąg kontenery woluminów na urządzeniu, wymagającym pozwalającym powyżej, na przykład: volcon1 volcon2, volcon3.
   * *RecoveryPlanName***- TargetDeviceName**: urządzenia chmury StorSimple, na którym kontenery są w tryb pracy awaryjnej.
   * *RecoveryPlanName***- TargetDeviceIpAddress**: adres IP urządzenia docelowego (znajdują się w **maszyny wirtualnej** sekcji &gt; **ustawienia**  grupy &gt; **sieci** kartę).
   * *RecoveryPlanName***- StorageAccountName**: Nazwa konta magazynu, w którym będą przechowywane skryptu (mającej do uruchamiania na nieudane przez VM). Może to być konto magazynu, które ma miejsce do tymczasowego przechowywania skryptu.
   * *RecoveryPlanName***- StorageAccountKey**: klucz dostępu dla konta magazynu powyżej.
   * *RecoveryPlanName***- VMGUIDS**: po ochrony maszyn wirtualnych, usługi Azure Site Recovery przypisuje każdej maszyny Wirtualnej Unikatowy identyfikator, który zawiera szczegóły nieudane za pośrednictwem maszyny Wirtualnej. Aby uzyskać VMGUID, wybierz **usług odzyskiwania** i kliknij polecenie **chronionego elementu** &gt; **grup ochrony** &gt;  **Maszyny** &gt; **właściwości**. Jeśli masz wiele maszyn wirtualnych, Dodaj identyfikatory GUID jako ciąg rozdzielony przecinkami.

    Na przykład, jeśli nazwa planu odzyskiwania jest fileServerpredayRP prawdopodobnie **zmienne**, **połączeń** i **certyfikaty** kartę powinna wyglądać następująco po dodaniu wszystkie zasoby.

    ![Elementy zawartości](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Przekaż modułu Runbook serii StorSimple 8000 na Twoim koncie automatyzacji. Użyj poniższych czynności, aby dodać moduł:

   a. Otwórz program powershell, Utwórz nowy folder i zmień katalog na folder.
    
    ```
         mkdir C:\scripts\StorSimpleSDKTools
         cd C:\scripts\StorSimpleSDKTools
    ```
   b. Pobierz nuget interfejsu wiersza polecenia w tym samym folderze w krok 1.
      Są dostępne w różnych wersjach nuget.exe [pobiera nuget](https://www.nuget.org/downloads). Każde łącze pobierania punktów bezpośrednio do pliku .exe tak należy kliknąć prawym przyciskiem myszy, a następnie zapisz plik do komputera zamiast uruchomienie jej w przeglądarce.

    ```
         wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

   c. Pobierz zestaw SDK zależne

    ```
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

   d. Tworzenie modułu Runbook automatyzacji Azure do zarządzania urządzeniami z serii StorSimple 8000. Użyj poniższych poleceń, aby utworzyć plik zip modułu automatyzacji.

    ```
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

     e. Importuj plik zip modułu usługi Automatyzacja Azure (Microsoft.Azure.Management.StorSimple8000Series.zip) utworzone w powyżej kroku. To może odbywać się wybierając konto automatyzacji, kliknij przycisk **modułów** w obszarze udostępnionych zasobów, a następnie kliknij przycisk **dodać moduł**.

    Po zaimportowaniu modułu serii StorSimple 8000 **modułów** kartę powinna wyglądać następująco:

    ![Moduły](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

8. Przejdź do **usług odzyskiwania** sekcji, a następnie wybierz utworzony wcześniej magazyn Azure Site Recovery.
9. Wybierz **plany odzyskiwania (lokacji odzyskiwania)** opcję **Zarządzaj** grupy i utworzenie nowego planu odzyskiwania w następujący sposób:

   a.  Kliknij przycisk **+ planu odzyskiwania** przycisku, otwiera poniżej bloku.

      ![Tworzenie planu odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Wprowadź nazwę planu odzyskiwania, wybierz wartości modelu źródłowego, docelowego i wdrożenia.

   c.  Wybierz maszyny wirtualne z grupy ochrony, które chcesz uwzględnić w planie odzyskiwania, a następnie kliknij przycisk **OK** przycisku.

   d.  Wybierz wcześniej utworzony plan odzyskiwania, kliknij przycisk **Dostosuj** przycisk, aby otworzyć widoku dostosowywania planu odzyskiwania.

   e.  Kliknij prawym przyciskiem myszy **wszystkich grup zamykania** i kliknij przycisk **dodać akcję sprzed**.

   f.  Otwiera Wstaw blok akcji, wprowadź nazwę, wybierz **głównej stronie** opcji w przypadku, gdy opcja uruchamiania, wybierz konto automatyzacji (w którym można dodać elementy runbook), a następnie wybierz **pracy awaryjnej-StorSimple--kontenery woluminów**  elementu runbook.

   g.  Kliknij prawym przyciskiem myszy **Grupa 1: Uruchom** i kliknij przycisk **Dodaj chronione elementy** opcji, a następnie wybierz maszyny wirtualne, które mają być chronione w planie odzyskiwania i kliknij przycisk **Ok** przycisku. Opcjonalne, jeśli jest ono już zaznaczone maszyn wirtualnych.

   h.  Kliknij prawym przyciskiem myszy **Grupa 1: Uruchom** i kliknij przycisk **Post akcji** opcji, a następnie dodaj następujące skrypty:

   * Element runbook Start-StorSimple--urządzenie wirtualne
   * Niepowodzenie elementu runbook przez StorSimple--kontenery woluminów
   * Element runbook instalacji woluminów po failover
   * Odinstaluj niestandardowy skryptu — rozszerzenia elementu runbook

   i.  Dodaj akcję ręczną po powyżej 4 skrypty w tym samym **Grupa 1: kroki po** sekcji. Ta akcja jest punkt, w którym można sprawdzić, czy wszystko działa poprawnie. Ta akcja musi zostać dodany tylko w ramach testowego trybu failover (tak wybierać tylko **testowy tryb Failover** wyboru).

   j.  Po akcji ręcznej, Dodaj **oczyszczania** przy użyciu tej samej procedury, który był używany przez inne elementy runbook w skrypcie. **Zapisz** planu odzyskiwania.

    > [!NOTE]
    > Podczas uruchamiania test trybu failover, należy sprawdzić wszystko, co w kroku akcji ręcznej, ponieważ woluminów StorSimple, które ma zostać sklonowany na urządzeniu docelowym zostaną usunięte w ramach czyszczenia po wykonaniu akcji ręcznej.
    >

    ![Recoery plan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Wykonaj test trybu failover
Zapoznaj się [rozwiązania Active Directory DR](../site-recovery/site-recovery-active-directory.md) Przewodnik uzupełniający szczególne zagadnienia dotyczące usługi Active Directory podczas testowania trybu failover. Ustawienia lokalnych nie zakłóceń w ogóle, podczas testowania trybu failover. Woluminów StorSimple, które zostały dołączone do lokalnej maszyny Wirtualnej są klonowane urządzenia StorSimple chmurze na platformie Azure. Maszyny Wirtualnej do celów testowych jest włączane na platformie Azure i sklonowany woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-the-test-failover"></a>Aby wykonać test trybu failover
1. W portalu Azure wybierz magazyn usługi Site Recovery.
2. Kliknij przycisk planu odzyskiwania utworzone dla serwera plików maszyny Wirtualnej.
3. Kliknij przycisk **testowanie trybu Failover**.
4. Wybierz sieć wirtualna platformy Azure, do której maszyny wirtualne Azure zostaną połączone po pracy awaryjnej.

   ![Uruchom pracę awaryjną](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postępy, klikając na maszynie Wirtualnej, aby otworzyć jego właściwości lub na **zadania testowego trybu failover** w nazwie magazynu &gt; **zadania** &gt; **zadania usługi Site Recovery**.
6. Po zakończeniu trybu failover, należy także widoczna replika Azure maszyny są wyświetlane w portalu Azure &gt; **maszyn wirtualnych**. Można wykonywać z operacji sprawdzania poprawności.
7. Po są wykonywane operacje sprawdzania poprawności, kliknij przycisk **pełne sprawdzanie poprawności**. To spowoduje usunięcie woluminów StorSimple i zamknąć urządzenia StorSimple w chmurze.
8. Gdy wszystko będzie gotowe, kliknij przycisk **oczyszczania testowy tryb failover** dla planu odzyskiwania. W notatkach zarejestrować i zapisać wszelkie obserwacje związane z testowania trybu failover. Spowoduje to usunięcie maszyny wirtualnej, które zostały utworzone podczas testowania trybu failover.

## <a name="perform-a-planned-failover"></a>Realizacja planowanego trybu failover
   Podczas planowanego trybu failover na lokalny serwer plików zamknięcia maszyny Wirtualnej jest bezpiecznie i chmury, w których wykonywana jest migawka kopii zapasowej woluminów na urządzeniu StorSimple. Woluminy StorSimple są Failover urządzeń wirtualnych, replika maszyny Wirtualnej jest włączane na platformie Azure i woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-planned-failover"></a>Aby wykonać planowanego trybu failover
1. W portalu Azure wybierz **usług odzyskiwania** magazynu &gt; **planów odzyskiwania (lokacji odzyskiwania)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
2. W bloku planu odzyskiwania, kliknij **więcej** &gt; **planowanego trybu failover**.

   ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. Na **potwierdzić planowanego trybu Failover** bloku, wybierz źródłowych i docelowych lokalizacji i Sieć docelowa wybierz i kliknij ikonę znacznika wyboru ✓, aby rozpocząć proces trybu failover.
4. Po utworzeniu maszyny wirtualnej repliki, są one w stan oczekiwania na zatwierdzenie. Kliknij przycisk **zatwierdzania** do zatwierdzenia pracy awaryjnej.
5. Po zakończeniu replikacji maszyn wirtualnych uruchomienia w lokalizacji dodatkowej.

## <a name="perform-a-failover"></a>Przejściu w tryb failover
Podczas nieplanowanego trybu failover woluminów StorSimple nie powiodło przez urządzenie wirtualne, replika maszyny Wirtualnej zostanie przeniesiony na platformie Azure i woluminy są dołączone do maszyny Wirtualnej.

#### <a name="to-perform-a-failover"></a>Aby włączyć tryb failover
1. W portalu Azure wybierz **usług odzyskiwania** magazynu &gt; **planów odzyskiwania (lokacji odzyskiwania)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
2. W bloku planu odzyskiwania, kliknij **więcej** &gt; **pracy awaryjnej**.
3. Na **potwierdzić trybu Failover** bloku, wybierz źródło i docelowych lokalizacji.
4. Wybierz **wyłączyć maszyny wirtualne i zsynchronizuj najnowsze dane** do określenia, czy usługi Site Recovery należy spróbować zamknąć chronioną maszynę wirtualną i synchronizacji danych, dzięki czemu będzie można przełączyć najnowszej wersji danych.
5. Po pracy awaryjnej maszyny wirtualne są w stan oczekiwania na zatwierdzenie. Kliknij przycisk **zatwierdzania** do zatwierdzenia pracy awaryjnej.


## <a name="perform-a-failback"></a>Wykonaj powrót po awarii
Podczas powrotu po awarii kontenery woluminów StorSimple są nie powiodło się na powrót do urządzenia fizycznego po wykonywana jest kopia zapasowa.

#### <a name="to-perform-a-failback"></a>Aby wykonać powrotu po awarii
1. W portalu Azure wybierz **usług odzyskiwania** magazynu &gt; **planów odzyskiwania (lokacji odzyskiwania)** &gt; **recoveryplan_name** utworzone dla Serwer plików maszyny Wirtualnej.
2. W bloku planu odzyskiwania, kliknij **więcej** &gt; **planowanego trybu Failover**.
3. Wybierz lokalizacje źródłowa i docelowa, wybierz odpowiednie synchronizacji danych oraz opcje tworzenia maszyny Wirtualnej.
4. Kliknij przycisk **OK** przycisk, aby rozpocząć proces powrotu po awarii.

   ![Uruchom powrotu po awarii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="capacity-planning-and-readiness-assessment"></a>Pojemności oceny planowania i gotowości
#### <a name="hyper-v-site"></a>Lokacja funkcji Hyper-V
Użyj [planisty wydajności użytkownika](http://www.microsoft.com/download/details.aspx?id=39057) do projektowania serwera, magazynu i infrastruktury sieci w środowisku funkcji Hyper-V replica.

#### <a name="azure"></a>Azure
Można uruchomić [narzędzie oceny gotowości maszyny wirtualnej Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi Azure i usług Azure Site Recovery. Narzędzie oceny gotowości sprawdza konfiguracji maszyny Wirtualnej, a także ostrzega, konfiguracje są niezgodne z platformy Azure. Na przykład jego wygeneruje ostrzeżenie, jeśli dysk C: jest większy niż 127 GB.

Planowanie wydajności składa się z co najmniej dwie ważne procesów:

* Mapowanie lokalnych maszyn wirtualnych funkcji Hyper-V do rozmiarów maszyn wirtualnych Azure (na przykład A6, A7 A8 i A9).
* Określanie wymaganego przepustowości połączenia z Internetem.

## <a name="limitations"></a>Ograniczenia
* Obecnie tylko 1 urządzenia StorSimple można Failover (na jednym urządzeniu chmury StorSimple). Scenariusz serwera plików, które obejmuje kilka urządzeń StorSimple nie jest jeszcze obsługiwany.
* Jeśli wystąpi błąd podczas włączania ochrony dla maszyny Wirtualnej, upewnij się, Rozłączono obiektów docelowych iSCSI.
* Wszystkie kontenery woluminów, które zostały zgrupowane razem z powodu zasad tworzenia kopii zapasowych, dzieląc go w kontenery woluminów będzie można przełączyć razem.
* Wszystkie woluminy w kontenerach woluminu wybrana zostanie zainicjowana.
* Nie można przełączyć woluminów, które dodać do więcej niż 64 TB, ponieważ maksymalną pojemność pojedynczego urządzenia chmury StorSimple wynosi 64 TB.
* Jeśli planowane lub nieplanowane przejście trybu failover nie powiedzie się i maszyn wirtualnych są tworzone na platformie Azure, następnie nie oczyszczania zapasowej maszyn wirtualnych. Zamiast tego powrotu po awarii. Jeśli usuniesz maszyn wirtualnych następnie lokalnych maszyn wirtualnych nie można włączyć ponownie.
* Po przejściu w tryb failover Jeśli nie są widoczne na woluminach, przejdź do maszyn wirtualnych, otwórz przystawkę Zarządzanie dyskami ponownie przeskanować dyski i dostosować je online.
* W niektórych przypadkach litery dysku w lokacji odzyskiwania po awarii może różnić się od litery lokalną. W takim przypadku należy ręcznie rozwiązać ten problem, po zakończeniu pracy awaryjnej.
* Limit czasu zadania trybu failover: StorSimple skryptu zostanie limit czasu, jeśli trybem failover kontenery woluminów trwa dłużej niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut).
* Limit czasu zadania tworzenia kopii zapasowej: Jeśli woluminów kopii zapasowej trwa dłużej niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut) limitu czasu skryptu StorSimple.

  > [!IMPORTANT]
  > Ręcznie uruchom kopię zapasową z portalu Azure, a następnie ponownie uruchom planu odzyskiwania.

* Limit czasu zadania sklonować: Jeśli Klonowanie woluminów zajmuje więcej czasu niż limit usługi Azure Site Recovery dla skryptu (obecnie 120 minut) limitu czasu skryptu StorSimple.
* Błąd synchronizacji czasu: StorSimple skrypty błędy limit informujący o tym, że kopie zapasowe nie powiodły, mimo że kopii zapasowej zakończy się pomyślnie w portalu. Możliwą przyczyną tego może być, że urządzenia StorSimple czasu może być zsynchronizowany z bieżącym czasem w strefie czasowej.

  > [!IMPORTANT]
  > Synchronizacja czasu urządzenia przy użyciu bieżącego czasu w strefie czasowej.

* Błąd trybu failover urządzenia: StorSimple skrypt może zakończyć się niepowodzeniem, jeśli występuje urządzenia trybu failover, gdy działa planu odzyskiwania.

  > [!IMPORTANT]
  > Po zakończeniu pracy awaryjnej urządzenia, uruchom ponownie planu odzyskiwania.


## <a name="summary"></a>Podsumowanie
Za pomocą usługi Azure Site Recovery, można utworzyć plan odzyskiwania ukończenia automatycznego po awarii dla serwera plików maszyny Wirtualnej o udziały plików hostowane na magazynie StorSimple. Możesz zainicjować trybu failover w ciągu kilku sekund z dowolnego miejsca w przypadku przerwy w pracy i get aplikacji do pracy w ciągu kilku minut.
