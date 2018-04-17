---
title: Rozwiązywanie problemów szyfrowania dysków Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów dla programu Microsoft Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: DevTiw
ms.openlocfilehash: 4bc1f7cf64a2a08215cd82d8e72e40eba8db3093
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Podręczniku rozwiązywania problemów z szyfrowania dysków Azure

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji i administratorów chmury, których organizacje szyfrowania dysków Azure i wymagają wskazówki dotyczące rozwiązywania problemów dotyczących szyfrowania dysków.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Rozwiązywanie problemów z szyfrowania dysku systemu operacyjnego Linux

Szyfrowanie dysków systemu operacyjnego (OS) Linux należy odinstalować dysku systemu operacyjnego przed jego uruchomieniem proces szyfrowania dysku pełna. Jeśli nie można odinstalować dysku, komunikat o błędzie z "nie można odinstalować po..." prawdopodobnie wystąpią.

Ten błąd jest najbardziej prawdopodobne, próba szyfrowania dysku systemu operacyjnego w środowisku maszyny Wirtualnej docelowego, który został zmodyfikowany lub zmieniła się z jego obrazu obsługiwane galerii standardowych. Następujące przykłady odchylenia od obsługiwanych obrazu, który może zakłócać możliwość rozszerzenia odinstalować dysku systemu operacyjnego:
- Niestandardowe obrazy zgodne nie jest już obsługiwany system plików lub schemat partycjonowania.
- Dużych aplikacji, takie jak SAP, MongoDB Apache Cassandra i Docker nie są obsługiwane, gdy są one zainstalowane i uruchomione w systemie operacyjnym przed szyfrowania.  Szyfrowanie dysków Azure nie może bezpiecznie zamknąć te procesy, zgodnie z wymaganiami przygotowanie dysku systemu operacyjnego do szyfrowania dysku.  Jeśli występują nadal aktywnych procesów zawierający dojść otwartych plików na dysku systemu operacyjnego, dysk systemu operacyjnego nie może być odinstalowane, co spowoduje niepowodzenie szyfrowania dysku systemu operacyjnego. 
- Niestandardowe skrypty uruchomionymi w pobliżu Zamknij czasu jest włączone szyfrowanie, lub jeśli inne jest zmieniana na maszynie Wirtualnej podczas procesu szyfrowania. Ten konflikt może się zdarzyć, gdy szablonu usługi Azure Resource Manager definiuje wiele rozszerzeń można wykonać jednocześnie lub uruchomienie rozszerzenia niestandardowego skryptu lub innego działania jednocześnie do szyfrowania dysku. Serializację i izolowanie tych czynności może rozwiązać ten problem.
- Nie wyłączono Linux zwiększonych zabezpieczeń (SELinux) przed włączeniem szyfrowania, więc krok odinstalowywania zakończy się niepowodzeniem. Może być reenabled SELinux, po zakończeniu szyfrowania.
- Dysk systemu operacyjnego wykorzystuje schemat menedżera woluminu logicznego (LVM). Ograniczona obsługa dysku danych LVM jest dostępne, nie jest dysk systemu operacyjnego LVM.
- Nie spełniono wymagania dotyczące minimalnej ilości pamięci (7 GB jest zalecane do szyfrowania dysku systemu operacyjnego).
- Dyski danych są rekursywnie zainstalowanego w ramach katalogu /mnt/ lub każdego innego (na przykład /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Inne szyfrowania dysków Azure [wymagania wstępne](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) dla systemu Linux nie są spełnione.

## <a name="unable-to-encrypt"></a>Nie można zaszyfrować

W niektórych przypadkach Linux szyfrowanie dysków wydaje się zostać zablokowana na "Rozpoczęto szyfrowania dysku systemu operacyjnego" i SSH jest wyłączone. Szyfrowanie może potrwać między 3-16 godzin na standardowych galerii. Jeśli zostaną dodane dyski danych o rozmiarze terabajt multi, proces może zająć dni.

Kolejność szyfrowania dysku systemu operacyjnego Linux tymczasowo odinstaluje dysk systemu operacyjnego. Następnie wykonuje blok po bloku szyfrowanie całego dysku systemu operacyjnego, zanim go ponownie instaluje on w stanie zaszyfrowane. W odróżnieniu od szyfrowania dysków Azure w systemie Windows Linux szyfrowanie dysków nie zezwala użytku równoczesnych maszyny wirtualnej, gdy szyfrowanie jest w toku. Charakterystyki wydajności maszyny wirtualnej można wprowadzać znaczące różnice dotyczące czasu wymaganego do ukończenia szyfrowania. Te właściwości obejmują rozmiar dysku i czy konto magazynu jest standard lub magazyn w warstwie premium (SSD).

Aby sprawdzić stan szyfrowania, sondowanie **postępu** pola zwrócony z [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) polecenia. Podczas szyfrowania dysku systemu operacyjnego, maszyna wirtualna przechodzi do stanu obsługi i wyłącza SSH, aby uniknąć zakłóceń do ciągły proces. **EncryptionInProgress** komunikatów raportów w większości przypadków, gdy Trwa szyfrowanie. Później, kilka godzin **VMRestartPending** monit o ponowne uruchomienie maszyny Wirtualnej. Na przykład:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Po zostanie wyświetlony monit o ponowny rozruch maszyny Wirtualnej, a po ponownym uruchomieniu maszyny Wirtualnej, należy poczekać 2 – 3 minuty dla ponownego rozruchu i końcowe kroki można wykonać na elemencie docelowym. Zmiany stanu wyświetlane komunikat podczas szyfrowania finally jest ukończona. Po ten komunikat jest dostępny, zaszyfrowanego dysku systemu operacyjnego powinien być gotowy do użycia, a maszyna wirtualna jest gotowa do ponownego wykorzystania.

W następujących przypadkach zaleca się przywrócenia maszyny Wirtualnej do migawki lub kopii zapasowej wykonanej bezpośrednio przed szyfrowania:
   - Jeśli sekwencji rozruchu opisanego wcześniej nie odbywa się.
   - Jeśli informacje rozruchowe, komunikat o postępie lub innych raport dotyczący wskaźników błąd szyfrowania tego systemu operacyjnego nie powiodła się w trakcie tego procesu. Przykładowy komunikat błędu "nie udało się odinstalować" opisanej w tym przewodniku.

Przed ponowieniem próby obliczyć ponownie właściwości maszyny wirtualnej i upewnij się, że wszystkie wymagania wstępne są spełnione.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów z szyfrowania dysków Azure za zaporą
Gdy łączność jest ograniczona przez zaporę, wymaganie serwera proxy lub ustawienia Grupa zabezpieczeń sieci, może być zakłócona rozszerzenia możliwość wykonywania wymaganych zadań. Ta przerw w działaniu może spowodować komunikaty stanu, takie jak "Nie jest dostępny na maszynie Wirtualnej stan rozszerzenia." W scenariuszach oczekiwanego szyfrowanie nie powiodło się zakończyć. W kolejnych sekcjach ma niektórych typowych problemów zapory, które mogą zbadać.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie ustawienia grupy zabezpieczeń sieci, które są stosowane nadal musi zezwalać na punkt końcowy do spełniają konfiguracja sieci udokumentowane [wymagania wstępne](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) szyfrowanie dysków.

### <a name="azure-key-vault-behind-a-firewall"></a>Usługa Azure Key Vault za zaporą
Maszyna wirtualna musi mieć możliwość dostępu magazynu kluczy. Odwołuje się do wskazówek dotyczących dostępu do magazynu kluczy za pośrednictwem zapory który [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) przechowuje zespołu.

### <a name="linux-package-management-behind-a-firewall"></a>Linux zarządzania pakietami za zaporą

W czasie wykonywania szyfrowania dysków Azure dla systemu Linux wykorzystuje w systemie zarządzania pakiet dystrybucji docelowej, aby zainstalować wstępnie wymagane składniki potrzebne przed włączeniem szyfrowania. Jeśli ustawienia zapory uniemożliwić maszyny Wirtualnej o pobranie i zainstalowanie tych składników, kolejne błędy są oczekiwane. Instrukcje konfiguracji tego pakietu systemu zarządzania może różnią się zależnie od dystrybucji. Na Red Hat gdy serwer proxy jest wymagany, pamiętaj, że Menedżer subskrypcji i yum są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [jak rozwiązywać problemy Menedżer subskrypcji i yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Rozwiązywanie problemów z systemem Windows Server 2016 Server Core

W systemie Windows Server 2016 Server Core składnik bdehdcfg nie jest dostępny domyślnie. Ten składnik jest wymagany przez szyfrowanie dysków Azure. Służy do dzielenia woluminu systemowego z woluminu systemu operacyjnego, który jest wykonywane tylko raz dla okresu istnienia maszyny wirtualnej. Te dane binarne nie są wymagane podczas późniejszych operacji szyfrowania.

Aby obejść ten problem, a pliki kopii następujące 4 z maszyny Wirtualnej systemu Windows Server 2016 danych Centrum w tej samej lokalizacji, w instalacji Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Wprowadź następujące polecenie:

   ```
   bdehdcfg.exe -target default
   ```

   3. To polecenie tworzy partycję systemową 550 MB. Ponowny rozruch systemu.

   4. Za pomocą narzędzia DiskPart Sprawdź woluminy, a następnie kontynuuj.  

Na przykład:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów z stanu szyfrowania

Jeśli stan oczekiwano szyfrowania nie jest zgodny, co jest raportowane w portalu, zobacz następujący artykuł pomocy technicznej: [stanu szyfrowania jest wyświetlany niepoprawnie w portalu zarządzania Azure](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por)

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono więcej informacji na temat typowych problemów w szyfrowania dysków Azure i sposoby rozwiązywania tych problemów. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Szyfrowanie przechowywanych danych Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
