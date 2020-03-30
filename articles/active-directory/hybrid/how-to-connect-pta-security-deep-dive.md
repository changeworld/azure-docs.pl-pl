---
title: Głębokie skanowanie zabezpieczeń uwierzytelniania przekazywanego usługi Azure Active Directory| Dokumenty firmy Microsoft
description: W tym artykule opisano, jak uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) chroni konta lokalne
services: active-directory
keywords: Uwierzytelnianie przekazywanie usługi Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
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
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185557"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Głębokie głębokie nurkowanie zabezpieczeń uwierzytelniania przekazywanego usługi Azure Active Directory

Ten artykuł zawiera bardziej szczegółowy opis działania uwierzytelniania przekazowego usługi Azure Active Directory (Azure AD). Koncentruje się na aspektach bezpieczeństwa tej funkcji. Ten artykuł dotyczy administratorów zabezpieczeń i IT, głównych inspektorów ds.

Poruszane tematy obejmują:
- Szczegółowe informacje techniczne dotyczące instalowania i rejestrowania agentów uwierzytelniania.
- Szczegółowe informacje techniczne dotyczące szyfrowania haseł podczas logowania użytkownika.
- Zabezpieczenia kanałów między lokalnymi agentami uwierzytelniania a usługą Azure AD.
- Szczegółowe informacje techniczne dotyczące sposobu zapewnienia bezpieczeństwa operacyjnego agentów uwierzytelniania.
- Inne tematy związane z zabezpieczeniami.

## <a name="key-security-capabilities"></a>Kluczowe funkcje zabezpieczeń

