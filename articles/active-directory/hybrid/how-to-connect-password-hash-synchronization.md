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
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 146fdc3ca2af708a96e6b9a604493eb63c2e6530
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916380"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync
Ten artykuł zawiera informacje potrzebne do synchronizacji haseł użytkowników z lokalnym wystąpieniem usługi Active Directory do wystąpienia usługi Azure Active Directory (Azure AD) oparte na chmurze.

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Domeny usługi Active Directory są przechowywane hasła w postaci reprezentację wartości skrótu hasła rzeczywisty użytkownik. Wartość skrótu jest wynikiem jednokierunkowe funkcji matematycznych ( *algorytmem wyznaczania wartości skrótu*). Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. Skrótu hasła nie można użyć w celu zalogowania się do sieci lokalnej.

Aby zsynchronizować hasła, synchronizacja programu Azure AD Connect wyodrębnia usługi skrótów haseł z lokalnym wystąpieniem usługi Active Directory. Przetwarzanie zapewnienia dodatkowego bezpieczeństwa jest stosowany do skrótu hasła, zanim jest zsynchronizowany z usługą uwierzytelniania usługi Azure Active Directory. Hasła są synchronizowane na poszczególnych użytkowników, a także w porządku chronologicznym.

Przepływ danych rzeczywisty proces synchronizacji skrótów haseł jest podobny do synchronizacji danych użytkownika. Jednak synchronizowanych haseł częściej niż w oknie Synchronizacja katalogu standard innych atrybutów. Proces synchronizacji skrótów haseł jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Podczas synchronizowania hasła zastępuje istniejące hasło w chmurze.

Przy pierwszym włączeniu funkcji synchronizacji skrótów haseł, wykonuje synchronizację początkową haseł wszystkich użytkowników w zakresie. Nie można jawnie zdefiniować podzbiór haseł użytkowników, które mają być synchronizowane.

Jeśli zmienisz hasło lokalne, zaktualizowanie hasła jest zsynchronizowana, w większości przypadków w ciągu kilku minut.
Funkcji synchronizacji skrótów haseł automatycznie ponawia próby synchronizacji nie powiodło się. Jeśli wystąpi błąd podczas próby synchronizacji haseł, błąd jest rejestrowany w podglądu zdarzeń.

Synchronizacja haseł nie ma wpływu na użytkownika, który jest aktualnie zalogowany.
Zmiany hasła zsynchronizowane, który występuje, gdy użytkownik jest zalogowany, usługi w chmurze nie występuje natychmiast w bieżącej sesji usługi chmury. Jednak gdy usługę w chmurze, należy uwierzytelnić się ponownie, należy podać nowe hasło.

Użytkownik musi wprowadzić swoje poświadczenia firmy po raz drugi do uwierzytelniania w usłudze Azure AD, niezależnie od tego, czy ich zalogowany do sieci firmowej. Ten wzorzec można zminimalizować, jednak jeśli użytkownik wybierze Zachowaj mnie (KMSI) pole wyboru przy logowaniu. Zaznacz to pole wyboru, ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie KMSI może być włączona lub wyłączona przez administratora usługi Azure AD. Ponadto, można zmniejszyć monitów o hasło, włączając [bezproblemowe logowanie Jednokrotne](how-to-connect-sso.md), który automatycznie zaloguje się użytkowników, gdy są one na urządzeniach firmowych połączonych z siecią firmową.

> [!NOTE]
> Synchronizacja haseł jest obsługiwana tylko dla użytkownika typu obiektu w usłudze Active Directory. Nie jest obsługiwana dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis działania synchronizacji skrótów haseł
W poniższej sekcji opisano, szczegółowe, jak działa synchronizacja skrótów haseł między usługi Active Directory i Azure AD.

