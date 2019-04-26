---
title: Azure uwierzytelnianie przekazujących w usłudze Active Directory security szczegółowe informacje | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) zapewnia ochronę kont lokalnych
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f5e2443a285e065426e3dba0312ef6420097ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60348102"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Usługa Azure uwierzytelnianie przekazujących w usłudze Active Directory security szczegółowe dane

Ten artykuł zawiera bardziej szczegółowy opis sposobu działania uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD). Koncentruje się ona na aspekty zabezpieczeń tej funkcji. Ten artykuł jest przeznaczony dla zabezpieczeń i administratorów IT, dyrektor zgodności i specjaliści ds. bezpieczeństwa i innym specjalistom IT, którzy są odpowiedzialni za bezpieczeństwo informatyczne i zgodności w małych i średnich wielkości organizacji lub dla dużych przedsiębiorstw.

Tematy, które zostały rozwiązane obejmują:
- Szczegółowe informacje techniczne na temat sposobu instalowania i rejestrowania agentów uwierzytelniania.
- Szczegółowe informacje techniczne dotyczące szyfrowania hasła podczas logowania użytkownika.
- Zabezpieczenia kanały między lokalną agentów uwierzytelniania i usługi Azure AD.
- Szczegółowe informacje techniczne dotyczące zachowania agentów uwierzytelniania pod względem zabezpieczeń.
- Inne tematy związane z zabezpieczeniami.

## <a name="key-security-capabilities"></a>Kluczowych funkcji zabezpieczeń

