---
title: Rozwiązywanie problemów z ochroną hasłem w usłudze Azure AD — Azure Active Directory
description: Opis typowego rozwiązywania problemów z ochroną hasłem w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d96f5bb189dfd20c65fc6fc6ddcb8fff66d52ff
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666232"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Rozwiązywanie problemów z ochroną hasłem usługi Azure AD

Po wdrożeniu ochrony hasłem usługi Azure AD może być wymagane rozwiązywanie problemów. W tym artykule szczegółowo przedstawiono kilka typowych kroków rozwiązywania problemów.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent DC nie może zlokalizować serwera proxy w katalogu

Głównym objawem tego problemu jest 30017 zdarzeń w dzienniku zdarzeń administratora agenta kontrolera domeny.

Zwykle przyczyną tego problemu jest to, że serwer proxy nie został jeszcze zarejestrowany. Jeśli serwer proxy został zarejestrowany, może wystąpić pewne opóźnienie wynikające z opóźnienia replikacji usługi AD do momentu, gdy określony Agent kontrolera domeny będzie mógł zobaczyć ten serwer proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent DC nie może komunikować się z serwerem proxy

Głównym objawem tego problemu jest 30018 zdarzeń w dzienniku zdarzeń administratora agenta kontrolera domeny. Ten problem może mieć kilka możliwych przyczyn:

1. Agent DC znajduje się w izolowanej części sieci, która nie zezwala na łączność sieciową z zarejestrowanymi serwerem proxy. W związku z tym problem może być niegroźny, o ile inni agenci DC mogą komunikować się z proxy, aby pobierać zasady haseł z platformy Azure, które zostaną następnie uzyskane przez izolowany kontroler domeny za pośrednictwem replikacji plików zasad w udziale Sysvol.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego mapowania punktów końcowych RPC (port 135)

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę ruchu przychodzącego zapory systemu Windows, która umożliwia dostęp do portu 135. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci DC nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować zaporę w taki sposób, aby zezwalała na dostęp do portu 135.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego RPC (dynamiczny lub statyczny), który jest wysłuchiwany przez usługę serwera proxy

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę ruchu przychodzącego zapory systemu Windows, która umożliwia dostęp do wszystkich portów przychodzących nasłuchiwania przez usługę serwera proxy ochrony hasłem usługi Azure AD. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci DC nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować zaporę tak, aby zezwalała na dostęp do wszystkich portów przychodzących nasłuchiwanych przez usługę serwera proxy ochrony hasłem usługi Azure AD. Ta konfiguracja może być bardziej szczegółowa, jeśli usługa serwera proxy została skonfigurowana do nasłuchiwania na określonym statycznym porcie RPC (za `Set-AzureADPasswordProtectionProxyConfiguration` pomocą polecenia cmdlet).

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Usługa serwera proxy nie może komunikować się z platformą Azure

1. Upewnij się, że komputer proxy ma połączenie z punktami końcowymi wymienionymi w [wymaganiach dotyczących wdrażania](howto-password-ban-bad-on-premises-deploy.md).

1. Upewnij się, że las i wszystkie serwery proxy są zarejestrowane w ramach tej samej dzierżawy platformy Azure.

   To wymaganie można sprawdzić, uruchamiając `Get-AzureADPasswordProtectionProxy` polecenia `Get-AzureADPasswordProtectionDCAgent` cmdlet i programu PowerShell, a następnie `AzureTenant` porównując właściwość każdego zwróconego elementu. W celu poprawnej operacji zgłoszona nazwa dzierżawy musi być taka sama dla wszystkich agentów kontrolerów domeny i serwerów proxy.

   Jeśli istnieje warunek niezgodności rejestracji dzierżawy platformy Azure, ten problem może zostać rozwiązany przez `Register-AzureADPasswordProtectionProxy` uruchomienie poleceń cmdlet `Register-AzureADPasswordProtectionForest` i/lub programu PowerShell zgodnie z wymaganiami, co pozwala upewnić się, że do wszystkich rejestracji są używane poświadczenia z tej samej dzierżawy platformy Azure.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent DC nie może zaszyfrować lub odszyfrować plików zasad haseł

