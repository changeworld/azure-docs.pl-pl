---
title: Usługa Azure Site Recovery dotyczące rozwiązywania problemów w trwające problemy z replikacją Azure – Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów i błędów podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: bf24b2d1395e128dc73361670ea93ac938574146
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258784"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Trwającą Rozwiązywanie problemów z w replikacji maszyn wirtualnych platformy Azure do platformy Azure

W tym artykule opisano typowe problemy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu. Wyjaśniono również sposób rozwiązać ten problem. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

Usługa Azure Site Recovery stale replikuje dane w regionie źródłowym w regionie odzyskiwania po awarii i tworzy punkt odzyskiwania spójnego na poziomie awarii, co 5 minut. Jeśli usługa Site Recovery nie może utworzyć punktów odzyskiwania na 60 minut, ostrzega użytkownika z tymi informacjami:

Komunikat o błędzie: "Brak awarii spójne dostępnego punktu odzyskiwania dla maszyny Wirtualnej w ciągu ostatnich 60 minut."</br>
Identyfikator błędu: 153007 </br>

W poniższych sekcjach opisano przyczyny i potencjalne rozwiązania.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Współczynnik zmian danych na źródłowej maszynie wirtualnej
Usługa Azure Site Recovery uruchamia zdarzenie, jeśli współczynnik zmian danych na źródłowej maszynie wirtualnej jest większy niż obsługiwany limit. Aby sprawdzić, czy problem jest spowodowane dużą liczbą zmian, przejdź do **zreplikowane elementy** > **maszyny Wirtualnej** > **zdarzenia — ostatnie 72 godziny**.
Zdarzenia powinny zostać wyświetlone "Współczynnik wykracza poza obsługiwane limity zmian danych":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Jeśli wybierzesz zdarzenia, powinny zostać wyświetlone informacje o dysku:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych operacji We/Wy kombinacji aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji. 

Istnieją dwa ograniczenia należy wziąć pod uwagę, współczynnik zmian danych na dysku i współczynnik zmian danych maszyny wirtualnej. Na przykład Przyjrzyjmy się dysk Premium P20 w poniższej tabeli. Usługa Site Recovery może obsługiwać 5 MB/s o współczynniku dysku z maksymalnie pięć tych dysków na maszynę Wirtualną, ze względu na limit wynoszący 25 MB/s z łącznym dziennym współczynniku maszyny Wirtualnej.

**Cel magazynu replikacji** | **Średniego rozmiaru operacji We/Wy na dysk źródłowy** |**Średni współczynnik zmian danych dla dysku źródłowego** | **Łączna ilość danych, współczynnika zmian dziennie w przypadku dysk danych źródłowych**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |10 MB/s | 842 GB na dysk

### <a name="solution"></a>Rozwiązanie
Usługa Azure Site Recovery ma ograniczenia częstotliwości zmian danych, w zależności od typu dysku. Aby dowiedzieć się, jeśli ten problem dotyczy przechwytują lub cykliczne, znaleźć je zmienić częstotliwość dotyczy maszyny wirtualnej. Przejdź do źródłowej maszyny wirtualnej i metryki w obszarze **monitorowanie**i Dodaj metryki, jak pokazano w tym zrzucie ekranu:

