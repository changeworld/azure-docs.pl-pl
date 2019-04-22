---
title: Rozwiązywanie problemów w ochrona za pomocą hasła usługi Azure AD — usługi Azure Active Directory
description: Omówienie usługi Azure AD hasło ochrony typowych rozwiązywania problemów
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
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58863184"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Rozwiązywanie problemów z usługi Azure AD ochrony hasłem

Po wdrożeniu ochrony haseł usługi Azure AD rozwiązywania problemów może być wymagane. W tym artykule przechodzi do szczegółów, aby lepiej zrozumieć niektóre typowe kroki rozwiązywania problemów.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent kontrolera domeny nie może zlokalizować serwera proxy w katalogu

Główne objawem tego problemu jest 30017 zdarzenia w dzienniku zdarzeń administratora agenta kontrolera domeny.

Najczęstszą przyczyną tego problemu jest to, że serwer proxy nie została jeszcze zarejestrowana. Jeśli serwer proxy został zarejestrowany, może istnieć pewne opóźnienie, ze względu na opóźnienie replikacji usługi AD do momentu określonego agenta kontrolera domeny jest w stanie zobaczyć, że serwer proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent kontrolera domeny nie jest w stanie nawiązać połączenia z serwerem proxy

Główne objawem tego problemu jest 30018 zdarzenia w dzienniku zdarzeń administratora agenta kontrolera domeny. Może to mieć kilka możliwych przyczyn:

1. Agent kontrolera domeny znajduje się w izolowanej część sieci, która nie zezwala na połączenie sieciowe z zarejestrowanych proxy(s). Ten problem może być w związku z tym expected\benign, tak długo, jak inni agenci kontroler domeny może komunikować się z proxy(s) w celu pobrania zasad haseł z platformy Azure, który następnie będzie można uzyskać za izolowane kontrolera domeny za pomocą replikacji plików zasad udziału sysvol.

1. Blokuje dostęp do endpoint mapowania punktu końcowego usługi RPC (port 135) komputera hosta serwera proxy

   Instalator serwera Proxy ochrony haseł usługi Azure AD automatycznie tworzy regułę ruchu przychodzącego zapory Windows, która zezwala na dostęp do portu 135 protokołów. Jeśli ta reguła jest później usunięte lub wyłączone, agentów kontrolera domeny będzie mógł komunikować się z usługą serwera Proxy. Jeśli wbudowane zapory Windows została wyłączona audytów innego produktu do zapory, należy skonfigurować zapory w celu zezwolić na dostęp do portu 135 protokołów.

