---
title: Rozwiązywanie problemów z lokalną ochroną hasłem usługi Azure AD
description: Dowiedz się, jak rozwiązywać problemy z ochroną hasłem usługi Azure AD w lokalnym środowisku usług domenowych Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263649"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Rozwiązywanie problemów: lokalna ochrona hasłem usługi Azure AD

Po wdrożeniu usługi Azure AD Password Protection może być wymagane rozwiązywanie problemów. W tym artykule opisano szczegółowo, aby ułatwić zrozumienie niektórych typowych kroków rozwiązywania problemów.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent kontrolera domeny nie może zlokalizować serwera proxy w katalogu

Głównym objawem tego problemu jest zdarzenia 30017 w dzienniku zdarzeń administratora agenta kontrolera domeny.

Zwykle przyczyną tego problemu jest to, że serwer proxy nie został jeszcze zarejestrowany. Jeśli serwer proxy został zarejestrowany, może wystąpić pewne opóźnienie z powodu opóźnienia replikacji usługi AD, dopóki określony agent kontrolera domeny nie będzie mógł zobaczyć tego serwera proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent kontrolera domeny nie może komunikować się z serwerem proxy

Głównym objawem tego problemu jest 30018 zdarzenia w dzienniku zdarzeń administratora agenta kontrolera domeny. Ten problem może mieć kilka możliwych przyczyn:

1. Agent kontrolera domeny znajduje się w odizolowanej części sieci, która nie zezwala na łączność sieciową z zarejestrowanymi serwerami proxy. Ten problem może być łagodny, o ile inni agenci kontrolera domeny mogą komunikować się z serwerami proxy w celu pobrania zasad haseł z platformy Azure. Po pobraniu te zasady zostaną uzyskane przez izolowany kontroler domeny za pomocą replikacji plików zasad w udziale sysvol.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego mapera punktu końcowego RPC (port 135)

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę przychodzącą Zapory systemu Windows, która umożliwia dostęp do portu 135. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci kontrolera domeny nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana Zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować tę zaporę, aby zezwoliła na dostęp do portu 135.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego RPC (dynamicznego lub statycznego) słuchany przez usługę proxy

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę przychodzącą Zapory systemu Windows, która umożliwia dostęp do wszystkich portów przychodzących nasłuchiwane przez usługę serwera proxy ochrony hasłem usługi Azure AD. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci kontrolera domeny nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana Zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować tę zaporę, aby zezwoliła na dostęp do wszystkich portów przychodzących nasłuchiwała usługa serwera proxy ochrony haseł usługi Azure AD. Ta konfiguracja może być bardziej szczegółowa, jeśli usługa proxy została skonfigurowana do `Set-AzureADPasswordProtectionProxyConfiguration` nasłuchiwania na określonym statycznym porcie RPC (przy użyciu polecenia cmdlet).

1. Komputer hosta serwera proxy nie jest skonfigurowany tak, aby umożliwić kontrolerom domeny logowanie się do komputera. To zachowanie jest kontrolowane za pomocą przypisania uprawnień użytkownika "Dostęp do tego komputera z sieci". Wszystkie kontrolery domeny we wszystkich domenach w lesie muszą mieć to uprawnienie. To ustawienie jest często ograniczone w ramach większego wysiłku hartowania sieci.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Usługa proxy nie może komunikować się z platformą Azure

1. Upewnij się, że komputer proxy ma łączność z punktami końcowymi wymienionymi w [wymaganiach dotyczących wdrażania](howto-password-ban-bad-on-premises-deploy.md).

1. Upewnij się, że las i wszystkie serwery proxy są zarejestrowane dla tej samej dzierżawy platformy Azure.

   Można sprawdzić to wymaganie, uruchamiając `Get-AzureADPasswordProtectionProxy` polecenia cmdlet i `Get-AzureADPasswordProtectionDCAgent` programu PowerShell, a następnie porównać `AzureTenant` właściwość każdego zwróconego towaru. W przypadku poprawnej operacji zgłoszona nazwa dzierżawy musi być taka sama dla wszystkich agentów kontrolera domeny i serwerów proxy.

   Jeśli istnieje warunek niezgodności rejestracji dzierżawy platformy Azure, ten `Register-AzureADPasswordProtectionProxy` problem `Register-AzureADPasswordProtectionForest` można rozwiązać, uruchamiając polecenia cmdlet i/lub programu PowerShell w razie potrzeby, upewniając się, że używa poświadczeń z tej samej dzierżawy platformy Azure dla wszystkich rejestracji.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent kontrolera domeny nie może szyfrować ani odszyfrowywać plików zasad haseł

