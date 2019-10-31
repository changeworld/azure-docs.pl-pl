---
title: 'Azure AD Connect: wymagania wstępne i sprzęt | Microsoft Docs'
description: Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe Azure AD Connect
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
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a1906b8ac281620381176a2a11dff3841069f69
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062669"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące Azure AD Connect
Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem Azure AD Connect
Przed zainstalowaniem Azure AD Connect istnieje kilka rzeczy, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Dzierżawa usługi Azure AD. Otrzymujesz jedną z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Aby zarządzać Azure AD Connect, można użyć jednego z następujących portalów:
  * [Azure Portal](https://portal.azure.com).
  * [Portal Office](https://portal.office.com).  
* [Dodaj i sprawdź domenę](../active-directory-domains-add-azure-portal.md) , która ma być używana w usłudze Azure AD. Jeśli na przykład zamierzasz używać contoso.com dla użytkowników, upewnij się, że ta domena została zweryfikowana i nie tylko używasz domyślnej domeny contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD zezwala na używanie domyślnych obiektów 50 000. Po zweryfikowaniu domeny limit zostaje zwiększony do obiektów 300 tysięcy pozycji. Jeśli potrzebujesz jeszcze więcej obiektów w usłudze Azure AD, musisz otworzyć zgłoszenie do pomocy technicznej, aby limit został jeszcze bardziej zwiększony. Jeśli potrzebujesz więcej niż 500 000 obiektów, potrzebna jest licencja, taka jak Office 365, Azure AD — wersja Podstawowa, Azure AD — wersja Premium lub Enterprise Mobility i Security.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych lokalnych
* Użyj [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) , aby zidentyfikować błędy, takie jak duplikaty i problemy z formatowaniem w katalogu przed synchronizacją do usługi Azure AD i pakietu Office 365.
* Zapoznaj [się z opcjonalnymi funkcjami synchronizacji, które można włączyć w usłudze Azure AD](how-to-connect-syncservice-features.md) , i Oceń, które funkcje należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja schematu usługi AD i poziom funkcjonalności lasu muszą być systemu Windows Server 2003 lub nowszego. Kontrolery domeny mogą uruchamiać dowolną wersję, o ile są spełnione wymagania dotyczące poziomu schematu i lasu.
* Jeśli planujesz używać funkcji **zapisywania zwrotnego haseł**, kontrolery domeny muszą być w systemie Windows Server 2008 R2 lub nowszym.
* Kontroler domeny używany przez usługę Azure AD musi być zapisywalny. Korzystanie z kontrolera RODC (tylko do odczytu) nie jest **obsługiwane** i Azure AD Connect nie są zgodne z żadnym przekierowaniami zapisu.
* Nie jest **obsługiwane** używanie lokalnych lasów/domen przy użyciu "kropek" (nazwa zawiera kropkę ".") Nazwy NetBios.
* Zaleca się [włączenie kosza Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serwer Azure AD Connect
>[!IMPORTANT]
>Serwer Azure AD Connect zawiera krytyczne dane tożsamości i powinny być traktowane jako składnik warstwy 0 zgodnie z opisem w [Active Directory modelu warstwy administracyjnej](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Nie można zainstalować Azure AD Connect na małym lub systemie Windows Server Essentials przed 2019 (obsługiwany jest system Windows Server Essentials 2019). Na serwerze musi być używany system Windows Server Standard lub szybszy.
* Instalowanie Azure AD Connect na kontrolerze domeny nie jest zalecane z powodu praktyk zabezpieczeń i bardziej restrykcyjnych ustawień, które mogą uniemożliwiać poprawne Instalowanie Azure AD Connect.
* Na serwerze Azure AD Connect musi być zainstalowany pełny graficzny interfejs użytkownika. Nie jest **obsługiwane** Instalowanie programu w trybie Server Core.
>[!IMPORTANT]
>Instalowanie Azure AD Connect w Small Business Server, Server Essentials lub Server Core nie jest obsługiwane.

* Azure AD Connect musi być zainstalowany w systemie Windows Server 2008 R2 lub nowszym. Ten serwer musi być przyłączony do domeny i może być kontrolerem domeny lub serwerem członkowskim.
* W przypadku instalowania Azure AD Connect w systemie Windows Server 2008 R2 upewnij się, że zastosowano najnowsze poprawki z Windows Update. Nie można rozpocząć instalacji przy użyciu niezgodnego serwera.
* Jeśli planujesz korzystanie z **synchronizacji haseł**funkcji, serwer Azure AD Connect musi być w systemie Windows Server 2008 R2 z dodatkiem SP1 lub nowszym.
* Jeśli planujesz użycie **konta usługi zarządzanego przez grupę**, serwer Azure AD Connect musi być w systemie Windows Server 2012 lub nowszym.
* Serwer Azure AD Connect musi mieć zainstalowany [.NET Framework 4.5.1](#component-prerequisites) lub nowszy oraz [program Microsoft PowerShell 3,0](#component-prerequisites) lub nowszy.
* Serwer Azure AD Connect nie może mieć włączonej zasady grupy transkrypcji programu PowerShell.
* Jeśli Active Directory Federation Services jest wdrażana, serwery, na których zainstalowano AD FS lub serwer proxy aplikacji sieci Web, muszą być systemu Windows Server 2012 R2 lub nowszego. [Zdalne zarządzanie systemem Windows](#windows-remote-management) musi być włączone na tych serwerach na potrzeby instalacji zdalnej.
* Jeśli Active Directory Federation Services jest wdrażana, potrzebne są [Certyfikaty SSL](#ssl-certificate-requirements).
* W przypadku wdrażania Active Directory Federation Services należy skonfigurować [rozpoznawanie nazw](#name-resolution-for-federation-servers).
* Jeśli administratorzy globalni mają włączoną usługę MFA, adres URL **https://secure.aadcdn.microsoftonline-p.com** musi znajdować się na liście zaufanych witryn. Zostanie wyświetlony monit o dodanie tej witryny do listy zaufanych witryn, gdy zostanie wyświetlony monit dotyczący wyzwania usługi MFA i nie został dodany przed. Możesz użyć programu Internet Explorer, aby dodać go do zaufanych witryn.
* Firma Microsoft zaleca, aby zabezpieczyć serwer Azure AD Connect, aby zmniejszyć obszar ataków zabezpieczeń dla tego krytycznego składnika środowiska IT.  Poniższe zalecenia spowodują zmniejszenie zagrożeń bezpieczeństwa organizacji.

* Wdróż Azure AD Connect na serwerze przyłączonym do domeny i Ogranicz dostęp administracyjny do administratorów domeny lub innych chronionych grup zabezpieczeń.

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grup administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie wbudowanego konta administratora](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Poprawa zabezpieczeń i utrzymywanie poprzez zmniejszenie powierzchni ataków](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszanie obszaru ataków Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server używany przez Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest zainstalowana SQL Server 2012 Express LocalDB (wersja uproszczona SQL Server Express). SQL Server Express ma limit rozmiaru 10 GB, który umożliwia zarządzanie około 100 000 obiektów. Aby zarządzać większą ilością obiektów katalogu, należy wskazać Kreator instalacji innej instalacji SQL Server. Typ instalacji SQL Server może mieć wpływ na [wydajność Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* W przypadku korzystania z innej instalacji SQL Server są stosowane następujące wymagania:
  * Azure AD Connect obsługuje wszystkie wersje Microsoft SQL Server z 2008 R2 (z najnowszym dodatkiem Service Pack) do SQL Server 2019. Microsoft Azure SQL Database **nie jest obsługiwana** jako baza danych.
  * Musisz użyć sortowania SQL bez uwzględniania wielkości liter. Te sortowania są identyfikowane za pomocą \_CI_ w ich imieniu. Nie jest **obsługiwane** użycie sortowania z uwzględnieniem wielkości liter, identyfikowane przez \_cs_ w ich imieniu.
  * Można korzystać tylko z jednego aparatu synchronizacji na wystąpienie bazy danych SQL. Udostępnianie wystąpienia programu SQL Server za pomocą narzędzia synchronizacji FIM/MIM, narzędzia DirSync lub Azure AD Sync **nie jest obsługiwane** .

### <a name="accounts"></a>Konta
* Konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD, z którą chcesz przeprowadzić integrację. To konto musi być **kontem szkoły lub organizacji** i nie może być **konto Microsoft**.
* W przypadku korzystania z ustawień ekspresowych lub uaktualniania z narzędzia DirSync wymagane jest posiadanie konta administratora przedsiębiorstwa dla Active Directory lokalnego.
* [Konta w Active Directory](reference-connect-accounts-permissions.md) , jeśli używasz ścieżki instalacji ustawień niestandardowych lub konta administratora przedsiębiorstwa dla Active Directory lokalnych.

### <a name="connectivity"></a>Łączność
* Serwer Azure AD Connect wymaga rozpoznawania nazw DNS dla intranetu i Internetu. Serwer DNS musi być w stanie rozpoznawać nazwy zarówno w lokalnym Active Directory, jak i w punktach końcowych usługi Azure AD.
* Jeśli masz zapory w intranecie i musisz otworzyć porty między serwerami Azure AD Connect i kontrolerami domeny, zobacz [Azure AD Connect portów](reference-connect-ports.md) , aby uzyskać więcej informacji.
* Jeśli Twój serwer proxy lub Zapora ogranicza dostęp do adresów URL, należy otworzyć adresy URL opisane w [adresach URL pakietu Office 365 i w zakresach adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
  * Jeśli używasz Microsoft Cloud w Niemczech lub w chmurze Microsoft Azure Government, zobacz [zagadnienia dotyczące wystąpień usługi synchronizacji Azure AD Connect](reference-connect-instances.md) dla adresów URL.
* Azure AD Connect (w wersji 1.1.614.0 i After) domyślnie używa protokołu TLS 1,2 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Jeśli protokół TLS 1,2 nie jest dostępny w podstawowym systemie operacyjnym, Azure AD Connect stopniowo powraca do starszych protokołów (TLS 1,1 i TLS 1,0).
* W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Aby zmienić protokół TLS 1,2, wykonaj kroki opisane w temacie [Włączanie protokołu tls 1,2 dla Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używasz wychodzącego serwera proxy do łączenia się z Internetem, do Kreatora instalacji należy dodać następujące ustawienie w pliku **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** oraz Azure AD Connect synchronizację. Aby nawiązać połączenie z Internetem i usługą Azure AD. Ten tekst musi być wprowadzony w dolnej części pliku. W tym kodzie &lt;PROXYADDRESS &gt; reprezentuje rzeczywisty adres IP lub nazwę hosta serwera proxy.

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

* Jeśli serwer proxy wymaga uwierzytelnienia, [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) musi znajdować się w domenie i należy użyć ścieżki instalacji dostosowane ustawienia, aby określić [niestandardowe konto usługi](how-to-connect-install-custom.md#install-required-components). Wymagana jest również inna zmiana pliku Machine. config. Po tej zmianie w pliku Machine. config Kreator instalacji i aparat synchronizacji odpowiadają na żądania uwierzytelniania z serwera proxy. Na wszystkich stronach Kreatora instalacji z wyłączeniem strony **Konfiguracja** są używane poświadczenia zalogowanego użytkownika. Na stronie **Konfigurowanie** na końcu Kreatora instalacji, kontekst jest przełączany do [konta usługi](reference-connect-accounts-permissions.md#adsync-service-account) , które zostało utworzone przez użytkownika. Sekcja Machine. config powinna wyglądać następująco.

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

* Gdy Azure AD Connect wysyła żądanie sieci Web do usługi Azure AD w ramach synchronizacji katalogów, odpowiedź usługi Azure AD może potrwać do 5 minut. W przypadku serwerów proxy często istnieje konfiguracja limitu czasu bezczynności połączenia. Upewnij się, że konfiguracja jest równa co najmniej 6 minut.

Aby uzyskać więcej informacji, zobacz MSDN dotyczący [domyślnego elementu proxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Aby uzyskać więcej informacji w przypadku problemów z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

### <a name="other"></a>Inne
* Opcjonalne: konto użytkownika testowego, aby zweryfikować synchronizację.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące składników
### <a name="powershell-and-net-framework"></a>PowerShell i .NET Framework
Azure AD Connect zależy od programu Microsoft PowerShell i .NET Framework 4.5.1. Wymagana jest ta wersja lub nowsza wersja zainstalowana na serwerze. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* 2012R2 systemu Windows Server
  * Program Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * .NET Framework 4.5.1 i nowsze wersje są oferowane przez Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje systemu Windows Server w panelu sterowania.
* Systemy Windows Server 2008 R2 i Windows Server 2012
  * Najnowsza wersja programu Microsoft PowerShell jest dostępna w **systemie Windows Management Framework 4,0**, dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 i nowsze wersje są dostępne w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Włącz protokół TLS 1,2 dla Azure AD Connect
W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między serwerem aparatu synchronizacji i usługą Azure AD. Można to zmienić, konfigurując aplikacje platformy .NET do używania protokołu TLS 1,2 domyślnie na serwerze programu. Więcej informacji na temat protokołu TLS 1,2 można znaleźć w [poradniku zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Protokołu TLS 1,2 nie można włączyć przed systemem Windows Server 2008 R2 lub starszym. Upewnij się, że masz zainstalowaną poprawkę .NET 4.5.1 dla danego systemu operacyjnego, zobacz [Poradnik zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Ta poprawka lub nowsza wersja została już zainstalowana na serwerze.
2. W przypadku korzystania z systemu Windows Server 2008 R2 upewnij się, że protokół TLS 1,2 jest włączony. Na serwerze z systemem Windows Server 2012 i nowszych wersjach protokół TLS 1,2 powinien być już włączony.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Dla wszystkich systemów operacyjnych Ustaw ten klucz rejestru i uruchom ponownie serwer.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Jeśli chcesz również włączyć protokół TLS 1,2 między serwerem aparatu synchronizacji i SQL Server zdalnym, upewnij się, że wymagane wersje są zainstalowane do [obsługi protokołu TLS 1,2 dla Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Wymagania wstępne dotyczące instalacji i konfiguracji federacji
### <a name="windows-remote-management"></a>Windows Remote Management
Podczas wdrażania Active Directory Federation Services lub serwera proxy aplikacji sieci Web przy użyciu Azure AD Connect należy sprawdzić następujące wymagania:

* Jeśli serwer docelowy jest przyłączony do domeny, upewnij się, że usługa zdalne zarządzanie systemem Windows jest włączona
  * W oknie wiersza polecenia z podwyższonym poziomem uprawnień PSH — Użyj polecenia `Enable-PSRemoting –force`
* Jeśli serwer docelowy jest maszyną WAP, która nie jest przyłączona do domeny, istnieje kilka dodatkowych wymagań
  * Na maszynie docelowej (komputer WAP):
    * Upewnij się, że usługa WinRM (Windows Remote Management/WS-Management) jest uruchomiona za pomocą przystawki usługi
    * W oknie wiersza polecenia z podwyższonym poziomem uprawnień PSH — Użyj polecenia `Enable-PSRemoting –force`
  * Na komputerze, na którym uruchomiony jest Kreator (jeśli maszyna docelowa nie jest przyłączona do domeny lub domeną niezaufaną):
    * W oknie wiersza polecenia z podwyższonym poziomem uprawnień PSH — Użyj polecenia `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * W Menedżer serwera:
      * Dodaj hosta sieci Web WAP do puli maszyn (Menedżer serwera — > Zarządzaj > Dodaj serwery... Korzystanie z karty DNS)
      * Karta Menedżer serwera wszystkie serwery: kliknij prawym przyciskiem myszy serwer WAP i wybierz polecenie Zarządzaj jako..., wprowadź poświadczenia lokalne (nie domena) dla maszyny WAP
      * Aby sprawdzić poprawność zdalnej łączności PSH —, na karcie Menedżer serwera wszystkie serwery: kliknij prawym przyciskiem myszy serwer WAP i wybierz polecenie Windows PowerShell. Zdalna sesja PSH — powinna być otwarta w celu zapewnienia, że można nawiązać zdalne sesje programu PowerShell.

### <a name="ssl-certificate-requirements"></a>Wymagania dotyczące certyfikatu SSL
* Zdecydowanie zaleca się użycie tego samego certyfikatu SSL dla wszystkich węzłów farmy AD FS i wszystkich serwerów proxy aplikacji sieci Web.
* Certyfikat musi być certyfikatem x509.
* Możesz użyć certyfikatu z podpisem własnym na serwerach federacyjnych w środowisku laboratorium testowego. Jednak dla środowiska produkcyjnego zalecamy uzyskanie certyfikatu z publicznego urzędu certyfikacji.
  * W przypadku korzystania z certyfikatu niezaufanego publicznie upewnij się, że certyfikat zainstalowany na każdym serwerze proxy aplikacji sieci Web jest zaufany na serwerze lokalnym i na wszystkich serwerach federacyjnych
* Tożsamość certyfikatu musi być zgodna z nazwą usługi federacyjnej (na przykład sts.contoso.com).
  * Tożsamość jest rozszerzeniem alternatywnej nazwy podmiotu (SAN) typu dNSName lub, jeśli nie ma żadnych wpisów sieci SAN, nazwa podmiotu określona jako nazwa pospolita.  
  * W certyfikacie może znajdować się wiele wpisów sieci SAN, pod warunkiem, że jedna z nich jest zgodna z nazwą usługi federacyjnej.
  * Jeśli planujesz używać Workplace Join, wymagana jest dodatkowa sieć SAN z wartością **enterpriseregistration.** a następnie sufiks głównej nazwy użytkownika (UPN) organizacji, na przykład **enterpriseregistration.contoso.com**.
* Certyfikaty oparte na kluczach nowej generacji (CNG) i dostawcy magazynu kluczy nie są obsługiwane. Oznacza to, że należy użyć certyfikatu opartego na dostawcy usług kryptograficznych (CSP), a nie dostawcy magazynu kluczy (dostawcy magazynu kluczy).
* Obsługiwane są certyfikaty z użyciem symboli wieloznacznych.

### <a name="name-resolution-for-federation-servers"></a>Rozpoznawanie nazw dla serwerów federacyjnych
* Skonfiguruj rekordy DNS dla nazwy usługi federacyjnej AD FS (na przykład sts.contoso.com) dla intranetu (wewnętrznego serwera DNS) i ekstranetu (publiczna usługa DNS za pośrednictwem rejestratora domen). W przypadku rekordu DNS intranet upewnij się, że używasz rekordów, a nie rekordów CNAME. Jest to wymagane do poprawnego działania uwierzytelniania systemu Windows z komputera przyłączonego do domeny.
* Jeśli wdrażasz więcej niż jeden serwer AD FS lub serwera proxy aplikacji sieci Web, upewnij się, że skonfigurowano moduł równoważenia obciążenia i że rekordy DNS dla nazwy usługi federacyjnej AD FS (na przykład sts.contoso.com) wskazują moduł równoważenia obciążenia.
* Aby zintegrowane uwierzytelnianie systemu Windows działało w aplikacjach przeglądarki przy użyciu programu Internet Explorer w intranecie, należy się upewnić, że nazwa usługi federacyjnej AD FS (na przykład sts.contoso.com) zostanie dodana do strefy intranetowej w programie IE. Może to być kontrolowane przez zasady grupy i wdrożone na wszystkich komputerach przyłączonych do domeny.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect składniki pomocnicze
Poniżej znajduje się lista składników, które Azure AD Connect instalowane na serwerze, na którym zainstalowano Azure AD Connect. Ta lista dotyczy podstawowej instalacji ekspresowej. Jeśli zdecydujesz się na użycie innego SQL Server na stronie Instalowanie usług synchronizacji, program SQL Express LocalDB nie zostanie zainstalowany lokalnie.

* Azure AD Connect Health
* Narzędzia wiersza polecenia Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Klient natywny Microsoft SQL Server 2012
* Pakiet redystrybucyjny Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Wymagania sprzętowe Azure AD Connect
W poniższej tabeli przedstawiono minimalne wymagania dotyczące komputera synchronizacji Azure AD Connect.

| Liczba obiektów w Active Directory | Procesor CPU | Pamięć | Rozmiar dysku twardego |
| --- | --- | --- | --- |
| Mniej niż 10 000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50 000 – 100000 |1,6 GHz |16 GB |100 GB |
| W przypadku 100 000 lub więcej obiektów wymagana jest pełna wersja SQL Server | | | |
| 100000 — 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600 000 |1,6 GHz |32 GB |450 GB |
| Więcej niż 600 000 |1,6 GHz |32 GB |500 GB |

Poniżej przedstawiono minimalne wymagania dotyczące komputerów z uruchomionymi AD FS lub serwerami proxy aplikacji sieci Web:

* Procesor CPU: dwurdzeniowy 1,6 GHz lub szybszy
* Pamięć: 2 GB lub więcej
* Maszyna wirtualna platformy Azure: Konfiguracja a2 lub nowsza

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