![Trzy kroki proces służące do znajdowania współczynnik zmian danych](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Wybierz **Dodaj metrykę**i Dodaj **systemu operacyjnego zapisu Bajty dysku/s** i **danych zapisu Bajty dysku/s**.
2. Monitoruj kolekcji, jak pokazano na zrzucie ekranu.
3. Widok Suma zapisu operacje wykonywane przez dyski systemu operacyjnego i połączyć wszystkie dyski z danymi. Te metryki może nie dać użytkownikowi informacje na poziomie na dysku, ale wskazują wzorzec łączny współczynnik zmian danych.

W przypadku kolekcji jest ze sporadycznymi danych serii i danych zmiany stawka jest większa niż 10 MB/s (dla warstwy Premium) oraz 2 MB/s (dla warstwy standardowa) dla niektórych czasu i sprowadza się, zostanie nadrób zaległości replikacji. Ale jeśli zmian poza obsługiwane ograniczenie w większości przypadków, należy wziąć pod uwagę jedną z tych opcji, jeśli jest to możliwe:

* **Wykluczanie dysku, który powoduje współczynnik zmian danych o wysokiej**: Dysk można wykluczyć za pomocą [PowerShell](./azure-to-azure-exclude-disks.md). Aby wykluczyć dysk, musisz najpierw wyłącz replikację. 
* **Zmień warstwę dysk magazynujący odzyskiwania po awarii**: Ta opcja jest możliwe tylko w przypadku zmian danych na dysku jest mniejsza niż 10 MB/s. Załóżmy, że Maszynę wirtualną z dyskiem P10 ma wartość współczynnika zmian danych przekracza 8 MB/s, ale mniej niż 10 MB/s. Jeśli odbiorca może używać dysk P30 dla magazynu docelowego podczas ochrony, problem może zostać rozwiązany.

## <a name="Network-connectivity-problem"></a>Problemy z łącznością sieciową

### <a name="network-latency-to-a-cache-storage-account"></a>Opóźnienie sieci z kontem magazynu pamięci podręcznej
Usługa Site Recovery wysyła dane replikowane do konta magazynu pamięci podręcznej. Może pojawić się opóźnienie sieci, w przypadku przekazywania danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej jest mniejsza niż 4 MB w 3 sekundy. 

Aby sprawdzić, czy problem związany z opóźnieniem, użyj [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) do przekazania danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej. Opóźnienie jest wysoka, sprawdź, w przypadku korzystania z wirtualnego urządzenia sieciowego (WUS) do kontrolowania wychodzącego ruchu sieciowego z maszynami wirtualnymi. Urządzenie może być ograniczona w przypadku wszystkich ruch związany z replikacją przechodzi przez urządzenia WUS. 

Zaleca się utworzenie punktu końcowego usługi sieci w sieci wirtualnej na "Magazyn", aby ruch związany z replikacją nie prowadzi do urządzenia WUS. Aby uzyskać więcej informacji, zobacz [konfiguracji urządzenia wirtualnego sieci](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Połączenie sieciowe
W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy. Aby upewnić się, wszystkie adresy URL są połączone, zobacz [połączenia ruchu wychodzącego dla adresów URL Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Identyfikator błędu 153006 — Brak punktu odzyskiwania spójnego na poziomie aplikacji dostępnego dla maszyny Wirtualnej w ciągu ostatnich minut "XXX"

Niektóre z najczęściej spotykanych problemów zostały wymienione poniżej

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Przyczyny 1: Znany problem w programie SQL server 2008/2008 R2 
**Jak naprawić** : Jest to znany problem z programem SQL server 2008/2008 R2. Można znaleźć w tym artykule KB [agenta usługi Azure Site Recovery lub inne usługi VSS nie będącego komponentem kopii zapasowej kończy się niepowodzeniem dla serwera hostingu SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>Przyczyny 2: Zadania usługi Azure Site Recovery zakończyć się niepowodzeniem na serwerach hostujących dowolna wersja wystąpienia programu SQL Server przy użyciu AUTO_CLOSE baz danych 
**Jak naprawić** : Zapoznaj się z bazy wiedzy [artykułu](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Przyczyny 3: Znany problem w programie SQL Server 2016 oraz 2017 r.
**Jak naprawić** : Zapoznaj się z bazy wiedzy [artykułu](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Przyczyna 4: W przypadku korzystania z konfiguracji usługi bezpośrednie miejsca do magazynowania magazynu
**Jak naprawić** : Usługa Azure Site Recovery nie można utworzyć punktu odzyskiwania spójnego na poziomie aplikacji dla magazynu konfiguracji usługi bezpośrednie miejsca do magazynowania. Zapoznaj się artykułem, aby poprawnie [Konfigurowanie zasad replikacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-due-to-vss-related-issues"></a>Problemy związane z innych przyczynach z powodu usługi VSS:

Aby dalej rozwiązywać, sprawdź pliki na maszynie źródłowej można uzyskać dokładny błąd kod błędu:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak znaleźć błędy w pliku?
Wyszukaj ciąg "vacpError", otwierając plik vacp.log w edytorze
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

W powyższym przykładzie **2147754994** jest kod błędu, który informuje o błędzie, jak pokazano poniżej

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Składnik zapisywania usługi VSS nie jest zainstalowany - 2147221164 błąd 

*Jak naprawić*: Aby wygenerować tagu spójności aplikacji, usługi Azure Site Recovery używa kopii w tle woluminu Microsoft Service (VSS). Instaluje dostawcę VSS do swoich operacji do robienia migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. W przypadku, gdy usługa dostawcy usługi VSS nie jest zainstalowana, tworzenia migawek spójności aplikacji nie powiedzie się z Identyfikator błędu: 0x80040154 "Klasa nie zarejestrowana". </br>
Zapoznaj się [artykuł dotyczący rozwiązywania problemów instalacji składnika zapisywania usługi VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Składnik zapisywania usługi VSS jest wyłączone — błąd 2147943458

**Jak naprawić**: Aby wygenerować tagu spójności aplikacji, usługi Azure Site Recovery używa kopii w tle woluminu Microsoft Service (VSS). Instaluje dostawcę VSS do swoich operacji do robienia migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. W przypadku, gdy usługa dostawcy usługi VSS jest wyłączona, tworzenia migawek spójności aplikacji nie powiedzie się o identyfikatorze błędu "określona usługa jest wyłączona i nie może być started(0x80070422)". </br>

- Wyłączenie usługi VSS
    - Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest równa **automatyczne**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Dostawcy usługi VSS programu usługi Azure Site Recovery
        - Usługę VDS

####  <a name="vss-provider-notregistered---error-2147754756"></a>DOSTAWCY usługi VSS, NOT_REGISTERED — błąd 2147754756

**Jak naprawić**: Aby wygenerować tagu spójności aplikacji, usługi Azure Site Recovery używa kopii w tle woluminu Microsoft Service (VSS). Sprawdź, czy usługa dostawcy usługi VSS usługi Azure Site Recovery jest zainstalowana, czy nie. </br>

- Ponów próbę instalacji dostawcy, używając następujących poleceń:
- Odinstaluj istniejącego dostawcy: C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Ponownie: C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest równa **automatyczne**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Dostawcy usługi VSS programu usługi Azure Site Recovery
        - Usługę VDS