Usługa Azure AD Password Protection ma krytyczną zależność od funkcji szyfrowania i odszyfrowywania dostarczonych przez usługę dystrybucji kluczy firmy Microsoft. Błędy szyfrowania lub odszyfrowywania mogą objawiać się różnymi objawami i mieć kilka potencjalnych przyczyn.

1. Upewnij się, że usługa KDS jest włączona i funkcjonalna na wszystkich kontrolerach domeny systemu Windows Server 2012 i nowszych w domenie.

   Domyślnie tryb uruchamiania usługi KDS jest skonfigurowany jako ręczny (wyzwalacz startowy). Ta konfiguracja oznacza, że przy pierwszym uruchomieniu usługi przez klienta jest uruchamiana na żądanie. Ten domyślny tryb uruchamiania usługi jest dopuszczalne dla usługi Azure AD Password Protection do pracy.

   Jeśli tryb uruchamiania usługi KDS został skonfigurowany jako Wyłączony, ta konfiguracja musi zostać naprawiona, zanim usługa Azure AD Password Protection będzie działać poprawnie.

   Prostym testem tego problemu jest ręczne uruchomienie usługi KDS za pośrednictwem konsoli programu MMC do zarządzania usługami lub przy użyciu innych narzędzi do zarządzania (na przykład uruchom polecenie "net start kdssvc" z konsoli wiersza polecenia). Oczekuje się, że usługa KDS rozpocznie się pomyślnie i pozostanie uruchomiona.

   Najczęstszą główną przyczyną niemożliwości uruchomienia usługi KDS jest to, że obiekt kontrolera domeny usługi Active Directory znajduje się poza domyślną ou administratorem kontrolerów domeny. Ta konfiguracja nie jest obsługiwana przez usługę KDS i nie jest ograniczeniem nałożonym przez usługę Azure AD Password Protection. Rozwiązaniem tego warunku jest przeniesienie obiektu kontrolera domeny do lokalizacji w ramach domyślnej ou administratora kontrolerów domeny.

1. Niezgodna zmiana formatu buforu szyfrowanego KDS z systemu Windows Server 2012 R2 na Windows Server 2016

   Poprawka zabezpieczeń KDS została wprowadzona w systemie Windows Server 2016, która modyfikuje format buforów szyfrowanych KDS; te bufory czasami nie można odszyfrować w systemach Windows Server 2012 i Windows Server 2012 R2. Kierunek odwrotny jest w porządku — bufory szyfrowane przez KDS w systemach Windows Server 2012 i Windows Server 2012 R2 zawsze będą pomyślnie odszyfrowywane w systemie Windows Server 2016 i nowszych. Jeśli kontrolery domeny w domenach usługi Active Directory są uruchomione w połączeniu z tymi systemami operacyjnymi, mogą być zgłaszane sporadyczne błędy odszyfrowywania usługi Azure AD Password Protection. Nie jest możliwe dokładne przewidzenie czasu lub symptomów tych błędów, biorąc pod uwagę charakter poprawki zabezpieczeń i biorąc pod uwagę, że nie jest deterministyczny, który agent kontrolera kontrolera kontrolera azure ad protection, na którym kontroler domeny będzie szyfrować dane w danym czasie.

   Firma Microsoft bada poprawkę tego problemu, ale nie jest jeszcze dostępna eta. W międzyczasie nie ma obejścia tego problemu, poza nie uruchamianiem kombinacji tych niezgodnych systemów operacyjnych w domenach usługi Active Directory. Innymi słowy należy uruchamiać tylko kontrolery domeny systemu Windows Server 2012 i Windows Server 2012 R2, a także uruchamiać tylko kontrolery domeny systemu Windows Server 2016 i powyżej.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Słabe hasła są akceptowane, ale nie powinny być

