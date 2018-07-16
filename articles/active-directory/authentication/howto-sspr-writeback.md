---
title: Usługa Azure AD SSPR z zapisywaniem zwrotnym haseł | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: fce92845591f20f7f2e9cff1a856e0904629255b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054798"
---
# <a name="password-writeback-overview"></a>Omówienie funkcji zapisywania zwrotnego haseł

Z zapisywaniem zwrotnym haseł można skonfigurować usługi Azure Active Directory (Azure AD) do zapisywania zwrotnego haseł usługi Active Directory w środowisku lokalnym. Zapisywanie zwrotne haseł eliminują konieczność konfigurowania i zarządzania nią skomplikowane lokalne rozwiązanie resetowania haseł i zapewnia wygodny sposób oparte na chmurze użytkownikom Resetowanie swoich haseł lokalnych, gdziekolwiek się znajdują. Zapisywanie zwrotne haseł jest składnikiem [Azure Active Directory Connect](./../connect/active-directory-aadconnect.md) , włączona i używane przez bieżących subskrybentów Premium [usługi Azure Active Directory w wersjach](../fundamentals/active-directory-whatis.md).

Zapisywanie zwrotne haseł oferuje następujące funkcje:

* **Zapewnia informacje zwrotne zero-delay**: zapisywanie zwrotne haseł jest operacja synchroniczna. Użytkownicy są niezwłocznie powiadomiona, czy swoje hasło nie spełnia warunków zasady lub może nie można zresetować zmieniona z jakiegokolwiek powodu.
* **Resetowania haseł obsługuje dla użytkowników korzystających z usługi Active Directory Federation Services (AD FS) lub inne technologie Federacji**: Z zapisywaniem zwrotnym haseł, tak długo, jak synchronizowanych kont użytkowników federacyjnych do dzierżawy usługi Azure AD są w stanie Zarządzam ich hasłami usługi Active Directory lokalnych z chmury.
* **Resetowania haseł obsługuje dla użytkowników korzystających z** [synchronizacji skrótów haseł](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): gdy usługa resetowania hasła wykryje, że konto użytkownika synchronizowanego jest włączone dla synchronizacji skrótów haseł, możemy teraz zresetować ta konta lokalne i haseł w chmurze równocześnie. Zapisywanie zwrotne haseł nie zależy od synchronizacji skrótów haseł.
* **Resetowania haseł obsługuje dla użytkowników korzystających z uwierzytelniania przekazywanego**: Z zapisywaniem zwrotnym haseł, tak długo, jak konta uwierzytelniania przekazywanego są synchronizowane w dzierżawie usługi Azure AD są w stanie zarządzać aktywnym ich w środowisku lokalnym Directory haseł w chmurze.
* **Hasło obsługuje zmieni się z panelu dostępu i usługi Office 365**: gdy Sfederowane lub użytkownicy z synchronizowanymi hasłami pochodzą konieczności zmiany haseł wygasł lub nie wygasły, napiszemy tych haseł z powrotem do środowiska lokalnego usługi Active Directory.
* **Obsługuje funkcję zapisywania zwrotnego haseł, gdy administrator resetuje je w witrynie Azure portal**: zawsze, gdy administrator resetuje hasło użytkownika w [witryny Azure portal](https://portal.azure.com), czy ten użytkownik jest Sfederowane synchronizowanymi hasłami ustawimy hasło Administrator wybiera się w lokalnej usłudze Active Directory oraz. Ta funkcja nie jest obecnie obsługiwana w portalu administracyjnym pakietu Office.
* **Wymusza zasadami haseł usługi Active Directory w środowisku lokalnym**: po użytkownik resetuje hasła, firma Microsoft, upewnij się, że spełnia zasady lokalnej usługi Active Directory, zanim jesteśmy zaangażowani do tego katalogu. Ten przegląd zawiera sprawdzanie historii, złożoności, wieku, filtry hasła i inne ograniczenia haseł, które zostały zdefiniowane w lokalnej usłudze Active Directory.
* **Nie wymaga żadnych reguł zapory dla ruchu przychodzącego**: zapisywanie zwrotne haseł używa usługi Azure Service Bus relay jako podstawowy kanał komunikacyjny. Nie trzeba otwierać żadnych portów przychodzących w zaporze, aby ta funkcja działała.
* **Nie jest obsługiwana dla kont użytkowników, które istnieją w ramach grup ochrony w usłudze Active Directory w środowisku lokalnym**: Aby uzyskać więcej informacji na temat grup ochrony, zobacz [chronione kont i grup w usłudze Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Sposób działania funkcji zapisywania zwrotnego haseł

Gdy skrót federacyjnego lub hasło użytkownika synchronizowanego pochodzi Resetowanie lub zmienianie swoich haseł w chmurze, są następujące operacje:

1. Możemy sprawdzić, aby sprawdzić, jakiego rodzaju hasła użytkownika. Jeśli widać, że hasło jest zarządzane lokalnie:
   * Możemy sprawdzić, czy usługa zapisywania zwrotnego jest uruchomiony. Jeśli jest uruchomiony, możemy zezwolić użytkownikom na kontynuować.
   * Jeśli usługa zapisywania zwrotnego nie znajduje się w górę, możemy poinformować użytkownika, nie można zresetować swoje hasło teraz.
2. Następnie przekazuje bramy uwierzytelniania użytkownika i osiągnie **Resetuj hasło** strony.
3. Użytkownik wybiera nowe hasło i potwierdza go.
4. Gdy użytkownik wybierze **przesyłania**, firma Microsoft szyfrowania kluczem symetrycznym, który został utworzony podczas procesu instalacji funkcji zapisywania zwrotnego haseł w postaci zwykłego tekstu.
5. Po szyfrowania hasła, możemy dołączyć ładunek, który jest wysyłany za pośrednictwem kanału protokołu HTTPS do usługi specyficznym dla dzierżawy usługi service bus relay (którym zostanie również skonfigurowana dla Ciebie podczas procesu instalacji funkcji zapisywania zwrotnego). To relay są chronione przez losowo wygenerowane hasło, który zna instalacji lokalnych.
6. Po komunikat dociera do usługi service bus, resetowania haseł punktu końcowego budzi i automatycznie widzi, że ma on oczekujące żądanie resetowania.
7. Za pomocą atrybutu zakotwiczenia chmury usługi wygląda dla użytkownika. Dla tego obszaru wyszukiwania została wykonana pomyślnie:

    * Obiekt użytkownika muszą istnieć w przestrzeni łącznika usługi Active Directory.
    * Obiekt użytkownika muszą być połączone z odpowiedniego obiektu metaverse (MV).
    * Obiekt użytkownika muszą być połączone z odpowiedni obiekt łącznika usługi Azure Active Directory.
    * Link z obiektu łącznika usługi Active Directory, aby MV musi mieć reguły synchronizacji `Microsoft.InfromADUserAccountEnabled.xxx` łącze. <br> <br>
    Po wywołaniu pochodzą z chmury, korzysta z aparatu synchronizacji **atrybutu cloudAnchor** atrybutu, aby wyszukać obiektu przestrzeni łącznika usługi Azure Active Directory. Następnie lokalizuje łącze obiektu MV, a następnie lokalizuje łącze obiektu usługi Active Directory. Ponieważ może istnieć wiele obiektów usługi Active Directory (obejmującego wiele lasów) dla tego samego użytkownika, aparat synchronizacji opiera się na `Microsoft.InfromADUserAccountEnabled.xxx` link, aby wybrać właściwy.

    > [!Note]
    > W wyniku tę logikę dla hasła funkcja zapisywania zwrotnego działała usługa Azure AD Connect musi mieć możliwość komunikowania się z emulator podstawowego kontrolera domeny (PDC). Jeśli musisz ręcznie włączyć, możesz połączyć program Azure AD Connect z emulatorem podstawowego kontrolera domeny. Kliknij prawym przyciskiem myszy **właściwości** łącznika synchronizacji usługi Active Directory, następnie wybierz pozycję **Konfigurowanie partycji katalogu**. Z tego miejsca poszukaj **ustawienia połączenia kontrolera domeny** sekcji, a następnie zaznacz pole o nazwie **korzystają z kontrolerów domeny preferowanych**. Nawet jeśli kontroler domeny preferowanych nie jest to emulator podstawowego kontrolera domeny, program Azure AD Connect próbuje nawiązać połączenie podstawowego kontrolera domeny na potrzeby zapisywania zwrotnego haseł.

8. Po użytkownik konta zostanie znaleziony, kolejna próba zresetowania hasła bezpośrednio w odpowiedniej lasu usługi Active Directory.
9. W przypadku powodzenia operacji Ustaw hasło o użytkownika, że ich hasło zostało zmienione.
    > [!NOTE]
    > Jeśli hasło użytkownika jest zsynchronizowany z usługą Azure AD przy użyciu synchronizacji haseł, istnieje prawdopodobieństwo, że w lokalnych zasadach haseł jest mniejsze niż zasady haseł w chmurze. W takim przypadku nadal wymuszane niezależnie od zasady lokalnej, a zamiast tego użyć synchronizacji skrótów haseł do synchronizowania skrótów tego hasła. Te zasady zapewniają, że zasad w środowisku lokalnym jest wymuszone w chmurze, niezależnie od tego w celu zapewnienia logowania jednokrotnego za pomocą synchronizacji haseł lub federacji.

10. Jeśli hasło ustawione operacja kończy się niepowodzeniem, możemy zwrócenie błędu przez użytkownika i poinformuj ich, spróbuj ponownie. Operacja może zakończyć się niepowodzeniem, ponieważ:
    * Usługa nie działał.
    * Hasło, które są wybrane nie spełniło zasad organizacji.
    * Firma Microsoft nie może znaleźć użytkownika w lokalnej usłudze Active Directory.

    Firma Microsoft może mieć szczegółowy komunikat o błędzie dla wielu z tych przypadków, a następnie poinformuj użytkownika, co można zrobić, aby rozwiązać ten problem.

## <a name="configure-password-writeback"></a>Konfigurowanie zapisywania zwrotnego haseł

Firma Microsoft zaleca, aby używać funkcji automatycznej aktualizacji [program Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) Jeśli chcesz użyć funkcji zapisywania zwrotnego haseł.

Narzędzia DirSync i Azure AD Sync nie są już obsługiwane jako sposób włączania zapisywania zwrotnego haseł. Aby uzyskać więcej informacji ułatwić przejście, zobacz [uaktualnienie z narzędzia DirSync i Azure AD Sync](../connect/active-directory-aadconnect-dirsync-deprecated.md).

W następujących krokach założono, program Azure AD Connect została już skonfigurowana w danym środowisku, za pomocą [Express](./../connect/active-directory-aadconnect-get-started-express.md) lub [niestandardowe](./../connect/active-directory-aadconnect-get-started-custom.md) ustawienia.

1. Aby skonfigurować i włączyć funkcję zapisywania zwrotnego haseł, zaloguj się do serwera usługi Azure AD Connect i uruchomić **program Azure AD Connect** Kreatora konfiguracji.
2. Na **powitalnej** wybierz opcję **Konfiguruj**.
3. Na **dodatkowe zadania** wybierz opcję **Dostosowywanie opcji synchronizacji**, a następnie wybierz pozycję **dalej**.
4. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **dalej**.
5. Na **łączenie katalogów** i **domen/jednostek organizacyjnych** filtrowanie stron, wybierz **dalej**.
6. Na **funkcje opcjonalne** strony, zaznacz pole obok pozycji **funkcji zapisywania zwrotnego haseł** i wybierz **dalej**.
   ![Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect][Writeback]
7. Na **wszystko gotowe do skonfigurowania** wybierz opcję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po wyświetleniu konfiguracji Zakończ wybierz **zakończenia**.

Typowe zadania dotyczące rozwiązywania problemów związanych z zapisywaniem zwrotnym haseł, zobacz sekcję dotyczącą [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) w naszym artykuł dotyczący rozwiązywania problemów.

## <a name="active-directory-permissions"></a>Uprawnienia usługi Active Directory

Konto określone w narzędziu Azure AD Connect muszą mieć następujące elementy ustawione, jeśli mają być uwzględnione w zakresie funkcji samoobsługowego resetowania HASEŁ:

* **Resetowanie hasła** 
* **Zmień hasło** 
* **Uprawnienia do zapisu** na `lockoutTime`  
* **Uprawnienia do zapisu** na `pwdLastSet`
* **Rozszerzone prawa** systemie:
   * Obiekt główny *każdej domeny* w tym lesie
   * Użytkownik jednostki organizacyjne (OU) mają być uwzględnione w zakresie funkcji samoobsługowego resetowania HASEŁ

Jeśli nie masz pewności co konta opisem konta odwołuje się do, Otwórz do usługi Azure Active Directory Connect interfejsu użytkownika konfiguracji i wybierz **wyświetlanie bieżącej konfiguracji** opcji. Konta, które należy dodać uprawnienia do znajduje się w obszarze **synchronizację katalogów**.

Jeśli możesz ustawić te uprawnienia kontom usług ma każdego lasu może Zarządzanie hasłami w imieniu kont użytkownika, w tym lesie. 

> [!IMPORTANT]
> Jeśli nie udzielisz tych uprawnień, następnie, mimo że zapisywania zwrotnego wydaje się być prawidłowo skonfigurowany, użytkownicy będą napotykać błędy podczas prób zarządzania swoich haseł lokalnych w chmurze.
>

> [!NOTE]
> Może potrwać do godziny lub dłużej tych uprawnień można replikować do wszystkich obiektów w katalogu.
>

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego haseł występuje, wykonaj następujące czynności:

1. Otwórz użytkownicy usługi Active Directory i komputerów za pomocą konta mającego uprawnienia administratora w odpowiedniej domenie.
2. Z **widoku** menu, upewnij się, że **zaawansowane funkcje** jest włączona.
3. W lewym panelu, kliknij prawym przyciskiem myszy obiekt, który reprezentuje katalog główny domeny, a następnie wybierz pozycję **właściwości** > **zabezpieczeń** > **zaawansowane**.
4. Z **uprawnienia** zaznacz **Dodaj**.
5. Wybierz konto, które uprawnienia są stosowane do (z konfiguracji usługi Azure AD Connect).
6. W **dotyczy** listy rozwijanej wybierz **potomne użytkownika** obiektów.
7. W obszarze **uprawnienia**, zaznacz następujące pola:
    * **Resetowanie hasła**
    * **Zmień hasło**
    * **Zapis lockoutTime**
    * **Zapis pwdLastSet**
8. Wybierz **Zastosuj/OK** Aby zastosować zmiany i zamknąć okna dialogowe.

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania licencyjne dla funkcji zapisywania zwrotnego haseł

Aby uzyskać informacje na temat licencjonowania, zobacz [licencje wymagane do zapisywania zwrotnego haseł](concept-sspr-licensing.md) lub następujące witryny:

* [Usługa Azure Active Directory, cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Tryby uwierzytelniania w środowisku lokalnym, które są obsługiwane na potrzeby zapisywania zwrotnego haseł

Pomoc techniczna funkcji zapisywania zwrotnego haseł dla następujących typów hasło użytkownika:

* **Użytkownicy tylko w chmurze**: funkcja zapisywania zwrotnego haseł nie ma zastosowania w takiej sytuacji, ponieważ nie ma żadnego hasła lokalnego.
* **Synchronizację haseł użytkowników**: zapisywanie zwrotne haseł jest obsługiwana.
* **Federacyjna użytkowników**: zapisywanie zwrotne haseł jest obsługiwana.
* **Użytkownicy uwierzytelnianie przekazujących**: zapisywanie zwrotne haseł jest obsługiwana.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Operacje użytkowników i administratorów, które są obsługiwane w przypadku zapisywania zwrotnego haseł

Hasła są zapisywane z powrotem w następujących sytuacjach:

* **Operacje obsługiwane przez użytkownika końcowego**
  * Hasło operacji zmiany dowolnego dobrowolnej samoobsługi użytkowników końcowych
  * Wszelkie życie samoobsługi użytkowników końcowych zmienić operacji hasła, na przykład, datę wygaśnięcia hasła
  * Wszelkie przez użytkownika końcowego samoobsługowego resetowania haseł pochodzący z [portal resetowania haseł](https://passwordreset.microsoftonline.com)
* **Operacje obsługiwane administratora**
  * Hasło operacji zmiany dowolnego dobrowolnej samoobsługi administratora
  * Wszelkie życie samoobsługi administratora Zmień operacji hasła, na przykład, datę wygaśnięcia hasła
  * Wszelkie administratora samoobsługowego resetowania haseł pochodzący z [portal resetowania haseł](https://passwordreset.microsoftonline.com)
  * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła z [witryny Azure portal](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Działania użytkowników i administratorów, które nie są obsługiwane na potrzeby zapisywania zwrotnego haseł

Hasła są *nie* zapisywane z powrotem w następujących sytuacjach:

* **Operacje nieobsługiwane przez użytkownika końcowego**
  * Każdy użytkownik końcowy resetowania ich własnych haseł przy użyciu programu PowerShell w wersji 1, wersja 2 lub interfejsu API programu Graph usługi Azure AD
* **Nieobsługiwana administrator operacji**
  * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła z [portalu zarządzania usługi Office](https://portal.office.com)
  * Wszelkie inicjowanych przez administratorów użytkownik końcowy resetowania hasła programu PowerShell w wersji 1, wersja 2 lub interfejsu API programu Graph usługi Azure AD

Pracujemy nad usunąć te ograniczenia, ale nie ma określonej osi czasu, który możemy jeszcze udostępnić.

## <a name="password-writeback-security-model"></a>Model zabezpieczeń funkcji zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest wysoce bezpieczna usługa. Aby upewnić się, że Twoje informacje są chronione, możemy włączyć model zabezpieczeń warstwy 4, który jest opisany w następujący sposób:

* **Specyficznym dla dzierżawy usługi service bus relay**
  * Po skonfigurowaniu usługi skonfigurujemy specyficznym dla dzierżawy usługi Service bus relay, który jest chroniony przez losowo generowany silne hasło, które firma Microsoft nigdy nie ma dostępu do.
* **Zablokowane, silną kryptograficznie hasło klucza szyfrowania**
  * Po utworzeniu usługi Service bus relay, możemy utworzyć silnego klucza symetrycznego używanego do szyfrowania hasła, ponieważ pochodzi przewodowo. Ten klucz znajduje się tylko w firmie magazynu wpisów tajnych w chmurze, co intensywnie jest zablokowana i inspekcji, podobnie jak każde inne hasło w katalogu.
* **Branżowy standard zabezpieczeń TLS (Transport Layer)**
  1. Gdy hasło Resetowanie lub zmienianie operacja odbywa się w chmurze, możemy pobrać hasło w postaci zwykłego tekstu i szyfrowania kluczem publicznym.
  2. Firma Microsoft, umieść do wiadomości protokołu HTTPS, która jest wysyłana za pośrednictwem szyfrowanego kanału za pomocą certyfikatów SSL firmy Microsoft do swojej usługi Service bus relay.
  3. Po odebraniu wiadomości w usłudze Service bus Twojego lokalnego agenta zostanie wznowiona i uwierzytelnia się z usługą service bus przy użyciu silnego hasła, który wcześniej został wygenerowany.
  4. Agent środowiska lokalnego przejmuje zaszyfrowanego komunikatu i odszyfrowuje ją przy użyciu klucza prywatnego, firma Microsoft wygenerowana.
  5. Lokalnego agenta podejmuje próbę ustawienia hasła, za pośrednictwem interfejsu API usługi AD DS SetPassword. Ten krok jest, co pozwala na do wymuszania zasad hasło lokalne usługi Active Directory (na przykład złożoności, wieku, Historia i filtrów) w chmurze.
* **Zasady wygasania wiadomości** 
  * Jeśli komunikat znajduje się w usłudze Service bus, ponieważ Twoja usługa lokalna jest wyłączona, upłynie limit czasu i zostanie usunięta po kilku minutach. Zwiększa bezpieczeństwo jeszcze bardziej, limit czasu i usuwanie wiadomości.

### <a name="password-writeback-encryption-details"></a>Szczegóły szyfrowania zapisywania zwrotnego haseł

Po użytkownik prześle resetowania haseł, żądanie zresetowania przechodzi przez kilka kroków szyfrowania przed premierą go w środowisku w środowisku lokalnym. Te kroki szyfrowania upewnij się, usługa maksymalna niezawodności i skutecznych zabezpieczeniach. Zostały one opisane w następujący sposób:

* **Krok 1: Hasła szyfrowania za pomocą klucza RSA 2048-bitowego**: po użytkownik przesyła hasła można zapisać zwrotnie w lokalnych, przesłane samego hasła są szyfrowane przy użyciu 2048-bitowego klucza RSA.
* **Krok 2: Poziom pakietu szyfrowania przy użyciu usługi GCM AES**: cały pakiet, hasło, a także wymagane metadane są szyfrowane przy użyciu usługi GCM AES. Szyfrowanie uniemożliwia każda osoba mająca bezpośredni dostęp do kanału usługi ServiceBus źródłowego wyświetlanie i manipulowanie zawartością.
* **Krok 3: Cała komunikacja odbywa się za pośrednictwem protokołu TLS/SSL**: cała komunikacja z magistrali usług odbywa się w kanału SSL/TLS. To szyfrowanie zabezpiecza zawartości nieautoryzowanym innych firm.
* **Automatyczne Przerzucanie klucza co sześć miesięcy**: co sześć miesięcy lub zapisywania zwrotnego haseł w każdej chwili jest wyłączona i ponownie włączyć w usłudze Azure AD Connect. Firma Microsoft automatycznie przejdą wszystkie klucze do zapewnienia bezpieczeństwa i zabezpieczeń usługi maksymalnej.

### <a name="password-writeback-bandwidth-usage"></a>Wykorzystanie przepustowości zapisywania zwrotnego haseł

Zapisywanie zwrotne haseł jest usługą o niskiej przepustowości, która tylko wysyła żądania do lokalnego agenta w następujących okolicznościach:

* Dwa komunikaty są wysyłane, gdy ta funkcja jest włączone lub wyłączone za pomocą usługi Azure AD Connect.
* Jeden komunikat jest wysyłany po co pięć minut jako pulsu usługi, aby uzyskać, tak długo, jak usługa jest uruchomiona.
* Dwa komunikaty są wysyłane w każdym przesłaniu nowego hasła czasu:
    * Pierwszy komunikat to żądanie do wykonania tej operacji.
    * Drugi komunikat zawiera wynik operacji i są wysyłane w następujących okolicznościach:
        * Każdym przesłaniu nowego hasła podczas resetowania hasła użytkownika.
        * Każdym przesłaniu nowego hasła podczas operacji zmiany hasła użytkownika.
        * Każdym przesłaniu nowego hasła podczas użytkownika inicjowanych przez administratora resetowania hasła (tylko z portale administracyjne Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Zagadnienia dotyczące rozmiaru i przepustowości wiadomości

Rozmiar każdego komunikatu opisanych powyżej jest zwykle poniżej 1 KB. Nawet w ekstremalnych obciążeń usługi zapisywania zwrotnego haseł, sama zużywa kilka kilobitów na sekundę przepustowości. Ponieważ każdy komunikat jest wysyłany w czasie rzeczywistym, tylko wtedy, gdy jest to wymagane przez operację aktualizacji hasła, a ponieważ tak mały rozmiar komunikatu przepustowości, funkcji zapisywania zwrotnego jest zbyt mała, aby ma zauważalnego wpływu.

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect"
