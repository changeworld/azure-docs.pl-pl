---
title: Tworzenie kopii zapasowych i przywracanie systemu SAP HANA na platformie Azure (duże wystąpienia) typu II SKU| Dokumenty firmy Microsoft
description: Wykonywanie kopii zapasowych i przywracania systemu operacyjnego dla sap HANA na platformie Azure (duże wystąpienia) SKU typu II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616862"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Tworzenie kopii zapasowych i przywracanie systemu operacyjnego dla jednostek SKU typu II sygnatury wersji 3

W tym dokumencie opisano kroki wykonywania kopii zapasowej i przywracania na poziomie pliku systemu operacyjnego dla **jednostek SKU typu II** dużych wystąpień wersji 3. 

>[!Important]
> **Ten artykuł nie dotyczy wdrożeń jednostek SKU typu II w sygnaturach dużych wystąpień wersji 4 HANA.** Rozruch LUNS typu II HANA jednostek dużych wystąpień, które są wdrażane w wersji 4 HANA duże wystąpienia sygnatury mogą być archiwizowane z migawek magazynu, jak to jest w przypadku typu I SKU już w wersji 3 znaczki


>[!NOTE]
>Skrypty kopii zapasowej systemu operacyjnego używają oprogramowania ReaR, które jest wstępnie zainstalowane na serwerze.  

Po zakończeniu inicjowania `Service Management` obsługi administracyjnej przez zespół firmy Microsoft, domyślnie serwer jest skonfigurowany z dwoma harmonogramami tworzenia kopii zapasowych w celu utworzenia kopii zapasowej poziomu systemu plików z tyłu systemu operacyjnego. Harmonogramy zadań tworzenia kopii zapasowej można sprawdzić za pomocą następującego polecenia:
```
#crontab –l
```
Harmonogram tworzenia kopii zapasowych można zmienić w dowolnym momencie, używając następującego polecenia:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak wykonać ręczną kopię zapasową?

Kopia zapasowa systemu plików systemu operacyjnego jest już zaplanowana przy użyciu **zadania cron.** Można jednak wykonać kopię zapasową na poziomie pliku systemu operacyjnego również ręcznie. Aby wykonać ręczną kopię zapasową, uruchom następujące polecenie:

```
#rear -v mkbackup
```
Na poniższym ekranie pokazano przykładową ręczną kopię zapasową:

![Jak](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak przywrócić kopię zapasową?

Możesz przywrócić pełną kopię zapasową lub pojedynczy plik z kopii zapasowej. Aby przywrócić, użyj następującego polecenia:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po przywróceniu plik jest odzyskiwany w bieżącym katalogu roboczym.

Następujące polecenie pokazuje przywracanie pliku */etc/fstabz* kopii zapasowej pliku *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Po przywróceniu pliku z kopii zapasowej należy skopiować plik do żądanej lokalizacji.

Poniższy zrzut ekranu przedstawia przywracanie pełnej kopii zapasowej:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak zainstalować narzędzie ReaR i zmienić konfigurację? 

Pakiety Relax-and-Recover (ReaR) są **wstępnie zainstalowane** w **jednostkach SKU typu II** dużych wystąpień HANA i nie są wymagane żadne działania od Ciebie. Można bezpośrednio rozpocząć korzystanie z ReaR dla kopii zapasowej systemu operacyjnego.
Jednak w sytuacji, gdy trzeba zainstalować pakiety we własnym, można wykonać wymienione kroki, aby zainstalować i skonfigurować narzędzie ReaR.

Aby zainstalować pakiety kopii zapasowych **ReaR,** użyj następujących poleceń:

W przypadku systemu operacyjnego **SLES** należy użyć następującego polecenia:
```
#zypper install <rear rpm package>
```
W przypadku systemu operacyjnego **RHEL** należy użyć następującego polecenia: 
```
#yum install rear -y
```
Aby skonfigurować narzędzie ReaR, należy zaktualizować parametry **OUTPUT_URL** i **BACKUP_URL** w *pliku /etc/rear/local.conf*.
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

Poniższy zrzut ekranu przedstawia przywracanie ![pełnej kopii zapasowej: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
