---
title: Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacji Azure AD Connect | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z synchronizacją skrótów haseł.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: billmath
ms.openlocfilehash: bcf266813476c682d47bfd483db77f5d8b73837a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacji Azure AD Connect
Ten temat zawiera procedurę rozwiązywania problemów z synchronizacją skrótów haseł. Jeśli nie można zsynchronizować hasła, zgodnie z oczekiwaniami, można dla podzbioru użytkowników lub dla wszystkich użytkowników.

W przypadku wdrożenia usługi Azure Active Directory (Azure AD) Połącz z wersją 1.1.614.0 lub po można użyć zadania dotyczące rozwiązywania problemów w kreatorze, rozwiązywanie problemów z synchronizacją skrótów haseł:

* Jeśli masz problem, których hasła nie są zsynchronizowane, zapoznaj się [hasła nie są zsynchronizowane: Rozwiązywanie problemów przy użyciu zadania dotyczące rozwiązywania problemów](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) sekcji.

* Jeśli masz problem z pojedyncze obiekty, zapoznaj się [jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu zadania dotyczące rozwiązywania problemów](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) sekcji.

Wdrożenia z wersją 1.1.524.0 lub później, brak diagnostycznego polecenia cmdlet, które służy do rozwiązywania problemów z synchronizacją skrótów haseł:

* Jeśli masz problem, których hasła nie są zsynchronizowane, zapoznaj się [hasła nie są zsynchronizowane: Rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznych](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) sekcji.

* Jeśli masz problem z pojedyncze obiekty, zapoznaj się [jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznych](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) sekcji.

Dla wcześniejszych wersji programu Azure AD Connect wdrażania:

* Jeśli masz problem, których hasła nie są zsynchronizowane, zapoznaj się [hasła nie są zsynchronizowane: Podręcznik rozwiązywania problemów z](#no-passwords-are-synchronized-manual-troubleshooting-steps) sekcji.

* Jeśli masz problem z pojedyncze obiekty, zapoznaj się [jeden obiekt nie synchronizuje haseł: Podręcznik rozwiązywania problemów z](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) sekcji.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Hasła nie są zsynchronizowane: Rozwiązywanie problemów przy użyciu zadania dotyczące rozwiązywania problemów
Rozwiązywania problemów zadania można używać, aby dowiedzieć się, dlaczego hasła nie są zsynchronizowane.

> [!NOTE]
> Zadania dotyczące rozwiązywania problemów jest dostępna tylko w przypadku wersji Azure AD Connect 1.1.614.0 lub nowszej.

### <a name="run-the-troubleshooting-task"></a>Uruchom zadanie rozwiązywania problemów
Aby rozwiązać problemy, których hasła nie są zsynchronizowane:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom Kreatora programu Azure AD Connect.

4. Przejdź do **dodatkowe zadania** wybierz pozycję **rozwiązywanie**i kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij **uruchamianie** do rozwiązywania problemów z menu start w programie PowerShell.

6. W menu głównym wybierz **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. Wybierz z menu sub **synchronizacji skrótu hasła nie działać w ogóle**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów
Rozwiązywania problemów z zadań wykonuje następujące testy:

* Sprawdza, czy dla dzierżawy usługi Azure AD jest włączona funkcja synchronizacji skrótu hasła.

* Sprawdza, czy serwer Azure AD Connect nie jest w trybie przejściowym.

* Dla każdego istniejącego lokalnego łącznika usługi Active Directory (która odnosi się do istniejącego lasu usługi Active Directory):

   * Sprawdza, czy włączono funkcję synchronizacji skrótu hasła.
   
   * Wyszukuje hasła skrótu synchronizacji pulsu zdarzenia w dzienniku zdarzeń aplikacji systemu Windows.

   * Dla każdej domeny usługi Active Directory, w obszarze łącznik lokalnej usługi Active Directory:

      * Sprawdza, czy domena jest dostępny z serwera usługi Azure AD Connect.

      * Sprawdza, czy konta usług domenowych w usłudze Active Directory (AD DS), używane przez łącznik lokalnej usługi Active Directory ma prawidłową nazwę użytkownika, hasło i uprawnienia wymagane dla synchronizacji skrótu hasła.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet dla topologii jednej domenie lokalnej usługi Active Directory:

![Dane wyjściowe diagnostyki dla synchronizacji skrótu hasła](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalgeneral.png)

Pozostałej części tej sekcji opisano określonych wyników zwracanych przez zadań i odpowiednie problemów.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkcja synchronizacji skrótu hasła nie jest włączona.
Jeśli nie włączono synchronizacji skrótu hasła przy użyciu Kreatora programu Azure AD Connect, jest zwracany następujący błąd:

![synchronizacji skrótu hasła nie jest włączona.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Serwer systemu Azure AD Connect jest w trybie przejściowym
Jeśli serwer usługi Azure AD Connect jest w trybie przejściowym, synchronizacji skrótów haseł jest tymczasowo wyłączona i zwrócony następujący błąd:

![Serwer systemu Azure AD Connect jest w trybie przejściowym](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Brak zdarzeń pulsu synchronizacji skrótu hasła
Każdy łącznik usługi Active Directory lokalnego ma własną kanału synchronizacji skrótu hasła. Po ustanowieniu kanału synchronizacji skrótów haseł i nie ma zmiany hasła mają być synchronizowane, co 30 minut w dzienniku zdarzeń aplikacji systemu Windows zostanie wygenerowane zdarzenie pulsu (identyfikator EventId 654). Dla każdego lokalnego łącznika usługi Active Directory polecenie cmdlet wyszukuje, poszukaj pokrewnych zdarzeń pulsu w ciągu ostatnich trzech godzin. Jeśli żadne zdarzenie pulsu zostanie znaleziony, jest zwracany następujący błąd:

![Nie pulsu synchronizacji skrótu hasła Ci zdarzeń](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Usługi AD DS konto nie ma odpowiednich uprawnień
Jeśli konto usług AD DS, które jest używane przez łącznik lokalnej usługi Active Directory do synchronizacji skrótów haseł nie ma odpowiednich uprawnień, jest zwracany następujący błąd:

![Nieprawidłowe poświadczenia](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nieprawidłowa nazwa użytkownika konta usług AD DS lub hasło
Jeśli konto usług AD DS używany przez łącznik lokalnej usługi Active Directory do synchronizacji skrótów haseł ma nieprawidłową nazwą użytkownika lub hasło, jest zwracany następujący błąd:

![Nieprawidłowe poświadczenia](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu zadania dotyczące rozwiązywania problemów

Rozwiązywania problemów zadania można używać, aby określić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> Zadania dotyczące rozwiązywania problemów jest dostępna tylko w przypadku wersji Azure AD Connect 1.1.614.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki
Aby rozwiązać problemy dla obiekt określonego użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom Kreatora programu Azure AD Connect.

4. Przejdź do **dodatkowe zadania** wybierz pozycję **rozwiązywanie**i kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij **uruchamianie** do rozwiązywania problemów z menu start w programie PowerShell.

6. W menu głównym wybierz **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. Wybierz z menu sub **hasło nie jest zsynchronizowane dla określonego konta użytkownika**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów
Rozwiązywania problemów z zadań wykonuje następujące testy:

* Sprawdza stan obiektu usługi Active Directory w przestrzeni łącznika usługi Active Directory, Metaverse i Azure AD przestrzeni łącznika.

* Sprawdza, czy są reguły synchronizacji z synchronizacji skrótu hasła włączone i zastosowane do obiektu usługi Active Directory.

* Próbuje pobrać i wyświetlić wyniki ostatniej próby synchronizacji haseł dla obiekt.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet podczas rozwiązywania problemów z synchronizacji skrótów haseł dla pojedynczego obiektu:

![Dane wyjściowe diagnostyki dla synchronizacji skrótu hasła - pojedynczego obiektu](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectgeneral.png)

Pozostałej części tej sekcji opisano określonych wyników zwróconych przez polecenie cmdlet i odpowiednie problemy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Obiekt usługi Active Directory nie jest eksportowane do usługi Azure AD
synchronizacji skrótu hasła dla tego konta usługi Active Directory lokalnego kończy się niepowodzeniem, ponieważ nie ma odpowiedniego obiektu w dzierżawie usługi Azure AD. Zwrócono następujący błąd:

![Brak obiektu usługi Active Directory systemu Azure](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Użytkownik ma hasło tymczasowe
Azure AD Connect nie obsługuje obecnie synchronizowanie tymczasowego hasła z usługą Azure AD. Hasło jest traktowany jako tymczasowy Jeśli **zmienić hasło przy następnym logowaniu** opcja jest ustawiona na lokalnych użytkowników usługi Active Directory. Zwrócono następujący błąd:

![Hasło tymczasowe nie są eksportowane.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Wyniki ostatniej próby synchronizacji haseł nie są dostępne
Domyślnie program Azure AD Connect przechowuje wyniki prób synchronizacji skrótu hasła na siedem dni. Jeśli nie są dostępne dla wybranego obiektu usługi Active Directory wyniki, zwracany jest następujące ostrzeżenie:

![Dane wyjściowe diagnostyki dla pojedynczego obiektu — nie historii synchronizacji haseł](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Hasła nie są zsynchronizowane: Rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznych
Można użyć `Invoke-ADSyncDiagnostics` polecenia cmdlet, aby dowiedzieć się, dlaczego hasła nie są zsynchronizowane.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Polecenia cmdlet jest dostępna tylko dla wersji Azure AD Connect 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki
Aby rozwiązać problemy, których hasła nie są zsynchronizowane:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom polecenie `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden obiekt nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznych
Można użyć `Invoke-ADSyncDiagnostics` polecenia cmdlet, aby ustalić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Polecenia cmdlet jest dostępna tylko dla wersji Azure AD Connect 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki
Aby rozwiązać problemy, których hasła nie są zsynchronizowane dla użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z **Uruchom jako Administrator** opcji.

2. Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom następujące polecenie cmdlet:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Na przykład:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Hasła nie są zsynchronizowane: ręczne kroki rozwiązywania problemów
Wykonaj następujące kroki, aby ustalić, dlaczego hasła nie są zsynchronizowane:

1. Jest to serwer Connect w [Tryb przejściowy](active-directory-aadconnectsync-operations.md#staging-mode)? Serwer w trybie przejściowym nie synchronizować hasła.

2. Uruchom skrypt w [Pobierz stan ustawienia synchronizacji haseł](#get-the-status-of-password-sync-settings) sekcji. Udostępnia przegląd konfiguracji synchronizacji haseł.  

    ![Dane wyjściowe skryptu programu PowerShell z ustawień synchronizacji haseł](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/psverifyconfig.png)  

3. Jeśli funkcja nie jest włączona w usłudze Azure AD lub stan kanału synchronizacji nie jest włączona, należy uruchomić Kreatora instalacji Connect. Wybierz **Dostosuj opcje synchronizacji**i usuń zaznaczenie pozycji synchronizacji haseł. Ta zmiana tymczasowo wyłącza funkcję. Następnie uruchom ponownie kreatora i ponownie włączyć synchronizację haseł. Uruchom skrypt ponownie, aby sprawdzić, czy konfiguracja jest poprawna.

4. Poszukaj w dzienniku błędów. Poszukaj następujących zdarzeń, które wskazują problem:
    * Źródła: Identyfikator "Synchronizacji katalogów": 0, 611, 652, 655, jeśli te zdarzenia są widoczne masz problem z połączeniem. Komunikat dziennika zdarzeń zawiera informacje lasu, gdzie występuje problem. Aby uzyskać więcej informacji, zobacz [problem z łącznością](#connectivity problem).

5. Jeśli widzisz Brak pulsu lub pracuje zaczynającego, uruchom [wyzwolenia pełnej synchronizacji haseł wszystkich](#trigger-a-full-sync-of-all-passwords). Uruchom skrypt tylko raz.

6. Zobacz [Rozwiązywanie problemów z jednego obiektu, który nie jest synchronizowanie haseł](#one-object-is-not-synchronizing-passwords) sekcji.

### <a name="connectivity-problems"></a>Problemy z łącznością

Czy masz połączenie z usługą Azure AD?

Czy konto ma wymagane uprawnienia do odczytu skrótów haseł we wszystkich domenach? Jeśli zainstalowano Połącz przy użyciu ustawień ekspresowych, uprawnienia powinny już być poprawne. 

Jeśli używasz niestandardowej instalacji, należy ręcznie ustawić uprawnienia w następujący sposób:
    
1. Aby znaleźć konto używane przez łącznik usługi Active Directory, należy uruchomić **Menedżera usługi synchronizacji**. 
 
2. Przejdź do **łączniki**, a następnie wyszukaj lokalnego lasu usługi Active Directory, rozwiązywania problemów. 
 
3. Wybierz łącznik, a następnie kliknij przycisk **właściwości**. 
 
4. Przejdź do **nawiązać połączenia z lasu usługi Active Directory**.  
    
    ![Konto używane przez łącznik usługi Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/connectoraccount.png)  
    Zanotuj nazwę użytkownika i domeny, w której znajduje się konto.
    
5. Uruchom **użytkownicy usługi Active Directory i komputery**, a następnie sprawdź, czy konto, aby odnaleźć wcześniej ma uprawnienia wykonaj ustawiony w katalogu głównym wszystkich domen w lesie:
    * Replikować zmiany katalogu
    * Replikowanie katalogu zmienia wszystkie

6. Kontrolery domeny są osiągalne Azure AD Connect? Jeśli serwer Connect nie może połączyć do wszystkich kontrolerów domeny, należy skonfigurować **tylko Użyj preferowanego kontrolera domeny**.  
    
    ![Kontroler domeny jest używany przez łącznik usługi Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/preferreddc.png)  
    
7. Wróć do **Menedżera usługi synchronizacji** i **Konfigurowanie partycji katalogu**. 
 
8. Wybierz domenę w **Wybieranie partycji katalogu**, wybierz pozycję **korzystają z kontrolerów domeny preferowanych** pole wyboru, a następnie kliknij przycisk **Konfiguruj**. 

9. Na liście wprowadź kontrolerów domeny, które mają zostać użyte Connect do celów synchronizacji haseł. Tę samą listę służy do importowania i eksportowania również. Wykonaj te kroki dla wszystkich domen.

10. Jeśli skrypt pokazuje, że nie pulsu, uruchom skrypt [wyzwolenia pełnej synchronizacji haseł wszystkich](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden obiekt nie synchronizuje haseł: ręczne kroki rozwiązywania problemów
Umożliwia łatwe rozwiązywanie problemów z synchronizacją skrótów haseł, przeglądając stanu obiektu.

1. W **użytkownicy usługi Active Directory i komputery**, Wyszukaj użytkownika, a następnie sprawdź, czy **użytkownik musi zmienić hasło przy następnym logowaniu** pole wyboru jest wyczyszczone.  

    ![Active Directory produktywności hasła.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/adprodpassword.png)  

    Jeśli pole wyboru jest zaznaczone, należy poprosić użytkownika o zalogować się i zmienić hasło. Tymczasowe hasła nie są zsynchronizowane z usługą Azure AD.

2. Jeśli hasło jest prawidłowe w usłudze Active Directory, należy wykonać użytkownika aparatu synchronizacji. Przez użytkownika z lokalnej usługi Active Directory do usługi Azure AD, można sprawdzić, czy w obiekcie jest opisem błędu.

    a. Uruchom [Menedżera usługi synchronizacji](active-directory-aadconnectsync-service-manager-ui.md).

    b. Kliknij przycisk **łączniki**.

    c. Wybierz **łącznika usługi Active Directory** gdzie znajduje się użytkownik.

    d. Wybierz **wyszukiwania przestrzeni łącznika**.

    e. W **zakres** wybierz opcję **nazwa Wyróżniająca lub zakotwiczenia**, a następnie wprowadź pełną nazwę Wyświetlaną użytkownika, rozwiązywania problemów.

    ![Wyszukaj użytkownika w przestrzeni łącznika o nazwie Wyróżniającej](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/searchcs.png)  

    f. Użytkownik chce się dowiedzieć, a następnie kliknij przycisk Znajdź **właściwości** aby zobaczyć wszystkie atrybuty. Jeśli użytkownik nie jest w wynikach wyszukiwania, upewnij się, Twoje [reguły filtrowania](active-directory-aadconnectsync-configure-filtering.md) i upewnij się, że uruchamiasz [Zastosuj i Sprawdź zmiany](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) dla użytkownika, które mają być widoczne w Connect.

    g. Aby wyświetlić szczegóły synchronizacji haseł obiektu w poprzednim tygodniu, kliknij przycisk **dziennika**.  

    ![Szczegóły dziennika obiektu](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/csobjectlog.png)  

    Jeśli w dzienniku obiektu jest pusta, Azure AD Connect została nie można odczytać skrótów haseł z usługi Active Directory. Kontynuuj rozwiązywanie problemów z [błędów połączenia](#connectivity-errors). Jeśli widzisz innych wartości niż **Powodzenie**, zgodnie z tabelą w [Dziennik synchronizacji haseł](#password-sync-log).

    h. Wybierz **TABLE/}[%{Column/}]")** karcie i upewnij się, że tej reguły synchronizacji co najmniej jeden w **PasswordSync** kolumna jest **True**. W konfiguracji domyślnej nazwy reguły synchronizacji jest **w z usługi AD - AccountEnabled użytkownika**.  

    ![Elementy powiązane informacje o użytkowniku](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync.png)  

    i. Kliknij przycisk **właściwości obiektu Metaverse** Aby wyświetlić listę atrybutów użytkowników.  

    ![Informacje magazynie metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvpasswordsync.png)  

    Sprawdź, czy jest nie **cloudFiltered** atrybutu. Upewnij się, że oczekiwanych wartości atrybutów domeny (domainFQDN i domainNetBios).

    j. Kliknij przycisk **łączniki** kartę. Upewnij się, że widoczny łączniki do lokalnej usługi Active Directory i Azure AD.

    ![Informacje magazynie metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvconnectors.png)  

    k. Wybierz wiersz, który reprezentuje usługi Azure AD, kliknij przycisk **właściwości**, a następnie kliknij przycisk **elementy powiązane** kartę. Obiekt miejsca łącznik powinien mieć Reguła ruchu wychodzącego **PasswordSync** kolumny ustawioną **True**. W konfiguracji domyślnej nazwy reguły synchronizacji jest **Out do usługi AAD — użytkownik przyłączyć**.  

    ![Okno dialogowe właściwości obiektu obszaru łącznika](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Dziennik synchronizacji haseł
W kolumnie Stan może mieć następujące wartości:

| Stan | Opis |
| --- | --- |
| Powodzenie |Hasło zostało pomyślnie zsynchronizowane. |
| FilteredByTarget |Ustawiono hasło **użytkownik musi zmienić hasło przy następnym logowaniu**. Hasło nie zostały zsynchronizowane. |
| NoTargetConnection |Brak obiektu metaverse lub przestrzeni łącznika usługi Azure AD. |
| SourceConnectorNotPresent |Nie można odnaleźć obiektu w lokalnej przestrzeni łącznika usługi Active Directory. |
| TargetNotExportedToDirectory |Obiekt do przestrzeni łącznika usługi Azure AD nie zostały wyeksportowane. |
| MigratedCheckDetailsForMoreInfo |Wpis dziennika został utworzony przed kompilacji 1.0.9125.0 i jest wyświetlany w stanie starszej wersji. |
| Błąd |Usługa zwróciła nieznany błąd. |
| Brak informacji |Wystąpił błąd podczas próby przetworzenia partii skrótów haseł.  |
| MissingAttribute |Określone atrybuty (na przykład protokołu Kerberos wyznaczania wartości skrótu) wymagane przez usługi domenowe Azure AD nie są dostępne. |
| RetryRequestedByTarget |Określone atrybuty (na przykład protokołu Kerberos wyznaczania wartości skrótu) wymagane przez usługi domenowe Azure AD nie były wcześniej dostępne. Podejmowana jest próba, aby ponownie zsynchronizować skrót hasła użytkownika. |

## <a name="scripts-to-help-troubleshooting"></a>Skrypty pomocne podczas rozwiązywania problemów

### <a name="get-the-status-of-password-sync-settings"></a>Pobierz stan ustawienia synchronizacji haseł
```
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Wyzwalacz pełnej synchronizacji haseł wszystkich
> [!NOTE]
> Ten skrypt należy uruchomić tylko raz. Jeśli trzeba je uruchomić więcej niż jeden raz, jest coś innego problemu. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną firmy Microsoft.

Pełna synchronizacja haseł wszystkich można wywołać za pomocą następującego skryptu:

```
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
* [Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
* [Azure AD Connect Sync: Dostosowywanie opcji synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