![Przepływ szczegółowe hasła](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Co dwie minuty agenta synchronizacji skrótów haseł dla żądań serwera AD Connect przechowywane wartości skrótów haseł (atrybutu unicodePwd) z kontrolera domeny.  To żądanie jest za pomocą standardowej [MS DRSR](https://msdn.microsoft.com/library/cc228086.aspx) protokołu replikacji używane do synchronizowania danych między kontrolery domeny. Konto usługi musi mieć Replikacja zmian katalogów i Replikuj wszystkie zmiany katalogu AD uprawnienia (domyślnie na instalację), do uzyskania wartości skrótu hasła.
2. Przed wysłaniem, kontroler domeny szyfruje za pomocą klucza, który jest wartość skrótu hasła MD4 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) skrót klucza sesji RPC i ziarna. Następnie wysyła wyniki do agenta synchronizacji skrótu hasła za pośrednictwem wywołania RPC. Kontroler domeny również przekazuje ziarna do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, aby agent będzie można odszyfrować koperty.
3.  Po agenta synchronizacji skrótów haseł koperty zaszyfrowane, używa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) i ziarna do wygenerowania klucza do odszyfrowywania odebranych danych do oryginalnego formatu MD4. Agent synchronizacji skrótów haseł nigdy nie ma dostępu do hasła w postaci zwykłego tekstu. Użycie agenta synchronizacji skrótów haseł MD5 jest wyłącznie dla zgodności protokołu replikacji z kontrolerem domeny i jest używana tylko w środowisku lokalnym między kontrolerem domeny i agenta synchronizacji skrótów haseł.
4.  Agent synchronizacji skrótów haseł rozwija skrót hasła binarne 16-bajtowy 64 bajtów konwertując pierwszy wyznaczania wartości skrótu do 32 bajtów ciąg szesnastkowy, następnie konwertując ten ciąg z powrotem do pliku binarnego z kodowaniem UTF-16.
5.  Dodaje agenta synchronizacji skrótów haseł na ziarna użytkowników, składające się soli długości 10 bajtów do pliku binarnego 64 bajtów, aby jeszcze lepiej chronić oryginalnego wyznaczania wartości skrótu.
6.  Agent synchronizacji skrótów haseł następnie łączy skrót MD4 plus na ziarna użytkownika i danych wejściowych do [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funkcji. 1000 iteracji [HMAC SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) kluczem algorytmu wyznaczania wartości skrótu są używane. 
7.  Agent synchronizacji skrótów haseł ma wynikowy skrót 32 bajtów, łączy się zarówno na ziarna użytkownika i liczba SHA256 iteracji do niego (do użytku przez usługę Azure AD), przesyła ciąg z usługi Azure AD Connect do usługi Azure AD za pośrednictwem protokołu SSL.</br> 
8.  Gdy użytkownik spróbuje się zalogować do usługi Azure AD i wprowadza swoje hasło, hasło jest uruchamiane za pomocą tego samego MD4 + ziarna + PBKDF2 + HMAC SHA256 procesu. Jeśli wynikowy skrót pasuje do wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, użytkownik wprowadził prawidłowe hasło i jest uwierzytelniony. 

>[!Note] 
>Oryginalny skrót MD4 nie są przesyłane do usługi Azure AD. Zamiast tego są przesyłane skrót SHA256 obiektu oryginalnego wyznaczania wartości skrótu MD4. W rezultacie w przypadku wyznaczania wartości skrótu, przechowywane w usłudze Azure AD, nie można użyć w przypadku ataków typu pass--hash w środowisku lokalnym.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Jak synchronizacja skrótów haseł współpracuje z usługą Azure Active Directory Domain Services
Można również użyć funkcji synchronizacji skrótów haseł do synchronizacji haseł w środowisku lokalnym do [usługi Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). W tym scenariuszu wystąpienia usługi Azure Active Directory Domain Services uwierzytelniani są użytkownicy usługi w chmurze za pomocą wszystkich metod dostępnych w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym. Proces ten scenariusz jest podobny do przy użyciu usługi Active Directory migracji Tool (ADMT) w środowisku lokalnym.

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
Jeśli użytkownik znajduje się w zakresie synchronizacji skrótów haseł, hasło konta w chmurze jest ustawiony na *nigdy nie wygasa*.

Można nadal logować się do usługi w chmurze przy użyciu hasła zsynchronizowane, który wygasł w środowisku w środowisku lokalnym. Hasło do chmury jest aktualizowana przy następnym zmienić hasło w środowisku lokalnym.

#### <a name="account-expiration"></a>Termin wygaśnięcia konta
Jeśli Twoja organizacja używa atrybutu accountExpires jako część zarządzania kontami użytkowników, ten atrybut nie jest zsynchronizowany z usługą Azure AD. Co w efekcie Wygasłe konta usługi Active Directory w środowisku, który został skonfigurowany do synchronizacji skrótów haseł nadal jest aktywny w usłudze Azure AD. Zaleca się, że konto wygasło akcji przepływu pracy powinny wywoływać skrypt programu PowerShell, które wyłączają konta usługi Azure AD (Użyj [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) polecenia cmdlet). Z drugiej strony gdy konto jest włączone, wystąpienia usługi Azure AD powinien zostać włączony.

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
>Jeśli migrujesz z usług AD FS (lub inne technologie federacyjnych) do synchronizacji skrótów haseł, zdecydowanie zalecamy, skorzystaj z naszego przewodnika wdrożenia są szczegółowo opublikowane [tutaj](https://aka.ms/adfstophsdpdownload).

Po zainstalowaniu usługi Azure AD Connect przy użyciu **ustawień ekspresowych** opcji synchronizacji skrótów haseł jest automatycznie włączone. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md).

Jeśli używasz ustawienia niestandardowe, podczas instalowania programu Azure AD Connect, synchronizacji skrótów haseł jest dostępna na stronie logowania użytkownika. Aby uzyskać więcej informacji, zobacz [instalację niestandardową programu Azure AD Connect](how-to-connect-install-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

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

Aby uzyskać informacje o zabezpieczeniach i ze standardem FIPS, zobacz [synchronizacji skrótów haseł usługi Azure AD, szyfrowania i zgodności ze standardem FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Kolejne kroki
* [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Pobieranie planu wdrożenie krok po kroku dotyczące migracji z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
