---
title: Rozwiązywanie problemów z synchronizacją skrótów haseł z usługą Azure AD Connect sync | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z synchronizacją skrótów haseł.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60383264"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł z usługą Azure AD Connect sync

Ten temat zawiera procedurę rozwiązywania problemów z synchronizacją skrótów haseł. Nie można zsynchronizować hasła, zgodnie z oczekiwaniami, może być dla podzbioru użytkowników lub dla wszystkich użytkowników.

Dla wdrożenia usługi Azure Active Directory (Azure AD) Connect wersji 1.1.614.0 lub później należy użyć zadania rozwiązywania problemów w kreatorze do rozwiązywania problemów z synchronizacją skrótów haseł:

* Jeśli masz problem, gdy brak synchronizowanych haseł, zobacz [Brak synchronizowanych haseł: Rozwiązywanie problemów za pomocą zadania rozwiązywania problemów](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) sekcji.

* Jeśli masz problem z poszczególnych obiektów, zobacz [jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów za pomocą zadania rozwiązywania problemów](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) sekcji.

Wdrożenia z wersją w 1.1.524.0 lub nowszy, jest diagnostycznych polecenia cmdlet, które służy do rozwiązywania problemów z synchronizacją skrótów haseł:

* Jeśli masz problem, gdy brak synchronizowanych haseł, zobacz [Brak synchronizowanych haseł: Rozwiązywanie problemów za pomocą polecenia cmdlet diagnostyki](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) sekcji.

* Jeśli masz problem z poszczególnych obiektów, zobacz [jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów za pomocą polecenia cmdlet diagnostyki](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) sekcji.

Dla starszych wersji wdrożenia usługi Azure AD Connect:

