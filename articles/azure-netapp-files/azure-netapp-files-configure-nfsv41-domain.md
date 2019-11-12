---
title: Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób konfigurowania klienta NFS do korzystania z NFSv 4.1 z Azure NetApp Files.
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
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906288"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files

NFSv4 wprowadza koncepcję domeny uwierzytelniania. Azure NetApp Files obecnie obsługuje mapowanie użytkownika tylko do katalogu głównego z usługi do klienta NFS. Aby można było używać funkcji NFSv 4.1 z Azure NetApp Files, należy zaktualizować klienta systemu plików NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Domyślne zachowanie mapowania użytkownika/grupy

Domyślne mapowanie elementu głównego do `nobody` użytkownika, ponieważ domena NFSv4 jest ustawiona na `localdomain`. Po zainstalowaniu woluminu Azure NetApp Files NFSv 4.1 jako katalogu głównego zostaną wyświetlone następujące uprawnienia:  

![Domyślne zachowanie mapowania użytkownika/grupy dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak pokazano na powyższym przykładzie, użytkownik `file1` powinien być `root`, ale domyślnie mapuje do `nobody`.  W tym artykule opisano sposób ustawiania `root`użytkownika `file1`.  

## <a name="steps"></a>Kroki 

1. Edytuj plik `/etc/idmapd.conf` na kliencie NFS.   
    Usuń znaczniki komentarza z wiersza `#Domain` (to oznacza usunięcie `#` z wiersza) i zmień wartość `localdomain` na `defaultv4iddomain.com`. 

    Początkowa konfiguracja: 
    
    ![Początkowa konfiguracja dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Zaktualizowana konfiguracja:
    
    ![Zaktualizowana konfiguracja dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odinstaluj wszystkie aktualnie zainstalowane woluminy systemu plików NFS.
3. Zaktualizuj plik `/etc/idmapd.conf`.
4. Uruchom ponownie usługę `rpcbind` na hoście (`service rpcbind restart`) lub po prostu uruchom ponownie hosta.
5. Zainstaluj woluminy NFS zgodnie z potrzebami.   

    Zobacz [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Poniższy przykład pokazuje zmianę wyniku zmiany użytkownika/grupy: 

![Konfiguracja powstająca dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak pokazano w przykładzie, zmieniono użytkownika/grupę z `nobody` na `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Zachowanie innych użytkowników (innych niż główne) i grup

Azure NetApp Files obsługuje lokalnych użytkowników (użytkowników utworzonych lokalnie na hoście), którzy mają uprawnienia skojarzone z plikami lub folderami na woluminach NFSv 4.1. Jednak usługa obecnie nie obsługuje mapowania użytkowników/grup w wielu węzłach. W związku z tym użytkownicy utworzeni na jednym hoście nie są domyślnie zamapowane dla użytkowników utworzonych na innym hoście. 

W poniższym przykładzie `Host1` ma trzy istniejące konta użytkowników testowych (`testuser01`, `testuser02`, `testuser03`): 

![Konfiguracja powstająca dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Na `Host2`Zwróć uwagę, że konta użytkowników testowych nie zostały utworzone, ale ten sam wolumin jest instalowany na obu hostach:

![Konfiguracja powstająca dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Następny krok 

[Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

