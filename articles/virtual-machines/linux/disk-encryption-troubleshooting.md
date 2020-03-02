---
title: Rozwiązywanie problemów — Azure Disk Encryption dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z Microsoft Azure szyfrowaniem dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: dd21b6520dc68a6f7faa5500054b2865556e3dfb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205912"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Usługa Azure Disk Encryption przewodnik rozwiązywania problemów

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji oraz administratorów chmury, w których organizacje używają usługi Azure Disk Encryption. Ten artykuł stanowi pomagające w rozwiązywaniu problemów dotyczących szyfrowania dysku.

Przed wykonaniem poniższych czynności należy najpierw upewnić się, że maszyny wirtualne, które mają zostać zaszyfrowane, znajdują się wśród [obsługiwanych rozmiarów maszyn wirtualnych i w systemach operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems)oraz że zostały spełnione wszystkie wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Rozwiązywanie problemów z szyfrowania dysku systemu operacyjnego Linux

Szyfrowanie dysków systemu operacyjnego (OS) w systemie Linux należy odinstalować dysku systemu operacyjnego przed jego uruchomieniem proces szyfrowania pełnego dysku. Jeśli go nie można odinstalować dysku, komunikat o błędzie z "nie można odinstalować po..." może nastąpić.

Ten błąd może wystąpić, gdy podjęto próbę szyfrowania dysku systemu operacyjnego na maszynie wirtualnej ze środowiskiem, które zostało zmienione z obsługiwanego obrazu galerii zasobów. Odchylenia od obsługiwanych obrazów może kolidować z możliwością rozszerzenia odinstalował dysk systemu operacyjnego. Przykłady odchyleń może zawierać następujące elementy:
- Dostosowanych obrazów dopasowania nie jest już obsługiwany system plików lub schematu partycjonowania.
- Duże aplikacje, takie jak SAP, MongoDB, Apache Cassandra i platformy Docker nie są obsługiwane, gdy są one zainstalowane i uruchomione w systemie operacyjnym przed szyfrowania. Usługa Azure Disk Encryption nie może zamknąć te procesy bezpiecznie zgodnie z potrzebami w ramach przygotowania dysku systemu operacyjnego dotyczące szyfrowania dysku. Jeśli są nadal aktywne procesy zawierający otwarte dojścia do plików na dysku systemu operacyjnego, dysku systemu operacyjnego nie może być odinstalowane, uniemożliwiające do szyfrowania dysku systemu operacyjnego. 
- Niestandardowe skrypty uruchamianą w pobliżu Zamknij czasu jest włączone szyfrowanie, lub jeśli inne jest zmieniana na maszynie Wirtualnej podczas procesu szyfrowania. Ten konflikt może się zdarzyć, gdy szablon usługi Azure Resource Manager definiuje wiele rozszerzeń, aby wykonywać operacje jednocześnie lub rozszerzenia niestandardowego skryptu lub innego działania jest uruchamiany jednocześnie do szyfrowania dysku. Serializacja i izolowania takich kroków może rozwiązać ten problem.
- Linux zwiększonych zabezpieczeń (SELinux) nie zostały wyłączone przed włączeniem szyfrowania, dlatego krok odinstalowywania zakończy się niepowodzeniem. Może być reenabled SELinux, po zakończeniu szyfrowania.
- Dysk systemu operacyjnego wykorzystuje schemat Menedżer woluminów logicznych (LVM). Ograniczona obsługa dysku danych LVM jest dostępne, nie ma dysku systemu operacyjnego LVM.
- Nie są spełnione wymagania minimalnej ilości pamięci (7 GB jest zalecane dla szyfrowania dysku systemu operacyjnego).
- Dyski danych są rekursywnie zainstalowane w katalogu /mnt/ lub każdego innego (na przykład /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Zaktualizuj domyślne jądro dla Ubuntu 14,04 LTS

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

Operacje Azure Disk Encryption mogą kończyć się niepowodzeniem w obrazach maszyn wirtualnych przy użyciu nieobsługiwanych wersji agenta maszyny wirtualnej platformy Azure. Obrazy systemu Linux z wersjami agenta wcześniejszą niż 2.2.38 należy zaktualizować przed włączeniem szyfrowania. Aby uzyskać więcej informacji, zobacz [jak zaktualizować agenta systemu Linux na maszynie wirtualnej](../extensions/update-linux-agent.md) i [minimalną wersję dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Wymagana jest również prawidłowa wersja rozszerzenia agenta gościa Microsoft. Azure. Security. AzureDiskEncryption lub Microsoft. Azure. Security. AzureDiskEncryptionForLinux. Wersje rozszerzeń są obsługiwane i aktualizowane automatycznie przez platformę, gdy spełnione są wymagania wstępne agenta maszyny wirtualnej platformy Azure i jest używana obsługiwana wersja agenta maszyny wirtualnej.

Rozszerzenie Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux jest przestarzałe i nie jest już obsługiwane.  

## <a name="unable-to-encrypt-linux-disks"></a>Nie można zaszyfrować dysków systemu Linux

W niektórych przypadkach Linux, szyfrowanie dysków prawdopodobnie nie reaguje na "Do szyfrowania dysku systemu operacyjnego" i ustawieniami SSH jest wyłączona. Szyfrowanie może potrwać od 3 – 16 godzin dla obrazu podstawowego galerii. Jeśli zostaną dodane dyski danych o rozmiarze terabajt multi, proces może potrwać dni.

Sekwencja szyfrowania dysku systemu operacyjnego Linux tymczasowo umożliwia odinstalowanie dysku systemu operacyjnego. Następnie wykonuje blok po bloku szyfrowanie całego dysku systemu operacyjnego, przed jej ponownie instaluje on w stanie zaszyfrowane. Szyfrowanie dysków systemu Linux nie zezwala na współbieżne korzystanie z maszyny wirtualnej, gdy szyfrowanie jest w toku. Charakterystyki wydajności maszyny wirtualnej można wprowadzać znaczące różnice w czas wymagany do ukończenia szyfrowania. Te właściwości obejmują rozmiar dysku i czy jest standardowe konto magazynu lub magazynu w warstwie premium (SSD).

Aby sprawdzić stan szyfrowania, należy wykonać sondowanie pola **komunikat dotyczący postępu** zwróconego za pomocą polecenia [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Podczas szyfrowania dysku systemu operacyjnego maszyny Wirtualnej przechodzi do stanu obsługi i wyłączenie protokołu SSH w celu uniknięcia zakłóceń w celu ciągły proces. Komunikat **EncryptionInProgress** jest raportowany przez większość czasu, podczas gdy szyfrowanie jest w toku. Kilka godzin później komunikat **VMRestartPending** monit o ponowne uruchomienie maszyny wirtualnej. Na przykład:


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

Zobacz [szyfrowanie dysków w sieci izolowanej](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

W portalu może być wyświetlany dysk zaszyfrowany nawet po zaszyfrowaniu go w ramach maszyny wirtualnej.  Taka sytuacja może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego wyszyfrowania dysku z poziomu maszyny wirtualnej, a nie przy użyciu Azure Disk Encryption polecenia zarządzania wyższego poziomu.  Polecenia wyższego poziomu nie tylko deszyfrują dysk z maszyny wirtualnej, ale poza maszyną wirtualną również aktualizują ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one zachowane, platforma nie będzie mogła zgłosić stanu szyfrowania ani zainicjować obsługi maszyny wirtualnej.   

Aby wyłączyć Azure Disk Encryption za pomocą programu PowerShell, użyj polecenia [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) , a następnie polecenie [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć Azure Disk Encryption przy użyciu interfejsu wiersza polecenia, użyj polecenie [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono więcej informacji na temat niektórych typowych problemów dotyczących usługi Azure Disk Encryption i rozwiązywania tych problemów. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
