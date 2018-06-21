---
title: Planowanie wydajności i skalowania dla replikacji maszyn wirtualnych VMware do platformy Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Użyj w tym artykule Planowanie wydajności i skalowania podczas replikowania maszyn wirtualnych VMware do platformy Azure z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rayne
ms.openlocfilehash: 30e4534fbc235a228ac887ddc3336f09909b4fa6
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287358"
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planowanie wydajności i skalowania w przypadku replikacji maszyn wirtualnych VMware z usługą Azure Site Recovery

Użyj w tym artykule, aby zorientować się planowanie pojemności i skalowanie podczas replikowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure z [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak rozpocząć planowanie pojemności

Zbierz informacje o środowisku replikacji, uruchamiając [Azure lokacji odzyskiwania wdrożenia Planistę](https://aka.ms/asr-deployment-planner-doc) dla replikacji maszyn wirtualnych VMware. [Dowiedz się więcej](site-recovery-deployment-planner.md) na temat tego narzędzia. Będzie zbierać informacje o zgodnych i niezgodnych maszyn wirtualnych, dysków dla maszyny Wirtualnej, a danych churn — na dysku. Narzędzie uwzględnia również wymagania dotyczące przepustowości sieci i infrastruktury platformy Azure potrzebnej do pomyślnej replikacji i testowania trybu failover.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności

**Składnik** | **Szczegóły** |
--- | --- | ---
**Replikacja** | **Maksymalna szybkość codziennych zmian:** chronionej maszyny można używać tylko jednego serwera przetwarzania i serwer pojedynczego procesu może obsłużyć dziennym zmiany szybkości maksymalnie 2 TB. W związku z tym 2 TB jest maksymalną codziennych danych zmienić szybkość, z której jest obsługiwana dla komputera chronionego.<br/><br/> **Maksymalna przepustowość:** zreplikowanej maszyny mogą należeć do jednego konta magazynu na platformie Azure. Konto magazynu w warstwie standardowa może obsługiwać maksymalnie 20 000 żądań na sekundę, i zaleca się utrzymać liczby operacji wejścia/wyjścia na sekundę (IOPS) dla maszyny źródłowej do 20 000. Na przykład jeśli masz maszyny źródłowej z dyskami 5, a każdy dysk generuje 120 IOPS (rozmiarze 8 KB) na maszynie źródłowej, następnie będzie w obrębie platformy Azure limitu IOPS dysku 500. (Liczba kont magazynu wymagana jest równa maszyny źródłowej całkowita IOPS, podzielona przez 20 000).
**Serwer konfiguracji** | Serwer konfiguracji powinno być możliwe do obsługi codziennych pojemności szybkość zmian we wszystkich obciążeń uruchomionych na chronionych komputerach i musi wystarczającą przepustowość do ciągłej replikacji danych do usługi Azure Storage.<br/><br/> Najlepszym rozwiązaniem zlokalizować serwera konfiguracji na tej samej sieci, a segment sieci LAN jako maszyn, które chcesz chronić. Może znajdować się w innej sieci, ale maszyny, które chcesz chronić powinny mieć widoczność warstwy 3 sieci do niego.<br/><br/> Rozmiar zalecenia dotyczące serwera konfiguracji są podsumowane w tabeli w następnej sekcji.
**Serwer przetwarzania** | Pierwszy serwer przetwarzania jest instalowany domyślnie na serwerze konfiguracji. Można wdrożyć serwery dodatkowych procesów do skalowania środowiska. <br/><br/> Serwer przetwarzania odbiera dane replikacji z chronionych maszyn i optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania. Następnie wysyła dane na platformie Azure. Proces maszyny serwera ma wystarczające zasoby, aby wykonać te zadania.<br/><br/> Serwer przetwarzania korzysta z pamięci podręcznej opartej na dysku. Użyj dysku pamięci podręcznej oddzielne 600 GB lub więcej do obsługi zmian danych przechowywanych w przypadku awarii lub wąskich gardeł.

## <a name="size-recommendations-for-the-configuration-server"></a>Rozmiar zalecenia dotyczące serwera konfiguracji

**CPU** | **Pamięci** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz [GHz]) | 16 GB | 300 GB | 500 GB lub mniej | Replikowanie maszyn mniej niż 100.
12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) | 18 GB | 600 GB | 500 GB do 1 TB | Replikują między 100 150 maszyn.
16 Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz) | 32 GB | 1 TB | 1 TB do 2 TB | Replikują między 150 – 200 maszyn.
Wdrażanie inny serwer przetwarzania | | | > 2 TB | Wdrażanie serwerów dodatkowych procesów, Jeśli replikujesz ponad 200 maszyn lub zmiana codziennych danych szybkość przekracza 2 TB.

