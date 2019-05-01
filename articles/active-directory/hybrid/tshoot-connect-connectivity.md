---
title: 'Program Azure AD Connect: Rozwiązywanie problemów z usługi Azure AD problemy z łącznością | Dokumentacja firmy Microsoft'
description: Wyjaśnia, jak rozwiązywać problemy z łącznością w programie Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7519f47037d2d7ff37564ab27c1cc58b65ff6c14
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572785"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Rozwiązywanie problemów z łącznością z usługi Azure AD
W tym artykule wyjaśniono, jak działa łączność między program Azure AD Connect a usługą Azure AD i jak rozwiązać problemy z łącznością. Te problemy z największym prawdopodobieństwem mogą być widoczne w środowisku przy użyciu serwera proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Rozwiązywanie problemów z łącznością w Kreatorze instalacji
Program Azure AD Connect jest korzystających z nowoczesnego uwierzytelniania (przy użyciu biblioteki ADAL), do uwierzytelniania. Kreator instalacji i odpowiedniego aparatu synchronizacji wymagają machine.config, aby zostać prawidłowo skonfigurowane, ponieważ te dwa aplikacji .NET.

W tym artykule pokazano, jak firma Fabrikam nawiązuje połączenie z usługą Azure AD za pośrednictwem jego serwera proxy. Serwer proxy ma nazwę fabrikamproxy i jest używany port 8080.

