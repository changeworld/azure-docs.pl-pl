---
title: 'Azure AD Connect: Rozwiązywanie problemów z łącznością | Dokumentacja firmy Microsoft'
description: Wyjaśniono, jak rozwiązywać problemy z łącznością z programem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c12630deb45fd057537c42157d88fdeef22d18b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593017"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Rozwiązywanie problemów z łącznością z programem Azure AD Connect
W tym artykule opisano, jak działa połączenie między Azure AD Connect i Azure AD i jak rozwiązywać problemy z połączeniem. Te problemy najprawdopodobniej będzie można przejrzeć w środowisku z serwerem proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Rozwiązywanie problemów z łącznością w Kreatorze instalacji
Azure AD Connect jest korzystających z nowoczesnego uwierzytelniania (przy użyciu biblioteki ADAL), do uwierzytelniania. Kreator instalacji i odpowiednie aparatu synchronizacji wymagają machine.config, aby zostać prawidłowo skonfigurowane, ponieważ te dwa aplikacji .NET.

W tym artykule zostanie przedstawiony sposób firmy Fabrikam nawiązuje połączenie z usługą Azure AD przy użyciu jego serwera proxy. Serwer proxy ma nazwę fabrikamproxy i używa portu 8080.

Najpierw musimy upewnij się, że [ **machine.config** ](active-directory-aadconnect-prerequisites.md#connectivity) został poprawnie skonfigurowany.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> W niektórych blogi innych niż Microsoft jest udokumentowany czy zmiany należy wprowadzić miiserver.exe.config zamiast tego. Jednak ten plik jest zastępowany na każdym uaktualnieniu, nawet jeśli działa podczas początkowej instalacji, system przestanie działać na pierwszym uaktualnienia. Dlatego zaleca się zaktualizować machine.config zamiast tego.
>
>

Serwer proxy musi mieć również odpowiednie adresy URL otwarty. Oficjalna lista jest zawarta w [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Tych adresów URL Poniższa tabela jest absolutne minimum systemu od zera, aby można było połączyć się z usługą Azure AD w ogóle. Ta lista nie zawiera żadnych opcjonalne funkcje, takie jak zapisywanie zwrotne haseł lub Azure AD Connect Health. Jest on opisanych tutaj pomagające w rozwiązywaniu problemów ukończenie początkowej konfiguracji.

| Adres URL | Port | Opis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Używany do pobierania listy CRL. |
| \*.verisign.com |HTTP/80 |Używany do pobierania listy CRL. |
| \*.entrust.com |HTTP/80 |Używany do pobierania listy CRL dla usługi MFA. |
| \*.windows.net |HTTPS/443 |Używane do logowania do usługi Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Używany do uwierzytelniania Wieloskładnikowego. |
| \*.microsoftonline.com |HTTPS/443 |Umożliwia skonfigurowanie katalogu usługi Azure AD i importowania/eksportowania danych. |

## <a name="errors-in-the-wizard"></a>Błędy w Kreatorze
Kreator instalacji używa dwóch różnych kontekstach zabezpieczeń. Na stronie **nawiązywanie połączenia z usługą Azure AD**, używane są aktualnie zalogowanego użytkownika. Na stronie **Konfiguruj**, jest zmiana [konta usługi dla aparatu synchronizacji](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Jeśli wystąpi problem, zostanie wyświetlone prawdopodobnie już w **nawiązywanie połączenia z usługą Azure AD** w Kreatorze konfiguracji serwera proxy jest globalny.

Następujące problemy są typowe błędy, które wystąpią w Kreatorze instalacji.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Kreator instalacji nie został prawidłowo skonfigurowany
Ten błąd jest wyświetlany, gdy Kreator nie może połączyć się serwera proxy.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Jeśli zostanie wyświetlony ten błąd, sprawdź [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) została poprawnie skonfigurowana.
* Jeśli wygląda prawidłowe, postępuj zgodnie z instrukcjami [weryfikacji łączności serwera proxy](#verify-proxy-connectivity) aby zobaczyć, czy problem znajduje się poza również kreatora.

### <a name="a-microsoft-account-is-used"></a>Jest używane konto Microsoft
Jeśli używasz **konta Microsoft** zamiast **organizacji lub szkolnymi** konta, zobacz błąd ogólny.  
![Account firmy Microsoft jest używane](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Nie można osiągnąć punktu końcowego usługi MFA
Ten błąd pojawia się, gdy punkt końcowy **https://secure.aadcdn.microsoftonline-p.com** nie można nawiązać połączenia i administratora globalnego jest włączone uwierzytelnianie MFA.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Jeśli zostanie wyświetlony ten błąd, sprawdź, czy punkt końcowy **secure.aadcdn.microsoftonline p.com** został dodany do serwera proxy.

### <a name="the-password-cannot-be-verified"></a>Nie można zweryfikować hasło
Jeśli Kreator instalacji zakończy się pomyślnie w połączeniu z usługą Azure AD, ale nie można zweryfikować samego hasła, że wyświetlenie tego błędu:  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* To hasło tymczasowe hasło i musi zostać zmienione? Jest to rzeczywiście prawidłowe hasło? Spróbuj zalogować się do https://login.microsoftonline.com (na innym komputerze niż serwer Azure AD Connect) i upewnij się, konto jest użyteczne.

### <a name="verify-proxy-connectivity"></a>Sprawdź łączność serwera proxy
Sprawdź, czy serwer Azure AD Connect ma rzeczywistego łączności z serwera Proxy i Internet, użyć niektórych programu PowerShell, aby zobaczyć, czy serwer proxy zezwala na żądania sieci web lub nie. W wierszu programu PowerShell, uruchom `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Jest technicznie pierwsze wywołanie https://login.microsoftonline.com tego identyfikatora URI działa również, ale identyfikator URI jest szybszy w odpowiedzi.)

PowerShell korzysta z konfiguracji w pliku machine.config nawiązać połączenia z serwerem proxy. Ustawienia winhttp/Netsh nie powinny mieć wpływ na te polecenia cmdlet.

Jeśli serwer proxy jest skonfigurowany prawidłowo, należy pobrać stan oznaczający powodzenie: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Jeśli zostanie wyświetlony **nie można nawiązać połączenia z serwerem zdalnym**, spróbuj programu PowerShell do bezpośredniego wywoływania bez użycia serwera proxy lub DNS nie jest poprawnie skonfigurowana. Upewnij się, że **machine.config** pliku jest poprawnie skonfigurowana.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Jeśli serwer proxy nie jest skonfigurowane poprawnie, jest wyświetlany komunikat o błędzie: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Błąd | Tekst błędu | Komentarz |
| --- | --- | --- |
| 403 |Zabroniony |Serwer proxy nie został otwarty dla żądanego adresu URL. Ponownie konfigurację serwera proxy i upewnij się, że [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) został otwarty. |
| 407 |Wymagane jest uwierzytelnianie serwera proxy |Wymagany serwer proxy logowania i żaden nie został podany. Jeśli serwer proxy wymaga uwierzytelnienia, upewnij się, że to ustawienie skonfigurowane w pliku machine.config. Upewnij się, że korzystasz z konta domeny dla użytkownika kreatora i konta usługi. |

### <a name="proxy-idle-timeout-setting"></a>Ustawienie limitu czasu bezczynności serwera proxy
Gdy Azure AD Connect wysyła żądanie eksportu do usługi Azure AD, Azure AD może potrwać do 5 minut do przetwarzania żądania przed wygenerowaniem odpowiedzi. Może to nastąpić, zwłaszcza, jeśli istnieje wiele obiektów grupy z członkostwa w grupach dużych zawarte w tym samym żądaniu eksportu. Upewnij się, że limit czasu bezczynności serwera Proxy jest skonfigurowany tak, aby większa niż 5 minut. W przeciwnym wypadku problem niestabilne połączenie z usługą Azure AD można zaobserwować na serwerze programu Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Wzorzec komunikacji między Azure AD Connect i Azure AD
Jeśli zostały wykonane następujące kroki poprzedniego i nadal nie można połączyć, może w tym momencie uruchomić, analizując dzienniki sieci. W tej sekcji jest dokumentowanie wzorca normalnego i pomyślne łączność. Jest również wyświetlanie listy typowych śledzi czerwony, które może być ignorowane podczas czytania dzienniki sieci.

* Brak wywołania https://dc.services.visualstudio.com. Nie jest wymagane do tego adresu URL otwarte na serwerze proxy dla instalacji powiodło się i można zignorować te wywołania.
* Możesz sprawdzić, czy rozpoznawanie nazw dns Wyświetla rzeczywiste hosty w nsatc.net przestrzeni nazw DNS i innych przestrzeniach nazw pod microsoftonline.com nie. Jednak nie są wszystkie żądania usługi sieci web o nazwach używanego serwera i nie trzeba dodać je do serwera proxy.
* Punkty końcowe adminwebservice i provisioningapi odnajdywania punktów końcowych i użyć w celu znalezienia rzeczywisty punkt końcowy do użycia. Te punkty końcowe są różne w zależności od regionu.

### <a name="reference-proxy-logs"></a>Odwołanie do dzienników serwera proxy
Oto zrzutu z dziennika rzeczywiste serwera proxy i strony Kreatora instalacji, z której wykonano (zostały usunięte w niej zduplikowanych pozycji do tego samego punktu końcowego). W tej sekcji może służyć jako odwołanie dla własnego dzienników serwera proxy i sieci. Rzeczywiste punktów końcowych, które mogą się różnić w danym środowisku (w szczególności tych adresów URL w *kursywą*).

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

**Początkowej synchronizacji**

| Time | Adres URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Błędy uwierzytelniania
W tej sekcji omówiono błędów, które mogą zostać zwrócone z biblioteki ADAL (Biblioteka uwierzytelniania używany przez program Azure AD Connect) i programu PowerShell. Błąd wyjaśniono powinny pomóc w zrozumieć innej.

### <a name="invalid-grant"></a>Nieprawidłowy Grant
Nieprawidłowa nazwa użytkownika lub nieprawidłowe hasło. Aby uzyskać więcej informacji, zobacz [nie można zweryfikować hasło](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Typ nieznanego użytkownika
Katalog usługi Azure AD nie można odnaleźć lub rozwiązany. Może być spróbuj zalogować się za pomocą nazwy użytkownika w niezweryfikowanej domeny?

### <a name="user-realm-discovery-failed"></a>Odnajdowanie obszaru użytkownika nie powiodło się
Problemy z konfiguracją sieci lub serwera proxy. Nie można połączyć się z sieci. Zobacz [Rozwiązywanie problemów z łącznością w Kreatorze instalacji](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Hasło użytkownika wygasło
Twoje poświadczenia wygasły. Zmień hasło.

### <a name="authorizationfailure"></a>AuthorizationFailure
Nieznany problem.

### <a name="authentication-cancelled"></a>Uwierzytelnianie anulowane
Żądanie uwierzytelniania wieloskładnikowego (MFA) zostało anulowane.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Uwierzytelnianie zakończyło się pomyślnie, ale programu Azure AD PowerShell ma problem z uwierzytelnianiem.

### <a name="azurerolemissing"></a>AzureRoleMissing
Uwierzytelnianie zakończyło się pomyślnie. Nie jesteś administratorem globalnym.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Uwierzytelnianie zakończyło się pomyślnie. Zarządzanie tożsamościami uprzywilejowanymi został włączony i obecnie nie jesteś administratorem globalnym. Aby uzyskać więcej informacji, zobacz [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o firmie z usługi Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o domenie z usługi Azure AD.

### <a name="unexpected-exception"></a>Nieoczekiwany wyjątek
Wyświetlany jako wystąpił nieoczekiwany błąd w Kreatorze instalacji. Może wystąpić w przypadku próby użycia **Account Microsoft** zamiast **konto służbowe lub organizacji**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Kroki rozwiązywania problemów z poprzednimi wersjami.
Począwszy od numeru kompilacji 1.1.105.0 (wydane luty 2016), Asystenta logowania w witrynie została wycofana z wersjami. Ta sekcja konfiguracji nie może być wymagane, a jest przechowywana jako odwołanie.

Jednokrotnego w Asystenta do pracy muszą być skonfigurowane winhttp. Tej konfiguracji można wykonać za pomocą [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Asystent logowania w witrynie nie został prawidłowo skonfigurowany
Ten błąd jest wyświetlany, gdy Asystenta logowania w witrynie nie można osiągnąć serwera proxy lub serwera proxy nie zezwala na żądanie.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Jeśli zostanie wyświetlony ten błąd, sprawdź konfiguracji serwera proxy w [netsh](active-directory-aadconnect-prerequisites.md#connectivity) i sprawdź poprawność.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Jeśli wygląda prawidłowe, postępuj zgodnie z instrukcjami [weryfikacji łączności serwera proxy](#verify-proxy-connectivity) aby zobaczyć, czy problem znajduje się poza również kreatora.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