Ten problem może prowadzić do manifestu z różnymi objawami, ale zazwyczaj ma wspólną główną przyczynę.

Ochrona hasłem w usłudze Azure AD ma krytyczne znaczenie dla funkcji szyfrowania i odszyfrowywania dostarczonych przez usługę dystrybucji kluczy firmy Microsoft, która jest dostępna na kontrolerach domeny z systemem Windows Server 2012 lub nowszym. Usługa KDS musi być włączona i działać na wszystkich kontrolerach domeny z systemem Windows Server 2012 lub nowszym w domenie.

Domyślnie tryb uruchamiania usługi KDS jest konfigurowany jako ręczny (wyzwalacz uruchomienia). Ta konfiguracja oznacza, że po raz pierwszy klient próbuje użyć usługi, jest uruchamiana na żądanie. Ten domyślny tryb uruchamiania usługi jest akceptowalny do działania ochrony hasłem usługi Azure AD.

Jeśli tryb uruchamiania usługi KDS został skonfigurowany jako wyłączony, ta konfiguracja musi zostać rozwiązana, zanim Ochrona hasłem usługi Azure AD będzie działać prawidłowo.

Prostym testem tego problemu jest ręczne uruchomienie usługi KDS przy użyciu konsoli MMC zarządzania usługami lub innych narzędzi do zarządzania (na przykład uruchom polecenie "net start kdssvc" z konsoli wiersza polecenia). Usługa KDS powinna zostać uruchomiona pomyślnie i nadal działać.

Najbardziej powszechną przyczyną głównej przyczyny uruchomienia usługi KDS jest to, że obiekt kontrolera domeny Active Directory znajduje się poza domyślną jednostką organizacyjną Kontrolery domeny. Ta konfiguracja nie jest obsługiwana przez usługę KDS i nie jest ograniczeniem narzuconym przez ochronę hasłem usługi Azure AD. Rozwiązaniem tego problemu jest przeniesienie obiektu kontrolera domeny do lokalizacji w ramach domyślnej jednostki organizacyjnej Kontrolery domeny.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Hasła słabe są akceptowane, ale nie powinny być

Ten problem może mieć kilka przyczyn.

1. Agenci kontrolera domeny nie mogą pobrać zasad lub nie mogą odszyfrować istniejących zasad. Sprawdź możliwe przyczyny w powyższych tematach.

