---
title: 'Program Azure AD Connect: Wymagania wstępne i sprzętu | Dokumentacja firmy Microsoft'
description: W tym temacie opisano wymagania wstępne i wymagania sprzętowe programu Azure AD Connect
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
ms.openlocfilehash: f2916c9aba7d404ff4ad380d249bd507fadf71ea
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310072"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące usługi Azure AD Connect
W tym temacie opisano wymagania wstępne i wymagania sprzętowe programu Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem usługi Azure AD Connect
Zanim zainstalujesz program Azure AD Connect, istnieje kilka kwestii, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Dzierżawa usługi Azure AD. Zostanie wyświetlony jeden z [bezpłatna wersja próbna platformy](https://azure.microsoft.com/pricing/free-trial/). Jedną z następujących portali umożliwia zarządzanie program Azure AD Connect:
  * [Witryny Azure portal](https://portal.azure.com).
  * [Portalu Office](https://portal.office.com).  
* [Dodanie i zweryfikowanie domeny](../active-directory-domains-add-azure-portal.md) zamierzasz używać w usłudze Azure AD. Na przykład jeśli planujesz użyć contoso.com dla użytkowników, a następnie upewnij się, że ta domena została zweryfikowana, i nie używasz tylko domyślnej domeny contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD umożliwia przez obiekty domyślne 50 tys. Podczas weryfikowania domeny zwiększono limit do 300 tys. obiektów. Jeśli potrzebujesz więcej obiektów w usłudze Azure AD, musisz otworzyć sprawę pomocy technicznej, aby jeszcze bardziej limitu. Jeśli potrzebujesz więcej niż 500 tys. obiektów, potrzebujesz licencji, takich jak usługi Office 365, Azure AD podstawowa, Azure AD Premium lub pakietu Enterprise Mobility i zabezpieczeń.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych w środowisku lokalnym
* Użyj [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) na identyfikację błędów, takich jak duplikaty i problemów z formatowaniem w katalogu, można było zsynchronizować z usługą Azure AD i Office 365.
* Przegląd [funkcji opcjonalnych synchronizacji, można włączyć w usłudze Azure AD](how-to-connect-syncservice-features.md) i ocena funkcji, które należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja i lasu poziom funkcjonalności dla schematu usługi AD musi być Windows Server 2003 lub nowszym. Kontrolery domeny można uruchomić z dowolnej wersji, tak długo, jak są spełnione wymagania poziomu schematu i lasu.
* Jeśli planujesz używać funkcji **funkcji zapisywania zwrotnego haseł**, kontrolery domeny musi być w systemie Windows Server 2008 R2 lub nowszym.
* Kontroler domeny używane przez usługę Azure AD musi być zapisywalny. Jest **nieobsługiwane** przy użyciu kontrolera RODC (kontroler domeny tylko do odczytu) i usługi Azure AD Connect nie jest zgodna z dowolnym przekierowuje zapisu.
* Jest **nieobsługiwane** do użycia w środowisku lokalnym lasami/domenami przy użyciu "kropkowana" (nazwa zawiera kropkę ".") Nazwy NetBios.
* Zaleca się [Włączanie Kosza usługi Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serwer usługi Azure AD Connect
>[!IMPORTANT]
>Serwer programu Azure AD Connect zawiera dane krytyczne tożsamości i powinny być traktowane jako składnik warstwy 0, zgodnie z opisem w [model warstw administracyjnych usługi Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#ADATM_BM)

* Program Azure AD Connect nie można zainstalować na Small Business Server, Windows Server Essentials przed 2019 r (system Windows Server Essentials 2019 jest obsługiwane). Serwer musi używać systemu Windows Server standard lub lepszej.
* Instalowanie usługi Azure AD Connect na kontrolerze domeny nie jest zalecane z powodu rozwiązania w zakresie zabezpieczeń i bardziej restrykcyjne ustawienia, które mogą uniemożliwić poprawne zainstalowanie przez program Azure AD Connect.
* Na serwerze usługi Azure AD Connect musi być pełnym interfejsem GUI zainstalowane. Jest **nieobsługiwane** zainstalować w instalacji server core.
>[!IMPORTANT]
>Instalowanie usługi Azure AD Connect na small business server, podstawowe informacje dotyczące serwera lub instalacja server core nie jest obsługiwane.

* Program Azure AD Connect musi być zainstalowany w systemie Windows Server 2008 R2 lub nowszym. Ten serwer musi być przyłączone do domeny i może być kontrolerem domeny lub serwer członkowski.
* Następnie po zainstalowaniu usługi Azure AD Connect w systemie Windows Server 2008 R2, upewnij się, zastosuj najnowsze poprawki z witryny Windows Update. Instalacja nie jest możliwe jej uruchomienie bez serwera.
* Jeśli planujesz używać funkcji **synchronizacji haseł**, serwer programu Azure AD Connect musi być w systemie Windows Server 2008 R2 z dodatkiem SP1 lub nowszym.
* Jeśli planujesz używać **konto usługi zarządzane przez grupę**, serwer programu Azure AD Connect musi być w systemie Windows Server 2012 lub nowszym.
* Na serwerze usługi Azure AD Connect musi być [programu .NET Framework 4.5.1](#component-prerequisites) lub nowszej i [Microsoft PowerShell 3.0](#component-prerequisites) lub nowszej.
* Serwer programu Azure AD Connect nie może mieć włączonymi zasadami grupy transkrypcji programu PowerShell.
* Jeśli jest wdrażana usług federacyjnych Active Directory, serwery, na którym zainstalowany jest usług AD FS lub serwera Proxy aplikacji sieci Web musi być Windows Server 2012 R2 lub nowszym. [Windows zdalne zarządzanie](#windows-remote-management) musi być włączona na tych serwerach dla instalacji zdalnej.
* Trwa wdrażanie usług federacyjnych Active Directory, należy [certyfikaty SSL](#ssl-certificate-requirements).
* Jeśli wdrożono usług federacyjnych Active Directory, a następnie należy skonfigurować [rozpoznawanie nazw](#name-resolution-for-federation-servers).
* Jeśli Twoje Administratorzy globalni mają MFA włączona, następnie adres URL **https://secure.aadcdn.microsoftonline-p.com** musi znajdować się na liście zaufanych witryn. Monit o dodanie tej witryny do listy zaufanych witryn, gdy zostanie wyświetlony monit o żądania uwierzytelniania MFA i nie został dodany przed. Można użyć programu Internet Explorer, aby dodać go do zaufanych witryn.
* Firma Microsoft zaleca, ograniczenie funkcjonalności serwera Azure AD Connect do zmniejszenia bezpieczeństwa obszar narażony na atak to kluczowy składnik środowiska informatycznego.  Zgodnie z zaleceniami poniżej zostaną obniżone zagrożenia dla bezpieczeństwa organizacji.

* Wdrażanie usługi Azure AD Connect na serwerze przyłączonym do domeny i Ogranicz dostęp administracyjny do administratorów domeny lub inne grupy zabezpieczeń ściśle kontrolowany.

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grupy Administratorzy](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie konta wbudowanego konta administratora](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Ulepszenia zabezpieczeń i sustainment, zmniejszając powierzchni ataku](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszenie podatności usługi Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Program SQL Server używane przez program Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest instalowany program SQL Server 2012 Express LocalDB (uproszczonej wersji programu SQL Server Express). SQL Server Express ma ograniczenie rozmiaru 10GB, który pozwala na zarządzanie około 100 000 obiektów. Jeśli trzeba zarządzać większą liczbą obiektów katalogu, należy wskazać kreatorowi instalacji na inną instalację programu SQL Server. Typ instalacji programu SQL Server mogą mieć wpływ na [wydajności programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Jeśli używasz innej instalacji programu SQL Server, mają zastosowanie następujące wymagania:
  * Azure AD Connect obsługuje wszystkich wersji programu Microsoft SQL Server 2008 R2 (za pomocą najnowszego dodatku Service Pack) do 2019 r programu SQL Server. Microsoft Azure SQL Database to **nieobsługiwane** jako bazy danych.
  * Należy użyć bez uwzględniania wielkości liter sortowania bazy danych SQL. Te sortowania są oznaczone symbolem \_CI_ w ich imieniu. Jest **nieobsługiwane** do liter identyfikowane przez \_cs_ — element w ich imieniu.
  * Może mieć tylko jeden aparat synchronizacji dla każdego wystąpienia programu SQL. Jest **nieobsługiwane** udostępniania wystąpienia programu SQL za pomocą synchronizacji programu FIM/programu MIM, narzędzie DirSync lub Azure AD Sync.

### <a name="accounts"></a>Konta
* Konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD, do którego mają być Integracja z usługą. To konto musi być **służbowego lub konta organizacji** i nie może być **konta Microsoft**.
* Użyj ustawień ekspresowych lub uaktualnienie z narzędzia DirSync, musi mieć konto administratora przedsiębiorstwa dla usługi Active Directory w środowisku lokalnym.
* [Konta w usłudze Active Directory](reference-connect-accounts-permissions.md) dla usługi Active Directory w środowisku lokalnym za pomocą ustawień niestandardowych ścieżki instalacji lub konto administratora przedsiębiorstwa.

### <a name="connectivity"></a>Łączność
* Serwer programu Azure AD Connect wymaga rozpoznawania nazw DNS dla intranetowych i internetowych. Serwer DNS musi umożliwiać rozpoznawanie nazw zarówno w usłudze Active Directory w środowisku lokalnym i punktów końcowych usługi Azure AD.
* Jeśli masz zapory w sieci Intranet i należy otworzyć porty między serwerami usługi Azure AD Connect i kontrolery domeny, a następnie zobacz [usługi Azure AD Connect porty](reference-connect-ports.md) Aby uzyskać więcej informacji.
* Jeśli serwerowi proxy lub zaporze ograniczenia, które adresy URL można uzyskać dostęp, a następnie adresy URL opisane w [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) muszą być otwarte.
  * Jeśli używasz Microsoft Cloud w Niemczech, czy w chmurze platformy Microsoft Azure Government, zobacz [Usługa synchronizacji Azure AD Connect wystąpień zagadnienia](reference-connect-instances.md) dla adresu URL.
* Program Azure AD Connect (wersja 1.1.614.0 oraz po) domyślnie używa protokołu TLS 1.2 w celu szyfrowania komunikacji między aparatem synchronizacji i Azure AD. Jeśli protokół TLS 1.2, nie jest dostępna w podstawowym systemie operacyjnym, program Azure AD Connect przyrostowo powraca do starsze protokoły (TLS 1.1 i TLS 1.0).
* Przed wersją 1.1.614.0 program Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między aparatem synchronizacji i usługi Azure AD. Aby zmienić protokołu TLS 1.2, wykonaj kroki opisane w [włączenia protokołu TLS 1.2, programu Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używasz serwera proxy ruchu wychodzącego do łączenia z Internetem, następujące ustawienie w **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** konieczne jest dodanie pliku dla Kreatora instalacji i Azure AD Synchronizacja aby można było nawiązać połączenie z Internetem a usługą Azure AD Connect. Ten tekst wpisany w dolnej części pliku. W tym kodzie &lt;PROXYADDRESS&gt; reprezentuje nazwę hosta lub adres IP rzeczywistego serwera proxy.

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

* Jeśli Twój serwer proxy wymaga uwierzytelniania, a następnie [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) musi znajdować się w domenie i musi być określona przez ścieżkę instalacji dostosowanych ustawień [niestandardowe konto usługi](how-to-connect-install-custom.md#install-required-components). Należy również inną zmianę do pliku machine.config. Dzięki tej zmianie w pliku machine.config instalacji aparatu kreatora i synchronizacji odpowiadać na żądania uwierzytelniania serwera proxy. Na wszystkich stronach kreatora instalacji, z wyłączeniem **Konfiguruj** stronie podpisanej w użytkownika poświadczenia są używane. Na **Konfiguruj** strony na końcu kreatora instalacji w kontekście zostanie przełączone na [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) utworzony przez użytkownika. Sekcja pliku machine.config powinna wyglądać następująco.

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

* Gdy program Azure AD Connect wysyła żądania sieci web do usługi Azure AD w ramach synchronizacji katalogów, usługi Azure AD może potrwać do 5 minut na odpowiedź. To częsty problem w konfiguracji limitu czasu bezczynności połączenia serwerów proxy. Upewnij się, że konfiguracja jest równa co najmniej 6 minut lub dłużej.

Aby uzyskać więcej informacji, zobacz MSDN na temat [domyślny serwer proxy elementu](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Aby uzyskać więcej informacji, jeśli masz problemy z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

### <a name="other"></a>Inne
* Opcjonalnie: Testowe konto użytkownika weryfikowanie synchronizacji.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące składników
### <a name="powershell-and-net-framework"></a>Program PowerShell i .NET Framework
Program Azure AD Connect, zależy od Microsoft PowerShell i .NET Framework 4.5.1. Należy tej wersji lub nowszej na serwerze jest zainstalowany. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* Windows Server 2012R2
  * Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * .NET framework 4.5.1 i nowszych są oferowane za pośrednictwem usługi Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje do systemu Windows Server w Panelu sterowania.
* Systemy Windows Server 2008 R2 i Windows Server 2012
  * Najnowszą wersję programu Microsoft PowerShell jest dostępna w **Windows Management Framework 4.0**, która jest dostępna na [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET framework 4.5.1 i nowszych wersji są dostępne na [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Włącz szyfrowanie TLS 1.2, dla programu Azure AD Connect
Przed wersją 1.1.614.0 program Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między serwerem aparat synchronizacji i usługi Azure AD. Można to zmienić, po skonfigurowaniu aplikacji .NET do użycia protokołu TLS 1.2, domyślnie na serwerze. Więcej informacji na temat protokołu TLS 1.2, można znaleźć w [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Nie można włączyć protokołu TLS 1.2, przed systemu Windows Server 2008 R2 lub nowszym. Upewnij się, .NET 4.5.1 poprawka instalowana w systemie operacyjnym, zobacz [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Może mieć tej poprawki lub jego nowsza wersja już zainstalowana na serwerze.
2. Jeśli używasz systemu Windows Server 2008 R2, upewnij się, że jest włączony protokół TLS 1.2. W systemu Windows Server 2012 i nowszych wersjach już powinien być włączony protokół TLS 1.2.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Dla wszystkich systemów operacyjnych ustaw następujący klucz rejestru, a następnie uruchom ponownie serwer.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Jeśli użytkownik chce również Włącz szyfrowanie TLS 1.2 między serwerem aparat synchronizacji, a na zdalnym serwerze SQL, a następnie upewnij się, że masz wymagane wersje zainstalowane dla [Obsługa protokołu TLS 1.2 dla programu Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Wymagania wstępne dotyczące Federacji instalacja i Konfiguracja
### <a name="windows-remote-management"></a>Windows Remote Management
Wdrażanie usług Active Directory Federation Services lub serwera Proxy aplikacji sieci Web za pomocą usługi Azure AD Connect, Sprawdź te wymagania:

* Jeśli serwer docelowy jest przyłączony do domeny, następnie upewnij się, że zdalnego Managed Windows jest włączona
  * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Enable-PSRemoting –force`
* Jeśli serwer docelowy jest na nie — przyłączonym do domeny maszyny proxy aplikacji sieci Web, a następnie kilka dodatkowych wymagań
  * Na komputerze docelowym (maszyna WAP):
    * Upewnij się, usługi winrm (Windows Remote Management / usługi WS-Management) jest uruchomiona za pomocą przystawki usługi
    * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Enable-PSRemoting –force`
  * Na komputerze, na którym uruchomiono kreatora (Jeśli maszyna docelowa nienależących do domeny połączone lub niezaufanej domeny):
    * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * W Menedżerze serwera:
      * Dodaj hosta sieci obwodowej WAP do puli maszyn (Menedżer serwera -> Zarządzaj -> Dodaj serwery... karta DNS)
      * Karta serwery wszystkie Menedżera serwera: kliknij prawym przyciskiem myszy serwer proxy aplikacji sieci Web i wybierz polecenie Zarządzaj jako..., wprowadź poświadczenia lokalnego (nie w domenie) dla maszyny WAP
      * Do sprawdzania poprawności połączenia zdalnego PSH, na karcie Serwery wszystkie Menedżera serwera: serwer proxy aplikacji sieci Web kliknij prawym przyciskiem myszy i wybierz polecenie programu Windows PowerShell. Sesję zdalną Psh — powinna zostać otwarta upewnij się, że można nawiązać sesji zdalnej programu PowerShell.

### <a name="ssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów SSL
* Zdecydowanie zaleca się używać tego samego certyfikatu SSL we wszystkich węzłach farmy usług AD FS i serwery proxy aplikacji sieci Web.
* Certyfikat musi być X509 certyfikatu.
* Można użyć certyfikatu z podpisem własnym na serwery federacyjne w środowisko laboratorium testowego. W środowisku produkcyjnym zaleca uzyskać certyfikat z publicznego urzędu certyfikacji.
  * Jeśli używasz certyfikatu, który nie jest zaufany publicznie, upewnij się, że certyfikat zainstalowany na każdym serwerze Proxy aplikacji sieci Web zaufane na serwerze lokalnym i na wszystkich serwerach federacyjnych
* Tożsamość certyfikatu musi być zgodna z nazwą usługi federacyjnej (na przykład sts.contoso.com).
  * Tożsamość jest albo podmiotu alternatywna nazwa podmiotu (SAN) rozszerzeniem nazwa_DNS typu lub, jeśli nie ma żadnych wpisów sieci SAN, nazwa podmiotu określone jako nazwa wspólna.  
  * Wiele wpisów SAN może być obecne w certyfikacie, pod warunkiem jeden z nich jest zgodna z nazwą usługi federacyjnej.
  * Jeśli planowane jest używana funkcja Dołącz do, dodatkowe sieci SAN jest wymagany w przypadku wartości **enterpriseregistration.** następuje sufiks główną nazwę użytkownika (UPN) w organizacji, na przykład **enterpriseregistration.contoso.com**.
* Certyfikaty oparte na CryptoAPI następnej generacji (CNG) kluczy i dostawcy magazynu kluczy nie są obsługiwane. Oznacza to, że należy użyć certyfikatu opartego na CSP (dostawca usług kryptograficznych), a nie dostawcy magazynu KLUCZY (Dostawca magazynu kluczy).
* Certyfikatów z symbolami wieloznacznymi są obsługiwane.

### <a name="name-resolution-for-federation-servers"></a>Rozpoznawanie nazw dla serwerów federacyjnych
* Skonfiguruj rekordy DNS dla usług AD FS nazwą usługi federacyjnej (na przykład sts.contoso.com) zarówno w sieci intranet (wewnętrzny serwer DNS), jak i ekstranetu (publicznym serwerze DNS za pomocą rejestratora domen). W przypadku rekordu DNS w sieci intranet, upewnij się, że używasz, A nie rekordów CNAME i rekordów. Jest to wymagane do uwierzytelniania systemu windows działać poprawnie, w tym komputerze dołączonym do domeny.
* Jeżeli wdrażasz więcej niż jednego serwera usług AD FS lub serwera Proxy aplikacji sieci Web, upewnij się że skonfigurowano moduł równoważenia obciążenia i że rekordy DNS dla nazwy usługi federacyjnej usług AD FS (np. sts.contoso.com) odwołują się do modułu równoważenia obciążenia.
* Zintegrowane uwierzytelnianie systemu windows do działania aplikacji przeglądarki przy użyciu programu Internet Explorer w sieci intranet upewnij się, że nazwa usługi federacyjnej usług AD FS (np. sts.contoso.com) został dodany do strefy intranet w programie Internet Explorer. Może to kontrolowany za pośrednictwem zasad grupy i wdrożone na wszystkich komputerach przyłączonych do domeny.

## <a name="azure-ad-connect-supporting-components"></a>Program Azure AD Connect obsługuje składniki
Oto lista składników usługi Azure AD Connect jest instalowana na serwerze, na którym jest zainstalowany program Azure AD Connect. Ta lista jest podstawowe instalacji ekspresowej. Jeśli chcesz użyć innego serwera SQL na stronie instalacji usługi synchronizacji, następnie SQL Express LocalDB nie jest zainstalowany lokalnie.

* Azure AD Connect Health
* Program Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Wymagania sprzętowe programu Azure AD Connect
W poniższej tabeli przedstawiono minimalne wymagania dotyczące komputera do synchronizacji Azure AD Connect.

| Liczba obiektów w usłudze Active Directory | Procesor CPU | Memory (Pamięć) | Rozmiar dysku twardego |
| --- | --- | --- | --- |
| Mniej niż 10 000 operacji |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Dla 100 000 lub więcej obiektów pełną wersję programu SQL Server jest wymagany | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Ponad 600 000. |1,6 GHz |32 GB |500 GB |

Minimalne wymagania dotyczące komputerów z systemem usług AD FS lub serwerów aplikacji sieci Web jest następująca:

* CPU: Podwójne podstawowe 1,6 GHz lub nowszej
* PAMIĘĆ: 2 GB lub nowszej
* Maszyna wirtualna platformy Azure: Konfiguracja a2 lub nowszej

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
