---
title: Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect | Dokumenty firmy Microsoft
description: Zawiera informacje o działaniu synchronizacji skrótów haseł i sposobie konfigurowania.
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
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331369"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł za pomocą usługi synchronizacji programu Azure AD Connect
Ten artykuł zawiera informacje potrzebne do synchronizowania haseł użytkowników z lokalnego wystąpienia usługi Active Directory do wystąpienia usługi Azure Active Directory (Azure AD) w chmurze.

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Usługa domeny usługi Active Directory przechowuje hasła w postaci reprezentacji wartości skrótu rzeczywistego hasła użytkownika. Wartość mieszania jest wynikiem jednokierunkowej funkcji matematycznej *(algorytm mieszania).* Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. 

Aby zsynchronizować hasło, synchronizacja usługi Azure AD Connect wyodrębnia skrót hasła z lokalnego wystąpienia usługi Active Directory. Dodatkowe przetwarzanie zabezpieczeń jest stosowane do skrótu hasła, zanim zostanie zsynchronizowany z usługą uwierzytelniania usługi Azure Active Directory. Hasła są synchronizowane na podstawie na użytkownika i w porządku chronologicznym.

Rzeczywisty przepływ danych procesu synchronizacji skrótu hasła jest podobny do synchronizacji danych użytkownika. Jednak hasła są synchronizowane częściej niż standardowe okno synchronizacji katalogów dla innych atrybutów. Proces synchronizacji skrótu hasła jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Podczas synchronizowania hasła zastępuje ono istniejące hasło w chmurze.

