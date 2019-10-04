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
ms.date: 10/02/2019
ms.author: b-juche
ms.openlocfilehash: bd00c04ecfc211ae4ed410e886c0fe6553bea241
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827507"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla Azure NetApp Files

Azure NetApp Files obsługuje woluminy NFS i SMBv3. Zużycie pojemności woluminu odniesie się do pojemności zainicjowanej przez pulę. W tym artykule pokazano, jak utworzyć wolumin SMBv3. Jeśli chcesz utworzyć wolumin systemu plików NFS, zobacz [Tworzenie woluminu NFS dla Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Należy już skonfigurować pulę pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do Azure NetApp Files.  
[Delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Wymagania dotyczące Active Directory połączeń

 Przed utworzeniem woluminu SMB należy utworzyć połączenia Active Directory. Wymagania dotyczące połączeń Active Directory są następujące: 

* Konto administratora, którego używasz, musi mieć możliwość tworzenia kont komputerów w określonej ścieżce jednostki organizacyjnej (OU).  

* Odpowiednie porty muszą być otwarte na odpowiednim serwerze Windows Active Directory (AD).  
    Wymagane porty są następujące: 

    |     Usługa           |     Port     |     Protokół     |
    |-----------------------|--------------|------------------|
    |    Usługi sieci Web AD    |    9389      |    TCP           |
    |    systemem DNS,                |    53        |    TCP           |
    |    systemem DNS,                |    53        |    UDP           |
    |    Ruch             |    Brak       |    Odpowiedź echa    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nazwa NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Secure LDAP        |    636       |    TCP           |
    |    Secure LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

* Topologia lokacji dla konkretnych Active Directory Domain Services musi być zgodna z najlepszymi rozwiązaniami, w szczególności z siecią wirtualną platformy Azure, w której wdrożono Azure NetApp Files.  

    Przestrzeń adresowa dla sieci wirtualnej, w której wdrożono Azure NetApp Files, musi zostać dodana do nowej lub istniejącej lokacji Active Directory (w której znajduje się kontroler domeny osiągalny przez Azure NetApp Files). 

* Określone serwery DNS muszą być dostępne z [delegowanej podsieci](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    Zapoznaj się z tematem [wskazówki dotyczące planowania sieci Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) obsługiwanych topologii sieci.

    Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i zapory muszą mieć odpowiednio skonfigurowane reguły zezwalające na żądania ruchu Active Directory i DNS. 

* Delegowana podsieć Azure NetApp Files musi mieć dostęp do wszystkich Active Directory Domain Services (dodaje) kontrolerów domeny w domenie, łącznie z lokalnymi i zdalnymi kontrolerami domeny. W przeciwnym razie może wystąpić przerwa w działaniu usługi.  

    Jeśli masz kontrolery domeny, które są nieosiągalne za pośrednictwem delegowanej podsieci Azure NetApp Files, możesz przesłać żądanie pomocy technicznej platformy Azure w celu zmiany zakresu z **globalnego** (domyślnego) na **lokację**.  Azure NetApp Files musi komunikować się tylko z kontrolerami domeny w lokacji, w której znajduje się Azure NetApp Files przestrzeń adresowa delegowanej podsieci.

    Zobacz [projektowanie topologii lokacji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) dotyczącej witryn i usług AD. 

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

        Jeśli używasz Azure NetApp Files z Azure Active Directory Domain Services, ścieżka jednostki organizacyjnej jest `OU=AADDC Computers` podczas konfigurowania Active Directory dla konta NetApp.
        
    * Poświadczenia, w tym **Nazwa użytkownika** i **hasło**

    ![Przyłączanie Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kliknij pozycję **Dołącz**.  

    Zostanie wyświetlone utworzone połączenie Active Directory.

    ![Połączenia Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij blok **woluminy** w bloku pule pojemności. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin** , aby utworzyć wolumin.  
    Zostanie wyświetlone okno Tworzenie woluminu.

3. W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dla następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi składać się z co najmniej trzech znaków. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć `default` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Działa**  
        Określ ilość pamięci logicznej, która jest przypisana do woluminu.  

        Pole **dostępne przydziału** pokazuje ilość nieużywanych miejsc w wybranej puli pojemności, która może być używana do tworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskać dostęp do woluminu.  

        Określona Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej, która znajduje się w tym samym regionie co wolumin za pośrednictwem sieci równorzędnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieci**  
        Określ podsieć, która ma być używana dla woluminu.  
        Określona podsieć musi być delegowana do Azure NetApp Files. 
        
        Jeśli podsieć nie została oddelegowana, można kliknąć pozycję **Utwórz nową** na stronie Tworzenie woluminu. Następnie na stronie Tworzenie podsieci Określ informacje o podsieci, a następnie wybierz pozycję **Microsoft. NetApp/Volumes** , aby delegować podsieć do Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Utwórz podsieć](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij pozycję **Protokół** i wykonaj następujące informacje:  
    * Wybierz opcję **SMB** jako typ protokołu dla woluminu. 
    * Wybierz połączenie **Active Directory** z listy rozwijanej.
    * Określ nazwę udostępnionego woluminu w polu **Nazwa udziału**.

    ![Określ protokół SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin SMB.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy subskrypcję, grupę zasobów, atrybuty lokalizacji z puli pojemności. Aby monitorować stan wdrożenia woluminu, można użyć karty powiadomienia.

## <a name="next-steps"></a>Następne kroki  

* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limity zasobów dla Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Funkcja SMB — często zadawane pytania](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informacje o integracji sieci wirtualnej dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalowanie nowego lasu Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
