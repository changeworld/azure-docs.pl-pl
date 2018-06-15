---
title: Rozwiązywanie problemów z zmiany nazwy urządzenia maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, dlaczego urządzenia maszyny Wirtualnej systemu Linux nazwy zmian i jak rozwiązać problem.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 1b500cc279cf9b92a3358d66e6db59dbf41d73b8
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072355"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Rozwiązywanie problemów z zmiany nazwy urządzenia maszyny Wirtualnej systemu Linux

W tym artykule opisano, dlaczego nazwy urządzenia zmienić po ponownie uruchomić Maszynę wirtualną systemu Linux lub podłącz dysków z danymi. Artykuł zawiera również rozwiązania tego problemu.

## <a name="symptoms"></a>Objawy
Podczas uruchamiania maszyn wirtualnych systemu Linux na platformie Microsoft Azure, mogą wystąpić następujące problemy:

- Maszyna wirtualna nie można uruchomić po ponownym uruchomieniu.
- Gdy dyski danych są odłączyć i ponownie nałożona, nazwy urządzenia dysków są zmieniane.
- Aplikacja lub skrypt, który odwołuje się do dysku przy użyciu nazwy urządzenia nie działa, ponieważ zmieniono nazwę urządzenia.

## <a name="cause"></a>Przyczyna

Ścieżki urządzeń w systemie Linux nie ma gwarancji spójne uruchomieniach. Nazwy urządzenia składają się z głównych numerów (liter) oraz pomocniczych. Sterownika urządzenia pamięci masowej Linux wykrycie nowego urządzenia sterownika przypisuje główne i pomocnicze cyfry z przedziału na urządzeniu. Gdy urządzenie jest usuwane, liczby urządzeń są zwalniane do ponownego użycia.

Problem występuje, ponieważ urządzenie skanowanie w systemie Linux zaplanowanych przez podsystem SCSI operacji asynchronicznie. W związku z tym urządzenia nazwa ścieżki może się różnić uruchomieniach. 

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Użyj trwałego nazewnictwa. Istnieją cztery sposoby używania, trwałe nazewnictwa: przez system plików etykietę, identyfikatora UUID, identyfikator lub ścieżki. Zalecamy używanie Etykieta systemu plików lub identyfikator UUID dla maszyn wirtualnych systemu Linux platformy Azure. 

Podaj większości dystrybucji `fstab` **nofail** lub **nobootwait** parametrów. Te parametry Włącz system rozruchu po awarii dysku do zainstalowania podczas uruchamiania. Zajrzyj do dokumentacji dystrybucji, aby uzyskać więcej informacji o tych parametrach. Aby uzyskać informacje na temat konfigurowania maszyny Wirtualnej systemu Linux, aby użyć UUID po dodaniu dysku danych, zobacz [Połącz z maszyny Wirtualnej systemu Linux, aby zainstalować nowy dysk](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Po zainstalowaniu agenta systemu Linux platformy Azure na maszynie Wirtualnej agenta wykorzystuje Udev reguły do utworzenia zestaw łącza symbolicznego w ścieżce /dev/disk/azure. Aplikacje i skrypty umożliwia zidentyfikowanie dysków dołączonych do maszyny Wirtualnej, oraz typ dysku i numery LUN dysku Udev reguły.

### <a name="identify-disk-luns"></a>Identyfikowanie jednostek LUN dysku

Aplikacje używają jednostek LUN, aby znaleźć wszystkie dołączone dyski i utworzyć łącza symbolicznego. Agent systemu Linux Azure oferuje Udev reguł, które skonfigurować linki symboliczne z jednostki LUN, na urządzeniach:

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
                                 
Informacje o jednostce LUN z konta gościa Linux są pobierane przy użyciu `lsscsi` lub podobne narzędzie:

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Informacje o jednostce LUN gościa jest używany z metadanymi subskrypcji platformy Azure można znaleźć w usłudze Azure Storage, zawierający dane partycji dysku VHD. Na przykład można użyć `az` interfejsu wiersza polecenia:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Odnajdywanie UUID systemu plików za pomocą blkid

Aplikacje i skrypty odczytać dane wyjściowe `blkid`, lub podobne źródła informacji do utworzenia łącza symbolicznego w ścieżce /dev. Dane wyjściowe zawierają identyfikatory wszystkich dysków dołączonych do maszyny Wirtualnej i ich plik skojarzone urządzenia:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Reguły Udev agenta systemu Linux Azure utworzyć zestaw łącza symbolicznego w ścieżce /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikacje do identyfikacji urządzenia dysku rozruchowego i zasobu dysku (efemeryczne) używają łącza. Na platformie Azure aplikacje powinien wyglądać w ścieżkach /dev/disk/azure/root-part1 lub /dev/disk/azure-resource-part1 odnajdywania tych partycji.

Wszelkie dodatkowe partycje z `blkid` listy znajdują się na dysk z danymi. Aplikacje Obsługa UUID dla tych partycji i użyć ścieżki rozpoznać nazwy urządzenia w czasie wykonywania:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Pobierz najnowsze zasady usługi Azure Storage

Aby uzyskać najnowsze zasady usługi Azure Storage, uruchom następujące polecenia:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Zobacz także

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Ubuntu: Przy użyciu identyfikatora UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Stałe nazewnictwa](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Identyfikatory UUID czynności dla Ciebie](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Wprowadzenie do zarządzania urządzeniami w nowoczesnych systemu Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