1. Tryb wymuszania zasad haseł nadal jest ustawiony na inspekcję. Jeśli ta konfiguracja jest w tej sytuacji, skonfiguruj ją ponownie, aby wymusić korzystanie z portalu ochrony hasłem usługi Azure AD. Zobacz [Włączanie ochrony hasłem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Zasady dotyczące haseł zostały wyłączone. Jeśli ta konfiguracja jest aktywna, skonfiguruj ją ponownie, aby była dostępna przy użyciu portalu ochrony hasłem usługi Azure AD. Zobacz [Włączanie ochrony hasłem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Nie zainstalowano oprogramowania agenta kontrolera domeny na wszystkich kontrolerach domeny w domenie. W takiej sytuacji trudno jest zapewnić, że zdalni klienci systemu Windows będą kierowani do określonego kontrolera domeny podczas operacji zmiany hasła. Jeśli uważasz, że pomyślnie określono konkretnego kontrolera domeny, na którym jest zainstalowane oprogramowanie agenta DC, możesz sprawdzić przez podwójne sprawdzenie, czy dziennik zdarzeń administratora agenta kontrolera domeny: bez względu na wynik, aby udokumentować wynik hasła, będzie co najmniej jedno zdarzenie. zatwierdzenia. Jeśli nie ma żadnego zdarzenia dla użytkownika, którego hasło zostało zmienione, zmiana hasła prawdopodobnie została przetworzona przez inny kontroler domeny.

   Alternatywny Test polega na tym, że podczas logowania bezpośrednio do kontrolera domeny, na którym jest zainstalowane oprogramowanie Agent DC, należy setting\changing hasła. Ta technika nie jest zalecana w przypadku domen Active Directory produkcyjnych.

   Podczas przyrostowego wdrażania oprogramowania agenta kontrolera domeny jest obsługiwane z uwzględnieniem tych ograniczeń, firma Microsoft zdecydowanie zaleca, aby oprogramowanie Agent DC zostało zainstalowane na wszystkich kontrolerach domeny w domenie najszybciej, jak to możliwe.

1. Algorytm weryfikacji hasła może faktycznie działać zgodnie z oczekiwaniami. Zobacz [jak oceniane są hasła](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Narzędzie Ntdsutil. exe nie może ustawić słabego hasła DSRM

Active Directory zawsze zweryfikuje nowe hasło trybu naprawy usług katalogowych, aby upewnić się, że spełnia wymagania dotyczące złożoności hasła domeny; to sprawdzanie poprawności wywołuje również metody filtrowania hasła, takie jak ochrona hasłem usługi Azure AD. Jeśli nowe hasło trybu DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

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

Gdy ochrona hasłem w usłudze Azure AD rejestruje zdarzenia w dzienniku zdarzeń weryfikacji hasła dla hasła Active Directory DSRM, oczekuje się, że komunikaty dziennika zdarzeń nie będą zawierać nazwy użytkownika. Dzieje się tak, ponieważ konto trybu DSRM jest kontem lokalnym, które nie jest częścią rzeczywistej domeny Active Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Podwyższanie poziomu repliki kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła DSRM

W procesie podwyższania poziomu kontrolera domeny nowe hasło trybu naprawy usług katalogowych zostanie przesłane do istniejącego kontrolera domeny w domenie w celu weryfikacji. Jeśli nowe hasło trybu DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Podobnie jak w przypadku powyższego problemu, wszystkie zdarzenia weryfikacji hasła ochrony hasła usługi Azure AD będą mieć pustą nazwę użytkownika w tym scenariuszu.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Obniżanie poziomu kontrolera domeny nie powiodło się z powodu słabego hasła administratora lokalnego

Jest ona obsługiwana, aby obniżyć poziom kontrolera domeny, w którym nadal działa oprogramowanie agenta kontrolera sieci. Administratorzy powinni mieć jednak świadomość, że oprogramowanie agenta DC nadal wymusza bieżące zasady haseł podczas procedury obniżania poziomu. Nowe hasło do konta administratora lokalnego (określone jako część operacji obniżania poziomu) jest weryfikowane jak każde inne hasło. Firma Microsoft zaleca, aby w ramach procedury obniżania poziomu kontrolera domeny wybierać bezpieczne hasła dla kont administratorów lokalnych.

Po pomyślnym obniżeniu poziomu i ponownym uruchomieniu kontrolera domeny i ponownym uruchomieniu programu jako normalny serwer członkowski oprogramowanie Agent DC zostanie przywrócone do trybu pasywnego. Może on zostać odinstalowany w dowolnym momencie.

## <a name="booting-into-directory-services-repair-mode"></a>Rozruch w trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchamiany w trybie naprawy usług katalogowych, usługa agenta kontrolera domeny wykrywa ten warunek i spowoduje wyłączenie wszystkich działań weryfikacji hasła lub wymuszania, niezależnie od obecnie aktywnej konfiguracji zasad.

## <a name="emergency-remediation"></a>Korygowanie awaryjne

Jeśli wystąpi sytuacja, w której usługa agenta kontrolera domeny powoduje problemy, usługa agenta kontrolera domeny może zostać natychmiast wyłączona. Biblioteka DLL filtru haseł agenta DC nadal próbuje wywołać niedziałającą usługę i będzie rejestrować zdarzenia ostrzegawcze (10012, 10013), ale w tym czasie wszystkie hasła przychodzące są akceptowane. Usługę agenta kontrolera domeny można również skonfigurować za pomocą Menedżera kontroli usług systemu Windows z typem uruchamiania "wyłączone" zgodnie z wymaganiami.

Kolejną środkiem naprawczym byłoby ustawienie trybu włączania na wartość nie w portalu ochrony hasłem usługi Azure AD. Po pobraniu zaktualizowanych zasad każda usługa agenta kontrolera domeny przejdzie w tryb quiescent, w którym wszystkie hasła są akceptowane jako-is. Aby uzyskać więcej informacji, zobacz [tryb](howto-password-ban-bad-on-premises-operations.md#enforce-mode)wymuszania.

## <a name="removal"></a>Usunięcie

Jeśli podjęto decyzję o odinstalowaniu oprogramowania ochrony hasłem usługi Azure AD i oczyszczeniu wszystkich powiązanych Stanów z domen i lasów, to zadanie można wykonać, wykonując następujące czynności:

> [!IMPORTANT]
> Ważne jest, aby wykonać te kroki w kolejności. Jeśli jakiekolwiek wystąpienie usługi proxy zostanie uruchomione, spowoduje to ponowne utworzenie obiektu serviceConnectionPoint. Jeśli jakiekolwiek wystąpienie usługi agenta kontrolera domeny zostanie uruchomione, będzie okresowo utworzyć ponownie obiekt serviceConnectionPoint i stan SYSVOL.

1. Odinstaluj oprogramowanie serwera proxy ze wszystkich komputerów. Ten krok nie **wymaga ponownego** uruchomienia.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. Ten krok **wymaga** ponownego uruchomienia.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera proxy w każdym kontekście nazewnictwa domen. Lokalizację tych obiektów można odnaleźć za pomocą następującego polecenia Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pomijaj gwiazdki ("*") na końcu wartości zmiennej $keywords.

   Obiekty uzyskane za pośrednictwem `Get-ADObject` polecenia mogą być następnie potoku `Remove-ADObject`lub usunięte ręcznie.

4. Ręcznie usuń wszystkie punkty połączenia z agentem DC w każdym kontekście nazewnictwa domen. W zależności od tego, jak szeroko wdrożono oprogramowanie, może istnieć jeden z tych obiektów na kontroler domeny w lesie. Lokalizację tego obiektu można odnaleźć za pomocą następującego polecenia Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Obiekty uzyskane za pośrednictwem `Get-ADObject` polecenia mogą być następnie potoku `Remove-ADObject`lub usunięte ręcznie.

   Nie pomijaj gwiazdki ("*") na końcu wartości zmiennej $keywords.

5. Ręcznie usuń stan konfiguracji poziomu lasu. Stan konfiguracji lasu jest przechowywany w kontenerze w kontekście nazewnictwa konfiguracji Active Directory. Można go odnaleźć i usunąć w następujący sposób:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ręcznie usuń wszystkie stany związane z folderem Sysvol przez ręczne usunięcie następującego folderu i całej jego zawartości:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   W razie potrzeby Ta ścieżka może być również dostępna lokalnie na danym kontrolerze domeny; lokalizacja domyślna będzie wyglądać następująco:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ta ścieżka jest inna, jeśli udział SYSVOL został skonfigurowany w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Następne kroki

[Często zadawane pytania dotyczące ochrony hasłem w usłudze Azure AD](howto-password-ban-bad-on-premises-faq.md)

Aby uzyskać więcej informacji na temat globalnych i niestandardowych list zakazanych haseł, zobacz artykuł Zablokuj [złe hasła](concept-password-ban-bad.md)
