---
title: Uwierzytelnianie — narzędzie microsoft do modelowania zagrożeń — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244629"
---
# <a name="security-frame-authentication--mitigations"></a>Ramka zabezpieczeń: Uwierzytelnianie | Czynniki 

| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja internetowa**    | <ul><li>[Rozważ użycie standardowego mechanizmu uwierzytelniania do uwierzytelniania w aplikacji sieci Web](#standard-authn-web-app)</li><li>[Aplikacje muszą bezpiecznie obsługiwać scenariusze uwierzytelniania, które nie powiodły się](#handle-failed-authn)</li><li>[Włączanie uwierzytelniania krokowego lub adaptacyjnego](#step-up-adaptive-authn)</li><li>[Upewnij się, że interfejsy administracyjne są odpowiednio zablokowane](#admin-interface-lockdown)</li><li>[Bezpieczne wdrażanie funkcji zapomnianych haseł](#forgot-pword-fxn)</li><li>[Upewnij się, że hasło i zasady konta są implementowane](#pword-account-policy)</li><li>[Implementowanie formantów zapobiegające wyliczaniu nazwy użytkownika](#controls-username-enum)</li></ul> |
| **baza danych** | <ul><li>[Jeśli to możliwe, użyj uwierzytelniania systemu Windows do łączenia się z programem SQL Server](#win-authn-sql)</li><li>[Jeśli to możliwe, użyj uwierzytelniania usługi Azure Active Directory do łączenia się z bazą danych SQL](#aad-authn-sql)</li><li>[Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że zasady konta i hasła są wymuszane na serwerze SQL](#authn-account-pword)</li><li>[Nie używaj uwierzytelniania SQL w zamkniętych bazach danych](#autn-contained-db)</li></ul> |
| **Centrum zdarzeń Azure** | <ul><li>[Używanie poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS](#authn-sas-tokens)</li></ul> |
| **Granica zaufania platformy Azure** | <ul><li>[Włączanie uwierzytelniania wieloskładnikowego platformy Azure dla administratorów platformy Azure](#multi-factor-azure-admin)</li></ul> |
| **Granica zaufania sieci usług** | <ul><li>[Ograniczanie dostępu anonimowego do klastra sieci szkieletowej usług](#anon-access-cluster)</li><li>[Upewnij się, że certyfikat klient-węzeł sieci szkieletowej usług różni się od certyfikatu węzeł-węzeł](#fabric-cn-nn)</li><li>[Uwierzytelnianie klientów do obsługi klastrów sieci szkieletowej za pomocą usługi AAD](#aad-client-fabric)</li><li>[Upewnij się, że certyfikaty sieci szkieletowej usług są uzyskiwane z zatwierdzonego urzędu certyfikacji](#fabric-cert-ca)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Używanie standardowych scenariuszy uwierzytelniania obsługiwanych przez serwer tożsamości](#standard-authn-id)</li><li>[Zastępowanie domyślnej pamięci podręcznej tokenów serwera tożsamości skalowalnymi alternatywą](#override-token)</li></ul> |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Włączanie uwierzytelniania podczas łączenia się z kolejkami usługi MSMQ w wiktymnie](#msmq-queues)</li><li>[WCF-Nie ustawiaj message clientCredentialType na brak](#message-none)</li><li>[WCF-Nie ustawiaj elementu klienta transportuCredentialType na brak](#transport-none)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że standardowe techniki uwierzytelniania są używane do zabezpieczania interfejsów API sieci Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Używanie standardowych scenariuszy uwierzytelniania obsługiwanych przez usługę Azure Active Directory](#authn-aad)</li><li>[Zastępowanie domyślnej pamięci podręcznej tokenów ADAL skalowalnym alternatywą](#adal-scalable)</li><li>[Upewnij się, że tokenReplayCache jest używany, aby zapobiec powtórce tokenów uwierzytelniania ADAL](#tokenreplaycache-adal)</li><li>[Biblioteki ADAL za pomocą bibliotek ADAL do zarządzania żądaniami tokenów od klientów OAuth2 do usługi AAD (lub lokalnej usługi AD)](#adal-oauth2)</li></ul> |
| **Brama pola IoT** | <ul><li>[Uwierzytelnianie urządzeń łączących się z bramą polowej](#authn-devices-field)</li></ul> |
| **Brama w chmurze IoT** | <ul><li>[Upewnij się, że urządzenia łączące się z bramą w chmurze są uwierzytelnione](#authn-devices-cloud)</li><li>[Używanie poświadczeń uwierzytelniania na urządzenie](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że tylko wymagane kontenery i obiekty blob mają anonimowy dostęp do odczytu](#req-containers-anon)</li><li>[Udzielanie ograniczonego dostępu do obiektów w magazynie platformy Azure przy użyciu usługi SAS lub SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Rozważ użycie standardowego mechanizmu uwierzytelniania do uwierzytelniania w aplikacji sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Uwierzytelnianie to proces, w którym jednostka potwierdza swoją tożsamość, zazwyczaj za pomocą poświadczeń, takich jak nazwa użytkownika i hasło. Dostępnych jest wiele protokołów uwierzytelniania, które mogą być brane pod uwagę. Niektóre z nich są wymienione poniżej:</p><ul><li>Certyfikaty klientów</li><li>Oparte na systemie Windows</li><li>Oparte na formularzach</li><li>Federacja — ADFS</li><li>Federacja — usługa Azure AD</li><li>Federacja — serwer tożsamości</li></ul><p>Rozważ użycie standardowego mechanizmu uwierzytelniania w celu zidentyfikowania procesu źródłowego</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Aplikacje muszą bezpiecznie obsługiwać scenariusze uwierzytelniania, które nie powiodły się

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Aplikacje jawnie uwierzytelniają użytkowników, które muszą bezpiecznie obsługiwać scenariusze uwierzytelniania, które nie powiodły się. Mechanizm uwierzytelniania musi:</p><ul><li>Odmowa dostępu do zasobów uprzywilejowanych, gdy uwierzytelnianie nie powiedzie się</li><li>Wyświetlanie ogólnego komunikatu o błędzie po wystąpieniu nieudanego uwierzytelniania i odmowy dostępu</li></ul><p>Badanie na:</p><ul><li>Ochrona uprzywilejowanych zasobów po nieudanych logowaniach</li><li>Ogólny komunikat o błędzie jest wyświetlany w przypadku zdarzeń uwierzytelniania nie powiodło się i odmowy dostępu</li><li>Konta są wyłączone po nadmiernej liczbie nieudanych prób</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Włączanie uwierzytelniania krokowego lub adaptacyjnego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Sprawdź, czy aplikacja ma dodatkową autoryzację (np. zwiększenie lub uwierzytelnianie adaptacyjne, za pomocą uwierzytelniania wieloskładnikowego, takiego jak wysyłanie OTP w wiadomościach SMS, e-mailach itp. lub monitowanie o ponowne uwierzytelnienie), aby użytkownik został zakwestionowany przed udzieleniem dostępu do informacji szczególnie chronionych. Zasada ta ma również zastosowanie do wprowadzania krytycznych zmian na koncie lub</p><p>Oznacza to również, że dostosowanie uwierzytelniania musi być zaimplementowane w taki sposób, aby aplikacja poprawnie wymuszała autoryzację kontekstową, aby nie zezwalać na nieautoryzowaną manipulację za pomocą np.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Upewnij się, że interfejsy administracyjne są odpowiednio zablokowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | Pierwszym rozwiązaniem jest udzielenie dostępu tylko z określonego źródłowego zakresu ip do interfejsu administracyjnego. Jeśli to rozwiązanie nie byłoby możliwe, niż zawsze zaleca się wymuszanie intencyjnego lub adaptacyjnego uwierzytelniania w celu zalogowania się do interfejsu administracyjnego |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Bezpieczne wdrażanie funkcji zapomnianych haseł

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Pierwszą rzeczą jest sprawdzenie, czy zapomniane hasło i inne ścieżki odzyskiwania wysyłają link zawierający ograniczony czas token aktywacji, a nie samo hasło. Przed wysłaniem łącza może być wymagane dodatkowe uwierzytelnianie oparte na tokenach programowych (np. tokenu SMS, natywnych aplikacjach mobilnych itp.). Po drugie, nie należy blokować konta użytkowników, podczas gdy proces uzyskiwania nowego hasła jest w toku.</p><p>Może to prowadzić do ataku typu "odmowa usługi", gdy osoba atakująca zdecyduje się celowo zablokować użytkowników za pomocą automatycznego ataku. Po trzecie, za każdym razem, gdy nowe żądanie hasła zostało ustawione w toku, wyświetlany komunikat powinien być uogólniony, aby zapobiec wyliczaniu nazwy użytkownika. Po czwarte, zawsze nie zezwalaj na używanie starych haseł i implementuje silną politykę haseł.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Upewnij się, że hasło i zasady konta są implementowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| Szczegóły | <p>Należy zaimplementować zasady haseł i kont zgodnie z zasadami organizacji i najlepszymi praktykami.</p><p>Aby bronić się przed zgadywaniem opartym na brutalnej sile i słowniku: należy wdrożyć zasady silnego hasła, aby zapewnić użytkownikom tworzenie złożonych haseł (np. 12 znaków o minimalnej długości, znaków alfanumerycznej i specjalnych).</p><p>Zasady blokady konta mogą być implementowane w następujący sposób:</p><ul><li>**Miękka blokada:** Może to być dobrym rozwiązaniem do ochrony użytkowników przed atakami siłowymi. Na przykład za każdym razem, gdy użytkownik wprowadzi nieprawidłowe hasło trzy razy aplikacja może zablokować konto na minutę, aby spowolnić proces brutalnego wymuszania hasła, co czyni go mniej opłacalnym dla osoby atakującej. Jeśli w tym przykładzie zostaną wprowadzone środki zaradcze dotyczące blokady twardej, można by uzyskać "DoS", definitywnie blokując konta. Alternatywnie, aplikacja może wygenerować OTP (One Time Password) i wysłać go poza pasmem (za pośrednictwem poczty e-mail, sms itp.) do użytkownika. Innym podejściem może być wdrożenie CAPTCHA po osiągnięciu progu liczby nieudanych prób.</li><li>**Blokada twarda:** Ten typ blokady powinny być stosowane za każdym razem, gdy wykryje użytkownika atakującego aplikację i przeciwdziałać im za pomocą trwałego blokowania ich konta, dopóki zespół odpowiedzi nie miał czasu, aby zrobić ich kryminalistyki. Po tym procesie możesz zdecydować się na oddanie użytkownikowi konta lub podjąć dalsze kroki prawne przeciwko nim. Ten typ podejścia uniemożliwia osobie atakującej dalsze penetracji aplikacji i infrastruktury.</li></ul><p>Aby bronić się przed atakami na konta domyślne i przewidywalne, sprawdź, czy wszystkie klucze i hasła są wymienne i są generowane lub zastępowane po czasie instalacji.</p><p>Jeśli aplikacja ma do automatycznego generowania haseł, upewnij się, że wygenerowane hasła są losowe i mają wysoką entropię.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementowanie formantów zapobiegające wyliczaniu nazwy użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wszystkie komunikaty o błędach powinny być uogólnione, aby zapobiec wyliczaniu nazwy użytkownika. Czasami nie można również uniknąć wycieku informacji w funkcjach, takich jak strona rejestracji. W tym miejscu należy użyć metod ograniczających szybkość, takich jak CAPTCHA, aby zapobiec automatycznemu atakowi przez osobę atakującą. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Jeśli to możliwe, użyj uwierzytelniania systemu Windows do łączenia się z programem SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | OnPrem ( OnPrem ) |
| **Atrybuty**              | Wersja SQL - Wszystkie |
| **Odwołania**              | [SQL Server — wybieranie trybu uwierzytelniania](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroki** | Uwierzytelnianie systemu Windows używa protokołu zabezpieczeń Kerberos, zapewnia wymuszanie zasad haseł w odniesieniu do sprawdzania poprawności złożoności dla silnych haseł, zapewnia obsługę blokady konta i obsługuje wygasanie hasła.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Jeśli to możliwe, użyj uwierzytelniania usługi Azure Active Directory do łączenia się z bazą danych SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL - V12 |
| **Odwołania**              | [Łączenie się z bazą danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroki** | **Wersja minimalna:** Usługa Azure SQL Database V12 wymagana do zezwalania bazie danych SQL azure na używanie uwierzytelniania AAD w katalogu Microsoft Directory |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że zasady konta i hasła są wymuszane na serwerze SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zasady haseł programu SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroki** | Podczas korzystania z uwierzytelniania programu SQL Server logowania są tworzone w programie SQL Server, które nie są oparte na kontach użytkowników systemu Windows. Zarówno nazwa użytkownika, jak i hasło są tworzone przy użyciu programu SQL Server i przechowywane w programie SQL Server. Program SQL Server może używać mechanizmów zasad haseł systemu Windows. Można zastosować te same zasady złożoności i wygasania używane w systemie Windows do haseł używanych w programie SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Nie używaj uwierzytelniania SQL w zamkniętych bazach danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | OnPrem, SQL Azure |
| **Atrybuty**              | Wersja SQL - MSSQL2012, wersja SQL - V12 |
| **Odwołania**              | [Najważniejsze wskazówki dotyczące zabezpieczeń dotyczące zawartych w nich baz danych](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroki** | Brak wymuszanych zasad haseł może zwiększyć prawdopodobieństwo ustanowienia słabych poświadczeń w zawartej bazie danych. Wykorzystaj uwierzytelnianie systemu Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Używanie poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie uwierzytelniania i modelu zabezpieczeń centrum zdarzeń](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | <p>Model zabezpieczeń Centrum zdarzeń jest oparty na kombinacji tokenów sygnatury dostępu współdzielonego (SAS) i wydawców zdarzeń. Nazwa wydawcy reprezentuje DeviceID, który odbiera token. Pomogłoby to skojarzyć tokeny wygenerowane z odpowiednimi urządzeniami.</p><p>Wszystkie wiadomości są oznaczone inicjatorem po stronie usługi, co pozwala na wykrycie prób fałszowania pochodzenia ładunku. Podczas uwierzytelniania urządzeń wygeneruj token SaS na urządzenie o zakresie do unikatowego wydawcy.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Włączanie uwierzytelniania wieloskładnikowego platformy Azure dla administratorów platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania platformy Azure | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Co to jest usługa Multi-Factor Authentication platformy Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroki** | <p>Uwierzytelnianie wieloskładnikowe (MFA) jest metodą uwierzytelniania, która wymaga więcej niż jednej metody weryfikacji i dodaje krytyczną drugą warstwę zabezpieczeń do logowania i transakcji użytkowników. Działa poprzez wymaganie dwóch lub więcej z następujących metod weryfikacji:</p><ul><li>Coś, co znasz (zazwyczaj hasło)</li><li>Coś, co masz (zaufane urządzenie, które nie jest łatwo powielane, jak telefon)</li><li>Coś, czym jesteś (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Ograniczanie dostępu anonimowego do klastra sieci szkieletowej usług

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Platforma Azure  |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroki** | <p>Klastry powinny być zawsze zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się z klastrem, zwłaszcza gdy ma uruchomione obciążenia produkcyjne.</p><p>Podczas tworzenia klastra sieci szkieletowej usług upewnij się, że tryb zabezpieczeń jest ustawiony na "bezpieczny" i skonfiguruj wymagany certyfikat serwera X.509. Utworzenie klastra "niezabezpieczonego" umożliwi każdemu anonimowemu użytkownikowi nawiązanie z nim połączenia, jeśli udostępnia punkty końcowe zarządzania do publicznego Internetu.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Upewnij się, że certyfikat klient-węzeł sieci szkieletowej usług różni się od certyfikatu węzeł-węzeł

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure, Środowisko — autonomiczne |
| **Odwołania**              | [Zabezpieczenia certyfikatu klient-węzeł sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Łączenie się z bezpiecznym klastrem przy użyciu certyfikatu klienta](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroki** | <p>Zabezpieczenia certyfikatu klient-węzeł są konfigurowane podczas tworzenia klastra za pośrednictwem portalu Azure, szablonów usługi Resource Manager lub autonomicznego szablonu JSON przez określenie certyfikatu klienta administratora i/lub certyfikatu klienta użytkownika.</p><p>Określone certyfikaty klienta administratora i klienta użytkownika powinny być inne niż certyfikaty podstawowe i pomocnicze określone dla zabezpieczeń węzeł-węzeł.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Uwierzytelnianie klientów do obsługi klastrów sieci szkieletowej za pomocą usługi AAD

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Platforma Azure |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra — zalecenia dotyczące zabezpieczeń](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroki** | Klastry uruchomione na platformie Azure mogą również zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (AAD), oprócz certyfikatów klientów. W przypadku klastrów platformy Azure zaleca się używanie zabezpieczeń usługi AAD do uwierzytelniania klientów i certyfikatów dla zabezpieczeń między węzłami.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Upewnij się, że certyfikaty sieci szkieletowej usług są uzyskiwane z zatwierdzonego urzędu certyfikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Platforma Azure |
| **Odwołania**              | [Certyfikaty X.509 i sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroki** | <p>Usługa Service Fabric używa certyfikatów serwera X.509 do uwierzytelniania węzłów i klientów.</p><p>Kilka ważnych rzeczy, które należy wziąć pod uwagę podczas korzystania z certyfikatów w sieci szkieletowej usług:</p><ul><li>Certyfikaty używane w klastrach z uruchomionymi obciążeniami produkcyjnymi powinny być tworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskiwane z zatwierdzonego urzędu certyfikacji.Certificates used in clusters running production workloads should be created by using a correctly configured Windows Server certificate service or obtained from an approved Certificate Authority (CA). Urząd certyfikacji może być zatwierdzonym zewnętrznym urzędem certyfikacji lub prawidłowo zarządzaną wewnętrzną infrastrukturą kluczy publicznych (PKI)</li><li>Nigdy nie używaj żadnych certyfikatów tymczasowych lub testowych w produkcji, które są tworzone za pomocą narzędzi, takich jak MakeCert.exe</li><li>Można użyć certyfikatu z podpisem własnym, ale należy to zrobić tylko dla klastrów testowych, a nie w produkcji</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Używanie standardowych scenariuszy uwierzytelniania obsługiwanych przez serwer tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [IdentityServer3 - Wielki obraz](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroki** | <p>Poniżej znajdują się typowe interakcje obsługiwane przez serwer tożsamości:</p><ul><li>Przeglądarki komunikują się z aplikacjami internetowymi</li><li>Aplikacje sieci Web komunikują się z internetowymi interfejsami API (czasami samodzielnie, czasami w imieniu użytkownika)</li><li>Aplikacje oparte na przeglądarce komunikują się z internetowymi interfejsami API</li><li>Aplikacje natywne komunikują się z internetowymi interfejsami API</li><li>Aplikacje oparte na serwerze komunikują się z internetowymi interfejsami API</li><li>Internetowe interfejsy API komunikują się z internetowymi interfejsami API (czasami samodzielnie, czasami w imieniu użytkownika)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Zastępowanie domyślnej pamięci podręcznej tokenów serwera tożsamości skalowalnymi alternatywą

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wdrażanie serwera tożsamości — buforowanie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | <p>IdentityServer posiada prostą wbudowaną pamięć podręczną w pamięci. Chociaż jest to dobre dla aplikacji natywnych na małą skalę, nie skaluje się dla aplikacji warstwy środkowej i wewnętrznej z następujących powodów:</p><ul><li>Dostęp do tych aplikacji jest dostępnych dla wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie stwarza problemy z izolacją i stanowi wyzwanie podczas pracy na dużą skalę: wielu użytkowników, z których każdy ma tyle tokenów, ile zasobów, do których aplikacja uzyskuje dostęp w ich imieniu, może oznaczać ogromne liczby i bardzo kosztowne operacje wyszukiwania</li><li>Te aplikacje są zazwyczaj wdrażane w rozproszonych topologiach, gdzie wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny buforowane muszą przetrwać recyklingi i dezaktywacji procesów</li><li>Ze wszystkich powyższych powodów podczas implementowania aplikacji sieci web zaleca się zastąpienie domyślnej pamięci podręcznej tokenów serwera tożsamości skalowalną alternatywą, taką jak Usługa Azure Cache for Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo, aby można było zweryfikować integralność plików binarnych|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Włączanie uwierzytelniania podczas łączenia się z kolejkami usługi MSMQ w wiktymnie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroki** | Program nie włącza uwierzytelniania podczas łączenia się z kolejkami usługi MSMQ, osoba atakująca może anonimowo przesyłać wiadomości do kolejki w celu przetworzenia. Jeśli uwierzytelnianie nie jest używane do łączenia się z kolejką usługi MSMQ używaną do dostarczania wiadomości do innego programu, osoba atakująca może przesłać anonimową wiadomość, która jest złośliwa.|

### <a name="example"></a>Przykład
Element `<netMsmqBinding/>` pliku konfiguracyjnego WCF poniżej nakazuje WCF wyłączyć uwierzytelnianie podczas łączenia się z kolejką usługi MSMQ do dostarczania wiadomości.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Skonfiguruj program MSMQ tak, aby wymagał uwierzytelniania domeny lub certyfikatu systemu Windows przez cały czas dla wszystkich wiadomości przychodzących lub wychodzących.

### <a name="example"></a>Przykład
Element `<netMsmqBinding/>` pliku konfiguracyjnego WCF poniżej nakazuje WCF włączyć uwierzytelnianie certyfikatów podczas łączenia się z kolejką usługi MSMQ. Klient jest uwierzytelniony przy użyciu certyfikatów X.509. Certyfikat klienta musi znajdować się w magazynie certyfikatów serwera.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Nie ustawiaj message clientCredentialType na brak

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta — brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroki** | Brak uwierzytelniania oznacza, że każdy ma dostęp do tej usługi. Usługa, która nie uwierzytelnia swoich klientów, umożliwia dostęp wszystkim użytkownikom. Skonfiguruj aplikację do uwierzytelniania za poświadczenia klienta. Można to zrobić, ustawiając komunikat clientCredentialType na Windows lub Certificate. |

### <a name="example"></a>Przykład
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Nie ustawiaj elementu klienta transportuCredentialType na brak

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta — brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroki** | Brak uwierzytelniania oznacza, że każdy ma dostęp do tej usługi. Usługa, która nie uwierzytelnia swoich klientów, umożliwia wszystkim użytkownikom dostęp do jej funkcji. Skonfiguruj aplikację do uwierzytelniania za poświadczenia klienta. Można to zrobić, ustawiając klienta transportuCredentialType do systemu Windows lub certyfikatu. |

### <a name="example"></a>Przykład
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Upewnij się, że standardowe techniki uwierzytelniania są używane do zabezpieczania interfejsów API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w ASP.NET interfejsie API sieci Web,](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) [zewnętrzne usługi uwierzytelniania z ASP.NET interfejsem API sieci Web (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroki** | <p>Uwierzytelnianie to proces, w którym jednostka potwierdza swoją tożsamość, zazwyczaj za pomocą poświadczeń, takich jak nazwa użytkownika i hasło. Dostępnych jest wiele protokołów uwierzytelniania, które mogą być brane pod uwagę. Niektóre z nich są wymienione poniżej:</p><ul><li>Certyfikaty klientów</li><li>Oparte na systemie Windows</li><li>Oparte na formularzach</li><li>Federacja — ADFS</li><li>Federacja — usługa Azure AD</li><li>Federacja — serwer tożsamości</li></ul><p>Łącza w sekcji odwołania zawierają szczegółowe informacje na temat sposobu zaimplementowania każdego ze schematów uwierzytelniania w celu zabezpieczenia interfejsu API sieci Web.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Używanie standardowych scenariuszy uwierzytelniania obsługiwanych przez usługę Azure Active Directory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Scenariusze uwierzytelniania dla usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [przykłady kodu usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [przewodnik dla dewelopera usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroki** | <p>Usługa Azure Active Directory (Azure AD) upraszcza uwierzytelnianie deweloperów, udostępniając tożsamość jako usługę, obsługując protokoły zgodne ze standardami branżowymi, takie jak OAuth 2.0 i OpenID Connect. Poniżej znajduje się pięć podstawowych scenariuszy aplikacji obsługiwanych przez usługę Azure AD:</p><ul><li>Przeglądarka sieci Web do aplikacji sieci Web: użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczona przez usługę Azure AD</li><li>Aplikacja jednostronicowa (SPA): Użytkownik musi zalogować się do aplikacji jednostronicowej zabezpieczonej usługą Azure AD</li><li>Natywna aplikacja do interfejsu API sieci Web: natywna aplikacja uruchamiana na telefonie, tablecie lub komputerze musi uwierzytelnić użytkownika, aby uzyskać zasoby z internetowego interfejsu API zabezpieczonego usługą Azure AD</li><li>Aplikacja sieci Web do interfejsu API sieci Web: aplikacja sieci Web musi uzyskać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD</li><li>Daemon lub Aplikacja serwera do interfejsu API sieci Web: aplikacja demona lub aplikacja serwera bez interfejsu użytkownika sieci Web musi uzyskać zasoby z internetowego interfejsu API zabezpieczonego usługą Azure AD</li></ul><p>Szczegółowe informacje na temat implementacji niskiego poziomu można znaleźć w linkach w sekcji odniesień</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Zastępowanie domyślnej pamięci podręcznej tokenów ADAL skalowalnym alternatywą

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie za pomocą usługi Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), przy użyciu pamięci [podręcznej tokenu Redis jako ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroki** | <p>Domyślna pamięć podręczna używana przez bibliotekę uwierzytelniania usługi ADAL (Biblioteka uwierzytelniania usługi Active Directory) jest pamięcią podręczną w pamięci, która opiera się na magazynie statycznym, dostępnym w całym procesie. Chociaż działa to dla aplikacji natywnych, nie skaluje się dla aplikacji warstwy środkowej i wewnętrznej z następujących powodów:</p><ul><li>Dostęp do tych aplikacji jest dostępnych dla wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie stwarza problemy z izolacją i stanowi wyzwanie podczas pracy na dużą skalę: wielu użytkowników, z których każdy ma tyle tokenów, ile zasobów, do których aplikacja uzyskuje dostęp w ich imieniu, może oznaczać ogromne liczby i bardzo kosztowne operacje wyszukiwania</li><li>Te aplikacje są zazwyczaj wdrażane w rozproszonych topologiach, gdzie wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny buforowane muszą przetrwać recyklingi i dezaktywacji procesów</li></ul><p>Ze wszystkich powyższych powodów podczas implementowania aplikacji sieci web zaleca się zastąpienie domyślnej pamięci podręcznej tokenu ADAL skalowalną alternatywą, taką jak Usługa Azure Cache for Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Upewnij się, że tokenReplayCache jest używany, aby zapobiec powtórce tokenów uwierzytelniania ADAL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie za pomocą usługi Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroki** | <p>Właściwość TokenReplayCache umożliwia deweloperom definiowanie pamięci podręcznej powtarzania tokenów, magazynu, który może być używany do zapisywania tokenów w celu sprawdzenia, czy żaden token nie może być używany więcej niż jeden raz.</p><p>Jest to środek przeciwko wspólnemu atakowi, trafnie nazywanego atakiem powtarzania tokenu: osoba atakująca przechwytująca token wysłany przy logowaniu może spróbować wysłać go ponownie do aplikacji ("powtórka" go) w celu ustanowienia nowej sesji. Na przykład w przepływie oidc kod-grant, po pomyślnym uwierzytelnieniu użytkownika, żądanie punktu końcowego "/signin-oidc" jednostki uzależnionej jest wykonane z parametrami "id_token", "code" i "state".</p><p>Jednostka uzależniająca sprawdza poprawność tego żądania i ustanawia nową sesję. Jeśli przeciwnik przechwytuje to żądanie i odtwarza je, może ustanowić udaną sesję i sfałszować użytkownika. Obecność nonce w OpenID Connect może ograniczyć, ale nie w pełni wyeliminować okoliczności, w których atak może być pomyślnie uchwalony. Aby chronić swoje aplikacje, deweloperzy mogą zapewnić implementację ITokenReplayCache i przypisać wystąpienie do TokenReplayCache.</p>|

### <a name="example"></a>Przykład
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Przykład
Oto przykładowa implementacja interfejsu ITokenReplayCache. (Dostosuj i zaimplementuj ramy buforowania specyficzne dla projektu)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Zaimplementowanej pamięci podręcznej musi być odwołuje się w opcjach OIDC za pośrednictwem "TokenValidationParameters" właściwość w następujący sposób.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Należy pamiętać, że aby przetestować skuteczność tej konfiguracji, zaloguj się do lokalnej aplikacji `"/signin-oidc"` chronionej oidc i przechwyć żądanie do punktu końcowego w skrzypek. Gdy ochrona nie jest w miejscu, odtwarzanie tego żądania w fiddler ustawi nowy plik cookie sesji. Gdy żądanie jest odtwarzane po tokenreplayCache ochrony jest dodawany, aplikacja zda wyjątek w następujący sposób:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Biblioteki ADAL za pomocą bibliotek ADAL do zarządzania żądaniami tokenów od klientów OAuth2 do usługi AAD (lub lokalnej usługi AD)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroki** | <p>Biblioteka uwierzytelniania usługi Azure AD (ADAL) umożliwia deweloperom aplikacji klienckich łatwe uwierzytelnianie użytkowników w chmurze lub lokalnej usłudze Active Directory (AD), a następnie uzyskiwanie tokenów dostępu do zabezpieczania wywołań interfejsu API.</p><p>ADAL ma wiele funkcji, które ułatwiają uwierzytelnianie dla deweloperów, takich jak obsługa asynchronii, konfigurowalna pamięć podręczna tokenów, która przechowuje tokeny dostępu i tokeny odświeżania, automatyczne odświeżanie tokenu po wygaśnięciu tokenu dostępu i token odświeżania jest dostępny, oraz Więcej.</p><p>Obsługując większość złożoności, ADAL może pomóc deweloperowi skupić się na logice biznesowej w swojej aplikacji i łatwo zabezpieczyć zasoby bez bycia ekspertem w dziedzinie zabezpieczeń. Oddzielne biblioteki są dostępne dla .NET, JavaScript (klient i Node.js), Python, iOS, Android i Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Uwierzytelnianie urządzeń łączących się z bramą polowej

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że każde urządzenie jest uwierzytelnione przez bramę field przed zaakceptowaniem danych z nich i przed ułatwieniem komunikacji nadrzędnej z bramą w chmurze. Upewnij się również, że urządzenia łączą się z poświadczeniem na urządzenie, aby poszczególne urządzenia mogły być jednoznacznie identyfikowane.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Upewnij się, że urządzenia łączące się z bramą w chmurze są uwierzytelnione

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, C#, Node.JS,  |
| **Atrybuty**              | Nie dotyczy, wybór bramy — Usługa Azure IoT Hub |
| **Odwołania**              | Nie dotyczy, [centrum Usługi Azure IoT z usługą .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), wprowadzenie do centrum [IoT hub i węzeł JS,](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted) [zabezpieczanie IoT przy sygnaturze dostępu Współdzielonego i certyfikatach,](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) [repozytorium Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroki** | <ul><li>**Rodzajowy:** Uwierzytelnij urządzenie przy użyciu zabezpieczeń warstwy transportowej (TLS) lub PROTOKOŁU IPSec. Infrastruktura powinna obsługiwać przy użyciu klucza wstępnego (PSK) na tych urządzeniach, które nie obsługują pełnej kryptografii asymetrycznej. Wykorzystaj usługę Azure AD, Oauth.</li><li>**C#:** Podczas tworzenia wystąpienia DeviceClient domyślnie Create Metoda tworzy DeviceClient wystąpienie, który używa protokołu AMQP do komunikowania się z Centrum IoT Hub. Aby użyć protokołu HTTPS, użyj zastępowania metody Utwórz, które pozwala na określenie protokołu. Jeśli używasz protokołu HTTPS, należy `Microsoft.AspNet.WebApi.Client` również dodać pakiet NuGet `System.Net.Http.Formatting` do projektu, aby uwzględnić obszar nazw.</li></ul>|

### <a name="example"></a>Przykład
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Przykład
**Node.JS: Uwierzytelnianie**
#### <a name="symmetric-key"></a>Klucz symetryczny
* Tworzenie centrum IoT na platformie Azure
* Tworzenie wpisu w rejestrze tożsamości urządzenia
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Tworzenie symulowanego urządzenia
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS Token
* Pobiera wewnętrznie generowane podczas korzystania z klucza symetrycznego, ale możemy wygenerować i używać go jawnie, jak również
* Definiowanie protokołu:`var Http = require('azure-iot-device-http').Http;`
* Utwórz token sas:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Połącz się za pomocą tokenu sas: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certyfikaty
* Generowanie samodzielnie podpisanego certyfikatu X509 przy użyciu dowolnego narzędzia, takiego jak OpenSSL, do generowania plików .cert i .key do przechowywania odpowiednio certyfikatu i klucza
* Aprowizuj urządzenie, które akceptuje zabezpieczone połączenie przy użyciu certyfikatów.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Podłączanie urządzenia przy użyciu certyfikatu
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Używanie poświadczeń uwierzytelniania na urządzenie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT  | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługa Azure IoT Hub |
| **Odwołania**              | [Tokeny zabezpieczające usługi Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroki** | Użyj poświadczeń uwierzytelniania na urządzeniu przy użyciu tokenów SaS opartych na kluczu urządzenia lub certyfikatie klienta zamiast zasad dostępu współużytkowanego na poziomie usługi IoT Hub. Zapobiega to ponownemu użyciu tokenów uwierzytelniania jednego urządzenia lub bramy polowej przez inną |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Upewnij się, że tylko wymagane kontenery i obiekty blob mają anonimowy dostęp do odczytu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ magazynu — obiekt blob |
| **Odwołania**              | [Zarządzanie anonimowym dostępem do odczytu kontenerów i obiektów blob](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sygnatury dostępu współdzielonego, część 1: Opis modelu sygnatury dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroki** | <p>Domyślnie kontener i wszystkie obiekty blob w nim mogą być dostępne tylko przez właściciela konta magazynu. Aby nadać anonimowym użytkownikom uprawnienia do odczytu kontenera i jego obiektów blob, można ustawić uprawnienia kontenera, aby umożliwić dostęp publiczny. Anonimowi użytkownicy mogą odczytywać obiekty blob w kontenerze publicznie dostępnym bez uwierzytelniania żądania.</p><p>Kontenery zapewniają następujące opcje zarządzania dostępem do kontenerów:</p><ul><li>Pełny publiczny dostęp do odczytu: dane kontenera i obiektu blob można odczytać za pośrednictwem anonimowego żądania. Klienci mogą wyliczać obiekty BLOB w kontenerze za pośrednictwem żądania anonimowego, ale nie można wyliczyć kontenerów w ramach konta magazynu.</li><li>Publiczny dostęp do odczytu tylko dla obiektów blob: dane obiektów blob w tym kontenerze można odczytać za pomocą żądania anonimowego, ale dane kontenera nie są dostępne. Klienci nie mogą wyliczać obiektów blob w kontenerze za pomocą żądania anonimowego</li><li>Brak publicznego dostępu do odczytu: dane kontenera i obiektu blob mogą być odczytywane tylko przez właściciela konta</li></ul><p>Dostęp anonimowy jest najlepszy w scenariuszach, w których niektóre obiekty blob powinny być zawsze dostępne dla anonimowego dostępu do odczytu. W przypadku kontroli drobnieziarnistej można utworzyć sygnaturę dostępu współdzielonego, która umożliwia delegowanie ograniczonego dostępu przy użyciu różnych uprawnień i w określonym przedziale czasu. Upewnij się, że kontenery i obiekty BLOB, które mogą potencjalnie zawierać poufne dane, nie mają przypadkowo dostępu anonimowego</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Udzielanie ograniczonego dostępu do obiektów w magazynie platformy Azure przy użyciu usługi SAS lub SAP

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [Sygnatury dostępu współdzielonego, część 1: Opis modelu dostępu współdzielonego,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [sygnatury dostępu współdzielonego, część 2: Tworzenie i używanie sygnatury dostępu współdzielonego z magazynem obiektów Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Jak delegować dostęp do obiektów na koncie przy użyciu sygnatur dostępu współdzielonego i zasad dostępu przechowywanego](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroki** | <p>Przy użyciu sygnatury dostępu współdzielonego (SAS) jest skutecznym sposobem przyznania ograniczonego dostępu do obiektów na koncie magazynu innym klientom, bez konieczności udostępniania klucza dostępu do konta. Sygnatura dostępu Współdzielonego jest identyfikatorem URI, który obejmuje w swoich parametrach zapytania wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu Współdzielonego, klient musi przekazać tylko w sygnaturze dostępu Współdzielonego do odpowiedniego konstruktora lub metody.</p><p>Sygnatury dostępu Współdzielonego można użyć, gdy chcesz zapewnić dostęp do zasobów na koncie magazynu klientowi, któremu nie można ufać przy użyciu klucza konta. Klucze konta magazynu zawierają klucz podstawowy i pomocniczy, z których oba zapewniają dostęp administracyjny do konta i wszystkich zasobów w nim. Ujawnienie któregokolwiek z kluczy konta otwiera twoje konto na możliwość złośliwego lub niedbałego użycia. Sygnatury dostępu współdzielonego zapewniają bezpieczną alternatywę, która umożliwia innym klientom odczytywanie, zapisywanie i usuwanie danych z konta magazynu zgodnie z przyznanymi uprawnieniami i bez konieczności posiadania klucza konta.</p><p>Jeśli masz logiczny zestaw parametrów, które są podobne za każdym razem, przy użyciu zasady dostępu przechowywane (SAP) jest lepszym pomysłem. Ponieważ przy użyciu sygnatury dostępu współdzielonego pochodzącego z zasad dostępu przechowywane daje możliwość natychmiastowego odwołania tego sygnatury dostępu współdzielonego, zaleca się najlepsze rozwiązanie, aby zawsze używać zasad dostępu przechowywanego, gdy jest to możliwe.</p>|
