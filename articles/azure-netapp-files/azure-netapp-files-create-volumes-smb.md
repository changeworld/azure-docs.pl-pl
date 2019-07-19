---
title: Tworzenie woluminu SMB dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia woluminu SMB dla Azure NetApp Files.
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 6e425eba3159f8840e1a7960f6a6c3171b1ba163
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850420"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla usługi Azure NetApp Files

Azure NetApp Files obsługuje woluminy NFS i SMBv3. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin SMBv3. Jeśli chcesz utworzyć wolumin systemu plików NFS, zobacz [Tworzenie woluminu NFS dla Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Wymagania dotyczące Active Directory połączeń

 Przed utworzeniem woluminu SMB należy utworzyć połączenia Active Directory. Wymagania dotyczące połączeń Active Directory są następujące: 

* Konto administratora, którego używasz, musi mieć możliwość tworzenia kont komputerów w określonej ścieżce jednostki organizacyjnej (OU).  

* Odpowiednie porty muszą być otwarte na odpowiednim serwerze Windows Active Directory (AD).  
    Wymagane porty są następujące: 

    |     Usługa           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    Usługi sieci Web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    ND       |    Odpowiedź echa    |
    |    Udziałem           |    464       |    TCP           |
    |    Udziałem           |    464       |    UDP           |
    |    Udziałem           |    88        |    TCP           |
    |    Udziałem           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nazwa NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Secure LDAP        |    636       |    TCP           |
    |    Secure LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

## <a name="create-an-active-directory-connection"></a>Utwórz połączenie Active Directory

1. Na koncie usługi NetApp kliknij pozycję **połączenia Active Directory**a następnie kliknij pozycję **Dołącz**.  

    ![Połączenia Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. W oknie sprzęganie Active Directory podaj następujące informacje:

    * **Podstawowy serwer DNS**  
        Jest to serwer DNS, który jest wymagany do przyłączania do domeny Active Directory i operacji uwierzytelniania SMB. 
    * **Pomocniczy serwer DNS**   
        Jest to pomocniczy serwer DNS służący do zapewniania nadmiarowych usług nazw. 
    * **Domeny**  
        To jest nazwa domeny Active Directory Domain Services, do której chcesz dołączyć.
    * **Prefiks serwera SMB (konto komputera)**  
        Jest to prefiks nazwy dla konta komputera w Active Directory, które Azure NetApp Files będzie używane do tworzenia nowych kont.

        Na przykład jeśli standard nazewnictwa używany przez organizację dla serwerów plików to NAS-01, NAS-02..., NAS-045, wpisz "NAS" dla prefiksu. 

        W razie konieczności usługa utworzy dodatkowe konta komputera w Active Directory.

    * **Ścieżka jednostki organizacyjnej**  
        Jest to ścieżka LDAP jednostki organizacyjnej (OU), w której zostaną utworzone konta komputerów serwera SMB. Oznacza to, OU = drugi poziom, OU = pierwszy poziom. 
    * Poświadczenia, w tym **Nazwa użytkownika** i **hasło**

    ![Przyłączanie Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kliknij pozycję **Dołącz**.  

    Zostanie wyświetlone utworzone połączenie Active Directory.

    ![Połączenia Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij blok **woluminy** w bloku pule pojemności. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Tworzenie woluminu.

3. W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dla następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij pozycję **Protokół** i wykonaj następujące informacje:  
    * Wybierz opcję **SMB** jako typ protokołu dla woluminu. 
    * Wybierz połączenie **Active Directory** z listy rozwijanej.
    * Określ nazwę udostępnionego woluminu w polu **Nazwa udziału**.

    ![Określ protokół SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin SMB.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="next-steps"></a>Kolejne kroki  

* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
