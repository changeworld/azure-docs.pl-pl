---
title: Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync | Dokumentacja firmy Microsoft
description: Zawiera informacje o sposobie działania synchronizacji skrótów haseł i sposób konfigurowania.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: af9a2e47a5e91d9b32a787f8f40ba06b5a1596e7
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265050"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync
Ten artykuł zawiera informacje potrzebne do synchronizacji haseł użytkowników z lokalnym wystąpieniem usługi Active Directory do wystąpienia usługi Azure Active Directory (Azure AD) oparte na chmurze.

## <a name="what-is-password-hash-synchronization"></a>Co to jest synchronizacja skrótów haseł
Prawdopodobieństwo, że czy one zablokowany pracę z powodu zapomnianego hasła jest powiązany z liczby różnych haseł, o których należy pamiętać. Więcej hasła, należy pamiętać, tym większe prawdopodobieństwo, że zapomnienie o jeden. Pytania i wywołania dotyczących resetowania hasła i inne problemy związane z hasłami popytu najwięcej zasobów pomocy technicznej.

Synchronizacja skrótów haseł jest funkcją, używane do synchronizacji haseł użytkowników z lokalnym wystąpieniem usługi Active Directory na platformie Azure opartych na chmurze wystąpienia usługi AD.
Użyj tej funkcji, aby zalogować się do usługi Azure AD, takich jak usługi Office 365, Microsoft Intune, CRM Online i Azure Active Directory Domain Services (Azure AD DS). Możesz zalogować się w usłudze przy użyciu tego samego hasła, którego używasz do logowania się do swojego wystąpienia usługi Active Directory w środowisku lokalnym.

