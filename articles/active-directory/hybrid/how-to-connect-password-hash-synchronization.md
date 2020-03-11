---
title: Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync | Dokumentacja firmy Microsoft
description: Zawiera informacje o sposobie działania synchronizacji skrótów haseł i sposób konfigurowania.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6451507eb5a25f432c73468d0da0db1838c8c9a
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082619"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync
Ten artykuł zawiera informacje potrzebne do synchronizacji haseł użytkowników z lokalnym wystąpieniem usługi Active Directory do wystąpienia usługi Azure Active Directory (Azure AD) oparte na chmurze.

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Domeny usługi Active Directory są przechowywane hasła w postaci reprezentację wartości skrótu hasła rzeczywisty użytkownik. Wartość skrótu jest wynikiem jednokierunkowej funkcji matematycznej ( *algorytmem wyznaczania wartości skrótu*). Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. 

Aby zsynchronizować hasła, synchronizacja programu Azure AD Connect wyodrębnia usługi skrótów haseł z lokalnym wystąpieniem usługi Active Directory. Przetwarzanie zapewnienia dodatkowego bezpieczeństwa jest stosowany do skrótu hasła, zanim jest zsynchronizowany z usługą uwierzytelniania usługi Azure Active Directory. Hasła są synchronizowane na poszczególnych użytkowników, a także w porządku chronologicznym.

Przepływ danych rzeczywisty proces synchronizacji skrótów haseł jest podobny do synchronizacji danych użytkownika. Jednak synchronizowanych haseł częściej niż w oknie Synchronizacja katalogu standard innych atrybutów. Proces synchronizacji skrótów haseł jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Podczas synchronizowania hasła zastępuje istniejące hasło w chmurze.