Poniżej przedstawiono kluczowych aspektów zabezpieczeń tej funkcji:
- Jest on oparty na bezpieczną architekturę wielodostępnych, która zapewnia izolację żądań logowania między dzierżawami.
- Hasłami lokalnymi nigdy nie są przechowywane w chmurze w dowolnej postaci.
- Agentów uwierzytelniania w środowisku lokalnym, który nasłuchuje i reagowanie na nie, żądania weryfikacji hasła należy tylko połączenia wychodzące z sieci. Nie jest wymagane do zainstalowania tych agentów uwierzytelniania w sieci obwodowej. Najlepszym rozwiązaniem jest traktowanie wszystkich serwerach z uruchomionym agentów uwierzytelniania w jak warstwy 0 systemów (zobacz [odwołania](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Tylko standardowe porty (80 i 443) są używane dla komunikacji wychodzącej z agentów uwierzytelniania w usłudze Azure AD. Nie ma potrzeby otwierania portów przychodzących na zaporze. 
  - Port 443 jest używany dla wszystkich uwierzytelnionych komunikacji wychodzącej.
  - Port 80 jest używana tylko w przypadku pobierania list odwołania certyfikatów (CRL), aby upewnić się, że żaden z certyfikatów używanych przez tę funkcję zostały odwołane.
  - Aby uzyskać pełną listę wymagań sieci, zobacz [uwierzytelnianie przekazywane usługi Azure Active Directory: Szybki start](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Hasła podanych przez użytkownika podczas logowania są szyfrowane w chmurze, zanim agentów uwierzytelniania w środowisku lokalnym akceptujesz na weryfikację pod kątem usługi Active Directory.
- Kanał HTTPS między usługą Azure AD i lokalnego agenta uwierzytelniania są zabezpieczane przy użyciu wzajemnego uwierzytelniania.
- Chroni Twoje konta użytkownika poprzez bezproblemowe współdziałanie z [zasady dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md), łącznie z uwierzytelniania Multi-Factor Authentication (MFA) [blokowanie uwierzytelniania starszych](../conditional-access/conditions.md) i [ Filtrowanie siłowego złamania hasła](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Składniki zaangażowane

Ogólne szczegółowe informacje o operacyjne, usługa Azure AD i bezpieczeństwo danych, zobacz [Centrum zaufania](https://azure.microsoft.com/support/trust-center/). Użyj uwierzytelniania przekazującego, podczas logowania użytkownika obejmuje następujące składniki:
- **Tokenu Zabezpieczającego usług Azure AD**: Bezstanowa Usługa tokenu zabezpieczającego (STS) przetwarza żądań logowania, która wystawia tokeny zabezpieczające do przeglądarki, klientów lub usług, zgodnie z potrzebami użytkowników.
- **Azure Service Bus**: Zapewnia, że komunikacji w chmurze z obsługą wiadomości przedsiębiorstwa i komunikację przekaźniki pomoże Ci połączyć rozwiązania lokalne z chmurą.
- **Azure AD Connect Agent uwierzytelniania**: Składnik w środowisku lokalnym, który odbiera i odpowiada na żądania weryfikacji hasła.
- **Azure SQL Database**: Przechowuje informacje dotyczące agentów uwierzytelniania Twojej dzierżawy, w tym do kluczy szyfrowania i metadanych.
- **Active Directory**: Lokalne usługi Active Directory, w którym są przechowywane kont użytkowników i haseł.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalowaniem i rejestrowaniem agentów uwierzytelniania

Agentów uwierzytelniania są zainstalowane i zarejestrowane w usłudze Azure AD po użytkownik albo:
   - [Włącz uwierzytelnianie przekazywane za pośrednictwem usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Dodaj więcej agentów uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Rozpoczynanie pracy Agent uwierzytelniania obejmuje trzy główne etapy:

1. Instalacja agenta uwierzytelniania
2. Rejestracja agenta uwierzytelniania
3. Inicjowanie agenta uwierzytelniania

W poniższych sekcjach omówiono te etapy szczegółowo.

### <a name="authentication-agent-installation"></a>Instalacja agenta uwierzytelniania

Tylko administratorzy globalni można zainstalować agenta uwierzytelniania (przy użyciu usługi Azure AD Connect lub autonomiczne) na serwerze w środowisku lokalnym. Instalacja dodaje dwa nowe wpisy, aby **Panelu sterowania** > **programy** > **programy i funkcje** listy:
- Agent uwierzytelniania aplikacji. Ta aplikacja działa przy użyciu [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) uprawnień.
- Aplikacja aktualizatora, która służy do automatycznej aktualizacji agenta uwierzytelniania. Ta aplikacja działa przy użyciu [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) uprawnień.

### <a name="authentication-agent-registration"></a>Rejestracja agenta uwierzytelniania

Po zainstalowaniu agenta uwierzytelniania, musi się zarejestrować w usłudze Azure AD. Usługa Azure AD przypisuje każdego agenta uwierzytelniania tożsamości cyfrowej, unikatową certyfikatu, który można użyć do bezpiecznej komunikacji z usługą Azure AD.

Procedura rejestracji także wiąże agenta uwierzytelniania z dzierżawą. Dzięki temu usługa Azure AD wie, że tego określonego agenta uwierzytelniania jest jedynym kontem z uprawnieniami do obsługi żądań weryfikacji haseł dla dzierżawy. Czynności te należy powtórzyć dla każdego nowego agenta uwierzytelniania, należy zarejestrować.

Agentów uwierzytelniania wykonaj następujące kroki, aby zarejestrować się z usługą Azure AD:

![Rejestracja agenta](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Usługa Azure AD po raz pierwszy żąda, czy administrator globalny zalogować się do usługi Azure AD przy użyciu swoich poświadczeń. Podczas logowania Agent uwierzytelniania uzyskuje token dostępu, której można użyć w imieniu administratora globalnego.
2. Agent uwierzytelniania następnie generowana jest para kluczy: klucz publiczny i klucz prywatny.
    - Pary kluczy jest generowana za pomocą standardowych szyfrowania RSA 2048-bitowych.
    - Klucz prywatny pozostaje na lokalnym serwerze, w którym znajduje się Agent uwierzytelniania.
3. Agent uwierzytelniania sprawia, że żądanie "rejestracja" do usługi Azure AD przy użyciu protokołu HTTPS za pomocą następujących składników, które są zawarte w żądaniu:
    - Token dostępu, uzyskaną w kroku 1.
    - Klucz publiczny, wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). Dotyczy to żądanie certyfikatu tożsamości cyfrowej, za pomocą usługi Azure AD jako jego urzędu certyfikacji (CA).
4. Usługa Azure AD sprawdza poprawność tokenu dostępu w żądaniu rejestracji i sprawdza, czy żądanie pochodzi z administratorem globalnym.
5. Następnie usługa Azure AD podpisuje i wysyła certyfikat tożsamości cyfrowej agenta uwierzytelniania.
    - Główny urząd certyfikacji w usłudze Azure AD jest używany do podpisywania certyfikatu. 

      > [!NOTE]
      > Ten urząd certyfikacji jest _nie_ w Windows zaufane główne urzędy certyfikacji są przechowywane.
    - Urząd certyfikacji jest używany tylko przez funkcję uwierzytelniania przekazywanego. Urząd certyfikacji jest używany tylko do podpisania obsługi podczas rejestracji agenta uwierzytelniania.
    -  Żadna z usługi Azure AD, użyj tego urzędu certyfikacji.
    - Podmiot certyfikatu (nazwa wyróżniająca lub DN) jest ustawiona na Twoim identyfikatorem dzierżawy. Ta nazwa Wyróżniająca jest identyfikatorem GUID, który unikatowo identyfikuje dzierżawy. Ta nazwa Wyróżniająca zakresów certyfikatu do użytku tylko z Twojej dzierżawy.
6. Usługa Azure AD przechowuje klucz publiczny agenta uwierzytelniania w usłudze Azure SQL database tylko usługi Azure AD z dostępem do.
7. Certyfikat (wydany w kroku 5) jest przechowywany na serwerze w środowisku lokalnym, w magazynie certyfikatów Windows (w szczególności w [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) lokalizacji). Jest on używany przez agenta uwierzytelniania i aplikacje Updater.

### <a name="authentication-agent-initialization"></a>Inicjowanie agenta uwierzytelniania

Po uruchomieniu agenta uwierzytelniania po raz pierwszy po rejestracji lub po serwera ponowne uruchomienie, musi w jakiś sposób do bezpiecznego komunikowania się z usługą Azure AD i zacznij akceptować żądania weryfikacji hasła.

![Inicjowanie agenta](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Poniżej przedstawiono, jak są inicjowane agentów uwierzytelniania:

1. Agent uwierzytelniania sprawia, że żądanie bootstrap ruchu wychodzącego do usługi Azure AD. 
    - To żądanie jest wykonywane za pośrednictwem portu 443 i znajduje się nad uwierzytelnionego wzajemnie kanału protokołu HTTPS. Żądanie używa tego samego certyfikatu, który został wystawiony podczas rejestracji agenta uwierzytelniania.
2. Usługa Azure AD odpowie na żądanie, zapewniając klucza dostępu do kolejki usługi Azure Service Bus, która jest unikatowa dla dzierżawy i który jest identyfikowany przez identyfikatora dzierżawy.
3. Agent uwierzytelniania sprawia, że trwałe wychodzące połączenia HTTPS (za pośrednictwem portu 443) kolejki. 
    - Agent uwierzytelniania jest teraz gotowy do pobrania i obsługi żądań sprawdzenie poprawności hasła.

Jeśli masz wielu agentów uwierzytelniania zarejestrowany w dzierżawie usługi, a następnie procedura inicjowania zapewnia, że każdy z nich łączy się z tej samej kolejki usługi Service Bus. 

## <a name="process-sign-in-requests"></a>Przetwarzanie żądań logowania 

Na poniższym diagramie przedstawiono, jak uwierzytelnianie przekazywane przetwarza żądania logowania użytkownika.

![Proces logowania](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Uwierzytelnianie przekazywane obsługuje żądania logowania użytkownika w następujący sposób: 

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa).
2. Jeśli użytkownik nie jest już zalogowany, aplikacja przekierowuje przeglądarki do strony logowania usługi Azure AD.
3. Współpracuje Usługa tokenu Zabezpieczającego usług Azure AD z tyłu **logowania użytkownika** strony.
4. Użytkownik musi wprowadzić swoją nazwę użytkownika do **logowania użytkownika** strony, a następnie wybiera **dalej** przycisku.
5. Użytkownik wprowadza swoje hasło w **logowania użytkownika** strony, a następnie wybiera **logowania** przycisku.
6. Nazwa użytkownika i hasło są przesyłane do usługi Azure AD usługi STS w żądaniu POST protokołu HTTPS.
7. Usługa Azure AD usługi STS pobiera kluczy publicznych dla wszystkich agentów uwierzytelniania zarejestrowany w dzierżawie usługi z bazy danych Azure SQL i szyfruje hasło za ich pomocą.
    - Generuje "N" zaszyfrowane wartości haseł dla agentów uwierzytelniania "N" zarejestrowany w dzierżawie.
8. Usługa Azure AD usługi STS umieszcza żądania weryfikacji hasła, która składa się z nazwy użytkownika i wartości zaszyfrowane hasło do kolejki usługi Service Bus, przeznaczony dla Twojej dzierżawy.
9. Ponieważ zainicjowane agentów uwierzytelniania są trwale dołączona do kolejki usługi Service Bus, pobiera jeden z dostępnych agentów uwierzytelniania żądania weryfikacji hasła.
10. Agent uwierzytelniania lokalizuje wartość zaszyfrowane hasło, która jest specyficzne dla swojego klucza publicznego przy użyciu identyfikatora i odszyfrowuje je, korzystając z kluczem prywatnym.
11. Agent uwierzytelniania podejmie próbę zweryfikowania nazwy użytkownika i hasła w usłudze Active Directory w środowisku lokalnym za pomocą [Win32 API funkcji LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) z **dwLogonType** parametr wartość **LOGON32_LOGON_NETWORK**. 
    - Ten interfejs API jest ten sam interfejs API, który jest używany przez usługi Active Directory Federation Services (AD FS) do logowania użytkowników w federacyjnym scenariuszu logowania.
    - Ten interfejs API zależy od rozdzielczości standardowej procesu w systemie Windows Server do zlokalizowania kontrolera domeny.
12. Agent uwierzytelniania odbiera wynik z usługi Active Directory, takie jak powodzenie, nazwa użytkownika lub hasło są niepoprawne lub hasło wygasło.

   > [!NOTE]
   > Jeśli Agent uwierzytelniania zakończy się niepowodzeniem podczas procesu logowania, zupełnie żądanie logowania zostało porzucone. Istnieje nie ręcznie wyłączyć logowania żądań od jednego agenta uwierzytelniania do innego agenta uwierzytelniania serwera lokalnego. Agenci komunikować się tylko w chmurze, a nie ze sobą.
13. Agent uwierzytelniania przekazuje wynik do tokenu Zabezpieczającego usług AD systemu Azure za pośrednictwem kanału wychodzącego HTTPS uwierzytelnionego wzajemnie za pośrednictwem portu 443. Wzajemne uwierzytelnianie przy użyciu certyfikatu wystawionego wcześniej do agenta uwierzytelniania, podczas rejestracji.
14. Usługi Azure AD usługi STS sprawdza, czy ten wynik jest skorelowane z określonego żądania logowania w dzierżawie.
15. Usługa Azure AD usługi STS będzie kontynuowane przy użyciu procedura logowania, zgodnie z konfiguracją. Na przykład jeśli sprawdzenie poprawności hasła zakończyło się pomyślnie, użytkownika może być wezwaniem uwierzytelniania wieloskładnikowego lub przekierowany z powrotem do aplikacji.

## <a name="operational-security-of-the-authentication-agents"></a>Bezpieczeństwo działania agentów uwierzytelniania

Aby upewnić się, że uwierzytelnianie przekazywane pozostanie bezpieczne pod względem usługi Azure AD okresowo odnawia agentów uwierzytelniania certyfikatów. Usługa Azure AD wyzwala odnowienia. Odnowienie nie są zarządzane przez agentów uwierzytelniania, samodzielnie.

![Bezpieczeństwo działania](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Aby odnowić relacji zaufania agenta uwierzytelniania w usłudze Azure AD:

1. Agent uwierzytelniania okresowo wysyła pakiet usługi ping usługi Azure AD co kilka godzin, aby sprawdzić, czy jest może odnowić swój certyfikat. Certyfikat zostanie odnowiony 30 dni przed jego wygaśnięciem.
    - To sprawdzenie odbywa się za pośrednictwem uwierzytelnionego wzajemnie kanału protokołu HTTPS i używa tego samego certyfikatu, który został wystawiony podczas rejestracji.
2. Jeśli usługa wskazuje, że nadszedł czas na odnowienie, Agent uwierzytelniania generuje nową parę kluczy: klucz publiczny i klucz prywatny.
    - Te klucze są generowane przy użyciu standardowych szyfrowania RSA 2048-bitowego szyfrowania.
    - Klucz prywatny nigdy nie opuszcza na lokalnym serwerze.
3. Agent uwierzytelniania następnie żąda "odnawiania certyfikatów" do usługi Azure AD przy użyciu protokołu HTTPS za pomocą następujących składników, które są zawarte w żądaniu:
    - Istniejącego certyfikatu, które są pobierane z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE w magazynie certyfikatów Windows. Istnieje nie administrator globalny zaangażowane w tej procedurze, więc nie ma żadnych token dostępu potrzebne w imieniu administratora globalnego.
    - Klucz publiczny, wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). Dotyczy to żądanie nowego certyfikatu tożsamości cyfrowej, za pomocą usługi Azure AD jako jego urzędu certyfikacji.
4. Usługa Azure AD weryfikuje istniejącego certyfikatu w żądanie odnowienia certyfikatu. Następnie sprawdza, czy żądanie pochodzi z agenta uwierzytelniania, zarejestrowanych w dzierżawie.
5. Jeśli istniejący certyfikat jest nadal ważny, usługa Azure AD następnie podpisuje nowego certyfikatu tożsamości cyfrowej i wystawia nowy certyfikat agenta uwierzytelniania. 
6. Jeśli istniejący certyfikat wygasł, usługi Azure AD Usuwa agenta uwierzytelniania z Twojej dzierżawy listę zarejestrowanych agentów uwierzytelniania. Administrator globalny musi ręcznie zainstalować i zarejestrować nowego agenta uwierzytelniania.
    - Użyj usługi Azure AD głównego urzędu certyfikacji do podpisywania certyfikatu.
    - Ustaw swój identyfikator dzierżawy, identyfikator GUID, który unikatowo identyfikuje dzierżawy podmiot certyfikatu (nazwy wyróżniającej lub nazwy domeny). Nazwa Wyróżniająca zakresów certyfikat, który ma tylko dzierżawy.
6. Usługa Azure AD przechowuje nowego klucza publicznego agenta uwierzytelniania programu w bazie danych Azure SQL, to tylko ma dostęp do. Również powoduje unieważnienie starego klucza publicznego skojarzonego z agentem uwierzytelniania.
7. Nowy certyfikat (pojawiły się w kroku 5) jest następnie przechowywany w magazynie certyfikatów Windows na serwerze (w szczególności w [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) lokalizacji).
    - Ponieważ procedurę odnowienia zaufania występuje nieinteraktywnego (bez obecności administrator globalny), Agent uwierzytelniania nie ma już dostępu do aktualizowania istniejącego certyfikatu w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Ta procedura nie powoduje usunięcia sam certyfikat z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nowy certyfikat jest używany do uwierzytelniania z tego punktu w. Każdy z kolejnych odnowienia certyfikatu zastępuje certyfikat w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatyczna aktualizacja agentów uwierzytelniania

Aplikacja aktualizatora automatycznie aktualizuje agenta uwierzytelniania, po wydaniu nowej wersji (za pomocą poprawki lub ulepszenia wydajności). Aplikacja aktualizatora nie obsługuje wszystkie żądania weryfikacji hasła dla Twojej dzierżawy.

Usługa Azure AD obsługuje nową wersję oprogramowania jako zalogowany **pakietu Instalatora Windows (MSI)**. Plik MSI jest podpisany przy użyciu [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) z SHA256 algorytmu skrótu. 

![Automatyczna aktualizacja](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Aby automatycznie Aktualizuj agenta uwierzytelniania:

1. Polecenia ping aplikacja aktualizatora usługi Azure AD do sprawdzania, czy jest nowa wersja agenta uwierzytelniania programu na godzinę. 
    - To sprawdzenie odbywa się za pośrednictwem uwierzytelnionego wzajemnie kanału protokołu HTTPS przy użyciu tego samego certyfikatu, który został wystawiony podczas rejestracji. Agent uwierzytelniania i aktualizacji współdzielą certyfikat przechowywany na serwerze.
2. Jeśli dostępna jest nowa wersja, usługa Azure AD zwraca podpisanego pliku MSI do aktualizacji.
3. Aktualizacji sprawdza, czy plik MSI jest podpisany przez firmę Microsoft.
4. Aktualizator uruchamiany plik MSI. Ta akcja obejmuje następujące czynności:

   > [!NOTE]
   > Aktualizacji uruchamiany przy użyciu [systemu lokalnego](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) uprawnień.

    - Zatrzymuje usługę agenta uwierzytelniania
    - Instaluje nową wersję agenta uwierzytelniania na serwerze
    - Uruchamia ponownie usługę Agent uwierzytelniania

>[!NOTE]
>W przypadku wielu agentów uwierzytelniania zarejestrowany w dzierżawie usługi Azure AD nie odnawiać swoje certyfikaty lub je zaktualizować w tym samym czasie. Zamiast tego usługa Azure AD ma jedną pojedynczo, aby zapewnić wysoką dostępność żądań logowania.
>


## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i te, które nie są.
- [Szybki start](how-to-connect-pta-quick-start.md): Rozpocznij pracę na uwierzytelnianie przekazywane usługi AD platformy Azure.
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadpdownload) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): Konfigurowanie funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Jak działa](how-to-connect-pta-how-it-works.md): Poznaj podstawy działania uwierzytelniania przekazywanego usługi AD platformy Azure.
- [Często zadawane pytania dotyczące](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie Jednokrotne usługi Azure AD](how-to-connect-sso.md): Dowiedz się więcej na temat tej dodatkowej funkcji.