Najpierw musimy upewnić się, że [ **machine.config** ](how-to-connect-install-prerequisites.md#connectivity) jest poprawnie skonfigurowana.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> W niektórych blogi firmy Microsoft jest udokumentowany, należy wprowadzić zmiany do miiserver.exe.config zamiast tego. Jednak ten plik jest zastępowany na każdym uaktualnieniu, nawet jeśli działa podczas początkowej instalacji, system przestanie działać na najpierw uaktualnić. Z tego powodu zaleca się zaktualizować machine.config w zamian.
>
>

Serwer proxy musi mieć odpowiednie adresy URL otwarty. Oficjalna listy jest udokumentowany w [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Tych adresów URL Poniższa tabela dotyczy absolutne minimum systemu od zera, aby można było połączyć się z usługi Azure AD w ogóle. Ta lista nie zawiera żadnych opcjonalne funkcje, takie jak zapisywanie zwrotne haseł lub Azure AD Connect Health. Jest on opisane tutaj pomagające w rozwiązywaniu problemów dla początkowej konfiguracji.

| Adres URL | Port | Opis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Używane do pobierania listy CRL. |
| \*.verisign.com |HTTP/80 |Używane do pobierania listy CRL. |
| \*.entrust.net |HTTP/80 |Używane do pobierania listy CRL dla usługi MFA. |
| \*.windows.net |HTTPS/443 |Używane do logowania do usługi Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Używany do uwierzytelniania Wieloskładnikowego. |
| \*.microsoftonline.com |HTTPS/443 |Umożliwia konfigurowanie katalogu usługi Azure AD i importowanie/eksportowanie danych. |

## <a name="errors-in-the-wizard"></a>Błędy Kreatora
Kreator instalacji korzysta z dwóch różnych kontekstach zabezpieczeń. Na stronie **nawiązywanie połączenia z usługi Azure AD**, korzysta ona z aktualnie zalogowanego użytkownika. Na stronie **Konfiguruj**, jest zmiana [konto na którym uruchomiono usługę dla aparatu synchronizacji](reference-connect-accounts-permissions.md#adsync-service-account). Jeśli wystąpi problem, wygląda na to, najprawdopodobniej osiągnięcia **nawiązywanie połączenia z usługi Azure AD** strony w Kreatorze konfiguracji serwera proxy jest globalne.

Następujące problemy są typowe błędy, które wystąpią w Kreatorze instalacji.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Kreator instalacji nie został poprawnie skonfigurowany
Ten błąd jest wyświetlany, gdy Kreator nie może nawiązać połączenia serwera proxy.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Jeśli zostanie wyświetlony ten błąd, sprawdź [machine.config](how-to-connect-install-prerequisites.md#connectivity) został poprawnie skonfigurowany.
* Jeśli który wydaje się prawidłowe, wykonaj kroki opisane w [Zweryfikuj łączność serwera proxy](#verify-proxy-connectivity) aby zobaczyć, czy problem znajduje się poza także kreatora.

### <a name="a-microsoft-account-is-used"></a>Używane jest konto Microsoft
Jeśli używasz **konta Microsoft** zamiast **szkoły lub organizacji** konta, zostanie wyświetlony błąd ogólny.  
![Account firmy Microsoft jest używane](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Nie można nawiązać połączenia z punktu końcowego usługi MFA
Ten błąd pojawia się, gdy punkt końcowy **https://secure.aadcdn.microsoftonline-p.com** nie można nawiązać połączenia i administratorem globalnym jest włączone uwierzytelnianie MFA.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Jeśli zostanie wyświetlony ten błąd, upewnij się, że punkt końcowy **secure.aadcdn.microsoftonline p.com** został dodany do serwera proxy.

### <a name="the-password-cannot-be-verified"></a>Nie można zweryfikować hasła
Jeśli Kreator instalacji zakończy się podczas łączenia z usługą Azure AD, ale nie można zweryfikować hasła, że zostanie wyświetlony ten błąd:  
![Nieprawidłowe hasło.](./media/tshoot-connect-connectivity/badpassword.png)

* Jest to hasło hasło tymczasowe i muszą być zmienione? Jest to rzeczywiście poprawne hasło? Spróbuj zalogować się do https://login.microsoftonline.com (na innym komputerze niż serwer programu Azure AD Connect) i Sprawdź konta są wykorzystywane.

### <a name="verify-proxy-connectivity"></a>Sprawdź łączność z serwera proxy
Aby sprawdzić, czy serwer Azure AD Connect ma rzeczywisty łączność z serwera Proxy i Internet, aby zobaczyć, jeśli serwer proxy zezwala na żądania sieci web lub nie należy użyć niektórych programu PowerShell. W wierszu polecenia programu PowerShell, uruchom `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Ma pod względem technicznym pierwsze wywołanie https://login.microsoftonline.com ten identyfikator URI działa tak dobrze, ale inny identyfikator URI jest szybsze reagowanie.)

Program PowerShell korzysta z konfiguracji w pliku machine.config do kontaktowania się z serwerem proxy. Ustawienia usług winhttp/Netsh nie wpływa na tych poleceń cmdlet.

Jeśli serwer proxy jest skonfigurowany poprawnie, należy uzyskać stan oznaczający powodzenie: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Jeśli zostanie wyświetlony **nie można nawiązać połączenia z serwerem zdalnym**, następnie próbuje nawiązać bezpośrednie wywołanie bez użycia serwera proxy programu PowerShell lub DNS nie jest poprawnie skonfigurowana. Upewnij się, że **machine.config** plik jest poprawnie skonfigurowana.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Jeśli serwer proxy nie jest poprawnie skonfigurowana, wystąpi błąd: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Błąd | Tekst błędu | Komentarz |
| --- | --- | --- |
| 403 |Zabroniony |Serwer proxy nie został otwarty dla żądanego adresu URL. Ponownie konfigurację serwera proxy i upewnij się, [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zostały otwarte. |
| 407 |Wymagane uwierzytelnianie serwera proxy |Wymagany serwer proxy, zaloguj się i żaden nie został podany. Jeśli Twój serwer proxy wymaga uwierzytelniania, upewnij się mieć tego ustawienia skonfigurowane w pliku machine.config. Ponadto upewnij się, że używasz konta domeny dla użytkownika działania kreatora, a dla konta usługi. |

### <a name="proxy-idle-timeout-setting"></a>Ustawienia limitu czasu bezczynności serwera proxy
Gdy program Azure AD Connect wysyła żądanie eksportu do usługi Azure AD, usługa Azure AD może potrwać do 5 minut, aby przetworzyć żądanie przed wygenerowaniem odpowiedzi. Można to zrobić, zwłaszcza, jeśli istnieje wiele obiektów grupy za pomocą członkostwa w grupach dużych zawarte w tym samym żądanie eksportu. Upewnij się, że limit czasu bezczynności serwera Proxy jest skonfigurowany do być większa niż 5 minut. W przeciwnym razie można zaobserwować sporadycznie problemu z usługą Azure AD na serwerze programu Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Wzorzec komunikacji między program Azure AD Connect a usługą Azure AD
Jeśli wykonano wszystkie te kroki poprzedniego i nadal nie można połączyć, może być w tym momencie rozpocząć przyglądanie się dzienniki sieciowe. W tej sekcji jest dokumentowanie wzorzec normalnych, jak i pomyślnie łączności. Jest również wyświetlanie listy wspólne śledzi czerwony, które można zignorować, odczytując dzienniki sieciowe.

* Brak wywołania https://dc.services.visualstudio.com. Nie jest wymagane do tego otwórz adres URL serwera proxy dla instalacji zakończyło się sukcesem i można zignorować te wywołania.
* Zobaczysz, że rozpoznawanie nazw dns Wyświetla rzeczywiste hosty w nsatc.net przestrzeni nazw DNS i inne przestrzenie nazw nie ma pod microsoftonline.com. Jednak nie są wszystkie żądania usługi sieci web o nazwach używanego serwera i nie trzeba dodać je do serwera proxy.
* Punkty końcowe adminwebservice provisioningapi odnajdywania punktów końcowych i używana do znajdowania rzeczywisty punkt końcowy do użycia. Te punkty końcowe są różne w zależności od regionu.

### <a name="reference-proxy-logs"></a>Odwołanie do dzienników serwera proxy
Oto zrzut z dziennika rzeczywiste serwera proxy i strona kreatora instalacji, z którym została wykonana (zostały usunięte zduplikowane wpisy do tego samego punktu końcowego). W tej sekcji może służyć jako odwołanie dla własnego dzienników serwera proxy i sieci. Rzeczywiste punktów końcowych, które mogą różnić się w danym środowisku (w szczególności te adresy URL w *italic*).

**Łączenie z usługą Azure AD**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurowanie**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Synchronizacja początkowa**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Błędy uwierzytelniania
W tej sekcji opisano błędy, które mogą być zwracane z biblioteki ADAL (Biblioteka uwierzytelniania używany przez program Azure AD Connect) i programu PowerShell. Błąd wyjaśniono powinien pomóc zrozumiesz, w następnych krokach.

### <a name="invalid-grant"></a>Nieprawidłowy przydział
Nieprawidłowa nazwa użytkownika lub nieprawidłowe hasło. Aby uzyskać więcej informacji, zobacz [nie można zweryfikować hasła](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Typ Nieznany użytkownik
Katalogu usługi Azure AD, nie można odnaleźć lub rozwiązane. Może spróbować zalogować się za pomocą nazwy użytkownika w niezweryfikowanej domeny?

### <a name="user-realm-discovery-failed"></a>Odnajdowanie obszaru użytkownika nie powiodło się
Problemy z konfiguracją sieci lub serwera proxy. Nie można połączyć sieci. Zobacz [Rozwiązywanie problemów z łącznością w Kreatorze instalacji](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Wygasło hasło użytkownika
Wygasły poświadczenia. Zmień hasło.

### <a name="authorization-failure"></a>Błąd autoryzacji
Nie można autoryzować użytkownika do wykonania akcji w usłudze Azure AD.

### <a name="authentication-canceled"></a>Uwierzytelnianie zostało anulowane
Uwierzytelnienia Multi-Factor Authentication (MFA) zostało anulowane.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Połącz MS w trybie Online nie powiodło się
Uwierzytelnianie zakończyło się pomyślnie, ale usługi Azure AD PowerShell ma problem z uwierzytelnianiem.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Rola administratora globalnego usługi Azure AD wymagane
Użytkownik został pomyślnie uwierzytelniony. Jednak użytkownik nie jest przypisana rola administratora globalnego. Jest to [przypisywanie roli administratora globalnego](../users-groups-roles/directory-assign-admin-roles.md) dla użytkownika. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Usługa Privileged Identity Management włączone
Uwierzytelnianie zakończyło się pomyślnie. Usługa Privileged identity management został włączony i użytkownik nie jest administratorem globalnym. Aby uzyskać więcej informacji, zobacz [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informacje o firmie niedostępny
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o firmie z usługi Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informacje o domenie niedostępny
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o domenie z usługi Azure AD.

### <a name="unspecified-authentication-failure"></a>Błąd nieokreślonego uwierzytelniania
Wyświetlane jako nieoczekiwany błąd w Kreatorze instalacji. Może się zdarzyć, Jeśli spróbujesz użyć **Account Microsoft** zamiast **służbowego lub konta organizacji**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Kroki rozwiązywania problemów dla wcześniejszych wersji.
Począwszy od numeru kompilacji 1.1.105.0 (wydane w lutym 2016 r.), Asystenta logowania został wycofany z wersjami. W tej sekcji i konfiguracji nie powinny już być wymagane, ale są przechowywane jako odwołanie.

Pojedynczy znak w Asystencie do pracy musi być skonfigurowana winhttp. Ta konfiguracja może odbywać się przy użyciu [ **netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Asystent logowania nie został poprawnie skonfigurowany
Ten błąd jest wyświetlany, gdy Asystent logowania nie może nawiązać połączenia serwera proxy lub serwera proxy nie zezwala na żądanie.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Jeśli zostanie wyświetlony ten błąd, sprawdź konfigurację serwera proxy w [netsh](how-to-connect-install-prerequisites.md#connectivity) i sprawdź, jest on poprawny.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Jeśli który wydaje się prawidłowe, wykonaj kroki opisane w [Zweryfikuj łączność serwera proxy](#verify-proxy-connectivity) aby zobaczyć, czy problem znajduje się poza także kreatora.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
