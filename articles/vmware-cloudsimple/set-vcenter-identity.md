---
title: Rozwiązanie Azure VMware firmy CloudSimple — konfigurowanie źródeł tożsamości vCenter w chmurze prywatnej
description: W tym artykule opisano sposób konfigurowania centrum wirtualnego private cloud do uwierzytelniania przy użyciu usługi Active Directory dla administratorów VMware w celu uzyskania dostępu do centrum vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564027"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Konfigurowanie źródeł tożsamości vCenter do używania usługi Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Informacje o źródłach tożsamości VMware vCenter

VMware vCenter obsługuje różne źródła tożsamości do uwierzytelniania użytkowników, którzy uzyskują dostęp do vCenter.  Urządzenie CloudSimple Private Cloud vCenter można skonfigurować w celu uwierzytelniania za pomocą usługi Active Directory dla administratorów VMware w celu uzyskania dostępu do centrum vCenter. Po zakończeniu instalacji użytkownik **chmury** może dodawać użytkowników ze źródła tożsamości do vCenter.  

Domenę usługi Active Directory i kontrolery domeny można skonfigurować w dowolny z następujących sposobów:

* Kontrolery domeny i domeny usługi Active Directory z lokalnymi kontrolerami
* Kontrolery domeny i domeny usługi Active Directory uruchomione na platformie Azure jako maszyny wirtualne w ramach subskrypcji platformy Azure
* Nowe kontrolery domeny i domeny usługi Active Directory uruchomione w chmurze prywatnej
* Usługa Azure Active Directory

W tym przewodniku opisano zadania konfigurowania domeny usługi Active Directory i kontrolerów domeny działających lokalnie lub jako maszyny wirtualne w ramach subskrypcji.  Jeśli chcesz używać usługi Azure AD jako źródła tożsamości, zapoznaj się [z użyciem usługi Azure AD jako dostawcy tożsamości dla vCenter w usłudze CloudSimple Private Cloud,](azure-ad.md) aby uzyskać szczegółowe instrukcje dotyczące konfigurowania źródła tożsamości.

