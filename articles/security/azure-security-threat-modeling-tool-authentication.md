---
title: Uwierzytelnianie — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 56620dc1d3e315caa3e259715ed84a539b91356d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610878"
---
# <a name="security-frame-authentication--mitigations"></a>Ramka zabezpieczeń: Uwierzytelnianie | Środki zaradcze 

| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web**    | <ul><li>[Należy wziąć pod uwagę przy użyciu mechanizmu uwierzytelniania standardowego uwierzytelniania do aplikacji sieci Web](#standard-authn-web-app)</li><li>[Aplikacje musi bezpiecznie obsługiwać scenariusze uwierzytelniania nie powiodło się](#handle-failed-authn)</li><li>[Włącz krok w górę lub adaptacyjne uwierzytelniania](#step-up-adaptive-authn)</li><li>[Upewnij się, że interfejsów administracyjnych są odpowiednio zablokowana](#admin-interface-lockdown)</li><li>[Implementowanie bezpiecznego nie pamiętam hasła funkcje](#forgot-pword-fxn)</li><li>[Upewnij się, że są implementowane zasady haseł i kont](#pword-account-policy)</li><li>[Wdrożenie kontroli w celu zapobiegania wyliczenia nazwy użytkownika](#controls-username-enum)</li></ul> |
| **Baza danych** | <ul><li>[Jeśli to możliwe, należy użyć uwierzytelniania Windows do łączenia z programem SQL Server](#win-authn-sql)</li><li>[Jeśli to możliwe na użytek uwierzytelniania usługi Azure Active Directory łączenie z bazą danych SQL](#aad-authn-sql)</li><li>[Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że konta i hasła zasady są wymuszane w programie SQL server](#authn-account-pword)</li><li>[Nie należy używać uwierzytelniania SQL w zawartych baz danych](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Użyj na poświadczenia uwierzytelniania urządzeń przy użyciu tokenów sygnatur dostępu współdzielonego](#authn-sas-tokens)</li></ul> |
| **Granicy zaufania platformy Azure** | <ul><li>[Włącz uwierzytelnianie wieloskładnikowe systemu Azure dla administratorów systemu Azure](#multi-factor-azure-admin)</li></ul> |
| **Granicy zaufania usługi Service Fabric** | <ul><li>[Ogranicz dostęp anonimowy do klastra usługi Service Fabric](#anon-access-cluster)</li><li>[Upewnij się, że certyfikat klienta do węzła usługi Service Fabric innego niż certyfikat węzła do węzła](#fabric-cn-nn)</li><li>[Użyj usługi AAD do uwierzytelniania klientów, aby klastry usługi Service fabric](#aad-client-fabric)</li><li>[Upewnij się, że certyfikaty usługi service fabric są uzyskiwane z zatwierdzonych urząd certyfikacji (CA)](#fabric-cert-ca)</li></ul> |
| **Tożsamość serwera** | <ul><li>[Użyj uwierzytelniania standardowego scenariusze obsługiwane przez serwer tożsamości](#standard-authn-id)</li><li>[Zastąp domyślną tożsamości serwera pamięci podręcznej tokenu z alternatywnymi skalowalne](#override-token)</li></ul> |
| **Maszyny granicy zaufania** | <ul><li>[Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Włączanie uwierzytelniania podczas nawiązywania połączenia z kolejki usługi MSMQ w programie WCF](#msmq-queues)</li><li>[Czy usługi WCF nie ustawiono komunikat właściwości ClientCredentialType o wartości none](#message-none)</li><li>[Czy usługi WCF nie ustawiono transportu właściwości ClientCredentialType o wartości none](#transport-none)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, uwierzytelniania standardowych technik są używane do zabezpieczania interfejsów API sieci Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Użyj uwierzytelniania standardowego scenariusze obsługiwane przez usługę Azure Active Directory](#authn-aad)</li><li>[Zastąp domyślną pamięci podręcznej tokenu ADAL z alternatywnymi skalowalne](#adal-scalable)</li><li>[Upewnij się, że TokenReplayCache używany w celu zapobiegania powtórzeń tokenów uwierzytelniania ADAL](#tokenreplaycache-adal)</li><li>[Użyj biblioteki ADAL do zarządzania od klientów protokołu OAuth2 żądania tokenu usługi AAD (lub lokalnej usługi AD)](#adal-oauth2)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Uwierzytelnianie urządzeń nawiązujących połączenie z bramy w terenie](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Upewnij się, czy urządzeń nawiązujących połączenie z bramy w chmurze są uwierzytelniane](#authn-devices-cloud)</li><li>[Użyj poświadczeń uwierzytelniania poszczególnych urządzeń.](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że tylko wymagane kontenerów i obiektów blob są podane anonimowy dostęp do odczytu](#req-containers-anon)</li><li>[Przyznawanie ograniczonego dostępu do obiektów w usłudze Azure storage przy użyciu sygnatury dostępu Współdzielonego lub firmy SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Należy wziąć pod uwagę przy użyciu mechanizmu uwierzytelniania standardowego uwierzytelniania do aplikacji sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Uwierzytelnianie jest proces jednostki, w przypadku gdy okaże się swoją tożsamość, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło. Brak dostępnych wiele protokołów uwierzytelniania, które mogą być uważane za. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na bazie systemu Windows</li><li>Oparte na formularzach</li><li>Federacyjna - usług AD FS</li><li>Federacyjna — usługa Azure AD</li><li>Federacyjna - tożsamości serwera</li></ul><p>Należy wziąć pod uwagę przy użyciu mechanizmu uwierzytelniania standardowego do identyfikowania procesu źródła</p>|

## <a id="handle-failed-authn"></a>Aplikacje musi bezpiecznie obsługiwać scenariusze uwierzytelniania nie powiodło się

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Aplikacje, które jawnie uwierzytelniania użytkowników muszą obsługiwać bezpiecznie scenariuszy uwierzytelniania nie powiodło się. Mechanizm uwierzytelniania, musi:</p><ul><li>Odmowa dostępu do uprzywilejowanych zasobów, gdy uwierzytelnianie nie powiodło się.</li><li>Wyświetlany ogólny komunikat o błędzie uwierzytelniania nie powiodło się po ponownym uruchomieniu i następuje odmowa dostępu</li></ul><p>Testowanie:</p><ul><li>Ochrona zasobów uprzywilejowanych po nieudanych prób logowania</li><li>Ogólny komunikat o błędzie jest wyświetlany na uwierzytelnianie nie powiodło się i zdarzenia odmowa dostępu</li><li>Konta są wyłączone po nadmiernej liczby nieudanych prób</li><ul>|

## <a id="step-up-adaptive-authn"></a>Włącz krok w górę lub adaptacyjne uwierzytelniania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Sprawdź, aplikacja ma dodatkowe autoryzacji (takich jak przejścia lub adaptacyjne uwierzytelniania za pośrednictwem usługi Multi-Factor authentication, takie jak wysyłanie wiadomości SMS, e-mail itp. lub monitować o ponowne uwierzytelnianie OTP), dzięki czemu użytkownik jest kwestionowana, zanim zostanie im przyznany dostęp do poufne informacje. Ta reguła ma zastosowanie także do zmiany krytyczne z konta lub akcji</p><p>Oznacza to również, że dostosowania uwierzytelniania ma być implementowane w taki sposób, że aplikacji poprawnie wymusza kontekstową autoryzacji umożliwiającej nie nieautoryzowanych manipulacji przez przykład modyfikowaniu parametru</p>|

## <a id="admin-interface-lockdown"></a>Upewnij się, że interfejsów administracyjnych są odpowiednio zablokowana

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | Jest to pierwsze rozwiązanie do udzielania dostępu tylko z określonego zakresu źródłowego adresu IP do interfejsu administracyjnego. Jeśli rozwiązanie nie jest możliwe, nie zawsze jest zalecany do wymuszania podniesienie lub adaptacyjne uwierzytelniania do logowania do interfejsu administracyjnego |

## <a id="forgot-pword-fxn"></a>Implementowanie bezpiecznego nie pamiętam hasła funkcje

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Pierwszym krokiem jest sprawdzenie, czy nie pamiętasz hasła i inne ścieżki odzyskiwania wysłać łącze w tym token aktywacji ograniczonej czasowo, a nie samego hasła. Dodatkowe uwierzytelnianie oparte na tokeny nietrwałego (np. wiadomości SMS tokenu, natywne aplikacje mobilne, itp.) może być wymagane również przed wysłaniem za pośrednictwem łącza. Po drugie użytkownik nie powinien być blokowany się konta użytkowników przy jednoczesnym proces pobierania nowego hasła jest w toku.</p><p>Może to prowadzić do typu "odmowa usługi" zawsze wtedy, gdy osoba atakująca decyduje się na celowo zablokowania użytkowników za pomocą automatycznych ataku. Po trzecie zawsze wtedy, gdy nowe żądanie hasła została ustawiona w toku, powinien być uogólniony komunikat, który można wyświetlić, aby zapobiec wyliczenia nazwy użytkownika. Czwarty zawsze nie zezwalaj na użycie starego hasła i wdrożyć zasady silne hasło.</p> |

## <a id="pword-account-policy"></a>Upewnij się, że są implementowane zasady haseł i kont

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Powinny zostać wdrożone zasady haseł i kont zgodne z zasadami organizacyjnymi i najlepsze rozwiązania.</p><p>Do obrony przed siłowych i zgadywania słownik na podstawie: Aby upewnić się, że użytkownicy tworzyć złożone hasło (np. 12 znaków długości minimalnej, znakami alfanumerycznymi i specjalnymi) należy zaimplementować zasady silnych haseł.</p><p>Zasady blokady konta może być implementowana w następujący sposób:</p><ul><li>**Elastyczne blokady:** Może to być to dobra opcja w celu ochrony użytkowników przed atakami siłowymi. Na przykład zawsze wtedy, gdy użytkownik wprowadzi nieprawidłowe hasło trzykrotnie aplikacji może zablokować konta w ciągu minuty w celu spowolnić proces słownikowym wymuszania jego hasła, dzięki czemu mniej korzystna dla osoby atakującej kontynuować. Gdyby zaimplementować twardych przeciwdziałanie blokady w tym przykładzie wyniesie "DOS" przez trwale blokowania kont. Alternatywnie aplikacji może Wygeneruj hasło Jednorazowe (jednego hasła czasu) i wysłać go poza pasmem (za pośrednictwem poczty e-mail, sms itp.) dla użytkownika. Innym rozwiązaniem może być do zaimplementowania CAPTCHA, po osiągnięciu wartości progowej liczby nieudanych prób.</li><li>**Twarde blokady:** Ten typ blokady powinny być stosowane zawsze, gdy wykrywanie atakiem aplikację i licznik go za pomocą trwale blokowaniem ustawił konto tak, aż zespół reagowania mam czasu na ich analiza śledcza czy użytkownik. Po zakończeniu tego procesu można zdecydować, aby przyznać użytkownikowi tworzenie kopii swojego konta lub wykonywanie dodatkowych czynności prawne względem niego. Takie podejście uniemożliwia osobie atakującej dalsze przechodzące przez aplikację i infrastruktury.</li></ul><p>Obrony przed atakami na domyślne i przewidywalne konta, sprawdź wszystkich kluczy i haseł wymienne, a są generowane lub zmiana po czas instalacji.</p><p>Jeśli aplikacja ma opcję automatycznego generowania haseł, upewnij się, że wygenerowanego hasła są losowe i masz o wysokiej entropii.</p>|

## <a id="controls-username-enum"></a>Wdrożenie kontroli w celu zapobiegania wyliczenia nazwy użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wszystkie komunikaty o błędach powinien być uogólniony, aby zapobiec wyliczenia nazwy użytkownika. Również czasami nie można uniknąć informacji przecieki w funkcje, takie jak strona rejestracji. W tym miejscu należy użyć metod ograniczania szybkości, takich jak CAPTCHA, aby uniemożliwić automatyczne ataku przez osobę atakującą. |

## <a id="win-authn-sql"></a>Jeśli to możliwe, należy użyć uwierzytelniania Windows do łączenia z programem SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | OnPrem |
| **Atrybuty**              | Wersja SQL — wszystkie |
| **Odwołania**              | [SQL Server — wybierz tryb uwierzytelniania](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroki** | Uwierzytelnianie Windows protokole zabezpieczeń Kerberos, zapewnia wymuszanie zasad haseł w odniesieniu do weryfikacji złożoności silnych haseł, zapewnia obsługę blokady konta i obsługuje wygasanie haseł.|

## <a id="aad-authn-sql"></a>Jeśli to możliwe na użytek uwierzytelniania usługi Azure Active Directory łączenie z bazą danych SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL - V12 |
| **Odwołania**              | [Łączenie z bazą danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroki** | **Minimalna wersja:** Usługa Azure SQL Database V12 wymagany w celu umożliwienia usłudze Azure SQL Database do użycia uwierzytelnianie usługi AAD w usłudze Microsoft Directory |

## <a id="authn-account-pword"></a>Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że konta i hasła zasady są wymuszane w programie SQL server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zasady haseł programu SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroki** | Korzystając z uwierzytelniania programu SQL Server, logowania są tworzone w programie SQL Server, które nie są oparte na kontach użytkowników Windows. Nazwa użytkownika i hasło są tworzone przy użyciu programu SQL Server i przechowywane w programie SQL Server. Program SQL Server można użyć mechanizmów zasad haseł Windows. Możliwość stosowania tych samych złożoności i zasadami wygasania używane w Windows to haseł używanych w programie SQL Server. |

## <a id="autn-contained-db"></a>Nie należy używać uwierzytelniania SQL w zawartych baz danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Lokalnego, usługi SQL Azure |
| **Atrybuty**              | Bazy danych SQL — MSSQL2012, wersja SQL — w wersji V12 |
| **Odwołania**              | [Najlepsze rozwiązania dotyczące zabezpieczeń przy użyciu zawartych baz danych](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroki** | Brak zasad haseł wymuszone może zwiększyć prawdopodobieństwo słabe poświadczenie, jest określana w zawartej bazie danych. Korzystać z uwierzytelniania Windows. |

## <a id="authn-sas-tokens"></a>Użyj na poświadczenia uwierzytelniania urządzeń przy użyciu tokenów sygnatur dostępu współdzielonego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | <p>Model zabezpieczeń usługi Event Hubs jest oparty na kombinacji tokeny sygnatury dostępu współdzielonego (SAS) i wydawców zdarzeń. Nazwa wydawcy reprezentuje identyfikator urządzenia, która odbiera token. Umożliwi to skojarzenie tokenów wygenerowanych przy użyciu odpowiednich urządzeń.</p><p>Wszystkie komunikaty są oznaczane za pomocą inicjatora po stronie usługi, umożliwiając wykrywanie pochodzenia — jest ładunku fałszowania prób. Podczas uwierzytelniania urządzenia, należy wygenerować token sygnatury dostępu współdzielonego urządzenia o określonym zakresie przez wydawcę unikatowy na.</p>|

## <a id="multi-factor-azure-admin"></a>Włącz uwierzytelnianie wieloskładnikowe systemu Azure dla administratorów systemu Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania platformy Azure | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Co to jest usługa Multi-Factor Authentication platformy Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroki** | <p>Uwierzytelnianie wieloskładnikowe (MFA) to metoda uwierzytelniania, która wymaga więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. To działa, wymagając dowolne dwa lub więcej z poniższych metod weryfikacji:</p><ul><li>Coś, co wiesz (zwykle hasła)</li><li>Coś, co masz (zaufane urządzenie, którego nie można łatwo zduplikować, takie jak telefon)</li><li>Coś, co Ciebie cechuje (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Ogranicz dostęp anonimowy do klastra usługi Service Fabric

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowisko — platformy Azure  |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra usługi Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroki** | <p>Klastry należy zawsze zabezpieczać uniemożliwiające nieupoważnionym użytkownikom łączenie z klastrem, szczególnie w przypadku, gdy ma ona obciążeń produkcyjnych na nim.</p><p>Podczas tworzenia klastra usługi Service fabric, upewnij się, że tryb zabezpieczeń jest ustawiona na "wartość secure" i skonfiguruj wymagany certyfikat X.509. Tworzenie klastra "niezabezpieczone" umożliwi każdy użytkownik anonimowy, połączyć się z nim Jeśli udostępnia punktów końcowych zarządzania do publicznej sieci Internet.</p>|

## <a id="fabric-cn-nn"></a>Upewnij się, że certyfikat klienta do węzła usługi Service Fabric innego niż certyfikat węzła do węzła

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowiska — platformy Azure, środowisko — autonomiczny |
| **Odwołania**              | [Zabezpieczenia usługi Service Fabric węzeł klienta certyfikatu](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [nawiązywanie połączenia z zabezpieczonym klastrem przy użyciu certyfikatu klienta](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroki** | <p>Certyfikat klienta węzeł zabezpieczeń jest skonfigurowana podczas tworzenia klastra za pośrednictwem witryny Azure portal, szablonów usługi Resource Manager lub autonomiczny szablonu JSON, określając certyfikat klienta administracyjnego i/lub certyfikatu klienta użytkownika.</p><p>Administrator klientów i użytkowników przez Ciebie certyfikaty klienta można określić powinny różnić się od głównego i dodatkowego certyfikatu, które określisz dla zabezpieczeń między węzłami.</p>|

## <a id="aad-client-fabric"></a>Użyj usługi AAD do uwierzytelniania klientów, aby klastry usługi Service fabric

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowisko — platformy Azure |
| **Odwołania**              | [Scenariusze zabezpieczeń — zalecenia dotyczące zabezpieczeń klastra](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroki** | Klastry na platformie Azure można również bezpieczny dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (AAD), aby oprócz certyfikatów klienta. W przypadku klastrów platformy Azure zaleca się używać zabezpieczeń usługi AAD do uwierzytelniania klientów i certyfikaty dla zabezpieczeń między węzłami.|

## <a id="fabric-cert-ca"></a>Upewnij się, że certyfikaty usługi service fabric są uzyskiwane z zatwierdzonych urząd certyfikacji (CA)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowisko — platformy Azure |
| **Odwołania**              | [Certyfikaty X.509 i Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroki** | <p>Usługa Service Fabric używa certyfikatów serwera X.509 w celu uwierzytelniania węzłów i klientów.</p><p>Niektóre ważne kwestie należy wziąć pod uwagę podczas korzystania z certyfikatów w sieci szkieletowej usługi:</p><ul><li>Certyfikaty używane w klastrach obciążeń produkcyjnych należy utworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskane z zatwierdzonych urząd certyfikacji (CA). Urząd certyfikacji może być zatwierdzone zewnętrznego urzędu certyfikacji lub właściwie zarządzane wewnętrznej infrastruktury kluczy publicznych (PKI)</li><li>Nie wolno używać żadnych tymczasowych lub certyfikaty testów w środowisku produkcyjnym, które są tworzone za pomocą narzędzi, takich jak MakeCert.exe</li><li>Można użyć certyfikatu z podpisem własnym, ale tylko to zrobić w przypadku klastrów testowych, a nie w środowisku produkcyjnym</li></ul>|

## <a id="standard-authn-id"></a>Użyj uwierzytelniania standardowego scenariusze obsługiwane przez serwer tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IdentityServer3 - szerszej perspektywie](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroki** | <p>Poniżej przedstawiono typowe interakcji, obsługiwane przez serwer tożsamości:</p><ul><li>Przeglądarki komunikować się z aplikacjami sieci web</li><li>Aplikacje sieci Web komunikują się za pomocą interfejsów API sieci web (czasami samodzielnie, czasami w imieniu użytkownika)</li><li>Aplikacje oparte na przeglądarce komunikować się za pomocą interfejsów API sieci web</li><li>Aplikacje natywne komunikują się za pomocą interfejsów API sieci web</li><li>Aplikacje oparte na serwerze komunikować się za pomocą interfejsów API sieci web</li><li>Interfejsy API sieci Web komunikują się za pomocą interfejsów API sieci web (czasami samodzielnie, czasami w imieniu użytkownika)</li></ul>|

## <a id="override-token"></a>Zastąp domyślną tożsamości serwera pamięci podręcznej tokenu z alternatywnymi skalowalne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wdrażanie serwera tożsamości — pamięć podręczna](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | <p>IdentityServer ma proste wbudowanej w pamięci podręcznej. Gdy jest to dobra w przypadku aplikacji natywnych na małą skalę, go trudna w skalowaniu mid aplikacji warstwy i wewnętrznej bazy danych z następujących powodów:</p><ul><li>Te aplikacje są dostępne dla wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie tworzy problemy izolacji i przedstawia informacje o wyzwania działających na dużą skalę: wielu użytkowników, z których każdy z dowolną liczbę tokenów jako zasoby aplikacja uzyskuje dostęp w ich imieniu, może oznaczać ogromną i operacji bardzo kosztowna wyszukiwania</li><li>Te aplikacje są zazwyczaj wdrożone w topologii rozproszonej, w której wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny pamięci podręcznej musi być odporny, odtwarzanie procesów i deactivations</li><li>Wszystkie z powyższych powodów podczas wdrażania aplikacji sieci web, zalecane jest aby zastąpić domyślny serwer tożsamości pamięć podręczną tokenu jest alternatywą skalowalne, takich jak pamięć podręczna systemu Azure dla usługi Redis</li></ul>|

## <a id="binaries-signed"></a>Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo, tak, aby można zweryfikować integralności plików binarnych|

## <a id="msmq-queues"></a>Włączanie uwierzytelniania podczas nawiązywania połączenia z kolejki usługi MSMQ w programie WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroki** | Program nie powiedzie się włączyć uwierzytelnianie, łącząc się z kolejki usługi MSMQ, osoba atakująca może anonimowo przesyłania komunikatów w kolejce do przetworzenia. Jeśli nie jest używane uwierzytelnianie, aby nawiązać połączenie z kolejki usługi MSMQ, używany do dostarczenia komunikatu do innego programu, osoba atakująca może przesłać anonimowe komunikat o błędzie jest złośliwe.|

### <a name="example"></a>Przykład
`<netMsmqBinding/>` Element pliku konfiguracji usługi WCF poniżej powoduje, że usługi WCF, aby wyłączyć uwierzytelnianie, łącząc się z kolejki usługi MSMQ do dostarczania wiadomości.
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
Konfigurowanie usługi MSMQ, aby wymagać domeny Windows lub uwierzytelniania certyfikatu przez cały czas jakiekolwiek komunikaty przychodzące lub wychodzące.

### <a name="example"></a>Przykład
`<netMsmqBinding/>` Element pliku konfiguracji usługi WCF poniżej powoduje, że usługi WCF, aby włączyć uwierzytelnianie certyfikatu, łącząc się z kolejki usługi MSMQ. Klient jest uwierzytelniany przy użyciu certyfikatów X.509. Certyfikat klienta musi być obecny w magazynie certyfikatów serwera.
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

## <a id="message-none"></a>Czy usługi WCF nie ustawiono komunikat właściwości ClientCredentialType o wartości none

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta - brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy jest w stanie uzyskać dostępu do tej usługi. To usługa, która nie uwierzytelnia klientów umożliwia dostęp do wszystkich użytkowników. Konfigurowanie aplikacji do uwierzytelniania względem poświadczeń klienta. Można to zrobić, ustawiając clientCredentialType komunikat Windows lub certyfikat. |

### <a name="example"></a>Przykład
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>Czy usługi WCF nie ustawiono transportu właściwości ClientCredentialType o wartości none

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólne, .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta - brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy jest w stanie uzyskać dostępu do tej usługi. To usługa, która nie uwierzytelnia klientów umożliwia wszystkim użytkownikom na dostęp do jego funkcji. Konfigurowanie aplikacji do uwierzytelniania względem poświadczeń klienta. Można to zrobić, ustawiając clientCredentialType transportu Windows lub certyfikat. |

### <a name="example"></a>Przykład
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Upewnij się, uwierzytelniania standardowych technik są używane do zabezpieczania interfejsów API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w składniku ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [zewnętrznych usług uwierzytelniania przy użyciu interfejsu API sieci Web platformy ASP.NET (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroki** | <p>Uwierzytelnianie jest proces jednostki, w przypadku gdy okaże się swoją tożsamość, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło. Brak dostępnych wiele protokołów uwierzytelniania, które mogą być uważane za. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na bazie systemu Windows</li><li>Oparte na formularzach</li><li>Federacyjna - usług AD FS</li><li>Federacyjna — usługa Azure AD</li><li>Federacyjna - tożsamości serwera</li></ul><p>Linków w sekcji odwołań zawierają szczegóły niskiego poziomu dotyczące jak wszystkich schematów uwierzytelniania zaimplementowane do zabezpieczania internetowego interfejsu API.</p>|

## <a id="authn-aad"></a>Użyj uwierzytelniania standardowego scenariusze obsługiwane przez usługę Azure Active Directory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Scenariusze uwierzytelniania dla usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [przykłady kodu platformy Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [przewodnik dewelopera usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroki** | <p>Azure Active Directory (Azure AD) ułatwia uwierzytelnianie dla deweloperów, zapewniając tożsamość jako usługa, dzięki obsłudze protokoły będące standardami branżowymi, takie jak OAuth 2.0 i OpenID Connect. Poniżej przedstawiono pięciu scenariuszy głównej aplikacji obsługiwanych przez usługę Azure AD:</p><ul><li>Przeglądarka sieci Web do aplikacji sieci Web: Użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD</li><li>Aplikacja jednostronicowa (SPA): Użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD</li><li>Aplikacji natywnej do internetowego interfejsu API: Natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z internetowego interfejsu API, która jest zabezpieczony przez usługę Azure AD</li><li>Aplikacja sieci Web do internetowego interfejsu API: Aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD</li><li>Demon lub serwera aplikacji do internetowego interfejsu API: Aplikacji demona albo aplikacja serwera bez interfejsu użytkownika sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD</li></ul><p>Zapoznaj się z linkami w sekcji odwołań dla szczegółów implementacji niskiego poziomu</p>|

## <a id="adal-scalable"></a>Zastąp domyślną pamięci podręcznej tokenu ADAL z alternatywnymi skalowalne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie w usłudze Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [przy użyciu usługi Redis jako pamięci podręcznej tokenu ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroki** | <p>Pamięć podręczna domyślnej, która używa biblioteki ADAL (Active Directory Authentication Library) jest w pamięci podręcznej, która opiera się na statyczne magazynu, dostępne całego procesu. Gdy działa to w przypadku aplikacji natywnych, jego trudna w skalowaniu mid aplikacji warstwy i wewnętrznej bazy danych z następujących powodów:</p><ul><li>Te aplikacje są dostępne dla wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie tworzy problemy izolacji i przedstawia informacje o wyzwania działających na dużą skalę: wielu użytkowników, z których każdy z dowolną liczbę tokenów jako zasoby aplikacja uzyskuje dostęp w ich imieniu, może oznaczać ogromną i operacji bardzo kosztowna wyszukiwania</li><li>Te aplikacje są zazwyczaj wdrożone w topologii rozproszonej, w której wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny pamięci podręcznej musi być odporny, odtwarzanie procesów i deactivations</li></ul><p>Wszystkie z powyższych powodów podczas wdrażania aplikacji sieci web, zalecane jest aby zastąpić domyślny pamięci podręcznej tokenu ADAL za pomocą skalowalnej alternatywne, takie jak pamięć podręczna systemu Azure dla usługi Redis.</p>|

## <a id="tokenreplaycache-adal"></a>Upewnij się, że TokenReplayCache używany w celu zapobiegania powtórzeń tokenów uwierzytelniania ADAL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie w usłudze Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroki** | <p>Właściwość TokenReplayCache umożliwia deweloperom Definiowanie pamięci podręcznej powtórzeń tokenów, magazynu, który może służyć do zapisywania tokenów w celu sprawdzenia, że token nie mogą być używane w więcej niż jeden raz.</p><p>Jest to miara względem atak typowych ataków aptly o nazwie powtarzania tokenu: osoba atakująca przechwytuje token wysyłane podczas logowania może próbować ponownie wysłać do aplikacji ("" odtworzyć je ponownie) do utworzenia nowej sesji. Np. przepływ przyznawania kodu w OIDC, po uwierzytelnieniu użytkownik pomyślnie, żądanie "/ signin-oidc" punktu końcowego uzależnionej składa się z "id_token", "code" i "stan" parametrów.</p><p>Uzależnionej weryfikuje żądanie i ustanawia nowej sesji. Jeśli osoba atakująca przechwytuje tego żądania i jego odtwarzaniem, ma uprawnienia ustanowić sesję pomyślne i podszywały się pod użytkownika. Obecność identyfikatora jednorazowego w OpenID Connect można ograniczyć, ale nie są w pełni wyeliminuj okoliczności, w których ataku mogą zostać pomyślnie wprowadzone. Aby chronić swoje aplikacje, deweloperzy mogą zapewniać implementację ITokenReplayCache i przypisz wystąpienie do TokenReplayCache.</p>|

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
Poniżej przedstawiono przykład implementacji interfejsu ITokenReplayCache. (Można dostosować i zaimplementować preferowanej struktury buforowania specyficzne dla projektu)
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
Zaimplementowano pamięci podręcznej musi odwoływać się do opcji OIDC za pośrednictwem właściwości "TokenValidationParameters" w następujący sposób.
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

Należy pamiętać, że testowanie skuteczności tę konfigurację, zaloguj się do lokalnych aplikacji chronionych OIDC i przechwytywać żądania `"/signin-oidc"` punktu końcowego w narzędziu fiddler. Gdy ochrona nie jest w miejscu, odtworzenie tego żądania w narzędziu fiddler ustawi nowego pliku cookie sesji. Gdy żądanie jest odtwarzany po dodaniu ochrony TokenReplayCache, aplikacji spowoduje zgłoszenie wyjątku w następujący sposób: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Użyj biblioteki ADAL do zarządzania od klientów protokołu OAuth2 żądania tokenu usługi AAD (lub lokalnej usługi AD)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroki** | <p>Usługi Azure AD authentication Library (ADAL) umożliwia deweloperom aplikacji klienta łatwe uwierzytelnianie użytkowników w chmurze lub środowiska lokalnego usługi Active Directory (AD), a następnie uzyskiwanie tokenów dostępu do zabezpieczania wywołań interfejsu API.</p><p>Biblioteka ADAL ma wiele funkcji uwierzytelniania upewnij łatwiejsze dla deweloperów, takie jak obsługa asynchronicznego, można skonfigurować tokenu pamięci podręcznej, które przechowuje tokeny dostępu i tokenów odświeżania, automatyczne odświeżanie tokenu po wygaśnięciu token dostępu i token odświeżania jest dostępny, i więcej.</p><p>Dzięki obsłudze najbardziej złożoność, ADAL można zawęzić dewelopera na logice biznesowej w aplikacji i łatwe zabezpieczanie zasobów bez konieczności ekspertem w zakresie zabezpieczeń. Oddzielne biblioteki są dostępne dla platformy .NET, JavaScript (klient i Node.js), iOS, Android i Java.</p>|

## <a id="authn-devices-field"></a>Uwierzytelnianie urządzeń nawiązujących połączenie z bramy w terenie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że każde urządzenie jest uwierzytelniany przez bramy w terenie, przed zaakceptowaniem z nich danych, a także przed ułatwienia nadrzędnego komunikacji z bramy w chmurze. Upewnij się również, że urządzenia są połączone z każdego urządzenia poświadczeń, aby jednoznacznie zidentyfikować poszczególnych urządzeń.|

## <a id="authn-devices-cloud"></a>Upewnij się, czy urządzeń nawiązujących połączenie z bramy w chmurze są uwierzytelniane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólne, C#, Node.JS,  |
| **Atrybuty**              | N/d, wybór bramy — usługi Azure IoT Hub |
| **Odwołania**              | N/d, [Azure IoT hub przy użyciu platformy .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [wprowadzenie do usługi IoT hub i Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [zabezpieczanie IoT przy użyciu sygnatury dostępu Współdzielonego i certyfikaty](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repozytorium Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroki** | <ul><li>**Ogólne:** Uwierzytelnianie urządzeń przy użyciu zabezpieczeń TLS (Transport Layer) lub protokołu IPSec. Infrastruktura powinien obsługiwać za pomocą klucz wstępny (PSK) na tych urządzeniach, które nie obsługują pełne asymetrycznego kryptografii. Korzystać z usługi Azure AD i Oauth.</li><li>**C#:** Podczas tworzenia wystąpienia DeviceClient, domyślnie, Metoda Create tworzy instancję DeviceClient, która używa protokołu AMQP do komunikowania się z usługą IoT Hub. Do używania protokołu HTTPS, użyj zastępowania metody Create, która pozwala na określenie protokołu. Jeśli używasz protokołu HTTPS, należy również dodać `Microsoft.AspNet.WebApi.Client` pakiet NuGet do projektu, aby uwzględnić `System.Net.Http.Formatting` przestrzeni nazw.</li></ul>|

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
* Tworzenie Centrum IoT na platformie azure
* Utwórz wpis w rejestrze tożsamości urządzeń
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
  #### <a name="sas-token"></a>Token SAS
* Pobiera wewnętrznie generowane, gdy przy użyciu klucza symetrycznego, ale możemy wygenerować i używać go także jawnie
* Zdefiniuj protokół: `var Http = require('azure-iot-device-http').Http;`
* Utwórz token sygnatury dostępu współdzielonego:
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
* Połącz przy użyciu tokenu sygnatury dostępu współdzielonego: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certyfikaty
* Wygenerować własny X509 podpisanego certyfikatu, przy użyciu dowolnego narzędzia, takie jak biblioteki OpenSSL do generowania .cert i Key plików do przechowywania certyfikatu i klucza odpowiednio
* Aprowizacja urządzenia, który akceptuje połączenia zabezpieczonego przy użyciu certyfikatów.
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
* Podłącz urządzenie przy użyciu certyfikatu
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

## <a id="authn-cred"></a>Użyj poświadczeń uwierzytelniania poszczególnych urządzeń.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway  | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługi Azure IoT Hub |
| **Odwołania**              | [Tokeny zabezpieczeń usługi Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroki** | Użyj na poświadczenia uwierzytelniania urządzeń przy użyciu tokenów sygnatur dostępu współdzielonego oparte na klucz urządzenia lub certyfikat klienta, zamiast poziomie Centrum IoT współużytkowane zasady dostępu. Przez inny to zapobiega ponownemu użyciu tokeny uwierzytelniania jednego pola lub urządzenia bramy |

## <a id="req-containers-anon"></a>Upewnij się, że tylko wymagane kontenerów i obiektów blob są podane anonimowy dostęp do odczytu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Zarządzanie dostępem anonimowym odczytu do kontenerów i obiektów blob](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sygnatur dostępu współdzielonego, część 1: Opis modelu sygnatur dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroki** | <p>Domyślnie kontener i wszystkie obiekty BLOB w niej można uzyskać dostęp tylko właściciel konta magazynu. Aby dać użytkownikom anonimowym uprawnienia odczytu do kontenera i jego obiektów blob, jednego zestawu uprawnień kontenera, aby zezwolić na publiczny dostęp. Użytkownicy anonimowi mogą czytać obiektów blob w kontenerze publicznie dostępny bez uwierzytelniania żądania.</p><p>Kontenery zapewniają następujące opcje w celu zarządzania dostępem kontenera:</p><ul><li>Pełny publiczny dostęp do odczytu: Dane kontenera i obiektów blob mogą być odczytywane za pośrednictwem żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze za pomocą żądania od użytkowników anonimowych, ale nie można wyliczyć kontenerów na koncie magazynu.</li><li>Publiczny dostęp do odczytu tylko dla obiektów blob: Danych obiektów blob w ramach tego kontenera, można je odczytać za pomocą żądania od użytkowników anonimowych, ale kontenera dane są niedostępne. Klienci nie można wyliczyć obiektów blob w kontenerze za pomocą żądań anonimowych</li><li>Brak publicznego dostępu do odczytu: Dane kontenera i obiektów blob mogą być odczytywane tylko właściciel konta</li></ul><p>Dostęp anonimowy jest najlepsze dla scenariuszy, w której niektóre obiekty BLOB zawsze powinna być dostępna dla anonimowy dostęp do odczytu. Dla bardziej szczegółowej kontroli można utworzyć sygnaturę dostępu współdzielonego, co umożliwia dostęp do delegata z ograniczeniami przy użyciu różnych uprawnień i w określonym przedziale czasu. Upewnij się, że kontenerów i obiektów blob, które potencjalnie mogą zawierać poufne dane, nie otrzymują dostęp anonimowy przypadkowo</p>|

## <a id="limited-access-sas"></a>Przyznawanie ograniczonego dostępu do obiektów w usłudze Azure storage przy użyciu sygnatury dostępu Współdzielonego lub firmy SAP

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND |
| **Odwołania**              | [Udostępnione sygnatur dostępu, część 1: Opis modelu sygnatur dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [sygnatur dostępu współdzielonego, część 2: Tworzenie i używanie sygnatury dostępu Współdzielonego z usługą Blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [jak delegować dostęp do obiektów na koncie przy użyciu sygnatury dostępu współdzielonego i przechowywanych zasad dostępu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroki** | <p>Przy użyciu sygnatury dostępu współdzielonego (SAS) jest wydajnym sposobem przyznawanie ograniczonego dostępu do obiektów na koncie magazynu w innym klientom bez konieczności udostępnienia klucz dostępu konta. Sygnatura dostępu Współdzielonego jest identyfikator URI, który obejmuje w jego parametry zapytania wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient musi tylko przekazanej sygnatury dostępu Współdzielonego do odpowiedniego konstruktora lub metody.</p><p>Sygnatury dostępu Współdzielonego można użyć zapewnić dostęp do zasobów w ramach konta magazynu do klienta, który nie jest zaufany klucz konta. Klucze konta magazynu obejmują zarówno klucz podstawowy i pomocniczy, z których oba udzielić dostępu administracyjnego do Twojego konta i wszystkie zasoby w niej. Udostępnianie jednego z kluczy konta zostanie otwarty na możliwość użycia złośliwych lub wynikającego z niedbałości na koncie. Sygnatury dostępu współdzielonego zapewniają bezpieczne alternatywę umożliwiająca innych klientów do odczytu, zapisu i usuwania danych w ramach konta magazynu zgodnie z uprawnieniami, które zostały przyznane i bez konieczności klucz konta.</p><p>Jeśli masz logiczny zbiór parametrów, które są podobne za każdym razem, przy użyciu przechowywanych zasad dostępu (SAP) jest lepiej zrozumieć. Ponieważ przy użyciu sygnatury dostępu Współdzielonego pochodną zasady dostępu przechowywane daje możliwość natychmiast odwołać tej sygnatury dostępu Współdzielonego, jest zalecanym najlepszym rozwiązaniem jest zawsze używaj przechowywanych zasad dostępu, gdy jest to możliwe.</p>|
