---
title: 'Azure AD Connect: Wymagania wstępne i sprzętu | Dokumentacja firmy Microsoft'
description: W tym temacie opisano wymagania wstępne i wymagania sprzętowe programu Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: billmath
ms.openlocfilehash: d6d6eadf0ae8996b019a0564715f843913101944
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące usługi Azure AD Connect
W tym temacie opisano wymagania wstępne i wymagania sprzętowe programu Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem usługi Azure AD Connect
Przed zainstalowaniem usługi Azure AD Connect, istnieje kilka rzeczy, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Subskrypcja platformy Azure lub [subskrypcji wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Ta subskrypcja jest tylko wymagane do uzyskiwania dostępu do portalu Azure, a nie za pomocą usługi Azure AD Connect. Jeśli korzystasz z programu PowerShell lub usługi Office 365, nie można uzyskać subskrypcję Azure do użycia usługi Azure AD Connect. Jeśli użytkownik ma licencję usługi Office 365, można także skorzystać z portalu usługi Office 365. Z płatnej licencji usługi Office 365 można także uzyskać w portalu Azure w portalu usługi Office 365.
  * Można również użyć [portalu Azure](https://portal.azure.com). Ten portal nie wymaga licencji usługi Azure AD.
* [Dodaj i zweryfikuj domenę](../active-directory-domains-add-azure-portal.md) planujesz używać w usłudze Azure AD. Na przykład jeśli planujesz użyć contoso.com dla użytkowników, a następnie upewnij się, że ta domena została zweryfikowana i nie używasz tylko domyślnej domeny contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD umożliwia przez obiekty domyślne 50k. Podczas weryfikowania domeny limit zostaje zwiększona do 300 obiektów k. Jeśli potrzebujesz więcej obiektów w usłudze Azure AD, należy otworzyć do sprawę pomocy technicznej, aby jeszcze bardziej zwiększyć limit. Jeśli potrzebujesz więcej niż 500 obiektów k potrzebna licencja, takich jak usługi Office 365, Azure AD podstawowa usługi Azure AD Premium lub pakietu Enterprise Mobility i zabezpieczeń.
* ADSyncPrep jest moduł skryptu programu PowerShell, który udostępnia funkcje, które są używane w celu przygotowania środowiska usługi Active Directory programu Azure AD Connect.  Wymaga ADSyncPrep [Azure AD Microsoft Online v1.1 modułu PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).  W wersji 2 nie będzie działać.  Będzie można zainstalować przy użyciu modułu `Install-Module` polecenia cmdlet.  Aby uzyskać więcej informacji, zobacz link podany.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych lokalnych
* Użyj [narzędzia IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) do identyfikowania błędów, takich jak duplikaty i problemów z formatowaniem w katalogu przed synchronizacją z usługą Azure AD i Office 365.
* Przegląd [funkcje opcjonalne synchronizacji, można włączyć w usłudze Azure AD](active-directory-aadconnectsyncservice-features.md) i ocenić, które funkcje należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja i lasu poziom funkcjonalności dla schematu AD musi być systemu Windows Server 2003 lub nowszym. Kontrolery domeny można dowolną wersją tak długo, jak zostały spełnione wymagania poziomu schematu i lasu.
* Jeśli zamierzasz korzystać z funkcji **funkcji zapisywania zwrotnego haseł**, kontrolery domeny musi być w systemie Windows Server 2008 (z najnowszą SP) lub nowszym. Jeśli Twoje kontrolery domeny są 2008 (pre-R2), a następnie również należy zastosować [poprawkę KB2386717](http://support.microsoft.com/kb/2386717).
* Musi być zapisywalny kontroler domeny używane przez usługę Azure AD. Jest **nieobsługiwane** przy użyciu kontrolera RODC (kontroler domeny tylko do odczytu) i Azure AD Connect nie jest zgodna z dowolnym przekierowuje zapisu.
* Jest **nieobsługiwane** do użycia z lokalnymi lasami/domenami przy użyciu drugiego poziomu (jednej etykiety domen).
* Jest **nieobsługiwane** do użycia z lokalnymi lasami/domenami przy użyciu "kropkami" (nazwa zawiera znak kropki ".") Nazwy NetBios.
* Zaleca się [Włączanie Kosza usługi Active Directory](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serwer systemu Azure AD Connect
* Nie można zainstalować usługi Azure AD Connect na Small Business Server lub Windows Server Essentials. Serwer muszą używać systemu Windows Server standard lub większą.
* Serwer usługi Azure AD Connect musi mieć pełnym interfejsem GUI zainstalowane. Jest **nieobsługiwane** zainstalować w instalacji server core.
* Azure AD Connect musi być zainstalowany w systemie Windows Server 2008 lub nowszym. Ten serwer może być kontrolerem domeny lub serwer członkowski po przy użyciu ustawień ekspresowych. Jeśli używasz ustawienia niestandardowe, serwer może być autonomiczne i nie musi być przyłączony do domeny.
* Następnie po zainstalowaniu usługi Azure AD Connect w systemie Windows Server 2008 lub Windows Server 2008 R2, upewnij się, że zastosowanie najnowszych poprawek z witryny Windows Update. Instalacja nie jest możliwe jej uruchomienie z serwerem bez poprawki.
* Jeśli zamierzasz korzystać z funkcji **synchronizacji haseł**, Azure AD Connect serwera musi być w systemie Windows Server 2008 R2 z dodatkiem SP1 lub nowszym.
* Jeśli planujesz używać **konto usługi zarządzane przez grupę**, Azure AD Connect serwera musi być w systemie Windows Server 2012 lub nowszym.
* Serwer usługi Azure AD Connect musi mieć [.NET Framework 4.5.1](#component-prerequisites) lub nowszym i [Microsoft PowerShell 3.0](#component-prerequisites) lub nowszy.
* Serwer usługi Azure AD Connect nie może mieć włączonymi zasadami grupy przekształcania programu PowerShell.
* Jeśli wdrażana usług federacyjnych Active Directory, serwery, na którym są zainstalowane usługi AD FS lub serwer Proxy aplikacji sieci Web musi być Windows Server 2012 R2 lub nowszym. [Zdalne zarządzanie systemem Windows](#windows-remote-management) na te serwery do zdalnej instalacji musi być włączona.
* Jeśli wdrażana usług federacyjnych Active Directory, należy [certyfikaty SSL](#ssl-certificate-requirements).
* Jeśli jest wdrażany usług federacyjnych Active Directory, a następnie należy skonfigurować [rozpoznawanie nazw](#name-resolution-for-federation-servers).
* Jeśli Twoje Administratorzy globalni mają MFA włączona, następnie adres URL **https://secure.aadcdn.microsoftonline-p.com** musi znajdować się na liście zaufanych witryn. Zostanie wyświetlony monit, gdy zostanie wyświetlony monit o żądanie uwierzytelniania MFA i nie został dodany przed, Dodaj tę witrynę do listy zaufanych witryn. Aby dodać go do zaufanych witryn, można użyć programu Internet Explorer.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server używane przez program Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest instalowany program SQL Server 2012 Express LocalDB (światła wersja programu SQL Server Express). SQL Server Express ma limit rozmiaru 10GB, który umożliwia zarządzanie około 100 000 obiektów. Trzeba zarządzać większą ilość obiektów katalogu, należy wskazać Kreatora instalacji na inną instalację programu SQL Server.
* Jeśli używasz oddzielny serwer SQL, mają zastosowanie te wymagania:
  * Azure AD Connect obsługuje wszystkie wersje programu Microsoft SQL Server z programu SQL Server 2008 (za pomocą najnowszego dodatku Service Pack) do dodatku SP1 dla programu SQL Server 2016. Baza danych SQL Azure firmy Microsoft jest **nieobsługiwane** jako bazy danych.
  * Należy użyć bez uwzględniania wielkości liter sortowania bazy danych SQL. Te sortowania są oznaczone symbolem \_CI_ w ich imieniu. Jest **nieobsługiwane** do korzystania z sortowania z uwzględnieniem wielkości liter, identyfikowane przez \_cs_ — w ich imieniu.
  * Może mieć tylko jeden aparat synchronizacji pojedyncze wystąpienie serwera SQL. Jest **nieobsługiwane** udostępniać wystąpienia programu SQL synchronizacji programu FIM/MIM narzędzia DirSync i Azure AD Sync.

### <a name="accounts"></a>Konta
* Konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD chcesz zintegrować z. To konto musi być **konto służbowe lub organizacji** i nie może być **konta Microsoft**.
* Użyj ustawień ekspresowych lub uaktualnienie z narzędzia DirSync, musi mieć konto administratora przedsiębiorstwa dla lokalnej usługi Active Directory.
* [Konta w usłudze Active Directory](active-directory-aadconnect-accounts-permissions.md) użycie ścieżki instalacji ustawienia niestandardowe.

### <a name="connectivity"></a>Łączność
* Serwer usługi Azure AD Connect potrzebuje rozpoznawania nazw DNS dla intranetowych i internetowych. Serwer DNS musi mieć możliwość rozpoznania nazwy zarówno do lokalnej usługi Active Directory i punktów końcowych usługi Azure AD.
* Jeśli masz zapory w sieci Intranet i należy otworzyć porty między serwerami usługi Azure AD Connect i kontrolerach domeny, a następnie zobacz [Azure AD Connect porty](active-directory-aadconnect-ports.md) Aby uzyskać więcej informacji.
* Jeśli z serwera proxy lub zapory ograniczenia, których adresy URL są dostępne, a następnie adresy URL opisane w [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) musi być otwarty.
  * Jeżeli używasz Microsoft Cloud w Niemczech lub w chmurze Microsoft Azure dla instytucji rządowych, zobacz [zagadnienia dotyczące wystąpienia usługi synchronizacji programu Azure AD Connect](active-directory-aadconnect-instances.md) dla adresu URL.
* Azure AD Connect (wersja 1.1.614.0 oraz po) domyślnie używa protokołu TLS 1.2 w celu szyfrowania komunikacji między aparatem synchronizacji i Azure AD. Jeśli protokół TLS 1.2 jest niedostępna na system operacyjny, Azure AD Connect przyrostowo powraca do poprzednich wersji protokołów (TLS 1.1 i TLS 1.0). Na przykład Azure AD Connect uruchomiony w systemie Windows Server 2008 używa protokołu TLS 1.0, ponieważ systemu Windows Server 2008 nie obsługuje protokołu TLS 1.1 i TLS 1.2.
* Przed wersją 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między aparatem synchronizacji i Azure AD. Aby zmienić protokołu TLS 1.2, postępuj zgodnie z instrukcjami [włączenia protokołu TLS 1.2, programu Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używasz serwera proxy ruchu wychodzącego do łączenia się z Internetem następujące ustawienie w **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** pliku, należy dodać do Kreatora instalacji i Azure AD Połącz synchronizacji, aby można było nawiązać połączenia z Internetem i Azure AD. Ten tekst musi być wprowadzona w dolnej części pliku. W tym kodzie &lt;PROXYADRESS&gt; reprezentuje rzeczywisty proxy IP adres lub nazwę hosta.

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

* Jeśli serwer proxy wymaga uwierzytelniania, a następnie [konto usługi](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) musi znajdować się w domenie i musi użyć ścieżki instalacji, dostosowane ustawienia, aby określić [konto niestandardowe usługi](active-directory-aadconnect-get-started-custom.md#install-required-components). Należy również różnych zmiana w pliku machine.config. Dzięki tej zmianie w pliku machine.config aparat instalacji kreatora i synchronizacji odpowiadać na żądania uwierzytelniania z serwerem proxy. Na wszystkich stronach kreatora instalacji, z wyłączeniem **Konfiguruj** strony, podpisanych użytkownika używane są poświadczenia. Na **Konfiguruj** stronę po zakończeniu Kreatora instalacji, w kontekście się przełączone do [konto usługi](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) utworzone przez użytkownika. W sekcji machine.config powinna wyglądać następująco.

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

* Gdy Azure AD Connect wysyła żądania sieci web do usługi Azure AD w ramach synchronizacji katalogów, usługi Azure AD może potrwać do 5 minut na odpowiedź. Bardzo często serwery proxy do konfiguracji limit czasu bezczynności połączenia. Upewnij się, że konfiguracja jest ustawiona na co najmniej 6 minut lub dłużej.

Aby uzyskać więcej informacji, zobacz MSDN [domyślny serwer proxy elementu](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Aby uzyskać więcej informacji, jeśli masz problemy z łącznością, zobacz [Rozwiązywanie problemów z łącznością](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Inne
* Opcjonalnie: Test konto weryfikowanie synchronizacji.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące składnika
### <a name="powershell-and-net-framework"></a>Środowiska PowerShell i .net Framework
Azure AD Connect jest zależny od firmy Microsoft PowerShell i .NET Framework 4.5.1. Potrzebujesz tej wersji lub nowszy zainstalowany na serwerze. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* Windows Server 2012 R2
  * Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * .NET framework 4.5.1 i jego nowszych wersjach są dostępne za pośrednictwem usługi Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje do systemu Windows Server w Panelu sterowania.
* Windows Server 2008R2 i Windows Server 2012
  * Najnowszą wersję programu PowerShell firmy Microsoft jest dostępna w **Windows Management Framework 4.0**, dostępnych na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET framework 4.5.1 i jego nowszych wersjach są dostępne na [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * Najnowsza wersja obsługiwana wersja programu PowerShell jest dostępny w **Windows Management Framework 3.0**, dostępnych na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET framework 4.5.1 i jego nowszych wersjach są dostępne na [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Włącz protokół TLS 1.2 dla programu Azure AD Connect
Przed wersją 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1.0 do szyfrowania komunikacji między serwerem aparatu synchronizacji i Azure AD. Można to zmienić po skonfigurowaniu aplikacji .net do użycia protokołu TLS 1.2, domyślnie na serwerze. Więcej informacji na temat protokołu TLS 1.2 znajduje się w [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Nie można włączyć protokołu TLS 1.2 w systemie Windows Server 2008. Należy systemu Windows Server 2008 R2 lub nowszym. Upewnij się, że zainstalowana poprawka .net 4.5.1 zainstalowane w systemie operacyjnym, zobacz [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Konieczne może być ta poprawka lub jego nowsza wersja już zainstalowana na serwerze.
2. Jeśli używasz systemu Windows Server 2008 R2, upewnij się, że jest włączony protokół TLS 1.2. Na serwerze Windows Server 2012 i nowszych wersjach już powinien być włączony protokół TLS 1.2.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. We wszystkich systemach operacyjnych ustawić ten klucz rejestru, a następnie uruchom ponownie serwer.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Jeśli chcesz również włączyć protokołu TLS 1.2 między serwerem aparatu synchronizacji i zdalnym serwerze SQL, a następnie upewnij się, że masz wymagane wersje zainstalowane dla [obsługę protokołu TLS 1.2 dla programu Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Wymagania wstępne dotyczące federacyjnego instalacji i konfiguracji
### <a name="windows-remote-management"></a>Zdalne zarządzanie systemem Windows
Wdrażanie usług federacyjnych Active Directory lub serwer Proxy aplikacji sieci Web za pomocą usługi Azure AD Connect, sprawdź następujące wymagania:

* Jeśli serwer docelowy jest przyłączony do domeny, następnie upewnij się, że zdalnego zarządzania systemu Windows jest włączona.
  * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Enable-PSRemoting –force`
* Jeśli serwer docelowy jest poza domeną przyłączone WAP maszyny, a następnie istnieje kilka dodatkowych wymagań
  * Na docelowym komputerze (WAP):
    * Upewnij się, usługi winrm (zdalne zarządzanie systemem Windows / usługi WS-Management) jest uruchomiona za pomocą przystawki usługi
    * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Enable-PSRemoting –force`
  * Na komputerze, na którym działa Kreator (Jeśli komputer docelowy jest domeny z systemem innym niż sprzężone lub niezaufanej domeny):
    * W oknie polecenia z podwyższonym poziomem uprawnień Psh — polecenie `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * W Menedżerze serwera:
      * Dodaj hosta sieci obwodowej WAP do puli maszyn (Menedżer serwera -> Zarządzaj -> Dodaj serwery... karta DNS)
      * Karta serwery wszystkie Menedżera serwera: kliknij prawym przyciskiem myszy serwer proxy i wybierz polecenie Zarządzaj jako..., wprowadź poświadczenia lokalnego (nie domeny) dla komputera WAP
      * Można sprawdzić poprawności połączenia zdalnego PSH, na karcie w Menedżerze serwera wszystkie serwery: kliknij prawym przyciskiem myszy serwer proxy i wybierz polecenie programu Windows PowerShell. Sesję zdalną PSH należy otworzyć do upewnij się, że można nawiązać sesji zdalnego programu PowerShell.

### <a name="ssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów SSL
* Zdecydowanie zaleca się Użyj tego samego certyfikatu SSL na wszystkich węzłach farmę usług AD FS i wszystkich serwerach proxy aplikacji sieci Web.
* Certyfikat musi być X509 certyfikatu.
* Certyfikatu z podpisem własnym można używać na serwerach federacyjnych w środowisku laboratorium testowego. W środowisku produkcyjnym zalecamy uzyskać certyfikat z publicznego urzędu certyfikacji.
  * Jeśli używa certyfikatu, który nie jest zaufany publicznie, upewnij się, że certyfikat zainstalowany na każdym serwerze Proxy aplikacji sieci Web jest zaufany na serwerze lokalnym, a na wszystkich serwerach federacyjnych
* Tożsamość certyfikat musi być zgodna nazwa usługi federacyjnej (na przykład sts.contoso.com).
  * Tożsamość jest albo podmiot alternatywny (SAN) rozszerzenie nazwy elementu dNSName typu lub, jeśli nie ma żadnych wpisów sieci SAN, nazwa podmiotu określony jako nazwę pospolitą.  
  * Wiele wpisów sieci SAN mogą być obecne w certyfikatu, pod warunkiem jeden z nich jest zgodna z nazwą usługi federacyjnej.
  * Jeśli planujesz używać funkcji dołączania, dodatkowe sieci SAN jest wymagany w przypadku wartości **enterpriseregistration.** następuje sufiks główną nazwę użytkownika (UPN) w Twojej organizacji, na przykład **enterpriseregistration.contoso.com**.
* Certyfikaty na podstawie CryptoAPI next generation (CNG) kluczy i dostawcy magazynu kluczy nie są obsługiwane. Oznacza to, że muszą używać certyfikatu na podstawie CSP (dostawca usług kryptograficznych) i nie dostawcy magazynu KLUCZY (Dostawca magazynu kluczy).
* Certyfikaty — symbol wieloznaczny są obsługiwane.

### <a name="name-resolution-for-federation-servers"></a>Rozpoznawanie nazw dla serwerów federacyjnych
* Należy skonfigurować rekordy DNS dla usług AD FS nazwa usługi federacyjnej (np. sts.contoso.com) dla (wewnętrznego serwera DNS) intranetu i ekstranetu (publicznym systemie DNS za pomocą rejestratora domen). Dla rekordu DNS w intranecie, upewnij się, że używasz A rekordów i nie rekordy CNAME. Jest to wymagane do uwierzytelniania systemu windows działać poprawnie z komputera dołączonego do domeny.
* Jeśli wdrażasz więcej niż jednego serwera usług AD FS lub serwer Proxy aplikacji sieci Web, następnie upewnij się, czy zostały skonfigurowane przez moduł równoważenia obciążenia i że rekordy DNS dla nazwy usługi federacyjnej usług AD FS (np. sts.contoso.com) odwołują się do usługi równoważenia obciążenia.
* Dla zintegrowanego uwierzytelniania systemu windows dla aplikacji przeglądarki przy użyciu programu Internet Explorer w sieci intranet upewnij się, że nazwa usługi federacyjnej usług AD FS (np. sts.contoso.com) został dodany do strefy intranet w programie Internet Explorer. Może to kontrolowane przez zasady grupy i wdrożone na wszystkich komputerach przyłączonych do domeny.

## <a name="azure-ad-connect-supporting-components"></a>Obsługa składników usługi Azure AD Connect
Poniżej znajduje się lista składników, które Azure AD Connect instaluje na serwerze, na którym zainstalowano Azure AD Connect. Ta lista jest podstawowe instalacji ekspresowej. Jeśli chcesz użyć innego serwera SQL na stronie instalacji usługi synchronizacji, następnie SQL Express LocalDB nie zainstalowano lokalnie.

* Azure AD Connect Health
* Microsoft Online Services Asystenta logowania dla specjalistów IT (zainstalowany ale nie zależność)
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 redystrybucji pakietu

## <a name="hardware-requirements-for-azure-ad-connect"></a>Wymagania sprzętowe programu Azure AD Connect
W poniższej tabeli przedstawiono minimalne wymagania dotyczące komputera do synchronizacji Azure AD Connect.

| Liczba obiektów w usłudze Active Directory | Procesor CPU | Memory (Pamięć) | Rozmiar dysku twardego |
| --- | --- | --- | --- |
| Mniej niż 10 000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Dla 100 000 lub więcej obiektów jest wymagana pełna wersja programu SQL Server | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Ponad 600 000. |1,6 GHz |32 GB |500 GB |

Minimalne wymagania dotyczące komputerów z usług AD FS lub serwerów aplikacji sieci Web jest następujący:

* Procesora CPU: Dwóch podstawowych 1,6 GHz lub nowszej
* PAMIĘCI: 2 GB lub nowszej
* Maszyna wirtualna platformy Azure: A2 konfiguracji lub nowszej

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