Przed [dodaniem źródła tożsamości](#add-an-identity-source-on-vcenter)tymczasowo [eskalować uprawnienia vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nowi użytkownicy muszą być dodani tylko do *grupy Cloud-Owner,* *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* lub *Cloud-Global-VM-Admin-Group*.  Użytkownicy dodani do grupy *Administratorzy* zostaną automatycznie usunięci.  Tylko konta usług muszą być dodawane do grupy *administratorów* i kont usług nie mogą być używane do logowania się do interfejsu użytkownika sieci web vSphere.   


## <a name="identity-source-options"></a>Opcje źródła tożsamości

* [Dodawanie lokalnej usługi Active Directory jako źródła tożsamości logowania jednokrotnego](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Konfigurowanie nowej usługi Active Directory w chmurze prywatnej](#set-up-new-active-directory-on-a-private-cloud)
* [Konfigurowanie usługi Active Directory na platformie Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Dodawanie lokalnej usługi Active Directory jako źródła tożsamości logowania jednokrotnego

Aby skonfigurować lokalną usługę Active Directory jako źródło tożsamości logowania jednokrotnego, należy:

* [Połączenie sieci VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między lokacjami z lokalnego centrum danych do chmury prywatnej.
* Lokalny adres IP serwera DNS dodany do kontrolera vCenter i Platform Services Controller (PSC).

Podczas konfigurowania domeny usługi Active Directory należy użyć informacji zawartych w poniższej tabeli.

| **Opcja** | **Opis** |
|------------|-----------------|
| **Nazwa** | Nazwa źródła tożsamości. |
| **Podstawowa liczba DN dla użytkowników** | Podstawowa nazwa wyróżniająca użytkowników. |
| **Nazwa domeny** | FQDN domeny, na przykład, example.com. Nie podaj adresu IP w tym polu tekstowym. |
| **Alias domeny** | Nazwa netbios domeny. Dodaj nazwę NetBIOS domeny usługi Active Directory jako alias źródła tożsamości, jeśli używasz uwierzytelniania SSPI. |
| **Podstawowy dn dla grup** | Podstawowa nazwa wyróżniająca dla grup. |
| **Adres URL serwera podstawowego** | Serwer LDAP podstawowego kontrolera domeny dla domeny.<br><br>Użyj `ldap://hostname:port` formatu `ldaps://hostname:port`lub . Port jest zazwyczaj 389 dla połączeń LDAP i 636 dla połączeń LDAPS. W przypadku wdrożeń kontrolerów wielu domen usługi Active Directory port jest zazwyczaj 3268 dla LDAP i 3269 dla LDAPS.<br><br>Certyfikat, który ustanawia zaufanie dla punktu końcowego LDAPS serwera usługi Active `ldaps://` Directory jest wymagany podczas korzystania z podstawowego lub pomocniczego adresu URL LDAP. |
| **Pomocniczy adres URL serwera** | Adres pomocniczego serwera LDAP kontrolera domeny, który jest używany do pracy awaryjnej. |
| **Wybieranie certyfikatu** | Jeśli chcesz używać protokołu LDAPS z serwerem LDAP serwera active directory lub źródłem `ldaps://` tożsamości serwera OpenLDAP server, po wpisaniu pola tekstowego adresu URL pojawi się przycisk Wybierz certyfikat. Pomocniczy adres URL nie jest wymagany. |
| **Nazwę użytkownika** | Identyfikator użytkownika w domenie, który ma minimalny dostęp tylko do odczytu do podstawowej nazwy dn dla użytkowników i grup. |
| **Hasło** | Hasło użytkownika, który jest określony przez nazwę użytkownika. |

Jeśli masz informacje w poprzedniej tabeli, można dodać lokalnej usługi Active Directory jako źródło tożsamości logowania jednokrotnego w vCenter.

> [!TIP]
> Więcej informacji na temat źródeł tożsamości logowania jednokrotnego znajdziesz na [stronie dokumentacji VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Konfigurowanie nowej usługi Active Directory w chmurze prywatnej

Możesz skonfigurować nową domenę usługi Active Directory w chmurze prywatnej i używać jej jako źródła tożsamości dla logowania jednokrotnego.  Domena usługi Active Directory może być częścią istniejącego lasu usługi Active Directory lub może być skonfigurowana jako niezależny las.

### <a name="new-active-directory-forest-and-domain"></a>Nowy las i domena usługi Active Directory

Aby skonfigurować nowy las i domenę usługi Active Directory, potrzebujesz:

* Co najmniej jedna maszyna wirtualna z systemem Microsoft Windows Server będzie używana jako kontrolery domeny dla nowego lasu i domeny usługi Active Directory.
* Co najmniej jedna maszyna wirtualna z uruchomieniem usługi DNS w celu rozpoznawania nazw.

Szczegółowe kroki można [znaleźć w cieczy nowej usługi Windows Server 2012 Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) Forest.

> [!TIP]
> Aby uzyskać wysoką dostępność usług, zalecamy skonfigurowanie wielu kontrolerów domeny i serwerów DNS.

Po skonfigurowaniu lasu i domeny usługi Active Directory można [dodać źródło tożsamości w centrum vCenter](#add-an-identity-source-on-vcenter) dla nowej usługi Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nowa domena usługi Active Directory w istniejącym lesie usługi Active Directory

Aby skonfigurować nową domenę usługi Active Directory w istniejącym lesie usługi Active Directory, potrzebujesz:

* Połączenie sieci VPN lokacja z lokalizacją lasu usługi Active Directory.
* serwer DNS, aby rozpoznać nazwę istniejącego lasu usługi Active Directory.

Aby uzyskać szczegółowe kroki, zobacz [Instalowanie nowej domeny podrzędnej lub podrzędnej usługi Active Directory systemu Windows Server 2012.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)

Po skonfigurowaniu domeny usługi Active Directory można [dodać źródło tożsamości w vCenter](#add-an-identity-source-on-vcenter) dla nowej usługi Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Konfigurowanie usługi Active Directory na platformie Azure

Usługa Active Directory działająca na platformie Azure jest podobna do usługi Active Directory działającej lokalnie.  Aby skonfigurować usługę Active Directory działającą na platformie Azure jako źródło tożsamości logowania jednokrotnego w centrum vCenter, serwer vCenter i psc muszą mieć łączność sieciową z siecią wirtualną platformy Azure, w której są uruchomione usługi Active Directory.  Tę łączność można ustanowić przy użyciu [usługi Azure Virtual Network Connection przy użyciu usługi ExpressRoute](azure-expressroute-connection.md) z sieci wirtualnej platformy Azure, w której usługi Active Directory są uruchomione w usłudze CloudSimple Private Cloud.

Po nawiązaniu połączenia sieciowego wykonaj kroki opisane w programie [Dodaj lokalną usługę Active Directory jako źródło tożsamości logowania jednokrotnego,](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) aby dodać je jako źródło tożsamości.  

## <a name="add-an-identity-source-on-vcenter"></a>Dodawanie źródła tożsamości w centrum vCenter

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) w chmurze prywatnej.

2. Zaloguj się do centrum wirtualnego dla chmury prywatnej.

3. Wybierz **pozycję Administracja > do domu**.

    ![Administracja](media/OnPremAD01.png)

4. Wybierz **opcję Logowanie jednokrotne na > konfiguracji**.

    ![Logowanie jednokrotne](media/OnPremAD02.png)

5. Otwórz kartę **Źródła tożsamości** **+** i kliknij, aby dodać nowe źródło tożsamości.

    ![Źródła tożsamości](media/OnPremAD03.png)

6. Wybierz **pozycję Active Directory jako serwer LDAP** i kliknij przycisk **Dalej**.

    ![Usługa Active Directory](media/OnPremAD04.png)

7. Określ parametry źródła tożsamości dla środowiska i kliknij przycisk **Dalej**.

    ![Usługa Active Directory](media/OnPremAD05.png)

8. Przejrzyj ustawienia i kliknij przycisk **Zakończ**.