Gdzie:

* Każda maszyna źródłowa jest skonfigurowany z 3 dyski 100 GB.
* My używamy najlepszymi magazynu 8 dysków SAS 10 k obr. / min, z RAID 10 dla pamięci podręcznej dysku pomiarów.

## <a name="size-recommendations-for-the-process-server"></a>Zalecenia dotyczące rozmiaru serwera przetwarzania

Jeśli musisz ochrony ponad 200 maszyn lub szybkość codziennych zmian jest większa niż 2 TB, można dodać procesu serwerów do obsługi obciążenia replikacji. Aby skalować w poziomie, można:

* Zwiększ liczbę serwerów konfiguracji. Na przykład można chronić maksymalnie 400 maszyn przy użyciu dwóch serwerów konfiguracji.
* Dodawanie kolejnych serwerów procesu i ich używać do obsługi ruchu zamiast (lub oprócz) serwera konfiguracji.

W poniższej tabeli opisano scenariusz, w którym:

* Nie planujesz użyć konfiguracji serwera jako serwera przetwarzania.
* Po skonfigurowaniu serwera dodatkowego przetwarzania.
* Skonfigurowano chronionych maszyn wirtualnych do używania serwera dodatkowych procesów.
* Każda maszyna chronionego źródła jest skonfigurowany z trzech dysków 100 GB.

**Serwer konfiguracji** | **Serwer przetwarzania dodatkowe** | **Rozmiar pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Chronione maszyny**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz), 16 GB pamięci | 4 Vcpu (2 sockets * 2 rdzenie @ 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Replikowanie maszyn 85 lub mniej.
8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz), 16 GB pamięci | 8 Vcpu (2 sockets * 4 rdzenie @ 2,5 GHz), 12 GB pamięci RAM | 600 GB | 250 GB do 1 TB | Replikują między 85 150 maszyny.
12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz), 18 GB pamięci | 12 Vcpu (2 sockets * 6 rdzeni @ 2,5 GHz) 24 GB pamięci | 1 TB | 1 TB do 2 TB | Replikują między 150 225 komputerów.

Sposób, w którym można skalować serwery zależy od swoich preferencji modelu skalowania w górę i skalowania w poziomie.  Skalowanie w górę przez wdrożenie kilku konfiguracje i serwerów procesu lub skalowanie w poziomie przez wdrożenie więcej serwerów z mniejszą liczbą zasobów. Na przykład jeśli musisz chronić 220 maszyny, wykonaj jedną z następujących czynności:

* Konfigurowanie serwera konfiguracji z 12 vCPU, 18 GB pamięci, a serwerem dodatkowych procesów z 12 vCPU, 24 GB pamięci. Skonfiguruj chronione maszyny do korzystania z serwera dodatkowych procesów.
* Skonfiguruj dwa serwery konfiguracji (vCPU, 16 GB pamięci RAM 2 x 8) i dwa serwery dodatkowych procesów (vCPU 1 x 8 i 4 maszyny vCPU x 1 do uchwytu 135 + 85 [220]). Skonfiguruj chronione maszyny, aby korzystały z serwerów dodatkowych procesów.


## <a name="control-network-bandwidth"></a>Sterowania przepustowością sieci

Po używano [narzędzie wdrożenia Planistę](site-recovery-deployment-planner.md) do obliczania wartości przepustowości wymagane dla replikacji (Replikacja początkowa i następnie delta) można kontrolować, ilość przepustowości używanej w ramach replikacji przy użyciu kilku opcji:

* **Ograniczanie przepustowości**: VMware ruchu, który replikuje Azure przechodzi przez serwer określonego procesu. Możesz ograniczyć przepustowość na maszynach działających jako serwery procesu.
* **Wpływ przepustowości**: może mieć wpływ na przepustowość wykorzystywaną podczas replikacji przy użyciu kilku kluczy rejestru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** wartość rejestru określa liczbę wątków, które są używane do transferu danych (Replikacja początkowa lub przyrostowa) dysku. Wyższa wartość zwiększa przepustowość sieciową używaną podczas replikacji.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** określa liczbę wątków używanych do transferu danych podczas powrotu po awarii.

### <a name="throttle-bandwidth"></a>Ograniczanie przepustowości

