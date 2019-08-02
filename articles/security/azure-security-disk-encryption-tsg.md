---
title: Rozwiązywanie problemów — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów dla Microsoft Azure dysku Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: e2464332727b0ef1e616c04a975df5ac475a7b19
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610292"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Usługa Azure Disk Encryption przewodnik rozwiązywania problemów

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji oraz administratorów chmury, w których organizacje używają usługi Azure Disk Encryption. Ten artykuł stanowi pomagające w rozwiązywaniu problemów dotyczących szyfrowania dysku.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Rozwiązywanie problemów z szyfrowania dysku systemu operacyjnego Linux

Szyfrowanie dysków systemu operacyjnego (OS) w systemie Linux należy odinstalować dysku systemu operacyjnego przed jego uruchomieniem proces szyfrowania pełnego dysku. Jeśli go nie można odinstalować dysku, komunikat o błędzie z "nie można odinstalować po..." może nastąpić.

Ten błąd może wystąpić, gdy wypróbowuje szyfrowania dysku systemu operacyjnego w środowisku maszyny Wirtualnej docelowego, który został zmieniony z obrazu obsługiwane podstawowe galerii. Odchylenia od obsługiwanych obrazów może kolidować z możliwością rozszerzenia odinstalował dysk systemu operacyjnego. Przykłady odchyleń może zawierać następujące elementy:
- Dostosowanych obrazów dopasowania nie jest już obsługiwany system plików lub schematu partycjonowania.
- Duże aplikacje, takie jak SAP, MongoDB, Apache Cassandra i platformy Docker nie są obsługiwane, gdy są one zainstalowane i uruchomione w systemie operacyjnym przed szyfrowania. Usługa Azure Disk Encryption nie może zamknąć te procesy bezpiecznie zgodnie z potrzebami w ramach przygotowania dysku systemu operacyjnego dotyczące szyfrowania dysku. Jeśli są nadal aktywne procesy zawierający otwarte dojścia do plików na dysku systemu operacyjnego, dysku systemu operacyjnego nie może być odinstalowane, uniemożliwiające do szyfrowania dysku systemu operacyjnego. 
- Niestandardowe skrypty uruchamianą w pobliżu Zamknij czasu jest włączone szyfrowanie, lub jeśli inne jest zmieniana na maszynie Wirtualnej podczas procesu szyfrowania. Ten konflikt może się zdarzyć, gdy szablon usługi Azure Resource Manager definiuje wiele rozszerzeń, aby wykonywać operacje jednocześnie lub rozszerzenia niestandardowego skryptu lub innego działania jest uruchamiany jednocześnie do szyfrowania dysku. Serializacja i izolowania takich kroków może rozwiązać ten problem.
- Linux zwiększonych zabezpieczeń (SELinux) nie zostały wyłączone przed włączeniem szyfrowania, dlatego krok odinstalowywania zakończy się niepowodzeniem. Może być reenabled SELinux, po zakończeniu szyfrowania.
- Dysk systemu operacyjnego wykorzystuje schemat Menedżer woluminów logicznych (LVM). Ograniczona obsługa dysku danych LVM jest dostępne, nie ma dysku systemu operacyjnego LVM.
- Nie są spełnione wymagania minimalnej ilości pamięci (7 GB jest zalecane dla szyfrowania dysku systemu operacyjnego).
- Dyski danych są rekursywnie zainstalowane w katalogu /mnt/ lub każdego innego (na przykład /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Inne usługi Azure Disk Encryption [wymagania wstępne](azure-security-disk-encryption-prerequisites.md) dla systemu Linux nie są spełnione.

## <a name="bkmk_Ubuntu14"></a>Zaktualizuj domyślne jądro dla Ubuntu 14,04 LTS

Obraz Ubuntu 14,04 LTS jest dostarczany z domyślną wersją jądra 4,4. W tej wersji jądra występuje znany problem polegający na tym, że zbyt mało pamięci, aby nie przerywać polecenia DD w trakcie procesu szyfrowania systemu operacyjnego. Ten błąd został rozwiązany w najnowszej jądrze systemu Linux z systemem Azure. Aby uniknąć tego błędu, przed włączeniem szyfrowania obrazu należy zaktualizować do [systemu Azure dostrojone jądro 4,15](https://packages.ubuntu.com/trusty/linux-azure) lub nowsze przy użyciu następujących poleceń:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po ponownym uruchomieniu maszyny wirtualnej w nowym jądrze można potwierdzić nową wersję jądra przy użyciu:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualizuj agenta maszyny wirtualnej platformy Azure i wersje rozszerzeń

Operacje Azure Disk Encryption mogą kończyć się niepowodzeniem w obrazach maszyn wirtualnych przy użyciu nieobsługiwanych wersji agenta maszyny wirtualnej platformy Azure. Obrazy systemu Linux z wersjami agenta wcześniejszą niż 2.2.38 należy zaktualizować przed włączeniem szyfrowania. Aby uzyskać więcej informacji, zobacz [jak zaktualizować agenta systemu Linux na maszynie wirtualnej](../virtual-machines/extensions/update-linux-agent.md) i [minimalną wersję dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Wymagana jest również prawidłowa wersja rozszerzenia agenta gościa Microsoft. Azure. Security. AzureDiskEncryption lub Microsoft. Azure. Security. AzureDiskEncryptionForLinux. Wersje rozszerzeń są obsługiwane i aktualizowane automatycznie przez platformę, gdy spełnione są wymagania wstępne agenta maszyny wirtualnej platformy Azure i jest używana obsługiwana wersja agenta maszyny wirtualnej.

Rozszerzenie Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux jest przestarzałe i nie jest już obsługiwane.  

## <a name="unable-to-encrypt-linux-disks"></a>Nie można zaszyfrować dysków systemu Linux

W niektórych przypadkach Linux, szyfrowanie dysków prawdopodobnie nie reaguje na "Do szyfrowania dysku systemu operacyjnego" i ustawieniami SSH jest wyłączona. Szyfrowanie może potrwać od 3 – 16 godzin dla obrazu podstawowego galerii. Jeśli zostaną dodane dyski danych o rozmiarze terabajt multi, proces może potrwać dni.

Sekwencja szyfrowania dysku systemu operacyjnego Linux tymczasowo umożliwia odinstalowanie dysku systemu operacyjnego. Następnie wykonuje blok po bloku szyfrowanie całego dysku systemu operacyjnego, przed jej ponownie instaluje on w stanie zaszyfrowane. W przeciwieństwie do usługi Azure Disk Encryption na Windows szyfrowania dysku systemu Linux nie pozwala na współbieżne używanie obiektu maszyny Wirtualnej, gdy szyfrowanie jest w toku. Charakterystyki wydajności maszyny wirtualnej można wprowadzać znaczące różnice w czas wymagany do ukończenia szyfrowania. Te właściwości obejmują rozmiar dysku i czy jest standardowe konto magazynu lub magazynu w warstwie premium (SSD).

Aby sprawdzić stan szyfrowania, należy wykonać sondowanie pola **komunikat dotyczący postępu** zwróconego za pomocą polecenia [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Podczas szyfrowania dysku systemu operacyjnego maszyny Wirtualnej przechodzi do stanu obsługi i wyłączenie protokołu SSH w celu uniknięcia zakłóceń w celu ciągły proces. **EncryptionInProgress** komunikatu raportów dla większości przypadków, gdy szyfrowanie jest w toku. Kilka godzin później, **VMRestartPending** monit o ponowne uruchomienie maszyny Wirtualnej. Na przykład:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Po wyświetleniu monitu o ponowne uruchomienie maszyny Wirtualnej, a po ponownym uruchomieniu maszyny Wirtualnej, należy poczekać 2 – 3 minuty dla ponownego uruchamiania i ostatnie kroki do wykonania w elemencie docelowym. Ukończ zmiany komunikatów stanu, gdy szyfrowanie jest na końcu. Po udostępnieniu tego komunikatu zaszyfrowanego dysku systemu operacyjnego powinien być gotowy do użycia, a maszyna wirtualna jest gotowa do ponownego wykorzystania.

W następujących przypadkach zaleca się przywrócenia maszyny Wirtualnej do migawki lub kopii zapasowej wykonanej bezpośrednio przed szyfrowania:
   - Jeśli sekwencja ponowny rozruch, opisanej powyżej, nie jest realizowane.
   - Gdy informacje rozruchu, komunikat o postępie lub innych raportu o błędach wskaźników, aby funkcja szyfrowania systemu operacyjnego zakończyło się niepowodzeniem w trakcie tego procesu. Przykładowy komunikat błędu "nie można odinstalować" opisanej w tym przewodniku.

Przed kolejnym próby to ponowne ocenienie właściwości maszyny Wirtualnej i upewnij się, że są spełnione wszystkie wymagania wstępne.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów z usługi Azure Disk Encryption za zaporą
Gdy łączność jest ograniczona przez zapory, wymagania serwera proxy lub ustawienia Sieciowej grupy zabezpieczeń sieci, może zostać przerwane możliwości rozszerzenia, aby wykonać niezbędne zadania. Ta przerw w działaniu może doprowadzić do komunikatów o stanie, takie jak "Nie jest dostępna na maszynie Wirtualnej stan rozszerzenia." W scenariuszach oczekiwanego szyfrowania nie powiedzie się. W kolejnych sekcjach mają niektórych typowych problemów zapory, które warto zbadać.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszelkie ustawienia sieciowej grupy zabezpieczeń, które są stosowane nadal muszą zezwalać na punkt końcowy, aby spełnić konfiguracji sieci udokumentowanego [wymagania wstępne](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) dotyczące szyfrowania dysku.

### <a name="azure-key-vault-behind-a-firewall"></a>Usługa Azure Key Vault za zaporą

Gdy szyfrowanie jest włączane przy użyciu [poświadczeń usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md), docelowa maszyna wirtualna musi zezwalać na połączenie z punktami końcowymi Azure Active Directory i Key Vault punktów końcowych. Bieżące punkty końcowe uwierzytelniania Azure Active Directory są obsługiwane w sekcjach 56 i 59 w dokumentacji [pakietu Office 365 adresy URL i zakresy adresów IP](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) . Instrukcje Key Vault są dostępne w dokumentacji dotyczącej sposobu [uzyskiwania dostępu Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Instance Metadata Service platformy Azure 
Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure](../virtual-machines/windows/instance-metadata-service.md) , w którym jest używany dobrze znany adres IP bez obsługi routingu`169.254.169.254`(), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.  Konfiguracje serwera proxy, które modyfikują lokalny ruch HTTP na ten adres (na przykład dodanie nagłówka X-forwardd-for) nie są obsługiwane.

### <a name="linux-package-management-behind-a-firewall"></a>Zarządzanie pakietami systemu Linux za zaporą

W czasie wykonywania usługa Azure Disk Encryption dla systemu Linux opiera się na system zarządzania pakietami dystrybucji docelowej, aby zainstalować wymagane wstępnie wymagane składniki przed włączeniem szyfrowania. Jeśli ustawienia zapory uniemożliwić maszyny Wirtualnej do pobrania i zainstalowania tych składników, oczekiwane są kolejne błędy. Kroki, aby skonfigurować ten system zarządzania pakietami można różnią się zależnie od dystrybucji. W systemie Red Hat Jeśli serwer proxy jest wymagane, należy się upewnić, że Menedżer subskrypcji i yum są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [rozwiązywania problemów Menedżera subskrypcji i yum](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Rozwiązywanie problemów z systemem Windows Server 2016 Server Core

W systemie Windows Server 2016 Server Core składnika bdehdcfg nie jest dostępny domyślnie. Ten składnik jest wymagany przez usługi Azure Disk Encryption. Służy do dzielenia wolumin systemowy z woluminu systemu operacyjnego, które jest wykonywane tylko raz dla okresu istnienia maszyny wirtualnej. Te pliki binarne nie są wymagane podczas późniejszych operacji szyfrowania.

Aby obejść ten problem, skopiuj następujące pliki cztery Maszynę centrum danych systemu Windows Server 2016, w tej samej lokalizacji, w instalacji Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Wprowadź następujące polecenie:

   ```
   bdehdcfg.exe -target default
   ```

1. To polecenie tworzy partycję systemową 550 MB. Ponowne uruchomienie systemu.

1. Sprawdź woluminy, a następnie przejść za pomocą narzędzia DiskPart  

Na przykład:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

W portalu może być wyświetlany dysk zaszyfrowany nawet po zaszyfrowaniu go w ramach maszyny wirtualnej.  Taka sytuacja może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego wyszyfrowania dysku z poziomu maszyny wirtualnej, a nie przy użyciu Azure Disk Encryption polecenia zarządzania wyższego poziomu.  Polecenia wyższego poziomu nie tylko deszyfrują dysk z maszyny wirtualnej, ale poza maszyną wirtualną również aktualizują ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one zachowane, platforma nie będzie mogła zgłosić stanu szyfrowania ani zainicjować obsługi maszyny wirtualnej.   

Aby wyłączyć Azure Disk Encryption za pomocą programu PowerShell, użyj polecenia [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) , a następnie polecenie [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć Azure Disk Encryption przy użyciu interfejsu wiersza polecenia, użyj polecenie [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono więcej informacji na temat niektórych typowych problemów dotyczących usługi Azure Disk Encryption i rozwiązywania tych problemów. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w usłudze Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Funkcja szyfrowania nieaktywnych danych platformy Azure](fundamentals/encryption-atrest.md)
