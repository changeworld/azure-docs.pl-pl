---
title: 'Usługa Azure AD Connect: uwierzytelnianie w chmurze za pomocą wdrożenia etapowego | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak przeprowadzić migrację z uwierzytelnienia federacyjnego do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915230"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migracja do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego (wersja zapoznawcza)

Za pomocą metody stopniowego wdrażania można przeprowadzić migrację z uwierzytelnienia federacyjnego do uwierzytelniania w chmurze. W tym artykule omówiono sposób przełączania. Przed rozpoczęciem wdrażania etapowego należy jednak wziąć pod uwagę implikacje, jeśli spełniony jest co najmniej jeden z następujących warunków:
    
-  Obecnie używasz lokalnego serwera uwierzytelniania wieloskładnikowego. 
-  Do uwierzytelniania używasz kart inteligentnych. 
-  Bieżący serwer oferuje pewne funkcje tylko dla federacji.

Przed wypróbowaniem tej funkcji zalecamy zapoznanie się z naszym przewodnikiem na temat wyboru właściwej metody uwierzytelniania. Aby uzyskać więcej informacji, zobacz tabelę "Porównywanie metod" w [obszarze Wybieranie właściwej metody uwierzytelniania dla rozwiązania tożsamości hybrydowej usługi Azure Active Directory.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)

Aby zapoznać się z omówieniem tej funkcji, wyświetl tę "Usługa Azure Active Directory: Co to jest wdrażanie etapowe?" Wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Wymagania wstępne

-   Masz dzierżawę usługi Azure Active Directory (Azure AD) z domenami federatywnymi.

-   Zdecydowałeś się przejść do jednej z dwóch opcji:
    - **Opcja Synchronizacja** - *skrótu hasła (synchronizacja)* + bez*szwu (Logowanie jednokrotne)*
    - **Bezproblemowe** - *logowanie jednokrotne do uwierzytelniania typu "opcja B"* + *seamless SSO*
    
    Mimo że *bezproblemowe logowanie jednokrotne* jest opcjonalne, zalecamy włączenie go do osiągnięcia cichego logowania dla użytkowników, którzy korzystają z maszyn przyłączonych do domeny z sieci firmowej.

-   Skonfigurowano wszystkie odpowiednie zasady znakowania dzierżawy i dostępu warunkowego, których potrzebujesz dla użytkowników, którzy są migrowani do uwierzytelniania w chmurze.

-   Jeśli planujesz używać uwierzytelniania wieloskładnikowego platformy Azure, zaleca się użycie [rejestracji konwergentnej do samoobsługowego resetowania haseł (SSPR) i uwierzytelniania wieloskładnikowego,](../authentication/concept-registration-mfa-sspr-combined.md) aby użytkownicy zarejestrowali swoje metody uwierzytelniania raz.

-   Aby korzystać z funkcji wdrażania etapowego, musisz być administratorem globalnym w dzierżawie.

-   Aby włączyć *bezproblemowe logowanie jednokrotne* w określonym lesie usługi Active Directory, musisz być administratorem domeny.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Następujące scenariusze są obsługiwane dla wdrożenia etapowego. Funkcja działa tylko dla:

- Użytkownicy, którzy są aprowizowani do usługi Azure AD przy użyciu usługi Azure AD Connect. Nie dotyczy użytkowników tylko w chmurze.

- Ruch logowania użytkowników w przeglądarkach i *nowoczesnych klientach uwierzytelniania.* Aplikacje lub usługi w chmurze, które używają starszego uwierzytelniania, powrócą do przepływów uwierzytelniania federacyjnego. Przykładem może być usługa Exchange online z wyłączonym nowoczesnym uwierzytelnianiem lub program Outlook 2010, który nie obsługuje nowoczesnego uwierzytelniania.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane dla wdrożenia etapowego:

- Niektóre aplikacje wysyłają parametr zapytania "domain_hint" do usługi Azure AD podczas uwierzytelniania. Przepływy te będą kontynuowane, a użytkownicy, którzy są włączeni do wdrażania etapowego, będą nadal używać federacji do uwierzytelniania.

<!-- -->

- Administratorzy mogą wdrażać uwierzytelnianie w chmurze przy użyciu grup zabezpieczeń. Aby uniknąć opóźnień synchronizacji podczas korzystania z lokalnych grup zabezpieczeń usługi Active Directory, zaleca się używanie grup zabezpieczeń w chmurze. Obowiązują następujące warunki:

    - Można użyć maksymalnie 10 grup na operację. Oznacza to, że można użyć 10 grup, z których każda służy do *synchronizacji skrótów haseł,* *uwierzytelniania przekazywanego*i *bezproblemowego logowania jednokrotnego.*
    - Grupy zagnieżdżone nie są *obsługiwane*. Ten zakres dotyczy również publicznej wersji zapoznawczej.
    - Grupy dynamiczne nie są *obsługiwane* w przypadku wdrażania etapowego.
    - Obiekty kontaktowe wewnątrz grupy zablokują dodawać grupę.

