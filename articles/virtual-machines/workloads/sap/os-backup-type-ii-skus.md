---
title: System operacyjny kopii zapasowej i przywracania SAP HANA na platformie Azure (wystąpienia duże) wpisz jednostki SKU II | Dokumentacja firmy Microsoft
description: Wykonaj Operatign systemu z kopii zapasowej i przywracania dla SAP HANA na jednostki SKU II typu Azure (wystąpienia duże)
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
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d5caf5836b96555e01b55d408e51f3df2407d35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657608"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Kopia zapasowa systemu operacyjnego i przywracania dla jednostki SKU II typu

W tym dokumencie opisano kroki, aby wykonać kopię zapasową systemu operacyjnego i przywracania dla **jednostki SKU II typu** wystąpień dużych HANA. 

>[!NOTE]
>Skrypty kopii zapasowej systemu operacyjnego przy użyciu oprogramowania tyłu, który jest wstępnie zainstalowane na serwerze.  

Po ukończeniu inicjowania obsługi administracyjnej przez zespół zarządzania usługami firmy Microsoft, domyślnie serwer jest skonfigurowany z harmonogramem dwóch kopii zapasowych, aby utworzyć kopię zapasową w pełnym systemie operacyjnym. Harmonogram zadania tworzenia kopii zapasowej można sprawdzić za pomocą następującego polecenia:
```
#crontab –l
```
Można zmienić harmonogram tworzenia kopii zapasowych dowolnej chwili za pomocą następującego polecenia:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak wykonać kopię zapasową ręczne?

Kopia zapasowa systemu operacyjnego jest zaplanowane, za pomocą **zadania cron** już. Można jednak wykonać ręcznie również kopię systemu operacyjnego. Aby wykonać kopię zapasową ręczne, uruchom następujące polecenie:

```
#rear -v mkbackup
```
Następujący program ekranu pokazuje przykładowe ręcznego wykonywania kopii zapasowej:

![Jak](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak przywrócić z kopii zapasowej?

Pełnej kopii zapasowej lub pojedynczy plik można przywrócić z kopii zapasowej. Aby przywrócić, użyj następującego polecenia:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po przywróceniu plik jest odzyskiwana w bieżącym katalogu roboczym.

Polecenie przedstawia przywracania pliku */etc/fstabfrom* plik kopii zapasowej *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Należy skopiować plik do odpowiedniej lokalizacji po przywróceniu z kopii zapasowej.

Poniższy zrzut ekranu przedstawia przywracania pełnej kopii zapasowej:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak zainstalować narzędzie tyłu i zmienić konfigurację? 

Pakiety Relax i Odzyskaj (tylne) **preinstalowanym** w **jednostki SKU II typu** HANA dużych wystąpień i żadna akcja ze strony użytkownika. Bezpośrednio możesz rozpocząć korzystanie z tyłu dla kopii zapasowej systemu operacyjnego.
Jednak w sytuacjach, w którym należy zainstalować pakiety w ramach własnego, można wykonać wymienionych kroki, aby zainstalować i skonfigurować narzędzie do tyłu.

Aby zainstalować **tyłu** zapasowe pakietów, użyj następujących poleceń:

Aby uzyskać **SLES** systemie operacyjnym, użyj następującego polecenia:
```
#zypper install <rear rpm package>
```
Aby uzyskać **RHEL** systemie operacyjnym, użyj następującego polecenia: 
```
#yum install rear -y
```
Aby skonfigurować narzędzie do tyłu, musisz zaktualizować parametrów **OUTPUT_URL** i **BACKUP_URL** w *pliku /etc/rear/local.conf*.
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

Poniższy zrzut ekranu przedstawia przywracania pełnej kopii zapasowej: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
