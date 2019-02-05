---
title: Rozwiązywanie problemów w wersji zapoznawczej ochrony haseł usługi Azure AD
description: Omówienie usługi Azure AD hasło ochrony (wersja zapoznawcza) wspólnej rozwiązywania problemów
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 7474027368949d5ad2202881ac68096fac2b8bd2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693908"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Wersja zapoznawcza: Rozwiązywanie problemów z usługi Azure AD ochrony hasłem

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po wdrożeniu ochrony haseł usługi Azure AD rozwiązywania problemów może być wymagane. W tym artykule przechodzi do szczegółów, aby lepiej zrozumieć niektóre typowe kroki rozwiązywania problemów.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Słabe hasła nie są wprowadzenie odrzucane, zgodnie z oczekiwaniami

Może to mieć kilka możliwych przyczyn:

1. Twoje agentach DC nie zostały jeszcze pobrane zasady. Objawem tego jest 30001 zdarzenia w dzienniku zdarzeń administratora agenta kontrolera domeny.

    Możliwe przyczyny tego problemu to:

    1. Las nie został jeszcze zarejestrowany.
    2. Serwer proxy nie jest jeszcze zarejestrowana.
    3. Problemy z łącznością sieciową uniemożliwiają usługę serwera Proxy podczas komunikowania się z platformą Azure (wymagania dotyczące serwera Proxy HTTP wyboru)

2. Tryb wymuszania zasad haseł jest nadal równa inspekcji. Jeśli jest to możliwe, ją ponownie skonfigurować, aby wymusić przy użyciu portalu ochrony haseł usługi Azure AD. Zobacz [ochrony hasłem Włącz](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. Zasady haseł zostało wyłączone. Jeśli jest to możliwe, należy ponownie skonfigurować ją włączyć za pomocą portalu ochrony haseł usługi Azure AD. Zobacz [ochrony hasłem Włącz](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. Algorytm sprawdzania poprawności hasła może działać zgodnie z oczekiwaniami. Zobacz [jak są hasła obliczane](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchomiony w trybie naprawy usług katalogowych, Usługa agenta kontrolera domeny wykryje to i spowoduje, że wszystkie sprawdzenie poprawności hasła lub działań wykonawczych, można wyłączyć niezależnie od aktualnie aktywne zasady konfiguracji.

## <a name="emergency-remediation"></a>Korygowanie awaryjnego

Jeśli sytuacja występuje, gdy usługa agenta kontrolera domeny powoduje problemy, Usługa agenta kontrolera domeny może być natychmiast zamknięty. Dll filtru haseł agenta kontrolera domeny nadal próbuje wywołać bez działającej usługi i będzie rejestrować zdarzenia ostrzeżeń (10012, 10013), ale wszystkie przychodzące hasła są akceptowane w tym samym czasie. Usługa agenta kontrolera domeny może także można skonfigurować za pomocą Windows Menedżer sterowania usługami za pomocą typu uruchamiania "Disabled" zgodnie z potrzebami.

Kolejną miarę korygowania jest ustawienie Włącz tryb na nie w portalu ochrony haseł usługi Azure AD. Po pobraniu zaktualizowane zasady każda usługa agenta DC przejdzie w tryb spoczynku, gdzie wszystkie hasła będzie akceptowane jako-to. Aby uzyskać więcej informacji, zobacz [trybie wymuszania](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Obniżenia poziomu kontrolera domeny

Możliwe jest obniżenie poziomu kontrolera domeny, który wciąż działa oprogramowanie agenta kontrolera domeny. Administratorzy Pamiętaj jednak, oprogramowanie agenta kontrolera domeny w dalszym ciągu Wymuszaj bieżące zasady haseł podczas wykonywania procedury obniżania poziomu. Nowe hasło administratora lokalnego konta (określonego jako część operacji obniżania poziomu) jest weryfikowana, podobnie jak inne hasło. Firma Microsoft zaleca, można wybrać bezpiecznych haseł dla konta administratora lokalnego w ramach procedury obniżania poziomu kontrolera domeny; Jednakże weryfikacji nowe hasło administratora lokalnego konta przez oprogramowanie agenta kontrolera domeny może być znaczący wpływ na istniejące procedury operacyjne obniżania poziomu.

Po obniżania poziomu zakończyła się pomyślnie, a kontroler domeny zostanie ponownie uruchomiony i jest ponownie uruchomiony jako serwer członkowski normalne, przywraca uruchomiony w trybie pasywnym oprogramowanie agenta kontrolera domeny. Może on następnie odinstalowano w dowolnym momencie.

## <a name="removal"></a>Usunięcie

Jeśli zostanie podjęta decyzja, aby odinstalować oprogramowanie w publicznej wersji zapoznawczej i czyszczenia stan wszystkich powiązanych z domeny i lasu, to zadanie można osiągnąć wykonując następujące czynności:

> [!IMPORTANT]
> Należy wykonać następujące kroki w kolejności. Jeśli dowolne wystąpienie usługi serwera Proxy zostanie pozostawiony jako uruchomiony okresowo ponownie utworzy jej obiektu serviceConnectionPoint. Jeśli dowolne wystąpienie usługi agenta kontrolera domeny jest w trybie ciągłym okresowo ponownie utworzy jej obiektu serviceConnectionPoint i folderu sysvol stanu.

1. Odinstaluj oprogramowanie serwera Proxy z wszystkich maszyn. Ten krok jest **nie** konieczne jest ponowne uruchomienie.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. W tym kroku **wymaga** ponowne uruchomienie komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera Proxy w każdym kontekście nazewnictwa domeny. Może zostać odnalezionych lokalizacji tych obiektów za pomocą następującego polecenia środowiska PowerShell usługi Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie. 

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekście nazewnictwa domeny. Może istnieć tylko jeden tych obiektów na kontrolerze domeny w lesie, w zależności od tego, jak powszechnie prapremiery publicznej została wdrożona. Lokalizacja tego obiektu może zostać odnalezionych za pomocą następującego polecenia środowiska PowerShell usługi Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Obiekty wynikowe znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie.

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywana w kontenerze w kontekście nazewnictwa konfiguracji w usłudze Active Directory. Można go odnalezione i usunięte w następujący sposób:

   ```PowerShell
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