Przy pierwszym włączeniu funkcji synchronizacji skrótów haseł, wykonuje synchronizację początkową haseł wszystkich użytkowników w zakresie. Nie można jawnie zdefiniować podzbiór haseł użytkowników, które mają być synchronizowane. Jeśli jednak istnieje wiele łączników, można wyłączyć synchronizację skrótów haseł dla niektórych łączników, ale nie inne za pomocą polecenia cmdlet [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

Jeśli zmienisz hasło lokalne, zaktualizowanie hasła jest zsynchronizowana, w większości przypadków w ciągu kilku minut.
Funkcji synchronizacji skrótów haseł automatycznie ponawia próby synchronizacji nie powiodło się. Jeśli wystąpi błąd podczas próby synchronizacji haseł, błąd jest rejestrowany w podglądu zdarzeń.

Synchronizacja haseł nie ma wpływu na użytkownika, który jest aktualnie zalogowany.
Zmiany hasła zsynchronizowane, który występuje, gdy użytkownik jest zalogowany, usługi w chmurze nie występuje natychmiast w bieżącej sesji usługi chmury. Jednak gdy usługę w chmurze, należy uwierzytelnić się ponownie, należy podać nowe hasło.

Użytkownik musi wprowadzić swoje poświadczenia firmy po raz drugi do uwierzytelniania w usłudze Azure AD, niezależnie od tego, czy ich zalogowany do sieci firmowej. Ten wzorzec można zminimalizować, jednak jeśli użytkownik wybierze Zachowaj mnie (KMSI) pole wyboru przy logowaniu. Zaznacz to pole wyboru, ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie KMSI może być włączona lub wyłączona przez administratora usługi Azure AD. Ponadto można zmniejszyć monity o hasło, włączając [bezproblemową rejestrację jednokrotną](how-to-connect-sso.md), która automatycznie podpisuje użytkowników w przypadku, gdy znajdują się one na urządzeniach firmowych podłączonych do sieci firmowej.

> [!NOTE]
> Synchronizacja haseł jest obsługiwana tylko dla użytkownika typu obiektu w usłudze Active Directory. Nie jest obsługiwana dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis działania synchronizacji skrótów haseł

W poniższej sekcji opisano, szczegółowe, jak działa synchronizacja skrótów haseł między usługi Active Directory i Azure AD.

![Przepływ szczegółowe hasła](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Co dwie minuty agenta synchronizacji skrótów haseł dla żądań serwera AD Connect przechowywane wartości skrótów haseł (atrybutu unicodePwd) z kontrolera domeny.  To żądanie jest realizowane za pośrednictwem standardowego protokołu replikacji [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) służącego do synchronizowania danych między kontrolerami domeny. Konto usługi musi mieć Replikacja zmian katalogów i Replikuj wszystkie zmiany katalogu AD uprawnienia (domyślnie na instalację), do uzyskania wartości skrótu hasła.
2. Przed wysłaniem kontroler domeny szyfruje skrót hasła algorytmu MD4 przy użyciu klucza, który jest skrótem [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klucza sesji RPC i soli. Następnie wysyła wyniki do agenta synchronizacji skrótu hasła za pośrednictwem wywołania RPC. Kontroler domeny również przekazuje ziarna do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, aby agent będzie można odszyfrować koperty.
3. Gdy Agent synchronizacji skrótów haseł ma zaszyfrowaną kopertę, używa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) i soli do wygenerowania klucza w celu odszyfrowania odebranych danych z powrotem do oryginalnego formatu MD4. Agent synchronizacji skrótów haseł nigdy nie ma dostępu do hasła w postaci zwykłego tekstu. Użycie agenta synchronizacji skrótów haseł MD5 jest wyłącznie dla zgodności protokołu replikacji z kontrolerem domeny i jest używana tylko w środowisku lokalnym między kontrolerem domeny i agenta synchronizacji skrótów haseł.
4. Agent synchronizacji skrótów haseł rozwija skrót hasła binarne 16-bajtowy 64 bajtów konwertując pierwszy wyznaczania wartości skrótu do 32 bajtów ciąg szesnastkowy, następnie konwertując ten ciąg z powrotem do pliku binarnego z kodowaniem UTF-16.
5. Dodaje agenta synchronizacji skrótów haseł na ziarna użytkowników, składające się soli długości 10 bajtów do pliku binarnego 64 bajtów, aby jeszcze lepiej chronić oryginalnego wyznaczania wartości skrótu.
6. Agent synchronizacji skrótów haseł łączy skrót MD4 ze znakiem poszczególnych użytkowników i wprowadza je do funkcji [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iteracji algorytmem wyznaczania wartości skrótu [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) . 
7. Agent synchronizacji skrótów haseł ma wynikowy skrót 32 bajtów, łączy się zarówno na ziarna użytkownika i liczba SHA256 iteracji do niego (do użytku przez usługę Azure AD), przesyła ciąg z usługi Azure AD Connect do usługi Azure AD za pośrednictwem protokołu SSL.</br> 
8. Gdy użytkownik spróbuje się zalogować do usługi Azure AD i wprowadza swoje hasło, hasło jest uruchamiane za pomocą tego samego MD4 + ziarna + PBKDF2 + HMAC SHA256 procesu. Jeśli wynikowy skrót pasuje do wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, użytkownik wprowadził prawidłowe hasło i jest uwierzytelniony.

> [!NOTE]
> Oryginalny skrót MD4 nie są przesyłane do usługi Azure AD. Zamiast tego są przesyłane skrót SHA256 obiektu oryginalnego wyznaczania wartości skrótu MD4. W rezultacie w przypadku wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, nie można użyć w przypadku ataków typu pass--hash w środowisku lokalnym.

### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Podczas synchronizacji haseł wersji tekstowego hasła nie jest uwidaczniany w funkcji synchronizacji skrótów haseł, do usługi Azure AD lub dowolnej z usług jest skojarzony.

Uwierzytelnianie użytkownika ma miejsce, w usłudze Azure AD, a nie względem wystąpienia usługi Active Directory w organizacji. Dane haseł SHA256, które są przechowywane w usłudze Azure AD — skrót oryginalnego wyznaczania wartości skrótu MD4 — jest bezpieczniejszy niż co to jest przechowywany w usłudze Active Directory. Co więcej ponieważ nie można odszyfrować ten skrót SHA256, uniemożliwiający sprowadzony z powrotem do środowiska usługi Active Directory w firmie i przedstawiane jako prawidłowego użytkownika hasła w ataku typu pass--hash.

### <a name="password-policy-considerations"></a>Zagadnienia dotyczące zasad haseł

Istnieją dwa typy zasad haseł, których dotyczy Włączanie synchronizacji skrótów haseł:

* Zasady złożoności haseł
* Zasady wygasania haseł

#### <a name="password-complexity-policy"></a>Zasady złożoności haseł

Po włączeniu synchronizacji skrótów haseł, zasady złożoności haseł w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym zastępują zasady złożoności w chmurze na potrzeby zsynchronizowani użytkownicy. Wszystkie prawidłowe haseł z lokalnego wystąpienia usługi Active Directory można użyć na dostęp do usług Azure AD.

> [!NOTE]
> Hasła użytkowników, które są tworzone bezpośrednio w chmurze są nadal podlega procesowi zasady dotyczące haseł, zgodnie z definicją w chmurze.

#### <a name="password-expiration-policy"></a>Zasady wygasania haseł

Jeśli użytkownik należy do zakresu synchronizacji skrótów haseł, domyślnie hasło konta w chmurze jest ustawione na *nigdy nie wygasa*.

Można nadal logować się do usługi w chmurze przy użyciu hasła zsynchronizowane, który wygasł w środowisku w środowisku lokalnym. Hasło do chmury jest aktualizowana przy następnym zmienić hasło w środowisku lokalnym.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Publiczna wersja zapoznawcza funkcji *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

W przypadku synchronizacji użytkowników, którzy współpracują z usługami zintegrowanymi z usługą Azure AD i muszą być zgodni z zasadami wygasania haseł, można wymusić ich zgodność z zasadami wygasania haseł usługi Azure AD, włączając funkcję *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Gdy *EnforceCloudPasswordPolicyForPasswordSyncedUsers* jest wyłączone (jest to ustawienie domyślne), Azure AD Connect ustawia atrybut PasswordPolicies synchronizowanych użytkowników na "DisablePasswordExpiration". Jest to wykonywane za każdym razem, gdy hasło użytkownika jest synchronizowane i powoduje, że usługa Azure AD zignoruje zasady wygaśnięcia hasła w chmurze dla tego użytkownika. Wartość atrybutu można sprawdzić przy użyciu modułu Azure AD PowerShell za pomocą następującego polecenia:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Aby włączyć funkcję EnforceCloudPasswordPolicyForPasswordSyncedUsers, uruchom następujące polecenie przy użyciu modułu MSOnline PowerShell, jak pokazano poniżej. Należy wpisać wartość tak dla parametru Enable, jak pokazano poniżej:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po włączeniu usługa Azure AD nie przechodzi do każdego synchronizowanego użytkownika w celu usunięcia wartości `DisablePasswordExpiration` z atrybutu PasswordPolicies. Zamiast tego wartość jest ustawiana na `None` podczas następnej synchronizacji haseł dla każdego użytkownika po kolejnym zmianie hasła w lokalnej usłudze AD.  

Zaleca się włączenie EnforceCloudPasswordPolicyForPasswordSyncedUsers przed włączeniem synchronizacji skrótów haseł, dzięki czemu początkowa synchronizacja skrótów haseł nie dodaje wartości `DisablePasswordExpiration` do atrybutu PasswordPolicies dla użytkowników.

Domyślne zasady haseł usługi Azure AD wymagają od użytkowników zmiany haseł co 90 dni. Jeśli zasady usługi AD są również 90 dni, dwie zasady powinny być zgodne. Jeśli jednak zasady usługi AD nie są 90 dni, można zaktualizować zasady haseł usługi Azure AD tak, aby odpowiadały za pomocą polecenia Set-MsolPasswordPolicy programu PowerShell.

Usługa Azure AD obsługuje oddzielne zasady wygasania haseł dla zarejestrowanej domeny.

Zastrzeżenie: Jeśli istnieją zsynchronizowane konta, które muszą mieć niewygasające hasła w usłudze Azure AD, musisz jawnie dodać wartość `DisablePasswordExpiration` do atrybutu PasswordPolicies obiektu user w usłudze Azure AD.  Można to zrobić, uruchamiając następujące polecenie.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Ta funkcja jest teraz dostępna w publicznej wersji zapoznawczej.
> Polecenie Set-MsolPasswordPolicy programu PowerShell nie będzie działało w domenach federacyjnych. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Publiczna wersja zapoznawcza synchronizacji haseł tymczasowych i "Wymuś zmianę hasła przy następnym logowaniu"

Typowo, aby wymusić zmianę hasła przez użytkownika podczas pierwszego logowania, szczególnie po wystąpieniu resetowania hasła administratora.  Jest on często znany jako ustawienie "tymczasowego" hasła i jest wykonywane przez sprawdzenie flagi "użytkownik musi zmienić hasło przy następnym logowaniu" w obiekcie użytkownika w Active Directory (AD).
  
Funkcja hasła tymczasowego pomaga zapewnić, że przeniesienie własności poświadczenia odbywa się przy pierwszym użyciu, aby zminimalizować czas, w którym więcej niż jedna osoba ma wiedzę na temat tego poświadczenia.

Aby obsłużyć tymczasowe hasła w usłudze Azure AD dla synchronizowanych użytkowników, można włączyć funkcję *ForcePasswordChangeOnLogOn* , uruchamiając następujące polecenie na serwerze Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Wymuszanie użytkownikowi zmiany hasła przy następnym logowaniu wymaga zmiany hasła w tym samym czasie.  Azure AD Connect nie spowoduje pobrania przez siebie flagi "Wymuszaj zmianę hasła". jest uzupełnieniem wykrytej zmiany hasła, która występuje podczas synchronizacji skrótów haseł.

> [!CAUTION]
> Tej funkcji należy używać tylko wtedy, gdy w dzierżawie są włączone SSPR i zapisywanie zwrotne haseł.  Jest tak dlatego, że jeśli użytkownik zmieni hasło za pośrednictwem SSPR, zostanie zsynchronizowany z Active Directory.

> [!NOTE]
> Ta funkcja jest teraz dostępna w publicznej wersji zapoznawczej.

#### <a name="account-expiration"></a>Termin wygaśnięcia konta

Jeśli Twoja organizacja używa atrybutu accountExpires jako część zarządzania kontami użytkowników, ten atrybut nie jest zsynchronizowany z usługą Azure AD. Co w efekcie Wygasłe konta usługi Active Directory w środowisku, który został skonfigurowany do synchronizacji skrótów haseł nadal jest aktywny w usłudze Azure AD. Zalecamy, aby Jeśli konto wygasło, Akcja przepływu pracy powinna wyzwolić skrypt programu PowerShell, który wyłącza konto usługi Azure AD użytkownika (Użyj polecenia cmdlet [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) ). Z drugiej strony gdy konto jest włączone, wystąpienia usługi Azure AD powinien zostać włączony.

### <a name="overwrite-synchronized-passwords"></a>Zastąp synchronizowanych haseł

Administrator może ręcznie zresetować hasło za pomocą programu Windows PowerShell.

W tym przypadku nowe hasło zastępuje zsynchronizowane hasło, a wszystkie zasady haseł, zdefiniowane w chmurze są stosowane do nowego hasła.

Jeśli zmienisz hasło lokalne ponownie nowe hasło jest zsynchronizowany z chmurą i zastępuje ona ręczne zaktualizowanie hasła.

Synchronizacja haseł nie ma wpływu na użytkownika platformy Azure, który jest zalogowany. Zmiany hasła zsynchronizowane, który występuje, gdy zalogowany do usługi w chmurze nie występuje natychmiast w bieżącej sesji usługi chmury. KMSI rozszerza czas trwania tej różnicy. Gdy usługa w chmurze, należy uwierzytelnić się ponownie, musisz podać nowe hasło.

### <a name="additional-advantages"></a>Dodatkowe korzyści

- Ogólnie rzecz biorąc synchronizacji skrótów haseł jest prostsza do zaimplementowania niż usługi federacyjnej. Nie wymaga żadnych dodatkowych serwerów i eliminuje zależność od usługi federacyjnej o wysokiej dostępności do uwierzytelniania użytkowników.
- Można również włączyć synchronizację skrótów haseł oprócz federacji. Może być używany jako rezerwowe, jeśli usługi federacyjnej ulegnie awarii.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizacji skrótów haseł dla Azure AD Domain Services

Jeśli używasz Azure AD Domain Services w celu zapewnienia starszego uwierzytelniania dla aplikacji i usług, które muszą korzystać z protokołu Kerberos, LDAP lub NTLM, niektóre dodatkowe procesy są częścią przepływu synchronizacji skrótów haseł. Azure AD Connect wykorzystuje dodatkowy następujący proces do synchronizowania skrótów haseł w usłudze Azure AD do użycia w programie Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Instalowanie Azure AD Connect w domenie zarządzanej AD DS platformy Azure nie jest obsługiwane do synchronizowania obiektów z powrotem do usługi Azure AD.
>
> Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu AD DS platformy Azure dla dzierżawy usługi Azure AD. Poniższe kroki nie są używane, jeśli do synchronizowania środowiska lokalnego AD DS z usługą Azure AD jest używana tylko Azure AD Connect.
>
> Jeśli starsze aplikacje nie korzystają z uwierzytelniania NTLM lub prostych powiązań LDAP, zalecamy wyłączenie synchronizacji skrótów haseł NTLM dla AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [wyłączanie słabych mechanizmów szyfrowania i synchronizacji skrótów poświadczeń NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect Pobiera klucz publiczny dla wystąpienia Azure AD Domain Services dzierżawcy.
1. Po zmianie hasła przez użytkownika lokalny kontroler domeny przechowuje wynik zmiany hasła (skróty) w dwóch atrybutach:
    * wartość *unicodePwd* dla skrótu hasła NTLM.
    * *supplementalCredentials* dla skrótu hasła protokołu Kerberos.
1. Azure AD Connect wykrywa zmiany haseł za pośrednictwem kanału replikacji katalogu (zmiany atrybutów, które muszą być replikowane do innych kontrolerów domeny).
1. W przypadku każdego użytkownika, którego hasło zostało zmienione, Azure AD Connect wykonuje następujące czynności:
    * Generuje losowy klucz symetryczny AES 256-bitowy.
    * Generuje losowy wektor inicjujący, który jest wymagany podczas pierwszej rundy szyfrowania.
    * Wyodrębnia skróty haseł protokołu Kerberos z atrybutów *supplementalCredentials* .
    * Azure AD Domain Services sprawdza ustawienie *SyncNtlmPasswords* zabezpieczeń konfiguracji.
        * Jeśli to ustawienie jest wyłączone, generuje losowy, wieloznaczny skrót protokołu NTLM (różny od hasła użytkownika). Ten skrót jest następnie połączony z dokładnymi skrótami hasła protokołu Kerberos z atrybutu *supplementalCrendetials* do jednej struktury danych.
        * Jeśli ta funkcja jest włączona, łączy wartość atrybutu *unicodePwd* z wyodrębnionymi skrótami hasła protokołu Kerberos z atrybutu *supplementalCredentials* do jednej struktury danych.
    * Szyfruje pojedynczą strukturę danych przy użyciu klucza symetrycznego AES.
    * Szyfruje klucz symetryczny AES przy użyciu klucza publicznego Azure AD Domain Services dzierżawy.
1. Azure AD Connect przesyła szyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych zawierających skróty haseł oraz wektor inicjujący do usługi Azure AD.
1. Usługa Azure AD przechowuje zaszyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych i wektor inicjalizacji dla użytkownika.
1. Usługa Azure AD wypycha zaszyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych i wektor inicjalizacji przy użyciu wewnętrznego mechanizmu synchronizacji przez zaszyfrowaną sesję HTTP do Azure AD Domain Services.
1. Azure AD Domain Services Pobiera klucz prywatny dla wystąpienia dzierżawcy z magazynu kluczy platformy Azure.
1. W przypadku każdego zaszyfrowanego zestawu danych (reprezentującego zmianę hasła pojedynczego użytkownika) Azure AD Domain Services następnie wykonuje następujące czynności:
    * Używa swojego klucza prywatnego do odszyfrowania klucza symetrycznego AES.
    * Używa klucza symetrycznego AES z wektorem inicjalizacji w celu odszyfrowania struktury zaszyfrowanych danych, która zawiera skróty haseł.
    * Zapisuje wartości skrótów haseł protokołu Kerberos, które otrzymuje do kontrolera domeny Azure AD Domain Services. Skróty są zapisywane w atrybucie *supplementalCredentials* obiektu użytkownika, który jest szyfrowany do klucza publicznego Azure AD Domain Services kontrolera domeny.
    * Azure AD Domain Services zapisuje skrót hasła NTLM otrzymany na Azure AD Domain Services kontrolerze domeny. Skrót jest zapisywany w atrybucie *unicodePwd* obiektu użytkownika, który jest szyfrowany do klucza publicznego Azure AD Domain Services kontrolera domeny.

## <a name="enable-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

>[!IMPORTANT]
>W przypadku migrowania z AD FS (lub innych technologii federacyjnych) do synchronizacji skrótów haseł zdecydowanie zalecamy zapoznanie się z naszym szczegółowym przewodnikiem wdrażania opublikowanym [tutaj](https://aka.ms/adfstophsdpdownload).

W przypadku instalowania Azure AD Connect przy użyciu opcji **Ustawienia ekspresowe** synchronizacja skrótów haseł jest włączana automatycznie. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md).

Jeśli używasz ustawienia niestandardowe, podczas instalowania programu Azure AD Connect, synchronizacji skrótów haseł jest dostępna na stronie logowania użytkownika. Aby uzyskać więcej informacji, zobacz [Instalacja Niestandardowa Azure AD Connect](how-to-connect-install-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizacja skrótów haseł i ze standardem FIPS
Jeśli serwer został zablokowany zgodnie z informacji przetwarzania Standard FIPS (Federal), MD5 jest wyłączone.

**Aby włączyć MD5 dla synchronizacji skrótów haseł, wykonaj następujące czynności:**

1. Przejdź do Sync\Bin %programfiles%\Azure AD.
2. Otwórz miiserver.exe.config.
3. Przejdź do węzła Konfiguracja/środowiska uruchomieniowego na końcu pliku.
4. Dodaj następujący węzeł: `<enforceFIPSPolicy enabled="false"/>`
5. Zapisz zmiany.

Odwołanie ten fragment kodu jest, co powinno to wyglądać:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Aby uzyskać informacje o zabezpieczeniach i standardzie FIPS, zobacz [synchronizowanie skrótów haseł, szyfrowanie i zgodność ze standardem FIPS usługi Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Zapoznaj się z planem wdrożenia krok po kroku na potrzeby migracji z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
