---
title: Azure AD SSPR z zapisywaniem zwrotnym haseł | Dokumentacja firmy Microsoft
description: Użyj usługi Azure AD i Azure AD Connect do zapisywania zwrotnego haseł do katalogu lokalnego
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 1c44b2738fb21450c5a37f715c995a3e7aecc4a6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="password-writeback-overview"></a>Omówienie funkcji zapisywania zwrotnego haseł

Dzięki funkcji zapisywania zwrotnego haseł można skonfigurować usługi Azure Active Directory (Azure AD), aby zapisywać hasła zwrotnie do lokalnej usługi Active Directory. Zapisywanie zwrotne haseł eliminuje to potrzebę konfigurowania i zarządzania rozwiązania Resetowanie hasła samoobsługi skomplikowane lokalnymi i zapewnia wygodny sposób oparte na chmurze użytkownikom resetowania swoich haseł lokalnych, wszędzie tam, gdzie są one. Zapisywanie zwrotne haseł jest składnikiem [Azure Active Directory Connect](./../connect/active-directory-aadconnect.md) czy włączone i używane przez bieżące subskrybentów Premium [wersje usługi Azure Active Directory](../active-directory-whatis.md).

Zapisywanie zwrotne haseł oferuje następujące funkcje:

* **Udostępnia opinii opóźnienie zero**: zapisywanie zwrotne haseł jest operacja synchroniczna. Użytkownicy są niezwłocznie powiadomiona, jeśli hasło nie spełnia warunków zasady lub nie można zresetować lub zmianie dowolnej przyczyny.
* **Resetowania haseł obsługuje dla użytkowników korzystających z usługi Active Directory Federation Services (AD FS) ani innych technologii federacyjnego**: Z funkcji zapisywania zwrotnego haseł, jak długo są synchronizowane konta użytkowników federacyjnych dzierżawy usługi Azure AD są w stanie Zarządzanie lokalnymi haseł usługi Active Directory z chmury.
* **Resetowania haseł obsługuje dla użytkowników korzystających z** [synchronizacji skrótu hasła](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): wykrycie usługi resetowania hasła włączenia dla synchronizacji skrótu hasła konta użytkownika zsynchronizowane, aż przywrócone zostaną ta lokalne konta i hasło chmury jednocześnie. Zapisywanie zwrotne haseł działa niezależnie od synchronizacji skrótu hasła.
* **Resetowania haseł obsługuje dla użytkowników korzystających z uwierzytelniania przekazywanego**: Z funkcji zapisywania zwrotnego haseł, tak długo, jak konta uwierzytelniania przekazywanego są synchronizowane w dzierżawie usługi Azure AD są można zarządzać ich aktywnym lokalnej Hasła katalogu w chmurze.
* **Zmiany hasła obsługuje z panelu dostępu i usługi Office 365**: gdy Sfederowane lub synchronizacji haseł użytkowników się zmieniać swoje hasła wygasły lub nie wygasły, możemy zapisywać te hasła zwrotnie do lokalnego środowiska usługi Active Directory.
* **Obsługuje funkcję zapisywania zwrotnego haseł, gdy administrator resetuje je w portalu Azure**: zawsze, gdy administrator resetuje hasło użytkownika w [portalu Azure](https://portal.azure.com), jeśli tego użytkownika jest Sfederowane lub synchronizacji haseł, zaplanujemy hasło wybiera administrator w lokalnej usługi Active Directory oraz. Ta funkcja nie jest obecnie obsługiwane w portalu administracyjnym pakietu Office.
* **Wymusza lokalnymi zasadami haseł usługi Active Directory**: gdy użytkownik resetuje hasła, firma Microsoft upewnij się, że spełnia Twoje lokalnej usługi Active Directory zasady przed możemy przekazać do tego katalogu. Ten przegląd zawiera historię, złożoności, wieku, filtry hasła i inne ograniczenia hasło zdefiniowane w lokalnej usłudze Active Directory.
* **Nie wymaga żadnych reguł zapory dla ruchu przychodzącego**: zapisywanie zwrotne haseł używa przekaźnika usługi Azure Service Bus jako podstawowy kanał komunikacyjny. Nie trzeba otworzyć żadnych portów przychodzących na zaporze, aby ta funkcja działała.
* **Nie jest obsługiwana dla kont użytkowników, które istnieją w ramach grup ochrony w lokalnej usłudze Active Directory**: Aby uzyskać więcej informacji na temat grup ochrony, zobacz [chronione kont i grup w usłudze Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Sposób działania funkcji zapisywania zwrotnego haseł

Gdy skrót federacyjnych lub hasło synchronizowanego użytkownika jest dostarczany do resetowania lub zmieniania swoich haseł w chmurze, są następujące operacje:

1. Sprawdzamy, do jakiego rodzaju hasła użytkownik nie ma. Jeśli firma Microsoft Zobacz, czy hasło jest zarządzane lokalnie:
   * Sprawdzamy, czy usługa zapisywania zwrotnego jest uruchomiona. Jeśli jest uruchomiona, nie możemy udostępnić przejść użytkownik.
   * Jeśli usługa zapisywania zwrotnego nie znajduje się w górę, możemy Monituj użytkownika, że nie można zresetować swoje hasło w tej chwili.
2. Następnie przekazuje bramy uwierzytelniania użytkownika i osiągnie **resetowania hasła** strony.
3. Użytkownik wybiera nowe hasło i potwierdza go.
4. Gdy użytkownik wybierze **przesyłania**, możemy szyfrowania z klucza symetrycznego, który został utworzony podczas instalacji funkcji zapisywania zwrotnego haseł w postaci zwykłego tekstu.
5. Po szyfrowania hasła, przeprowadzamy go w ładunku, które są wysyłane za pośrednictwem kanału HTTPS do sieci specyficznego dla dzierżawy usługi bus relay (również skonfigurowanie automatycznie podczas instalacji funkcji zapisywania zwrotnego). Ta przekazywania jest chroniony przez losowo generowane hasło, który zna instalacji lokalnej.
6. Po komunikat dociera do usługi service bus, punkt końcowy resetowania hasła automatycznie wznowiona i widzi, że ma oczekujące żądanie resetowania.
7. Przy użyciu atrybutu zakotwiczenia chmury usługi wygląda dla użytkownika. Dla tego odnośnika powiodła się:

    * Obiekt użytkownika musi istnieć w przestrzeni łącznika usługi Active Directory.
    * Obiekt użytkownika muszą być połączone z odpowiedniego obiektu metaverse (MV).
    * Obiekt użytkownika muszą być połączone z odpowiedni obiekt łącznika usługi Azure Active Directory.
    * Link z obiektu łącznika usługi Active Directory do MV musi mieć reguły synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` łącze. <br> <br>
    W przypadku wywołania polega na z chmury, korzysta z aparatu synchronizacji **cloudAnchor** atrybut do odszukania obiekt miejsca łącznika usługi Azure Active Directory. Następnie lokalizuje łącze obiektu MV, a następnie lokalizuje łącze obiektu usługi Active Directory. Ponieważ może istnieć wiele obiektów usługi Active Directory (obejmującego wiele lasów) dla tego samego użytkownika, na podstawie aparatu synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` łącze do pobrania poprawne.

    > [!Note]
    > W wyniku tej logiki hasła zapisywania zwrotnego do pracy Azure AD Connect musi być mogły komunikować się z emulator podstawowego kontrolera domeny (PDC). Jeśli musisz ręcznie włączyć usługi Azure AD Connect umożliwia nawiązywanie emulator podstawowego kontrolera domeny. Kliknij prawym przyciskiem myszy **właściwości** synchronizacji łącznika usługi Active Directory, następnie wybierz **Konfigurowanie partycji katalogu**. Z tego miejsca, wyszukaj **ustawienia połączenia kontrolera domeny** sekcji, a następnie zaznacz pole zatytułowany **korzystają z kontrolerów domeny preferowanych**. Nawet jeśli kontroler domeny preferowany nie jest emulator podstawowego kontrolera domeny, Azure AD Connect próbuje połączyć się podstawowego kontrolera domeny dla funkcji zapisywania zwrotnego haseł.

8. Gdy użytkownik zostanie znaleziony konta, możemy próba zresetowania hasła bezpośrednio w odpowiednich lasu usługi Active Directory.
9. Jeśli hasło operacji set zakończy się pomyślnie, firma Microsoft Poinformuj użytkownika swoje hasło zostało zmienione.
    > [!NOTE]
    > Jeśli hasło użytkownika jest synchronizowany z usługą Azure AD przy użyciu synchronizacji haseł, istnieje ryzyko, że lokalne zasady haseł jest mniejsze niż zasady haseł w chmurze. W takim przypadku Będziemy nadal wymusić niezależnie od zasady lokalne, a zamiast tego użyć synchronizacji skrótu hasła do synchronizowania skrótów tego hasła. Ta zasada zapewnia, czy zasady lokalne są wymuszane w chmurze, niezależnie w celu zapewnienia logowania jednokrotnego za pomocą synchronizacji haseł lub federacyjnych.

10. Jeśli hasło ustawione operacja kończy się niepowodzeniem, możemy zwrócić błąd do użytkownika i daj spróbuj ponownie. Operacja może się nie powieść, ponieważ:
    * Usługa była w dół.
    * Hasło, które są wybrane nie spełniał zasad organizacji.
    * Firma Microsoft nie może znaleźć użytkownika w lokalnej usłudze Active Directory.

    Firma Microsoft może mieć określonego komunikatu dla wielu z tych przypadkach, a następnie poinformuj użytkownika, czego mogą służyć do rozwiązania problemu.

## <a name="configure-password-writeback"></a>Konfigurowanie funkcji zapisywania zwrotnego haseł

Zalecane jest użycie funkcji automatycznej aktualizacji [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) Jeśli chcesz użyć funkcji zapisywania zwrotnego haseł.

Narzędzia DirSync i Azure AD Sync nie są już obsługiwane w sposób, aby włączyć funkcję zapisywania zwrotnego haseł. Aby uzyskać więcej informacji pomóc w przejście, zobacz [uaktualnienie z narzędzia DirSync i Azure AD Sync](../connect/active-directory-aadconnect-dirsync-deprecated.md).

W następujących krokach założono Azure AD Connect została już skonfigurowana w danym środowisku, za pomocą [Express](./../connect/active-directory-aadconnect-get-started-express.md) lub [niestandardowy](./../connect/active-directory-aadconnect-get-started-custom.md) ustawienia.

1. Aby skonfigurować i włączyć funkcję zapisywania zwrotnego haseł, zaloguj się do serwera usługi Azure AD Connect i uruchom **Azure AD Connect** Kreatora konfiguracji.
2. Na **powitalnej** wybierz pozycję **Konfiguruj**.
3. Na **dodatkowe zadania** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz **dalej**.
4. Na **nawiązywanie połączenia z usługą Azure AD** , wprowadź poświadczenia administratora globalnego, a następnie wybierz **dalej**.
5. Na **połączenie katalogów** i **jednostki Organizacyjnej i domeny** filtrowanie stron, wybierz **dalej**.
6. Na **funkcje opcjonalne** strony, zaznacz pole obok **funkcji zapisywania zwrotnego haseł** i wybierz **dalej**.
   ![Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect][Writeback]
7. Na **wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po wyświetleniu konfiguracji Zakończ wybierz **zakończenia**.

Aby typowe zadania dotyczące rozwiązywania problemów związanych z funkcji zapisywania zwrotnego haseł, zobacz sekcję [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) w naszym artykuł dotyczący rozwiązywania problemów.

## <a name="active-directory-permissions"></a>Uprawnienia usługi Active Directory

Konto określone w narzędziu Azure AD Connect musi mieć następujące elementy możesz zdefiniować znajdował się w zakresie dla usługi SSPR:

* **Resetowanie hasła** 
* **Zmień hasło** 
* **Uprawnienia do zapisu** na `lockoutTime`  
* **Uprawnienia do zapisu** na `pwdLastSet`
* **Rozszerzone prawa** w zależności:
   * Obiekt główny *każdej domeny* w danym lesie
   * Użytkownik jednostek organizacyjnych (OU) ma być w zakresie dla usługi SSPR

Jeśli nie masz pewności co konto opisane konto odnosi się do, otwórz konfigurację usługi Azure Active Directory Connect interfejsu użytkownika i wybierz **bieżącej konfiguracji widoku** opcji. Konto, które należy dodać uprawnienia do znajduje się w obszarze **synchronizacji katalogów**.

Jeśli ustawisz te uprawnienia kontom usług dla każdego lasu można Zarządzanie hasłami w imieniu kont użytkownika w tym lesie. 

> [!IMPORTANT]
> Jeśli udzielisz tych uprawnień, następnie, mimo że zapisywania zwrotnego wydaje się być skonfigurowana poprawnie, użytkownicy będą napotykać błędy podczas prób Zarządzam ich hasłami lokalnymi z chmury.
>

> [!NOTE]
> Go może potrwać godzinę lub dłużej te uprawnienia zostaną zreplikowane do wszystkich obiektów w katalogu.
>

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego haseł występują, wykonaj następujące czynności:

1. Otwórz użytkownicy usługi Active Directory i komputery przy użyciu konta mającego uprawnienia administratora w odpowiedniej domenie.
2. Z **widoku** menu, upewnij się, że **zaawansowane funkcje** jest włączona.
3. W lewym panelu kliknij prawym przyciskiem myszy obiekt, który reprezentuje katalog główny domeny, a następnie wybierz **właściwości** > **zabezpieczeń** > **zaawansowane**.
4. Z **uprawnienia** wybierz opcję **Dodaj**.
5. Wybierz konto, które uprawnienia są stosowane do (od Instalatora programu Azure AD Connect).
6. W **dotyczy** listy rozwijanej wybierz **potomne użytkownika** obiektów.
7. W obszarze **uprawnienia**, zaznacz pola dla następujących:
    * **Resetowanie hasła**
    * **Zmień hasło**
    * **Zapis lockoutTime**
    * **Zapis pwdLastSet**
8. Wybierz **Zastosuj/OK** Aby zastosować zmiany i zamknąć wszystkie otwarte okna dialogowe.

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania dotyczące funkcji zapisywania zwrotnego haseł licencjonowania

Aby uzyskać informacje na temat licencjonowania, zobacz [licencji wymagane dla funkcji zapisywania zwrotnego haseł](concept-sspr-licensing.md#licenses-required-for-password-writeback) lub następujących witryn:

* [Usługa Azure Active Directory cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Tryby uwierzytelniania lokalnego, które są obsługiwane w przypadku funkcji zapisywania zwrotnego haseł

Obsługa zapisywania zwrotnego haseł dla następujących typów hasło użytkownika:

* **Tylko w chmurze użytkowników**: funkcji zapisywania zwrotnego haseł nie ma zastosowania w takiej sytuacji, ponieważ nie istnieje żadne hasło lokalne.
* **Synchronizowane hasła użytkowników**: zapisywanie zwrotne haseł jest obsługiwana.
* **Użytkownicy federacyjni**: zapisywanie zwrotne haseł jest obsługiwana.
* **Użytkownicy uwierzytelniania przekazywanego**: zapisywanie zwrotne haseł jest obsługiwana.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Operacje użytkownika i administratora, które są obsługiwane w przypadku funkcji zapisywania zwrotnego haseł

Hasła są zapisywane w następujących sytuacjach:

* **Operacje obsługiwane przez użytkownika końcowego**
  * Wszelkie dobrowolnej samoobsługi użytkownika końcowego zmienić operacji hasła
  * Wszelkie życie samoobsługi użytkownika końcowego zmienić operacji hasła, na przykład, datę wygaśnięcia hasła
  * Dowolnego użytkownika samoobsługowego resetowania hasła wynikająca z [portal resetowania hasła](https://passwordreset.microsoftonline.com)
* **Administrator obsługiwane operacje**
  * Wszelkie dobrowolnej samoobsługi administratora Zmień operacji hasła
  * Wszelkie życie samoobsługi administratora Zmień operacji hasła, na przykład, datę wygaśnięcia hasła
  * Wszelkie administratora samoobsługowego resetowania hasła wynikająca z [portal resetowania hasła](https://passwordreset.microsoftonline.com)
  * Wszystkie hasła przez użytkownika końcowego inicjowanych przez administratora zresetować z [portalu Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Operacje użytkownika i administratora, które nie są obsługiwane dla funkcji zapisywania zwrotnego haseł

Hasła są *nie* zapisywane w następujących sytuacjach:

* **Operacje nieobsługiwane przez użytkownika końcowego**
  * Każdy użytkownik końcowy Resetowanie własnych haseł przy użyciu programu PowerShell w wersji 1, w wersji 2 lub interfejsu API programu Azure AD Graph
* **Administrator nieobsługiwanej operacji**
  * Wszystkie hasła przez użytkownika końcowego inicjowanych przez administratora zresetować z [portalu zarządzania usługi Office](https://portal.office.com)
  * Wszystkie hasła przez użytkownika końcowego inicjowanych przez administratora zresetować z programu PowerShell w wersji 1, w wersji 2 lub interfejsu API programu Azure AD Graph

Pracujemy nad usunąć te ograniczenia, ale nie mamy określonych osi czasu, który firma Microsoft może udostępniać jeszcze.

## <a name="password-writeback-security-model"></a>Model zabezpieczeń zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest usługą wysokim poziomie zabezpieczeń. Aby upewnić się, że Twoje informacje są chronione, możemy Włącz model warstwowy czterech zabezpieczeń, opisaną w następujący sposób:

* **Przekaźnik usługi service bus specyficznego dla dzierżawy**
  * Po skonfigurowaniu usługi skonfigurowanie specyficznego dla dzierżawy usługi przekazywania magistrali, która jest chroniona przez losowo generowany silne hasło, które firma Microsoft nigdy nie ma dostępu do.
* **Klucz szyfrowania zablokowaną, kryptograficznie silne hasło**
  * Po utworzeniu przekaźnik magistrali usług, utworzymy silnego klucza symetrycznego używanego do szyfrowania hasła, ponieważ pochodzi przez sieć. Ten klucz znajduje się tylko w magazynie tajny firmy w chmury, co znacznie jest zablokowany i inspekcji, podobnie jak inne hasło w katalogu.
* **Branżowy standard zabezpieczeń TLS (Transport Layer)**
  1. Gdy hasło Resetowanie lub zmienianie operacja odbywa się w chmurze, możemy pobrać hasła w postaci zwykłego tekstu i szyfrowania kluczem publicznym.
  2. Który możemy umieścić w komunikat protokołu HTTPS, który był wysyłany za pośrednictwem kanału szyfrowanego przy użyciu certyfikatów Microsoft SSL do Twojej przekaźnik magistrali usługi.
  3. Po odebraniu komunikatu przez magistralę usługi agenta lokalnie budzi i przeprowadza uwierzytelnianie usługi service bus przy użyciu silnego hasła, który wcześniej został wygenerowany.
  4. Agent lokalnymi przejmuje zaszyfrowanego komunikatu i odszyfrowuje ją przy użyciu klucza prywatnego, możemy wygenerowany.
  5. Agent lokalnymi podejmuje próbę ustawienia hasła za pośrednictwem interfejsu API UstawianieHasła AD DS. Ten krok jest to, co pozwala wymusić zasady haseł lokalnej usługi Active Directory, tak (na przykład złożoności, wieku, Historia i filtrów) w chmurze.
* **Zasady wygasania wiadomości** 
  * Jeśli wiadomość znajduje się w usłudze service bus, ponieważ usługi lokalnej nie działa, upłynie limit czasu i zostanie usunięta po kilku minutach. Limit czasu i usuwanie wiadomości zwiększa się jeszcze bardziej zabezpieczeń.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisywania zwrotnego haseł

Po przesłaniu resetowania hasła, żądanie zresetowania przechodzi przez kilka kroków szyfrowania, zanim zostanie odebrane w środowisku lokalnym. Te kroki szyfrowania zapewnienia maksymalnej niezawodności i zabezpieczeń. Są opisane w następujący sposób:

* **Krok 1: Hasła szyfrowania kluczem RSA 2048-bitowego**: po przesłaniu przez użytkownika hasła można zapisać zwrotnie do lokalnej przesłanych samego hasła są szyfrowane za pomocą klucza RSA 2048-bitowego.
* **Krok 2: Pakiet poziom szyfrowania z usługą GCM AES**: cały pakiet, hasło i wymagane metadane są szyfrowane przy użyciu usługi GCM AES. Szyfrowanie uniemożliwia każda osoba mająca bezpośredni dostęp do podstawowego kanału magistrali usług wyświetlanie i manipulowanie zawartością.
* **Krok 3: Cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL**: cała komunikacja z magistrali usług odbywa się w kanału SSL/TLS. Szyfrowanie chroni zawartość od nieautoryzowanych stron trzecich.
* **Automatyczne Przerzucanie klucza co sześć miesięcy**: co sześć miesięcy lub zapisywania zwrotnego haseł w każdej chwili jest wyłączona i następnie ponownie włączona na Azure AD Connect. Firma Microsoft automatycznie są przerzucane wszystkie klucze do zapewnienia bezpieczeństwa i zabezpieczeń maksymalnej.

### <a name="password-writeback-bandwidth-usage"></a>Wykorzystanie przepustowości zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest usługą niskiej przepustowości, która tylko wysyła żądania do agenta lokalnie w następujących okolicznościach:

* Dwa komunikaty są wysyłane, gdy funkcja jest włączone lub wyłączone za pośrednictwem usługi Azure AD Connect.
* Jeden komunikat jest wysyłane co pięć minut jako pulsu usługi dla tak długo, jak usługa jest uruchomiona.
* Dwa komunikaty są wysyłane poszczególnych czas nowe hasło jest przesyłane:
    * Pierwszy komunikat o błędzie jest żądaniem do wykonania tej operacji.
    * Drugi komunikat zawiera wynik operacji i są wysyłane w następujących okolicznościach:
        * Zawsze przesłaniu nowego hasła podczas użytkownika samoobsługowego resetowania hasła.
        * Zawsze przesłaniu nowego hasła podczas operacji zmiany hasła użytkownika.
        * Zawsze przesłaniu nowego hasła podczas resetowania (tylko z portali administratora platformy Azure) hasła użytkownika inicjowanych przez administratora.

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Rozmiar każdego z opisanych powyżej wiadomości jest zazwyczaj poniżej 1 KB. Nawet w przypadku obciążeń extreme sama usługa zapisywania zwrotnego haseł zajmuje kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła i rozmiar wiadomości jest to mała przepustowość możliwości zapisywania zwrotnego jest za mały do ma zauważalnego wpływu.

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect"
