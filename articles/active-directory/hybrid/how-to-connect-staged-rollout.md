---
title: 'Azure AD Connect: uwierzytelnianie w chmurze — wdrażanie etapowe | Microsoft Docs'
description: Wyjaśnia, jak przeprowadzić migrację z uwierzytelniania federacyjnego do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847233"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Uwierzytelnianie w chmurze: wdrażanie etapowe (publiczna wersja zapoznawcza)

Ta funkcja umożliwia migrowanie z uwierzytelniania federacyjnego do uwierzytelniania w chmurze przy użyciu podejścia etapowego.

Przejście z uwierzytelniania federacyjnego ma konsekwencje. Na przykład jeśli masz jedną z następujących czynności:
    
-  lokalny serwer usługi MFA 
-  używa kart inteligentnych do uwierzytelniania 
-  inne funkcje tylko federacyjne

Te funkcje należy wziąć pod uwagę przed przełączeniem na uwierzytelnianie w chmurze.  Przed podjęciem próby wykonania tej funkcji zalecamy zapoznanie się z naszym przewodnikiem dotyczącym wybierania właściwej metody uwierzytelniania. Aby uzyskać więcej informacji, zobacz [tę tabelę](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) .

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Wymagania wstępne

-   Masz dzierżawę usługi Azure AD z domenami federacyjnymi.

-   Podjęto decyzję o przeniesieniu na synchronizację skrótów haseł + bezproblemowe logowanie jednokrotne **(opcja A)** lub uwierzytelnianie przekazywane + bezproblemowe logowanie jednokrotne **(opcja B).** Chociaż bezproblemowe logowanie jednokrotne jest opcjonalne, zalecamy włączenie bezproblemowego logowania jednokrotnego w celu uzyskania cichego logowania dla użytkowników korzystających z maszyn przyłączonych do domeny w sieci firmowej.

-   Skonfigurowano wszystkie odpowiednie zasady znakowania dzierżawy i dostępu warunkowego, które są potrzebne dla użytkowników, którzy są migrowani do uwierzytelniania w chmurze.

-   Jeśli planujesz korzystanie z usługi Azure Multi-Factor Authentication, zalecamy użycie [rejestracji zbieżnej do samoobsługowego resetowania hasła (SSPR) i usługi Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) , aby użytkownicy mogli rejestrować swoje metody uwierzytelniania.

-   Aby korzystać z tej funkcji, musisz być administratorem globalnym w dzierżawie.

-   Aby umożliwić bezproblemowe logowanie jednokrotne w określonym lesie usługi AD, musisz być administratorem domeny.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Te scenariusze są obsługiwane w przypadku wdrażania etapowego:

- Ta funkcja działa tylko dla użytkowników, którym Zainicjowano obsługę usługi Azure AD przy użyciu Azure AD Connect i nie dotyczy tylko użytkowników w chmurze.

- Ta funkcja działa tylko w przypadku ruchu związanego z logowaniem użytkowników w przeglądarkach i nowoczesnych klientach uwierzytelniania. Aplikacje lub usługi w chmurze korzystające ze starszego uwierzytelniania będą powracać do przepływów uwierzytelniania federacyjnego. (Przykład: Usługa Exchange Online z wyłączoną funkcją nowoczesnego uwierzytelniania lub Outlook 2010, która nie obsługuje nowoczesnego uwierzytelniania).

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Te scenariusze nie są obsługiwane w przypadku wdrażania etapowego:

- Niektóre aplikacje wysyłają parametr zapytania "Wskazówka\_domeny" do usługi Azure AD podczas uwierzytelniania. Te przepływy będą kontynuowane, a użytkownicy włączeni do wdrożenia przemieszczania będą nadal używać Federacji do uwierzytelniania.

<!-- -->

- Administrator może wdrożyć uwierzytelnianie w chmurze przy użyciu grup zabezpieczeń. (Grupy zabezpieczeń chmury zaleca się uniknięcie opóźnienia synchronizacji przy użyciu lokalnych grup zabezpieczeń usługi AD).

    - Można użyć **maksymalnie 10 grup na funkcję**; oznacza to, że dla każdej synchronizacji skrótu hasła/uwierzytelniania przekazywanego/bezproblemowego logowania jednokrotnego.

    - Zagnieżdżone grupy **nie są obsługiwane**. Jest to również zakres publicznej wersji zapoznawczej.

    - Grupy dynamiczne **nie są obsługiwane** w przypadku wdrażania etapowego.

    - Obiekty Contact wewnątrz grupy blokują Dodawanie formularza grupy.