* Jeśli masz problem, gdy brak synchronizowanych haseł, zobacz [Brak synchronizowanych haseł: ręczne kroki rozwiązywania problemów](#no-passwords-are-synchronized-manual-troubleshooting-steps) sekcji.

* Jeśli masz problem z poszczególnych obiektów, zobacz [jeden obiekt nie synchronizuje haseł: ręczne kroki rozwiązywania problemów](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) sekcji.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Brak synchronizowanych haseł: Rozwiązywanie problemów za pomocą zadania rozwiązywania problemów

Aby ustalić, dlaczego Brak synchronizowanych haseł, można użyć zadania dotyczące rozwiązywania problemów.

> [!NOTE]
> Zadanie dotyczące rozwiązywania problemów jest dostępna tylko dla wersji Azure AD Connect 1.1.614.0 lub nowszej.

### <a name="run-the-troubleshooting-task"></a>Uruchom zadanie dotyczące rozwiązywania problemów

Aby rozwiązać problemy, w których brak synchronizowanych haseł:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom Kreatora programu Azure AD Connect.

4. Przejdź do **dodatkowe zadania** wybierz opcję **rozwiązywanie**i kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij **Uruchom** do rozwiązywania problemów z menu start w programie PowerShell.

6. W menu głównym wybierz **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. W podmenu, wybierz **synchronizacji skrótów haseł nie działa na wszystkich**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów

Zadanie dotyczące rozwiązywania problemów wykonuje następujące testy:

* Weryfikuje, włączenia funkcji synchronizacji skrótów haseł dla dzierżawy usługi Azure AD.

* Weryfikuje, że serwer programu Azure AD Connect nie jest w trybie przejściowym.

* Dla każdego istniejącego środowiska lokalnego łącznika usługi Active Directory (odnoszące się do istniejącego lasu usługi Active Directory):

   * Weryfikuje, że włączona jest funkcja synchronizacji skrótów haseł.
   
   * Wyszukuje hasła skrótu zdarzenia pulsu synchronizacji w dziennikach zdarzeń aplikacji Windows.

   * Dla każdej domeny usługi Active Directory, w obszarze łącznika usługi Active Directory w środowisku lokalnym:

      * Sprawdza, czy domena jest dostępny z serwera usługi Azure AD Connect.

      * Sprawdza, czy konta usługi Active Directory Domain Services (AD DS), używane przez lokalny łącznik usługi Active Directory ma prawidłową nazwę użytkownika, hasło i uprawnienia wymagane do synchronizacji skrótów haseł.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet dla jednej domeny lokalnej usługi Active Directory topologii:

![Dane wyjściowe diagnostyki do synchronizacji skrótów haseł](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

W pozostałej części tej sekcji opisano określone wyniki, które są zwracane przez zadanie i odpowiednie problemów.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>nie włączono funkcji synchronizacji skrótów haseł

Jeśli synchronizacja skrótów haseł nie zostały włączone za pomocą Kreatora programu Azure AD Connect, zwracany jest następujący błąd:

![Synchronizacja skrótów haseł nie jest włączona.](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Serwer usługi Azure AD Connect jest w trybie przejściowym

Jeśli serwer programu Azure AD Connect jest w trybie przejściowym, synchronizacji skrótów haseł jest tymczasowo wyłączona i zwrócony następujący błąd:

![Serwer usługi Azure AD Connect jest w trybie przejściowym](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Żadne zdarzenia pulsu synchronizacji skrótu hasła

Każdy lokalny łącznik usługi Active Directory ma swój własny kanału synchronizacji skrótów haseł. Po ustanowieniu kanału synchronizacji skrótów haseł i nie ma zmiany hasła do synchronizacji, to zdarzenie pulsu (identyfikator EventId 654) jest generowany co 30 minut w dzienniku zdarzeń aplikacji Windows. Dla każdego łącznika usługi Active Directory w środowisku lokalnym polecenie cmdlet przeszukuje, poszukaj pokrewnych zdarzeń pulsu w ciągu ostatnich trzech godzin. Jeśli zostanie znalezione żadne zdarzenie pulsu, jest zwracany następujący błąd:

![Nie serce synchronizacji skrótu hasła zapewniała zdarzeń](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Usługi AD DS konto nie ma odpowiednich uprawnień

Gdy konto usług AD DS, który jest używany przez lokalny łącznik usługi Active Directory do synchronizacji skrótów haseł nie ma odpowiednich uprawnień, zostanie zwrócony następujący błąd:

![Nieprawidłowe poświadczenia](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Niepoprawna nazwa użytkownika konta usług AD DS lub hasło

Jeśli konto usług AD DS, używane przez łącznik usługi Active Directory w środowisku lokalnym do synchronizacji skrótów haseł jest niepoprawna nazwa użytkownika lub hasło, jest zwracany następujący błąd:

![Nieprawidłowe poświadczenia](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów za pomocą zadania rozwiązywania problemów

Zadania dotyczące rozwiązywania problemów można użyć, aby ustalić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> Zadanie dotyczące rozwiązywania problemów jest dostępna tylko dla wersji Azure AD Connect 1.1.614.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy dla obiektu określonego użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom Kreatora programu Azure AD Connect.

4. Przejdź do **dodatkowe zadania** wybierz opcję **rozwiązywanie**i kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij **Uruchom** do rozwiązywania problemów z menu start w programie PowerShell.

6. W menu głównym wybierz **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. W podmenu, wybierz **hasło nie jest zsynchronizowane dla określonego konta użytkownika**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów

Zadanie dotyczące rozwiązywania problemów wykonuje następujące testy:

* Sprawdza stan obiektu usługi Active Directory, w obszarze łącznika usługi Active Directory, Metaverse i Azure AD, obszar łączników.

* Weryfikuje, że nie istnieją reguły synchronizacji i synchronizacji skrótów haseł włączone i zastosowane do obiektu usługi Active Directory.

* Próbuje pobrać i wyświetlić wyniki ostatniej próby synchronizacji haseł dla obiektu.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet podczas rozwiązywania problemów z synchronizacją skrótów haseł dla pojedynczego obiektu:

![Dane wyjściowe diagnostyki do synchronizacji skrótów haseł — pojedynczy obiekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

W pozostałej części tej sekcji opisano konkretnych wyników zwróconych przez polecenie cmdlet i odpowiednie problemów.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Obiekt usługi Active Directory nie są eksportowane do usługi Azure AD

Synchronizacja skrótów haseł dla tego konta usługi Active Directory w środowisku lokalnym nie powiedzie się, ponieważ nie ma odpowiedniego obiektu w dzierżawie usługi Azure AD. Zwrócony następujący błąd:

![Brak obiektu usługi Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Użytkownik ma hasło tymczasowe

Obecnie usługa Azure AD Connect nie obsługuje synchronizacji haseł tymczasowych z usługą Azure AD. Hasło jest uznawane za tymczasowe Jeśli **zmienić hasło przy następnym logowaniu** opcja jest ustawiona na lokalnego użytkownika usługi Active Directory. Zwrócony następujący błąd:

![Hasło tymczasowe nie są eksportowane.](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Wyniki ostatniej próby synchronizacji haseł nie są dostępne

Domyślnie program Azure AD Connect są przechowywane wyniki prób synchronizacji skrótów haseł przez siedem dni. Jeśli żadne wyniki nie są dostępne dla wybranego obiektu usługi Active Directory, zwracany jest następujące ostrzeżenie:

![Dane wyjściowe diagnostyki dla pojedynczego obiektu — Brak historii synchronizacji haseł](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Brak synchronizowanych haseł: Rozwiązywanie problemów za pomocą polecenia cmdlet diagnostyki

Możesz użyć `Invoke-ADSyncDiagnostics` polecenia cmdlet, aby ustalić, dlaczego Brak synchronizowanych haseł.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Polecenia cmdlet jest dostępna tylko dla wersji Azure AD Connect 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy, w których brak synchronizowanych haseł:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom polecenie `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów za pomocą polecenia cmdlet diagnostyki

Możesz użyć `Invoke-ADSyncDiagnostics` polecenia cmdlet, aby ustalić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Polecenia cmdlet jest dostępna tylko dla wersji Azure AD Connect 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy, w których brak synchronizowanych haseł dla użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom następujące polecenie cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Na przykład:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Brak synchronizowanych haseł: ręczne kroki rozwiązywania problemów

Wykonaj następujące kroki, aby ustalić, dlaczego Brak synchronizowanych haseł:

1. Jest serwer programu Connect w [Tryb przejściowy](how-to-connect-sync-staging-server.md)? Serwer w trybie przejściowym nie synchronizuje hasła.

2. Uruchom skrypt w [Pobierz stan ustawienia synchronizacji haseł](#get-the-status-of-password-sync-settings) sekcji. Daje Przegląd konfigurowania synchronizacji haseł.  

    ![Dane wyjściowe skryptu programu PowerShell z ustawienia synchronizacji haseł](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Ta funkcja nie jest włączona w usłudze Azure AD lub stan kanału synchronizacji nie jest włączona, należy uruchomić Kreatora instalacji programu Connect. Wybierz **Dostosowywanie opcji synchronizacji**i usuń zaznaczenie pozycji synchronizacji haseł. Ta zmiana powoduje tymczasowe wyłączenie tej funkcji. Następnie uruchom ponownie kreatora i ponownie włączyć synchronizację haseł. Uruchom skrypt ponownie, aby sprawdzić, czy konfiguracja jest prawidłowa.

4. Poszukaj w dzienniku błędów. Zwróć uwagę na następujące zdarzenia, które wskazują problem:
    * Źródło: Identyfikator "Synchronizacji katalogów": 0, 611, 652, 655, jeśli widzisz te zdarzenia, masz problem z połączeniem. Komunikat dziennika zdarzeń zawiera informacje lasu, gdzie występuje problem. Aby uzyskać więcej informacji, zobacz [problem z połączeniem](#connectivity problem).

5. Jeśli widzisz Brak pulsu lub nic pracy, uruchom [wyzwolić pełną synchronizację haseł wszystkich](#trigger-a-full-sync-of-all-passwords). Uruchom skrypt tylko raz.

6. Zobacz Rozwiązywanie problemów z jednego obiektu, który nie jest synchronizowany z sekcji hasła.

### <a name="connectivity-problems"></a>Problemy z łącznością

Czy masz połączenie z usługą Azure AD?

Czy konto ma wymagane uprawnienia do odczytu skrótów haseł we wszystkich domenach? Po zainstalowaniu Połącz przy użyciu ustawień ekspresowych uprawnienia powinny już być poprawne. 

Jeśli używasz niestandardowej instalacji, należy ręcznie ustawić uprawnienia, wykonując następujące czynności:
    
1. Aby znaleźć konto używane przez łącznik usługi Active Directory, uruchom **Menedżera usługi synchronizacji**. 
 
2. Przejdź do **łączników**, a następnie wyszukaj lasu usługi Active Directory w środowisku lokalnym, rozwiązywania problemów. 
 
3. Wybierz łącznik a następnie kliknij przycisk **właściwości**. 
 
4. Przejdź do **nawiązać połączenie z lasu usługi Active Directory**.  
    
    ![Konto używane przez łącznik usługi Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Zanotuj nazwę użytkownika i domeny, w której znajduje się konto.
    
5. Rozpocznij **użytkownicy usługi Active Directory i komputery**, a następnie sprawdź, czy znaleziono w wcześniej konto ma uprawnienia postępuj zgodnie z zestawu w katalogu głównym wszystkich domen w lesie:
    * Replikuj zmiany katalogu
    * Replikacja katalogu zmienia wszystkie

6. Kontrolery domeny są dostępne przy użyciu usługi Azure AD Connect? Jeśli serwer programu Connect nie może połączyć się do wszystkich kontrolerów domeny, należy skonfigurować **tylko używać kontrolera domeny preferowanych**.  
    
    ![Kontroler domeny używany przez łącznik usługi Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Wróć do **Menedżera usługi synchronizacji** i **Konfigurowanie partycji katalogu**. 
 
8. Wybierz swoją domenę na platformie **wybierz partycje katalogu**, wybierz opcję **korzystają z kontrolerów domeny preferowanych** pole wyboru, a następnie kliknij przycisk **Konfiguruj**. 

9. Na liście wprowadź kontrolerów domeny, które Connect powinniśmy skorzystać w celu synchronizacji haseł. Tę samą listę służy do importowania i eksportowania także. Wykonaj te kroki dla Twoich domen.

10. Jeśli skrypt pokazuje, że brak pulsu, uruchom skrypt w [wyzwolić pełną synchronizację haseł wszystkich](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden obiekt nie synchronizuje haseł: ręczne kroki rozwiązywania problemów

Umożliwia łatwe rozwiązywanie problemów z synchronizacją skrótów haseł, sprawdzając stan obiektu.

1. W **użytkownicy usługi Active Directory i komputery**, Wyszukaj użytkownika i sprawdź, czy **użytkownik musi zmienić hasło przy następnym logowaniu** pole wyboru jest wyczyszczone.  

    ![Aktywne hasła produktywność w katalogu](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Jeśli pole wyboru jest zaznaczone, należy poprosić użytkownika o Zaloguj się i zmienić hasło. Haseł tymczasowych nie są zsynchronizowane z usługą Azure AD.

2. Jeśli hasło wydaje się prawidłowe w usłudze Active Directory, postępuj zgodnie z użytkownikiem w aparacie synchronizacji. Wykonując czynności użytkownika z usługi Active Directory w środowisku lokalnym, do usługi Azure AD, możesz zobaczyć, czy obiekt jest opisem błędu.

    a. Rozpocznij [Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui.md).

    b. Kliknij przycisk **łączników**.

    c. Wybierz **łącznika usługi Active Directory** gdzie znajduje się użytkownik.

    d. Wybierz **wyszukiwania obszaru łącznika**.

    e. W **zakres** wybierz opcję **nazwy Wyróżniającej lub zakotwiczenia**, a następnie wprowadź pełną nazwę Wyświetlaną użytkownika, rozwiązywania problemów.

    ![Wyszukaj użytkownika w obszarze łącznika z nazwą domeny](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Znajdź użytkownika, czego szukasz, a następnie kliknij przycisk **właściwości** aby zobaczyć wszystkie atrybuty. Jeśli użytkownik nie jest w wynikach wyszukiwania, sprawdź swoje [reguły filtrowania](how-to-connect-sync-configure-filtering.md) i upewnij się, że uruchomieniu [Zastosuj i Sprawdź zmiany](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) użytkownika są wyświetlane w programie Connect.

    g. Aby wyświetlić szczegóły synchronizacji haseł obiektu dla zeszłego tygodnia, kliknij **dziennika**.  

    ![Szczegóły dziennika obiektu](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Jeśli w dzienniku obiektu jest puste, program Azure AD Connect została nie można odczytać skrótów haseł z usługi Active Directory. Kontynuuj rozwiązywanie problemów z błędami łączności. Jeśli jakakolwiek inna wartość niż **Powodzenie**, zapoznaj się z tabelą w [Dziennik synchronizacji haseł](#password-sync-log).

    h. Wybierz **pochodzenie** kartę i upewnić się, że tej reguły synchronizacji co najmniej jeden w **PasswordSync** kolumna jest **True**. W konfiguracji domyślnej, nazwa reguły synchronizacji to **w z usługi AD - AccountEnabled użytkownika**.  

    ![Pochodzenie informacji o użytkowniku](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kliknij przycisk **właściwości obiektu Metaverse** Aby wyświetlić listę atrybutów użytkowników.  

    ![Informacje o magazynie metaverse](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Sprawdź, czy nie **cloudFiltered** atrybut. Upewnij się, że oczekiwane wartości atrybutów domeny (domainFQDN i domainNetBios).

    j. Kliknij przycisk **łączników** kartę. Upewnij się, czy widzisz łączników, aby zarówno w lokalnej usłudze Active Directory a usługą Azure AD.

    ![Informacje o magazynie metaverse](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Zaznacz wiersz, który reprezentuje usługę Azure AD, kliknij przycisk **właściwości**, a następnie kliknij przycisk **pochodzenie** kartę. Obiektu przestrzeni łącznika powinien zawierać regułę dla ruchu wychodzącego w **PasswordSync** kolumna ustawiona **True**. W konfiguracji domyślnej, nazwa reguły synchronizacji to **poziomie w celu AAD — użytkownik przyłączyć**.  

    ![Okno dialogowe właściwości obiektu obszaru łącznika](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Dziennik synchronizacji haseł

W kolumnie Stan może mieć następujące wartości:

| Stan | Opis |
| --- | --- |
| Powodzenie |Hasło zostało pomyślnie zsynchronizowane. |
| FilteredByTarget |Hasło jest ustawione na **użytkownik musi zmienić hasło przy następnym logowaniu**. Hasło nie zostało zsynchronizowane. |
| NoTargetConnection |Brak obiektu w obiekcie metaverse lub w obszarze łącznika usługi Azure AD. |
| SourceConnectorNotPresent |Nie można odnaleźć obiektu w obszarze łącznika usługi Active Directory w środowisku lokalnym. |
| TargetNotExportedToDirectory |Obiekt w obszarze łącznika usługi Azure AD nie została wyeksportowana. |
| MigratedCheckDetailsForMoreInfo |Wpis dziennika został utworzony przed kompilacją 1.0.9125.0 i jest wyświetlany w stanie starszej wersji. |
| Błąd |Usługa zwróciła nieznany błąd. |
| Nieznane |Wystąpił błąd podczas próby przetworzenia partii skrótów haseł.  |
| MissingAttribute |Określone atrybuty (na przykład protokołu Kerberos skrót) wymagane przez usługi domenowe Azure AD nie są dostępne. |
| RetryRequestedByTarget |Określone atrybuty (na przykład protokołu Kerberos skrót) wymagane przez usługi domenowe Azure AD nie były wcześniej dostępne. Zostanie podjęta próba ponowną synchronizację skrótów haseł przez użytkownika. |

## <a name="scripts-to-help-troubleshooting"></a>Skrypty pomagające w rozwiązywaniu problemów

### <a name="get-the-status-of-password-sync-settings"></a>Pobierz stan ustawienia synchronizacji haseł

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Wyzwalacz pełną synchronizację wszystkich haseł

> [!NOTE]
> Ten skrypt należy uruchomić tylko raz. Jeśli musisz uruchomić go jeden raz, jest coś innego problemu. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną firmy Microsoft.

Możesz wyzwolić pełną synchronizację wszystkich haseł za pomocą następującego skryptu:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Kolejne kroki

* [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](how-to-connect-password-hash-synchronization.md)
* [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)