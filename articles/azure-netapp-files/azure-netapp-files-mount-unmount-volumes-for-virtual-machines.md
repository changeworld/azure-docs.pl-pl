---
title: Zainstaluj woluminy Azure NetApp Files dla maszyn wirtualnych
description: Dowiedz się, jak zainstalować lub odinstalować wolumin dla maszyn wirtualnych z systemem Windows lub maszyn wirtualnych systemu Linux na platformie Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551543"
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

4. Jeśli chcesz zainstalować wolumin w systemie Windows przy użyciu systemu plików NFS:

    a. Najpierw zainstaluj wolumin na maszynie wirtualnej z systemem UNIX lub Linux.  
    b. Uruchom `chmod 777` lub `chmod 775` polecenie względem woluminu.  
    d. Zainstaluj wolumin za pośrednictwem klienta NFS w systemie Windows.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Często zadawane pytania dotyczące NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
