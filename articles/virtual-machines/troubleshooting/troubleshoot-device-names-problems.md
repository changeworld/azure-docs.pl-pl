---
title: Rozwiązywanie problemów ze zmianami nazw urządzeń maszyn wirtualnych z systemem Linux na platformie Azure | Dokumenty firmy Microsoft
description: Wyjaśnia, dlaczego nazwy urządzeń maszyn wirtualnych z systemem Linux zmieniają się i jak rozwiązać problem.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058201"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Rozwiązywanie problemów ze zmianami nazwy urządzenia maszyny Wirtualnej w systemie Linux

W tym artykule wyjaśniono, dlaczego nazwy urządzeń zmieniają się po ponownym uruchomieniu maszyny Wirtualnej systemu Linux lub ponownym podmociniu dysków z danymi. Artykuł zawiera również rozwiązania tego problemu.

## <a name="symptoms"></a>Objawy
Podczas uruchamiania maszyn wirtualnych z systemem Linux na platformie Microsoft Azure mogą wystąpić następujące problemy:

- Maszyna wirtualna nie uruchamia się po ponownym uruchomieniu.
- Gdy dyski danych są odłączane i ponownie podłączane, nazwy urządzeń dyskowych są zmieniane.
- Aplikacja lub skrypt, który odwołuje się do dysku przy użyciu nazwy urządzenia, kończy się niepowodzeniem, ponieważ nazwa urządzenia została zmieniona.

## <a name="cause"></a>Przyczyna

Ścieżki urządzeń w systemie Linux nie są gwarantowane, aby być spójne podczas ponownego uruchamiania. Nazwy urządzeń składają się z głównych cyfr (liter) i pomniejszych. Gdy sterownik urządzenia magazynującego Linuksa wykryje nowe urządzenie, kierowca przypisuje do urządzenia główne i pomocnicze numery z dostępnego zakresu. Po usunięciu urządzenia numery urządzeń są zwalniane do ponownego użycia.

Problem występuje, ponieważ skanowanie urządzenia w systemie Linux jest zaplanowane przez podsystem SCSI odbywa się asynchronicznie. W rezultacie nazwa ścieżki urządzenia może się różnić w zależności od ponownego uruchomienia.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj nazewnictwa trwałego. Istnieją cztery sposoby używania nazewnictwa trwałego: według etykiety systemu plików, identyfikatora UUID, identyfikatora lub ścieżki. Zaleca się używanie etykiety systemu plików lub UUID dla maszyn wirtualnych z systemem Azure Linux.

Większość dystrybucji zapewnia `fstab` parametry **nofail** lub **nobootwait.** Te parametry umożliwiają uruchomienie systemu, gdy dysk nie może zamontować podczas uruchamiania. Więcej informacji na temat tych parametrów można znaleźć w dokumentacji dystrybucji. Aby uzyskać informacje na temat konfigurowania maszyny Wirtualnej z systemem Linux do używania UUID podczas dodawania dysku z danymi, zobacz [Łączenie się z maszyną wirtualną z systemem Linux](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)w celu zainstalowania nowego dysku .

Gdy agent systemu Azure Linux jest zainstalowany na maszynie Wirtualnej, agent używa reguł Udev do konstruowania zestawu dowiązań symbolicznych w ramach /dev/disk/azure path. Aplikacje i skrypty używają reguł Udev do identyfikowania dysków dołączonych do maszyny Wirtualnej, wraz z typem dysku i LUN dysku.

Jeśli twój fstab został już edytowany w taki sposób, że maszyna wirtualna nie uruchamia się i nie można ssh do maszyny wirtualnej, można użyć [konsoli szeregowej maszyny Wirtualnej,](./serial-console-linux.md) aby przejść do [trybu pojedynczego użytkownika](./serial-console-grub-single-user-mode.md) i zmodyfikować fstab.

### <a name="identify-disk-luns"></a>Identyfikowanie sieci LUN dysków

Aplikacje używają LUN, aby znaleźć wszystkie podłączone dyski i skonstruować dowiązania symboliczne. Agent systemu Azure Linux zawiera reguły Udev, które konfigurują łącza symboliczne z jednostki LUN do urządzeń:

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

Informacje o jednostce LUN z konta `lsscsi` gościa systemu Linux są pobierane za pomocą lub podobnego narzędzia:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Informacje o jednostce LUN gościa są używane z metadanymi subskrypcji platformy Azure w celu zlokalizowania dysku VHD w usłudze Azure Storage zawierającego dane partycji. Na przykład można użyć `az` interfejsu wiersza polecenia:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Odnajduj identyfikatory UUID systemu plików przy użyciu blkid

Aplikacje i skrypty `blkid`odczytywać dane wyjściowe lub podobne źródła informacji, do konstruowania dowiązań symbolicznych w /dev path. Dane wyjściowe pokazują identyfikatory UUID wszystkich dysków podłączonych do maszyny Wirtualnej i skojarzonego z nimi pliku urządzenia:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Reguły Udev agenta systemu Azure Linux konstruują zestaw dowiązań symbolicznych w ścieżce /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikacje używają łączy do identyfikowania urządzenia dysku rozruchowego i dysku zasobu (efemerycznego). Na platformie Azure aplikacje powinny wyglądać w /dev/disk/azure/root-part1 lub /dev/disk/azure-resource-part1 ścieżek do odnajdowania tych partycji.

Wszystkie dodatkowe partycje `blkid` z listy znajdują się na dysku danych. Aplikacje utrzymują identyfikator UUID dla tych partycji i używają ścieżki do odnajdowania nazwy urządzenia w czasie wykonywania:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Pobierz najnowsze reguły usługi Azure Storage

Aby uzyskać najnowsze reguły usługi Azure Storage, uruchom następujące polecenia:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Zobacz też

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Ubuntu: Korzystanie z UUID](https://help.ubuntu.com/community/UsingUUID)
- [Czerwony kapelusz: Trwałe nazewnictwo](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Co uuids może zrobić dla Ciebie](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Wprowadzenie do zarządzania urządzeniami w nowoczesnym systemie Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

