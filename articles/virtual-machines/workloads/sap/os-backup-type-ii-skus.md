---
title: Tworzenie kopii zapasowej systemu operacyjnego i przywracania środowiska SAP Hana na platformie Azure (duże wystąpienia) typ jednostki SKU II | Dokumentacja firmy Microsoft
description: Wykonaj Operatign systemu z kopii zapasowej i przywracania dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU typu z II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708609"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Kopia zapasowa systemu operacyjnego i przywracania dla jednostek SKU typu II

W tym dokumencie opisano kroki, aby wykonywać kopie zapasowe poziomu pliku systemu operacyjnego i przywracania dla **jednostek SKU typu II** dużych wystąpień HANA. 

>[!NOTE]
>Skrypty kopii zapasowej systemu operacyjnego korzysta z oprogramowania tylko przemyślane decyzje, które jest wstępnie instalowane na serwerze.  

Po zakończeniu aprowizacji przez zespół zarządzania usługami firmy Microsoft, domyślnie serwer jest skonfigurowany z harmonogramem dwie kopie zapasowe do tworzenia kopii zapasowej systemu plików na poziomie Utwórz kopię zapasową systemu operacyjnego. Harmonogram zadania tworzenia kopii zapasowej można sprawdzić za pomocą następującego polecenia:
```
#crontab –l
```
Możesz zmienić harmonogram tworzenia kopii zapasowych w dowolnym momencie przy użyciu następującego polecenia:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak wykonać kopię zapasową ręczne?

Kopia zapasowa systemu plików systemu operacyjnego jest zaplanowane, za pomocą **zadania cron** już. Można jednak wykonać systemu operacyjnego plik Kopia zapasowa na poziomie także ręcznie. Aby ręcznie wykonaj kopię zapasową, uruchom następujące polecenie:

```
#rear -v mkbackup
```
Poniższy pokaz ekranie przedstawiono przykładowe ręcznego tworzenia kopii zapasowej:

![Jak](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak przywrócić kopię zapasową?

Możesz przywrócić pełnej kopii zapasowej lub pojedynczy plik z kopii zapasowej. Aby przywrócić, użyj następującego polecenia:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po przywróceniu plik jest odzyskiwany w bieżącym katalogu roboczym.

Następujące polecenie wyświetla Przywracanie pliku */etc/fstabfrom* pliku kopii zapasowej *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Należy skopiować plik do żądanej lokalizacji, po przywróceniu z kopii zapasowej.

Poniższy zrzut ekranu przedstawia Przywracanie pełnej kopii zapasowej:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak zainstalować narzędzie do tyłu i zmienić konfigurację? 

Pakiety Relax i odzyskiwanie (tylne) **preinstalowanym** w **jednostek SKU typu II** dużych wystąpień HANA i wykonywać żadnych czynności ze strony użytkownika. Bezpośrednio aby zacząć korzystać z tyłu do utworzenia kopii zapasowej systemu operacyjnego.
Jednak w sytuacjach, w którym należy zainstalować te pakiety w ramach własnego, możesz wykonać wymienione kroki, aby zainstalować i skonfigurować narzędzie do tyłu.

Aby zainstalować **tyłu** kopie zapasowe pakietów, użyj następujących poleceń:

Aby uzyskać **SLES** system operacyjny, użyj następującego polecenia:
```
#zypper install <rear rpm package>
```
Aby uzyskać **RHEL** system operacyjny, użyj następującego polecenia: 
```
#yum install rear -y
```
Aby skonfigurować narzędzie do tyłu, należy zaktualizować parametry **OUTPUT_URL** i **BACKUP_URL** w *pliku /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Poniższy zrzut ekranu przedstawia Przywracanie pełnej kopii zapasowej: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