![Co to jest program Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

Dzięki zmniejszeniu liczby haseł, użytkownicy muszą zachować co najmniej jeden. Synchronizacja skrótów haseł ułatwi Ci:

* Zwiększ produktywność użytkowników.
* Obniżyć koszty pomocy technicznej.  

Ponadto jeśli użytkownik zdecyduje się użyć [Federacja z usługą Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych na wypadek awarii infrastruktury usług AD FS.

Synchronizacja skrótów haseł jest rozszerzeniem funkcji synchronizacji katalogu, które są implementowane przez program Azure AD Connect sync. Aby korzystać z synchronizacji skrótów haseł w danym środowisku, należy:

* Instalowanie programu Azure AD Connect.  
* Skonfigurować synchronizację katalogów między wystąpienia usługi Active Directory w środowisku lokalnym i wystąpienie usługi Azure Active Directory.
* Włączanie synchronizacji skrótów haseł.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Aby uzyskać więcej informacji na temat usługi Azure Active Directory Domain Services skonfigurowane dla standardu FIPS i synchronizacja skrótów haseł zobacz "synchronizacji skrótów haseł i ze standardem FIPS" w dalszej części tego artykułu.
>
>

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Domeny usługi Active Directory są przechowywane hasła w postaci reprezentację wartości skrótu hasła rzeczywistego użytkownika. Wartość skrótu jest wynikiem jednokierunkowe funkcji matematycznych ( *algorytmem wyznaczania wartości skrótu*). Nie istnieje metoda można przywrócić wynik jednokierunkowa funkcja zwykły tekst wersję hasła. Nie można używać skrótu hasła do logowania do sieci lokalnej.

Aby zsynchronizować hasła, synchronizacja programu Azure AD Connect wyodrębnia usługi skrótów haseł z lokalnym wystąpieniem usługi Active Directory. Przetwarzanie zapewnienia dodatkowego bezpieczeństwa jest stosowany do skrótu hasła, zanim jest zsynchronizowany z usługą uwierzytelniania usługi Azure Active Directory. Hasła są synchronizowane na poszczególnych użytkowników, a także w porządku chronologicznym.

Przepływ danych rzeczywisty proces synchronizacji skrótów haseł jest podobny do synchronizacji danych użytkownika, takich jak DisplayName lub adresy E-mail. Jednak synchronizowanych haseł częściej niż w oknie Synchronizacja katalogu standard innych atrybutów. Proces synchronizacji skrótów haseł jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Podczas synchronizowania hasła zastępuje istniejące hasło w chmurze.

Przy pierwszym włączeniu funkcji synchronizacji skrótów haseł, wykonuje synchronizację początkową haseł wszystkich użytkowników w zakresie. Nie można jawnie zdefiniować podzbiór haseł użytkowników, które mają być synchronizowane.

Jeśli zmienisz hasło lokalne, zaktualizowanie hasła jest zsynchronizowana, w większości przypadków w ciągu kilku minut.
Funkcji synchronizacji skrótów haseł automatycznie ponawia próby synchronizacji nie powiodło się. Jeśli wystąpi błąd podczas próby synchronizacji haseł, błąd jest rejestrowany w podglądu zdarzeń.

Synchronizacja haseł nie ma wpływu na użytkownika, który jest aktualnie zalogowany.
Zmiany hasła zsynchronizowane, który występuje, gdy użytkownik jest zalogowany do usługi w chmurze nie występuje natychmiast w bieżącej sesji usługi chmury. Jednak gdy usługę w chmurze, należy uwierzytelnić się ponownie, należy podać nowe hasło.

Użytkownik musi wprowadzić swoje poświadczenia firmy po raz drugi do uwierzytelniania w usłudze Azure AD, niezależnie od tego, czy ich zalogowany do sieci firmowej. Te wzorzec można zminimalizować, jednak jeśli użytkownik wybierze Zachowaj mnie pole wyboru (KMSI) podczas logowania. Zaznacz to pole wyboru, ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie KMSI może być włączona lub wyłączona przez administratora usługi Azure AD. Ponadto, można zmniejszyć monitów o hasło, włączając [bezproblemowe logowanie Jednokrotne](active-directory-aadconnect-sso.md) który automatycznie zaloguje się użytkowników, gdy są one na urządzeniach firmowych połączonych z siecią firmową.

> [!NOTE]
> Synchronizacja haseł jest obsługiwana tylko dla użytkownika typu obiektu w usłudze Active Directory. Nie jest obsługiwana dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis działania synchronizacji skrótów haseł
Poniżej przedstawiono szczegółowe działania synchronizacji skrótów haseł między usługi Active Directory i Azure AD.

![Przepływ szczegółowe hasła](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. Co dwie minuty agenta synchronizacji skrótów haseł na serwerze AD Connect żądań skrótów haseł przechowywanych (atrybutu unicodePwd) z kontrolera domeny za pomocą standardowej [MS DRSR](https://msdn.microsoft.com/library/cc228086.aspx) protokołu replikacji używane do synchronizowania danych między kontrolery domeny. Konto usługi musi mieć Replikacja zmian katalogów i Replikuj wszystkie zmiany katalogu AD uprawnienia (domyślnie na instalację), do uzyskania wartości skrótu hasła.
2. Przed wysłaniem, kontroler domeny szyfruje za pomocą klucza, który jest wartość skrótu hasła MD4 [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) skrót klucza sesji RPC i ziarna. Następnie wysyła wyniki do agenta synchronizacji skrótu hasła za pośrednictwem wywołania RPC. Kontroler domeny również przekazuje ziarna do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, aby agent będzie można odszyfrować koperty.
3.  Po agenta synchronizacji skrótów haseł koperty zaszyfrowane, używa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) i ziarna do wygenerowania klucza do odszyfrowywania odebranych danych do oryginalnego formatu MD4. W żadnym punkcie agenta synchronizacji skrótów haseł ma dostęp do hasła w postaci zwykłego tekstu. Użycie agenta synchronizacji skrótów haseł MD5 jest wyłącznie dla zgodności protokołu replikacji z kontrolerem domeny i jest używana tylko w środowisku lokalnym między kontrolerem domeny i agenta synchronizacji skrótów haseł.
4.  Agent synchronizacji skrótów haseł rozwija skrót hasła binarne 16-bajtowy 64 bajtów konwertując pierwszy wyznaczania wartości skrótu do 32 bajtów ciąg szesnastkowy, następnie konwertując ten ciąg z powrotem do pliku binarnego z kodowaniem UTF-16.
5.  Dodaje agenta synchronizacji skrótów haseł na ziarna użytkowników, składające się soli długości 10 bajtów do pliku binarnego 64 bajtów, aby jeszcze lepiej chronić oryginalnego wyznaczania wartości skrótu.
6.  Agent synchronizacji skrótów haseł następnie łączy skrót MD4 plus na ziarna użytkownika i danych wejściowych do [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funkcji. 1000 iteracji [HMAC SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) jest używany algorytm wyznaczania wartości skrótu kluczem. 
7.  Agent synchronizacji skrótów haseł ma wynikowy skrót 32 bajtów, łączy się zarówno na ziarna użytkownika i liczba SHA256 iteracji do niego (do użytku przez usługę Azure AD), przesyła ciąg z usługi Azure AD Connect do usługi Azure AD za pośrednictwem protokołu SSL.</br> 
8.  Gdy użytkownik spróbuje się zalogować do usługi Azure AD i wprowadza swoje hasło, hasło jest uruchamiane za pomocą tego samego MD4 + ziarna + PBKDF2 + HMAC SHA256 procesu. Jeśli wynikowy skrót pasuje do wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, użytkownik wprowadził prawidłowe hasło i jest uwierzytelniony. 

>[!Note] 
>Oryginalny skrót MD4 nie są przesyłane do usługi Azure AD. Zamiast tego są przesyłane skrót SHA256 obiektu oryginalnego wyznaczania wartości skrótu MD4. W rezultacie w przypadku wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, nie można użyć w przypadku ataków typu pass--hash w środowisku lokalnym.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Jak synchronizacja skrótów haseł współpracuje z usługą Azure Active Directory Domain Services
Można również użyć funkcji synchronizacji skrótów haseł do synchronizacji haseł w środowisku lokalnym do [usługi Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). W tym scenariuszu wystąpienia usługi Azure Active Directory Domain Services uwierzytelniani są użytkownicy usługi w chmurze za pomocą wszystkich metod dostępnych w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym. Proces ten scenariusz jest podobny do przy użyciu usługi Active Directory migracji Tool (ADMT) w środowisku lokalnym.

### <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Podczas synchronizacji haseł wersji tekstowego hasła nie jest uwidaczniany w funkcji synchronizacji skrótów haseł, do usługi Azure AD lub dowolnej z usług jest skojarzony.

Uwierzytelnianie użytkownika ma miejsce, w usłudze Azure AD, a nie względem wystąpienia usługi Active Directory w organizacji. Jeśli Twoja organizacja ma obawy dane haseł w dowolnym formularza, pozostawiając lokalnego, należy wziąć pod uwagę fakt, że dane haseł SHA256 przechowywane w usłudze Azure AD — skrót oryginalnego wyznaczania wartości skrótu MD4 — jest znacznie bezpieczniejsze niż co to jest przechowywany w usłudze Active Directory. Co więcej ponieważ nie można odszyfrować ten skrót SHA256, uniemożliwiający sprowadzony z powrotem do środowiska usługi Active Directory w firmie i przedstawiane jako prawidłowego użytkownika hasła w ataku typu pass--hash.

### <a name="password-policy-considerations"></a>Zagadnienia dotyczące zasad haseł
Istnieją dwa typy zasad haseł, których dotyczy Włączanie synchronizacji skrótów haseł:

* Zasady złożoności haseł
* Zasady wygasania haseł

#### <a name="password-complexity-policy"></a>Zasady złożoności haseł  
Po włączeniu synchronizacji skrótów haseł, zasady złożoności haseł w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym zastępują zasady złożoności w chmurze na potrzeby zsynchronizowani użytkownicy. Wszystkie prawidłowe haseł z lokalnego wystąpienia usługi Active Directory można użyć na dostęp do usług Azure AD.

> [!NOTE]
> Hasła użytkowników, które są tworzone bezpośrednio w chmurze są nadal podlega procesowi zasady dotyczące haseł, zgodnie z definicją w chmurze.

#### <a name="password-expiration-policy"></a>Zasady wygasania haseł  
Jeśli użytkownik znajduje się w zakresie synchronizacji skrótów haseł, hasło konta w chmurze jest ustawiony na *nigdy nie wygasa*.

Można nadal logować się do usługi w chmurze przy użyciu hasła zsynchronizowane, który wygasł w środowisku w środowisku lokalnym. Hasło do chmury jest aktualizowana przy następnym zmienić hasło w środowisku lokalnym.

#### <a name="account-expiration"></a>Termin wygaśnięcia konta
Jeśli Twoja organizacja używa atrybutu accountExpires jako część zarządzania kontami użytkowników, należy pamiętać, że ten atrybut nie jest zsynchronizowany z usługą Azure AD. Co w efekcie Wygasłe konta usługi Active Directory w środowisku, który został skonfigurowany do synchronizacji skrótów haseł nadal jest aktywny w usłudze Azure AD. Zaleca się, że konto wygasło akcji przepływu pracy powinny wywoływać skrypt programu PowerShell, które wyłączają konta usługi Azure AD (Użyj [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) polecenia cmdlet). Z drugiej strony gdy konto jest włączone, wystąpienia usługi Azure AD powinien zostać włączony.

### <a name="overwrite-synchronized-passwords"></a>Zastąp synchronizowanych haseł
Administrator może ręcznie zresetować hasło za pomocą programu Windows PowerShell.

W tym przypadku nowe hasło zastępuje zsynchronizowane hasło, a wszystkie zasady haseł, zdefiniowane w chmurze są stosowane do nowego hasła.

Jeśli zmienisz hasło lokalne ponownie nowe hasło jest zsynchronizowany z chmurą i zastępuje ona ręczne zaktualizowanie hasła.

Synchronizacja haseł nie ma wpływu na użytkownika platformy Azure, który jest zalogowany. Zmiany hasła zsynchronizowane, który występuje, gdy zalogowany do usługi w chmurze nie występuje natychmiast w bieżącej sesji usługi chmury. KMSI rozszerza czas trwania tej różnicy. Gdy usługa w chmurze, należy uwierzytelnić się ponownie, musisz podać nowe hasło.

### <a name="additional-advantages"></a>Dodatkowe korzyści

- Ogólnie rzecz biorąc synchronizacji skrótów haseł jest prostsza do zaimplementowania niż usługi federacyjnej. Nie wymaga żadnych dodatkowych serwerów i eliminuje zależność od usługi federacyjnej o wysokiej dostępności do uwierzytelniania użytkowników.
- Można również włączyć synchronizację skrótów haseł oprócz federacji. Może być używany jako rezerwowe, jeśli usługi federacyjnej ulegnie awarii.

## <a name="enable-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

>[!IMPORTANT]
>Jeśli migrujesz z usług AD FS (lub inne technologie federacyjnych) do synchronizacji skrótów haseł, zdecydowanie zalecamy, skorzystaj z naszego przewodnika wdrożenia są szczegółowo opublikowane [tutaj](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Password%20Hash%20Synchronization.docx).

Po zainstalowaniu usługi Azure AD Connect przy użyciu **ustawień ekspresowych** opcji synchronizacji skrótów haseł jest automatycznie włączone. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](active-directory-aadconnect-get-started-express.md).

Jeśli używasz ustawienia niestandardowe, podczas instalowania programu Azure AD Connect, synchronizacji skrótów haseł jest dostępna na stronie logowania użytkownika. Aby uzyskać więcej informacji, zobacz [instalację niestandardową programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizacja skrótów haseł i ze standardem FIPS
Jeśli serwer został zablokowany zgodnie z informacji przetwarzania Standard FIPS (Federal), MD5 jest wyłączone.

**Aby włączyć MD5 dla synchronizacji skrótów haseł, wykonaj następujące czynności:**

1. Przejdź do Sync\Bin %programfiles%\Azure AD.
2. Otwórz miiserver.exe.config.
3. Przejdź do węzła Konfiguracja/środowiska uruchomieniowego na końcu pliku.
4. Dodaj następującego węzła: `<enforceFIPSPolicy enabled="false"/>`
5. Zapisz zmiany.

Odwołanie ten fragment kodu jest, co powinno to wyglądać:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Aby uzyskać informacje o zabezpieczeniach i ze standardem FIPS, zobacz [zgodności synchronizacji haseł w usłudze AAD, szyfrowania i ze standardem FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="next-steps"></a>Kolejne kroki
* [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
* [Pobieranie planu wdrożenie krok po kroku dotyczące migracji z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
