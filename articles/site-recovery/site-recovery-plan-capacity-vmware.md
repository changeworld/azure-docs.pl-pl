---
title: Planowanie pojemności odzyskiwania po awarii VMware za pomocą usługi Azure Site Recovery
description: Ten artykuł może pomóc w planowaniu pojemności i skalowania podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257617"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planowanie pojemności i skalowania odzyskiwania po awarii vMware na platformie Azure

Ten artykuł służy do planowania pojemności i skalowania podczas replikowania lokalnych maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak rozpocząć planowanie pojemności?

Aby dowiedzieć się więcej o wymaganiach dotyczących infrastruktury usługi Azure Site Recovery, zbierz informacje o środowisku replikacji, uruchamiając [planista wdrażania usługi Azure Site Recovery](https://aka.ms/asr-deployment-planner-doc) dla replikacji VMware. Aby uzyskać więcej informacji, zobacz [Planer wdrażania odzyskiwania witryny dla VMware na platformie Azure](site-recovery-deployment-planner.md). 

Planista wdrażania odzyskiwania lokacji zawiera raport zawierający pełne informacje o zgodnych i niezgodnych maszynach wirtualnych, dyskach na maszynę wirtualną i przepływach danych na dysku. Narzędzie podsumowuje również wymagania dotyczące przepustowości sieci, aby spełnić docelowy celowy cel ochrony cel i infrastrukturę platformy Azure, która jest wymagana do pomyślnej replikacji i testowania pracy awaryjnej.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

Składnik | Szczegóły
--- | ---
**Replikacji** | **Maksymalna dzienna szybkość zmian:** chroniony komputer może używać tylko jednego serwera przetwarzania. Pojedynczy serwer przetwarzania może obsłużyć dziennie do 2 TB zmian. Tak więc 2 TB to maksymalna dzienna szybkość zmiany danych, która jest obsługiwana dla chronionego komputera.<br /><br /> **Maksymalna przepustowość:** zreplikowana maszyna może należeć do jednego konta magazynu na platformie Azure. Standardowe konto usługi Azure Storage może obsługiwać maksymalnie 20 000 żądań na sekundę. Zaleca się ograniczenie liczby operacji wejścia/wyjścia na sekundę (IOPS) na komputerze źródłowym do 20 000. Na przykład jeśli masz komputer źródłowy, który ma pięć dysków i każdy dysk generuje 120 Operacji We/Wy (8 K w rozmiarze) na komputerze źródłowym, komputer źródłowy znajduje się w ramach limitu operacji We/Wy platformy Azure na dysk 500. (Liczba wymaganych kont magazynu jest równa całkowitej liczbie we/wy komputera źródłowego podzielonej przez 20 000).
**Serwer konfiguracji** | Serwer konfiguracji musi być w stanie obsłużyć dzienną szybkość zmiany we wszystkich obciążeniach uruchomionych na chronionych komputerach. Komputer konfiguracyjny musi mieć wystarczającą przepustowość, aby stale replikować dane do usługi Azure Storage.<br /><br /> Najlepszym rozwiązaniem jest umieszczenie serwera konfiguracji w tym samym segmencie sieci i sieci LAN, co maszyny, które chcesz chronić. Serwer konfiguracji można umieścić w innej sieci, ale maszyny, które mają być chronione, powinny mieć widoczność sieci w warstwie 3.<br /><br /> Zalecenia dotyczące rozmiaru serwera konfiguracji są podsumowane w tabeli w poniższej sekcji.
**Serwer przetwarzania** | Pierwszy serwer przetwarzania jest instalowany domyślnie na serwerze konfiguracji. Można wdrożyć dodatkowe serwery procesów do skalowania środowiska. <br /><br /> Serwer przetwarzania odbiera dane replikacji z chronionych komputerów. Serwer przetwarzania optymalizuje dane przy użyciu buforowania, kompresji i szyfrowania. Następnie serwer przetwarzania wysyła dane do platformy Azure. Komputer serwera przetwarzania musi mieć wystarczające zasoby do wykonywania tych zadań.<br /><br /> Serwer przetwarzania używa pamięci podręcznej opartej na dysku. Użyj oddzielnego dysku pamięci podręcznej o rozmiarze co najmniej 600 GB, aby obsłużyć zmiany danych, które są przechowywane w przypadku wystąpienia wąskiego gardła lub awarii sieci.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Zalecenia dotyczące rozmiaru serwera konfiguracji i wbudowanego serwera przetwarzania

Serwer konfiguracji, który używa wbudowanego serwera przetwarzania do ochrony obciążenia, może obsługiwać do 200 maszyn wirtualnych na podstawie następujących konfiguracji:

Procesor CPU | Memory (Pamięć) | Rozmiar dysku w pamięci podręcznej | Szybkość zmiany danych | Chronione maszyny
--- | --- | --- | --- | ---
8 procesorów wirtualnych (2 gniazda \@ * 4 rdzenie 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | Służy do replikowania mniej niż 100 maszyn.
12 procesorów wirtualnych (2 gniazda \@ * 6 rdzeni 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | Służy do replikowania od 100 do 150 maszyn.
16 procesorów wirtualnych (2 gniazda \@ * 8 rdzeni 2,5 GHz) | 32 GB | 1 TB | >1 TB do 2 TB | Służy do replikowania od 151 do 200 maszyn.
Wdrożyć inny serwer konfiguracji przy użyciu [szablonu OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Wdrażanie nowego serwera konfiguracji, jeśli replikujesz więcej niż 200 maszyn.
Wdrożenie innego [serwera przetwarzania](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Wdrażanie nowego serwera procesów skalowanych w poziomie, jeśli ogólna dzienna szybkość zmiany danych jest większa niż 2 TB.

W tych konfiguracjach:

* Każdy komputer źródłowy ma trzy dyski po 100 GB każdy.
* Użyliśmy przechowywania porównawczego ośmiu dysków sygnowanych współdzielonym o prędkości 10 K RPM z raidem 10 do pomiarów dysków pamięci podręcznej.

## <a name="size-recommendations-for-the-process-server"></a>Zalecenia dotyczące rozmiaru dla serwera przetwarzania

Serwer przetwarzania jest składnikiem, który obsługuje replikację danych w usłudze Azure Site Recovery. Jeśli dzienna szybkość zmiany jest większa niż 2 TB, należy dodać serwery procesów skalowanych w poziomie do obsługi obciążenia replikacji. Aby skalować w poziomie, można:

* Zwiększ liczbę serwerów konfiguracyjnych, wdrażając za pomocą [szablonu OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Na przykład można chronić maksymalnie 400 maszyn przy użyciu dwóch serwerów konfiguracji.
* Dodaj [serwery procesów skalowanych w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file). Serwery procesów skalowaj w poziomie są używane do obsługi ruchu replikacji zamiast (lub dodatkowo) serwera konfiguracji.

W poniższej tabeli opisano ten scenariusz:

* Konfigurowanie serwera procesów skalowanych w poziomie.
* Skonfigurowano chronione maszyny wirtualne do używania serwera procesów skalowanych w poziomie.
* Każdy chroniony komputer źródłowy ma trzy dyski po 100 GB każdy.

Dodatkowy serwer przetwarzania | Rozmiar dysku w pamięci podręcznej | Szybkość zmiany danych | Chronione maszyny
--- | --- | --- | ---
4 procesory wirtualne (2 gniazda \@ * 2 rdzenie 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Służy do replikowania 85 lub mniej maszyn.
8 procesorów wirtualnych (2 gniazda \@ * 4 rdzenie 2,5 GHz), 12 GB pamięci | 600 GB | 251 GB do 1 TB | Służy do replikowania 86 do 150 maszyn.
12 procesorów wirtualnych (2 gniazda \@ * 6 rdzeni 2,5 GHz) 24 GB pamięci | 1 TB | >1 TB do 2 TB | Służy do replikowania od 151 do 225 maszyn.

Sposób skalowania serwerów zależy od preferencji dotyczących modelu skalowania w górę lub w poziomie. Aby skalować w górę, wdrożyć kilka serwerów konfiguracji wysokiej klasy i serwerów przetwarzania. Aby skalować w poziomie, należy wdrożyć więcej serwerów, które mają mniej zasobów. Na przykład, jeśli chcesz chronić 200 maszyn z ogólną dzienną szybkością zmiany danych 1,5 TB, możesz podjąć jedną z następujących czynności:

* Konfigurowanie serwera pojedynczego procesu (16 procesorów wirtualnych, 24 GB pamięci RAM).
* Skonfiguruj dwa serwery przetwarzania (2 x 8 procesorów wirtualnych, 2* 12 GB pamięci RAM).

## <a name="control-network-bandwidth"></a>Sterowanie przepustowością sieci

Po użyciu [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) do obliczania przepustowości potrzebnej do replikacji (replikacja początkowa, a następnie delta), masz kilka opcji kontrolowania przepustowości używanej do replikacji:

* **Przepustowość przepustnicy:** ruch VMware, który replikuje się na platformę Azure przechodzi przez serwer określonego procesu. Przepustowość można ograniczyć na komputerach, które są uruchomione jako serwery przetwarzania.
* **Wpływ na przepustowość:** Można wpływać na przepustowość używaną do replikacji przy użyciu kilku kluczy rejestru:
  * Wartość rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** określa liczbę wątków używanych do przesyłania danych (replikacja początkowa lub delta) dysku. Wyższa wartość zwiększa przepustowość sieci, która jest używana do replikacji.
  * Wartość rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** określa liczbę wątków używanych do przesyłania danych podczas powrotu po awarii.

### <a name="throttle-bandwidth"></a>Ograniczanie przepustowości

1. Otwórz przystawkę programu MMC usługi Azure Backup na komputerze używanym jako serwer przetwarzania. Domyślnie skrót do kopii zapasowej jest dostępny na pulpicie lub w następującym folderze: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. W przystawce wybierz pozycję **Zmień właściwości**.

    ![Zrzut ekranu przedstawiający opcję przystawki programu MMC usługi Azure Backup, aby zmienić właściwości](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na karcie **Ograniczanie wybierz** pozycję **Włącz ograniczanie przepustowości internetu w przypadku operacji tworzenia kopii zapasowych**. Ustaw limity godzin pracy i niepracy. Prawidłowe zakresy to od 512 Kb/s do 1023 Mb/s.

    ![Zrzut ekranu przedstawiający okno dialogowe Właściwości kopii zapasowej platformy Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Możesz też użyć polecenia cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx), aby ustawić ograniczanie przepływności. Oto przykład:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** wskazuje, że ograniczanie przepływności nie jest wymagane.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Zmienianie przepustowości sieci dla maszyny Wirtualnej

1. W rejestrze maszyny Wirtualnej przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Aby zmienić ruch przepustowości na dysku replikującym, zmodyfikuj wartość **UploadThreadsPerVM**. Utwórz klucz, jeśli nie istnieje.
   * Aby zmienić przepustowość dla ruchu po awarii z platformy Azure, zmodyfikuj wartość **DownloadThreadsPerVM**.
2. Wartość domyślna dla każdego klawisza to **4**. W sieci „o nadmiarowych zasobach” należy zmienić wartości domyślne tych kluczy rejestru. Maksymalna wartość, której można użyć, to **32**. Monitoruj ruch, aby zoptymalizować tę wartość.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Konfigurowanie infrastruktury odzyskiwania witryny w celu ochrony ponad 500 maszyn wirtualnych

Przed skonfigurowaniem infrastruktury odzyskiwania lokacji należy uzyskać dostęp do środowiska w celu zmierzenia następujących czynników: zgodnych maszyn wirtualnych, dziennej szybkości zmiany danych, wymaganej przepustowości sieci dla celu celowego, który chcesz osiągnąć, liczby funkcji Odzyskiwaniu lokacji komponentów, które są wymagane, oraz czas potrzebny do ukończenia replikacji początkowej. Wykonaj następujące kroki, aby zebrać wymagane informacje:

1. Aby zmierzyć te parametry, uruchom Planer wdrażania odzyskiwania witryny w swoim środowisku. Aby uzyskać przydatne wskazówki, zobacz [Planer wdrażania odzyskiwania witryny dla VMware na platformie Azure](site-recovery-deployment-planner.md).
2. Wdrażanie serwera konfiguracji, który spełnia [zalecenia dotyczące rozmiaru serwera konfiguracji](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Jeśli obciążenie produkcyjne przekracza 650 maszyn wirtualnych, wdrożyć inny serwer konfiguracji.
3. Na podstawie zmierzonego dziennego współczynnika zmiany danych należy wdrażać [serwery procesów skalowanych w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file) za pomocą [wytycznych dotyczących rozmiaru](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Jeśli oczekujesz, że szybkość zmiany danych dla maszyny wirtualnej dysku przekroczy 2 MB/s, upewnij się, że używasz dysków zarządzanych w wersji premium. Planer wdrażania odzyskiwania lokacji działa przez określony czas. Szczyty szybkości zmiany danych w innych okresach mogą nie zostać przechwycone w raporcie.
5. [Ustaw przepustowość sieci](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) na podstawie celu celowego, który chcesz osiągnąć.
6. Po skonfigurowaniu infrastruktury włącz odzyskiwanie po awarii dla obciążenia. Aby dowiedzieć się, jak to zrobić, zobacz [Konfigurowanie środowiska źródłowego replikacji VMware na platformie Azure.](vmware-azure-set-up-source.md)

## <a name="deploy-additional-process-servers"></a>Wdrażanie dodatkowych serwerów procesów

Jeśli skalowanie w poziomie wdrożenia poza 200 komputerów źródłowych lub jeśli masz całkowity dzienny współczynnik zmian więcej niż 2 TB, należy dodać serwery procesów do obsługi woluminu ruchu. Ulepszyliśmy produkt w wersji 9.24, aby zapewnić [alerty serwera procesów dotyczące](vmware-physical-azure-monitor-process-server.md#process-server-alerts) tego, kiedy skonfigurować serwer procesów skalowanych w poziomie. [Skonfiguruj serwer przetwarzania,](vmware-azure-set-up-process-server-scale.md) aby chronić nowe maszyny źródłowe lub [równoważyć obciążenie](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrowanie komputerów w celu korzystania z nowego serwera przetwarzania

1. Wybierz **pozycję Ustawienia** > **serwerów odzyskiwania witryny**. Wybierz serwer konfiguracji, a następnie rozwiń **pozycję Serwery przetwarzania**.

    ![Zrzut ekranu przedstawiający okno dialogowe Serwer przetwarzania](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kliknij prawym przyciskiem myszy aktualnie używany serwer przetwarzania, a następnie wybierz polecenie **Przełącz**.

    ![Zrzut ekranu przedstawiający okno dialogowe Serwer konfiguracji](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. W **obszarze Wybierz docelowy serwer przetwarzania**wybierz nowy serwer przetwarzania, którego chcesz użyć. Następnie wybierz maszyny wirtualne, które serwer będzie obsługiwać. Aby uzyskać informacje o serwerze, wybierz ikonę informacji. Aby ułatwić podejmowanie decyzji o obciążeniu, wyświetlana jest średnia ilość miejsca wymagana do replikacji każdej wybranej maszyny wirtualnej na nowy serwer przetwarzania. Zaznacz znacznik wyboru, aby rozpocząć replikację na nowy serwer przetwarzania.

## <a name="deploy-additional-master-target-servers"></a>Wdrażanie dodatkowych głównych serwerów docelowych

W następujących scenariuszach wymagany jest więcej niż jeden główny serwer docelowy:

*   Chcesz chronić maszynę wirtualną opartą na systemie Linux.
*   Główny serwer docelowy dostępny na serwerze konfiguracji nie ma dostępu do magazynu danych maszyny Wirtualnej.
*   Całkowita liczba dysków na głównym serwerze docelowym (liczba dysków lokalnych na serwerze oraz liczba dysków, które mają być chronione) jest większa niż 60 dysków.

Aby dowiedzieć się, jak dodać główny serwer docelowy dla maszyny wirtualnej opartej na systemie Linux, zobacz [Instalowanie głównego serwera docelowego systemu Linux w celu powrotu po awarii](vmware-azure-install-linux-master-target.md).

Aby dodać główny serwer docelowy dla maszyny wirtualnej opartej na systemie Windows:

1. Przejdź do**serwerów konfiguracji**infrastruktury odzyskiwania**lokacji** >  **usługi Vault .** > 
2. Wybierz wymagany serwer konfiguracji, a następnie wybierz **główny serwer docelowy**.

    ![Zrzut ekranu przedstawiający przycisk Dodaj główny serwer docelowy](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Pobierz ujednolicony plik instalacyjny, a następnie uruchom plik na maszynie Wirtualnej, aby skonfigurować główny serwer docelowy.
4. Wybierz **pozycję Zainstaluj główny cel** > **Dalej**.

    ![Zrzut ekranu przedstawiający wybranie opcji Zainstaluj główny cel](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Wybierz domyślną lokalizację instalacji, a następnie wybierz pozycję **Zainstaluj**.

     ![Zrzut ekranu przedstawiający domyślną lokalizację instalacji](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Aby zarejestrować główny obiekt docelowy na serwerze konfiguracji, wybierz opcję **Przejdź do konfiguracji**.

    ![Zrzut ekranu przedstawiający przycisk Przejdź do konfiguracji](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Wprowadź adres IP serwera konfiguracji, a następnie wprowadź hasło. Aby dowiedzieć się, jak wygenerować hasło, zobacz [Generowanie hasła serwera konfiguracji](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Zrzut ekranu, który pokazuje, gdzie wprowadzić adres IP i hasło dla serwera konfiguracji](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Wybierz pozycję **Zarejestruj**. Po zakończeniu rejestracji wybierz pozycję **Zakończ**.

Po pomyślnym zakończeniu rejestracji serwer jest wyświetlany w portalu Azure w **witrynie** > Recovery**Services Site Recovery Infrastructure** > **Configuration servery**, na głównych serwerach docelowych serwera konfiguracji.

 > [!NOTE]
 > Pobierz najnowszą wersję [głównego pliku instalacyjnego serwera docelowego dla systemu Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Następne kroki

Pobierz i uruchom [Planer wdrażania odzyskiwania witryny](https://aka.ms/asr-deployment-planner).