- Nadal należy dokonać ostatecznego cutover z federacyjne do uwierzytelniania w chmurze przy użyciu usługi Azure AD Connect lub programu PowerShell. Wdrożenie etapowe nie przełącza domen z federacyjnego na zarządzane.

- Po pierwszym dodaniu grupy zabezpieczeń do wdrożenia etapowego, jesteś ograniczony do 200 użytkowników, aby uniknąć limitu czasu środowiska użytkownika. Po dodaniu grupy możesz dodać do niej więcej użytkowników bezpośrednio, zgodnie z wymaganiami.

## <a name="get-started-with-staged-rollout"></a>Wprowadzenie do wdrażania etapowego

Aby przetestować logowanie *synchronizacji skrótu hasła* przy użyciu wdrożenia etapowego, postępuj zgodnie z instrukcjami przed pracą w następnej sekcji.

Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [Wersja zapoznawcza usługi Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Wstępnie pracować nad synchronizacją skrótów haseł

1. Włącz *synchronizację* skrótów haseł ze strony [Funkcje](how-to-connect-install-custom.md#optional-features) opcjonalne w usłudze Azure AD Connect. 

   ![Zrzut ekranu przedstawiający stronę "Funkcje opcjonalne" w usłudze Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Upewnij się, że pełny cykl *synchronizacji hash hasła* został uruchomiony, tak aby wszystkie skróty haseł użytkowników zostały zsynchronizowane z usługą Azure AD. Aby sprawdzić stan *synchronizacji skrótów haseł,* można użyć diagnostyki programu PowerShell w [rozwiązywaniu problemów z synchronizacją skrótów haseł z synchronizacją usługi Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Zrzut ekranu przedstawiający dziennik rozwiązywania problemów z funkcją AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Jeśli chcesz przetestować *logowanie uwierzytelniania przekazywanego* przy użyciu wdrożenia etapowego, włącz go, postępując zgodnie z instrukcjami przed pracą w następnej sekcji.

## <a name="pre-work-for-pass-through-authentication"></a>Praca wstępna w zakresie uwierzytelniania przekazywanego

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszym, na którym ma zostać uruchomiony agent *uwierzytelniania przekazowego.* 

   *Nie* należy wybierać serwera usługi Azure AD Connect.Upewnij się, że serwer jest przyłączony do domeny, może uwierzytelniać wybranych użytkowników za pomocą usługi Active Directory i może komunikować się z usługą Azure AD na portach wychodzących i adresach URL. Aby uzyskać więcej informacji, zobacz sekcję "Krok 1: Sprawdź wymagania wstępne" programu [Szybki start: Bezproblemowe logowanie jednokrotne usługi Azure AD](how-to-connect-sso-quick-start.md).

1. [Pobierz agenta uwierzytelniania usługi Azure AD Connect](https://aka.ms/getauthagent)i zainstaluj go na serwerze. 

1. Aby włączyć [wysoką dostępność,](how-to-connect-sso-quick-start.md)zainstaluj dodatkowe agentów uwierzytelniania na innych serwerach.

1. Upewnij się, że ustawienia [blokady inteligentnej](../authentication/howto-password-smart-lockout.md) zostały odpowiednio skonfigurowane. Pomaga to zapewnić, że lokalne konta usługi Active Directory użytkowników nie są blokowane przez złych aktorów.

Zalecamy włączenie *bezproblemowego logowania jednokrotnego* niezależnie od metody logowania *(synchronizacja skrótu hasła* lub *uwierzytelnianie przekazywane)* wybranej do wdrożenia etapowego. Aby włączyć *bezproblemowe logowanie*jednokrotne, postępuj zgodnie z instrukcjami dotyczącymi pracy wstępnej w następnej sekcji.

## <a name="pre-work-for-seamless-sso"></a>Wstępna praca dla bezproblemowego logowania jednokrotnego

Włącz *bezproblemowe logowanie jednokrotne* w lasach usługi Active Directory przy użyciu programu PowerShell. Jeśli masz więcej niż jeden las usługi Active Directory, włącz go dla każdego lasu indywidualnie.  *Bezproblemowe logowanie jednokrotne* jest wyzwalane tylko dla użytkowników, którzy są wybierani do wdrożenia etapowego. Nie ma to wpływu na istniejącą konfigurację federacji.

Włącz *bezproblemowe logowanie* jednokrotne, wykonując następujące czynności:

1. Zaloguj się do usługi Azure AD Connect Server.

2. Przejdź do folderu *%programfiles%\\Microsoft Azure Active Directory Connect.* 

3. Zaimportuj bezproblemowy moduł *programu SSO* PowerShell, uruchamiając następujące polecenie: 

   `Import-Module .\AzureADSSO.psd1`

4. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . To polecenie otwiera okienko, w którym można wprowadzić poświadczenia administratora globalnego dzierżawy.

5. Zadzwoń `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie wyświetla listę lasów usługi Active Directory (zobacz listę "Domeny"), na której ta funkcja została włączona. Domyślnie jest ustawiona na false na poziomie dzierżawy.

   ![Przykład danych wyjściowych programu Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Zadzwoń `$creds = Get-Credential`. W wierszu polecenia wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

7. Zadzwoń `Enable-AzureADSSOForest -OnPremCredentials $creds`. To polecenie tworzy konto komputera AZUREADSSOACC z lokalnego kontrolera domeny dla lasu usługi Active Directory, który jest wymagany do *bezproblemowego logowania jednokrotnego.*

8. *Bezproblemowe logowanie jednokrotne* wymaga, aby adresy URL były w strefie intranetu. Aby wdrożyć te adresy URL przy użyciu zasad grupy, zobacz [Szybki start: Azure AD bezproblemowe logowanie jednokrotne](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Aby uzyskać pełny przewodnik, można również pobrać nasze [plany wdrażania](https://aka.ms/SeamlessSSODPDownload) *dla bezproblemowego logowania jednokrotnego.*

## <a name="enable-staged-rollout"></a>Włączanie wdrażania etapowego

Aby wdrożyć określoną funkcję *(uwierzytelnianie przekazywane,* *synchronizacja skrótów haseł*lub *bezproblemowe logowanie jednokrotne)* dla wybranego zestawu użytkowników w grupie, postępuj zgodnie z instrukcjami w następnych sekcjach.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Włączanie stopniowego wdrażania określonej funkcji w dzierżawie

Można wdrożyć jedną z następujących opcji:

- **Opcja Synchronizacja** - *hash* + hasła bez*szwu jednokrotna*
- **Bezproblemowe** - *logowanie jednokrotne do uwierzytelniania typu "opcja B"* + *seamless SSO*
- **Nie obsługiwana** - *synchronizacja hash* + password*pass-through authentication* + *bezproblemowe logowanie jednokrotne*

Wykonaj następujące czynności:

1. Aby uzyskać dostęp do środowiska użytkownika w wersji zapoznawczej, zaloguj się do [portalu usługi Azure AD](https://aka.ms/stagedrolloutux).

2. Wybierz **łącze Włącz wdrażanie etapowe dla logowania użytkownika zarządzanego (wersja zapoznawcza).**

   Na przykład, jeśli chcesz włączyć *opcję A*, przesuń kontrolki **Synchronizacja hash hasła** i **Bezproblemowe logowanie jednokrotne do** **on**, jak pokazano na poniższych obrazach.

   ![Strona Usługi Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Strona "Włącz funkcje wdrażania etapowego (podgląd)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Dodaj grupy do tej funkcji, aby włączyć *uwierzytelnianie przekazywane* i *bezproblemowe logowanie jednokrotne.* Aby uniknąć przesuwu środowiska użytkownika, upewnij się, że grupy zabezpieczeń zawierają początkowo nie więcej niż 200 członków.

   ![Strona "Zarządzanie grupami synchronizacji skrótów haseł (wersja zapoznawcza)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Członkowie grupy są automatycznie włączani do wdrażania etapowego. Grupy zagnieżdżone i dynamiczne nie są obsługiwane w przypadku wdrażania etapowego.

## <a name="auditing"></a>Inspekcja

Włączyliśmy zdarzenia inspekcji dla różnych akcji, które wykonujemy w celu wdrożenia etapowego:

- Zdarzenie inspekcji po włączeniu wdrożenia etapowego dla *synchronizacji skrótów haseł,* *uwierzytelniania przekazywanego*lub bezproblemowego logowania *jednokrotnego*.

  >[!NOTE]
  >Zdarzenie inspekcji jest rejestrowane, gdy *bezproblemowe logowanie jednokrotne* jest włączone przy użyciu wdrożenia etapowego.

  ![Okienko "Tworzenie zasad wdrażania dla funkcji" — karta Aktywność](./media/how-to-connect-staged-rollout/sr7.png)

  ![Okienko "Tworzenie zasad wdrażania dla funkcji" — zmodyfikowane właściwości](./media/how-to-connect-staged-rollout/sr8.png)

- Zdarzenie inspekcji, gdy grupa jest dodawana do *synchronizacji skrótu hasła,* *uwierzytelniania przekazywanego*lub *bezproblemowego logowania jednokrotnego*.

  >[!NOTE]
  >Zdarzenie inspekcji jest rejestrowane, gdy grupa jest dodawana do *synchronizacji skrótów haseł* do wdrożenia etapowego.

  ![Okienko "Dodawanie grupy do wdrożenia funkcji" — karta Aktywność](./media/how-to-connect-staged-rollout/sr9.png)

  ![Okienko "Dodawanie grupy do wdrożenia funkcji" — zmodyfikowane właściwości](./media/how-to-connect-staged-rollout/sr10.png)

- Inspekcja zdarzenia, gdy użytkownik, który został dodany do grupy jest włączona do wdrożenia etapowego.

  ![Okienko "Dodaj użytkownika do wdrożenia funkcji" — karta Aktywność](media/how-to-connect-staged-rollout/sr11.png)

  ![Okienko "Dodaj użytkownika do wdrożenia funkcji" — karta Target(y)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Sprawdzanie poprawności

Aby przetestować logowanie za pomocą *synchronizacji skrótu hasła* lub *uwierzytelniania przekazywanego (logowanie* się do nazwy użytkownika i hasła), wykonaj następujące czynności:

1. W ekstranecie przejdź do [strony Aplikacje](https://myapps.microsoft.com) w prywatnej sesji przeglądarki, a następnie wprowadź numer USN (UserPrincipalName) konta użytkownika wybranego do wdrożenia etapowego.

   Użytkownicy, którzy zostali zaatakowani do wdrożenia etapowego, nie są przekierowywani na stronę logowania federacyjnego. Zamiast tego są monitowani o zalogowanie się na stronie logowania z marką dzierżawy usługi Azure AD.

1. Upewnij się, że logowanie pomyślnie pojawia się w [raporcie aktywności logowania usługi Azure AD,](../reports-monitoring/concept-sign-ins.md) filtrując za pomocą userPrincipalName.

Aby przetestować logowanie z *bezproblemowym logowaniem jednokrotnym:*

1. W intranecie przejdź do [strony Aplikacje](https://myapps.microsoft.com) w prywatnej sesji przeglądarki, a następnie wprowadź numer UPN (UserPrincipalName) konta użytkownika wybranego do wdrożenia etapowego.

   Użytkownicy, którzy zostali ukierunkowani na etapowe wdrażanie *bezproblemowego logowania jednokrotnego,* otrzymują "Próba zalogowania się ..." przed zalogowaniem się po cichu.

1. Upewnij się, że logowanie pomyślnie pojawia się w [raporcie aktywności logowania usługi Azure AD,](../reports-monitoring/concept-sign-ins.md) filtrując za pomocą userPrincipalName.

   Aby śledzić logowania użytkowników, które nadal występują w usługach federacyjnych Active Directory (AD FS) dla wybranych użytkowników wdrażania etapowego, postępuj zgodnie z instrukcjami dotyczącymi rozwiązywania problemów z [usługami AD FS: Zdarzenia i rejestrowanie](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Sprawdź dokumentację dostawcy, jak to sprawdzić u dostawców federacji innych firm.

## <a name="remove-a-user-from-staged-rollout"></a>Usuwanie użytkownika z wdrażania etapowego

Usunięcie użytkownika z grupy powoduje wyłączenie wdrożenia etapowego dla tego użytkownika. Aby wyłączyć funkcję stopniowego wdrażania, przesuń formant z powrotem do **wyłączonego**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Czy mogę użyć tej funkcji w produkcji?**

Odp.: Tak, można użyć tej funkcji w dzierżawie produkcyjnej, ale zaleca się, aby najpierw wypróbować go w dzierżawie testowej.

**Pyt.: Czy ta funkcja może służyć do utrzymania stałego "współistnienia", gdzie niektórzy użytkownicy używają uwierzytelniania federacyjnego, a inni używają uwierzytelniania w chmurze?**

Odp.: Nie, ta funkcja jest przeznaczona do migracji z uwierzytelniania federacyjnego do uwierzytelniania w chmurze etapami, a następnie do przecięcia na uwierzytelnianie w chmurze. Nie zaleca się przy użyciu stałego stanu mieszanego, ponieważ takie podejście może prowadzić do nieoczekiwanych przepływów uwierzytelniania.

**Pyt.: Czy można używać programu PowerShell do wykonywania wdrażania etapowego?**

Odp. Tak. Aby dowiedzieć się, jak używać programu PowerShell do stopniowego wdrażania, zobacz [Usługa Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Następne kroki
- [Wersja zapoznawcza usługi Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
