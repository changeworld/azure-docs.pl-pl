---
title: Rozwiązywanie problemów z szyfrowaniem dysków platformy Azure dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z szyfrowaniem dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970484"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z szyfrowaniem dysków platformy Azure

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji i administratorów chmury, których organizacje używają szyfrowania dysków platformy Azure. Ten artykuł ułatwia rozwiązywanie problemów związanych z szyfrowaniem dysku.

Przed podjęciem któregokolwiek z poniższych kroków, najpierw upewnij się, że maszyny wirtualne, które próbujesz zaszyfrować, należą do [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems)i że spełnione są wszystkie wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Rozwiązywanie problemów z szyfrowaniem dysku systemu operacyjnego Linux

Szyfrowanie dysku systemu operacyjnego Linux musi odinstalować dysk systemu operacyjnego przed uruchomieniem go w procesie szyfrowania całego dysku. Jeśli nie można odinstalować dysku, komunikat o błędzie "nie można odinstalować po ..." może wystąpić.

Ten błąd może wystąpić, gdy próba szyfrowania dysku systemu operacyjnego jest podejmowana na maszynie Wirtualnej ze środowiskiem, które zostało zmienione z obsługiwanego obrazu galerii stockowej. Odchylenia od obsługiwanego obrazu mogą zakłócać możliwość odinstalowania dysku systemu operacyjnego przez rozszerzenie. Przykłady odchyleń mogą obejmować następujące elementy:
- Dostosowane obrazy nie są już zgodne z obsługiwanym systemem plików lub schematem partycjonowania.
- Duże aplikacje, takie jak SAP, MongoDB, Apache Cassandra i Docker nie są obsługiwane, gdy są zainstalowane i uruchomione w systemie operacyjnym przed szyfrowaniem. Usługa Azure Disk Encryption nie może bezpiecznie zamknąć tych procesów zgodnie z wymaganiami podczas przygotowywania dysku operacyjnego do szyfrowania dysku. Jeśli nadal istnieją aktywne procesy przechowujące otwarte dojścia do plików na dysku systemu operacyjnego, nie można odinstalować dysku systemu operacyjnego, co powoduje niepowodzenie szyfrowania dysku systemu operacyjnego. 
- Skrypty niestandardowe, które działają w bliskiej odległości od włączonego szyfrowania lub jeśli jakiekolwiek inne zmiany są wprowadzane na maszynie Wirtualnej podczas procesu szyfrowania. Ten konflikt może się zdarzyć, gdy szablon usługi Azure Resource Manager definiuje wiele rozszerzeń do wykonania jednocześnie lub gdy niestandardowe rozszerzenie skryptu lub inne działanie jest uruchamiane jednocześnie z szyfrowaniem dysku. Serializacji i izolowanie takich kroków może rozwiązać problem.
- Security Enhanced Linux (SELinux) nie został wyłączony przed włączeniem szyfrowania, więc krok odinstalowania kończy się niepowodzeniem. SELinux można ponownie ponależyć po zakończeniu szyfrowania.
- Dysk systemu operacyjnego używa schematu Menedżera woluminów logicznych (LVM). Chociaż dostępna jest ograniczona obsługa dysku danych LVM, dysk systemu operacyjnego LVM nie jest.
- Minimalne wymagania dotyczące pamięci nie są spełnione (sugerowane jest 7 GB dla szyfrowania dysku systemu operacyjnego).
- Dyski danych są cyklicznie montowane pod katalogiem /mnt/ lub sobą nawzajem (na przykład /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Aktualizacja domyślnego jądra dla Ubuntu 14.04 LTS

Obraz Ubuntu 14.04 LTS jest dostarczany z domyślną wersją jądra 4.4. Ta wersja jądra ma znany problem, w którym Out of Memory Killer nieprawidłowo kończy polecenie dd podczas procesu szyfrowania systemu operacyjnego. Ten błąd został naprawiony w najnowszym jądrze systemu Linux dostrojonym na platformie Azure. Aby uniknąć tego błędu, przed włączeniem szyfrowania obrazu, zaktualizuj [jądro dostrojone platformy Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) lub nowsze przy użyciu następujących poleceń:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Po ponownym uruchomieniu maszyny Wirtualnej do nowego jądra, nowa wersja jądra może zostać potwierdzona za pomocą:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualizowanie agenta i wersji rozszerzeń maszyny wirtualnej platformy Azure

Operacje szyfrowania dysków platformy Azure mogą zakończyć się niepowodzeniem w obrazach maszyn wirtualnych przy użyciu nieobsługiwałych wersji agenta maszyny wirtualnej platformy Azure. Obrazy systemu Linux z wersjami agenta wcześniejszymi niż 2.2.38 powinny zostać zaktualizowane przed włączeniem szyfrowania. Aby uzyskać więcej informacji, zobacz [Jak zaktualizować agenta systemu Azure Linux na maszynie Wirtualnej](../extensions/update-linux-agent.md) i [minimalnej obsłudze wersji dla agentów maszyn wirtualnych na platformie Azure.](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

Wymagana jest również poprawna wersja rozszerzenia agenta gościa Microsoft.Azure.Security.AzureDiskEncryption lub Microsoft.Azure.Security.AzureDiskEncryptionForLinux. Wersje rozszerzenia są obsługiwane i aktualizowane automatycznie przez platformę, gdy wymagania wstępne agenta maszyny wirtualnej platformy Azure są spełnione i używana jest obsługiwana wersja agenta maszyny wirtualnej.

Rozszerzenie Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux zostało przestarzałe i nie jest już obsługiwane.  

## <a name="unable-to-encrypt-linux-disks"></a>Nie można zaszyfrować dysków Systemu Linux

W niektórych przypadkach szyfrowanie dysku Linuksa wydaje się być zablokowane w "uruchomiono szyfrowanie dysku operacyjnego", a SSH jest wyłączone. Proces szyfrowania może potrwać od 3 do 16 godzin, aby zakończyć na obrazie galerii stockowej. Jeśli dodawane są dyski danych o rozmiarze wielu terabajtów, proces może potrwać kilka dni.

Sekwencja szyfrowania dysku systemu operacyjnego Linux odinstalowuje tymczasowo dysk systemu operacyjnego. Następnie wykonuje szyfrowanie blok po bloku całego dysku systemu operacyjnego, zanim ponownie zmontuje go w stanie zaszyfrowanym. Szyfrowanie dysków systemu Linux nie zezwala na jednoczesne korzystanie z maszyny Wirtualnej, gdy szyfrowanie jest w toku. Charakterystyka wydajności maszyny Wirtualnej może znacząco zmienić czas wymagany do ukończenia szyfrowania. Te cechy obejmują rozmiar dysku i to, czy konto magazynu jest magazynem standardowym czy premium (SSD).

Aby sprawdzić stan szyfrowania, sonduj pole **ProgressMessage zwrócone** z polecenia [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Gdy dysk systemu operacyjnego jest szyfrowany, maszyna wirtualna przechodzi w stan obsługi i wyłącza SSH, aby zapobiec zakłóceniom trwającego procesu. **Komunikat EncryptionInProgress** zgłasza się przez większość czasu, gdy szyfrowanie jest w toku. Kilka godzin później komunikat **VMRestartPending** monituje o ponowne uruchomienie maszyny Wirtualnej. Przykład:


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

Po wyświetleniu monitu o ponowne uruchomienie maszyny Wirtualnej i po ponownym uruchomieniu maszyny Wirtualnej należy odczekać 2-3 minuty na ponowne uruchomienie komputera i na wykonanie ostatnich kroków na docelowych. Komunikat o stanie zmienia się po zakończeniu szyfrowania. Po udostępnienie tego komunikatu oczekuje się, że zaszyfrowany dysk systemu operacyjnego będzie gotowy do użycia, a maszyna wirtualna jest gotowa do ponownego użycia.

W następujących przypadkach zaleca się przywrócenie maszyny Wirtualnej do migawki lub kopii zapasowej wykonanej bezpośrednio przed szyfrowaniem:
   - Jeśli sekwencja ponownego uruchomienia, opisana wcześniej, nie ma miejsca.
   - Jeśli informacje o rozruchu, komunikat o postępie lub inne wskaźniki błędów zgłaszają, że szyfrowanie systemu operacyjnego nie powiodło się w trakcie tego procesu. Przykładem wiadomości jest błąd "nie można odinstalować", który jest opisany w tym przewodniku.

Przed następną próbą ponownie ocenić właściwości maszyny Wirtualnej i upewnij się, że wszystkie wymagania wstępne są spełnione.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów z szyfrowaniem dysku platformy Azure za zaporą

Zobacz [Szyfrowanie dysków w sieci izolowanej](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

Portal może wyświetlać dysk jako zaszyfrowany nawet po jego niezaszyfrowaniu w maszynie wirtualnej.  Taka możliwość może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego odszyfrowywania dysku z poziomu maszyny Wirtualnej, zamiast używać poleceń zarządzania szyfrowaniem usługi Azure Disk wyższego poziomu.  Polecenia wyższego poziomu nie tylko odszyfrowywać dysk z poziomu maszyny Wirtualnej, ale poza maszyną wirtualną aktualizują również ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one przechowywane w wyrównaniu, platforma nie będzie w stanie poprawnie zgłosić stanu szyfrowania lub aprowizować maszyny Wirtualnej.   

Aby wyłączyć szyfrowanie dysków platformy Azure za pomocą programu PowerShell, należy użyć [funkcji Disable-AzVMDiskEncryption,](/powershell/module/az.compute/disable-azvmdiskencryption) a następnie [usunąć-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć szyfrowanie dysków platformy Azure za pomocą interfejsu wiersza polecenia, użyj [funkcji szyfrowania az vm disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się więcej o niektórych typowych problemach w szyfrowaniu dysków platformy Azure i o tym, jak rozwiązać te problemy. Aby uzyskać więcej informacji na temat tej usługi i jej możliwości, zobacz następujące artykuły:

- [Stosowanie szyfrowania dysku w usłudze Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
