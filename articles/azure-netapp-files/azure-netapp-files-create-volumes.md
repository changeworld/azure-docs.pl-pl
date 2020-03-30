---
title: Tworzenie woluminu NFS dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia woluminu NFS dla plików NetApp platformy Azure.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274088"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Tworzenie woluminu NFS dla usługi Azure NetApp Files

Usługa Azure NetApp Files obsługuje woluminy NFSv3 i NFSv4.1) i SMBv3. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin NFS. Jeśli chcesz utworzyć wolumin SMB, zobacz [Tworzenie woluminu SMB dla plików NetApp Usługi Azure](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Zagadnienia do rozważenia 

* Decydowanie o wersji systemu plików NFS do użycia  
  NFSv3 może obsługiwać wiele różnych przypadków użycia i jest powszechnie wdrażany w większości aplikacji korporacyjnych. Należy sprawdzić, jakiej wersji (NFSv3 lub NFSv4.1) aplikacja wymaga i utworzyć wolumin przy użyciu odpowiedniej wersji. Na przykład, jeśli używasz [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), blokowanie plików z NFSv4.1 jest zalecane przez NFSv3. 

* Zabezpieczenia  
  Obsługa bitów trybu UNIX (odczytu, zapisu i wykonania) jest dostępna dla plików NFSv3 i NFSv4.1. Dostęp na poziomie głównym jest wymagany na kliencie systemu plików NFS do instalowania woluminów NFS.

* Lokalna obsługa użytkownika/grupy i protokołu LDAP dla nfsv4.1  
  Obecnie system NFSv4.1 obsługuje tylko dostęp do woluminów głównych. Zobacz [Konfigurowanie domyślnej domeny NFSv4.1 dla plików NetApp platformy Azure](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Najlepsze rozwiązania

* Należy upewnić się, że używasz odpowiednich instrukcji montażu woluminu.  Zobacz [Instalowanie lub odinstalowywszycie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Klient usługi NFS powinien znajdować się w tej samej sieci wirtualnej lub w sieci wirtualnej równorzędnej co wolumin plików NetApp platformy Azure. Połączenie spoza sieci wirtualnej jest obsługiwane; jednak wprowadzi dodatkowe opóźnienie i zmniejszyć ogólną wydajność.

* Należy upewnić się, że klient systemu plików NFS jest aktualny i uruchamia najnowsze aktualizacje dla systemu operacyjnego.

## <a name="create-an-nfs-volume"></a>Tworzenie woluminu NFS

1.  Kliknij **blok Woluminy** z bloku Pule pojemności. 

    ![Przechodzenie do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetle okno Utwórz wolumin.

3.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dotyczące następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumeryczne.   

        Nie można `default` używać jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Można również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem trasy ekspresowej.   

    * **Podsieci**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej tylko jedna podsieć może być delegowana do plików NetApp platformy Azure.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij **pozycję Protokół**, a następnie wykonaj następujące czynności:  
    * Wybierz **nfs** jako typ protokołu dla woluminu.   
    * Określ **ścieżkę pliku,** która będzie używana do tworzenia ścieżki eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.

        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków. 

        Ścieżka pliku musi być unikatowa w ramach każdej subskrypcji i każdego regionu. 

    * Wybierz wersję NFS **(NFSv3** lub **NFSv4.1**) dla woluminu.  
    * Opcjonalnie [skonfiguruj zasady eksportu dla woluminu NFS](azure-netapp-files-configure-export-policy.md).

    ![Określanie protokołu NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kliknij **przycisk Recenzja + Utwórz,** aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz,** aby utworzyć wolumin NFS.

    Utworzony wolumin pojawi się na stronie Woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.


## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie domyślnej domeny NFSv4.1 dla usługi Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurowanie zasad eksportu dla woluminu NFS](azure-netapp-files-configure-export-policy.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
