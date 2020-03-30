---
title: Jak skonfigurować LVM i RAID on-crypt na maszynie wirtualnej z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania LVM i RAID na krypty na maszynach wirtualnych z systemem Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284912"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Jak skonfigurować LVM i RAID on-crypt

Ten dokument jest procesem krok po kroku o tym, jak wykonać LVM w krypty i raid na konfiguracje krypty.

### <a name="environment"></a>Środowisko

- Dystrybucje Linuksa
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- ADE pojedynczy przejazd
- Podwójny przełęcz ADE


## <a name="scenarios"></a>Scenariusze

**Ten scenariusz ma zastosowanie do rozszerzeń dwuprzebiegowych i jednoprzebiegowych ADE.**  

- Konfigurowanie lvm na wierzchu zaszyfrowanych urządzeń (LVM-on-Crypt)
- Konfigurowanie macierzy RAID na zaszyfrowanych urządzeniach (RAID-on-Crypt)

Po zaszyfrowaniu urządzeń bazowych można utworzyć struktury LVM lub RAID na tej zaszyfrowanej warstwie. Woluminy fizyczne (PV) są tworzone na górze zaszyfrowanej warstwy.
Woluminy fizyczne są używane do tworzenia grupy woluminów.
Tworzenie woluminów i dodawanie wymaganych wpisów na /etc/fstab. 

