---
title: Azure Active Directory przekazywanego uwierzytelniania zabezpieczeń nowości | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD) chroni konta lokalnego
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cb8382a9801c3570a190259416d846fe518cc6ea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595040"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory przekazywanego uwierzytelniania zabezpieczeń nowości

Ten artykuł zawiera bardziej szczegółowy opis działania usługi Azure Active Directory (Azure AD) przekazywanego uwierzytelniania. Uwzględniono w szczególności aspekty zabezpieczeń funkcji. W tym artykule jest zabezpieczeń i administratorów IT, dyrektora zgodności i funkcjonariusze i innym specjalistom IT, którzy są odpowiedzialni za bezpieczeństwo IT i zgodności w małych i średnich wielkości organizacji lub dużych przedsiębiorstw.

Tematy skierowana obejmują:
- Szczegółowe informacje techniczne dotyczące sposobu instalowania i zarejestrować agentów uwierzytelniania.
- Szczegółowe informacje techniczne dotyczące szyfrowania hasła podczas logowania użytkownika.
- Zabezpieczenia kanały między lokalnymi agentów uwierzytelniania i Azure AD.
- Szczegółowe informacje techniczne dotyczące zachowania agentów uwierzytelniania pod względem zabezpieczeń.
- Inne tematy związane z zabezpieczeniami.

## <a name="key-security-capabilities"></a>Funkcje zabezpieczeń klucza