1. Otwieranie przystawki MMC kopia zapasowa Azure na komputerze działającym jako serwer przetwarzania. Domyślnie skrót do utworzenia kopii zapasowej jest dostępny na pulpicie lub w następującym folderze: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. W przystawce kliknij pozycję **Zmień właściwości**.

    ![Zrzut ekranu Azure Backup MMC przystawki opcję, aby zmienić właściwości](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **ograniczania** wybierz opcję **włączyć użycia przepustowości połączenia internetowego do operacji tworzenia kopii zapasowej**. Ustaw limity dla pracy oraz innych niż godziny pracy. Prawidłowe zakresy są od 512 KB/s do 1023 MB/s na sekundę.

    ![Okno dialogowe zrzut ekranu Azure Backup właściwości](./media/site-recovery-vmware-to-azure/throttle2.png)

Możesz też użyć polecenia cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx), aby ustawić ograniczanie przepływności. Oto przykład:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** wskazuje, że ograniczanie przepływności nie jest wymagane.

### <a name="influence-network-bandwidth-for-a-vm"></a>Wpływ na przepustowość sieci dla maszyny Wirtualnej

1. W rejestrze maszyny Wirtualnej, przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Wpływ na przepustowość ruchu replikacji dysku, zmodyfikuj wartość **UploadThreadsPerVM**, lub Utwórz klucz, jeśli nie istnieje.
   * Wpływ przepustowości dla ruchu powrotu po awarii z platformy Azure, zmodyfikuj wartość **DownloadThreadsPerVM**.
2. Wartość domyślna to 4. W sieci „o nadmiarowych zasobach” należy zmienić wartości domyślne tych kluczy rejestru. Wartość maksymalna to 32. Monitoruj ruch, aby zoptymalizować tę wartość.


## <a name="deploy-additional-process-servers"></a>Wdrażanie serwerów dodatkowych procesów

Jeśli masz skalowania mieszczą się w danym wdrożeniu ponad 200 maszyn źródłowych, lub łączna codziennie churn — liczba więcej niż 2 TB, potrzebujesz dodatkowych procesów serwerów do obsługi natężenia ruchu. Wykonaj te instrukcje, aby skonfigurować serwer przetwarzania. Po skonfigurowaniu serwera, można dokonać migracji maszyn źródłowych z niego korzystać.

1. W **serwerów usługi Site Recovery**, kliknij serwer konfiguracji, a następnie kliknij przycisk **serwera przetwarzania**.

    ![Zrzut ekranu usługi Site Recovery serwerów opcję, aby dodać serwer przetwarzania](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. W **typ serwera**, kliknij przycisk **serwera przetwarzania (lokalnego)**.

    ![Zrzut ekranu serwera przetwarzania, okno dialogowe](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Pobierz plik Instalatora Unified usługi Site Recovery, a następnie uruchom go, aby zainstalować serwer przetwarzania. To również rejestruje go w magazynie.
4. W obszarze **Przed rozpoczęciem** wybierz pozycję **Dodaj dodatkowe serwery przetwarzania w celu zwiększenia skali wdrożenia**.
5. Ukończ pracę kreatora w taki sam sposób jak podczas możesz [Konfigurowanie](#step-2-set-up-the-source-environment) serwera konfiguracji.

    ![Zrzut ekranu z Instalatora Azure Site Recovery Unified Kreatora](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. W **szczegóły konfiguracji serwera**, podaj adres IP serwera konfiguracji i hasło. Aby uzyskać hasło, uruchom **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** na serwerze konfiguracji.

    ![Zrzut ekranu konfiguracji szczegóły serwera strony](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migruj maszyny, aby użyć nowego serwera przetwarzania
1. W **ustawienia** > **serwerów usługi Site Recovery**, kliknij serwer konfiguracji, a następnie rozwiń węzeł **przetworzyć serwerów**.

    ![Zrzut ekranu serwera przetwarzania, okno dialogowe](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kliknij prawym przyciskiem myszy serwer przetwarzania aktualnie w użyciu, a następnie kliknij przycisk **przełącznika**.

    ![Zrzut ekranu konfiguracji serwera, okno dialogowe](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. W **wybierz docelowy serwer przetwarzania**, wybierz nowego serwera przetwarzania, którego chcesz użyć, a następnie wybierz maszyny wirtualne, które będzie obsługiwać serwer. Kliknij ikonę informacji, aby uzyskać informacje o serwerze. Pomaga załadować decyzji, zostanie wyświetlony średni ilości miejsca, która jest potrzebna do replikowania każdego wybraną maszynę wirtualną do nowego serwera przetwarzania. Kliknij znacznik wyboru, aby rozpocząć replikację do nowego serwera przetwarzania.


## <a name="next-steps"></a>Kolejne kroki

Pobierz i uruchom [Azure lokacji odzyskiwania wdrożenia Planistę](https://aka.ms/asr-deployment-planner)
