---
title: Planowanie wydajności i skalowania dla odzyskiwania po awarii programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule ułatwiają planowanie wydajności i skalowania podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: c198e6cd9d5c5e0aca69491db9df5d0ab8e08c7a
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358013"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planowanie wydajności i skalowanie na potrzeby odzyskiwania po awarii programu VMware na platformę Azure

W tym artykule umożliwiają planowanie pojemności i skalowania podczas replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak rozpocząć planowanie pojemności

Aby dowiedzieć się więcej na temat wymagań dotyczących infrastruktury usługi Azure Site Recovery, należy zebrać informacje o środowisku replikacji, uruchamiając [planista wdrażania usługi Azure Site Recovery](https://aka.ms/asr-deployment-planner-doc) potrzeby replikacji oprogramowania VMware. Aby uzyskać więcej informacji, zobacz [Planisty wdrożenia o Site Recovery dla oprogramowania VMware na platformę Azure](site-recovery-deployment-planner.md). 

Planista wdrażania usługi Site Recovery udostępnia raport, który zawiera pełne informacje na temat zgodnych i niezgodnych maszyn wirtualnych, dysków na maszynę Wirtualną, a współczynnik zmian danych na dysku. Narzędzie jest także podsumowanie wymagania dotyczące przepustowości sieci w celu spełnienia docelowego cel punktu odzyskiwania i infrastruktury platformy Azure, które są wymagane do pomyślnej replikacji i testowania trybu failover.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności

Składnik | Szczegóły
--- | ---
**Replikacja** | **Maksymalna dziennych zmian**: Chronione maszyny można użyć tylko jeden serwer przetwarzania. Serwer pojedynczego procesu może obsługiwać dzienny zmiany szybkości do 2 TB. Dlatego 2 TB jest maksymalny Dzienny współczynnik zmian danych, która jest obsługiwana dla chronionej maszyny.<br /><br /> **Maksymalna przepływność**: Replikowanej maszyny mogą należeć do jednego konta magazynu na platformie Azure. Standardowe konto usługi Azure Storage może obsługiwać maksymalnie 20 000 żądań na sekundę. Zaleca się ograniczenie liczby operacji wejścia/wyjścia na sekundę (IOPS) na maszynie źródłowej do 20 000. Na przykład jeśli masz maszyny źródłowej, która ma pięć dysków, a każdy dysk generuje 120 operacje We/Wy (8 K rozmiar) na maszynie źródłowej, maszyna źródłowa jest w ramach limitu 500 operacji We/Wy dysku platformy Azure. (Liczba kont magazynu wymagana jest równa maszyny źródłowej łączna liczba operacji We/Wy, podzielona przez 20 000).
**Serwer konfiguracji** | Serwer konfiguracji musi być w stanie obsłużyć dzienną wydajność współczynnika zmian we wszystkich obciążeń uruchomionych na chronionych maszynach. Maszyna konfiguracji musi mieć wystarczającą przepustowość, aby stale replikować dane do usługi Azure Storage.<br /><br /> Najlepszym rozwiązaniem jest umieścić serwer konfiguracji na tej samej sieci i segment sieci LAN, jak na komputerach, które mają być chronione. Serwer konfiguracji można umieścić w innej sieci, ale maszyn, które mają być chronione, powinni mieć wgląd sieci 3 warstwy.<br /><br /> Zalecenia dotyczące rozmiaru serwera konfiguracji są podsumowane w tabeli w poniższej sekcji.
**Serwer przetwarzania** | Pierwszy serwer przetwarzania jest instalowany domyślnie na serwerze konfiguracji. Można wdrażać dodatkowych serwerów przetwarzania do skalowania środowiska. <br /><br /> Serwer przetwarzania odbiera dane replikacji z chronionych maszyn. Serwer przetwarzania optymalizuje dane przy użyciu pamięci podręcznej, kompresji i szyfrowania. Następnie serwer przetwarzania wysyła dane do platformy Azure. Maszyna serwera przetwarzania musi mieć wystarczające zasoby do wykonywania tych zadań.<br /><br /> Serwer przetwarzania korzysta z pamięci podręcznej opartej na dyskach. Do obsługi zmian danych, które są przechowywane w przypadku wąskich gardeł lub awaria wystąpienia, należy użyć dysku oddzielne pamięci podręcznej 600 GB lub więcej.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Zalecenia dotyczące rozmiaru serwera konfiguracji i wbudowanych serwera przetwarzania

Serwera konfiguracji, który korzysta z serwerem przetwarzania wbudowanych chronić obciążenia może obsługiwać maksymalnie 200 maszyn wirtualnych, w oparciu o następujące konfiguracje:

Procesor CPU | Memory (Pamięć) | Rozmiar dysku w pamięci podręcznej | Współczynnik zmian danych | Chronione maszyny
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | Umożliwia replikowanie maszyn mniej niż 100.
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | Umożliwia replikowanie maszyn 100-150.
16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB do 2 TB | Umożliwia replikowanie maszyn 151 do 200.
Wdrażanie z innego serwera konfiguracji za pomocą [szablonu pakietu OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Jeśli replikujesz ponad 200 maszyn, należy wdrożyć nowy serwer konfiguracji.
Wdrażanie drugiego [serwera przetwarzania](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Wdrażanie nowego serwera przetwarzania skalowalnego w poziomie, jeśli całkowity Dzienny współczynnik zmian danych jest większa niż 2 TB.

W tych konfiguracjach:

* Każda maszyna źródłowa ma trzy dyski 100 GB.
* Użyliśmy porównawczych magazyn ośmiu dysków sygnatury dostępu współdzielonego o wielkości 10 tys. obr/min za pomocą macierzy RAID 10 dla miar dysk pamięci podręcznej.

## <a name="size-recommendations-for-the-process-server"></a>Zalecenia dotyczące rozmiaru serwera przetwarzania

Serwer przetwarzania jest składnikiem, który obsługuje replikację danych w usłudze Azure Site Recovery. Jeśli dziennych zmian jest większa niż 2 TB, należy dodać serwerów przetwarzania skalowalnego w poziomie w celu obsługi obciążenia replikacji. Aby skalować, możesz wykonywać następujące czynności:

* Zwiększ liczbę serwerów konfiguracji, wdrażania przy użyciu [szablonu pakietu OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Na przykład można chronić maksymalnie 400 maszyn przy użyciu dwóch serwerów konfiguracji.
* Dodaj [serwerów przetwarzania skalowalnego w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file). Użyj serwerów przetwarzania skalowalnego w poziomie do obsługi ruchu związanego z replikacją zamiast (lub oprócz) na serwerze konfiguracji.

W poniższej tabeli opisano w tym scenariuszu:

* Konfigurowania serwera przetwarzania skalowalnego w poziomie.
* Skonfigurowano chronionych maszyn wirtualnych używających serwera przetwarzania skalowalnego w poziomie.
* Każda maszyna chronionego źródła ma trzy dyski 100 GB.

Dodatkowym serwerze przetwarzania | Rozmiar dysku w pamięci podręcznej | Współczynnik zmian danych | Chronione maszyny
--- | --- | --- | ---
4 Vcpu (2 sockets * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Umożliwia replikowanie maszyn 85 lub mniej.
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci RAM | 600 GB | 251 GB do 1 TB | Umożliwia replikowanie maszyn 86-150.
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) 24 GB pamięci | 1 TB | > 1 TB do 2 TB | Umożliwia replikowanie maszyn 151 do 225.

Jak skalować serwery w zależy od preferencjami dla modelu, skalowanie w pionie lub poziomie. Skalowanie w górę, wdrażanie kilka serwerów wysokiej klasy konfiguracji i serwerów przetwarzania. Aby skalować w poziomie, należy wdrożyć więcej serwerów, które mają mniej zasobów. Na przykład jeśli chcesz chronić 200 maszyn z ogólną Dzienny współczynnik zmian danych o rozmiarze 1,5 TB, może potrwać, jedną z następujących czynności:

* Skonfiguruj serwer pojedynczego procesu (16 vCPU, 24 GB pamięci RAM).
* Należy skonfigurować dwa serwery przetwarzania, (2 x 8 procesorów vCPU, 2 * 12 GB pamięci RAM).

## <a name="control-network-bandwidth"></a>Sterowania przepustowością sieci

Po użyciu [planista wdrażania usługi Site Recovery](site-recovery-deployment-planner.md) do obliczania przepustowości, należy dla replikacji (Replikacja początkowa i przyrostowa), masz kilka opcji umożliwiających kontrolę przepustowości, który służy do Replikacja:

* **Ograniczanie przepustowości**: Ruch VMware są replikowane do platformy Azure przechodzi przez serwer określonego procesu. Możesz ograniczyć przepustowość na maszynach, które działają jako serwerów przetwarzania.
* **Mają wpływ na przepustowość**: Może mieć wpływ na przepustowość, która jest używana do replikacji przy użyciu kilku kluczy rejestru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** wartość rejestru określa liczbę wątków, które są używane do transferu danych (Replikacja początkowa lub przyrostowa) dysku. Wyższa wartość zwiększa przepustowość sieci, która jest używana do replikacji.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** wartość rejestru określa liczbę wątków, które są używane do transferu danych podczas powrotu po awarii.

### <a name="throttle-bandwidth"></a>Ograniczanie przepustowości

1. Otwórz przystawkę MMC usługi Azure Backup na komputerze, którego używasz jako serwera przetwarzania. Domyślnie skrót do usługi Backup jest dostępny na pulpicie lub w następującym folderze: Agent\bin C:\Program Files\Microsoft Azure Recovery Services.
2. W przystawce, wybierz **Zmień właściwości**.

    ![Zrzut ekranu przedstawiający Azure Backup programu MMC przystawki możliwość zmiany właściwości](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **ograniczania** zaznacz **włączyć internetowe ograniczanie użycia przepustowości dla operacji tworzenia kopii zapasowej**. Ustaw limity dla pracy i innych niż godziny pracy. Prawidłowe zakresy są od 512 KB/s do 1,023 MB/s.

    ![Zrzut ekranu przedstawiający okno dialogowe właściwości kopia zapasowa Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Możesz też użyć polecenia cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx), aby ustawić ograniczanie przepływności. Oto przykład:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** wskazuje, że ograniczanie przepływności nie jest wymagane.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Instrukcja ALTER przepustowość sieci dla maszyny Wirtualnej

1. W rejestrze maszyny Wirtualnej, przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Aby zmienić przepustowość wykorzystywaną przez ruch na dysk replikujący, zmodyfikuj wartość **UploadThreadsPerVM**. Utwórz klucz, jeśli nie istnieje.
   * Aby zmienić przepustowości dla ruchu powrotu po awarii z platformy Azure, zmodyfikuj wartość **DownloadThreadsPerVM**.
2. Wartością domyślną dla każdego klucza jest **4**. W sieci „o nadmiarowych zasobach” należy zmienić wartości domyślne tych kluczy rejestru. Wartość maksymalna, możesz użyć **32**. Monitoruj ruch, aby zoptymalizować tę wartość.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Konfigurowanie infrastruktury Site Recovery, aby chronić więcej niż 500 maszyn wirtualnych

Przed skonfigurowaniem infrastruktura usługi Site Recovery dostęp do środowiska, aby zmierzyć od następujących czynników: zgodnych maszyn wirtualnych, dzienny współczynnik zmian danych, wymagana przepustowość sieci punktu odzyskiwania, aby uzyskać liczbę Site Recovery składniki, które są wymagane, a czas potrzebny do ukończenia replikacji początkowej. Wykonanie poniższych kroków w celu zebrania wymaganych informacji:

1. W celu mierzenia tych parametrów, należy uruchomić planista wdrażania usługi Site Recovery w środowisku. Aby uzyskać pomocne wskazówki, zobacz [Planisty wdrożenia o Site Recovery dla oprogramowania VMware na platformę Azure](site-recovery-deployment-planner.md).
2. Wdrażanie serwera konfiguracji, który spełnia [zalecenia dotyczące serwera konfiguracji rozmiaru](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Jeśli obciążenie produkcyjne przekracza 650 maszyn wirtualnych, należy wdrożyć innym serwerze konfiguracji.
3. Oparte na mierzonego dziennych zmian danych, wdrażanie [serwerów przetwarzania skalowalnego w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file) za pomocą [rozmiar wytycznych](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Jeśli spodziewasz się, że szybkość dysku maszyny wirtualnej przekracza 2 MB/s zmian danych, upewnij się, korzystać z dysków zarządzanych w warstwie premium. Planista wdrażania usługi Site Recovery jest uruchamiany dla określonego przedziału czasu. Szczytowe współczynnik zmian danych w innym czasie nie zostaną przechwycone w raporcie.
5. [Ustaw przepustowość sieci](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) oparte na cel punktu odzyskiwania chcesz osiągnąć.
6. Po skonfigurowaniu infrastruktury włączyć odzyskiwanie po awarii dla obciążenia. Aby dowiedzieć się więcej, zobacz temat [Konfigurowanie środowiska źródłowego dla oprogramowania VMware do platformy Azure replikacji](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Wdrażanie dodatkowych serwerów przetwarzania

Możesz skalować wdrożenie ponad 200 maszyn źródłowych lub masz łącznie codziennie współczynnik więcej niż 2 TB do zmian danych, należy dodać serwerów przetwarzania do obsługi natężenia ruchu. Aby dowiedzieć się, jak skonfigurować serwer przetwarzania, zobacz [skali na potrzeby powrotu po awarii przy użyciu dodatkowych serwerów przetwarzania](vmware-azure-set-up-process-server-scale.md). Po skonfigurowaniu serwera przetwarzania można migrować maszyny źródłowe z niego korzystać.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrowanie maszyn do użycia nowego serwera przetwarzania

1. Wybierz **ustawienia** > **serwerów Site Recovery**. Wybierz serwer configuration server, a następnie rozwiń **przetwarzania serwerów**.

    ![Zrzut ekranu przedstawiający okno dialogowe serwera przetwarzania](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kliknij prawym przyciskiem myszy serwer przetwarzania, obecnie w użyciu, a następnie wybierz **przełącznika**.

    ![Zrzut ekranu przedstawiający okno dialogowe konfiguracji serwera](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. W **wybierz docelowy serwer przetwarzania**, wybierz nowego serwera przetwarzania, którego chcesz użyć. Następnie wybierz maszyny wirtualne, które będzie obsługiwać serwer. Aby uzyskać informacje o serwerze, wybierz ikonę informacji. Aby ułatwić ładowanie decyzje, jest wyświetlana średnia miejsca, które są wymagane do replikowania każdej wybranej maszyny wirtualnej do nowego serwera przetwarzania. Kliknij znacznik wyboru, aby rozpocząć replikowanie do nowego serwera przetwarzania.

## <a name="deploy-additional-master-target-servers"></a>Wdrażanie dodatkowych główne serwery docelowe

W następujących scenariuszach wymaganych jest więcej niż jeden główny serwer docelowy:

*   Chcesz chronić maszyny wirtualnej z systemem Linux.
*   Główny serwer docelowy dostępne na serwerze konfiguracji nie ma dostępu do magazynu danych maszyny wirtualnej.
*   Łączna liczba dysków na głównym serwerze docelowym (liczba dyski lokalne na serwerze), a także liczbę dysków, które ma być chroniony, jest większa niż 60 dysków.

Aby dowiedzieć się, jak dodać serwer główny serwer docelowy dla maszyny wirtualnej z systemem Linux, zobacz [instalowanie Linux główny serwer docelowy do powrotu po awarii](vmware-azure-install-linux-master-target.md).

Aby dodać serwer główny serwer docelowy dla maszyny wirtualnej z systemem Windows:

1. Przejdź do **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
2. Wybierz serwer konfiguracji, a następnie wybierz pozycję **główny serwer docelowy**.

    ![Zrzut ekranu przedstawiający przycisk dodawania głównego serwera docelowego](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Pobierz plik instalacyjny ujednoliconego, a następnie uruchom plik na maszynie Wirtualnej, aby skonfigurować główny serwer docelowy.
4. Wybierz **głównego elementu docelowego instalacji** > **dalej**.

    ![Zrzut ekranu pokazujący, wybierając opcję głównego elementu docelowego instalacji](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Wybierz domyślną lokalizację instalacji, a następnie wybierz **zainstalować**.

     ![Zrzut ekranu pokazujący domyślną lokalizację instalacji](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Aby zarejestrować głównego elementu docelowego z serwerem konfiguracji, zaznacz opcję **przejść do konfiguracji**.

    ![Zrzut ekranu pokazujący przejdź do opcji konfiguracji, przycisk](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Wprowadź adres IP serwera konfiguracji, a następnie wprowadź hasło. Aby dowiedzieć się, jak wygenerować hasła, zobacz [wygenerować hasło serwera konfiguracji](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Zrzut ekranu przedstawiający miejsce wprowadzania adresu IP i hasła dla serwera konfiguracji](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Wybierz pozycję **Zarejestruj**. Po zakończeniu rejestracji wybierz **Zakończ**.

Po pomyślnym zakończeniu rejestracji serwer znajduje się w witrynie Azure portal pod **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery**  >   **Serwery konfiguracji**, na serwerach głównego elementu docelowego serwera konfiguracji.

 > [!NOTE]
 > Pobierz najnowszą wersję [głównego elementu docelowego ujednoliconej konfiguracji w pliku serwera Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Kolejne kroki

Pobierz i uruchom [planista wdrażania usługi Site Recovery](https://aka.ms/asr-deployment-planner).
