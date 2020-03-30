---
title: Rozwiązanie Azure VMware by CloudSimple — używanie usługi Azure AD jako źródła tożsamości w chmurze prywatnej
description: W tym artykule opisano sposób dodawania usługi Azure AD jako dostawcy tożsamości w chmurze prywatnej CloudSimple w celu uwierzytelnienia użytkowników uzyskujących dostęp do usługi CloudSimple z platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564588"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Używanie usługi Azure AD jako dostawcy tożsamości dla usługi vCenter w chmurze prywatnej CloudSimple

Możesz skonfigurować cloudSimple Private Cloud vCenter do uwierzytelniania za pomocą usługi Azure Active Directory (Azure AD) dla administratorów VMware, aby uzyskać dostęp do vCenter. Po skonfigurowaniu źródła tożsamości logowania jednokrotnego użytkownik **chmury** może dodawać użytkowników ze źródła tożsamości do vCenter.  

Domenę usługi Active Directory i kontrolery domeny można skonfigurować w dowolny z następujących sposobów:

* Kontrolery domeny i domeny usługi Active Directory z lokalnymi kontrolerami
* Kontrolery domeny i domeny usługi Active Directory uruchomione na platformie Azure jako maszyny wirtualne w ramach subskrypcji platformy Azure
* Nowe kontrolery domeny i domeny usługi Active Directory uruchomione w chmurze cloudsimple private cloud
* Usługa Azure Active Directory

W tym przewodniku opisano zadania wymagane do skonfigurowania usługi Azure AD jako źródła tożsamości.  Aby uzyskać informacje na temat korzystania z lokalnej usługi Active Directory lub usługi Active Directory uruchomionej na platformie Azure, zobacz [Konfigurowanie źródeł tożsamości vCenter w celu użycia usługi Active Directory, aby](set-vcenter-identity.md) uzyskać szczegółowe instrukcje dotyczące konfigurowania źródła tożsamości.

## <a name="about-azure-ad"></a>Informacje o usłudze Azure AD

Usługa Azure AD to usługa zarządzania katalogami i tożsamościami opartymi na wielu dzierżawach firmy Microsoft.  Usługa Azure AD zapewnia skalowalny, spójny i niezawodny mechanizm uwierzytelniania dla użytkowników do uwierzytelniania i uzyskiwania dostępu do różnych usług na platformie Azure.  Zapewnia również bezpieczne usługi LDAP dla wszystkich usług innych firm do używania usługi Azure AD jako źródła uwierzytelniania/tożsamości.  Usługa Azure AD łączy podstawowe usługi katalogowe, zaawansowane zarządzanie tożsamościami i zarządzanie dostępem do aplikacji, które może służyć do udzielania dostępu do chmury prywatnej użytkownikom, którzy administrują chmurą prywatną.

Aby używać usługi Azure AD jako źródła tożsamości z vCenter, należy skonfigurować usługi domenowe usługi Azure AD i azure AD. Postępuj zgodnie z poniższymi instrukcjami:

1. [Jak skonfigurować usługi azure ad i usługi Azure AD domain](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Jak skonfigurować źródło tożsamości w centrum vCenter private cloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Konfigurowanie usług domeny usługi Azure AD i usługi Azure AD

Zanim zaczniesz, będziesz potrzebować dostępu do subskrypcji platformy Azure z uprawnieniami administratora globalnego.  Poniższe kroki zawierają ogólne wytyczne. Szczegóły są zawarte w dokumentacji platformy Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Jeśli masz już usługę Azure AD, możesz pominąć tę sekcję.

1. Skonfiguruj usługę Azure AD w ramach subskrypcji zgodnie z opisem w [dokumentacji usługi Azure AD.](../active-directory/fundamentals/get-started-azure-ad.md)
2. Włącz usługę Azure Active Directory Premium w ramach subskrypcji zgodnie z opisem w sekcji [Zarejestruj się w usłudze Azure Active Directory Premium.](../active-directory/fundamentals/active-directory-get-started-premium.md)
3. Skonfiguruj niestandardową nazwę domeny i zweryfikuj niestandardową nazwę domeny zgodnie z opisem w [obszarze Dodaj niestandardową nazwę domeny do usługi Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Skonfiguruj rekord DNS rejestratora domen z informacjami podanymi na platformie Azure.
    2. Ustaw niestandardową nazwę domeny jako domenę podstawową.

Opcjonalnie można skonfigurować inne funkcje usługi Azure AD.  Nie są one wymagane do włączania uwierzytelniania vCenter za pomocą usługi Azure AD.

### <a name="azure-ad-domain-services"></a>Usługi domenowe usługi Azure AD

> [!NOTE]
> Jest to ważny krok do włączania usługi Azure AD jako źródła tożsamości dla vCenter.  Aby uniknąć problemów, upewnij się, że wszystkie kroki są wykonywane poprawnie.

1. Włącz usługi domeny usługi Azure AD zgodnie z opisem w [obszarze Włącz usługi domenowe usługi azure active directory przy użyciu witryny Azure portal](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Skonfiguruj sieć, która będzie używana przez usługi domenowe usługi Azure AD zgodnie z opisem w [obszarze Włącz usługi domenowe Active Directory azure przy użyciu portalu Azure](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Skonfiguruj grupę administratorów do zarządzania usługami domenowymi usługi azure ad, zgodnie z opisem [w obszarze Włącz usługi domenowe Usługi domenowe Usługi active directory platformy Azure przy użyciu portalu Azure](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Aktualizowanie ustawień DNS usług domenowych usługi azure AD, zgodnie z opisem w [obszarze Włącz usługi domenowe Active Directory platformy Azure](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Jeśli chcesz połączyć się z usługą AD przez Internet, skonfiguruj rekord DNS dla publicznego adresu IP usług domeny usługi usługi Azure AD na nazwę domeny.
5. Włącz synchronizację skrótów haseł dla użytkowników.  Ten krok umożliwia synchronizację skrótów haseł wymaganych dla uwierzytelniania NT LAN Manager (NTLM) i protokołu Kerberos w usługach domenowych usługi Azure AD. Po skonfigurowaniu synchronizacji skrótów haseł użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych. Zobacz [Włączanie synchronizacji skrótów haseł w Usługach domenowych Active Directory platformy Azure](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Jeśli użytkownicy tylko w chmurze są obecni, muszą zmienić swoje hasło za pomocą <a href="http://myapps.microsoft.com/" target="_blank">panelu dostępu usługi Azure AD,</a> aby upewnić się, że skróty haseł są przechowywane w formacie wymaganym przez NTLM lub Kerberos.  Postępuj zgodnie z instrukcjami w [włącz synchronizację skrótów haseł do domeny zarządzanej dla kont użytkowników tylko w chmurze](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Ten krok należy wykonać dla poszczególnych użytkowników i każdego nowego użytkownika, który jest tworzony w katalogu usługi Azure AD przy użyciu portalu Azure lub poleceń cmdlet azure ad powershell. Użytkownicy, którzy wymagają dostępu do usług domeny usługi Azure AD, muszą użyć <a href="http://myapps.microsoft.com/" target="_blank">panelu dostępu usługi Azure AD</a> i uzyskać dostęp do swojego profilu, aby zmienić hasło.

        > [!NOTE]
        > Jeśli organizacja ma konta użytkowników tylko w chmurze, wszyscy użytkownicy, którzy muszą korzystać z usług Azure Active Directory Domain Services, muszą zmienić swoje hasła. Konto użytkownika tylko w chmurze to konto, które zostało utworzone w katalogu usługi Azure AD przy użyciu witryny Azure Portal lub poleceń cmdlet programu Azure AD PowerShell. Takie konta użytkownika nie są synchronizowane z poziomu katalogu lokalnego.

    2. Jeśli synchronizujesz hasła z lokalnej usługi Active Directory, wykonaj czynności opisane w [dokumentacji usługi Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Skonfiguruj bezpieczną usługę LDAP w usługach domenowych Usługi domenowe Active Directory platformy Azure zgodnie z [opisem w temacie Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych usługi azure AD](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Przekaż certyfikat do użycia przez bezpieczną usługę LDAP opisaną w temacie platformy [Azure, uzyskaj certyfikat bezpiecznego protokołu LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple zaleca używanie podpisanego certyfikatu wystawionego przez urząd certyfikacji, aby upewnić się, że vCenter może ufać certyfikatowi.
    2. Włącz bezpieczną usługę LDAP zgodnie z opisem [Włącz bezpieczną usługę LDAP (LDAPS) dla domeny zarządzanej usług domenowych usługi Azure AD](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Zapisz publiczną część certyfikatu (bez klucza prywatnego) w formacie cer do użycia z vCenter podczas konfigurowania źródła tożsamości.
    4. Jeśli wymagany jest dostęp do Internetu do usług domenowych usługi Azure AD, włącz opcję "Zezwalaj na bezpieczny dostęp do protokołu LDAP przez Internet".
    5. Dodaj regułę zabezpieczeń przychodzących dla grupy zabezpieczeń usług domenowych usługi Azure AD dla portu TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Konfigurowanie źródła tożsamości w centrum vCenter private cloud

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) dla usługi Private Cloud vCenter.
2. Zbieranie parametrów konfiguracji wymaganych do konfigurowania źródła tożsamości.

    | **Opcja** | **Opis** |
    |------------|-----------------|
    | **Nazwa** | Nazwa źródła tożsamości. |
    | **Podstawowa liczba DN dla użytkowników** | Podstawowa nazwa wyróżniająca użytkowników.  W przypadku usługi `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Azure `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`AD użyj: Przykład: .|
    | **Nazwa domeny** | FQDN domeny, na przykład, example.com. Nie podaj adresu IP w tym polu tekstowym. |
    | **Alias domeny** | *(opcjonalnie)* Nazwa netbios domeny. Dodaj nazwę NetBIOS domeny usługi Active Directory jako alias źródła tożsamości, jeśli używasz uwierzytelniania SSPI. |
    | **Podstawowy dn dla grup** | Podstawowa nazwa wyróżniająca dla grup. W przypadku usługi `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Azure AD użyj: Przykład:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Adres URL serwera podstawowego** | Serwer LDAP podstawowego kontrolera domeny dla domeny.<br><br>Użyj formatu `ldaps://hostname:port`. Port jest zazwyczaj 636 dla połączeń LDAPS. <br><br>Certyfikat, który ustanawia zaufanie dla punktu końcowego LDAPS serwera usługi Active `ldaps://` Directory jest wymagany podczas korzystania z podstawowego lub pomocniczego adresu URL LDAP. |
    | **Pomocniczy adres URL serwera** | Adres pomocniczego serwera LDAP kontrolera domeny, który jest używany do pracy awaryjnej. |
    | **Wybieranie certyfikatu** | Jeśli chcesz używać protokołu LDAPS z serwerem LDAP serwera active directory lub źródłem `ldaps://` tożsamości serwera OpenLDAP server, po wpisaniu pola tekstowego adresu URL pojawi się przycisk Wybierz certyfikat. Pomocniczy adres URL nie jest wymagany. |
    | **Nazwę użytkownika** | Identyfikator użytkownika w domenie, który ma minimalny dostęp tylko do odczytu do podstawowej nazwy dn dla użytkowników i grup. |
    | **Hasło** | Hasło użytkownika, który jest określony przez nazwę użytkownika. |

3. Zaloguj się do centrum wirtualnego private cloud po eskalacji uprawnień.
4. Postępuj zgodnie z instrukcjami w [Dodaj źródło tożsamości w vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) przy użyciu wartości z poprzedniego kroku, aby skonfigurować usługę Azure Active Directory jako źródło tożsamości.
5. Dodawanie użytkowników/grup z usługi Azure AD do grup vCenter zgodnie z opisem w temacie VMware [Dodawanie członków do grupy rejestracji jednokrotnej vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nowi użytkownicy muszą być dodani tylko do *grupy Cloud-Owner,* *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* lub *Cloud-Global-VM-Admin-Group*.  Użytkownicy dodani do grupy *Administratorzy* zostaną automatycznie usunięci.  Do grupy *Administratorzy* należy dodać tylko konta usług.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o modelu uprawnień usługi Private Cloud](learn-private-cloud-permissions.md)