- Końcowy uruchomienie produkcyjne z uwierzytelniania federacyjnego do chmury nadal musi mieć miejsce przy użyciu Azure AD Connect lub programu PowerShell. Ta funkcja nie przełącza domen z sfederować na zarządzane.

- Przy pierwszym dodawaniu grupy zabezpieczeń do wdrożenia przemieszczanego należy ograniczyć do 200 użytkowników, aby uniknąć przekroczenia limitu czasu środowiska. Po dodaniu grupy do środowiska użytkownika można dodać więcej użytkowników bezpośrednio do grupy, zgodnie z potrzebami.

## <a name="get-started-with-staged-rollout"></a>Wprowadzenie do wdrożenia przemieszczanego

Jeśli chcesz przetestować logowanie za pomocą funkcji synchronizacji skrótów haseł (PHS) przy użyciu wdrożenia przemieszczanego, wykonaj poniższe czynności przed włączeniem wstępnego wdrożenia synchronizacji skrótów haseł.

Aby uzyskać więcej informacji na temat używanych poleceń cmdlet programu PowerShell, zobacz artykuł [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Pre-Work dla synchronizacji skrótów haseł

1. Włącz synchronizację skrótów haseł na stronie [funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) w Azure AD Connect. 

   ![Zrzut ekranu strony funkcji opcjonalnych w Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Upewnij się, że cykl synchronizacji pełnego skrótu hasła został uruchomiony przez program, aby wszystkie skróty haseł użytkowników zostały zsynchronizowane z usługą Azure AD. W [tym miejscu](tshoot-connect-password-hash-synchronization.md) możesz użyć diagnostyki programu PowerShell, aby sprawdzić stan synchronizacji skrótów haseł.

   ![Zrzut ekranu przedstawiający dziennik rozwiązywania problemów z AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Jeśli chcesz przetestować logowanie za pośrednictwem uwierzytelniania (PTA) przy użyciu wdrożenia przemieszczanego, wykonaj poniższe czynności wstępne, aby włączyć PTA do wdrożenia etapowego.

## <a name="pre-work-for-pass-through-authentication"></a>Przed rozpoczęciem uwierzytelniania przekazywanego

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszym, w którym ma być uruchomiony Agent uwierzytelniania Pass-through (**nie wybieraj serwera Azure AD Connect**). Upewnij się, że serwer jest przyłączony do domeny, umożliwia uwierzytelnienie wybranych użytkowników za pomocą Active Directory i może komunikować się z usługą Azure AD na portach wychodzących/adresy URL (Zobacz szczegółowe [wymagania wstępne](how-to-connect-sso-quick-start.md)).

1. [Pobierz](https://aka.ms/getauthagent) & Zainstaluj na serwerze agenta uwierzytelniania Microsoft Azure AD Connect. 

1. Aby włączyć [wysoką dostępność](how-to-connect-sso-quick-start.md), należy zainstalować dodatkowych agentów uwierzytelniania na innych serwerach.

1. Upewnij się, że [Ustawienia inteligentnego blokady](../authentication/howto-password-smart-lockout.md) zostały odpowiednio skonfigurowane. Ma to na celu zapewnienie, że konta Active Directory użytkowników lokalnych nie są blokowane przez złe uczestników.

Zalecamy włączenie bezproblemowego logowania jednokrotnego niezależnie od metody logowania (PHS lub PTA) wybieranej do wdrożenia etapowego. Wykonaj poniższe czynności wstępne, aby umożliwić bezproblemowe logowanie jednokrotne w przypadku wdrożenia przemieszczanego.

## <a name="pre-work-for-seamless-sso"></a>Przedpracuj przed bezproblemowym logowaniem jednokrotnym

Włącz bezproblemową rejestrację jednokrotną w lasach usługi AD przy użyciu programu PowerShell. Jeśli masz więcej niż jeden las usługi AD, Włącz te same dla każdego lasu osobno. Bezproblemowy Logowanie jednokrotne zostanie wyzwolone tylko dla użytkowników wybranych do wdrożenia przemieszczanego i nie wpłynie to na istniejącą konfigurację Federacji.

**Podsumowanie kroków**

1. Najpierw Zaloguj się do Azure AD Connect Server.

2. Przejdź do folderu% PROGRAMFILES%\\Microsoft Azure Active Directory Connect.

3. Zaimportuj bezproblemowy moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj `New-AzureADSSOAuthenticationContext`. To polecenie powinno dać okno dialogowe, w którym można wprowadzić poświadczenia administratora globalnego dzierżawy.

5. Wywołaj `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie umożliwia wyświetlenie listy lasów usługi AD (poszukaj na liście \"domen\"), na których ta funkcja została włączona. Domyślnie na poziomie dzierżawy jest ustawiona wartość false.

   > **Przykład:** 
   > ![przykład danych wyjściowych programu Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Wywołaj `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi AD.

7. Wywołaj `Enable-AzureADSSOForest -OnPremCredentials $creds`. To polecenie tworzy konto komputera AZUREADSSOACC z lokalnego kontrolera domeny dla tego konkretnego lasu Active Directory, który jest wymagany do bezproblemowego logowania jednokrotnego.

8. Bezproblemowe logowanie jednokrotne wymaga, aby adresy URL znajdować się w strefie intranetowej. Aby wdrożyć te adresy URL przy użyciu zasad grupy, zapoznaj się z [przewodnikiem Szybki Start dotyczącego logowania](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) jednokrotnego.

9.  Możesz również pobrać nasze [plany wdrożenia](https://aka.ms/SeamlessSSODPDownload) w celu bezproblemowego logowania jednokrotnego w celu uzyskania pełnego przewodnika.

## <a name="enable-staged-rollout"></a>Włącz wdrażanie etapowe

Wykonaj następujące kroki, aby wdrożyć określoną funkcję (uwierzytelnianie przekazywane/synchronizacja skrótów haseł/bezproblemowe logowanie jednokrotne) do wybranego zestawu użytkowników w grupie:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Włącz etapowe wprowadzanie konkretnej funkcji w dzierżawie

Jedną z tych opcji można wycofać

-   Synchronizacja skrótów haseł + bezproblemowe logowanie jednokrotne **(opcja A)**

-   Uwierzytelnianie przekazywane i bezproblemowe logowanie jednokrotne **(opcja B)**

-   Synchronizacja skrótów haseł + uwierzytelnianie przekazywane i bezproblemowe logowanie jednokrotne **-\>** ***nieobsługiwane***

Wykonaj następujące kroki:

1. Zaloguj się do portalu usługi Azure AD przy użyciu poniższego adresu URL, aby uzyskać dostęp do środowiska w wersji zapoznawczej.

   > <https://aka.ms/stagedrolloutux>

2. Kliknij pozycję Włącz wdrażanie etapowe dla logowania użytkownika zarządzanego (wersja zapoznawcza)

   *Na przykład:* (**Opcja B**) Jeśli chcesz włączyć synchronizację skrótów haseł i bezproblemową rejestrację jednokrotną, przesuń ją na pozycję "włączone" i bezproblemowo Włącz funkcje logowania jednokrotnego do wartości **"on"** , jak pokazano poniżej.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Dodaj odpowiednie grupy do funkcji, aby włączyć uwierzytelnianie przekazywane i bezproblemowe logowanie jednokrotne. Upewnij się, że grupy zabezpieczeń nie mają początkowo więcej niż 200 członków, aby uniknąć limitu czasu środowiska użytkownika.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Członkowie w grupie są automatycznie włączeni do wdrożenia etapowego. Grupy zagnieżdżone i dynamiczne nie są obsługiwane w przypadku wdrażania etapowego.

## <a name="auditing"></a>Inspekcja

Włączono zdarzenia inspekcji dla różnych akcji wykonywanych w celu wdrożenia etapowego.

- Zdarzenie inspekcji po włączeniu wdrożenia przygotowanego do synchronizacji skrótów haseł/uwierzytelniania przekazywanego/bezproblemowego logowania jednokrotnego.

  >[!NOTE]
  >Zdarzenie inspekcji, które jest rejestrowane, gdy SeamlessSSO **jest włączony przy użyciu StagedRollout** .

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Zdarzenie inspekcji, gdy grupa jest dodawana do synchronizacji skrótów haseł/uwierzytelniania przekazywanego/bezproblemowego logowania jednokrotnego.

  >[!NOTE]
  >Zdarzenie inspekcji zarejestrowane, gdy grupa jest dodawana do synchronizacji skrótów haseł dla wdrożenia przemieszczanego

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Zdarzenie inspekcji, gdy użytkownik, który został dodany do grupy jest włączony do wdrożenia przemieszczanego

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Walidacja

Aby przetestować logowanie za pomocą synchronizacji skrótów haseł lub uwierzytelniania przekazywanego (Logowanie przy użyciu nazwy użytkownika/hasła):

1. Przejdź do <https://myapps.microsoft.com> w sesji przeglądarki prywatnej z ekstranetu i wprowadź wartość UserPrincipalName (UPN) konta użytkownika wybranego do przemieszczenia etapowego.

1. Jeśli użytkownik został skierowany do wdrożenia etapowego, użytkownik nie zostanie przekierowany do strony logowania federacyjnego, a zamiast tego zostanie wyświetlony monit o zalogowanie się na stronie Logowanie z oznaczeniem dzierżawy usługi Azure AD.

1. Upewnij się, że logowanie powiodło się w [raporcie aktywności logowania usługi Azure AD](../reports-monitoring/concept-sign-ins.md) przez filtrowanie z elementem userPrincipalName..

Aby przetestować logowanie za pomocą bezproblemowego logowania jednokrotnego:

1. Przejdź do <https://myapps.microsoft.com>/w sesji przeglądarki prywatnej z intranetu i wprowadź wartość UserPrincipalName (UPN) konta użytkownika wybranego do przemieszczenia etapowego.

1. Jeśli użytkownik został skierowany do przygotowanego wdrożenia bezproblemowego logowania jednokrotnego, użytkownik zobaczy "próba zalogowania się..." komunikat przed zalogowaniem dyskretnym.

1. Upewnij się, że logowanie powiodło się w [raporcie aktywności logowania usługi Azure AD](../reports-monitoring/concept-sign-ins.md) , filtrując element userPrincipalName.

Aby sprawdzić, czy Logowanie użytkowników odbywa się nadal w przypadku dostawców federacyjnych:

Oto jak można śledzić logowania użytkowników, które są nadal wykonywane na AD FS w przypadku wybranych użytkowników wdrożeniowych, korzystając z [tych instrukcji](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Sprawdź dokumentację dostawcy, jak to sprawdzić w przypadku dostawców federacyjnych innych firm.

## <a name="roll-back"></a>Wycofywanie

### <a name="remove-a-user-from-staged-rollout"></a>Usuwanie użytkownika z wdrożenia przemieszczanego

1.  Usunięcie użytkownika z grupy powoduje wyłączenie etapowego wdrażania dla użytkownika.

2.  Jeśli chcesz wyłączyć funkcję wdrażania etapowego, przesuń ją z powrotem do stanu **"off"** , aby wyłączyć wdrażanie etapowe.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

-   **P: czy klient może korzystać z tej funkcji w środowisku produkcyjnym?**

-   Odp.: tak, ta funkcja może być używana w dzierżawie produkcyjnej, ale zalecamy najpierw wypróbowanie tej funkcji w dzierżawie testowej.

-   **P: Czy można użyć tej funkcji do utrzymania trwałej "współistnienia", w której niektórzy użytkownicy używają uwierzytelniania federacyjnego i innych uwierzytelniania w chmurze?**

-   Odp.: nie, ta funkcja została zaprojektowana na potrzeby migrowania z federacyjnego do uwierzytelniania w chmurze w etapach, a następnie ostatecznie przetniesz uwierzytelnianie w chmurze. Nie zalecamy stałego stanu mieszanego, ponieważ może to prowadzić do nieoczekiwanych przepływów uwierzytelniania.

-   **P: Czy można użyć programu PowerShell, aby wykonać etapowe wdrażanie?**

-   Odp.: tak, Znajdź dokumentację, aby za pomocą programu PowerShell wykonać wdrożenie etapowe [.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="next-steps"></a>Następne kroki
- [AzureAD 2,0 — wersja zapoznawcza](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
