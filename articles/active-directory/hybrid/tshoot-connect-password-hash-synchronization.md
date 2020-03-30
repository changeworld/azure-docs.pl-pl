---
title: Rozwiązywanie problemów z synchronizacją skrótów haseł za pomocą synchronizacji usługi Azure AD Connect | Dokumenty firmy Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253548"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł za pomocą usługi synchronizacji programu Azure AD Connect

W tym temacie przedstawiono instrukcje dotyczące rozwiązywania problemów z synchronizacją skrótów haseł. Jeśli hasła nie są synchronizowane zgodnie z oczekiwaniami, może to być dla podzbioru użytkowników lub dla wszystkich użytkowników.

W przypadku wdrożenia usługi Azure Active Directory (Azure AD) Connect w wersji 1.1.614.0 lub później użyj zadania rozwiązywania problemów w kreatorze, aby rozwiązać problemy z synchronizacją skrótów haseł:

* Jeśli masz problem, w którym nie hasła są synchronizowane, zapoznaj się [z Brak haseł są synchronizowane: rozwiąż problem za pomocą sekcji zadań rozwiązywania problemów.](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)

* Jeśli masz problem z poszczególnymi obiektami, zapoznaj się [z One obiekt nie synchronizuje haseł: rozwiąż problem za pomocą sekcji zadań rozwiązywania problemów.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)

W przypadku wdrażania w wersji 1.1.524.0 lub nowszej istnieje diagnostyczne polecenie cmdlet, którego można użyć do rozwiązywania problemów z synchronizacją skrótów haseł:

* Jeśli masz problem, w którym nie hasła są synchronizowane, zapoznaj się [z Brak haseł są synchronizowane: rozwiąż problem przy użyciu sekcji polecenia cmdlet diagnostyczne.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Jeśli masz problem z poszczególnych obiektów, odnoszą się do [One obiekt nie synchronizuje hasła: rozwiąż problem przy użyciu sekcji polecenia cmdlet diagnostyczne.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)

W przypadku starszych wersji wdrożenia usługi Azure AD Connect:

* Jeśli masz problem, w którym nie hasła są synchronizowane, zapoznaj się [z Sekcji Nie hasła są synchronizowane: ręczne rozwiązywanie problemów](#no-passwords-are-synchronized-manual-troubleshooting-steps) sekcji.

* Jeśli masz problem z poszczególnymi obiektami, zapoznaj się [z sekcją One object nie synchronizuje haseł: ręczne kroki rozwiązywania problemów.](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Żadne hasła nie są synchronizowane: rozwiązywanie problemów przy użyciu zadania rozwiązywania problemów

Za pomocą zadania rozwiązywania problemów można ustalić, dlaczego nie są synchronizowane żadne hasła.

> [!NOTE]
> Zadanie rozwiązywania problemów jest dostępne tylko dla usługi Azure AD Connect w wersji 1.1.614.0 lub nowszej.

### <a name="run-the-troubleshooting-task"></a>Uruchamianie zadania rozwiązywania problemów

Aby rozwiązać problemy, w których nie są synchronizowane żadne hasła:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z opcją **Uruchom jako administrator.**

2. Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .

3. Uruchom kreatora usługi Azure AD Connect.

4. Przejdź do strony **Zadania dodatkowe,** wybierz pozycję **Rozwiązywanie problemów**i kliknij przycisk **Dalej**.

5. Na stronie Rozwiązywanie problemów kliknij przycisk **Uruchom,** aby uruchomić menu rozwiązywania problemów w programie PowerShell.

6. W menu głównym wybierz pozycję **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. W menu podrzędnym wybierz **opcję Synchronizacja skrótów haseł w ogóle nie działa**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Opis wyników zadania rozwiązywania problemów

Zadanie rozwiązywania problemów wykonuje następujące kontrole:

* Sprawdza, czy funkcja synchronizacji skrótu haseł jest włączona dla dzierżawy usługi Azure AD.

* Sprawdza, czy serwer usługi Azure AD Connect nie jest w trybie przemieszczania.

* Dla każdego istniejącego lokalnego łącznika usługi Active Directory (który odpowiada istniejącemu lasowi usługi Active Directory):

   * Sprawdza, czy funkcja synchronizacji skrótu hasła jest włączona.
   
   * Wyszukuje zdarzenia pulsu synchronizacji skrótu hasła w dziennikach zdarzeń aplikacji systemu Windows.

   * Dla każdej domeny usługi Active Directory w ramach lokalnego łącznika usługi Active Directory:

      * Sprawdza, czy domena jest osiągalna z serwera usługi Azure AD Connect.

      * Sprawdza, czy konta Usług domenowych Active Directory (AD DS) używane przez lokalny łącznik usługi Active Directory mają poprawną nazwę użytkownika, hasło i uprawnienia wymagane do synchronizacji skrótów haseł.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet dla topologii usługi Active Directory z jedną domeną w środowisku lokalnym:

![Dane wyjściowe diagnostyczne synchronizacji skrótów haseł](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

W pozostałej części tej sekcji opisano konkretne wyniki, które są zwracane przez zadanie i odpowiednie problemy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkcja synchronizacji skrótu hasła nie jest włączona

Jeśli synchronizacja skrótów haseł nie została włączona przy użyciu kreatora usługi Azure AD Connect, zwracany jest następujący błąd:

![synchronizacja skrótu hasła nie jest włączona](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Serwer usługi Azure AD Connect jest w trybie przemieszczania

Jeśli serwer usługi Azure AD Connect jest w trybie przemieszczania, synchronizacja skrótów haseł jest tymczasowo wyłączona i zwracany jest następujący błąd:

![Serwer usługi Azure AD Connect jest w trybie przemieszczania](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Brak zdarzeń pulsu synchronizacji skrótu hasła

Każdy lokalny łącznik usługi Active Directory ma swój własny kanał synchronizacji skrótów haseł. Po ustanowieniu kanału synchronizacji skrótu hasła i nie ma żadnych zmian hasła do zsynchronizowania, zdarzenie pulsu (EventId 654) jest generowany raz na 30 minut w obszarze Dziennika zdarzeń aplikacji systemu Windows. Dla każdego lokalnego łącznika usługi Active Directory polecenie cmdlet wyszukuje odpowiednie zdarzenia pulsu w ciągu ostatnich trzech godzin. Jeśli nie zostanie znalezione żadne zdarzenie pulsu, zwracany jest następujący błąd:

![Brak zdarzenia synchronizacji skrótu hasła](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Konto usług AD DS nie ma poprawnych uprawnień

Jeśli konto usług AD DS używane przez lokalny łącznik usługi Active Directory do synchronizowania skrótów haseł nie ma odpowiednich uprawnień, zwracany jest następujący błąd:

![Niepoprawne poświadczenia](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło konta usług AD DS

Jeśli konto usług AD DS używane przez lokalny łącznik usługi Active Directory do synchronizowania skrótów haseł ma nieprawidłową nazwę użytkownika lub hasło, zwracany jest następujący błąd:

![Niepoprawne poświadczenia](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden obiekt nie synchronizuje haseł: rozwiązywanie problemów przy użyciu zadania rozwiązywania problemów

Za pomocą zadania rozwiązywania problemów można ustalić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> Zadanie rozwiązywania problemów jest dostępne tylko dla usługi Azure AD Connect w wersji 1.1.614.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchamianie polecenia cmdlet diagnostyki

Aby rozwiązać problemy z określonym obiektem użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z opcją **Uruchom jako administrator.**

2. Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .

3. Uruchom kreatora usługi Azure AD Connect.

4. Przejdź do strony **Zadania dodatkowe,** wybierz pozycję **Rozwiązywanie problemów**i kliknij przycisk **Dalej**.

5. Na stronie Rozwiązywanie problemów kliknij przycisk **Uruchom,** aby uruchomić menu rozwiązywania problemów w programie PowerShell.

6. W menu głównym wybierz pozycję **Rozwiązywanie problemów z synchronizacją skrótów haseł**.

7. W menu podrzędnym wybierz **opcję Hasło nie jest synchronizowane dla określonego konta użytkownika**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Opis wyników zadania rozwiązywania problemów

Zadanie rozwiązywania problemów wykonuje następujące kontrole:

* Sprawdza stan obiektu usługi Active Directory w przestrzeni łącznika usługi Active Directory, Metaverse i przestrzeni łącznika usługi Azure AD.

* Sprawdza, czy istnieją reguły synchronizacji z synchronizacją skrótów haseł włączone i stosowane do obiektu usługi Active Directory.

* Próbuje pobrać i wyświetlić wyniki ostatniej próby zsynchronizowania hasła do obiektu.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet podczas rozwiązywania problemów z synchronizacją skrótów haseł dla pojedynczego obiektu:

![Wyjście diagnostyczne synchronizacji skrótu hasła — pojedynczy obiekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

W pozostałej części tej sekcji opisano konkretne wyniki zwrócone przez polecenie cmdlet i odpowiednie problemy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Obiekt usługi Active Directory nie jest eksportowany do usługi Azure AD

synchronizacja skrótu haseł dla tego lokalnego konta usługi Active Directory kończy się niepowodzeniem, ponieważ w dzierżawie usługi Azure AD nie ma odpowiedniego obiektu. Zwracany jest następujący błąd:

![Brak obiektu usługi Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Użytkownik ma hasło tymczasowe

Obecnie usługa Azure AD Connect nie obsługuje synchronizowania haseł tymczasowych z usługą Azure AD. Hasło jest uważane za tymczasowe, jeśli opcja **Zmień hasło przy następnym logowaniu** jest ustawiona na lokalnym użytkowniku usługi Active Directory. Zwracany jest następujący błąd:

![Hasło tymczasowe nie jest eksportowane](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Wyniki ostatniej próby synchronizacji hasła nie są dostępne

Domyślnie usługa Azure AD Connect przechowuje wyniki prób synchronizacji skrótów haseł przez siedem dni. Jeśli dla wybranego obiektu usługi Active Directory nie są dostępne żadne wyniki, zwracane jest następujące ostrzeżenie:

![Wyjście diagnostyczne dla pojedynczego obiektu - brak historii synchronizacji hasła](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nie są synchronizowane żadne hasła: rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznego

Można użyć `Invoke-ADSyncDiagnostics` polecenia cmdlet, aby dowiedzieć się, dlaczego nie hasła są synchronizowane.

> [!NOTE]
> Polecenie `Invoke-ADSyncDiagnostics` cmdlet jest dostępne tylko dla usługi Azure AD Connect w wersji 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchamianie polecenia cmdlet diagnostyki

Aby rozwiązać problemy, w których nie są synchronizowane żadne hasła:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z opcją **Uruchom jako administrator.**

2. Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom polecenie `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden obiekt nie synchronizuje haseł: rozwiązywanie problemów przy użyciu polecenia cmdlet diagnostycznego

Polecenia `Invoke-ADSyncDiagnostics` cmdlet można użyć do ustalenia, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> Polecenie `Invoke-ADSyncDiagnostics` cmdlet jest dostępne tylko dla usługi Azure AD Connect w wersji 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchamianie polecenia cmdlet diagnostyki

Aby rozwiązać problemy, w których żadne hasła nie są synchronizowane dla użytkownika:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z opcją **Uruchom jako administrator.**

2. Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom następujące polecenie cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Przykład:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Żadne hasła nie są synchronizowane: ręczne kroki rozwiązywania problemów

Wykonaj następujące kroki, aby ustalić, dlaczego nie są synchronizowane żadne hasła:

1. Czy serwer Connect jest w [trybie przejściowym?](how-to-connect-sync-staging-server.md) Serwer w trybie przejściowym nie synchronizuje żadnych haseł.

2. Uruchom skrypt w sekcji [Uzyskaj stan ustawień synchronizacji haseł.](#get-the-status-of-password-sync-settings) Zawiera przegląd konfiguracji synchronizacji haseł.  

    ![Dane wyjściowe skryptu programu PowerShell z ustawień synchronizacji haseł](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Jeśli funkcja nie jest włączona w usłudze Azure AD lub jeśli stan kanału synchronizacji nie jest włączony, uruchom Kreatora instalacji Połącz. Wybierz **pozycję Dostosuj opcje synchronizacji**i usuń zaznaczenie opcji synchronizacji hasła. Ta zmiana tymczasowo wyłącza tę funkcję. Następnie uruchom ponownie kreatora i włącz ponownie synchronizację haseł. Uruchom skrypt ponownie, aby sprawdzić, czy konfiguracja jest poprawna.

4. Poszukaj w dzienniku zdarzeń błędów. Poszukaj następujących zdarzeń, które wskazywałyby na problem:
    * Źródło: Identyfikator "Synchronizacja katalogów": 0, 611, 652, 655 Jeśli widzisz te zdarzenia, masz problem z łącznością. Komunikat dziennika zdarzeń zawiera informacje o lesie, w którym występuje problem. Aby uzyskać więcej informacji, zobacz [Problem z łącznością](#connectivity problem).

5. Jeśli nie widzisz pulsu lub jeśli nic więcej nie zadziałało, uruchom [uruchom wyzwalacz pełną synchronizację wszystkich haseł](#trigger-a-full-sync-of-all-passwords). Uruchom skrypt tylko raz.

6. Zobacz sekcję Rozwiązywanie problemów z jednym obiektem, który nie synchronizuje haseł.

### <a name="connectivity-problems"></a>Problemy z łącznością

Czy masz łączność z usługą Azure AD?

Czy konto ma wymagane uprawnienia do odczytywania skrótów haseł we wszystkich domenach? Jeśli zainstalowano connect przy użyciu ustawień ekspresowych, uprawnienia powinny być już poprawne. 

Jeśli użyto instalacji niestandardowej, ustaw uprawnienia ręcznie, wykonując następujące czynności:
    
1. Aby znaleźć konto używane przez łącznik usługi Active Directory, uruchom **Menedżera usług synchronizacji**. 
 
2. Przejdź do **programu Łączniki**, a następnie wyszukaj lokalnego lasu usługi Active Directory, którego dotyczy problem. 
 
3. Zaznacz łącznik, a następnie kliknij przycisk **Właściwości**. 
 
4. Przejdź do **funkcji Połącz z lasem usługi Active Directory**.  
    
    ![Konto używane przez łącznik usługi Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Zanotuj nazwę użytkownika i domenę, w której znajduje się konto.
    
5. Uruchom **program Active Directory Użytkownicy i komputery,** a następnie sprawdź, czy znalezione wcześniej konto ma ustawione uprawnienia obserwowania w katalogu głównym wszystkich domen w lesie:
    * Replikowanie zmian w katalogu
    * Wszystkie zmiany katalogu replikacji

6. Czy kontrolery domeny są dostępne dla usługi Azure AD Connect? Jeśli serwer Connect nie może połączyć się ze wszystkimi kontrolerami domeny, **skonfiguruj tylko preferowany kontroler domeny**.  
    
    ![Kontroler domeny używany przez łącznik usługi Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Wróć do **Menedżera usług synchronizacji** i **skonfiguruj partycję katalogu**. 
 
8. Zaznacz domenę w **obszarze Wybierz partycje katalogu**, zaznacz pole wyboru Tylko **używaj preferowanych kontrolerów domeny,** a następnie kliknij przycisk **Konfiguruj**. 

9. Na liście wprowadź kontrolery domeny, które Connect powinny używać do synchronizacji haseł. Ta sama lista jest również używana do importu i eksportu. Wykonaj te czynności dla wszystkich domen.

10. Jeśli skrypt pokazuje, że nie ma pulsu, uruchom skrypt w [Wyzwalacz pełną synchronizację wszystkich haseł](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden obiekt nie synchronizuje haseł: ręczne kroki rozwiązywania problemów

Można łatwo rozwiązać problemy z synchronizacją skrótów haseł, przeglądając stan obiektu.

1. W **usłudze Active Directory Użytkownicy i komputery**wyszukaj użytkownika, a następnie sprawdź, czy pole wyboru Użytkownik musi **zmienić hasło przy następnym logowaniu.**  

    ![Hasła produkcyjne usługi Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Jeśli to pole wyboru jest zaznaczone, poproś użytkownika o zalogowanie się i zmianę hasła. Tymczasowe hasła nie są synchronizowane z usługą Azure AD.

2. Jeśli hasło wygląda poprawnie w usłudze Active Directory, postępuj zgodnie z nim w wyszukiwarce synchronizacji. Postępować zgodnie z użytkownikiem z lokalnej usługi Active Directory do usługi Azure AD, można sprawdzić, czy występuje błąd opisowy na obiekcie.

    a. Uruchom [Menedżera usług synchronizacji](how-to-connect-sync-service-manager-ui.md).

    b. Kliknij **pozycję Łączniki**.

    d. Wybierz **łącznik usługi Active Directory,** w którym znajduje się użytkownik.

    d. Wybierz **miejsce łącznika wyszukiwania**.

    e. W polu **Zakres** wybierz pozycję **DN lub Anchor**, a następnie wprowadź pełną liczbę danych DN użytkownika, którego dotyczy problem.

    ![Wyszukiwanie użytkownika w przestrzeni łącznika za pomocą nazwy DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Znajdź użytkownika, którego szukasz, a następnie kliknij pozycję **Właściwości,** aby wyświetlić wszystkie atrybuty. Jeśli użytkownik nie znajduje się w wynikach wyszukiwania, sprawdź [reguły filtrowania](how-to-connect-sync-configure-filtering.md) i upewnij się, że [uruchomisz zastosuj i zweryfikuj zmiany,](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) aby użytkownik pojawił się w polu Połącz.

    g. Aby wyświetlić szczegóły synchronizacji hasła obiektu z ostatniego tygodnia, kliknij przycisk **Zaloguj**.  

    ![Szczegóły dziennika obiektów](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Jeśli dziennik obiektów jest pusty, usługa Azure AD Connect nie może odczytać skrótu hasła z usługi Active Directory. Kontynuuj rozwiązywanie problemów z błędami łączności. Jeśli widzisz inną wartość niż **sukces,** zapoznaj się z tabelą w [dzienniku synchronizacji haseł](#password-sync-log).

    h. Zaznacz kartę **rodowód** i upewnij się, że co najmniej jedna reguła synchronizacji w kolumnie **PasswordSync** to **True**. W konfiguracji domyślnej nazwa reguły synchronizacji to **In from AD — User AccountEnabled**.  

    ![Informacje o rodowód użytkownika](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kliknij **pozycję Właściwości obiektu Metaverse,** aby wyświetlić listę atrybutów użytkownika.  

    ![Informacje metaverse](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Sprawdź, czy nie ma żadnego atrybutu **cloudFiltered.** Upewnij się, że atrybuty domeny (domainFQDN i domainNetBios) mają oczekiwane wartości.

    j. Kliknij kartę **Łączniki.** Upewnij się, że są widoczne łączniki zarówno lokalnej usługi Active Directory, jak i usługi Azure AD.

    ![Informacje metaverse](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Zaznacz wiersz reprezentujący usługę Azure AD, kliknij pozycję **Właściwości**, a następnie kliknij kartę **Rodowód.** Obiekt spacji łącznika powinien mieć regułę wychodzącą w kolumnie **PasswordSync** ustawioną na **True**. W konfiguracji domyślnej nazwa reguły synchronizacji to Out to **AAD — User Join**.  

    ![Okno dialogowe Właściwości obiektu obszaru łącznika](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Dziennik synchronizacji hasła

Kolumna stanu może mieć następujące wartości:

| Stan | Opis |
| --- | --- |
| Powodzenie |Hasło zostało pomyślnie zsynchronizowane. |
| Filtrowany przeztarget |Hasło jest ustawione na **Użytkownik musi zmienić hasło przy następnym logowaniu**. Hasło nie zostało zsynchronizowane. |
| Połączenie Bez celu |Brak obiektu w metaverse lub w przestrzeni łącznika usługi Azure AD. |
| Połączenie źródłoweNotPresent |W lokalnym pliku złącza usługi Active Directory nie znaleziono żadnego obiektu. |
| TargetNotExportedToDirectory (Nierozprzemywający do reżyserii) |Obiekt w przestrzeni łącznika usługi Azure AD nie został jeszcze wyeksportowany. |
| MigrowaneZnakowanie SzczegółysforMoreInfo |Wpis dziennika został utworzony przed kompilacją 1.0.9125.0 i jest wyświetlany w jego stanie starszej wersji. |
| Błąd |Usługa zwróciła nieznany błąd. |
| Nieznane |Wystąpił błąd podczas próby przetworzenia partii skrótów haseł.  |
| Brakwłasnybut |Określone atrybuty (na przykład skrót Kerberos) wymagane przez usługi domenowe usługi Azure AD domain services nie są dostępne. |
| Ponawianie próbRequestedByTarget |Określone atrybuty (na przykład skrót Protokołu Kerberos) wymagane przez usługi domenowe usługi Azure AD nie były wcześniej dostępne. Podejmowana jest próba ponownej synchronizacji haszyszu hasła użytkownika. |

## <a name="scripts-to-help-troubleshooting"></a>Skrypty ułatwiające rozwiązywanie problemów

### <a name="get-the-status-of-password-sync-settings"></a>Uzyskaj stan ustawień synchronizacji haseł

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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Wyzwalanie pełnej synchronizacji wszystkich haseł

> [!NOTE]
> Uruchom ten skrypt tylko raz. Jeśli musisz uruchomić go więcej niż raz, problemem jest coś innego. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną firmy Microsoft.

Pełną synchronizację wszystkich haseł można wyzwolić przy użyciu następującego skryptu:

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

## <a name="next-steps"></a>Następne kroki

* [Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Usługa Azure AD Connect Sync: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)