1. Komputer hosta serwera proxy blokuje dostęp do końcowych wywołań RPC (dynamicznej lub statycznej) posłuchaliśmy na przez usługę serwera Proxy

   Instalator serwera Proxy ochrony haseł usługi Azure AD automatycznie tworzy zaporę Windows regułę ruchu przychodzącego, która umożliwia uzyskanie dostępu do żadnych portów przychodzących wysłuchaliśmy przez usługę serwera Proxy ochrony haseł usługi Azure AD. Jeśli ta reguła jest później usunięte lub wyłączone, agentów kontrolera domeny będzie mógł komunikować się z usługą serwera Proxy. Jeśli wbudowane zapory Windows została wyłączona audytów innego produktu do zapory, należy skonfigurować, zaporę, aby zezwolić na dostęp do żadnych portów przychodzących wysłuchaliśmy przez usługę serwera Proxy ochrony haseł usługi Azure AD. Tej konfiguracji mogą być nawiązywane dokładniej, jeśli usługa serwera Proxy została skonfigurowana do nasłuchiwania na konkretnym porcie RPC statyczne (przy użyciu `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Usługa serwera Proxy może odbierać wywołania z agentów dla kontrolera domeny w domenie, ale nie może komunikować się z platformą Azure

1. Upewnij się, komputera serwera proxy ma połączenia z punktami końcowymi na liście [wymagania w zakresie wdrażania](howto-password-ban-bad-on-premises-deploy.md).

1. Upewnij się, że lasu i wszystkie serwera proxy, które serwery są zarejestrowane dla tej samej dzierżawie usługi Azure.

   Można to sprawdzić, uruchamiając `Get-AzureADPasswordProtectionProxy` i `Get-AzureADPasswordProtectionDCAgent` poleceń cmdlet programu PowerShell, następnie porównać `AzureTenant` właściwości każdego zwrócony element. Do poprawnego działania te musi być taka sama w lesie, we wszystkich agentów kontrolera domeny i serwery proxy.

   Jeśli warunek niezgodność rejestracji dzierżawy platformy Azure istnieje, można to naprawić, uruchamiając `Register-AzureADPasswordProtectionProxy` i/lub `Register-AzureADPasswordProtectionForest` poleceń cmdlet programu PowerShell, zgodnie z potrzebami, upewniając się użyć poświadczeń z tą samą dzierżawą usługi Azure dla wszystkich rejestracji.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>Agent kontrolera domeny nie może zaszyfrować lub odszyfrować pliki zasad haseł i innych stanu

Ten problem można manifestu z różnymi rodzajami objawy, ale zazwyczaj ma typowych głównych przyczyn.

Ochrona za pomocą usługi Azure AD hasła ma krytyczne zależności od funkcji szyfrowania i odszyfrowywania, dostarczone przez usługi dystrybucji kluczy firmy Microsoft, co jest dostępne na kontrolerach domeny z systemem Windows Server 2012 i nowszych wersjach. Usługę KDS musi być włączona i działa na wszystkich systemu Windows Server 2012 i nowszych kontrolerów domeny w domenie.

Domyślnie KDS tryb uruchamiania usługi usługi jest skonfigurowany jako ręczne (uruchomienie wyzwalacza). Ta konfiguracja oznacza, że klient próbuje korzystać z tej usługi po raz pierwszy jej ponownym uruchomieniu na żądanie. To domyślny tryb uruchamiania usługi jest możliwa do obsługi ochrony haseł usługi Azure AD.

Jeśli tryb uruchamiania usługi KDS został skonfigurowany na wyłączone, ta konfiguracja muszą zostać usunięte przed ochrony haseł usługi Azure AD będzie działać prawidłowo.

Prosty test tego problemu jest ręcznie uruchom usługę KDS, za pośrednictwem konsoli MMC zarządzania usługami lub przy użyciu innych narzędzi do zarządzania usługi (na przykład uruchomić z poziomu konsoli wiersza polecenia polecenie "net start kdssvc"). Usługę KDS powinien uruchomić się pomyślnie i działają bez przerw.

Najczęstszą przyczyną główny nie będzie w stanie uruchomić usługę KDS jest zlokalizowane poza domyślnej jednostki Organizacyjnej kontrolery domeny obiektu kontrolera domeny usługi Active Directory. Ta konfiguracja nie jest obsługiwana przez usługę KDS i brak ograniczeń narzuconych przez funkcję ochrony haseł usługi Azure AD. Poprawka dla tego warunku jest przenoszenie obiektu kontrolera domeny do lokalizacji domyślnej jednostki Organizacyjnej kontrolery domeny.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Słabe hasła są akceptowane, ale nie powinien być

Ten problem może mieć kilka przyczyn.

1. Twoje agentach DC nie może pobrać zasadę lub nie jest w stanie odszyfrować istniejące zasady. Sprawdź możliwe przyczyny w tematach powyżej.

1. Tryb wymuszania zasad haseł jest nadal równa inspekcji. Jeśli ta konfiguracja jest włączone, ją ponownie skonfigurować, aby wymusić przy użyciu portalu ochrony haseł usługi Azure AD. Zobacz [ochrony hasłem Włącz](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Zasady haseł zostało wyłączone. Jeśli ta konfiguracja jest włączone, należy ponownie skonfigurować ją włączyć za pomocą portalu ochrony haseł usługi Azure AD. Zobacz [ochrony hasłem Włącz](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Nie zainstalowano oprogramowanie agenta kontrolera domeny na wszystkich kontrolerach domeny w domenie. W tej sytuacji jest trudne upewnić się, że klienci zdalni Windows docelowy od określonego kontrolera domeny podczas operacji zmiany hasła. Jeśli mają określonego kontrolera domeny, których instalowane jest oprogramowanie agenta kontrolera domeny jest pomyślnie skierowany, można sprawdzić, podwójnej w dzienniku zdarzeń administratora agenta kontrolera domeny: niezależnie od tego, w wyniku, będzie istnieć co najmniej jedno zdarzenie, aby udokumentować wyniki hasło Sprawdzanie poprawności. Jeśli nie ma żadnego zdarzenia, które są obecne dla użytkownika, którego hasło zostało zmienione, zmiany hasła prawdopodobnie została przetworzona za pomocą innego kontrolera domeny.

   Jako alternatywnego testu spróbuj setting\changing hasła po zalogowaniu się bezpośrednio z kontrolerem domeny, na których instalowane jest oprogramowanie agenta kontrolera domeny. Ta technika nie jest zalecane dla domen usługi Active Directory w środowisku produkcyjnym.

   Wdrożenie przyrostowe oprogramowania agenta kontrolera domeny jest obsługiwana z zastrzeżeniem tych ograniczeń, firma Microsoft zdecydowanie zaleca oprogramowanie agenta kontrolera domeny jest zainstalowanie na wszystkich kontrolerach domeny w domenie, tak szybko, jak to możliwe.

1. Algorytm sprawdzania poprawności hasła może rzeczywiście działa, zgodnie z oczekiwaniami. Zobacz [jak są hasła obliczane](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchomiony w trybie naprawy usług katalogowych, Usługa agenta kontrolera domeny wykrywa ten warunek i spowoduje, że wszystkie sprawdzenie poprawności hasła lub działań wykonawczych, można wyłączyć niezależnie od aktualnie aktywne zasady konfiguracji.

## <a name="emergency-remediation"></a>Korygowanie awaryjnego

Jeśli sytuacja występuje, gdy usługa agenta kontrolera domeny powoduje problemy, Usługa agenta kontrolera domeny może być natychmiast zamknięty. Dll filtru haseł agenta kontrolera domeny nadal próbuje wywołać bez działającej usługi i będzie rejestrować zdarzenia ostrzeżeń (10012, 10013), ale wszystkie przychodzące hasła są akceptowane w tym samym czasie. Usługa agenta kontrolera domeny może także można skonfigurować za pomocą Windows Menedżer sterowania usługami za pomocą typu uruchamiania "Disabled" zgodnie z potrzebami.

Kolejną miarę korygowania jest ustawienie Włącz tryb na nie w portalu ochrony haseł usługi Azure AD. Po pobraniu zaktualizowane zasady każda usługa agenta DC przejdzie w tryb spoczynku, gdzie wszystkie hasła będzie akceptowane jako-to. Aby uzyskać więcej informacji, zobacz [trybie wymuszania](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Obniżenia poziomu kontrolera domeny

Możliwe jest obniżenie poziomu kontrolera domeny, który wciąż działa oprogramowanie agenta kontrolera domeny. Administratorzy Pamiętaj jednak, oprogramowanie agenta kontrolera domeny w dalszym ciągu Wymuszaj bieżące zasady haseł podczas wykonywania procedury obniżania poziomu. Nowe hasło administratora lokalnego konta (określonego jako część operacji obniżania poziomu) jest weryfikowana, podobnie jak inne hasło. Firma Microsoft zaleca, można wybrać bezpiecznych haseł dla konta administratora lokalnego w ramach procedury obniżania poziomu kontrolera domeny; Jednakże weryfikacji nowe hasło administratora lokalnego konta przez oprogramowanie agenta kontrolera domeny może być znaczący wpływ na istniejące procedury operacyjne obniżania poziomu.

Po obniżania poziomu zakończyła się pomyślnie, a kontroler domeny zostanie ponownie uruchomiony i jest ponownie uruchomiony jako serwer członkowski normalne, przywraca uruchomiony w trybie pasywnym oprogramowanie agenta kontrolera domeny. Może on następnie odinstalowano w dowolnym momencie.

## <a name="removal"></a>Usunięcie

Jeśli zostanie podjęta decyzja, aby odinstalować oprogramowanie do ochrony haseł usługi Azure AD i czyszczenia stan wszystkich powiązanych z domeny i lasu, to zadanie można osiągnąć wykonując następujące czynności:

> [!IMPORTANT]
> Należy wykonać następujące kroki w kolejności. Jeśli dowolne wystąpienie usługi serwera Proxy zostanie pozostawiony jako uruchomiony okresowo ponownie utworzy jej obiektu serviceConnectionPoint. Jeśli dowolne wystąpienie usługi agenta kontrolera domeny jest w trybie ciągłym okresowo ponownie utworzy jej obiektu serviceConnectionPoint i folderu sysvol stanu.

1. Odinstaluj oprogramowanie serwera Proxy z wszystkich maszyn. Ten krok jest **nie** konieczne jest ponowne uruchomienie.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. W tym kroku **wymaga** ponowne uruchomienie komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera Proxy w każdym kontekście nazewnictwa domeny. Może zostać odnalezionych lokalizacji tych obiektów za pomocą następującego polecenia środowiska PowerShell usługi Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie.

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekście nazewnictwa domeny. Może istnieć tylko jeden tych obiektów na kontrolerze domeny w lesie, w zależności od tego, jak oprogramowanie zostało wdrożone. Lokalizacja tego obiektu może zostać odnalezionych za pomocą następującego polecenia środowiska PowerShell usługi Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie.

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywana w kontenerze w kontekście nazewnictwa konfiguracji w usłudze Active Directory. Można go odnalezione i usunięte w następujący sposób:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ręcznie usuń wszystkie sysvol związane stanu przez ręczne usuwanie następujący folder i całą jego zawartość:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   W razie potrzeby tę ścieżkę można również uzyskać dostęp lokalnie na kontrolerze danej domeny; Domyślna lokalizacja będzie podobny do następującej ścieżki:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ta ścieżka jest inny, jeśli skonfigurowano udziału sysvol, w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Kolejne kroki

[Często zadawane pytania dotyczące ochrony haseł usługi Azure AD](howto-password-ban-bad-on-premises-faq.md)

Aby uzyskać więcej informacji na temat list globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zablokuj błędnego hasła](concept-password-ban-bad.md)
