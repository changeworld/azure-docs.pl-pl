---
title: Rozwiązywanie problemów — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów dla Microsoft Azure dysku Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 35d494702673d59290a0073c55135138f533b8bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956696"
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

## <a name="bkmk_Ubuntu14"></a> Ubuntu 14.04 LTS aktualizacji jądra domyślne

Obraz Ubuntu 14.04 LTS jest dostarczany z domyślną wersję jądra 4.4. Ta wersja jądra jest to znany problem, w którym poza identyfikatory pamięci nieprawidłowo kończy działanie polecenia dd w procesie szyfrowania systemu operacyjnego. Ten błąd został naprawiony w ostatnim Azure dostrojone jądra systemu Linux. Aby uniknąć tego błędu, przed włączeniem szyfrowania na obrazie, należy zaktualizować do [Azure dostrojone jądra 4.15](https://packages.ubuntu.com/trusty/linux-azure) lub później za pomocą następujących poleceń:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po ponownym uruchomieniu maszyny Wirtualnej do nowego jądra nowa wersja jądra można potwierdzić, przy użyciu:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Zaktualizuj agenta maszyny wirtualnej platformy Azure i wersje rozszerzenia

Operacji na platformie Azure Disk Encryption może zakończyć się niepowodzeniem w obrazach maszyn wirtualnych przy użyciu nieobsługiwane wersje agenta maszyny wirtualnej platformy Azure. Obrazy systemu Linux przy użyciu agenta w wersji wcześniejszej niż 2.2.38 powinien zostać zaktualizowany przed włączeniem szyfrowania. Aby uzyskać więcej informacji, zobacz [jak zaktualizować agenta systemu Linux dla platformy Azure na maszynie Wirtualnej](../virtual-machines/extensions/update-linux-agent.md) i [minimalna obsługiwana wersja dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Poprawną wersję rozszerzenia Microsoft.Azure.Security.AzureDiskEncryption lub Microsoft.Azure.Security.AzureDiskEncryptionForLinux agenta gościa jest również wymagany. Wersje rozszerzenia są obsługiwane i są automatycznie aktualizowane przez platformę, gdy są spełnione wymagania wstępne dotyczące agenta w maszynie wirtualnej platformy Azure i służy obsługiwaną wersję agenta maszyny wirtualnej.

Rozszerzenie Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux jest przestarzała i nie jest już obsługiwana.  

## <a name="unable-to-encrypt-linux-disks"></a>Nie można zaszyfrować dyski w systemie Linux

W niektórych przypadkach Linux, szyfrowanie dysków prawdopodobnie nie reaguje na "Do szyfrowania dysku systemu operacyjnego" i ustawieniami SSH jest wyłączona. Szyfrowanie może potrwać od 3 – 16 godzin dla obrazu podstawowego galerii. Jeśli zostaną dodane dyski danych o rozmiarze terabajt multi, proces może potrwać dni.

Sekwencja szyfrowania dysku systemu operacyjnego Linux tymczasowo umożliwia odinstalowanie dysku systemu operacyjnego. Następnie wykonuje blok po bloku szyfrowanie całego dysku systemu operacyjnego, przed jej ponownie instaluje on w stanie zaszyfrowane. W przeciwieństwie do usługi Azure Disk Encryption na Windows szyfrowania dysku systemu Linux nie pozwala na współbieżne używanie obiektu maszyny Wirtualnej, gdy szyfrowanie jest w toku. Charakterystyki wydajności maszyny wirtualnej można wprowadzać znaczące różnice w czas wymagany do ukończenia szyfrowania. Te właściwości obejmują rozmiar dysku i czy jest standardowe konto magazynu lub magazynu w warstwie premium (SSD).

Aby sprawdzić stan szyfrowania, sondowanie **komunikat dotyczący postępu** pola zwróciło [Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) polecenia. Podczas szyfrowania dysku systemu operacyjnego maszyny Wirtualnej przechodzi do stanu obsługi i wyłączenie protokołu SSH w celu uniknięcia zakłóceń w celu ciągły proces. **EncryptionInProgress** komunikatu raportów dla większości przypadków, gdy szyfrowanie jest w toku. Kilka godzin później, **VMRestartPending** monit o ponowne uruchomienie maszyny Wirtualnej. Na przykład:


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

Jeśli szyfrowanie jest włączone za pomocą [poświadczeń usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md), docelowa maszyna wirtualna musi zezwalać na łączność z punktami końcowymi usługi Azure Active Directory i punkty końcowe usługi Key Vault. Bieżące punkty końcowe uwierzytelniania usługi Azure Active Directory znajdują się w sekcji 56 i 59 z [URL usługi Office 365 i zakresy adresów IP](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentacji. Usługa Key Vault instrukcje znajdują się w dokumentacji na temat sposobu [dostępu do usługi Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Wystąpienie usługi Azure Metadata Service 
Maszyna wirtualna musi być w stanie uzyskać dostęp do [Azure Instance Metadata service](../virtual-machines/windows/instance-metadata-service.md) punktu końcowego, który używa dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`), są dostępne tylko z poziomu maszyny Wirtualnej.  Konfiguracja serwera proxy, zmienić lokalne ruchu HTTP na ten adres (np. Dodawanie nagłówka X-Forwarded-uzyskać) nie są obsługiwane.

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

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów z stanu szyfrowania 

Portalu może wyświetlać dysku formie zaszyfrowanej, nawet po jego niezaszyfrowane na maszynie wirtualnej.  Taka sytuacja może wystąpić, gdy polecenia niskiego poziomu są używane bezpośrednio odszyfrować dysk z poziomu maszyny Wirtualnej, zamiast korzystać z wyższym poziomie polecenia zarządzania usługi Azure Disk Encryption.  Wyższy poziom polecenia nie tylko odszyfrowanie dysku z poziomu maszyny Wirtualnej, ale poza maszyną Wirtualną są również zaktualizować ustawienia szyfrowania na poziomie platformy ważne i rozszerzenie skojarzone z maszyną Wirtualną.  Jeśli nie są one przechowywane w wyrównanie, platforma nie będzie można zgłosić stan szyfrowania lub prawidłowo aprowizowania maszyny Wirtualnej.   

Aby wyłączyć usługi Azure Disk Encryption przy użyciu programu PowerShell, użyj [AzVMDiskEncryption Wyłącz](/powershell/module/az.compute/disable-azvmdiskencryption) następuje [AzVMDiskEncryptionExtension Usuń](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchamianie AzVMDiskEncryptionExtension Usuń przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć usługi Azure Disk Encryption przy użyciu interfejsu wiersza polecenia, użyj [az vm encryption, wyłącz](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono więcej informacji na temat niektórych typowych problemów dotyczących usługi Azure Disk Encryption i rozwiązywania tych problemów. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w usłudze Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Funkcja szyfrowania nieaktywnych danych platformy Azure](azure-security-encryption-atrest.md)