Oto kluczowe aspekty bezpieczeństwa tej funkcji:
- Jest on zbudowany na bezpiecznej architektury wielodostępnej, która zapewnia izolację żądań logowania między dzierżawcami.
- Hasła lokalne nigdy nie są przechowywane w chmurze w żadnej formie.
- Lokalni agenci uwierzytelniania, którzy nasłuchują żądań sprawdzania poprawności hasła i odpowiadają na nie, tylko nawiązują połączenia wychodzące z sieci. Nie ma wymogu instalowania tych agentów uwierzytelniania w sieci obwodowej (DMZ). Najlepszym rozwiązaniem jest traktowanie wszystkich serwerów z agentami uwierzytelniania jako systemów poziomu 0 (patrz [odwołanie).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Tylko standardowe porty (80 i 443) są używane do komunikacji wychodzącej z agentów uwierzytelniania do usługi Azure AD. Nie trzeba otwierać portów przychodzących na zaporze. 
  - Port 443 jest używany dla całej uwierzytelnionej komunikacji wychodzącej.
  - Port 80 jest używany tylko do pobierania list odwołania certyfikatów (CRL), aby upewnić się, że żaden z certyfikatów używanych przez tę funkcję nie został odwołany.
  - Aby uzyskać pełną listę wymagań sieciowych, zobacz [Uwierzytelnianie przekazywane usługi Azure Active Directory: Szybki start](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Hasła, które użytkownicy podają podczas logowania, są szyfrowane w chmurze, zanim lokalni agenci uwierzytelniania zaakceptują je do weryfikacji w usłudze Active Directory.
- Kanał HTTPS między usługą Azure AD a lokalnym agentem uwierzytelniania jest zabezpieczony przy użyciu wzajemnego uwierzytelniania.
- Chroni konta użytkowników, bezproblemowo współpracując z [zasadami dostępu warunkowego usługi Azure AD,](../active-directory-conditional-access-azure-portal.md)w tym uwierzytelnianiem wieloskładnikowym (MFA), [blokowaniem starszego uwierzytelniania](../conditional-access/concept-conditional-access-conditions.md) i [filtrowaniem ataków na hasła.](../authentication/howto-password-smart-lockout.md)

## <a name="components-involved"></a>Komponenty zaangażowane

Aby uzyskać ogólne informacje na temat zabezpieczeń operacyjnych, usług i danych usługi Azure AD, zobacz [Centrum zaufania](https://azure.microsoft.com/support/trust-center/). Następujące składniki są zaangażowane podczas korzystania z uwierzytelniania przekazywania do logowania użytkownika:
- **Usługa Azure AD STS:** usługa zabezpieczeń bezstanowych (STS), która przetwarza żądania logowania i wystawia tokeny zabezpieczające do przeglądarek użytkowników, klientów lub usług zgodnie z wymaganiami.
- **Usługa Azure Service Bus:** zapewnia komunikację w chmurze z komunikacją w zakresie obsługi wiadomości i przekazywania w przedsiębiorstwie, która ułatwia łączenie rozwiązań lokalnych z chmurą.
- **Agent uwierzytelniania usługi Azure AD Connect:** składnik lokalny, który nasłuchuje żądań sprawdzania poprawności haseł i odpowiada na nie.
- **Usługa Azure SQL Database:** przechowuje informacje o agentach uwierzytelniania dzierżawy, w tym ich metadane i klucze szyfrowania.
- **Usługa Active Directory:** lokalna usługa Active Directory, w której są przechowywane konta użytkowników i ich hasła.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalacja i rejestracja agentów uwierzytelniania

Agenci uwierzytelniania są instalowani i rejestrowani w usłudze Azure AD podczas:
   - [Włączanie uwierzytelniania przekazywanego za pośrednictwem usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Dodaj więcej agentów uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Praca agenta uwierzytelniania obejmuje trzy główne fazy:

1. Instalacja agenta uwierzytelniania
2. Rejestracja agenta uwierzytelniania
3. Inicjowanie agenta uwierzytelniania

W poniższych sekcjach szczegółowo omówiono te fazy.

### <a name="authentication-agent-installation"></a>Instalacja agenta uwierzytelniania

Tylko administratorzy globalni mogą zainstalować agenta uwierzytelniania (przy użyciu usługi Azure AD Connect lub autonomicznego) na serwerze lokalnym. Instalacja dodaje dwa nowe wpisy do listy**Programy** > **i funkcje** **Panelu** > sterowania:
- Sama aplikacja Agenta uwierzytelniania. Ta aplikacja jest uruchamiana z uprawnieniami [NetworkService.](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)
- Aplikacja Updater, która jest używana do automatycznej aktualizacji agenta uwierzytelniania. Ta aplikacja jest uruchamiana z uprawnieniami [LocalSystem.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

### <a name="authentication-agent-registration"></a>Rejestracja agenta uwierzytelniania

Po zainstalowaniu agenta uwierzytelniania musi zarejestrować się w usłudze Azure AD. Usługa Azure AD przypisuje każdemu agentowi uwierzytelniania unikatowy certyfikat tożsamości cyfrowej, którego może używać do bezpiecznej komunikacji z usługą Azure AD.

Procedura rejestracji wiąże również agenta uwierzytelniania z dzierżawą. Dzięki temu usługa Azure AD wie, że ten konkretny agent uwierzytelniania jest jedynym autoryzowanym agentem do obsługi żądań sprawdzania poprawności hasła dla dzierżawy. Ta procedura jest powtarzana dla każdego nowego agenta uwierzytelniania, który rejestrujesz.

Agenci uwierzytelniania używają następujących kroków, aby zarejestrować się w usłudze Azure AD:

![Rejestracja agenta](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Usługa Azure AD najpierw żąda, aby administrator globalny zalogował się do usługi Azure AD przy użyciu poświadczeń. Podczas logowania agent uwierzytelniania uzyskuje token dostępu, który może używać w imieniu administratora globalnego.
2. Agent uwierzytelniania następnie generuje parę kluczy: klucz publiczny i klucz prywatny.
    - Para kluczy jest generowana za pomocą standardowego szyfrowania RSA 2048-bitowego.
    - Klucz prywatny pozostaje na serwerze lokalnym, na którym znajduje się agent uwierzytelniania.
3. Agent uwierzytelniania sprawia, że żądanie "rejestracji" do usługi Azure AD za pośrednictwem protokołu HTTPS, z następujących składników zawartych w żądaniu:
    - Token dostępu nabyty w kroku 1.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). To żądanie dotyczy certyfikatu tożsamości cyfrowej z usługą Azure AD jako urzędem certyfikacji (CA).
4. Usługa Azure AD sprawdza poprawność tokenu dostępu w żądaniu rejestracji i sprawdza, czy żądanie pochodzi od administratora globalnego.
5. Usługa Azure AD następnie podpisuje i wysyła certyfikat tożsamości cyfrowej z powrotem do agenta uwierzytelniania.
    - Główny urząd certyfikacji w usłudze Azure AD służy do podpisywania certyfikatu. 

      > [!NOTE]
      > Ten urząd certyfikacji _nie_ znajduje się w magazynie zaufanych urzędów certyfikacji systemu Windows.
    - Urząd certyfikacji jest używany tylko przez funkcję uwierzytelniania przekazywanego. Urząd certyfikacji jest używany tylko do podpisywania dostawców obsługi klienta podczas rejestracji agenta uwierzytelniania.
    -  Żadna z innych usług Azure AD nie korzysta z tego urzędu certyfikacji.
    - Temat certyfikatu (Nazwa wyróżniająca lub Nazwa wyróżniająca) jest ustawiony na identyfikator dzierżawy. Ten numer DN jest identyfikatorem GUID, który jednoznacznie identyfikuje dzierżawę. Ten numer DN obejmuje certyfikat do użytku tylko z dzierżawą.
6. Usługa Azure AD przechowuje klucz publiczny agenta uwierzytelniania w bazie danych SQL platformy Azure, do której ma dostęp tylko usługa Azure AD.
7. Certyfikat (wystawiony w kroku 5) jest przechowywany na serwerze lokalnym w magazynie certyfikatów systemu Windows (w szczególności w [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) lokalizacji). Jest on używany zarówno przez agenta uwierzytelniania i aplikacji Updater.

### <a name="authentication-agent-initialization"></a>Inicjowanie agenta uwierzytelniania

Po uruchomieniu agenta uwierzytelniania po raz pierwszy po rejestracji lub po ponownym uruchomieniu serwera, potrzebuje sposobu, aby bezpiecznie komunikować się z usługą Azure AD i rozpocząć akceptowanie żądań sprawdzania poprawności hasła.

![Inicjowanie agenta](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Oto jak inicjowane są agenci uwierzytelniania:

1. Agent uwierzytelniania wykonuje wychodzące żądanie bootstrap do usługi Azure AD. 
    - To żądanie jest dokonywane za pośrednictwem portu 443 i jest za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS. Żądanie używa tego samego certyfikatu, który został wystawiony podczas rejestracji agenta uwierzytelniania.
2. Usługa Azure AD odpowiada na żądanie, udostępniając klucz dostępu do kolejki usługi Azure Service Bus, która jest unikatowa dla dzierżawy i która jest identyfikowana przez identyfikator dzierżawy.
3. Agent uwierzytelniania tworzy trwałe wychodzące połączenie HTTPS (za pośrednictwem portu 443) do kolejki. 
    - Agent uwierzytelniania jest teraz gotowy do pobierania i obsługi żądań sprawdzania poprawności hasła.

Jeśli w dzierżawie jest zarejestrowanych wielu agentów uwierzytelniania, procedura inicjowania gwarantuje, że każdy z nich łączy się z tą samą kolejką usługi Service Bus. 

## <a name="process-sign-in-requests"></a>Przetwarzanie żądań logowania 

Na poniższym diagramie pokazano, jak uwierzytelnianie przekazywane przetwarza żądania logowania użytkownika.

![Proces logowania](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Uwierzytelnianie przekazywane obsługuje żądanie logowania użytkownika w następujący sposób: 

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa).
2. Jeśli użytkownik nie jest jeszcze zalogowany, aplikacja przekierowuje przeglądarkę do strony logowania usługi Azure AD.
3. Usługa Azure AD STS odpowiada z powrotem na stronie **logowania użytkownika.**
4. Użytkownik wprowadza swoją nazwę użytkownika na stronie **logowania użytkownika,** a następnie wybiera przycisk **Dalej.**
5. Użytkownik wprowadza swoje hasło na stronie **logowania użytkownika,** a następnie wybiera przycisk **Logowania.**
6. Nazwa użytkownika i hasło są przesyłane do usługi Azure AD STS w żądaniu HTTPS POST.
7. Usługa Azure AD STS pobiera klucze publiczne dla wszystkich agentów uwierzytelniania zarejestrowanych w dzierżawie z bazy danych SQL platformy Azure i szyfruje hasło przy użyciu ich użycia.
    - Tworzy "N" zaszyfrowane wartości haseł dla agentów uwierzytelniania "N" zarejestrowanych w dzierżawie.
8. Usługa Azure AD STS umieszcza żądanie sprawdzania poprawności hasła, które składa się z nazwy użytkownika i zaszyfrowanych wartości haseł, w kolejce usługi Service Bus specyficzne dla dzierżawy.
9. Ponieważ zainicjowani agenci uwierzytelniania są trwale połączeni z kolejką usługi Service Bus, jeden z dostępnych agentów uwierzytelniania pobiera żądanie sprawdzania poprawności hasła.
10. Agent uwierzytelniania lokalizuje zaszyfrowaną wartość hasła, która jest specyficzna dla jego klucza publicznego, przy użyciu identyfikatora i odszyfrowuje go przy użyciu klucza prywatnego.
11. Agent uwierzytelniania próbuje zweryfikować nazwę użytkownika i hasło względem lokalnej usługi Active Directory przy użyciu [interfejsu API logowania użytkownika systemu Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) z parametrem **dwLogonType** ustawionym na **LOGON32_LOGON_NETWORK**. 
    - Ten interfejs API jest tym samym interfejsem API, który jest używany przez usługi federacyjne Active Directory (AD FS) do logowania użytkowników w scenariuszu logowania federacyjnego.
    - Ten interfejs API opiera się na procesie standardowej rozdzielczości w systemie Windows Server, aby zlokalizować kontroler domeny.
12. Agent uwierzytelniania odbiera wynik z usługi Active Directory, taki jak powodzenie, nieprawidłowa nazwa użytkownika lub hasło lub hasło wygasło.

   > [!NOTE]
   > Jeśli agent uwierzytelniania nie powiedzie się podczas procesu logowania, całe żądanie logowania jest odrzucane. Nie ma przekazywania żądań logowania od jednego agenta uwierzytelniania do innego agenta uwierzytelniania lokalnie. Agenci ci komunikują się tylko z chmurą, a nie ze sobą.
   
13. Agent uwierzytelniania przekazuje wynik z powrotem do usługi Azure AD STS za pośrednictwem wychodzącego wzajemnie uwierzytelnionego kanału HTTPS za pośrednictwem portu 443. Uwierzytelnianie wzajemne używa certyfikatu wystawionego wcześniej agentowi uwierzytelniającemu podczas rejestracji.
14. Usługa Azure AD STS sprawdza, czy ten wynik jest skorelowany z określonym żądaniem logowania w dzierżawie.
15. Usługa Azure AD STS kontynuuje procedurę logowania zgodnie z konfiguracją. Na przykład jeśli sprawdzanie poprawności hasła zakończyło się pomyślnie, użytkownik może zostać zakwestionowany w przypadku uwierzytelniania wieloskładnikowego lub przekierowany z powrotem do aplikacji.

## <a name="operational-security-of-the-authentication-agents"></a>Bezpieczeństwo operacyjne agentów uwierzytelniania

Aby upewnić się, że uwierzytelnianie przekazywane pozostaje bezpieczne operacyjnie, usługa Azure AD okresowo odnawia certyfikaty agentów uwierzytelniania. Usługa Azure AD wyzwala odnawiania. Odnowienia nie są regulowane przez samych agentów uwierzytelniania.

![Bezpieczeństwo działania](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Aby odnowić zaufanie agenta uwierzytelniania za pomocą usługi Azure AD:

1. Agent uwierzytelniania okresowo pinguje usługę Azure AD co kilka godzin, aby sprawdzić, czy nadszedł czas, aby odnowić swój certyfikat. Certyfikat jest odnawiany na 30 dni przed jego wygaśnięciem.
    - Ta kontrola jest wykonywana za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS i używa tego samego certyfikatu, który został wystawiony podczas rejestracji.
2. Jeśli usługa wskazuje, że nadszedł czas, aby odnowić, Agent uwierzytelniania generuje nową parę kluczy: klucz publiczny i klucz prywatny.
    - Klucze te są generowane za pomocą standardowego szyfrowania RSA 2048-bit.
    - Klucz prywatny nigdy nie opuszcza serwera lokalnego.
3. Agent uwierzytelniania następnie sprawia, że żądanie "odnowienie certyfikatu" do usługi Azure AD za pośrednictwem protokołu HTTPS, z następujących składników zawartych w żądaniu:
    - Istniejący certyfikat pobrany z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE w magazynie certyfikatów systemu Windows. W tej procedurze nie ma administratora globalnego, więc nie jest potrzebny token dostępu w imieniu administratora globalnego.
    - Klucz publiczny wygenerowany w kroku 2.
    - Żądanie podpisania certyfikatu (CSR lub żądanie certyfikatu). To żądanie dotyczy nowego certyfikatu tożsamości cyfrowej z usługą Azure AD jako urzędem certyfikacji.
4. Usługa Azure AD sprawdza poprawność istniejącego certyfikatu w żądaniu odnowienia certyfikatu. Następnie sprawdza, czy żądanie pochodzi od agenta uwierzytelniania zarejestrowanego w dzierżawie.
5. Jeśli istniejący certyfikat jest nadal ważny, usługa Azure AD podpisuje nowy certyfikat tożsamości cyfrowej i wystawia nowy certyfikat agentowi uwierzytelniania. 
6. Jeśli istniejący certyfikat wygasł, usługa Azure AD usuwa agenta uwierzytelniania z listy zarejestrowanych agentów uwierzytelniania dzierżawy. Następnie administrator globalny musi ręcznie zainstalować i zarejestrować nowego agenta uwierzytelniania.
    - Użyj głównego urzędu certyfikacji usługi Azure AD, aby podpisać certyfikat.
    - Ustaw temat certyfikatu (Nazwa wyróżniająca lub DN) na identyfikator dzierżawy, identyfikator GUID, który jednoznacznie identyfikuje dzierżawę. Nazwa gnn zakresy certyfikatu tylko do dzierżawy.
6. Usługa Azure AD przechowuje nowy klucz publiczny agenta uwierzytelniania w bazie danych SQL platformy Azure, do których ma dostęp tylko on. Unieważnia również stary klucz publiczny skojarzony z agentem uwierzytelniania.
7. Nowy certyfikat (wystawiony w kroku 5) jest następnie przechowywany na serwerze w magazynie certyfikatów systemu Windows (w szczególności w [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) lokalizacji).
    - Ponieważ procedura odnawiania zaufania odbywa się nieinterakcyjnie (bez obecności administratora globalnego), agent uwierzytelniania nie ma już dostępu do aktualizowania istniejącego certyfikatu w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Ta procedura nie powoduje usunięcia samego certyfikatu z lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nowy certyfikat jest używany do uwierzytelniania od tego momentu. Każde kolejne odnowienie certyfikatu zastępuje certyfikat w lokalizacji CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatyczna aktualizacja agentów uwierzytelniania

Aplikacja Updater automatycznie aktualizuje agenta uwierzytelniania po wydaniu nowej wersji (z poprawkami błędów lub ulepszeniami wydajności). Aplikacja Updater nie obsługuje żadnych żądań sprawdzania poprawności hasła dla dzierżawy.

Usługa Azure AD obsługuje nową wersję oprogramowania jako podpisany **pakiet Instalatora Windows (MSI).** MSI jest podpisany przy użyciu [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) z SHA256 jako algorytm skrótu. 

![Automatyczna aktualizacja](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Aby automatycznie zaktualizować agenta uwierzytelniania:

1. Aplikacja Updater pinguje usługę Azure AD co godzinę, aby sprawdzić, czy dostępna jest nowa wersja agenta uwierzytelniania. 
    - Ta kontrola jest wykonywana za pośrednictwem wzajemnie uwierzytelnionego kanału HTTPS przy użyciu tego samego certyfikatu, który został wystawiony podczas rejestracji. Agent uwierzytelniania i updater współużytkować certyfikat przechowywany na serwerze.
2. Jeśli dostępna jest nowa wersja, usługa Azure AD zwraca podpisaną usługę MSI z powrotem do updatera.
3. Updater sprawdza, czy MSI jest podpisany przez firmę Microsoft.
4. Updater uruchamia MSI. Ta akcja obejmuje następujące kroki:

   > [!NOTE]
   > Program updater działa z uprawnieniami [systemu lokalnego.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

    - Zatrzymuje usługę Agent uwierzytelniania
    - Instaluje nową wersję Agenta uwierzytelniania na serwerze
    - Ponowne uruchamianie usługi Agent uwierzytelniania

>[!NOTE]
>Jeśli masz wielu agentów uwierzytelniania zarejestrowanych w dzierżawie, usługa Azure AD nie odnawia ich certyfikatów ani nie aktualizuje ich w tym samym czasie. Zamiast tego usługa Azure AD robi to po kolei, aby zapewnić wysoką dostępność żądań logowania.
>


## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, które scenariusze są obsługiwane, a które nie.
- [Szybki start:](how-to-connect-pta-quick-start.md)Rozpocznij uruchamianie uwierzytelniania przekazywania usługi Azure AD.
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoptadpdownload) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada:](../authentication/howto-password-smart-lockout.md)Skonfiguruj funkcję smart lockout w dzierżawie, aby chronić konta użytkowników.
- [Jak to działa:](how-to-connect-pta-how-it-works.md)Poznaj podstawy działania uwierzytelniania przekazywania usługi Azure AD.
- [Często zadawane pytania](how-to-connect-pta-faq.md): znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD:](how-to-connect-sso.md)Dowiedz się więcej o tej uzupełniającej funkcji.