Przy pierwszym włączeniu funkcji synchronizacji skrótu hasła wykonuje początkową synchronizację haseł wszystkich użytkowników w zakresie. Nie można jawnie zdefiniować podzbioru haseł użytkowników, które mają zostać zsynchronizowane. Jeśli jednak istnieje wiele łączników, możliwe jest wyłączenie synchronizacji skrótów haseł dla niektórych łączników, ale nie innych przy użyciu polecenia cmdlet [Set-ADSyncADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

Po zmianie hasła lokalnego zaktualizowane hasło jest synchronizowane, najczęściej w ciągu kilku minut.
Funkcja synchronizacji skrótu hasła automatycznie ponawia próby synchronizacji nie powiodło się. Jeśli podczas próby zsynchronizowania hasła wystąpi błąd, w podglądzie zdarzeń zostanie zarejestrowany błąd.

Synchronizacja hasła nie ma wpływu na użytkownika, który jest aktualnie zalogowany.
Bieżąca sesja usługi w chmurze nie ma natychmiastowego wpływu na zsynchronizowane zmiany hasła, która występuje, podczas logowania się do usługi w chmurze. Jednak gdy usługa w chmurze wymaga ponownego uwierzytelnienia, należy podać nowe hasło.

Użytkownik musi wprowadzić swoje poświadczenia firmowe po raz drugi, aby uwierzytelnić się w usłudze Azure AD, niezależnie od tego, czy jest zalogowany do sieci firmowej. Ten wzorzec można jednak zminimalizować, jeśli użytkownik zaznaczy pole wyboru Zachowaj mnie zalogowany (KMSI) podczas logowania. To zaznaczenie ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie interfejsu KMI może być włączone lub wyłączone przez administratora usługi Azure AD. Ponadto można zmniejszyć liczbę monitów o hasło, włączając [bezproblemowe logowanie](how-to-connect-sso.md)jednokrotne, które automatycznie loguje użytkowników, gdy znajdują się na urządzeniach firmowych podłączonych do sieci firmowej.

> [!NOTE]
> Synchronizacja hasła jest obsługiwana tylko dla użytkownika typu obiektu w usłudze Active Directory. Nie jest obsługiwany dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis działania synchronizacji skrótów haseł

W poniższej sekcji opisano szczegółowo, jak działa synchronizacja skrótów haseł między usługą Active Directory a usługą Azure AD.

![Szczegółowy przepływ hasła](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Co dwie minuty agent synchronizacji skrótów haseł na serwerze usługi AD Connect żąda przechowywanych skrótów haseł (atrybutu unicodePwd) z kontrolera domeny.  To żądanie odbywa się za pośrednictwem standardowego protokołu replikacji [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) używanego do synchronizowania danych między kontrolerami domeny. Konto usługi musi mieć replikowane zmiany katalogu i replikuj katalog zmienia wszystkie uprawnienia USŁUGI AD (przyznane domyślnie podczas instalacji), aby uzyskać skróty haseł.
2. Przed wysłaniem kontroler domeny szyfruje skrót hasła MD4 przy użyciu klucza, który jest skrótem [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klucza sesji RPC i solą. Następnie wysyła wynik do agenta synchronizacji skrótu hasła za pomocą RPC. Kontroler domeny przekazuje również sól do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, dzięki czemu agent będzie mógł odszyfrować kopertę.
3. Po agent synchronizacji skrótu hasła ma zaszyfrowaną kopertę, używa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) i sól do generowania klucza do odszyfrowania odebranych danych z powrotem do oryginalnego formatu MD4. Agent synchronizacji skrótów haseł nigdy nie ma dostępu do hasła zwykłego tekstu. Użycie md5 przez agenta synchronizacji skrótu hasła jest ściśle zgodne z protokołem replikacji z kontrolerem domeny i jest używane tylko lokalnie między kontrolerem domeny a agentem synchronizacji skrótów haseł.
4. Agent synchronizacji skrótu hasła rozszerza 16-bajtowy skrót hasła binarnego do 64 bajtów, najpierw konwertując skrót na 32-bajtowy ciąg szesnastkowy, a następnie konwertując ten ciąg z powrotem na binarny z kodowaniem UTF-16.
5. Agent synchronizacji skrótu hasła dodaje sól na użytkownika, składającą się z soli o długości 10 bajtów, do 64-bajtowego pliku binarnego w celu dalszej ochrony oryginalnego skrótu.
6. Agent synchronizacji skrótu hasła następnie łączy skrót MD4 plus sól na użytkownika i wprowadza go do funkcji [PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) 1000 iteracji algorytmu mieszania [hmac-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) keyed są używane. 
7. Agent synchronizacji skrótu hasła przyjmuje wynikowy skrót 32-bajtowy, łączy zarówno sól na użytkownika, jak i liczbę iteracji SHA256 (do użycia przez usługę Azure AD), a następnie przesyła ciąg z usługi Azure AD Connect do usługi Azure AD za pomocą protokołu TLS.</br> 
8. Gdy użytkownik próbuje zalogować się do usługi Azure AD i wprowadzi swoje hasło, hasło jest uruchamiane za pośrednictwem tego samego procesu MD4 + salt + PBKDF2 + HMAC-SHA256. Jeśli wynikowy skrót pasuje do skrótu przechowywanego w usłudze Azure AD, użytkownik wprowadził poprawne hasło i jest uwierzytelniany.

> [!NOTE]
> Oryginalny skrót MD4 nie jest przesyłany do usługi Azure AD. Zamiast tego jest przesyłany skrót SHA256 oryginalnego skrótu MD4. W rezultacie jeśli uzyskuje się skrót przechowywany w usłudze Azure AD, nie można go używać w lokalnym ataku pass-the-hash.

### <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Podczas synchronizowania haseł, w postaci zwykłego tekstu hasła nie jest narażony na funkcję synchronizacji skrótu hasła, do usługi Azure AD lub żadnej z skojarzonych usług.

Uwierzytelnianie użytkowników odbywa się w przypadku usługi Azure AD, a nie w przypadku wystąpienia usługi Active Directory w organizacji. Dane haseł SHA256 przechowywane w usłudze Azure AD — skrót oryginalnego skrótu MD4 — są bezpieczniejsze niż te przechowywane w usłudze Active Directory. Ponadto ponieważ nie można odszyfrować tego skrótu SHA256, nie można go przywrócić do środowiska usługi Active Directory organizacji i przedstawić jako prawidłowe hasło użytkownika w ataku typu pass-the-hash.

### <a name="password-policy-considerations"></a>Zagadnienia dotyczące zasad haseł

Istnieją dwa typy zasad haseł, na które ma wpływ włączenie synchronizacji skrótów haseł:

* Zasady złożoności haseł
* Zasady wygasania haseł

#### <a name="password-complexity-policy"></a>Zasady złożoności haseł

Po włączeniu synchronizacji skrótów haseł zasady złożoności haseł w lokalnym wystąpieniu usługi Active Directory zastępują zasady złożoności w chmurze dla zsynchronizowanych użytkowników. Aby uzyskać dostęp do usług Usługi Azure AD, można użyć wszystkich prawidłowych haseł z lokalnego wystąpienia usługi Active Directory.

> [!NOTE]
> Hasła dla użytkowników, które są tworzone bezpośrednio w chmurze, nadal podlegają zasadom haseł zdefiniowanym w chmurze.

#### <a name="password-expiration-policy"></a>Zasady wygasania haseł

Jeśli użytkownik znajduje się w zakresie synchronizacji skrótów haseł, domyślnie hasło konta w chmurze jest ustawione na *Nigdy nie wygaśnie*.

Możesz nadal logować się do usług w chmurze przy użyciu zsynchronizowanego hasła, które wygasło w środowisku lokalnym. Hasło w chmurze jest aktualizowane przy następnej zmianie hasła w środowisku lokalnym.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Publiczny podgląd funkcji *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Jeśli istnieją zsynchronizowane użytkowników, którzy wchodzą w interakcje tylko ze zintegrowanymi usługami usługi Azure AD i muszą być zgodne z zasadami wygasania haseł, można wymusić na nich przestrzeganie zasad wygasania haseł usługi Azure AD, włączając funkcję *EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

Gdy *EnforceCloudPasswordPolicyForPasswordSyncedUsers* jest wyłączona (co jest ustawieniem domyślnym), usługa Azure AD Connect ustawia atrybut PasswordPolicies zsynchronizowanych użytkowników na "DisablePasswordExpiration". Odbywa się to za każdym razem, gdy hasło użytkownika jest synchronizowane i nakazuje usłudze Azure AD zignorowanie zasad wygasania haseł w chmurze dla tego użytkownika. Można sprawdzić wartość atrybutu za pomocą modułu programu Azure AD PowerShell za pomocą następującego polecenia:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Aby włączyć funkcję EnforceCloudPasswordPolicyForPasswordSyncedUsers, uruchom następujące polecenie przy użyciu modułu MSOnline PowerShell, jak pokazano poniżej. Musisz wpisać tak dla parametru Enable, jak pokazano poniżej:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po włączeniu usługi Azure AD nie przechodzi do `DisablePasswordExpiration` każdego zsynchronizowanego użytkownika, aby usunąć wartość z PasswordPolicies atrybutu. Zamiast tego wartość jest `None` ustawiona podczas następnej synchronizacji hasła dla każdego użytkownika, gdy następnie zmieni swoje hasło w lokalnej układzie AD.  

Zaleca się włączenie EnforceCloudPasswordPolicyForPasswordSyncedUsers, przed włączeniem synchronizacji hash hasła, tak aby początkowa `DisablePasswordExpiration` synchronizacja skrótów haseł nie dodawała wartości do atrybutu PasswordPolicies dla użytkowników.

Domyślna zasada haseł usługi Azure AD wymaga od użytkowników zmieniania haseł co 90 dni. Jeśli zasady w ad jest również 90 dni, dwie zasady powinny być zgodne. Jeśli jednak zasady usługi AD nie mają 90 dni, można zaktualizować zasady haseł usługi Azure AD, aby były zgodne za pomocą polecenia Set-MsolPasswordPolicy PowerShell.

Usługa Azure AD obsługuje oddzielne zasady wygasania haseł dla zarejestrowanej domeny.

Zastrzeżenie: Jeśli istnieją zsynchronizowane konta, które muszą mieć hasła nie wygasające `DisablePasswordExpiration` w usłudze Azure AD, należy jawnie dodać wartość do atrybutu PasswordPolicies obiektu użytkownika w usłudze Azure AD.  Można to zrobić, uruchamiając następujące polecenie.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Ta funkcja jest teraz dostępna w publicznej wersji zapoznawczej.
> Polecenie Set-MsolPasswordPolicy PowerShell nie będzie działać w domenach federacyjnej. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Publiczny podgląd synchronizacji haseł tymczasowych i "Wymuś zmianę hasła przy następnym logowaniu"

Jest to typowe, aby zmusić użytkownika do zmiany hasła podczas pierwszego logowania, zwłaszcza po zresetowaniu hasła administratora.  Jest powszechnie znany jako ustawienie hasła "tymczasowe" i jest wypełniany przez sprawdzenie "Użytkownik musi zmienić hasło przy następnym logowaniu" na obiekcie użytkownika w usłudze Active Directory (AD).
  
Funkcja hasła tymczasowego pomaga zapewnić, że przeniesienie własności poświadczeń jest zakończona przy pierwszym użyciu, aby zminimalizować czas, w którym więcej niż jedna osoba ma wiedzę na temat tego poświadczenia.

Aby obsługiwać tymczasowe hasła w usłudze Azure AD dla zsynchronizowanych użytkowników, można włączyć funkcję *ForcePasswordChangeOnLogOn,* uruchamiając następujące polecenie na serwerze usługi Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Zmuszanie użytkownika do zmiany hasła przy następnym logowaniu wymaga zmiany hasła w tym samym czasie.  Usługa Azure AD Connect nie będzie sama odbierać flagi zmiany hasła siły; jest uzupełnieniem wykrytej zmiany hasła, która ma miejsce podczas synchronizacji skrótu hasła.

> [!CAUTION]
> Tej funkcji należy używać tylko wtedy, gdy funkcja SSPR i zapisywanie hasła są włączone w dzierżawie.  Jest to tak, że jeśli użytkownik zmieni swoje hasło za pomocą samowłasnienia SSPR, zostanie on zsynchronizowany z usługą Active Directory.

> [!NOTE]
> Ta funkcja jest teraz w publicznej wersji zapoznawczej.

#### <a name="account-expiration"></a>Wygaśnięcie konta

Jeśli organizacja używa atrybutu accountExpires jako części zarządzania kontem użytkownika, ten atrybut nie jest synchronizowany z usługą Azure AD. W rezultacie wygasłe konto usługi Active Directory w środowisku skonfigurowanym do synchronizacji skrótów haseł będzie nadal aktywne w usłudze Azure AD. Zaleca się, że jeśli konto wygasło, akcja przepływu pracy powinna wyzwolić skrypt programu PowerShell, który wyłącza konto usługi Azure AD użytkownika (użyj polecenia cmdlet [Set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Z drugiej strony, gdy konto jest włączone, wystąpienie usługi Azure AD powinno być włączone.

### <a name="overwrite-synchronized-passwords"></a>Zastępowanie zsynchronizowanych haseł

Administrator może ręcznie zresetować hasło przy użyciu programu Windows PowerShell.

W takim przypadku nowe hasło zastępuje zsynchronizowane hasło, a wszystkie zasady haseł zdefiniowane w chmurze są stosowane do nowego hasła.

Jeśli ponownie zmienisz hasło lokalne, nowe hasło zostanie zsynchronizowane z chmurą i zastąpi ręcznie zaktualizowane hasło.

Synchronizacja hasła nie ma wpływu na użytkownika platformy Azure, który jest zalogowany. Bieżąca sesja usługi w chmurze nie ma natychmiastowego wpływu na zsynchronizowana zmiana hasła, która występuje podczas logowania się do usługi w chmurze. KMSI wydłuża czas trwania tej różnicy. Gdy usługa w chmurze wymaga ponownego uwierzytelnienia, należy podać nowe hasło.

### <a name="additional-advantages"></a>Dodatkowe zalety

- Ogólnie rzecz biorąc synchronizacja skrótów haseł jest prostsza do zaimplementowania niż usługa federacjonalna. Nie wymaga żadnych dodatkowych serwerów i eliminuje zależność od usługi federacyjnej o wysokiej dostępności do uwierzytelniania użytkowników.
- Oprócz synchronizacji haszyszu hasła można również włączyć synchronizację haszyszu. Może służyć jako rezerwowy, jeśli usługa federacyjne wystąpi awaria.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizacji skrótu hasła dla usług domenowych usługi Azure AD

Jeśli usługi domenowe usługi Azure AD są używane do dostarczania starszego uwierzytelniania dla aplikacji i usług, które muszą używać protokołu Kerberos, LDAP lub NTLM, niektóre dodatkowe procesy są częścią przepływu synchronizacji skrótów haseł. Usługa Azure AD Connect używa dodatkowego następującego procesu do synchronizowania skrótów haseł z usługą Azure AD do użycia w usługach domenowych usługi Azure AD:

> [!IMPORTANT]
> Usługa Azure AD Connect powinna być instalowana i konfigurowana tylko do synchronizacji z lokalnymi środowiskami usług AD DS. Nie jest obsługiwana instalacja usługi Azure AD Connect w domenie zarządzanej usług Azure AD DS w celu synchronizowania obiektów z powrotem do usługi Azure AD.
>
> Usługa Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu usług Azure AD DS dla dzierżawy usługi Azure AD. Poniższe kroki nie są używane, jeśli używasz tylko usługi Azure AD Connect do synchronizowania lokalnego środowiska usług AD DS z usługą Azure AD.
>
> Jeśli starsze aplikacje nie używają uwierzytelniania NTLM lub prostych powiązań LDAP, zaleca się wyłączenie synchronizacji skrótów haseł NTLM dla usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Wyłączanie słabych zestawów szyfrowania i synchronizacji skrótów poświadczeń NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Usługa Azure AD Connect pobiera klucz publiczny dla wystąpienia usług domenowych usługi Azure AD Domain Services.
1. Gdy użytkownik zmieni swoje hasło, lokalny kontroler domeny przechowuje wynik zmiany hasła (skróty) w dwóch atrybutach:
    * *unicodePwd* dla skrótu hasła NTLM.
    * *supplementalCredentials* dla skrótu hasła Kerberos.
1. Usługa Azure AD Connect wykrywa zmiany hasła za pośrednictwem kanału replikacji katalogu (zmiany atrybutów wymagające replikacji na inne kontrolery domeny).
1. Dla każdego użytkownika, którego hasło zostało zmienione, usługa Azure AD Connect wykonuje następujące kroki:
    * Generuje losowy 256-bitowy klucz symetryczny AES.
    * Generuje wektor inicjowania losowego potrzebny do pierwszej rundy szyfrowania.
    * Wyodrębnia skróty haseł Protokołu Kerberos z dodatkowych *atrybutówCredentials.*
    * Sprawdza ustawienie zabezpieczeń usługi Azure AD Domain Services *SyncNtlmPasswords.*
        * Jeśli to ustawienie jest wyłączone, generuje losowy skrót NTLM o wysokiej entropii (inny niż hasło użytkownika). Ten skrót jest następnie łączony z wymaganym skrótami haseł Protokołu Kerberos z atrybutu *supplementalCrendetials* w jedną strukturę danych.
        * Jeśli ta opcja jest włączona, łączy wartość atrybutu *unicodePwd* z wyodrębnianym skrótem hasła Protokołu Kerberos z atrybutu *SupplementalCredentials* w jedną strukturę danych.
    * Szyfruje strukturę pojedynczych danych przy użyciu klucza symetrycznego AES.
    * Szyfruje klucz symetryczny AES przy użyciu klucza publicznego usług domenowych usługi Azure AD.
1. Usługa Azure AD Connect przesyła zaszyfrowany klucz symetryczny usługi AES, strukturę zaszyfrowanych danych zawierającą skróty haseł oraz wektor inicjowania do usługi Azure AD.
1. Usługa Azure AD przechowuje zaszyfrowany klucz symetryczny usługi AES, strukturę zaszyfrowanych danych i wektor inicjowania dla użytkownika.
1. Usługa Azure AD wypycha zaszyfrowany klucz symetryczny usługi AES, strukturę zaszyfrowanych danych i wektor inicjowania przy użyciu mechanizmu synchronizacji wewnętrznej za pośrednictwem zaszyfrowanej sesji HTTP do usług domenowych usługi Azure AD.
1. Usługi domenowe usługi Azure AD pobierają klucz prywatny wystąpienia dzierżawy z magazynu azure key.
1. Dla każdego zaszyfrowanego zestawu danych (reprezentującego zmianę hasła jednego użytkownika) usługi domenowe usługi azure ad domain services wykonują następujące kroki:
    * Używa swojego klucza prywatnego do odszyfrowania klucza symetrycznego AES.
    * Używa klucza symetrycznego AES z wektorem inicjowania do odszyfrowania zaszyfrowanej struktury danych zawierającej skróty haseł.
    * Zapisuje skróty haseł protokołu Kerberos, które otrzymuje do kontrolera domeny usług domenowych usługi azure usług domenowych. Skróty są zapisywane w dodatkowym *atrybutemCredentials* obiektu użytkownika, który jest szyfrowany do klucza publicznego kontrolera domeny usług domenowych usługi azure ad.
    * Usługi domenowe usługi Azure AD domain Services zapisują skrót hasła NTLM odebrany do kontrolera domeny usług domenowych usługi Azure AD. Skrót jest zapisywany w atrybutie *unicodePwd* obiektu użytkownika, który jest szyfrowany do klucza publicznego kontrolera domeny usług domenowych usługi azure usług domenowych.

## <a name="enable-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

>[!IMPORTANT]
>W przypadku migracji z usług AD FS (lub innych technologii federacyjnych) do synchronizacji skrótów haseł zdecydowanie zalecamy śledzenie opublikowanego [tutaj](https://aka.ms/adfstophsdpdownload)szczegółowego przewodnika po wdrożeniach.

Po zainstalowaniu usługi Azure AD Connect przy użyciu opcji **Ustawienia ekspresowe** synchronizacja skrótów haseł jest automatycznie włączana. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md).

Jeśli używasz ustawień niestandardowych podczas instalowania usługi Azure AD Connect, synchronizacja skrótów haseł jest dostępna na stronie logowania użytkownika. Aby uzyskać więcej informacji, zobacz [Niestandardowa instalacja usługi Azure AD Connect](how-to-connect-install-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizacja skrótów haseł i FIPS
Jeśli serwer został zablokowany zgodnie z federalnym standardem przetwarzania informacji (FIPS), funkcja MD5 jest wyłączona.

**Aby włączyć md5 dla synchronizacji skrótów haseł, wykonaj następujące czynności:**

1. Przejdź do %programfiles%\Azure AD Sync\Bin.
2. Otwórz plik miiserver.exe.config.
3. Przejdź do węzła konfiguracji/środowiska wykonawczego na końcu pliku.
4. Dodaj następujący węzeł:`<enforceFIPSPolicy enabled="false"/>`
5. Zapisz zmiany.

Dla porównania, ten fragment kodu jest taki, jak powinien wyglądać:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Aby uzyskać informacje na temat zabezpieczeń i FIPS, zobacz [Synchronizacja skrótów haseł usługi Azure AD, szyfrowanie i zgodność z FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz Rozwiązywanie problemów z [synchronizacją skrótów haseł](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki
* [Synchronizacja usługi Azure AD Connect: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Pobierz plan wdrażania krok po kroku w celu migracji z usługi ADFS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