![Sprawdzanie dysków podłączonych do programu PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

W podobny sposób urządzenie RAID jest tworzone na wierzchu zaszyfrowanej warstwy na dyskach. System plików jest tworzony na urządzeniu RAID i dodawany do /etc/fstab jako zwykłe urządzenie.

### <a name="considerations"></a>Zagadnienia do rozważenia

Zalecaną metodą jest LVM-on-Crypt.

Raid jest brany pod uwagę, gdy LVM nie może być używany z powodu określonych ograniczeń aplikacji/środowiska.

Użyjesz opcji EncryptFormatAll, informacje o tej funkcji https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmsznajdują się tutaj: .

Chociaż ta metoda może być wykonana podczas szyfrowania systemu operacyjnego, po prostu szyfrujemy dyski danych.

Ta procedura zakłada, że już przeglądowi warunków wstępnych wymienionych https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux tutaj: i tutaj https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart.

Wersja dwuprzebiegowa ADE jest na ścieżce deprecation i nie powinna być już używana w nowych szyfrowaniach ADE.

### <a name="procedure"></a>Procedura

Korzystając z konfiguracji "na krypcie", będziesz podążać za procesem opisanym poniżej:

>[!NOTE] 
>Używamy zmiennych w całym dokumencie, odpowiednio zastąp wartości.
## <a name="general-steps"></a>Ogólne kroki
### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej 
>[!NOTE] 
>Jest to opcjonalne, zaleca się zastosowanie tego na nowo wdrożonej maszynie wirtualnej.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli:
```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Dołącz dyski do maszyny wirtualnej:
Powtórz tę czynność dla $N liczby nowych dysków, które chcesz dołączyć do programu VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Cli:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Sprawdź, czy dyski są dołączone do maszyny Wirtualnej:
Program PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Sprawdź dyski podłączone](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) do interfejsu wiersza polecenia programu PowerShell:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Sprawdź dyski dołączone](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) CLI ![Portal: Sprawdź](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) dyski dołączone CLI OS:
```bash
lsblk 
```
![Sprawdzanie dysków dołączonych do portalu](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurowanie zaszyfrowanych dysków
Ta konfiguracja jest wykonywana, że poziom systemu operacyjnego, odpowiednie dyski są skonfigurowane dla tradycyjnego szyfrowania ADE:

Systemy plików są tworzone na dyskach.

Tymczasowe punkty instalacji są tworzone w celu zainstalowania systemów plików.

Systemy plików są skonfigurowane na /etc/fstab do zamontowania w czasie rozruchu.

Sprawdź literę urządzenia przypisaną do nowych dysków, w tym przykładzie używamy czterech dysków z danymi

```bash
lsblk 
```
![Sprawdzanie dysków podłączonych do systemu operacyjnego](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Utwórz system plików na każdym dysku.
To polecenie iteruje tworzenie systemu plików ext4 na każdym dysku zdefiniowanym w części "w" cyklu "for".
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Sprawdź dyski dołączone](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) os Znajdź UUID ostatnio utworzonych systemów plików, utwórz folder tymczasowy, aby go zamontować, dodaj odpowiednie wpisy na /etc/fstab i zainstaluj wszystkie systemy plików.

To polecenie również iteruje na każdym dysku zdefiniowanym w części "w" cyklu "for":
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Sprawdź, czy dyski są prawidłowo zamontowane:
```bash
lsblk
```
![Sprawdź tymczasowe systemy](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) plików zamontowane i skonfigurowane:
```bash
cat /etc/fstab
```
![Sprawdź fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Szyfrowanie dysków danych:
Program PowerShell przy użyciu funkcji KEK:
```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```
Cli przy użyciu KEK:
```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Sprawdzanie stanu szyfrowania

Przejdź do następnego kroku tylko wtedy, gdy wszystkie dyski są szyfrowane.

Program PowerShell:
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Sprawdź szyfrowanie ps](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Sprawdź szyfrowanie ![portalu interfejsu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) WIERSZA](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) POLECENIA: Sprawdź poziom systemu operacyjnego szyfrowania:
```bash
lsblk
```
![Sprawdź szyfrowanie CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Rozszerzenie doda systemy plików do "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (stare szyfrowanie) lub doda do "/etc/crypttab" (nowe szyfrowanie).

Nie należy modyfikować żadnego z tych plików.

Ten plik będzie dbać o aktywację tych dysków podczas procesu rozruchu, dzięki czemu mogą być później używane przez LVM lub RAID. 

Nie martw się o punkty instalacji w tym pliku, ponieważ ADE straci możliwość zamontowania dysków jako normalnego systemu plików po utworzeniu woluminu fizycznego lub urządzenia raid na tych zaszyfrowanych urządzeniach (które pozbędą się formatu systemu plików, którego użyliśmy podczas procesu przygotowania).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Usuwanie folderów tymczasowych i wpisów temp fstab
Odinstalowywania systemów plików na dyskach, które będą używane jako część LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
I usunąć /etc/fstab wpisy:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Sprawdź, czy dyski nie są zamontowane i czy wpisy na /etc/fstab zostały usunięte
```bash
lsblk
```
![Sprawdź tymczasowe systemy plików](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) odinstalowane i skonfigurowane:
```bash
cat /etc/fstab
```
![Sprawdź, czy wpisy temp fstab zostaną usunięte](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Dla LVM-on-crypt
Teraz, gdy dyski źródłowe są szyfrowane, można przystąpić do tworzenia struktur LVM.

Zamiast używać nazwy urządzenia, użyj /dev/mapper paths dla każdego z dysków, aby utworzyć wolumin fizyczny (na warstwie krypty na górze dysku, a nie na samym dysku).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurowanie lvm na wierzchu zaszyfrowanych warstw
#### <a name="create-the-physical-volumes"></a>Tworzenie woluminów fizycznych
Otrzymasz ostrzeżenie z pytaniem, czy jest ok, aby wymazać podpis systemu plików. 

Możesz kontynuować, wpisując "y" lub użyć echa "y", jak pokazano na rysunku:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>/dev/mapper/device names here need to be replaced for your actual values based on the output of lsblk.The /dev/mapper/device names here need to be replaced for your actual values based on the output of lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Weryfikowanie informacji o woluminach fizycznych
```bash
pvs
```
![sprawdzanie objętości fizycznych 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Tworzenie grupy woluminów
Tworzenie VG przy użyciu tych samych urządzeń, które zostały już zainicjowane
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Sprawdzanie informacji o grupie woluminów
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![sprawdzanie objętości fizycznych 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Tworzenie woluminów logicznych
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Sprawdzanie utworzonych woluminów logicznych
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![sprawdzić lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Tworzenie systemów plików na wierzchu struktury woluminów logicznych
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Tworzenie punktów instalacji dla nowych systemów plików
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Dodaj nowe systemy plików do /etc/fstab i zamontuj je
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Sprawdź, czy nowe systemy plików są zamontowane
```bash
lsblk -fs
df -h
```
![sprawdź woluminy](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) logiczne W tej odmianie lsblk wyświetlamy listę urządzeń pokazujących zależności w kolejności odwrotnej, ta opcja pomaga zidentyfikować urządzenia pogrupowane według woluminu logicznego zamiast oryginalnych nazw urządzeń /dev/sd[disk].

Ważne: Upewnij się, że opcja "nofail" została dodana do opcji punktu instalacji woluminów LVM utworzonych na urządzeniu zaszyfrowanym ADE. Ważne jest, aby uniknąć utknięcia systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji). 

Zaszyfrowany dysk zostanie odblokowany po zakończeniu procesu rozruchu, woluminy LVM i systemy plików zostaną automatycznie zamontowane.

Jeśli opcja nofail nie jest używana, system operacyjny nigdy nie przejdzie do etapu, na którym jest uruchamiana usługa ADE, a dyski danych są odblokowane i zainstalowane.

Można przetestować ponowne uruchomienie maszyny Wirtualnej i sprawdzania poprawności systemów plików są również automatycznie coraz montowane po czasie rozruchu. 

Należy wziąć pod uwagę, że proces ten może potrwać kilka minut w zależności od liczby systemów plików i rozmiarów
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Uruchom ponownie maszynę wirtualną i sprawdź po ponownym uruchomieniu komputera
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Dla RAID-on-Crypt
Teraz dyski bazowe są szyfrowane, możesz nadal tworzyć struktury RAID, takie same jak LVM, zamiast używać nazwy urządzenia, użyj ścieżek /dev/mapper dla każdego z dysków.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurowanie macierzy RAID na zaszyfrowanej warstwie dysków
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![tworzenie mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>/dev/mapper/device names here need to be replaced for your actual values based on the output of lsblk.The /dev/mapper/device names here need to be replaced for your actual values based on the output of lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Sprawdź/monitoruj tworzenie macierzy RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![sprawdzanie mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Utwórz system plików na nowym urządzeniu Raid:
```bash
mkfs.ext4 /dev/md10
```
Utwórz nową punkt instalacji dla systemu plików, dodaj nowy system plików do /etc/fstab i zamontuj go
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Sprawdź, czy nowe systemy plików są zamontowane
```bash
lsblk -fs
df -h
```
![sprawdzanie mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Ważne: Upewnij się, że opcja "nofail" została dodana do opcji punktu instalacji woluminów RAID utworzonych na urządzeniu zaszyfrowanym ADE. 

Jest bardzo ważne, aby uniknąć utknięcia systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji). 

Zaszyfrowany dysk zostanie odblokowany po zakończeniu procesu rozruchu, a woluminy RAID i systemy plików zostaną automatycznie zamontowane, dopóki nie zostaną odblokowane przez ADE, jeśli opcja nofail nie jest używana.

System operacyjny nigdy nie przejdzie do etapu, w którym uruchomiono ADE, a dyski danych są odblokowywanie i montowane.

Można przetestować ponowne uruchomienie maszyny Wirtualnej i sprawdzania poprawności systemów plików są również automatycznie coraz montowane po czasie rozruchu. Należy wziąć pod uwagę, że proces ten może potrwać kilka minut w zależności od liczby systemów plików i rozmiarów
```bash
shutdown -r now
```
A kiedy można się zalogować:
```bash
lsblk
df -h
```
## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)

