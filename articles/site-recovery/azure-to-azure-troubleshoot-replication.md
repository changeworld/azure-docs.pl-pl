---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure za pomocą Azure Site Recovery
description: Rozwiązywanie problemów z replikacją w Azure Site Recovery ramach odzyskiwania po awarii maszyny wirtualnej platformy Azure
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: b8afdd0f2dd98260a628116fa7402e05cd39e06b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965854"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Rozwiązywanie problemów z replikacją w ramach odzyskiwania po awarii maszyny wirtualnej Azure

W tym artykule opisano typowe problemy w Azure Site Recovery w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Wyjaśniono również, jak rozwiązywać problemy. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery spójnie replikuje dane z regionu źródłowego do regionu odzyskiwania po awarii i tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut. Jeśli Site Recovery nie może utworzyć punktów odzyskiwania przez 60 minut, powiadamia o tych informacjach:

Komunikat o błędzie: "Brak punktu odzyskiwania spójnego na poziomie awarii dla maszyny wirtualnej w ciągu ostatnich 60 minut".</br>
Identyfikator błędu: 153007 </br>

W poniższych sekcjach opisano przyczyny i rozwiązania.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Wysoki współczynnik zmian danych na źródłowej maszynie wirtualnej
Azure Site Recovery uruchamia zdarzenie, jeśli współczynnik zmian danych na źródłowej maszynie wirtualnej jest większy niż obsługiwane limity. Aby sprawdzić, czy problem jest spowodowany dużą zmianą, przejdź do **pozycji zreplikowane elementy** > **VM** > **Events — ostatnie 72 godzin**.
Powinna zostać wyświetlona wartość "szybkość zmiany danych poza obsługiwanymi limitami":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

W przypadku wybrania zdarzenia powinny zostać wyświetlone dokładne informacje o dysku:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limity usługi Azure Site Recovery
W poniższej tabeli przedstawiono limity usługi Azure Site Recovery. Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.

Istnieją dwa limity, które należy wziąć pod uwagę, zmiany danych na dysk i zmiany danych na maszynę wirtualną. Na przykład przyjrzyjmy się dyskowi Premium P20 w poniższej tabeli. Site Recovery może obsłużyć 5 MB/s zmian na dysk z maksymalnie pięcioma dyskami na maszynę wirtualną, ze względu na limit 25 MB/s łącznego postępu na maszynę wirtualną.

**Cel magazynu replikacji** | **Średni rozmiar operacji we/wy dla dysku źródłowego** |**Średni współczynnik zmian danych na dysku źródłowym** | **Łączny współczynnik zmian danych dziennie dla dysku z danymi źródłowymi**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB  | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |  336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |10 MB/s | 842 GB na dysk

### <a name="solution"></a>Rozwiązanie
Usługa Azure Site Recovery ma limity współczynnika zmian danych, zależne od typu dysku. Aby dowiedzieć się, czy ten problem występuje cyklicznie, Znajdź wskaźnik zmian danych maszyny wirtualnej, której dotyczy problem. Przejdź do źródłowej maszyny wirtualnej, Znajdź metryki w obszarze **monitorowanie**i Dodaj metryki, jak pokazano na poniższym zrzucie ekranu:

![Proces z trzema krokami służący do znajdowania współczynnika zmian danych](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Wybierz pozycję **Dodaj metrykę**i Dodaj **Bajty zapisu dysku systemu operacyjnego/s** i **Bajty zapisu na dysku danych/s**.
2. Monitoruj skok, jak pokazano na zrzucie ekranu.
3. Wyświetlanie całkowitej liczby operacji zapisu wykonywanych między dyskami systemu operacyjnego a wszystkimi połączonymi dyskami danych. Te metryki mogą nie podawać informacji na poziomie dysku, ale wskazują łączny wzorzec zmian danych.

Jeśli skok jest z okazjonalnej serii danych, a szybkość zmiany danych jest większa niż 10 MB/s (w przypadku wersji Premium) i 2 MB/s (dla warstwy Standardowa) przez jakiś czas i zostanie wystawiona replikacja. Ale jeśli zmiany są znacznie poza obsługiwanym limitem, należy rozważyć jedną z tych opcji, jeśli jest to możliwe:

* **Wyklucz dysk powodujący wysoki współczynnik zmian danych**: możesz wykluczyć dysk przy użyciu [programu PowerShell](./azure-to-azure-exclude-disks.md). Aby wykluczyć dysk, na którym należy najpierw wyłączyć replikację.
* **Zmień warstwę dysku magazynu odzyskiwania po awarii**: Ta opcja jest możliwa tylko wtedy, gdy ilość danych na dysku jest mniejsza niż 20 MB/s. Załóżmy, że maszyna wirtualna z dyskiem P10 ma zmiany danych większe niż 8 MB/s, ale mniej niż 10 MB/s. Jeśli klient może korzystać z dysku P30 dla magazynu docelowego podczas ochrony, problem może zostać rozwiązany. Należy zauważyć, że to rozwiązanie jest możliwe tylko w przypadku maszyn korzystających z Managed Disks Premium. Wykonaj poniższe czynności:
    - Przejdź do bloku dyski zagrożonej replikowanej maszyny i skopiuj nazwę dysku repliki
    - Przejdź do tego dysku zarządzanego repliki
    - W bloku przeglądu może zostać wyświetlony transparent informujący o wygenerowaniu adresu URL sygnatury dostępu współdzielonego. Kliknij ten transparent i Anuluj eksport. Zignoruj ten krok, jeśli transparent nie jest widoczny.
    - Gdy tylko adres URL sygnatury dostępu współdzielonego zostanie odwołany, przejdź do bloku Konfiguracja dysku zarządzanego i Zwiększ rozmiar, tak aby Site Recovery obsługiwał częstotliwość zaobserwowanych zmian na dysku źródłowym

## <a name="Network-connectivity-problem"></a>Problemy z łącznością sieciową

### <a name="network-latency-to-a-cache-storage-account"></a>Opóźnienie sieci na konto magazynu pamięci podręcznej
Site Recovery wysyła zreplikowane dane na konto magazynu pamięci podręcznej. Opóźnienie sieci może pojawić się, jeśli przekazywanie danych z maszyny wirtualnej na konto magazynu pamięci podręcznej jest wolniejsze niż 4 MB w ciągu 3 sekund.

Aby sprawdzić, czy wystąpił problem związany z opóźnieniem, użyj [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) do przekazania danych z maszyny wirtualnej na konto magazynu pamięci podręcznej. Jeśli opóźnienie jest wysokie, sprawdź, czy używasz sieciowego urządzenia wirtualnego (urządzenie WUS) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych. Urządzenie może zostać ograniczone, jeśli cały ruch związany z replikacją przechodzi przez urządzenie WUS.

Zalecamy utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie przechodził do urządzenie WUS. Aby uzyskać więcej informacji, zobacz [Konfiguracja wirtualnego urządzenia sieciowego](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Łączność sieciowa
W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) do kontrolowania łączności wychodzącej, może to być przyczyną jednego z tych problemów. Aby upewnić się, że wszystkie adresy URL są połączone, zobacz [połączenia wychodzące dla adresów url Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Identyfikator błędu 153006 — Brak dostępnego na poziomie aplikacji punktu odzyskiwania dla maszyny wirtualnej w ciągu ostatnich "XXX" minut

Poniżej wymieniono niektóre z najczęstszych problemów

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Przyczyna 1: znany problem w programie SQL Server 2008/2008 R2
**Jak naprawić** : występuje znany problem z programem SQL Server 2008/2008 R2. Zapoznaj się z tym artykułem w bazie wiedzy [Azure Site Recovery agenta lub innej kopii zapasowej usługi VSS, która nie jest składnikiem, kończy się niepowodzeniem na serwerze 2008 SQL Server hostującym](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Przyczyna 2: zadania Azure Site Recovery kończą się niepowodzeniem na serwerach obsługujących dowolną wersję wystąpienia SQL Server z AUTO_CLOSE baz danych
**Jak naprawić** : [artykuł](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) z bazy wiedzy


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Przyczyna 3: znany problem w SQL Server 2016 i 2017
**Jak naprawić** : [artykuł](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) z bazy wiedzy

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Przyczyna 4: używasz konfiguracji bezpośredniej miejsc do magazynowania
**Jak naprawić** : Azure Site Recovery nie może utworzyć punktu odzyskiwania spójnego na poziomie aplikacji dla konfiguracji bezpośredniej miejsc do magazynowania. Aby poprawnie [skonfigurować zasady replikacji,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) Zobacz artykuł.

### <a name="more-causes-due-to-vss-related-issues"></a>Więcej przyczyn spowodowanych problemami związanymi z usługą VSS:

Aby przeprowadzić dalsze Rozwiązywanie problemów, sprawdź pliki na maszynie źródłowej, aby uzyskać dokładny kod błędu dla niepowodzenia:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak znaleźć błędy w pliku?
Wyszukaj ciąg "vacpError", otwierając plik vacp. log w edytorze

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

W powyższym przykładzie **2147754994** jest kod błędu informujący o niepowodzeniu, jak pokazano poniżej

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Składnik zapisywania usługi VSS nie jest zainstalowany — błąd 2147221164

*Jak naprawić*: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft. Powoduje zainstalowanie dostawcy usługi VSS w celu wykonania migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. Jeśli nie zainstalowano usługi dostawcy VSS, tworzenie migawki spójności aplikacji kończy się niepowodzeniem z IDENTYFIKATORem błędu 0x80040154 "Klasa nie jest zarejestrowana". </br>
Zobacz [artykuł dotyczący rozwiązywania problemów z instalacją składnika zapisywania usługi VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Składnik zapisywania usługi VSS jest wyłączony — błąd 2147943458

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft. Powoduje zainstalowanie dostawcy usługi VSS w celu wykonania migawek spójności aplikacji. Ten dostawca usługi VSS jest instalowany jako usługa. W przypadku wyłączenia usługi dostawcy VSS Tworzenie migawki spójności aplikacji kończy się niepowodzeniem z IDENTYFIKATORem błędu "określona usługa jest wyłączona i nie można jej uruchomić (0x80070422)". </br>

- Jeśli usługa VSS jest wyłączona,
    - Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Azure Site Recovery dostawcę usługi VSS
        - Usługa VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED dostawcy usługi VSS — błąd 2147754756

**Jak naprawić**: aby wygenerować tag spójności aplikacji, Azure Site Recovery używa usługi kopiowania woluminów w tle (VSS) firmy Microsoft.
Sprawdź, czy Azure Site Recovery jest zainstalowana usługa dostawcy usługi VSS. </br>

- Ponów próbę instalacji dostawcy, używając następujących poleceń:
- Odinstaluj istniejącego dostawcę: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Zainstaluj ponownie: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd

Sprawdź, czy typ uruchamiania usługi dostawcy VSS jest ustawiony na wartość **automatycznie**.
    - Uruchom ponownie następujące usługi:
        - Usługa VSS
        - Azure Site Recovery dostawcę usługi VSS
        - Usługa VDS
