---
title: Konfigurowanie lvm i raid na zaszyfrowanych urządzeniach — szyfrowanie dysków azure
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania lvm i RAID na zaszyfrowanych urządzeniach dla maszyn wirtualnych z systemem Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657442"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurowanie lvm i raid na zaszyfrowanych urządzeniach

Ten artykuł jest procesem krok po kroku, jak wykonać logiczne zarządzanie woluminami (LVM) i RAID na zaszyfrowanych urządzeniach. Proces ten ma zastosowanie do następujących środowisk:

- Dystrybucje Linuksa
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Rozszerzenie jednoprzebiegowe szyfrowania dysków platformy Azure
- Rozszerzenie dwuprzebiegowe usługi Azure Disk Encryption


## <a name="scenarios"></a>Scenariusze

Procedury w tym artykule obsługują następujące scenariusze:  

- Konfigurowanie lvm na zaszyfrowanych urządzeniach (LVM-on-crypt)
- Konfigurowanie macierzy RAID na zaszyfrowanych urządzeniach (RAID-on-crypt)

Po zaszyfrowaniu urządzenia lub urządzeń bazowych można utworzyć struktury LVM lub RAID na tej zaszyfrowanej warstwie. 

Woluminy fizyczne (PV) są tworzone na wierzchu zaszyfrowanej warstwy. Woluminy fizyczne są używane do tworzenia grupy woluminów. Tworzenie woluminów i dodawanie wymaganych wpisów na /etc/fstab. 

