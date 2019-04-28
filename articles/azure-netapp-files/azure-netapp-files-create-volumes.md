---
title: Tworzenie woluminu dla usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób tworzenia woluminu dla usługi Azure NetApp Files.
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
ms.date: 4/12/2019
ms.author: b-juche
ms.openlocfilehash: fc748ee993855c77f25f9b115ea472df4281acec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764362"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Tworzenie woluminu dla usługi Azure NetApp Files

Każda pojemność puli nie może przekraczać 500 woluminów. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. Usługa Azure Files NetApp obsługuje woluminy systemu plików NFS i SMBv3. 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Utwórz wolumin systemu plików NFS

1.  Kliknij przycisk **woluminów** bloku z poziomu bloku wydajności pul. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Tworzenie, które zostanie wyświetlone okno woluminu.

3.  Tworzenie okna wolumin, kliknij **Utwórz** i podaj informacje dotyczące następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Można użyć znaków alfanumerycznych.

    * **Pojemności puli**  
        Określ pulę pojemności, którego woluminu, który ma zostać utworzony.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Wolumin można również uzyskać dostęp z sieci lokalnej za pośrednictwem Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można przekazać tylko jednej podsieci do usługi Azure Files NetApp.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij przycisk **protokołu**, a następnie wybierz **systemu plików NFS** jako typ protokołu dla woluminu.   
    * Określ **ścieżka pliku** który będzie używany do tworzenia ścieżki eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.

        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków.  

    * Opcjonalnie [skonfigurować eksportowanie zasad dla woluminu systemu plików NFS](azure-netapp-files-configure-export-policy.md)

    ![Określenie protokołu systemu plików NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kliknij przycisk **przeglądanie + tworzenie** można zapoznać się z informacjami o woluminie.  Następnie kliknij przycisk **Utwórz** do utworzenia woluminu systemu plików NFS.

    Wolumin, który został utworzony, zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="create-an-smb-volume"></a>Utwórz wolumin SMB

Usługa Azure Files NetApp obsługuje SMBv3 woluminów. Należy utworzyć połączenia usługi Active Directory przed dodaniem woluminie SMB. 

### <a name="create-an-active-directory-connection"></a>Utwórz połączenie usługi Active Directory

1. Z Twojego konta rozwiązania NetApp kliknij **połączenia usługi Active Directory**, następnie kliknij przycisk **Dołącz**.  

    ![Połączenia usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. W oknie Dołącz do usługi Active Directory Podaj następujące informacje:

    * **Podstawowy serwer DNS**   
        Jest to adres IP kontrolera domeny preferowanych Active Directory Domain Services do użycia z usługą Azure Files NetApp. 
    * **Pomocniczy serwer DNS**  
        Jest to adres IP kontrolera domeny dla dodatkowej Active Directory Domain Services do użycia z usługą Azure Files NetApp. 
    * **Domeny**  
        Jest to nazwa domeny usługi Active Directory Domain Services, którą chcesz przyłączyć.
    * **Prefiks serwera (konto komputera) protokołu SMB**  
        Jest to prefiks nazewnictwa dla konta komputera w usłudze Active Directory, które usługi Azure Files NetApp zostanie użyte do tworzenia nowych kont.

        Na przykład jeśli zasady nazewnictwa używanymi przez organizację na serwerach plików jest NAS-01, NAS-02..., NAS 045, a następnie możesz wprowadzić "NAS" prefiks. 

        Usługa utworzy konta dodatkowe komputera w usłudze Active Directory zgodnie z potrzebami.

    * **Ścieżka jednostki organizacyjnej**  
        Jest to ścieżka LDAP dla jednostki organizacyjnej (OU), której zostanie utworzona kont komputerów serwera SMB. Oznacza to, OU = drugiego poziomu, OU = pierwszy poziom. 
    * Poświadczenia, w tym usługi **username** i **hasła**

    ![Połącz usługę Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kliknij pozycję **Dołącz**.  

    Pojawi się połączenie usługi Active Directory, który został utworzony.

    ![Połączenia usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij przycisk **woluminów** bloku z poziomu bloku wydajności pul. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Tworzenie, które zostanie wyświetlone okno woluminu.

3. Tworzenie okna wolumin, kliknij **Utwórz** i podaj informacje dotyczące następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Można użyć znaków alfanumerycznych.

    * **Ścieżka pliku**  
        Określ ścieżkę pliku, na podstawie której zostanie utworzona ścieżka eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.   
     
        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków.  

    * **Pojemności puli**  
        Określ pulę pojemności, którego woluminu, który ma zostać utworzony.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Wolumin można również uzyskać dostęp z sieci lokalnej za pośrednictwem Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można przekazać tylko jednej podsieci do usługi Azure Files NetApp.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij przycisk **protokołu** i wprowadzić następujące informacje:  
    * Wybierz **SMB** jako typ protokołu dla woluminu. 
    * Wybierz swoje **usługi Active Directory** połączenie z listy rozwijanej.
    * Określ nazwę udostępnionego woluminu w **nazwa udziału**.

    ![Określ protokół SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij przycisk **przeglądanie + tworzenie** można zapoznać się z informacjami o woluminie.  Następnie kliknij przycisk **Utwórz** do utworzenia woluminu SMB.

    Wolumin, który został utworzony, zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="next-steps"></a>Kolejne kroki  

* [Zainstalowanie lub odinstalowanie woluminu dla maszyn wirtualnych Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurowanie zasad eksportu dla woluminu systemu plików NFS](azure-netapp-files-configure-export-policy.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
