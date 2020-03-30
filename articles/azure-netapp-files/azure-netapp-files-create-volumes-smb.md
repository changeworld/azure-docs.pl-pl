---
title: Tworzenie woluminu SMB dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia woluminu SMB dla plików NetApp platformy Azure.
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
ms.date: 03/13/2020
ms.author: b-juche
ms.openlocfilehash: b2000c3fd3d64793f797e997d8f3c10eaed5d7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409601"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla usługi Azure NetApp Files

Usługa Azure NetApp Files obsługuje woluminy NFS i SMBv3. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin SMBv3. Jeśli chcesz utworzyć wolumin NFS, zobacz [Tworzenie woluminu NFS dla plików NetApp platformy Azure](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Wymagania dotyczące połączeń usługi Active Directory

 Przed utworzeniem woluminu SMB należy utworzyć połączenia usługi Active Directory. Wymagania dotyczące połączeń usługi Active Directory są następujące: 

* Konto administratora, którego używasz, musi mieć możliwość tworzenia kont maszyn w określonej ścieżce jednostki organizacyjnej.  

* Odpowiednie porty muszą być otwarte na odpowiednim serwerze usługi Windows Active Directory (AD).  
    Wymagane porty są następujące: 

    |     Usługa           |     Port     |     Protocol (Protokół)     |
    |-----------------------|--------------|------------------|
    |    Usługi sieci Web usługi AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Nie dotyczy       |    Echo Odpowiedź    |
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
    |    W32time            |    123       |    UDP           |

* Topologia lokacji dla ukierunkowanych Usług domenowych Active Directory musi być zgodna z najlepszymi rozwiązaniami, w szczególności z siecią wirtualną platformy Azure, w której wdrożono usługi Azure NetApp Files.  

    Przestrzeń adresowa sieci wirtualnej, w której wdrożono pliki NetApp platformy Azure, musi zostać dodana do nowej lub istniejącej lokacji usługi Active Directory (w której znajduje się kontroler domeny dostępny przez usługi Azure NetApp Files). 

* Określone serwery DNS muszą być dostępne z [delegowanej podsieci](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) plików NetApp usługi Azure.  

    Zobacz [wskazówki dotyczące planowania sieci usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) dla obsługiwanych topologii sieci.

    Sieciowe grupy zabezpieczeń (NSG) i zapory muszą mieć odpowiednio skonfigurowane reguły umożliwiające żądania ruchu usługi Active Directory i DNS. 

* Podsieć delegowana usługi Azure NetApp Files musi mieć możliwość dotarcia do wszystkich kontrolerów domeny Usług domenowych Active Directory (ADDS) w domenie, w tym do wszystkich kontrolerów domeny lokalnej i zdalnej. W przeciwnym razie może wystąpić przerwa w działaniu usługi.  

    Jeśli masz kontrolery domeny, które są nieosiągalne przez podsieć delegowane pliki usługi Azure NetApp, możesz określić lokację usługi Active Directory podczas tworzenia połączenia usługi Active Directory.  Usługa Azure NetApp Files musi komunikować się tylko z kontrolerami domeny w lokacji, w której znajduje się przestrzeń adresowa podsieci delegowanej plików Usługi Azure NetApp.

    Zobacz [Projektowanie topologii witryn dotyczących](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) lokacji i usług AD. 
    
Zobacz Często zadawane pytania dotyczące [smb](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) plików netapp platformy Azure o dodatkowych informacjach o usłudze AD. 

## <a name="decide-which-domain-services-to-use"></a>Określanie usług domenowych do użycia 

Usługa Azure NetApp Files obsługuje zarówno [usługi domenowe Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS), jak i usługi domenowe Usługi domenowe Active Directory (Azure Active Directory Domain Services) dla połączeń AD.  Przed utworzeniem połączenia usługi AD należy zdecydować, czy ma być używany dodatek ADDS, czy usługi AADDS.  

Aby uzyskać więcej informacji, zobacz [Porównanie samodzielnie zarządzanych Usług domenowych Active Directory, usługi Azure Active Directory i zarządzanych Usług domenowych Active Directory platformy Azure](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Można użyć preferowanego zakresu [witryn i usług Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) dla plików NetApp platformy Azure. Ta opcja umożliwia odczyty i zapisy na kontrolerach domeny Usług domenowych Active Directory (ADDS), które są [dostępne dla plików NetApp platformy Azure](azure-netapp-files-network-topologies.md). Uniemożliwia to również usłudze komunikowanie się z kontrolerami domeny, które nie znajdują się w określonej lokacji Lokacji lokacji lokacji lokacji usługi Active Directory. 

Aby znaleźć nazwę witryny podczas korzystania z programu ADDS, można skontaktować się z grupą administracyjną w organizacji odpowiedzialnej za Usługi domenowe Active Directory. W poniższym przykładzie pokazano wtyczkę Lokacje i usługi Active Directory, w której wyświetlana jest nazwa witryny: 

![Lokacje i usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Podczas konfigurowania połączenia usługi AD dla plików NetApp usługi Azure należy określić nazwę witryny w zakresie pola **Nazwa witryny usługi AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Aby zapoznać się z konfiguracją i wskazówkami dotyczącymi usług domenowych Active Directory (Azure Active Directory Domain Services), zobacz [dokumentację usług domenowych usługi azure ad](https://docs.microsoft.com/azure/active-directory-domain-services/).

Dodatkowe zagadnienia AADDS dotyczą plików NetApp platformy Azure: 

* Upewnij się, że sieć wirtualna lub podsieć, w której wdrożono usługę AADDS, znajduje się w tym samym regionie platformy Azure, co wdrożenie plików NetApp platformy Azure.
* Jeśli używasz innej sieci wirtualnej w regionie, w którym jest wdrażany usługi Azure NetApp Files, należy utworzyć komunikację równorzędną między dwiema sieciami wirtualnymi.
* Usługa Azure NetApp Files obsługuje `user` i `resource forest` typów.
* W przypadku typu synchronizacji `All` `Scoped`można wybrać lub .   
    Jeśli wybierzesz `Scoped`opcję , upewnij się, że do uzyskiwania dostępu do udziałów SMB wybrano właściwą grupę usługi Azure AD.  Jeśli nie masz pewności, `All` można użyć typu synchronizacji.
* Wymagane jest korzystanie z jednostki SKU Enterprise lub Premium. Standardowa jednostka SKU nie jest obsługiwana.

Podczas tworzenia połączenia usługi Active Directory należy zwrócić uwagę na następujące szczegóły dotyczące usługi AADDS:

* Informacje dotyczące **podstawowego systemu DNS,** **pomocniczego DNS**i **nazwy domeny DNS usługi AD** można znaleźć w menu AADDS.  
W przypadku serwerów DNS do konfigurowania połączenia usługi Active Directory używane będą dwa adresy IP. 
* **Ścieżka jednostki organizacyjnej** jest `OU=AADDC Computers`.  
To ustawienie jest skonfigurowane w **usłudze Połączenia usługi Active Directory** w obszarze **Konto NetApp:**

  ![Ścieżka jednostki organizacyjnej](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Poświadczenia nazwy użytkownika** mogą być dowolnym użytkownikiem, który jest członkiem grupy Azure AD **Administratorzy kontrolera domeny usługi Azure AD.**


## <a name="create-an-active-directory-connection"></a>Tworzenie połączenia usługi Active Directory

1. Na koncie NetApp kliknij pozycję **Połączenia usługi Active Directory**, a następnie kliknij pozycję **Dołącz**.  

    ![Połączenia usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. W oknie Dołącz do usługi Active Directory podaj następujące informacje na podstawie usług domenowych, których chcesz użyć:  

    Aby uzyskać informacje dotyczące używanych Usług domenowych, zobacz [Określanie usług domenowych, których należy używać.](#decide-which-domain-services-to-use) 

    * **Podstawowy dns**  
        Jest to system DNS wymagany dla operacji dołączania do domeny usługi Active Directory i uwierzytelniania SMB. 
    * **Dodatkowy dns**   
        Jest to pomocniczy serwer DNS zapewniający nadmiarowe usługi nazw. 
    * **Nazwa domeny DNS usługi AD**  
        Jest to nazwa domeny Usług domenowych Active Directory, do której chcesz dołączyć.
    * **Nazwa witryny usługi AD**  
        Jest to nazwa lokacji, do których odnajduje się kontroler domeny.
    * **Prefiks serwera (konta komputera) SMB**  
        Jest to prefiks nazewnictwa konta komputera w usłudze Active Directory, który będzie używany przez usługi Azure NetApp Files do tworzenia nowych kont.

        Jeśli na przykład standardem nazewnictwa używanym przez organizację dla serwerów plików są SERWERY NAS-01, NAS-02..., NAS-045, należy wprowadzić "NAS" dla prefiksu. 

        W razie potrzeby usługa utworzy dodatkowe konta komputerów w usłudze Active Directory.

    * **Ścieżka jednostki organizacyjnej**  
        Jest to ścieżka LDAP dla jednostki organizacyjnej (OU), w której będą tworzone konta komputerów serwerowych SMB. Oznacza to, że OU = drugi poziom, OU = pierwszy poziom. 

        Jeśli używasz usługi Azure NetApp Files z usługami domenowymi `OU=AADDC Computers` Usługi domenowe Active Directory platformy Azure, ścieżka jednostki organizacyjnej jest podczas konfigurowania usługi Active Directory dla konta NetApp.
        
    * Poświadczenia, w tym **nazwa użytkownika** i **hasło**

    ![Dołączanie do usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kliknij pozycję **Dołącz**.  

    Zostanie wyświetlenie utworzonego połączenia usługi Active Directory.

    ![Połączenia usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Po zapisaniu połączenia usługi Active Directory można edytować pola nazwy użytkownika i hasła. Po zapisaniu połączenia nie można edytować żadnych innych wartości. Jeśli trzeba zmienić inne wartości, należy najpierw usunąć wszystkie wdrożone woluminy SMB, a następnie usunąć i ponownie utworzyć połączenie usługi Active Directory.

## <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij **blok Woluminy** z bloku Pule pojemności. 

    ![Przechodzenie do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetle okno Utwórz wolumin.

3. W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dotyczące następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumeryczne.   

        Nie można używać `default` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskać dostęp do woluminu.  

        Określona sieć wirtualna musi mieć podsieć delegowaną do plików NetApp platformy Azure. Usługa Azure NetApp Files jest dostępna tylko z tej samej sieci wirtualnej lub sieci wirtualnej, która znajduje się w tym samym regionie co wolumin za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Można również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem trasy ekspresowej.   

    * **Podsieci**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, na stronie Tworzenie woluminu kliknij pozycję **Utwórz nowy.** Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej tylko jedna podsieć może być delegowana do plików NetApp platformy Azure.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij **przycisk Protokół** i uzupełnij następujące informacje:  
    * Wybierz **SMB** jako typ protokołu dla woluminu. 
    * Wybierz połączenie **usługi Active Directory** z listy rozwijanej.
    * Określ nazwę udostępnionego woluminu w **obszarze Nazwa udziału**.

    ![Określanie protokołu SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij **przycisk Recenzja + Utwórz,** aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz,** aby utworzyć wolumin SMB.

    Utworzony wolumin pojawi się na stronie Woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="next-steps"></a>Następne kroki  

* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Najczęściej zadawane pytania dotyczące SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalowanie nowego lasu usługi Active Directory przy użyciu interfejsu wiersza polecenia usługi Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
