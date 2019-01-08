---
title: Planowanie wydajności i skalowanie na potrzeby odzyskiwania po awarii programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Skorzystaj z tego artykułu. Planowanie wydajności i skalowania, podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Site Recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: dc903fca206f5d40f631181b83252f505b9f57a2
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065214"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planowanie wydajności i skalowanie na potrzeby odzyskiwania po awarii programu VMware na platformę Azure

Użyj w tym artykule, aby ustalić planowania pojemności i skalowania podczas replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak rozpocząć planowanie pojemności

Aby dowiedzieć się wymagania infrastruktura usługi Azure Site Recovery, zebranie informacji o środowisku replikacji, uruchamiając [planista wdrażania usługi Azure Site Recovery](https://aka.ms/asr-deployment-planner-doc) potrzeby replikacji oprogramowania VMware. [Dowiedz się więcej](site-recovery-deployment-planner.md) na temat tego narzędzia. To narzędzie udostępnia raportu przy użyciu pełnych informacji o zgodnych i niezgodnych maszyn wirtualnych, dysków na maszynę Wirtualną, a współczynnik zmian danych na dysku. Narzędzie jest także podsumowanie wymagania dotyczące przepustowości sieci w celu spełnienia docelowego cel punktu odzyskiwania i infrastruktury platformy Azure potrzebne do pomyślnej replikacji i testowania trybu failover.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności

**Składnik** | **Szczegóły** |
--- | --- | ---
**Replikacja** | **Maksymalna dziennych zmian:** Chronione maszyny można używać tylko jednego serwera przetwarzania i serwer pojedynczego procesu może obsługiwać dzienny zmiany szybkości do 2 TB. Ten sposób 2 TB jest maksymalny Dzienny współczynnik zmian danych, która jest obsługiwana dla chronionej maszyny.<br/><br/> **Maksymalna przepływność:** Replikowanej maszyny mogą należeć do jednego konta magazynu na platformie Azure. Standardowe konto magazynu może obsługiwać maksymalnie 20 000 żądań na sekundę, a firma Microsoft zaleca, Zmniejsz liczbę operacji wejścia/wyjścia na sekundę (IOPS) na maszynie źródłowej do 20 000. Na przykład jeśli masz maszyny źródłowej, z dyskami 5, a każdy dysk generuje 120 na SEKUNDĘ (8K rozmiar) na maszynie źródłowej, następnie będzie w obrębie platformy Azure limit operacji We/Wy dysku 500. (Liczba kont magazynu wymagana jest taki sam na maszynie źródłowej łączna liczba operacji We/Wy, podzielona przez 20 000).
**Serwer konfiguracji** | Serwer konfiguracji powinien być w stanie obsłużyć dzienną wydajność współczynnika zmian we wszystkich obciążeń uruchomionych na chronionych maszynach i wymaga wystarczającą przepustowość, aby stale replikować dane do usługi Azure Storage.<br/><br/> Najlepszym rozwiązaniem jest zlokalizować serwer konfiguracji na tej samej sieci oraz na segment sieci LAN jako maszyn, które mają być chronione. Mogą znajdować się w innej sieci, ale maszyn, które mają być chronione, powinny mieć widoczność sieci 3 warstwy do niego.<br/><br/> Zalecenia dotyczące rozmiaru serwera konfiguracji są podsumowane w tabeli w poniższej sekcji.
**Serwer przetwarzania** | Pierwszy serwer przetwarzania jest instalowany domyślnie na serwerze konfiguracji. Można wdrażać dodatkowych serwerów przetwarzania do skalowania środowiska. <br/><br/> Serwer przetwarzania odbiera dane replikacji z chronionych maszyn i optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania. Następnie wysyła dane do platformy Azure. Maszyna serwera przetwarzania powinna mieć wystarczające zasoby do wykonywania tych zadań.<br/><br/> Serwer przetwarzania korzysta z pamięci podręcznej opartej na dyskach. Do obsługi zmian danych przechowywanych w przypadku awarii lub wąskich gardeł, należy użyć dysku oddzielne pamięci podręcznej 600 GB lub więcej.

## <a name="size-recommendations-for-the-configuration-server-along-with-in-built-process-server"></a>Zalecenia dotyczące rozmiaru serwera konfiguracji (wraz z w wbudowanym serwerem przetwarzania)

Każdy serwer configuration wdrażanych za pośrednictwem [szablonu pakietu OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) z serwerem przetwarzania wbudowanych. Zasoby serwera konfiguracji, takich jak procesor CPU, pamięci, miejsca są wykorzystywane różne stawki, gdy serwer przetwarzania wbudowanych jest wykorzystywany do ochrony maszyn wirtualnych. Z tego powodu wymagania różnią się, gdy jest wykorzystywany przez serwer przetwarzania wbudowanych.
Serwer konfiguracji, w którym serwer przetwarzania wbudowanych służy do ochrony obciążeń może obsługiwać maksymalnie 200 maszyn wirtualnych, w oparciu o następujące konfiguracje

**CPU** | **Pamięć** | **Rozmiar dysku w pamięci podręcznej** | **Współczynnik zmian danych** | **Chronione maszyny**
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz [GHz]) | 16 GB | 300 GB | 500 GB lub mniej | Replikowanie maszyn mniej niż 100.
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | Od 500 GB do 1 TB | Replikacja między maszynami 100 150.
16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni \@ 2,5 GHz) | 32 GB | 1 TB | 1 TB do 2 TB | Replikacja między maszynami 150 – 200.
Wdrażanie na inny serwer konfiguracji za pośrednictwem [szablonu pakietu OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) | | | | Jeśli replikujesz ponad 200 maszyn, należy wdrożyć nowy serwer konfiguracji.
Wdrażanie drugiego [serwera przetwarzania](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | &GT; 2 TB| Wdrażanie nowego serwera przetwarzania skalowalnego w poziomie, jeśli całkowity Dzienny współczynnik zmian danych przekracza 2 TB.

Gdzie:

* Każda maszyna źródłowa jest konfigurowana 3 dyskami 100 GB.
* Użyliśmy porównawczych magazyn 8 dysków SAS o wielkości 10 tys. obr/min, za pomocą RAID 10 dla miar dysk pamięci podręcznej.

## <a name="size-recommendations-for-the-process-server"></a>Zalecenia dotyczące rozmiaru serwera przetwarzania

Serwer przetwarzania jest składnikiem, który obsługuje proces replikacji danych w usłudze Azure Site Recovery. Jeśli dziennych zmian jest większa niż 2 TB, należy dodać serwery proces skalowania w poziomie, do obsługi obciążenia replikacji. Aby skalować, możesz wykonywać następujące czynności:

* Zwiększ liczbę serwerów konfiguracji, wdrażania za pośrednictwem [szablonu pakietu OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Na przykład można chronić maksymalnie 400 maszyn przy użyciu dwóch serwerów konfiguracji.
* Dodaj [serwerów przetwarzania skalowalnego w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file)i służą do obsługi ruchu związanego z replikacją zamiast (lub oprócz) na serwerze konfiguracji.

W poniższej tabeli opisano scenariusz, w którym:

* Po skonfigurowaniu serwera przetwarzania skalowalnego w poziomie.
* Skonfigurowano chronionych maszyn wirtualnych używających serwera przetwarzania skalowalnego w poziomie.
* Każda maszyna chronionego źródła jest skonfigurowany przy użyciu trzech dysków 100 GB.

**Dodatkowym serwerze przetwarzania** | **Rozmiar dysku w pamięci podręcznej** | **Współczynnik zmian danych** | **Chronione maszyny**
--- | --- | --- | ---
4 Vcpu (2 sockets * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci RAM | 300 GB | 250 GB lub mniej | Replikowanie maszyn 85 lub mniej.
8 wirtualnych procesorów CPU (2 sockets * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci RAM | 600 GB | 250 GB do 1 TB | Replikacja między maszynami 85 150.
12 procesorów wirtualnych Vcpu (2 sockets * 6 rdzeni \@ 2,5 GHz) 24 GB pamięci | 1 TB | 1 TB do 2 TB | Replikacja między maszynami 150 225.

Sposób, w którym możesz skalować serwery w zależy od preferencjami dla modelu, skalowanie w pionie lub poziomie.  Skalowanie w górę, wdrażając kilka zaawansowanych konfiguracji i serwerów przetwarzania lub skalowania w poziomie poprzez wdrożenie kolejnych serwerów przy użyciu mniejszej ilości zasobów. Na przykład jeśli musisz chronić 200 maszyn z współczynnik zmian danych ogólną codziennie o rozmiarze 1,5 TB, wykonaj jedną z następujących czynności:

* Skonfiguruj serwer pojedynczego procesu z procesora vCPU 16, 24 GB pamięci RAM.
* Należy skonfigurować dwa serwery przetwarzania, (2 x 8 procesorów vCPU, 2 * 12 GB pamięci RAM).

## <a name="control-network-bandwidth"></a>Sterowania przepustowością sieci

Po wykorzystano [narzędzia planista wdrażania](site-recovery-deployment-planner.md) do obliczania przepustowości, należy dla replikacji (Replikacja początkowa i następnie delta), można kontrolować przepustowość używaną podczas replikacji przy użyciu kilku opcji:

* **Ograniczanie przepustowości**: Ruch VMware są replikowane do platformy Azure przechodzi przez serwer określonego procesu. Możesz ograniczyć przepustowość na maszynach, uruchomione jako serwerów przetwarzania.
* **Mają wpływ na przepustowość**: Może mieć wpływ na przepustowość wykorzystywaną podczas replikacji przy użyciu kilku kluczy rejestru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** wartość rejestru określa liczbę wątków, które są używane do transferu danych (Replikacja początkowa lub przyrostowa) dysku. Wyższa wartość zwiększa przepustowość sieciową używaną podczas replikacji.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** określa liczbę wątków używanych do transferu danych podczas powrotu po awarii.

### <a name="throttle-bandwidth"></a>Ograniczanie przepustowości

1. Otwórz przystawkę MMC usługi Azure Backup na komputerze działającym jako serwer przetwarzania. Domyślnie skrót do usługi Backup jest dostępny na pulpicie lub w następującym folderze: Agent\bin C:\Program Files\Microsoft Azure Recovery Services.
2. W przystawce kliknij pozycję **Zmień właściwości**.

    ![Zrzut ekranu z usługi Azure Backup programu MMC przystawki opcję, aby zmienić właściwości](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **ograniczania** zaznacz **włączyć internetowe ograniczanie użycia przepustowości dla operacji tworzenia kopii zapasowej**. Ustaw limity dla pracy i innych niż godziny pracy. Prawidłowe zakresy są od 512 KB/s do 1023 MB/s na sekundę.

    ![Zrzut ekranu usługi Azure Backup właściwości, okno dialogowe](./media/site-recovery-vmware-to-azure/throttle2.png)

Możesz też użyć polecenia cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx), aby ustawić ograniczanie przepływności. Oto przykład:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** wskazuje, że ograniczanie przepływności nie jest wymagane.

### <a name="influence-network-bandwidth-for-a-vm"></a>Mają wpływ na przepustowość sieci dla maszyny Wirtualnej

1. W rejestrze maszyny Wirtualnej, przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Aby wpłynąć na przepustowość wykorzystywaną przez ruch na dysk replikujący, zmodyfikuj wartość **UploadThreadsPerVM**, lub Utwórz klucz Jeśli nie istnieje.
   * Aby wpłynąć na przepustowość ruchu powrotu po awarii z platformy Azure, zmodyfikuj wartość **DownloadThreadsPerVM**.
2. Wartość domyślna to 4. W sieci „o nadmiarowych zasobach” należy zmienić wartości domyślne tych kluczy rejestru. Wartość maksymalna to 32. Monitoruj ruch, aby zoptymalizować tę wartość.

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>Konfigurowanie infrastruktury usługi Azure Site Recovery w celu ochrony więcej niż 500 maszyn wirtualnych

Przed rozpoczęciem konfigurowania infrastruktury usługi Azure Site Recovery, musisz uzyskać dostęp do środowiska, aby zmierzyć od następujących czynników: zgodnych maszyn wirtualnych, danych dotyczących dziennego zmieniać szybkości, wymagana przepustowość sieci dla żądanego celu punktu odzyskiwania, liczba usługi Azure site recovery składniki wymagane, czas potrzebny do ukończenia replikacji początkowej itp.,

1. W celu mierzenia tych parametrów, upewnij się, że uruchamianie planisty wdrożenia w środowisku za pomocą wytycznych udostępnione [tutaj](site-recovery-deployment-planner.md).
2. Wdrażanie serwera konfiguracji za pomocą wspomniane powyżej wymogi. Jeśli obciążenie produkcyjne przekracza 650 maszyn wirtualnych, należy wdrożyć innym serwerze konfiguracji.
3. Oparte na mierzonego dziennych zmian danych, wdrażanie [serwerów przetwarzania skalowalnego w poziomie](vmware-azure-set-up-process-server-scale.md#download-installation-file) za pomocą wytycznych rozmiar wyrażam [tutaj](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Jeśli oczekujesz, współczynnik zmian danych dla maszyny wirtualnej z dyskiem mogłaby spowodować przekroczenie 2 MB/s, upewnij się, że [Konfigurowanie konta usługi premium storage](tutorial-prepare-azure.md#create-a-storage-account). Ponieważ planista wdrożenia jest uruchamiana dla określonego przedziału czasu, szczytowe danych zmiany stawki innym czasie, gdy okresy nie mogą być przechwytywane w raporcie.
5. Zgodnie z żądanym celem punktu odzyskiwania [Ustaw przepustowość sieci](site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
6. Po zakończeniu instalacji infrastruktury, postępuj zgodnie z zasadami, opublikowane w ramach [porad sekcji](vmware-azure-set-up-source.md) w celu umożliwienia działania funkcji odzyskiwania po awarii na obciążeniu.

## <a name="deploy-additional-process-servers"></a>Wdrażanie dodatkowych serwerów przetwarzania

W przypadku skalowania poza wdrożenia ponad 200 maszyn źródłowych, lub masz łącznie codziennie współczynnik więcej niż 2 TB do zmian danych, potrzebujesz dodatkowych serwerów przetwarzania do obsługi natężenia ruchu. Postępuj zgodnie z instrukcjami podanymi w [w tym artykule](vmware-azure-set-up-process-server-scale.md) do skonfigurowania serwera przetwarzania. Po skonfigurowaniu serwera, można migrować maszyny źródłowe z niego korzystać.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrowanie maszyn do użycia nowego serwera przetwarzania

1. W **ustawienia** > **serwerów Site Recovery**, kliknij serwer konfiguracji, a następnie rozwiń węzeł **przetwarzania serwerów**.

    ![Zrzut ekranu z serwera przetwarzania, okno dialogowe](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kliknij prawym przyciskiem myszy serwer przetwarzania, obecnie w użyciu, a następnie kliknij przycisk **przełącznika**.

    ![Zrzut ekranu konfiguracji serwera, okno dialogowe](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. W **wybierz docelowy serwer przetwarzania**, wybierz nowego serwera przetwarzania, którego chcesz użyć, a następnie wybierz maszyny wirtualne, które będzie obsługiwać serwer. Kliknij ikonę informacji, aby uzyskać informacje o serwerze. Aby ułatwić ładowanie decyzje, zostanie wyświetlona średniej ilości miejsca jest potrzebne do replikowania każdej wybranej maszyny wirtualnej do nowego serwera przetwarzania. Kliknij znacznik wyboru, aby rozpocząć replikowanie ich do nowego serwera przetwarzania.

## <a name="deploy-additional-master-target-servers"></a>Wdrażanie dodatkowych główne serwery docelowe

Będą potrzebne dodatkowe główny serwer docelowy podczas następujące scenariusze

1. Jeśli próbujesz chronić maszyny wirtualnej z systemem Linux.
2. Jeśli na głównym serwerze docelowym dostępne na serwerze konfiguracji nie ma dostępu do magazynu danych maszyny wirtualnej.
3. Jeśli łączna liczba dysków na główny serwer docelowy (nie. dyski lokalne na serwerze) i dyski, które mają być chronione przekracza 60 dysków.

Aby dodać nowy serwer główny serwer docelowy dla **maszyny wirtualnej z systemem Linux**, [tutaj](vmware-azure-install-linux-master-target.md).

Aby uzyskać **maszyny wirtualnej z systemem Windows**, wykonaj poniższe otrzymuje instrukcje.

1. Przejdź do **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
2. Kliknij serwer konfiguracji > **+ główny serwer docelowy**.![ Dodaj — wzorzec target-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Pobierz ujednoliconej konfiguracji i uruchom go na maszynie Wirtualnej, aby skonfigurować główny serwer docelowy.
4. Wybierz **głównego elementu docelowego instalacji** > **dalej**. ![Wybierz MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Domyślna lokalizacja instalacji wybierz > kliknij **zainstalować**. ![Instalacja MT](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Kliknij pozycję **przejść do konfiguracji** zarejestrować główny serwer docelowy z serwerem konfiguracji. ![MT-kontynuować configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Wprowadź adres IP serwera konfiguracji i hasło. [Kliknij tutaj,](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) dowiesz się, jak można wygenerować hasło.![ CS-ip hasło](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Kliknij przycisk **zarejestrować** i kliknij wpis rejestracji **Zakończ**.
9. Po pomyślnej rejestracji tego serwera znajduje się w portalu w obszarze **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery** > **konfiguracji serwery** > główny serwer docelowy w konfiguracji związanych z serwera.

 >[!NOTE]
 >Możesz również pobrać najnowszą wersję programu Master target server ujednoliconego konfiguracji dla Windows [tutaj](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Kolejne kroki

Pobierz i uruchom [planista wdrażania usługi Azure Site Recovery](https://aka.ms/asr-deployment-planner)
