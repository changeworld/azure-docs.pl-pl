---
title: Konfigurowanie domyślnej domeny NFSv4.1 dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania klienta NFS do korzystania z pliku NFSv4.1 z plikami NetApp platformy Azure.
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
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906288"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurowanie domyślnej domeny NFSv4.1 dla usługi Azure NetApp Files

System NFSv4 wprowadza koncepcję domeny uwierzytelniania. Usługa Azure NetApp Files obsługuje obecnie mapowanie użytkowników tylko do katalogu głównego z usługi do klienta systemu plików NFS. Aby korzystać z funkcji NFSv4.1 z usługą Azure NetApp Files, należy zaktualizować klienta systemu plików NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Domyślne zachowanie mapowania użytkownika/grupy

Domyślne mapowanie `nobody` katalogu głównego jest domyślne dla użytkownika, ponieważ domena NFSv4 jest ustawiona na `localdomain`. Po zainstalowaniu woluminu NFSv4.1 plików Usługi Azure NetApp jako głównego, zobaczysz uprawnienia do plików w następujący sposób:  

![Domyślne zachowanie mapowania użytkownika/grupy dla nfsv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak pokazano w powyższym `file1` przykładzie, użytkownik dla powinien być `root`, ale mapuje domyślnie. `nobody`  W tym artykule pokazano, jak ustawić `file1` użytkownika na `root`.  

## <a name="steps"></a>Kroki 

1. Edytuj `/etc/idmapd.conf` plik na kliencie systemu plików NFS.   
    Usuń komentarz do `#Domain` wiersza (czyli `#` usuń ją z wiersza) i zmień wartość `localdomain` na . `defaultv4iddomain.com` 

    Konfiguracja początkowa: 
    
    ![Konfiguracja początkowa systemu plików NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Zaktualizowana konfiguracja:
    
    ![Zaktualizowana konfiguracja systemu plików NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odinstaluj wszystkie aktualnie zamontowane woluminy NFS.
3. Zaktualizuj `/etc/idmapd.conf` plik.
4. Uruchom `rpcbind` ponownie usługę na`service rpcbind restart`hoście ( ) lub po prostu uruchom ponownie hosta.
5. W razie potrzeby zamontuj woluminy NFS.   

    Zobacz [Instalowanie lub odinstalowywszycie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

W poniższym przykładzie przedstawiono wynikową zmianę użytkownika/grupy: 

![Wynikowa konfiguracja systemu plików NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak pokazano w przykładzie, użytkownik/grupa `nobody` `root`zmieniła się teraz z na .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Zachowanie innych użytkowników i grup (nieszawisowych)

Usługa Azure NetApp Files obsługuje użytkowników lokalnych (użytkowników utworzonych lokalnie na hoście), którzy mają uprawnienia skojarzone z plikami lub folderami w woluminach NFSv4.1. Jednak usługa nie obsługuje obecnie mapowania użytkowników/grup w wielu węzłach. W związku z tym użytkownicy utworzyli na jednym hoście nie mapują domyślnie użytkowników utworzonych na innym hoście. 

W poniższym `Host1` przykładzie ma trzy`testuser01`istniejące `testuser02` `testuser03`konta użytkowników testowych ( , , ), ): 

![Wynikowa konfiguracja systemu plików NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Na `Host2`, należy zauważyć, że konta użytkownika testowego nie zostały utworzone, ale ten sam wolumin jest zainstalowany na obu hostach:

![Wynikowa konfiguracja systemu plików NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Następny krok 

[Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

