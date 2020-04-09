---
title: 'Usługa Azure AD Connect: wymagania wstępne i sprzęt | Dokumenty firmy Microsoft'
description: W tym temacie opisano wymagania wstępne i wymagania sprzętowe dotyczące usługi Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6446b039d90e04c9fe7fca28b361f620183a0292
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875745"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące programu Azure AD Connect
W tym temacie opisano wymagania wstępne i wymagania sprzętowe dla usługi Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem usługi Azure AD Connect
Przed zainstalowaniem usługi Azure AD Connect, istnieje kilka rzeczy, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Dzierżawa usługi Azure AD. Otrzymujesz bezpłatną [wersję próbną platformy Azure.](https://azure.microsoft.com/pricing/free-trial/) Do zarządzania usługą Azure AD Connect można użyć jednego z następujących portali:
  * [Portal Azure](https://portal.azure.com).
  * [Portal pakietu Office](https://portal.office.com).  
* [Dodaj i zweryfikuj domenę,](../active-directory-domains-add-azure-portal.md) której zamierzasz używać w usłudze Azure AD. Jeśli na przykład zamierzasz używać contoso.com dla użytkowników, upewnij się, że ta domena została zweryfikowana i nie używasz tylko domeny domyślnej contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD zezwala domyślnie na obiekty 50k. Po zweryfikowaniu domeny limit zostanie zwiększony do 300 tys. Jeśli potrzebujesz jeszcze więcej obiektów w usłudze Azure AD, musisz otworzyć przypadek pomocy technicznej, aby jeszcze bardziej zwiększyć limit. Jeśli potrzebujesz więcej niż 500k obiektów, potrzebujesz licencji, takiej jak Office 365, Azure AD Basic, Azure AD Premium lub Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych lokalnych
* Użyj [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) do identyfikowania błędów, takich jak duplikaty i problemy z formatowaniem w katalogu przed synchronizacją z usługą Azure AD i office 365.
* Przejrzyj [opcjonalne funkcje synchronizacji, które można włączyć w usłudze Azure AD,](how-to-connect-syncservice-features.md) i oceń, które funkcje należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja schematu usługi AD i poziom funkcjonalności lasu muszą być systemami Windows Server 2003 lub nowszymi. Kontrolery domeny można uruchomić dowolną wersję, tak długo, jak wymagania dotyczące schematu i poziomu lasu są spełnione.
* Jeśli zamierzasz użyć **funkcji zapisywania hasła,** kontrolery domeny muszą znajdować się w systemie Windows Server 2008 R2 lub nowszym.
* Kontroler domeny używany przez usługę Azure AD musi być zapisywalny. Nie jest **obsługiwany** do korzystania z kontrolera RODC (kontroler domeny tylko do odczytu) i usługi Azure AD Connect nie następuje żadnych przekierowań zapisu.
* Nie jest **obsługiwane** używanie lokalnych lasów/domen przy użyciu "kropkowanego" (nazwa zawiera kropkowany okres ".") Nazwy NetBios.
* Zaleca się [włączenie kosza usługi Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serwer usługi Azure AD Connect
>[!IMPORTANT]
>Serwer usługi Azure AD Connect zawiera krytyczne dane tożsamości i powinien być traktowany jako składnik warstwy 0, zgodnie z dokumentami w [modelu warstwy administracyjnej usługi Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Nie można zainstalować usługi Azure AD Connect w programie Small Business Server lub Windows Server Essentials przed 2019 r. (obsługiwany jest system Windows Server Essentials 2019). Serwer musi używać systemu Windows Server standard lub lepszego.
* Instalowanie usługi Azure AD Connect na kontrolerze domeny nie jest zalecane ze względu na praktyki zabezpieczeń i bardziej restrykcyjne ustawienia, które mogą uniemożliwić poprawne zainstalowanie usługi Azure AD Connect.
* Serwer usługi Azure AD Connect musi mieć zainstalowany pełny identyfikator GUI. Instalacja na serwerze core nie jest **obsługiwana.**
>[!IMPORTANT]
>Instalowanie usługi Azure AD Connect na serwerze, serwerze lub rdzeniu serwera w małych firmach nie jest obsługiwane.

* Usługa Azure AD Connect musi być zainstalowana w systemie Windows Server 2012 lub nowszym. Ten serwer musi być przyłączony do domeny i może być kontrolerem domeny lub serwerem członkowskim.
* Serwer usługi Azure AD Connect nie może mieć włączone zasady grupy transkrypcji programu PowerShell, jeśli do zarządzania konfiguracją usługi ADFS jest używany kreator usługi Azure AD Connect. Transkrypcji programu PowerShell można włączyć, jeśli używasz kreatora usługi Azure AD Connect do zarządzania konfiguracją synchronizacji.
* Jeśli wdrażane są usługi federacyjne Active Directory, serwerami, na których są zainstalowane usługi AD FS lub serwer proxy aplikacji sieci Web, muszą być systemami Windows Server 2012 R2 lub nowszymi. [Zdalne zarządzanie systemem Windows](#windows-remote-management) musi być włączone na tych serwerach w celu instalacji zdalnej.
* Jeśli usługi federacyjne Active Directory są wdrażane, potrzebne są [certyfikaty TLS/SSL](#tlsssl-certificate-requirements).
* Jeśli wdrażane są Usługi federacyjne Active Directory, należy skonfigurować [rozpoznawanie nazw](#name-resolution-for-federation-servers).
* Jeśli administratorzy globalni mają włączoną **https://secure.aadcdn.microsoftonline-p.com** funkcję mfa, adres URL musi znajdować się na liście zaufanych witryn. Zostanie wyświetlony monit o dodanie tej witryny do listy zaufanych witryn, gdy zostanie wyświetlony monit o wyzwanie usługi MFA i nie zostało ono dodane wcześniej. Za pomocą programu Internet Explorer można dodać go do zaufanych witryn.
* Firma Microsoft zaleca wzmocnienie serwera usługi Azure AD Connect w celu zmniejszenia powierzchni ataku zabezpieczeń dla tego krytycznego składnika środowiska IT.  Przestrzeganie poniższych zaleceń zmniejszy zagrożenia bezpieczeństwa dla twojej organizacji.

* Wdrażanie usługi Azure AD Connect na serwerze przyłączanym do domeny i ograniczanie dostępu administracyjnego do administratorów domeny lub innych ściśle kontrolowanych grup zabezpieczeń.

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grup administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie wbudowanych kont administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Poprawa bezpieczeństwa i utrzymanie poprzez zmniejszenie powierzchni ataku](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszanie powierzchni ataku usługi Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Program SQL Server używany przez program Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest zainstalowany program SQL Server 2012 Express LocalDB (lekka wersja programu SQL Server Express). Program SQL Server Express ma limit rozmiaru 10 GB, który umożliwia zarządzanie około 100 000 obiektów. Jeśli chcesz zarządzać większą ilością obiektów katalogu, należy skierować kreatora instalacji na inną instalację programu SQL Server. Typ instalacji programu SQL Server może mieć wpływ na [wydajność usługi Azure AD Connect.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)
* Jeśli używasz innej instalacji programu SQL Server, obowiązują następujące wymagania:
  * Usługa Azure AD Connect obsługuje wszystkie wersje programu Microsoft SQL Server od 2012 r. (z najnowszym dodatkiem Service Pack) do programu SQL Server 2019. Baza danych SQL platformy Microsoft Azure nie jest **obsługiwana** jako baza danych.
  * Należy użyć sortowania SQL bez uwzględniania wielkości liter. Te sortowania są identyfikowane za pomocą \_CI_ w ich nazwie. Nie jest **obsługiwana** do używania sortowania z \_uwzględnieniem wielkości liter, identyfikowane przez CS_ w ich nazwie.
  * Na wystąpienie SQL można mieć tylko jeden aparat synchronizacji. Udostępnianie wystąpienia SQL z synchronizacją FIM/MIM Sync, DirSync lub Azure AD Sync nie jest **obsługiwane.**

### <a name="accounts"></a>Konta
* Konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD, z którą chcesz zintegrować. To konto musi być **kontem szkolnym lub organizacyjnym** i nie może być **kontem Microsoft**.
* Jeśli używasz [ustawień ekspresowych](reference-connect-accounts-permissions.md#express-settings-installation) lub uaktualnienia z programu DirSync, musisz mieć konto administratora przedsiębiorstwa dla lokalnej usługi Active Directory.
* Jeśli używasz ścieżki instalacji ustawień niestandardowych, masz więcej opcji. Aby uzyskać więcej informacji, zobacz [Niestandardowe ustawienia instalacji](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Łączność
* Serwer usługi Azure AD Connect wymaga rozpoznawania nazw DNS zarówno dla intranetu, jak i internetu. Serwer DNS musi mieć możliwość rozpoznawania nazw zarówno lokalnej usługi Active Directory, jak i punktów końcowych usługi Azure AD.
* Jeśli masz zapory w intranecie i musisz otworzyć porty między serwerami usługi Azure AD Connect a kontrolerami domeny, zobacz [Porty usługi Azure AD Connect,](reference-connect-ports.md) aby uzyskać więcej informacji.
* Jeśli serwer proxy lub zapora ograniczają dostęp do adresów URL, należy otworzyć adresy URL udokumentowane w [adresach URL i zakresach adresów IP usługi Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
  * Jeśli używasz chmury Microsoft Cloud w Niemczech lub chmury Microsoft Azure Dla Instytucji Rządowych, zobacz [Usługi synchronizacji usługi Azure AD Connect zagadnienia dotyczące adresów](reference-connect-instances.md) URL.
* Usługa Azure AD Connect (wersja 1.1.614.0 i później) domyślnie używa protokołu TLS 1.2 do szyfrowania komunikacji między aparatem synchronizacji a usługą Azure AD. Jeśli protokół TLS 1.2 nie jest dostępny w podstawowym systemie operacyjnym, usługa Azure AD Connect stopniowo powraca do starszych protokołów (TLS 1.1 i TLS 1.0).
* Przed wersją 1.1.614.0 usługa Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między aparatem synchronizacji a usługą Azure AD. Aby zmienić stan na TLS 1.2, wykonaj czynności opisane w [obszarze Włącz usługę TLS 1.2 dla usługi Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używany jest wychodzący serwer proxy do łączenia się z Internetem, należy dodać następujące ustawienie w pliku **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** dla kreatora instalacji i synchronizacji usługi Azure AD Connect, aby można było połączyć się z Internetem i usługą Azure AD. Ten tekst należy wprowadzić u dołu pliku. W tym &lt;kodzie proxyaddress&gt; reprezentuje rzeczywisty adres IP serwera proxy lub nazwę hosta.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Jeśli serwer proxy wymaga uwierzytelniania, [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) musi znajdować się w domenie i należy użyć ścieżki instalacji ustawień niestandardowych, aby określić [niestandardowe konto usługi](how-to-connect-install-custom.md#install-required-components). Potrzebna jest również inna zmiana na machine.config. Dzięki tej zmianie w pliku machine.config kreator instalacji i aparat synchronizacji odpowiadają na żądania uwierzytelniania z serwera proxy. Na wszystkich stronach kreatora instalacji, z wyjątkiem strony **Konfigurowanie,** używane są poświadczenia podpisane w użytkowniku. Na stronie **Konfigurowanie** na końcu kreatora instalacji kontekst jest przełączany na [konto usługi,](reference-connect-accounts-permissions.md#adsync-service-account) które zostało utworzone przez użytkownika. Sekcja machine.config powinna wyglądać tak.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Gdy usługa Azure AD Connect wysyła żądanie sieci Web do usługi Azure AD w ramach synchronizacji katalogów, usługa Azure AD może zająć do 5 minut, aby odpowiedzieć. Często serwery proxy mają konfigurację limitu czasu bezczynnego połączenia. Upewnij się, że konfiguracja jest ustawiona na co najmniej 6 minut lub więcej.

Aby uzyskać więcej informacji, zobacz MSDN o [domyślnym elemencie serwera proxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Aby uzyskać więcej informacji o problemach z łącznością, zobacz [Rozwiązywanie problemów](tshoot-connect-connectivity.md)z łącznością .

### <a name="other"></a>Inne
* Opcjonalnie: konto użytkownika testowego w celu zweryfikowania synchronizacji.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące komponentu
### <a name="powershell-and-net-framework"></a>Program PowerShell i .NET Framework
Usługa Azure AD Connect zależy od programu Microsoft PowerShell i programu .NET Framework 4.5.1. Potrzebna jest ta wersja lub nowsza wersja zainstalowana na serwerze. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* System Windows Server 2012R2
  * Program Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * Program .NET Framework 4.5.1 i nowsze wersje są oferowane za pośrednictwem witryny Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje systemu Windows Server w Panelu sterowania.
* Windows Server 2012
  * Najnowsza wersja programu Microsoft PowerShell jest dostępna w **programie Windows Management Framework 4.0,** dostępnym w Centrum pobierania [Microsoft.](https://www.microsoft.com/downloads)
  * Wersje .NET Framework 4.5.1 i nowsze są dostępne w [Centrum pobierania Microsoft.](https://www.microsoft.com/downloads)


### <a name="enable-tls-12-for-azure-ad-connect"></a>Włączanie protokołu TLS 1.2 dla usługi Azure AD Connect
Przed wersją 1.1.614.0 usługa Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między serwerem aparatu synchronizacji a usługą Azure AD. Można to zmienić, konfigurując aplikacje platformy .NET do domyślnego używania protokołu TLS 1.2 na serwerze. Więcej informacji na temat protokołu TLS 1.2 można znaleźć w [poradniku zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Upewnij się, że dla systemu operacyjnego jest zainstalowana poprawka .NET 4.5.1, zobacz [Poradnik zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Może być już zainstalowana ta poprawka lub nowsza wersja na serwerze.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
