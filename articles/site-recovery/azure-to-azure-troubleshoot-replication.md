---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure za pomocą usługi Azure Site Recovery
description: Rozwiązywanie problemów z replikacją w przypadku odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 8cba02d3c7d1e649853570b199b646b1c4dcce2d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667414"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Rozwiązywanie problemów z replikacją w odzyskiwaniu po awarii maszyny Wirtualnej platformy Azure

W tym artykule opisano typowe problemy w usłudze Azure Site Recovery podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure (VM) z jednego regionu do innego regionu. Wyjaśniono również, jak rozwiązać typowe problemy. Aby uzyskać więcej informacji na temat obsługiwanych konfiguracji, zobacz [macierz obsługi replikacji maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

Usługa Azure Site Recovery konsekwentnie replikuje dane z regionu źródłowego do regionu odzyskiwania po awarii. Tworzy również punkt odzyskiwania spójne z awarią co 5 minut. Jeśli odzysk witryny nie może utworzyć punktów odzyskiwania przez 60 minut, powiadomi Cię o tym z tymi informacjami:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

W poniższych sekcjach opisano przyczyny i rozwiązania.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Wysoki współczynnik zmian danych na źródłowej maszynie wirtualnej

Usługa Azure Site Recovery tworzy zdarzenie, jeśli szybkość zmiany danych na źródłowej maszynie wirtualnej jest wyższa niż obsługiwane limity. Aby sprawdzić, czy problem jest z powodu wysokiej zmiany, przejdź do **replikowane elementy** > zdarzenia**maszyny Wirtualnej** > **- ostatnie 72 godziny**.
Powinieneś zobaczyć **zdarzenie Szybkość zmiany danych poza obsługiwanymi limitami:**

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Strona usługi Azure Site Recovery, która pokazuje wysoki wskaźnik zmiany danych, który jest zbyt wysoki.":::

Jeśli wybierzesz zdarzenie, powinieneś zobaczyć dokładne informacje o dysku:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Strona z informacjami o zdarzeniu szybkości zmiany danych.":::

### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Te limity są oparte na naszych testach, ale nie mogą obejmować wszystkich możliwych kombinacji wejścia i wyjścia (We/Wy) aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.

Istnieją dwa ograniczenia do rozważenia: zmiany danych na dysku i zmiany danych na maszynie wirtualnej. Spójrzmy na dysk Premium P20 w poniższej tabeli na przykład. W przypadku pojedynczej maszyny Wirtualnej odzyskiwanie witryny może obsługiwać 5 MB/s zmian na dysku z maksymalnie pięcioma takimi dyskami. Odzysk lokacji ma limit 25 MB/s całkowitej zmiany na maszynę wirtualną.

**Miejsce docelowe magazynu replikacji** | **Średni rozmiar we/wy dla dysku źródłowego** |**Średni współczynnik zmian danych dla dysku źródłowego** | **Całkowita liczba zmian danych dziennie dla źródłowego dysku danych**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

### <a name="solution"></a>Rozwiązanie

Usługa Azure Site Recovery ma limity szybkości zmiany danych, w zależności od typu dysku. Aby sprawdzić, czy ten problem jest powtarzający się lub tymczasowy, znajdź szybkość zmiany danych maszyny wirtualnej, którego dotyczy problem. Przejdź do źródłowej maszyny wirtualnej, znajdź metryki w obszarze **Monitorowanie**i dodaj metryki, jak pokazano na tym zrzucie ekranu:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Strona, która pokazuje trzyetapowy proces znajdowania szybkości zmiany danych.":::

1. Wybierz **pozycję Dodaj metrykę**i dodaj **bajty zapisu dysku systemu operacyjnego/s** i **bajty zapisu dysku danych/s**.
1. Monitoruj skok, jak pokazano na zrzucie ekranu.
1. Służy do wyświetlania wszystkich operacji zapisu na dyskach systemu operacyjnego i wszystkich połączonych dyskach z danymi. Te metryki mogą nie podawać informacji na poziomie na dysk, ale wskazują całkowity wzorzec zmian danych.

Skok szybkości zmiany danych może pochodzić z okazjonalnej serii danych. Jeśli szybkość zmiany danych jest większa niż 10 MB/s (dla wersji Premium) lub 2 MB/s (dla standardu) i spadnie, replikacja nadrobi zaległości. Jeśli współczynnik zmian jest stale znacznie powyżej obsługiwanego limitu, należy wziąć pod uwagę jedną z następujących opcji:

- Wyklucz dysk, który powoduje wysoką szybkość zmiany danych: Najpierw wyłącz replikację. Następnie można wykluczyć dysk za pomocą programu [PowerShell](azure-to-azure-exclude-disks.md).
- Zmień warstwę dysku magazynu odzyskiwania po awarii: Ta opcja jest możliwa tylko wtedy, gdy zmiana danych dysku jest mniejsza niż 20 MB/s. Na przykład maszyna wirtualna z dyskiem P10 ma współczynnik wydajności danych większy niż 8 MB/s, ale mniejszy niż 10 MB/s. Jeśli klient może użyć dysku P30 do przechowywania docelowego podczas ochrony, problem można rozwiązać. To rozwiązanie jest możliwe tylko dla komputerów korzystających z dysków zarządzanych w wersji Premium. Wykonaj następujące kroki:

  1. Przejdź do **folderu Dyski** replikowanego komputera, którego dotyczy problem, i skopiuj nazwę dysku repliki.
  1. Przejdź do tej repliki dysku zarządzanego.
  1. W **przeglądzie** może pojawić się baner z napisem, że został wygenerowany adres URL sygnatury dostępu Współdzielonego. Wybierz ten baner i anuluj eksport. Zignoruj ten krok, jeśli nie widzisz banera.
  1. Po odwołaniu adresu URL sygnatury dostępu Współdzielonego przejdź do **strony Konfiguracja** dysku zarządzanego. Zwiększ rozmiar, tak aby usługa Site Recovery obsługuje obserwowaną szybkość zmian na dysku źródłowym.

## <a name="network-connectivity-problems"></a>Problemy z łącznością sieciową

### <a name="network-latency-to-a-cache-storage-account"></a>Opóźnienie sieci na koncie magazynu pamięci podręcznej

Usługa Site Recovery wysyła zreplikowane dane do konta magazynu pamięci podręcznej. Opóźnienie sieci może wystąpić, jeśli przekazywanie danych z maszyny wirtualnej do konta magazynu pamięci podręcznej jest mniejsza niż 4 MB w 3 sekundy.

Aby sprawdzić, czy nie występuje problem związany z opóźnieniem, użyj [programu AzCopy](/azure/storage/common/storage-use-azcopy). Za pomocą tego narzędzia wiersza polecenia można przekazywać dane z maszyny wirtualnej na konto magazynu pamięci podręcznej. Jeśli opóźnienie jest wysokie, sprawdź, czy używasz sieciowego urządzenia wirtualnego (NVA) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych. Urządzenie może zostać ograniczone, jeśli cały ruch replikacji przechodzi przez urządzenie WUS.

Zaleca się utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla "Magazyn", aby ruch replikacji nie przechodził do urządzenia WUS. Aby uzyskać więcej informacji, zobacz [Konfiguracja urządzenia wirtualnego sieci](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Łączność sieciowa

Aby replikacja usługi Site Recovery działała, maszyna wirtualna musi zapewnić łączność wychodzącą z określonymi adresami URL lub zakresami adresów IP. Maszyna wirtualna może mieć za zaporą lub użyć reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej. Jeśli tak, mogą wystąpić problemy. Aby upewnić się, że wszystkie adresy URL są połączone, zobacz [Łączność wychodząca dla adresów URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Identyfikator błędu 153006 — brak punktu odzyskiwania spójnego z aplikacją dostępnego dla maszyny Wirtualnej w ostatnich minutach "X"

Oto niektóre z najczęstszych problemów.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Znany problem w programie SQL server 2008/2008 R2

**Jak naprawić:** Istnieje znany problem z programu SQL Server 2008/2008 R2. Zapoznaj się z artykułem [Agent odzyskiwania witryny Platformy Azure lub inna nieskszła kopia zapasowa VSS kończy się niepowodzeniem dla serwera obsługującego program SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Zadania odzyskiwania usługi Azure Site Recovery nie powiodą się na serwerach obsługujących dowolną wersję wystąpień programu SQL Server z AUTO_CLOSE DB

**Jak naprawić:** Zapoznaj się z artykułem [Nieskszadne kopie zapasowe vss, takie jak zadania usługi Azure Site Recovery kończą się niepowodzeniem na serwerach obsługujących wystąpienia programu SQL Server z AUTO_CLOSE DB](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Znany problem w programie SQL Server w wersjach 2016 i 2017

**Jak naprawić**: Zapoznaj się z artykułem [Aktualizacja zbiorcza 16 dla programu SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Używasz bezpośredniej konfiguracji usługi Azure Storage Spaces

**Jak naprawić:** Usługa Azure Site Recovery nie może utworzyć spójnego punktu odzyskiwania aplikacji dla bezpośredniej konfiguracji miejsca do magazynowania. [Skonfiguruj zasady replikacji](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="more-causes-because-of-vss-related-issues"></a>Więcej przyczyn z powodu problemów związanych z VSS:

Aby rozwiązać dalsze problemy, sprawdź pliki na komputerze źródłowym, aby uzyskać dokładny kod błędu dla błędu:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Aby zlokalizować błędy, otwórz plik _vacp.log_ w edytorze tekstu wyszukuj ciąg **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

W poprzednim przykładzie **2147754994** jest kod błędu, który informuje o awarii po tym zdaniu.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Moduł zapisujący usługi VSS nie jest zainstalowany — błąd 2147221164

**Jak naprawić:** Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS). Usługa Site Recovery instaluje dostawcę usługi VSS w celu jego działania w celu robienia migawek spójności aplikacji. Usługa Azure Site Recovery instaluje tego dostawcę usługi VSS jako usługę. Jeśli dostawca usługi VSS nie jest zainstalowany, tworzenie migawki spójności aplikacji kończy się niepowodzeniem. Pokazuje **błąd O identyfikatorze 0x80040154 Klasa nie jest zarejestrowana**. Zapoznaj się z artykułem rozwiązywania [problemów z instalacją modułu zapisującego usługi VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Moduł zapisujący usługi VSS jest wyłączony — błąd 2147943458

**Jak naprawić:** Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi VSS. Usługa Site Recovery instaluje dostawcę usługi VSS w celu jego działania w celu robienia migawek spójności aplikacji. Ten dostawca usługi VSS jest zainstalowany jako usługa. Jeśli nie masz włączonej usługi dostawcy usługi VSS, tworzenie migawki spójności aplikacji zakończy się niepowodzeniem. Pokazuje błąd: **Określona usługa jest wyłączona i nie można jej uruchomić (0x80070422)**.

Jeśli usługa VSS jest wyłączona:

- Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest ustawiony na **Automatyczny**.
- Uruchom ponownie następujące usługi:
  - usługa VSS.
  - Dostawca usług VSS usługi Azure Site Recovery.
  - usługi VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED dostawcy usługi VSS — błąd 2147754756

**Jak naprawić:** Aby wygenerować tag spójności aplikacji, usługa Azure Site Recovery używa usługi VSS. Sprawdź, czy jest zainstalowana usługa dostawcy usługi Azure Site Recovery VSS Provider.

Aby ponownie zainstalować dostawcę usługi VSS, użyj następujących poleceń:

1. Odinstaluj istniejącego dostawcę:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Zainstaluj ponownie dostawcę usługi VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Sprawdź, czy typ uruchamiania usługi dostawcy usługi VSS jest ustawiony na **Automatyczny**.

Uruchom ponownie następujące usługi:

- usługa VSS.
- Dostawca usług VSS usługi Azure Site Recovery.
- usługi VDS.
