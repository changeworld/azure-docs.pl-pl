---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z łącznością usługi Azure AD | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak rozwiązywać problemy z łącznością z usługą Azure AD Connect.
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
ms.openlocfilehash: 72dbb404d1b4d3618909e0233f332d2f98b51516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049735"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Rozwiązywanie problemów z łącznością usługi Azure AD
W tym artykule wyjaśniono, jak działa łączność między usługą Azure AD Connect i usługą Azure AD oraz jak rozwiązywać problemy z łącznością. Te problemy są najprawdopodobniej widoczne w środowisku z serwerem proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Rozwiązywanie problemów z łącznością w kreatorze instalacji
Usługa Azure AD Connect używa uwierzytelniania nowoczesnego (przy użyciu biblioteki ADAL) do uwierzytelniania. Kreator instalacji i aparat synchronizacji wymagają, aby plik machine.config był poprawnie skonfigurowany, ponieważ te dwa są aplikacjami .NET.

W tym artykule pokazujemy, jak firma Fabrikam łączy się z usługą Azure AD za pośrednictwem serwera proxy. Serwer proxy nosi nazwę fabrikamproxy i używa portu 8080.

Najpierw musimy upewnić się, że [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) jest poprawnie skonfigurowany.  
![maszynaconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> W niektórych blogach innych firm jest udokumentowane, że zmiany powinny być wprowadzane do miiserver.exe.config zamiast. Jednak ten plik jest zastępowany przy każdym uaktualnieniu, więc nawet jeśli działa podczas początkowej instalacji, system przestaje działać przy pierwszym uaktualnieniu. Z tego powodu zalecenie jest aktualizacja machine.config zamiast.
>
>

Serwer proxy musi również mieć otwarte wymagane adresy URL. Oficjalna lista jest udokumentowana w [adresach URL i zakresach adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Z tych adresów URL poniższa tabela jest absolutnym minimum, aby móc w ogóle łączyć się z usługą Azure AD. Ta lista nie zawiera żadnych funkcji opcjonalnych, takich jak zapisywanie zwrotne hasła lub usługi Azure AD Connect Health. Jest to udokumentowane w tym miejscu, aby pomóc w rozwiązywaniu problemów dla konfiguracji początkowej.

| Adres URL | Port | Opis |
| --- | --- | --- |
| mscrl.microsoft.com |Protokół HTTP/80 |Służy do pobierania list LIST CRL. |
| \*verisign.com . |Protokół HTTP/80 |Służy do pobierania list LIST CRL. |
| \*entrust.net .entrust.net |Protokół HTTP/80 |Służy do pobierania list CRL dla usługi MFA. |
| \*.windows.net |Protokół HTTPS/443 |Służy do logowania się do usługi Azure AD. |
| secure.aadcdn.microsoftonline-p.com |Protokół HTTPS/443 |Używany do usługi MFA. |
| \*.microsoftonline.com |Protokół HTTPS/443 |Służy do konfigurowania katalogu usługi Azure AD i importowania/eksportowania danych. |

## <a name="errors-in-the-wizard"></a>Błędy w kreatorze
Kreator instalacji używa dwóch różnych kontekstów zabezpieczeń. Na stronie **Połącz z usługą Azure AD**używa aktualnie zalogowany użytkownik. Na stronie **Konfiguruj**, zmienia się na [konto z uruchomieniem usługi dla aparatu synchronizacji](reference-connect-accounts-permissions.md#adsync-service-account). Jeśli występuje problem, wydaje się najprawdopodobniej już na stronie **Połącz z usługą Azure AD** w kreatorze, ponieważ konfiguracja serwera proxy jest globalna.

Następujące problemy są najczęstszymi błędami napotykanymi w kreatorze instalacji.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Kreator instalacji nie został poprawnie skonfigurowany
Ten błąd pojawia się, gdy sam kreator nie może dotrzeć do serwera proxy.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Jeśli ten błąd jest widoczny, sprawdź, czy [plik machine.config](how-to-connect-install-prerequisites.md#connectivity) został poprawnie skonfigurowany.
* Jeśli wygląda to poprawnie, wykonaj kroki w [Sprawdź łączność serwera proxy,](#verify-proxy-connectivity) aby sprawdzić, czy problem występuje poza kreatorem, jak również.

### <a name="a-microsoft-account-is-used"></a>Używane jest konto Microsoft
Jeśli używasz **konta Microsoft,** a nie konta **szkolnego lub organizacyjnego,** zostanie wyświetlony błąd ogólny.  
![Konto Microsoft jest używane](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Nie można osiągnąć punktu końcowego usługi MFA
Ten błąd pojawia się, jeśli nie można osiągnąć punktu końcowego, **https://secure.aadcdn.microsoftonline-p.com** a administrator globalny ma włączone uwierzytelnianie wieloskładnikowe.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Jeśli widzisz ten błąd, sprawdź, czy punkt końcowy **secure.aadcdn.microsoftonline-p.com** został dodany do serwera proxy.

### <a name="the-password-cannot-be-verified"></a>Nie można zweryfikować hasła
Jeśli kreator instalacji zakończy się pomyślnie nawiązaniu połączenia z usługą Azure AD, ale nie można zweryfikować samego hasła, zostanie wyświetlony ten błąd:  
![Złe hasło.](./media/tshoot-connect-connectivity/badpassword.png)

* Czy hasło jest hasłom tymczasowym i należy je zmienić? Czy to rzeczywiście poprawne hasło? Spróbuj zalogować `https://login.microsoftonline.com` się do (na innym komputerze niż serwer usługi Azure AD Connect) i sprawdź, czy konto jest użyteczne.

### <a name="verify-proxy-connectivity"></a>Weryfikowanie łączności serwera proxy
Aby sprawdzić, czy serwer usługi Azure AD Connect ma rzeczywistą łączność z serwerem proxy i Internetem, użyj programu PowerShell, aby sprawdzić, czy serwer proxy zezwala na żądania sieci web, czy nie. W wierszu programu PowerShell uruchom program `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Technicznie pierwsze wywołanie `https://login.microsoftonline.com` jest i ten identyfikator URI działa również, ale inne identyfikatory URI jest szybszy do odpowiedzi.)

Program PowerShell używa konfiguracji w pliku machine.config do skontaktowania się z serwerem proxy. Ustawienia w winhttp/netsh nie powinny mieć wpływu na te polecenia cmdlet.

Jeśli serwer proxy jest poprawnie skonfigurowany, powinieneś uzyskać stan sukcesu: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Jeśli zostanie wyświetlony **nie można połączyć się z serwerem zdalnym,** program PowerShell próbuje nawiązać bezpośrednie połączenie bez użycia serwera proxy lub dns nie jest poprawnie skonfigurowany. Upewnij się, że plik **machine.config** jest poprawnie skonfigurowany.
![nie można połączyć](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Jeśli serwer proxy nie jest poprawnie skonfigurowany, pojawia się błąd: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Błąd | Tekst błędu | Komentarz |
| --- | --- | --- |
| 403 |Forbidden |Serwer proxy nie został otwarty dla żądanego adresu URL. Ponownie odwiedź konfigurację serwera proxy i upewnij się, że [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zostały otwarte. |
| 407 |Wymagane uwierzytelnianie serwera proxy |Serwer proxy wymagał logowania i nie podano żadnego. Jeśli serwer proxy wymaga uwierzytelniania, upewnij się, że to ustawienie jest skonfigurowane w pliku machine.config. Upewnij się również, że używasz kont domeny dla użytkownika z uruchomionym kreatorem i dla konta usługi. |

### <a name="proxy-idle-timeout-setting"></a>Ustawienie limitu czasu bezczynnego serwera proxy
Gdy usługa Azure AD Connect wysyła żądanie eksportu do usługi Azure AD, usługa Azure AD może zająć do 5 minut, aby przetworzyć żądanie przed wygenerowaniem odpowiedzi. Może się tak zdarzyć, zwłaszcza jeśli istnieje wiele obiektów grupy z członkostwem w dużych grupach uwzględnionych w tym samym żądaniu eksportu. Upewnij się, że limit czasu bezczynny serwera proxy jest skonfigurowany jako większy niż 5 minut. W przeciwnym razie sporadyczny problem z łącznością z usługą Azure AD może być obserwowany na serwerze usługi Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Wzorzec komunikacji między usługą Azure AD Connect a usługą Azure AD
Jeśli wszystkie te poprzednie kroki i nadal nie można połączyć, może w tym momencie rozpocząć przeglądanie dzienników sieciowych. W tej sekcji jest dokumentowanie normalnego i pomyślnego wzorca łączności. Jest to również lista typowych czerwonych śledzi, które mogą być ignorowane podczas czytania dzienników sieci.

* Są połączenia `https://dc.services.visualstudio.com`do . Nie jest wymagane, aby ten adres URL był otwarty w serwerze proxy, aby instalacja powiodła się i te wywołania mogą być ignorowane.
* Widoczna jest ta rozdzielczość dns zawierająca listę rzeczywistych hostów, które mają znajdować się w obszarze nazw DNS nsatc.net i innych obszarach nazw, które nie są microsoftonline.com. Nie ma jednak żadnych żądań usług sieci web na rzeczywistych nazwach serwerów i nie trzeba dodawać tych adresów URL do serwera proxy.
* Punkty końcowe adminwebservice i provisioningapi są punktami końcowymi odnajdywania i używane do znajdowania rzeczywistego punktu końcowego do użycia. Te punkty końcowe różnią się w zależności od regionu.

### <a name="reference-proxy-logs"></a>Odwoływanie się do dzienników serwera proxy
Oto zrzut z rzeczywistego dziennika serwera proxy i strona kreatora instalacji, z której została podjęta (zduplikowane wpisy do tego samego punktu końcowego zostały usunięte). Ta sekcja może służyć jako punkt odniesienia dla własnego serwera proxy i dzienników sieciowych. Rzeczywiste punkty końcowe mogą być różne w danym środowisku (w szczególności te adresy URL *kursywą).*

**Łączenie z usługą Azure AD**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-kotwica*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfiguruj**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-kotwica*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-kotwica*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-kotwica*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Synchronizacja początkowa**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-kotwica*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-kotwica*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Błędy uwierzytelniania
W tej sekcji opisano błędy, które mogą być zwracane z biblioteki ADAL (biblioteki uwierzytelniania używanej przez usługę Azure AD Connect) i programu PowerShell. Wyjaśniony błąd powinien pomóc w zrozumieniu kolejnych kroków.

### <a name="invalid-grant"></a>Nieprawidłowa dotacja
Nieprawidłowa nazwa użytkownika lub hasło. Aby uzyskać więcej informacji, zobacz [Hasło nie można zweryfikować](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Nieznany typ użytkownika
Nie można odnaleźć ani rozpoznać katalogu usługi Azure AD. Może spróbujesz zalogować się przy użyciu nazwy użytkownika w niezweryfikowanej domenie?

### <a name="user-realm-discovery-failed"></a>Odnajdowanie obszaru użytkownika nie powiodło się
Problemy z konfiguracją sieci lub serwera proxy. Nie można uzyskać dostępu do sieci. Zobacz [Rozwiązywanie problemów z łącznością w kreatorze instalacji](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Hasło użytkownika wygasło
Poświadczenia wygasły. Zmień hasło.

### <a name="authorization-failure"></a>Niepowodzenie autoryzacji
Nie można autoryzować użytkownika do wykonywania akcji w usłudze Azure AD.

### <a name="authentication-canceled"></a>Anulowano uwierzytelnianie
Odwołanie uwierzytelniania wieloskładnikowego (MFA) zostało anulowane.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Połączenie z usługą MS Online nie powiodło się
Uwierzytelnianie zakończyło się pomyślnie, ale usługa Azure AD PowerShell ma problem z uwierzytelnianiem.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Potrzebna rola administratora globalnego usługi Azure AD
Użytkownik został pomyślnie uwierzytelniony. Jednak użytkownik nie jest przypisany do roli administratora globalnego. W ten [sposób można przypisać rolę administratora globalnego](../users-groups-roles/directory-assign-admin-roles.md) do użytkownika. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Włączone zarządzanie tożsamościami uprzywilejowanymi
Uwierzytelnianie zakończyło się pomyślnie. Zarządzanie tożsamościami uprzywilejowanymi zostało włączone i obecnie nie jesteś administratorem globalnym. Aby uzyskać więcej informacji, zobacz [Zarządzanie tożsamościami uprzywilejowanymi](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informacje o firmie niedostępne
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o firmie z usługi Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informacje o domenie niedostępne
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o domenie z usługi Azure AD.

### <a name="unspecified-authentication-failure"></a>Nieokreślony błąd uwierzytelniania
Wyświetlany jako nieoczekiwany błąd w kreatorze instalacji. Może się zdarzyć, jeśli spróbujesz użyć **konta Microsoft,** a nie **konta szkolnego lub organizacyjnego**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Kroki rozwiązywania problemów z poprzednimi wersjami.
W wersji rozpoczynającej się od numeru kompilacji 1.1.105.0 (wydanej w lutym 2016 r.) asystent logowania został wycofany. Ta sekcja i konfiguracja nie powinny być już wymagane, ale jest zachowywana jako odwołanie.

Aby asystent logowania jednokrotnego działał, należy skonfigurować winhttp. Tę konfigurację można wykonać za pomocą [**programu netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![Netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Asystent logowania nie został poprawnie skonfigurowany
Ten błąd pojawia się, gdy asystent logowania nie może dotrzeć do serwera proxy lub serwer proxy nie zezwala na żądanie.
![nonetsh ( nonetsh )](./media/tshoot-connect-connectivity/nonetsh.png)

* Jeśli widzisz ten błąd, spójrz na konfigurację serwera proxy w [netsh](how-to-connect-install-prerequisites.md#connectivity) i sprawdź, czy jest poprawna.
  ![netshshow (netshshow)](./media/tshoot-connect-connectivity/netshshow.png)
* Jeśli wygląda to poprawnie, wykonaj kroki w [Sprawdź łączność serwera proxy,](#verify-proxy-connectivity) aby sprawdzić, czy problem występuje poza kreatorem, jak również.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
