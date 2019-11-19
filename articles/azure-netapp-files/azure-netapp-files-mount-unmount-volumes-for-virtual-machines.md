---
title: Instalowanie lub odinstalowywanie woluminu Azure NetApp Files dla maszyn wirtualnych z systemem Windows lub Linux | Microsoft Docs
description: Opisuje sposób instalowania lub odinstalowywania woluminu dla maszyn wirtualnych lub maszyn wirtualnych z systemem Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173580"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux 

W razie potrzeby można zainstalować lub odinstalować wolumin dla maszyn wirtualnych z systemem Windows lub Linux.  Instrukcje instalacji dla maszyn wirtualnych z systemem Linux są dostępne na stronie Azure NetApp Files.  

1. Kliknij blok **woluminy** , a następnie wybierz wolumin, dla którego chcesz zainstalować program. 
2. Kliknij pozycję **instrukcje instalacji** na wybranym woluminie, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wolumin. 

    ![Instrukcje dotyczące instalacji systemu plików NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instrukcje dotyczące instalacji SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Jeśli używasz NFSv 4.1, użyj następującego polecenia, aby zainstalować system plików: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Jeśli chcesz, aby wolumin NFS został automatycznie zainstalowany podczas uruchamiania lub ponownego uruchamiania maszyny wirtualnej platformy Azure, Dodaj wpis do pliku `/etc/fstab` na hoście. 

    Na przykład: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` jest adresem IP woluminu Azure NetApp Files znalezionym w bloku właściwości woluminu.
    * `$FILEPATH` jest ścieżką eksportu woluminu Azure NetApp Files.
    * `$MOUNTPOINT` to katalog utworzony na hoście z systemem Linux używany do instalowania eksportu systemu plików NFS.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Często zadawane pytania dotyczące NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
