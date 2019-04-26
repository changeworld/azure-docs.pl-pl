---
title: Rozwiązywanie problemów z zmiany nazwy urządzenia maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, dlaczego urządzenie maszyny Wirtualnej systemu Linux nazw zmian i jak rozwiązać problem.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d636d5f31e78828a518882091af29b25f7219304
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362241"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Rozwiązywanie problemów z zmiany nazwy urządzenia maszyny Wirtualnej systemu Linux

W tym artykule opisano, dlaczego zmiany nazwy urządzenia, po ponownym uruchomieniem maszyny Wirtualnej z systemem Linux lub ponownie dołączyć dyski z danymi. Artykuł zawiera również rozwiązania tego problemu.

## <a name="symptoms"></a>Objawy
Podczas uruchamiania maszyn wirtualnych systemu Linux na platformie Microsoft Azure, mogą wystąpić następujące problemy:

- Maszyna wirtualna przechodzi do rozruchu po ponownym uruchomieniu.
- Jeśli dyski danych są odłączone, ponownie dołączyć dysku nazwy urządzenia są zmieniane.
- Aplikacji lub skryptu, który odwołuje się do dysku przy użyciu nazwy urządzenia nie działa, ponieważ nazwa urządzenia została zmieniona.

## <a name="cause"></a>Przyczyna

Ścieżki urządzenia w systemie Linux nie są gwarantowane być spójny na ponowne uruchomienie. Nazwy urządzeń składają się z głównych numerów (litery) oraz pomocniczych. Gdy sterownik urządzenia pamięci masowej systemu Linux wykryje nowe urządzenie, sterownik przypisuje głównych i pomocniczych numerów z zakresu dostępne na urządzeniu. Po usunięciu urządzenia z systemem numery urządzenia są zwalniane do ponownego wykorzystania.

Ten problem występuje, ponieważ urządzenie skanowanie w systemie Linux jest zaplanowane przez podsystem SCSI ma być wykonywana asynchronicznie. W rezultacie nazwa ścieżki urządzenia mogą się różnić między ponowne uruchomienie.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć trwałych nazewnictwa. Istnieją cztery sposoby korzystania z trwałego nazewnictwa: przez system plików etykietę, identyfikatora UUID, identyfikator lub ścieżka. Zalecamy używanie etykiety systemu plików lub identyfikator UUID dla maszyn wirtualnych systemu Linux platformy Azure.

Podaj większości dystrybucji `fstab` **nofail** lub **nobootwait** parametrów. Te parametry Włącz rozruch z dysku niepowodzenia instalacji podczas uruchamiania systemu. Zajrzyj do dokumentacji dystrybucji, aby uzyskać więcej informacji na temat tych parametrów. Aby uzyskać informacje na temat konfigurowania maszyny Wirtualnej z systemem Linux do użycia to identyfikator UUID, w przypadku dodania dysku danych, zobacz [nawiązywanie połączenia z maszyny Wirtualnej systemu Linux zainstaluj nowy dysk](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Po zainstalowaniu agenta systemu Linux platformy Azure na maszynie Wirtualnej agenta przy użyciu reguł Udev do konstruowania zestaw łączy symbolicznych w ścieżce /dev/disk/azure. Aplikacje i skrypty należy użyć reguły Udev, aby zidentyfikować dyski, które są dołączone do maszyny Wirtualnej, oraz typ dysku i jednostki LUN.

Jeśli masz już edytować Twojego fstab w taki sposób, że Twoja maszyna wirtualna nie jest uruchamiany i nie będzie SSH z maszyną wirtualną, możesz użyć [konsoli szeregowej maszyny Wirtualnej](./serial-console-linux.md) wprowadzenia [trybie jednego użytkownika](./serial-console-grub-single-user-mode.md) i modyfikować Twojego fstab.

### <a name="identify-disk-luns"></a>Identyfikowanie jednostek LUN dysku

Aplikacje za jednostki LUN w celu znalezienia wszystkich dołączonych dysków i do utworzenia łącza symbolicznego. Agent systemu Linux platformy Azure zawiera reguły Udev, skonfigurowanych linki symboliczne z jednostki LUN do urządzeń:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

Informacje o jednostce LUN z konta gościa Linux jest pobierana za pomocą `lsscsi` lub podobnego narzędzia:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Informacje o jednostce LUN gościa jest używany z metadanymi subskrypcji platformy Azure można zlokalizować dysku VHD w usłudze Azure Storage, która zawiera dane partycji. Na przykład, można użyć `az` interfejsu wiersza polecenia:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Odkryj UUID systemu plików przy użyciu blkid

Aplikacje i skrypty odczytywania danych wyjściowych z `blkid`, lub podobne źródeł informacji do utworzenia łącza symbolicznego w ścieżce /dev. Dane wyjściowe zawierają identyfikatory UUID wszystkich dysków dołączonych do maszyny Wirtualnej i jego plik skojarzone urządzenie:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Reguły usługi Udev agenta systemu Linux platformy Azure utworzyć zestaw łączy symbolicznych w ścieżce /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikacje Użyj linków, aby zidentyfikować urządzenia dysku rozruchowego i zasobów (efemerycznego) dysku. Na platformie Azure aplikacji powinien wyglądać w ścieżkach /dev/disk/azure/root-part1 lub /dev/disk/azure-resource-part1, aby wykryć te partycje.

Wszelkie dodatkowe partycje z `blkid` listy znajdują się na dysku z danymi. Aplikacje Obsługa UUID dla tych partycji i użyj ścieżki, aby rozpoznać nazwy urządzenia w czasie wykonywania:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Pobierz najnowsze reguły usługi Azure Storage

Aby uzyskać najnowsze zasady usługi Azure Storage, uruchom następujące polecenia:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Zobacz także

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Ubuntu: Za pomocą identyfikatora UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Trwałe nazewnictwa](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Co można zrobić identyfikatory UUID nie są dla Ciebie](https://www.linux.com/news/what-uuids-can-do-you)
- [Usługa udev jest: Wprowadzenie do zarządzania urządzeniami za pomocą nowoczesnego systemu Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