![Diagram warstw struktur LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

W podobny sposób urządzenie RAID jest tworzone na wierzchu zaszyfrowanej warstwy na dyskach. System plików jest tworzony na urządzeniu RAID i dodawany do /etc/fstab jako zwykłe urządzenie.

## <a name="considerations"></a>Zagadnienia do rozważenia

Zalecamy korzystanie z LVM-on-crypt. RAID jest opcją, gdy LVM nie może być używany z powodu określonych ograniczeń aplikacji lub środowiska.

Użyjesz opcji **EncryptFormatAll.** Aby uzyskać więcej informacji na temat tej opcji, zobacz [Korzystanie z funkcji EncryptFormatAll dla dysków danych na maszynach wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Chociaż można użyć tej metody, gdy jesteś również szyfrowania systemu operacyjnego, jesteśmy po prostu szyfrowania dysków danych tutaj.

Procedury zakładają, że już przejrzano wymagania wstępne w [scenariuszach szyfrowania dysków platformy Azure na maszynach wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) i w [przewodniku Szybki start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart).

Wersja dwuprzebiegowa usługi Azure Disk Encryption jest na ścieżce wycofania i nie powinna być już używana w nowych szyfrowaniach.

## <a name="general-steps"></a>Ogólne kroki

Podczas korzystania z konfiguracji "on-crypt", użyj procesu opisanego w poniższych procedurach.

>[!NOTE] 
>Używamy zmiennych w całym artykule. Odpowiednio zastąp wartości.

### <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej 
Następujące polecenia są opcjonalne, ale zaleca się zastosowanie ich na nowo wdrożonej maszynie wirtualnej (VM).

Program PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Interfejsu wiersza polecenia platformy Azure:

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
### <a name="attach-disks-to-the-vm"></a>Dołączanie dysków do maszyny Wirtualnej
Powtórz następujące polecenia `$N` dla liczby nowych dysków, które chcesz dołączyć do maszyny Wirtualnej.

Program PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Interfejsu wiersza polecenia platformy Azure:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Sprawdź, czy dyski są podłączone do maszyny Wirtualnej
Program PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista dołączonych dysków w programie PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Interfejsu wiersza polecenia platformy Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista dołączonych dysków w wierszu polecenia platformy Azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista dołączonych dysków w portalu](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

System operacyjny:

```bash
lsblk 
```
![Lista dołączonych dysków w os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurowanie zaszyfrowanych dysków
Ta konfiguracja jest wykonywana na poziomie systemu operacyjnego. Odpowiednie dyski są skonfigurowane do tradycyjnego szyfrowania za pomocą szyfrowania dysków platformy Azure:

- Systemy plików są tworzone na dyskach.
- Tymczasowe punkty instalacji są tworzone w celu zainstalowania systemów plików.
- Systemy plików są skonfigurowane na /etc/fstab do zamontowania w czasie rozruchu.

Sprawdź literę urządzenia przypisaną do nowych dysków. W tym przykładzie używamy czterech dysków z danymi.

```bash
lsblk 
```
![Dyski danych podłączone do systemu operacyjnego](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Tworzenie systemu plików na każdym dysku
To polecenie iteruje tworzenie systemu plików ext4 na każdym dysku zdefiniowanym w części "w" cyklu "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Tworzenie systemu plików ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Znajdź uniwersalny unikatowy identyfikator (UUID) ostatnio utworzonych systemów plików, utwórz folder tymczasowy, dodaj odpowiednie wpisy na /etc/fstab i zainstaluj wszystkie systemy plików.

To polecenie również iteruje na każdym dysku zdefiniowanym w części "w" cyklu "for":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Sprawdź, czy dyski są prawidłowo zamontowane
```bash
lsblk
```
![Lista zamontowanych tymczasowych systemów plików](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Sprawdź również, czy dyski są skonfigurowane:

```bash
cat /etc/fstab
```
![Informacje o konfiguracji za pośrednictwem fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Szyfrowanie dysków z danymi
Program PowerShell przy użyciu klucza szyfrowania klucza (KEK):

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

Narzędzie CLI platformy Azure przy użyciu narzędzia KEK:

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
![Stan szyfrowania w programie PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Interfejsu wiersza polecenia platformy Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stan szyfrowania w wierszu polecenia platformy Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Stan szyfrowania w portalu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Poziom systemu operacyjnego:

```bash
lsblk
```
![Stan szyfrowania w os](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Rozszerzenie doda systemy plików do /var/lib/azure_disk_encryption_config/azure_crypt_mount (stare szyfrowanie) lub do /etc/crypttab (nowe szyfrowanie).

>[!NOTE] 
>Nie należy modyfikować żadnego z tych plików.

Ten plik zajmie się aktywacją tych dysków podczas procesu rozruchu, dzięki czemu LVM lub RAID mogą z nich korzystać później. 

Nie martw się o punkty instalacji w tym pliku. Szyfrowanie dysków platformy Azure utraci możliwość zamontowania dysków jako normalnego systemu plików po utworzeniu woluminu fizycznego lub urządzenia RAID na tych zaszyfrowanych urządzeniach. (Spowoduje to usunięcie formatu systemu plików, którego użyliśmy podczas procesu przygotowania).

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Usuwanie folderów tymczasowych i tymczasowych wpisów fstab
Odinstaluj systemy plików na dyskach, które będą używane jako część LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
I usunąć /etc/fstab wpisy:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Sprawdź, czy dyski nie są zamontowane i czy wpisy na /etc/fstab zostały usunięte

```bash
lsblk
```
![Weryfikacja, czy tymczasowe systemy plików są odinstalowane](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Sprawdź, czy dyski są skonfigurowane:
```bash
cat /etc/fstab
```
![Weryfikacja, czy tymczasowe wpisy fstab są usuwane](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Kroki dla LVM-on-crypt
Teraz, gdy dyski źródłowe są szyfrowane, można utworzyć struktury LVM.

Zamiast używać nazwy urządzenia, użyj /dev/mapper paths dla każdego z dysków, aby utworzyć wolumin fizyczny (na warstwie krypty na górze dysku, a nie na samym dysku).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurowanie lvm na wierzchu zaszyfrowanych warstw
#### <a name="create-the-physical-volumes"></a>Tworzenie woluminów fizycznych
Otrzymasz ostrzeżenie z pytaniem, czy jest ok, aby wymazać podpis systemu plików. Kontynuuj, wprowadzając **y**, lub użyj **echo "y",** jak pokazano na rysunku:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Weryfikacja utworzenia woluminu fizycznego](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Nazwy /dev/mapper/device w tym miejscu muszą zostać zastąpione dla rzeczywistych wartości na podstawie danych wyjściowych **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Weryfikowanie informacji o woluminach fizycznych
```bash
pvs
```

![Informacje o woluminach fizycznych](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Tworzenie grupy woluminów
Utwórz grupę woluminów przy użyciu tych samych urządzeń, które zostały już zainicjowane:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Sprawdź informacje dotyczące grupy woluminów

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informacje dla grupy woluminów](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Tworzenie woluminów logicznych

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Sprawdzanie utworzonych woluminów logicznych

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informacje o woluminach logicznych](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Tworzenie systemów plików na wierzchu struktur dla woluminów logicznych

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Tworzenie punktów instalacji dla nowych systemów plików

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

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Sprawdź, czy nowe systemy plików są zamontowane

```bash
lsblk -fs
df -h
```
![Informacje dotyczące zamontowanych systemów plików](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

W tej odmianie **LSBLK**wymieniamy urządzenia pokazujące zależności w odwrotnej kolejności. Ta opcja pomaga zidentyfikować urządzenia pogrupowane według woluminu logicznego zamiast oryginalnych nazw urządzeń /dev/sd[disk].

Ważne jest, aby upewnić się, że opcja **nofail** jest dodawany do opcji punktu instalacji woluminów LVM utworzonych na urządzeniu zaszyfrowanym za pomocą szyfrowania dysku Azure. Zapobiega utknięciu systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji).

Jeśli nie używasz opcji **nofail:**

- System operacyjny nigdy nie przejdzie do etapu, w którym jest uruchamiane szyfrowanie dysków azure, a dyski danych są odblokowywanie i montowane. 
- Zaszyfrowane dyski zostaną odblokowane po zakończeniu procesu rozruchu. Woluminy LVM i systemy plików zostaną automatycznie zainstalowane, dopóki usługa Azure Disk Encryption ich nie odblokuje. 

Można przetestować ponowne uruchomienie maszyny Wirtualnej i sprawdzić, czy systemy plików są również automatycznie coraz zainstalowany po czasie rozruchu. Ten proces może potrwać kilka minut, w zależności od liczby i rozmiarów systemów plików.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Uruchom ponownie maszynę wirtualną i sprawdź po ponownym uruchomieniu komputera

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Kroki dla RAID-on-crypt
Teraz, gdy dyski źródłowe są szyfrowane, można kontynuować tworzenie struktur RAID. Proces jest taki sam jak dla LVM, ale zamiast używać nazwy urządzenia, użyj /dev/mapper paths dla każdego dysku.

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
![Informacje o skonfigurowanym macierzy RAID za pomocą polecenia mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Nazwy /dev/mapper/device w tym miejscu należy zastąpić rzeczywistymi wartościami, na podstawie danych wyjściowych **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Sprawdzanie/monitorowanie tworzenia macierzy RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stan raidu](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Tworzenie systemu plików na nowym urządzeniu RAID
```bash
mkfs.ext4 /dev/md10
```

Utwórz nowy punkt instalacji dla systemu plików, dodaj nowy system plików do /etc/fstab i zamontuj go:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Sprawdź, czy nowy system plików jest zainstalowany:

```bash
lsblk -fs
df -h
```
![Informacje dotyczące zamontowanych systemów plików](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Ważne jest, aby upewnić się, że opcja **nofail** jest dodawana do opcji punktu instalacji woluminów RAID utworzonych na urządzeniu zaszyfrowanym za pomocą szyfrowania dysku Azure. Zapobiega utknięciu systemu operacyjnego podczas procesu rozruchu (lub w trybie konserwacji).

Jeśli nie używasz opcji **nofail:**

- System operacyjny nigdy nie przejdzie do etapu, w którym jest uruchamiane szyfrowanie dysków azure, a dyski danych są odblokowywanie i montowane.
- Zaszyfrowane dyski zostaną odblokowane po zakończeniu procesu rozruchu. Woluminy RAID i systemy plików zostaną automatycznie zainstalowane, dopóki usługa Azure Disk Encryption ich nie odblokuje.

Można przetestować ponowne uruchomienie maszyny Wirtualnej i sprawdzić, czy systemy plików są również automatycznie coraz zainstalowany po czasie rozruchu. Ten proces może potrwać kilka minut, w zależności od liczby i rozmiarów systemów plików.

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

