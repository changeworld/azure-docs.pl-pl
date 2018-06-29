---
title: Uwierzytelnianie — narzędzie Microsoft Threat modelowania - Azure | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: d53ade1e5c31ca25636b95d4f8b9e0fe29f9d081
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031111"
---
# <a name="security-frame-authentication--mitigations"></a>Ramka zabezpieczenia: Uwierzytelnianie | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web**    | <ul><li>[Rozważ użycie mechanizmu uwierzytelniania standardowego uwierzytelniania do aplikacji sieci Web](#standard-authn-web-app)</li><li>[Aplikacje muszą obsługiwać bezpiecznie scenariusze uwierzytelniania nie powiodło się](#handle-failed-authn)</li><li>[Włącz zwiększenia lub adaptacyjną uwierzytelniania](#step-up-adaptive-authn)</li><li>[Upewnij się, że interfejsy administracyjne są odpowiednio zablokowana](#admin-interface-lockdown)</li><li>[Implementowanie bezpiecznie nie pamiętasz hasła funkcji](#forgot-pword-fxn)</li><li>[Upewnij się, że są wykonywane zasad haseł i kont](#pword-account-policy)</li><li>[Wdrożenie kontroli w celu zapobieganie wyliczanie nazwy użytkownika](#controls-username-enum)</li></ul> |
| **Baza danych** | <ul><li>[Jeśli to możliwe, należy używać uwierzytelniania systemu Windows do połączenia z programem SQL Server](#win-authn-sql)</li><li>[Jeśli to możliwe Użyj uwierzytelniania usługi Azure Active Directory dotyczących nawiązania połączenia z bazą danych SQL](#aad-authn-sql)</li><li>[Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że konto i hasło zasady są wymuszane na serwerze SQL](#authn-account-pword)</li><li>[Nie należy używać uwierzytelniania SQL w zawartych baz danych](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Użyj na tokeny sygnatury dostępu współdzielonego poświadczenia uwierzytelniania urządzenia](#authn-sas-tokens)</li></ul> |
| **Granic zaufania Azure** | <ul><li>[Włącz uwierzytelnianie wieloskładnikowe platformy Azure dla administratorów usługi Azure](#multi-factor-azure-admin)</li></ul> |
| **Granic zaufania sieci szkieletowej usług** | <ul><li>[Ogranicz dostęp anonimowy do klastra sieci szkieletowej usług](#anon-access-cluster)</li><li>[Upewnij się, że certyfikat klienta do węzła sieci szkieletowej usług różni się od certyfikatu węzła do węzła](#fabric-cn-nn)</li><li>[Użyj usługi AAD do uwierzytelniania klientów do klastrów sieci szkieletowej usług](#aad-client-fabric)</li><li>[Upewnij się, że certyfikaty sieci szkieletowej usług są uzyskiwane z zatwierdzonych certyfikatu urzędu certyfikacji](#fabric-cert-ca)</li></ul> |
| **Tożsamości serwera** | <ul><li>[Standardowe uwierzytelnianie scenariusze obsługiwane przez serwer tożsamości użycia](#standard-authn-id)</li><li>[Zastąp domyślną tożsamości tokenu pamięci podręcznej serwera z alternatywnymi skalowalne](#override-token)</li></ul> |
| **Granic zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Włącz uwierzytelnianie podczas nawiązywania połączenia z kolejki usługi MSMQ w programie WCF](#msmq-queues)</li><li>[Czy WCF nie ustawiono clientCredentialType komunikatu none](#message-none)</li><li>[Czy WCF nie ustawiono właściwości ClientCredentialType o wartości transportu none](#transport-none)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, standardowe uwierzytelnianie techniki są używane do zabezpieczania interfejsów API sieci Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Użyj uwierzytelniania standardowego scenariusze obsługiwane przez usługę Azure Active Directory](#authn-aad)</li><li>[Zastąp domyślną ADAL pamięć podręczną tokenów z alternatywnymi skalowalne](#adal-scalable)</li><li>[Upewnij się, że TokenReplayCache używany w celu uniknięcia powtórzeń tokenów uwierzytelniania ADAL](#tokenreplaycache-adal)</li><li>[Umożliwia zarządzanie token żądania od klientów protokołu OAuth2 do usługi AAD biblioteki ADAL (lub lokalnej usługi AD)](#adal-oauth2)</li></ul> |
| **Pole IoT bramy** | <ul><li>[Uwierzytelnianie urządzeń łączących się z bramą pola](#authn-devices-field)</li></ul> |
| **Brama chmury IoT** | <ul><li>[Upewnij się, że urządzenia podłączone do bramy chmury są uwierzytelniane](#authn-devices-cloud)</li><li>[Użyj poświadczeń uwierzytelniania na urządzenie](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że tylko wymagane kontenerów i obiektów blob są podane anonimowy dostęp do odczytu](#req-containers-anon)</li><li>[Przyznać ograniczony dostęp do obiektów w magazynie Azure za pomocą sygnatury dostępu Współdzielonego lub SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Rozważ użycie mechanizmu uwierzytelniania standardowego uwierzytelniania do aplikacji sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Uwierzytelnianie to proces jednostki, w przypadku gdy okaże się swoją tożsamość, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło. Brak dostępnych wiele protokołów uwierzytelniania, które mogą być uważane za. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na bazie systemu Windows</li><li>Na podstawie formularzy</li><li>Federacyjna - usług AD FS</li><li>Federacyjna — usługi Azure AD</li><li>Federacyjna - tożsamości serwera</li></ul><p>Rozważ użycie mechanizmu uwierzytelniania standardowego do identyfikowania procesu źródła</p>|

## <a id="handle-failed-authn"></a>Aplikacje muszą obsługiwać bezpiecznie scenariusze uwierzytelniania nie powiodło się

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Aplikacje, które jawnie uwierzytelnianie użytkowników musi obsługiwać bezpiecznie scenariusze uwierzytelniania nie powiodło się. Mechanizm uwierzytelniania, musi:</p><ul><li>Odmowa dostępu do uprzywilejowanych zasobów, jeśli uwierzytelnianie nie powiedzie się</li><li>Wyświetl ogólny komunikat o błędzie po uwierzytelnieniu nie powiodło się i występuje odmowa dostępu</li></ul><p>Testowanie:</p><ul><li>Ochrona zasobów uprzywilejowanych po zakończonych niepowodzeniem prób zalogowania</li><li>Ogólny komunikat o błędzie jest wyświetlany na uwierzytelnianie nie powiodło się i zdarzenia odmowa dostępu</li><li>Konta są wyłączone po nadmiernej liczby nieudanych prób</li><ul>|

## <a id="step-up-adaptive-authn"></a>Włącz zwiększenia lub adaptacyjną uwierzytelniania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Sprawdź aplikacja ma dodatkowe autoryzacji (takie jak wzmocnić lub adaptacyjną uwierzytelniania, za pośrednictwem usługi Multi-Factor authentication, takie jak wysyłanie SMS, wiadomości e-mail itd. lub monitowanie o ponowne uwierzytelnianie OTP), użytkownik jest wezwał zanim zostanie im przyznany dostęp do poufne informacje. Ta zasada ma zastosowanie również do zmiany krytyczne do konta lub akcji</p><p>Oznacza to również, że dostosowania uwierzytelniania ma wdrażane w taki sposób, że aplikacja wymusza poprawnie kontekstowa autoryzacji umożliwiającej nie nieautoryzowanego manipulowania za pomocą klasy w przykładzie parametr naruszeniu</p>|

## <a id="admin-interface-lockdown"></a>Upewnij się, że interfejsy administracyjne są odpowiednio zablokowana

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | Pierwsze rozwiązanie jest przyznać dostęp tylko z określonego zakresu źródłowego adresu IP interfejsu administracyjnego. Jeśli rozwiązania nie jest możliwe, nie zawsze jest zalecane do wymuszania bardziej zaawansowanego lub adaptacyjną uwierzytelniania do logowania do interfejsu administracyjnego |

## <a id="forgot-pword-fxn"></a>Implementowanie bezpiecznie nie pamiętasz hasła funkcji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Pierwszą czynnością jest do sprawdzenia, czy nie pamiętasz hasła i inne ścieżki odzyskiwania wysłać łącze w tym token aktywacji ograniczone czasowo, a nie samego hasła. Dodatkowe uwierzytelnianie oparte na tokeny elastyczne (np. programu SMS tokenu, natywnych aplikacji dla urządzeń przenośnych, itp.) może być wymagana również przed wysłaniem za pośrednictwem łącza. Drugie użytkownik powinien nie zablokowania konta użytkowników podczas, gdy proces pobierania nowe hasło jest w toku.</p><p>Może to prowadzić do odmowy usługi zawsze, gdy osoba atakująca decyduje się na celowo zablokowania użytkownikom atak automatycznych. Trzecie zawsze, gdy nowe żądanie hasła została ustawiona w toku, wiadomości, którą można wyświetlić powinien uogólniony zapobiec wyliczenie nazwy użytkownika. Czwarty zawsze nie zezwalaj na korzystanie z haseł stary i zaimplementować zasady silne hasło.</p> |

## <a id="pword-account-policy"></a>Upewnij się, że są wykonywane zasad haseł i kont

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Szczegóły | <p>Powinny zostać wdrożone zasady haseł i kont zgodność z zasadami organizacyjnymi i najlepsze rozwiązania.</p><p>Obrony przed metodą siłową i słownik na podstawie odgadnięcie: musi być implementowana silnych haseł, aby upewnić się, że użytkownicy tworzyć złożone hasło (np. minimalna długość 12 znaków, alfanumeryczne i znaki specjalne).</p><p>Zasady blokowania konta mogą zostać wdrożone w następujący sposób:</p><ul><li>**Elastyczne blokady:** może to być dobre rozwiązanie do ochrony przed atakami siłowymi użytkowników. Na przykład zawsze, gdy użytkownik wprowadzi nieprawidłowe hasło trzy razy aplikacja może zablokować konta minutę celu spowalniać metodą siłową wymuszanie hasła, dzięki czemu mniej korzystna dla osoby atakującej kontynuować. Gdyby zaimplementować twardych przeciwdziałanie blokady w ramach tego przykładu może osiągnąć "DOS" przez trwale blokowania kont. Możesz też aplikacji może wygenerować OTP (jedno hasło czasu) i wysłać go poza pasmem (za pośrednictwem poczty e-mail, sms itp.) dla użytkownika. Innym rozwiązaniem może być do zaimplementowania CAPTCHA, po osiągnięciu wartości progowej liczby nieudanych prób.</li><li>**Twarde blokady:** powinien być zastosowany ten typ blokady, za każdym razem wykryć użytkownika aplikacji do zaatakowania i licznika go za pomocą trwale zablokowania jego konta, dopóki zespół miał czas na wykonanie ich dowodowe. Po tego procesu można zdecydować, aby dać użytkownikowi kopii jego konta lub wykonywanie dodatkowych działań prawnych względem niego. Takiego podejścia uniemożliwia osobie atakującej więcej przechodzące przez aplikacji i infrastruktury.</li></ul><p>Obrony przed atakami na domyślnym i przewidywalne kont, sprawdź wszystkie klucze i hasła są zastąpienia, a są wygenerować lub zastąpione po godzinie instalacji.</p><p>Jeśli aplikacja ma do automatycznego generowania haseł, upewnij się, że wygenerowanego hasła są losowe i wymagają wysokiej entropii.</p>|

## <a id="controls-username-enum"></a>Wdrożenie kontroli w celu zapobieganie wyliczanie nazwy użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wszystkie komunikaty o błędach powinny uogólniony zapobiec wyliczenie nazwy użytkownika. Również czasami nie można pominąć informacje przeciek w funkcje, takie jak strona rejestracji. W tym miejscu należy użyć metody limitów szybkości, takie jak CAPTCHA, aby zapobiec atak automatycznych przez osobę atakującą. |

## <a id="win-authn-sql"></a>Jeśli to możliwe, należy używać uwierzytelniania systemu Windows do połączenia z programem SQL Server

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | OnPrem |
| **Atrybuty**              | Wersja programu SQL — wszystkie |
| **Odwołania**              | [SQL Server — wybierz tryb uwierzytelniania](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroki** | Uwierzytelnianie systemu Windows używa protokołu zabezpieczeń Kerberos, zapewnia wymuszanie zasad haseł w odniesieniu do weryfikacji złożoności silnych haseł, zapewnia obsługę blokady konta i obsługuje wygasanie haseł.|

## <a id="aad-authn-sql"></a>Jeśli to możliwe Użyj uwierzytelniania usługi Azure Active Directory dotyczących nawiązania połączenia z bazą danych SQL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Usługi SQL Azure |
| **Atrybuty**              | Wersja programu SQL — wersja 12 |
| **Odwołania**              | [Łączenie z bazą danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroki** | **Minimalna wersja:** bazy danych SQL Azure V12 wymagany w celu umożliwienia uwierzytelniania usługi AAD przed Directory firmy Microsoft, baza danych SQL Azure |

## <a id="authn-account-pword"></a>Gdy używany jest tryb uwierzytelniania SQL, upewnij się, że konto i hasło zasady są wymuszane na serwerze SQL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zasady haseł programu SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroki** | Podczas korzystania z uwierzytelniania programu SQL Server, logowania są tworzone w programie SQL Server, które nie są oparte na kontach użytkowników systemu Windows. Nazwa użytkownika i hasło są tworzone za pomocą programu SQL Server i przechowywane w programie SQL Server. Program SQL Server można użyć mechanizmów zasad haseł systemu Windows. Użyć tego samego złożoność i zasady wygasania umożliwia hasła używane w programie SQL Server w systemie Windows. |

## <a id="autn-contained-db"></a>Nie należy używać uwierzytelniania SQL w zawartych baz danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Lokalnego, programu SQL Azure |
| **Atrybuty**              | Wersja - MSSQL2012, wersja programu SQL - SQL w wersji 12 |
| **Odwołania**              | [Najlepsze rozwiązania z zawartych baz danych](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroki** | Brak wymuszonego hasła może zwiększyć prawdopodobieństwo słabe poświadczenia ustanowiono w zawartej bazie danych. Wykorzystywanie uwierzytelniania systemu Windows. |

## <a id="authn-sas-tokens"></a>Użyj na tokeny sygnatury dostępu współdzielonego poświadczenia uwierzytelniania urządzenia

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | <p>Model zabezpieczeń usługi Event Hubs jest oparty na kombinację tokenów dostępu sygnatury dostępu Współdzielonego i wydawców zdarzeń. Nazwa wydawcy reprezentuje DeviceID, który odbiera token. Umożliwi to skojarzenie tokeny wygenerowane z odpowiednich urządzeń.</p><p>Wszystkie komunikaty są oznaczane nadawca po stronie usługi, umożliwiając wykrywanie pochodzenia — jest ładunku fałszowania prób. Podczas uwierzytelniania urządzenia, należy wygenerować token sygnatury dostępu współdzielonego urządzenia do wydawcy unikatowy zakresu na.</p>|

## <a id="multi-factor-azure-admin"></a>Włącz uwierzytelnianie wieloskładnikowe platformy Azure dla administratorów usługi Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania Azure | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Co to jest usługa Multi-Factor Authentication platformy Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroki** | <p>Uwierzytelnianie wieloskładnikowe (MFA) jest metoda uwierzytelniania, która wymaga więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Działania, gdyż dowolne dwa lub więcej z następujących metod weryfikacji:</p><ul><li>Coś znasz (zwykle hasła)</li><li>Coś (zaufanych urządzeń, który nie jest łatwo zduplikowany, takich jak telefon)</li><li>Coś jest (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Ogranicz dostęp anonimowy do klastra sieci szkieletowej usług

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure  |
| **Odwołania**              | [Scenariusze zabezpieczeń klastra sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroki** | <p>Klastry powinien zawsze zabezpieczony w taki sposób, aby uniemożliwić nieautoryzowanym użytkownikom połączenie z klastrem, szczególnie w przypadku, gdy ma na nim uruchomione obciążeń produkcyjnych.</p><p>Podczas tworzenia klastra sieci szkieletowej usług, upewnij się, że tryb zabezpieczeń jest ustawiony na "secure" i skonfiguruj wymagany certyfikat X.509. Tworzenie klastra "niebezpieczne" umożliwi każdy użytkownik anonimowy się z nim połączyć, jeśli takie punkty końcowe zarządzania do publicznej sieci Internet.</p>|

## <a id="fabric-cn-nn"></a>Upewnij się, że certyfikat klienta do węzła sieci szkieletowej usług różni się od certyfikatu węzła do węzła

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure, środowisko — autonomiczny |
| **Odwołania**              | [Zabezpieczenia certyfikatów klienta do węzła sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Połącz z klastrem bezpiecznego przy użyciu certyfikatu klienta](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroki** | <p>Węzeł klient certyfikat zabezpieczeń jest skonfigurowany podczas tworzenia klastra za pośrednictwem portalu Azure, szablonów usługi Resource Manager lub szablonu JSON autonomiczny, określając administracyjnej certyfikatu klienta i/lub certyfikatu klienta użytkownika.</p><p>Klientów i użytkowników certyfikatów klienta administracyjnego przez użytkownika powinna być inna niż określone dla węzła do węzła zabezpieczeń certyfikatów głównych i dodatkowych.</p>|

## <a id="aad-client-fabric"></a>Użyj usługi AAD do uwierzytelniania klientów do klastrów sieci szkieletowej usług

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [W scenariuszach zabezpieczeń — zalecenia dotyczące zabezpieczeń klastra](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroki** | Klastry z systemem Azure można również bezpieczny dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (AAD), z wyjątkiem certyfikatów klienta. W przypadku klastrów platformy Azure zaleca się użyć zabezpieczeń usługi AAD do uwierzytelniania klientów i certyfikatów dla zabezpieczeń węzła do węzła.|

## <a id="fabric-cert-ca"></a>Upewnij się, że certyfikaty sieci szkieletowej usług są uzyskiwane z zatwierdzonych certyfikatu urzędu certyfikacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Certyfikaty X.509 i sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroki** | <p>Sieć szkieletowa usług używa certyfikatów X.509 w celu uwierzytelniania węzłów i klientów.</p><p>Kilka ważnych rzeczy do uwzględnienia podczas korzystania z certyfikatów w sieci szkieletowej usług:</p><ul><li>Certyfikatów używanych w klastrach z systemem obciążeń produkcyjnych powinna być utworzony przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub uzyskane z zatwierdzonych certyfikatu urzędu certyfikacji. Urząd certyfikacji może być zatwierdzone zewnętrznego urzędu certyfikacji lub właściwie zarządzane wewnętrznej infrastruktury kluczy publicznych (PKI)</li><li>Nigdy nie używaj żadnych tymczasowej lub certyfikaty testów w środowisku produkcyjnym, które są tworzone za pomocą narzędzi, takich jak MakeCert.exe</li><li>Można użyć certyfikatu z podpisem własnym, ale powinien tylko zrobić dla klastrów testowych, a nie w środowisku produkcyjnym</li></ul>|

## <a id="standard-authn-id"></a>Standardowe uwierzytelnianie scenariusze obsługiwane przez serwer tożsamości użycia

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IdentityServer3 - duży obraz](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroki** | <p>Poniżej przedstawiono typowe interakcji obsługiwane przez serwer tożsamości:</p><ul><li>Przeglądarki komunikacji z aplikacjami sieci web</li><li>Aplikacje sieci Web komunikować się z interfejsów API sieci web (czasami samodzielnie, czasami w imieniu użytkownika)</li><li>Aplikacje oparte na przeglądarce komunikować się z interfejsów API sieci web</li><li>Natywnych aplikacji komunikować się z interfejsów API sieci web</li><li>Aplikacje oparte na serwerze komunikować się z interfejsów API sieci web</li><li>Interfejsy API sieci Web komunikować się z interfejsów API sieci web (czasami samodzielnie, czasami w imieniu użytkownika)</li></ul>|

## <a id="override-token"></a>Zastąp domyślną tożsamości tokenu pamięci podręcznej serwera z alternatywnymi skalowalne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wdrażanie serwera tożsamości — buforowanie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | <p>IdentityServer ma prosty wbudowanych w pamięci podręcznej. Jest to dobra dla natywnych aplikacji małą skalę, go nie obsługuje mid warstwy i wewnętrznej bazy danych aplikacji z następujących powodów:</p><ul><li>Te aplikacje są dostępne przez wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie tworzy problemy izolacji i przedstawia problemy podczas działania na dużą skalę: w przypadku wielu użytkowników, każda z dowolną liczbę tokenów jako zasoby w ich imieniu dostęp do aplikacji może oznaczać dużych liczb i operacji wyszukiwania bardzo kosztowna</li><li>Te aplikacje są zwykle wdrażane w topologii rozproszonej, w której wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny pamięci podręcznej musi być odporny odtwarza proces i deactivations</li><li>Dla wszystkich powyższych powodów podczas wdrażania aplikacji sieci web, zalecane jest zastąpienie domyślnego serwera tożsamości pamięci podręcznej tokenu z skalowalne alternatywne, takie jak pamięć podręczna Azure Redis</li></ul>|

## <a id="binaries-signed"></a>Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że pliki binarne wdrożonej aplikacji są podpisane cyfrowo, dzięki czemu można zweryfikować integralność plików binarnych|

## <a id="msmq-queues"></a>Włącz uwierzytelnianie podczas nawiązywania połączenia z kolejki usługi MSMQ w programie WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroki** | Program nie powiedzie się włączyć uwierzytelnianie podczas nawiązywania połączenia z kolejki usługi MSMQ, osoba atakująca może anonimowo przesyłania wiadomości do kolejki w celu przetworzenia. Jeśli uwierzytelnianie nie jest używana do nawiązania połączenia kolejki usługi MSMQ, używany do dostarczenia komunikatu do innego programu, osoba atakująca może przesłać anonimowe komunikat o błędzie jest złośliwe.|

### <a name="example"></a>Przykład
`<netMsmqBinding/>` Element pliku konfiguracji usługi WCF poniżej nakazuje WCF, aby wyłączyć uwierzytelnianie podczas nawiązywania połączenia z kolejki usługi MSMQ w celu dostarczania komunikatów.
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
Konfigurowanie usługi MSMQ, aby wymusić uwierzytelnianie domeny systemu Windows lub certyfikatu przez cały czas jakiekolwiek komunikaty przychodzące lub wychodzące.

### <a name="example"></a>Przykład
`<netMsmqBinding/>` Element pliku konfiguracji usługi WCF poniżej nakazuje WCF, aby włączyć uwierzytelnianie certyfikatu podczas nawiązywania połączenia z kolejki usługi MSMQ. Klient jest uwierzytelniany przy użyciu certyfikatów X.509. Certyfikat klienta musi znajdować się w magazynie certyfikatów serwera.
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

## <a id="message-none"></a>Czy WCF nie ustawiono clientCredentialType komunikatu none

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta - brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy jest w stanie uzyskać dostępu do tej usługi. Usługa, która nie uwierzytelnia klientów umożliwia dostęp do wszystkich użytkowników. Konfigurowanie aplikacji w celu uwierzytelniania poświadczeń klienta. Można to zrobić przez ustawienie clientCredentialType komunikatów systemu Windows lub certyfikatu. |

### <a name="example"></a>Przykład
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>Czy WCF nie ustawiono właściwości ClientCredentialType o wartości transportu none

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, .NET Framework 3 |
| **Atrybuty**              | Typ poświadczeń klienta - brak |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Kroki** | Brak uwierzytelniania oznacza, że wszyscy jest w stanie uzyskać dostępu do tej usługi. Usługa, która nie uwierzytelnia klientów zezwala wszystkim użytkownikom na dostęp do jego funkcjonalność. Konfigurowanie aplikacji w celu uwierzytelniania poświadczeń klienta. Można to zrobić, ustawiając właściwość clientCredentialType transportu do systemu Windows lub certyfikatu. |

### <a name="example"></a>Przykład
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Upewnij się, standardowe uwierzytelnianie techniki są używane do zabezpieczania interfejsów API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i autoryzacja w składniku ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [zewnętrznych usług uwierzytelniania z interfejsu API sieci Web platformy ASP.NET (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroki** | <p>Uwierzytelnianie to proces jednostki, w przypadku gdy okaże się swoją tożsamość, zwykle za pomocą poświadczeń, takie jak nazwa użytkownika i hasło. Brak dostępnych wiele protokołów uwierzytelniania, które mogą być uważane za. Poniżej wymieniono niektóre z nich:</p><ul><li>Certyfikaty klienta</li><li>Na bazie systemu Windows</li><li>Na podstawie formularzy</li><li>Federacyjna - usług AD FS</li><li>Federacyjna — usługi Azure AD</li><li>Federacyjna - tożsamości serwera</li></ul><p>Łącza w sekcji odwołań zawierają niskiego poziomu szczegółowe informacje dotyczące sposobu wszystkich schematów uwierzytelniania implementacji zabezpieczyć interfejs API sieci Web.</p>|

## <a id="authn-aad"></a>Użyj uwierzytelniania standardowego scenariusze obsługiwane przez usługę Azure Active Directory

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Scenariusze uwierzytelniania dla usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory przykłady kodu](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [przewodnik dewelopera usługi Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroki** | <p>Azure Active Directory (Azure AD) ułatwia uwierzytelniania dla deweloperów, zapewniając tożsamości jako usługa, z obsługą standardowych protokołów, takich jak OAuth 2.0 i OpenID Connect. Poniżej przedstawiono pięć scenariuszy głównej aplikacji obsługiwanych przez usługę Azure AD:</p><ul><li>Przeglądarki sieci Web aplikacji sieci Web: użytkownik musi zalogować się do aplikacji sieci web chronionej przez usługę Azure AD</li><li>Jednej strony aplikacji JEDNOSTRONICOWEJ: Użytkownik musi zalogować się do aplikacji jednej strony, która jest zabezpieczone przez usługę Azure AD</li><li>Aplikacji natywnej do interfejsu API sieci Web: natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z składnika web API, który jest zabezpieczony przez usługę Azure AD</li><li>Aplikacja sieci Web interfejsu API sieci Web: aplikacja sieci web musi pobrać zasobów z składnika web API zabezpieczone przez usługę Azure AD</li><li>Demon lub aplikacji serwera do interfejsu API sieci Web: aplikacja demon lub aplikacja serwera bez interfejsu użytkownika sieci web musi pobrać zasobów ze składnika web API zabezpieczone przez usługę Azure AD</li></ul><p>Zapoznaj się z linków w sekcji odwołań dla szczegóły implementacji niskiego poziomu</p>|

## <a id="adal-scalable"></a>Zastąp domyślną ADAL pamięć podręczną tokenów z alternatywnymi skalowalne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie w usłudze Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [przy użyciu Redis jako pamięci podręcznej tokenu ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroki** | <p>Pamięci podręcznej domyślne, które używa biblioteki ADAL (Active Directory Authentication Library) jest w pamięci podręcznej, używający statycznych magazynu, dostępne dla procesu. Gdy działa natywnych aplikacji, go nie obsługuje mid warstwy i wewnętrznej bazy danych aplikacji z następujących powodów:</p><ul><li>Te aplikacje są dostępne przez wielu użytkowników jednocześnie. Zapisywanie wszystkich tokenów dostępu w tym samym magazynie tworzy problemy izolacji i przedstawia problemy podczas działania na dużą skalę: w przypadku wielu użytkowników, każda z dowolną liczbę tokenów jako zasoby w ich imieniu dostęp do aplikacji może oznaczać dużych liczb i operacji wyszukiwania bardzo kosztowna</li><li>Te aplikacje są zwykle wdrażane w topologii rozproszonej, w której wiele węzłów musi mieć dostęp do tej samej pamięci podręcznej</li><li>Tokeny pamięci podręcznej musi być odporny odtwarza proces i deactivations</li></ul><p>Dla wszystkich powyższych powodów podczas wdrażania aplikacji sieci web, zalecane jest aby zastąpić domyślny ADAL pamięć podręczną tokenów z skalowalne alternatywne, takie jak pamięć podręczna Azure Redis.</p>|

## <a id="tokenreplaycache-adal"></a>Upewnij się, że TokenReplayCache używany w celu uniknięcia powtórzeń tokenów uwierzytelniania ADAL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nowoczesne uwierzytelnianie w usłudze Azure Active Directory dla aplikacji sieci Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroki** | <p>Właściwość TokenReplayCache umożliwia deweloperom Definiowanie pamięci podręcznej powtórzeń tokenów, magazynu, który może służyć do zapisywania tokenów w celu sprawdzenia, że token nie mogą być używane w więcej niż raz.</p><p>Jest to miara przed atak wspólnej ataku aptly wywołane powtórzeń tokenów: przechwytywaniu token wysyłane podczas logowania osoba atakująca może podjąć próbę ponownie wysłać do aplikacji ("" Odtwórz je ponownie) do utworzenia nowej sesji. Np. przepływu grant kodu w OIDC, po uwierzytelnieniu użytkownika pomyślnie, żądanie "/ signin-oidc" punkt końcowy uzależnionej staje się z "w żądaniu", "code" i "stanu" parametrów.</p><p>Jednostki uzależnionej weryfikuje tego żądania i ustanawia nowej sesji. Jeśli atakujący dokonuje tego żądania przechwytuje i odtwarzaniem go, on ustanowić sesję pomyślnie i podszywają się pod użytkownika. Obecność identyfikator jednorazowy w OpenID Connect można ograniczyć, ale całkowicie wyeliminować okoliczności, w których atak może pomyślnie przyjęte. Aby chronić swoje aplikacje, deweloperzy mogą zapewniać implementację ITokenReplayCache i przypisz wystąpienie do TokenReplayCache.</p>|

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
Oto przykładowe zastosowanie interfejsu ITokenReplayCache. (Należy dostosować i implementowanie framework buforowania z określonego projektu)
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
Zaimplementowany pamięci podręcznej musi odwoływać się do opcji OIDC za pomocą właściwości "TokenValidationParameters" w następujący sposób.
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

Należy pamiętać, że do testowania efektywności tę konfigurację, zaloguj się do lokalnych aplikacji chronionej przez OIDC i przechwytywania żądanie `"/signin-oidc"` punktu końcowego w narzędziu fiddler. Jeśli ochrona nie jest w miejscu, odtwarzanie tego żądania w narzędziu fiddler ustawi nowego pliku cookie sesji. Jeśli żądanie jest odtwarzany po dodaniu ochrony TokenReplayCache, aplikacji spowoduje zgłoszenie wyjątku w następujący sposób: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Umożliwia zarządzanie token żądania od klientów protokołu OAuth2 do usługi AAD biblioteki ADAL (lub lokalnej usługi AD)

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroki** | <p>Usługi Azure AD authentication Library (ADAL) umożliwia deweloperom aplikacji klienta łatwe uwierzytelnianie użytkowników w chmurze lub lokalnej usługi Active Directory (AD), a następnie uzyskać tokeny dostępu dla zabezpieczanie interfejsu API.</p><p>Biblioteka ADAL oferuje wiele funkcji uwierzytelniania upewnij łatwiejsze dla deweloperów, takie jak obsługa komunikacji asynchronicznej, można skonfigurować tokenu pamięci podręcznej, która przechowuje tokenów dostępu i tokenów odświeżania, automatyczne odświeżanie tokenu po wygaśnięciu tokenu dostępu i token odświeżania jest dostępny, i więcej.</p><p>Dzięki obsłudze większość złożoności, ADAL można zawęzić dewelopera na logice biznesowej w aplikacjach i łatwo zabezpieczenia zasobów bez konieczności ekspertem w zakresie zabezpieczeń. Oddzielne biblioteki są dostępne dla platformy .NET, JavaScript (klient i Node.js), iOS, Android i Java.</p>|

## <a id="authn-devices-field"></a>Uwierzytelnianie urządzeń łączących się z bramą pola

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, czy każde urządzenie jest uwierzytelniony przez bramę pola przed zaakceptowaniem z nich danych oraz przed ułatwienia nadrzędnego komunikacja z usługą bramy chmury. Upewnij się również, że z łączyć się urządzenia na urządzeniu poświadczeń, dzięki czemu można jednoznacznie zidentyfikować poszczególnych urządzeń.|

## <a id="authn-devices-cloud"></a>Upewnij się, że urządzenia podłączone do bramy chmury są uwierzytelniane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, C#, Node.JS,  |
| **Atrybuty**              | Nie dotyczy — wybór bramy - Centrum IoT Azure |
| **Odwołania**              | Nie dotyczy — [Centrum Azure IoT z platformą .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [wprowadzenie o Centrum IoT i Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [zabezpieczanie IoT z sygnatury dostępu Współdzielonego i certyfikaty](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repozytorium Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroki** | <ul><li>**Ogólny:** uwierzytelnienia urządzenia przy użyciu zabezpieczeń TLS (Transport Layer) lub protokołu IPSec. Infrastruktura powinna obsługiwać przy użyciu klucza wstępnego (PSK) na tych urządzeniach, które nie może obsłużyć pełnej asymetrycznego kryptografii. Korzystaj z usługi Azure AD, Oauth.</li><li>**C#:** podczas tworzenia wystąpienia DeviceClient, domyślnie, metody Create tworzy wystąpienie DeviceClient, który korzysta z protokołu AMQP do komunikowania się z Centrum IoT. Aby używać protokołu HTTPS, należy użyć zastąpienie metody Create, która umożliwia określenie protokołu. Jeśli używasz protokołu HTTPS, należy również dodać `Microsoft.AspNet.WebApi.Client` pakiet NuGet do projektu, aby uwzględnić `System.Net.Http.Formatting` przestrzeni nazw.</li></ul>|

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
**Node.JS: uwierzytelnianie**
#### <a name="symmetric-key"></a>Klucz symetryczny
* Tworzenie Centrum IoT na platformie azure
* Utwórz wpis w rejestrze tożsamości urządzeń
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Utworzyć symulowane urządzenie
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>Token sygnatury dostępu współdzielonego
* Pobiera zostały wygenerowane wewnętrznie przy użyciu klucza symetrycznego, ale można wygenerować i używają go jawnie także
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
* Generowanie self X509 podpisanego certyfikatu przy użyciu dowolnego narzędzia, takie jak biblioteki OpenSSL, aby wygenerować .cert i .key plików do przechowywania odpowiednio certyfikat i klucz
* Udostępnić urządzenia, który akceptuje połączenia zabezpieczonego za pomocą certyfikatów.
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

## <a id="authn-cred"></a>Użyj poświadczeń uwierzytelniania na urządzenie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT  | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy - Centrum IoT Azure |
| **Odwołania**              | [Tokeny zabezpieczające Centrum IoT Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroki** | Użycie na tokeny sygnatury dostępu współdzielonego poświadczenia uwierzytelniania urządzenia na podstawie klucza urządzenia lub certyfikat klienta, zamiast poziomie Centrum IoT udostępnionych zasad dostępu. Zapobiega to ponowne użycie tokeny uwierzytelniania jednego pola lub urządzenia bramy przez inną |

## <a id="req-containers-anon"></a>Upewnij się, że tylko wymagane kontenerów i obiektów blob są podane anonimowy dostęp do odczytu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sygnatury dostępu współdzielonego, część 1: opis modelu sygnatur dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroki** | <p>Domyślnie kontener i wszystkie obiekty BLOB w nim mogą być używane tylko przez właściciela konta magazynu. Aby udostępnić użytkownikom anonimowym uprawnienia odczytu do kontenera i jego obiektów blob, co można ustawić uprawnień kontenera, aby zezwolić na publiczny dostęp. Użytkownicy anonimowi mogą odczytywać obiekty BLOB w kontenerze publicznie bez uwierzytelniania żądania.</p><p>Kontenery zawierają następujące opcje Zarządzanie dostępem kontenera:</p><ul><li>Pełne publiczny dostęp do odczytu: obiektów blob i kontenera danych mogą być odczytywane za pomocą żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze, za pomocą żądania od użytkowników anonimowych, ale nie można wyliczyć kontenery w ramach konta magazynu.</li><li>Dostęp do obiektów blob tylko odczytu publicznego: dane obiektów Blob w tym kontenerze mogą być odczytywane za pomocą żądania od użytkowników anonimowych, ale nie są dostępne dane kontenera. Klienci nie można wyliczyć obiektów blob w kontenerze, za pomocą żądań anonimowych</li><li>Dostęp do odczytu żadnego elementu publicznego public: obiektów blob i kontenera danych mogą być odczytywane tylko właściciel konta</li></ul><p>Dostęp anonimowy jest najlepsze w przypadku scenariuszy, w którym niektórych obiektów blob zawsze powinny być dostępne dla anonimowego dostępu do odczytu. Precyzyjny system kontroli można utworzyć sygnatury dostępu współdzielonego, dzięki czemu przy użyciu różnych uprawnienia dostępu delegowanie ograniczone i w określonym przedziale czasu. Upewnij się, że kontenerów i obiektów blob, które potencjalnie mogą zawierać poufne dane, nie otrzymują dostęp anonimowy przypadkowo</p>|

## <a id="limited-access-sas"></a>Przyznać ograniczony dostęp do obiektów w magazynie Azure za pomocą sygnatury dostępu Współdzielonego lub SAP

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND |
| **Odwołania**              | [Współużytkowanych sygnatur dostępu, część 1: Opis modelu sygnatur dostępu Współdzielonego](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [sygnatury dostępu współdzielonego, część 2: tworzenie i używanie sygnatury dostępu Współdzielonego z magazynem obiektów Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [jak delegować dostęp do obiektów na koncie przy użyciu udostępnionych Sygnatur dostępu i zasad dostępu przechowywane](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroki** | <p>Przy użyciu sygnatury dostępu współdzielonego (SAS) jest wygodnym sposobem przyznać ograniczony dostęp do obiektów na koncie magazynu dla innych klientów bez konieczności ujawnia klucz dostępu do konta. Identyfikator URI, który obejmuje w jego parametrów zapytania jest sygnatury dostępu Współdzielonego wszystkie informacje niezbędne do uwierzytelniony dostęp do zasobów magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient musi tylko Przekaż sygnatury dostępu Współdzielonego do odpowiedniego konstruktora lub metody.</p><p>Można użyć sygnatury dostępu Współdzielonego, gdy chcesz zapewnić dostęp do zasobów na koncie magazynu do klienta, który nie jest zaufany z kluczem konta. Klucze konta magazynu obejmują zarówno podstawowego i pomocniczego klucza, które przyznawaj dostęp administracyjny do konta i wszystkich zawartych w niej zasobów. Udostępnianie jednej z Twoich kluczy konta otwiera konta możliwości złośliwego lub brak dbałości o ich użycia. Sygnatury dostępu współdzielonego zapewniają bezpieczne alternatywę umożliwiającą innych klientów do odczytu, zapisu i usuwania danych na koncie magazynu, zgodnie z uprawnieniami, które zostały przyznane i bez potrzeby klucz konta.</p><p>Jeśli masz logiczne zestaw parametrów, które są podobne za każdym razem, przy użyciu przechowywanych zasad dostępu (SAP) jest lepiej zrozumieć. Ponieważ przy użyciu sygnatury dostępu Współdzielonego uzyskane z zasad dostępu do przechowywanych daje możliwość natychmiast odwołać tego SAS, jest zalecanym najlepszym rozwiązaniem jest zawsze używaj przechowywane zasad dostępu, gdy jest to możliwe.</p>|