Ten problem może mieć kilka przyczyn.

1. Agent(y) kontrolera domeny są uruchomione publicznej wersji oprogramowania w wersji zapoznawczej, która wygasła. Zobacz [Oprogramowanie agenta kontrolera domeny w publicznej wersji zapoznawczej wygasło](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Agent(y) kontrolera domeny nie może pobrać zasad lub nie może odszyfrować istniejących zasad. Sprawdź możliwe przyczyny w powyższych tematach.

1. Zasady haseł Tryb wymuszania jest nadal ustawiony na Inspekcja. Jeśli ta konfiguracja obowiązuje, należy ponownie skonfigurować ją do wymuszania przy użyciu portalu ochrony hasłem usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tryby pracy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Zasady haseł zostały wyłączone. Jeśli ta konfiguracja obowiązuje, należy ponownie skonfigurować ją do włączenia przy użyciu portalu ochrony hasłem usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tryby pracy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Oprogramowanie agenta kontrolera domeny nie zostało zainstalowane na wszystkich kontrolerach domeny w domenie. W tej sytuacji trudno jest upewnić się, że zdalni klienci systemu Windows są kierowane na określony kontroler domeny podczas operacji zmiany hasła. Jeśli uważasz, że pomyślnie ukierunkowane na określonego kontrolera domeny, gdzie jest zainstalowane oprogramowanie agenta kontrolera domeny, można sprawdzić przez podwójne sprawdzenie dziennika zdarzeń administratora agenta kontrolera domeny: niezależnie od wyniku, będzie co najmniej jedno zdarzenie, aby udokumentować wynik hasła Sprawdzania poprawności. Jeśli nie ma zdarzenia dla użytkownika, którego hasło zostało zmienione, zmiana hasła prawdopodobnie została przetworzona przez inny kontroler domeny.

   Jako alternatywny test spróbuj zmienić hasła podczas logowania bezpośrednio do kontrolera domeny, w którym jest zainstalowane oprogramowanie agenta kontrolera domeny. Ta technika nie jest zalecana w przypadku produkcyjnych domen usługi Active Directory.

   Podczas gdy przyrostowe wdrażanie oprogramowania agenta kontrolera domeny jest obsługiwane z zastrzeżeniem tych ograniczeń, firma Microsoft zdecydowanie zaleca, aby oprogramowanie agenta kontrolera domeny było instalowane na wszystkich kontrolerach domeny w domenie tak szybko, jak to możliwe.

1. Algorytm sprawdzania poprawności hasła może rzeczywiście działać zgodnie z oczekiwaniami. Zobacz [Jak oceniane są hasła](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe nie może ustawić słabe hasło DSRM

Usługa Active Directory zawsze sprawdza poprawność nowego hasła trybu naprawy usług katalogowych, aby upewnić się, że spełnia wymagania dotyczące złożoności haseł domeny; ta sprawdzanie poprawności wywołuje również biblioteki dll filtrowania haseł, takie jak ochrona hasłem usługi Azure AD. Jeśli nowe hasło DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Gdy usługa Azure AD Password Protection rejestruje zdarzenia dziennika sprawdzania poprawności hasła dla hasła DSRM usługi Active Directory, oczekuje się, że komunikaty dziennika zdarzeń nie będą zawierać nazwy użytkownika. To zachowanie występuje, ponieważ konto DSRM jest kontem lokalnym, które nie jest częścią rzeczywistej domeny usługi Active Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Promocja repliki kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła DSRM

Podczas procesu promocji kontrolera domeny nowe hasło trybu naprawy usług katalogowych zostanie przesłane do istniejącego kontrolera domeny w domenie w celu weryfikacji. Jeśli nowe hasło DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Podobnie jak w powyższym problemie, każde zdarzenie wyniku sprawdzania poprawności hasła usługi Azure AD Password Protection będzie miało puste nazwy użytkowników w tym scenariuszu.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Obniżenie poziomu kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła lokalnego administratora

Jest obsługiwany do obniżenia poziomu kontrolera domeny, który nadal jest uruchomiony oprogramowanie agenta kontrolera domeny. Administratorzy powinni jednak pamiętać, że oprogramowanie agenta kontrolera domeny nadal wymusza bieżące zasady haseł podczas procedury obniżania poziomu. Nowe hasło lokalnego konta administratora (określone jako część operacji obniżania poziomu) jest sprawdzane jak każde inne hasło. Firma Microsoft zaleca wybranie bezpiecznych haseł dla lokalnych kont administratora w ramach procedury obniżania poziomu kontrolera domeny.

Po pomyślnym obniżeniu, a kontroler domeny został ponownie uruchomiony i ponownie działa jako normalny serwer członkowski, oprogramowanie agenta kontrolera domeny powraca do pracy w trybie pasywnym. Następnie można go odinstalować w dowolnym momencie.

## <a name="booting-into-directory-services-repair-mode"></a>Uruchamianie w trybie naprawy usług katalogowych

Jeśli kontroler domeny zostanie uruchomiony w trybie naprawy usług katalogowych, biblioteka dll filtru haseł agenta kontrolera domeny wykryje ten warunek i spowoduje wyłączenie wszystkich działań związanych z sprawdzaniem poprawności hasła lub wymuszania hasła, niezależnie od aktualnie aktywnej zasady Konfiguracji. Biblioteka dll filtru haseł agenta kontrolera domeny rejestruje zdarzenie ostrzegawcze 10023 w dzienniku zdarzeń administratora, na przykład:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Oprogramowanie agenta publicznego podglądu kontrolera domeny wygasło

Podczas publicznego okresu podglądu ochrony haseł usługi Azure AD oprogramowanie agenta kontrolera domeny zostało zakodowane na czas, aby zatrzymać przetwarzanie żądań sprawdzania poprawności hasła w następujących terminach:

* Wersja 1.2.65.0 przestanie przetwarzać żądania sprawdzania poprawności hasła 1 września 2019 r.
* Wersja 1.2.25.0 i wcześniej wstrzymane przetwarzanie żądań sprawdzania poprawności hasła w dniu 1 lipca 2019 r.

W miarę zbliżania się terminu wszystkie ograniczone w czasie wersje agenta kontrolera domeny będą emitować zdarzenie 10021 w dzienniku zdarzeń administratora agenta kontrolera domeny w czasie rozruchu, który wygląda następująco:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Po upływie terminu wszystkie ograniczone w czasie wersje agenta kontrolera domeny będą emitować zdarzenie 10022 w dzienniku zdarzeń administratora agenta kontrolera domeny w czasie rozruchu, który wygląda następująco:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Ponieważ ostateczny termin jest sprawdzany tylko przy początkowym rozruchu, te wydarzenia mogą być widoczne dopiero długo po upływie terminu kalendarza. Po rozpoznaniu ostatecznego terminu nie zostaną automatycznie zatwierdzone żadne negatywne skutki dla kontrolera domeny lub większego środowiska innego niż wszystkie hasła.

> [!IMPORTANT]
> Firma Microsoft zaleca, aby wygasłe publiczne agenty kontrolera domeny w wersji zapoznanej były natychmiast uaktualniane do najnowszej wersji.

Łatwym sposobem wykrywania agentów kontrolera domeny w twoim `Get-AzureADPasswordProtectionDCAgent` środowisku, które muszą zostać uaktualnione, jest uruchomienie polecenia cmdlet, na przykład:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

W tym temacie pole SoftwareVersion jest oczywiście kluczową właściwością, na która należy zwrócić uwagę. Za pomocą filtrowania programu PowerShell można również odfiltrować agentów kontrolera domeny, które znajdują się już na poziomie lub powyżej wymaganej wersji bazowej, na przykład:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Oprogramowanie serwera proxy ochrony hasłem usługi Azure AD nie jest ograniczone w czasie w żadnej wersji. Firma Microsoft nadal zaleca uaktualnienie agentów kontrolera domeny i serwera proxy do najnowszych wersji w miarę ich zwalniania. Polecenie `Get-AzureADPasswordProtectionProxy` cmdlet może służyć do znajdowania agentów proxy, które wymagają uaktualnień, podobnie jak w powyższym przykładzie dla agentów kontrolera domeny.

Aby uzyskać więcej informacji na temat określonych procedur uaktualniania, należy zapoznać się [z uaktualnianiem agenta kontrolera domeny](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) i [uaktualnieniem usługi proxy.](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)

## <a name="emergency-remediation"></a>Rekultywacja w nagłych wypadkach

Jeśli wystąpi sytuacja, w której usługa agenta kontrolera domeny powoduje problemy, usługa agenta kontrolera domeny może zostać natychmiast zamknięta. Biblioteka dll filtru haseł agenta kontrolera domeny nadal próbuje wywołać nieubiegającą się usługę i rejestruje zdarzenia ostrzegawcze (10012, 10013), ale wszystkie hasła przychodzące są akceptowane w tym czasie. Usługa agenta kontrolera domeny może być również skonfigurowana za pośrednictwem Menedżera sterowania usługami systemu Windows z typem uruchamiania "Wyłączone" w razie potrzeby.

Innym środkiem korygowania byłoby ustawienie trybu Włącz na Nie w portalu ochrony hasłem usługi Azure AD. Po pobraniu zaktualizowanych zasad każda usługa agenta kontrolera domeny przejdzie w tryb spoczynkowy, w którym wszystkie hasła są akceptowane w stanie rzeczywistym. Aby uzyskać więcej informacji, zobacz [Tryby pracy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Usuwania

Jeśli postanowiono odinstalować oprogramowanie ochrony hasłem usługi Azure AD i oczyścić wszystkie powiązane stany z domen i lasu, to zadanie można wykonać za pomocą następujących kroków:

> [!IMPORTANT]
> Ważne jest, aby wykonać te kroki w kolejności. Jeśli którekolwiek wystąpienie usługi Proxy pozostanie uruchomione, okresowo będzie ponownie tworzyć obiekt serviceConnectionPoint. Jeśli jakiekolwiek wystąpienie usługi agenta kontrolera domeny pozostanie uruchomione, okresowo będzie ponownie tworzyć jego serviceConnectionPoint obiektu i stan sysvol.

1. Odinstaluj oprogramowanie proxy ze wszystkich komputerów. Ten krok **nie** wymaga ponownego uruchomienia komputera.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. Ten krok **wymaga** ponownego uruchomienia komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi proxy w każdym kontekście nazewnictwa domen. Lokalizację tych obiektów można odnajdować za pomocą następującego polecenia programu PowerShell w usłudze Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pomijaj gwiazdki ("*") na końcu $keywords wartości zmiennej.

   Wynikowe obiekty znalezione za pomocą `Get-ADObject` polecenia można następnie `Remove-ADObject`przekierowywać lub usuwać ręcznie.

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekście nazewnictwa domen. W zależności od tego, jak szeroko wdrożono oprogramowanie, może istnieć jeden z tych obiektów na kontroler domeny. Lokalizację tego obiektu można odnajdować za pomocą następującego polecenia programu PowerShell w usłudze Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Wynikowe obiekty znalezione za pomocą `Get-ADObject` polecenia można następnie `Remove-ADObject`przekierowywać lub usuwać ręcznie.

   Nie pomijaj gwiazdki ("*") na końcu $keywords wartości zmiennej.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywany w kontenerze w kontekście nazewnictwa konfiguracji usługi Active Directory. Można go odkryć i usunąć w następujący sposób:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ręcznie usuń cały stan związany z sysvol, ręcznie usuwając następujący folder i całą jego zawartość:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   W razie potrzeby ta ścieżka może być również dostępna lokalnie na danym kontrolerze domeny; domyślna lokalizacja będzie wyglądać następująco:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ta ścieżka jest inna, jeśli udział sysvol został skonfigurowany w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Następne kroki

[Często zadawane pytania dotyczące ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-faq.md)

Aby uzyskać więcej informacji na temat globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zakaz złych haseł](concept-password-ban-bad.md)