Są to bezpieczeństwo kluczy aspektów tej funkcji:
- Jest on oparty na bezpieczną architekturę dzierżawcza multi, który zapewnia izolację żądań logowania między dzierżawcami.
- Lokalne hasła nigdy nie są przechowywane w chmurze w jakimkolwiek formularzu.
- Agenci uwierzytelniania lokalnego nasłuchiwania i odpowiedzieć, żądania weryfikacji haseł tylko nawiązywać połączenia wychodzące z sieci. Nie jest wymagane do zainstalowania tych agentów uwierzytelniania w sieci obwodowej.
- Tylko na standardowe porty (80 i 443) są używane dla komunikacji wychodzącej czynnikami uwierzytelniania usługi Azure AD. Nie trzeba otworzyć porty dla ruchu przychodzącego na zaporze. 
  - Port 443 jest używany do cała komunikacja wychodząca uwierzytelniony.
  - Port 80 jest używany tylko w przypadku pobierania odwołania list certyfikatów (CRL) aby upewnić się, że żaden z tych certyfikatów używanych przez tę funkcję zostały odwołane.
  - Aby uzyskać pełną listę wymagań sieciowych, zobacz [uwierzytelniania przekazywanego Azure Active Directory: Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Hasła podanych przez użytkownika podczas logowania są szyfrowane w chmurze, aby agentów uwierzytelniania lokalnego akceptowanie je na weryfikację pod kątem usługi Active Directory.
- Kanał HTTPS między usługą Azure AD i lokalnymi Agent uwierzytelniania jest zabezpieczone przy użyciu wzajemnego uwierzytelniania.
- Tę funkcję można bezproblemowo zintegrować z możliwości ochrony chmury Azure AD, takie jak zasady dostępu warunkowego (w tym uwierzytelnianie wieloskładnikowe Azure), ochrony tożsamości i blokady inteligentnej.

## <a name="components-involved"></a>Składniki zaangażowane

Aby uzyskać ogólne informacje o usłudze operational, usługi Azure AD i bezpieczeństwa danych, zobacz [Centrum zaufania](https://azure.microsoft.com/support/trust-center/). Korzystając z uwierzytelniania przekazywanego do logowania użytkownika obejmuje następujące składniki:
- **Azure AD STS**: bezstanowych usługę tokenu zabezpieczającego (STS), który przetwarza żądania rejestracji i wystawia tokeny zabezpieczające do przeglądarek użytkowników, klientów lub usług, zgodnie z wymaganiami.
- **Usługa Azure Service Bus**: zapewnia włączoną obsługę chmury komunikacji z obsługą wiadomości przedsiębiorstwa i komunikacji przekaźniki, która ułatwia łączenie rozwiązań lokalnych z chmurą.
- **Agent Azure AD Connect uwierzytelniania**: składnik lokalnymi nasłuchuje i odpowiada na żądania sprawdzania poprawności hasła.
- **Baza danych SQL Azure**: przechowuje informacje dotyczące agentów uwierzytelniania swojej dzierżawy, łącznie z ich metadanych i kluczy szyfrowania.
- **Usługi Active Directory**: lokalnej usługi Active Directory, przechowywania kont użytkowników i haseł.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalowanie i rejestrowanie agentów uwierzytelniania

Agenci uwierzytelniania są zainstalowane i zarejestrowane w usłudze Azure AD podczas możesz albo:
   - [Włączanie uwierzytelniania przekazywanego za pośrednictwem usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Dodaj więcej agentów uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Pobieranie pracy Agent uwierzytelniania obejmuje trzy główne fazy:

1. Instalacja agenta uwierzytelniania
2. Rejestracja agenta uwierzytelniania
3. Inicjowanie agenta uwierzytelniania

W poniższych sekcjach omówiono te fazy szczegółowo.

### <a name="authentication-agent-installation"></a>Instalacja agenta uwierzytelniania

Tylko administratorzy globalni można zainstalować agenta uwierzytelniania (przy użyciu usługi Azure AD Connect lub autonomiczne) na serwerze lokalnym. Instalacja dodaje dwa nowe wpisy, aby **Panelu sterowania** > **programy** > **programy i funkcje** listy:
- Agent uwierzytelniania aplikacji. Ta aplikacja działa z [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) uprawnienia.
- Aplikacja aktualizacji, która służy do automatycznej aktualizacji agenta uwierzytelniania. Ta aplikacja działa z [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) uprawnienia.

### <a name="authentication-agent-registration"></a>Rejestracja agenta uwierzytelniania

Po zainstalowaniu agenta uwierzytelniania, musi się zarejestrować w usłudze Azure AD. Usługi Azure AD przypisuje każdego agenta uwierzytelniania tożsamości cyfrowych, unikatową certyfikatu, który można użyć do zapewnienia bezpiecznej komunikacji z usługą Azure AD.

Procedury rejestracji także wiąże Agent uwierzytelniania z dzierżawą. Dzięki temu usługi Azure AD wie, że ten określonych Agent uwierzytelniania jest tylko jeden autoryzowane do obsługi żądań sprawdzania poprawności hasła dla dzierżawy. Czynności te należy powtórzyć dla każdego nowego agenta uwierzytelniania należy zarejestrować.

Agentów uwierzytelniania wykonaj następujące kroki, aby rejestrować się w usłudze Azure AD:

![Rejestracja agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Usługi Azure AD po raz pierwszy żąda, czy administrator globalny zalogować się do usługi Azure AD przy użyciu poświadczeń. Podczas logowania uwierzytelniania Agent uzyskuje token dostępu, której można użyć w imieniu administratora globalnego.
2. Agent uwierzytelniania następnie generuje parę kluczy: klucz publiczny i klucz prywatny.
    - Pary kluczy jest generowany przy użyciu standardowych szyfrowania RSA 2048-bitowego.
    - Klucz prywatny pozostaje lokalnego serwera, w którym znajduje się Agent uwierzytelniania.
3. Agent uwierzytelniania wysyła żądanie "rejestracji" do usługi Azure AD przy użyciu protokołu HTTPS, z następujących składników, które są zawarte w żądaniu:
    - Token dostępu uzyskaną w kroku 1.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądania certyfikatu). Dotyczy to żądanie certyfikatu tożsamości cyfrowych, za pomocą usługi Azure AD jako jego urzędu certyfikacji (CA).
4. Usługi Azure AD weryfikuje token dostępu w żądaniu rejestracji i sprawdza, czy żądanie pochodzi z administratora globalnego.
5. Następnie usługi Azure AD podpisuje i wysyła do agenta uwierzytelniania certyfikatów cyfrowych tożsamość.
    - Główny urząd certyfikacji w usłudze Azure AD jest używany do podpisywania certyfikatu. 

     >[!NOTE]
     > Ten urząd certyfikacji jest _nie_ w systemie Windows zaufane główne urzędy certyfikacji są przechowywane.
    - Urząd certyfikacji jest używany tylko przez funkcję uwierzytelniania przekazywanego. Urząd certyfikacji jest używany tylko do podpisania obsługi podczas rejestracji agenta uwierzytelniania.
    -  Brak innych usług Azure AD, użyj tego urzędu certyfikacji.
    - Podmiot certyfikatu (nazwa wyróżniająca lub nazwa Wyróżniająca) wynosi swojego identyfikatora dzierżawcy. Ta nazwa Wyróżniająca jest identyfikatorem GUID, który unikatowo identyfikuje dzierżawców. Ta nazwa Wyróżniająca zakresów certyfikat do użytku tylko z dzierżawą.
6. Usługi Azure AD przechowuje klucz publiczny Agent uwierzytelniania w bazie danych Azure SQL, która tylko usługi Azure AD ma dostęp do.
7. Certyfikat (wydane w kroku 5) jest przechowywany na serwerze lokalnym w magazynie certyfikatów systemu Windows (w szczególności w [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) lokalizacji). Jest on używany zarówno przez agenta uwierzytelniania i aktualizacji aplikacji.

### <a name="authentication-agent-initialization"></a>Inicjowanie agenta uwierzytelniania

Po uruchomieniu Agent uwierzytelniania po raz pierwszy po rejestracji lub po serwera należy ponownie uruchomić, musi on sposób do bezpiecznego komunikowania się z usługą Azure AD i uruchamiania, akceptując żądania sprawdzania poprawności hasła.

![Inicjowanie agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Oto, jak są inicjowane agentów uwierzytelniania:

1. Agent uwierzytelniania zgłasza wychodzącego bootstrap żądanie do usługi Azure AD. 
    - To żądanie jest nawiązywane za pośrednictwem portu 443 i jest za pośrednictwem uwierzytelnionego wzajemnie kanału HTTPS. Żądanie używa tego samego certyfikatu, który został wystawiony podczas rejestracji agenta uwierzytelniania.
2. Zapewniając klucz dostępu dla kolejek usługi Azure Service Bus jest unikatowa dla Twojej dzierżawy i który jest identyfikowany przez identyfikatora dzierżawy usługi Azure AD odpowiada na żądanie
3. Agent uwierzytelniania tworzy trwały wychodzące połączenie HTTPS (przez port 443) kolejki. 
    - Agent uwierzytelniania jest teraz gotowy do pobierania i obsługiwać żądania sprawdzenie poprawności hasła.

Jeśli masz wielu agentów uwierzytelniania zarejestrowane na Twojej dzierżawy, a następnie procedura inicjowania zapewnia, że każdy z nich łączy się z tej samej kolejki usługi Service Bus. 

## <a name="process-sign-in-requests"></a>Przetwarzanie żądań logowania 

Na poniższym diagramie przedstawiono sposób uwierzytelniania przekazywanego przetwarza żądania logowania użytkownika.

![Logowanie procesu](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Uwierzytelniania przekazywanego obsługi żądania logowania użytkownika w następujący sposób: 

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa).
2. Jeśli użytkownik nie jest już zalogowany, aplikacja przekierowuje przeglądarki do strony logowania usługi Azure AD.
3. Tworzenie kopii odpowiada usługi STS usługi Azure AD za pomocą **logowania użytkownika** strony.
4. Użytkownik wprowadza nazwę użytkownika i hasło do **logowania użytkownika** strony, a następnie wybiera **logowania** przycisku.
5. Nazwa użytkownika i hasło są przesyłane do usługi Azure AD STS w żądaniu POST protokołu HTTPS.
6. Usługi Azure AD STS pobiera klucze publiczne dla wszystkich agentów uwierzytelniania zarejestrowane na Twojej dzierżawy z bazy danych Azure SQL i szyfrowanie hasła za ich pomocą. 
    - Generuje "N" zaszyfrowane hasło wartości dla agentów "N" uwierzytelnianie zarejestrowane na Twojej dzierżawy.
7. Usługi Azure AD STS umieszcza żądania sprawdzenia poprawności hasła, która składa się z nazwy użytkownika i wartości zaszyfrowane hasło do kolejki usługi Service Bus przeznaczony dla Twojej dzierżawy.
8. Ponieważ zainicjowane agentów uwierzytelniania jest trwały podłączony do kolejki usługi Service Bus, pobiera jeden z dostępnych agentów uwierzytelniania żądania sprawdzenia poprawności hasła.
9. Agent uwierzytelniania lokalizuje wartość zaszyfrowane hasło, która jest specyficzne dla swojego klucza publicznego, za pomocą identyfikatora i odszyfrowuje je, korzystając z kluczem prywatnym.
10. Agent uwierzytelniania próbuje zweryfikować nazwę użytkownika i hasło z lokalną usługą Active Directory za pomocą [Win32 API funkcji LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) z **dwLogonType** ustawiona **LOGON32_LOGON_NETWORK**. 
    - Ten interfejs API jest tego samego interfejsu API, który jest używany przez usługi Active Directory Federation Services (AD FS) do logowania użytkowników w federacyjnym scenariuszu logowania.
    - Ten interfejs API korzysta z procesu rozpoznawania standardowego w systemie Windows Server można znaleźć kontrolera domeny.
11. Agent uwierzytelniania odbiera wynik z usługi Active Directory, takie jak powodzenie, nazwa użytkownika lub hasło jest nieprawidłowe lub hasło wygasło.
12. Agent uwierzytelniania przesyła wynik do usługi Azure AD STS za pośrednictwem uwierzytelnionego wzajemnie wychodzącego kanału HTTPS przez port 443. Uwierzytelnianie wzajemne używa certyfikatu, wcześniej wydane agentowi uwierzytelnianie podczas rejestracji.
13. Usługi Azure AD STS sprawdza, czy wynik skorelowany z określonym żądaniem logowania w dzierżawie.
14. Usługi Azure AD STS będzie kontynuowane przy użyciu procedury logowania, zgodnie z konfiguracją. Na przykład jeśli sprawdzanie poprawności hasła zakończyło się pomyślnie, użytkownik może wezwał uwierzytelnianie wieloskładnikowe lub przekierowany z powrotem do aplikacji.

## <a name="operational-security-of-the-authentication-agents"></a>Bezpieczeństwa operacyjnego agentów uwierzytelniania

Aby upewnić się, że uwierzytelniania przekazywanego pozostaje pod względem bezpiecznego, usługi Azure AD okresowo odnawia agentów uwierzytelniania certyfikatów. Usługi Azure AD wyzwala odnowienia. Odnowienia nie podlegają agentów uwierzytelniania samodzielnie.

![Bezpieczeństwo działania](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Aby odnowić Agent uwierzytelniania zaufania z usługą Azure AD:

1. Agent uwierzytelniania okresowo wysyła pakiet usługi ping Azure AD co kilka godzin, aby sprawdzić, czy jest może odnowić swój certyfikat. 
    - To sprawdzenie odbywa się za pośrednictwem uwierzytelnionego wzajemnie kanału HTTPS i używa tego samego certyfikatu, który został wystawiony podczas rejestracji.
2. Jeśli usługa wskazuje, czy nadszedł czas, aby odnowić, Agent uwierzytelniania generuje parę kluczy: klucz publiczny i klucz prywatny.
    - Klucze te są generowane przy użyciu standardowych szyfrowania RSA 2048-bitowego.
    - Klucz prywatny nigdy nie opuszcza serwera lokalnego.
3. Agent uwierzytelniania następnie żąda "odnawiania certyfikatu" do usługi Azure AD przy użyciu protokołu HTTPS, z następujących składników, które są zawarte w żądaniu:
    - Istniejący certyfikat zostanie pobrany z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE w magazynie certyfikatów systemu Windows. Brak nie administratora globalnego zaangażowane w tej procedurze, więc nie ma żadnych tokenu dostępu, wymagane w imieniu administratora globalnego.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądania certyfikatu). To żądanie dotyczy nowego świadectwa tożsamości cyfrowych, z usługą Azure AD jako jego urzędu certyfikacji.
4. Usługi Azure AD weryfikuje istniejącego certyfikatu w żądanie odnowienia certyfikatu. Następnie weryfikuje, czy żądanie pochodzi z agenta uwierzytelniania zarejestrowane na Twojej dzierżawy.
5. W przypadku istniejący certyfikat jest nadal ważny, usługa Azure AD następnie podpisuje nowego certyfikatu tożsamości cyfrowych i wystawia nowego świadectwa Agent uwierzytelniania. 
6. Jeśli istniejący certyfikat wygasł, usługi Azure AD Usuwa agenta uwierzytelniania z Twojej dzierżawy listę zarejestrowanych agentów uwierzytelniania. Następnie administrator globalny musi ręcznie zainstaluj i Zarejestruj nowy Agent uwierzytelniania.
    - Użyj usługi Azure AD głównego urzędu certyfikacji do podpisywania certyfikatu.
    - Podmiot certyfikatu (nazwa wyróżniająca lub nazwa Wyróżniająca) ustaw wartość Identyfikatora dzierżawy, identyfikator GUID, który unikatowo identyfikuje dzierżawców. Nazwa Wyróżniająca zakresów certyfikatu tylko dzierżawcy.
6. Usługi Azure AD przechowuje nowego klucza publicznego Agent uwierzytelniania w bazie danych Azure SQL, którą tylko ma dostęp do. Powoduje również unieważnienie starego klucza publicznego skojarzonego z agentem uwierzytelniania.
7. Nowy certyfikat (wydane w kroku 5) jest następnie przechowywany w magazynie certyfikatów systemu Windows na serwerze (w szczególności w [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) lokalizacji).
    - Ponieważ procedurę odnowienia zaufania występuje nieinteraktywnie (bez obecności administratora globalnego), Agent uwierzytelniania nie ma już dostępu, aby zaktualizować istniejący certyfikat w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Ta procedura nie powoduje usunięcia sam certyfikat z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nowy certyfikat jest używany do uwierzytelniania z tego punktu na. Każdy z kolejnych odnowienia certyfikatu zastępuje certyfikat w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatyczne aktualizowanie agentów uwierzytelniania

Po wydaniu nowej wersji aplikacji Updater automatycznie aktualizuje Agent uwierzytelniania. Aplikacja nie obsługuje wszystkie żądania weryfikacji hasła dla dzierżawy. 

Usługi Azure AD obsługuje nowej wersji oprogramowania jako zalogowany **pakiet Instalatora Windows (MSI)**. Plik MSI jest podpisany przy użyciu [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) z SHA256 jako algorytmu skrótu. 

![Automatyczna aktualizacja](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Do automatycznej aktualizacji agenta uwierzytelniania:

1. Aktualizator pakietów aplikacji usługi Azure AD co godzinę, aby sprawdzić, czy jest nowa wersja agenta uwierzytelniania. 
    - To sprawdzenie odbywa się za pośrednictwem uwierzytelnionego wzajemnie kanału HTTPS przy użyciu tego samego certyfikatu, który został wystawiony podczas rejestracji. Agent uwierzytelniania i aktualizacji Udostępnianie certyfikatów przechowywanych na serwerze.
2. Jeśli dostępna jest nowa wersja, usługi Azure AD zwraca podpisem MSI do aktualizacji.
3. Aktualizacji sprawdza, czy plik MSI jest podpisany przez firmę Microsoft.
4. Uruchamia Updater MSI. Ta akcja obejmuje następujące kroki:

 > [!NOTE]
 > Uruchamia aktualizacji przy użyciu [systemu lokalnego](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) uprawnienia.

    - Zatrzymuje usługę agenta uwierzytelniania
    - Zainstalowana nowa wersja agenta uwierzytelniania na serwerze
    - Uruchamia ponownie usługę Agent uwierzytelniania

>[!NOTE]
>Jeśli masz wielu agentów uwierzytelniania zarejestrowane na Twojej dzierżawy usługi Azure AD nie odnawiać swoje certyfikaty lub zaktualizuj je w tym samym czasie. Zamiast tego usługi Azure AD jest stopniowo zapewniające wysoką dostępność żądań logowania.
>


## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i zostały.
- [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md): rozpocząć pracę na Azure AD przekazywanego uwierzytelniania.
- [Inteligentne blokady](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Konfigurowanie funkcji blokady inteligentnej na swojej dzierżawy, aby chronić kont użytkowników.
- [Jak działa](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Poznaj podstawy działania usługi Azure AD przekazywanego uwierzytelniania.
- [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Azure AD SSO bezproblemowe](active-directory-aadconnect-sso.md): Dowiedz się więcej o tej funkcji uzupełniające.
