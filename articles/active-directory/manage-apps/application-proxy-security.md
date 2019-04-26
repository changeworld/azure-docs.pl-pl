---
title: Zagadnienia dotyczące zabezpieczeń dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Omówiono zagadnienia dotyczące zabezpieczeń dotyczące korzystania z serwera Proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bb9fc806779565581fa7667749402f5608edd80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292757"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji, które zdalnie za pomocą serwera Proxy aplikacji usługi Azure AD

W tym artykule opisano składniki, które współpracują, aby zabezpieczyć użytkowników i aplikacji korzystając z serwera Proxy usługi Azure Active Directory aplikacji.

Na poniższym diagramie przedstawiono jak usługa Azure AD umożliwia bezpieczny dostęp zdalny do aplikacji w środowisku lokalnym.

 ![Diagram przedstawiający bezpieczny dostęp zdalny za pośrednictwem serwera Proxy aplikacji usługi Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Korzyści w zakresie zabezpieczeń

Serwer Proxy aplikacji usługi Azure AD oferuje następujące korzyści zabezpieczeń:

### <a name="authenticated-access"></a>Dostęp uwierzytelniony 

Jeśli zdecydujesz się używać usługi Azure Active Directory uwierzytelnienia wstępnego, tylko uwierzytelnionych połączeń dostęp sieci.

Serwer Proxy aplikacji usługi Azure AD zależy od usługi Azure AD usługę tokenu zabezpieczającego (STS) do całego uwierzytelniania.  Wstępnego uwierzytelniania ze swej natury blokuje znaczna liczba anonimowe ataki, ponieważ tylko uwierzytelnionych tożsamości można uzyskać dostępu do aplikacji zaplecza.

Jeśli wybierzesz przekazywanie jako metody uwierzytelniania wstępnego nie uzyskasz tej korzyści. 

### <a name="conditional-access"></a>Dostęp warunkowy

Zastosować bardziej rozbudowane kontroli zasad, aby umożliwić nawiązywane są połączenia z siecią.

Za pomocą [dostępu warunkowego](../conditional-access/overview.md), można zdefiniować ograniczenia, w jaki ruch jest dozwolony dostęp do Twojej aplikacji zaplecza. Można utworzyć zasady, które ograniczają logowania na podstawie lokalizacji, siły uwierzytelniania i profil ryzyka użytkownika.

Dostęp warunkowy umożliwia również konfigurowanie zasad uwierzytelniania wieloskładnikowego, dodając kolejną warstwę zabezpieczeń do Twojej uwierzytelnienia użytkownika. Ponadto aplikacje też mogą być kierowane usługą Microsoft Cloud App Security za pośrednictwem dostępu warunkowego usługi Azure AD, aby zapewnić monitorowanie w czasie rzeczywistym i kontrolek przy użyciu [dostępu](https://docs.microsoft.com/cloud-app-security/access-policy-aad) i [sesji](https://docs.microsoft.com/cloud-app-security/session-policy-aad) zasad

### <a name="traffic-termination"></a>Zakończenie ruchu

Cały ruch zostanie zakończony w chmurze.

Ponieważ serwer Proxy aplikacji usługi Azure AD jest zwrotnym serwerem proxy, cały ruch do aplikacji zaplecza jest kończona na usługę. Sesja Pobierz ponownie ustanowić, tylko w przypadku serwerów zaplecza, co oznacza, że serwery zaplecza nie są widoczne do kierowania ruchu HTTP. Ta konfiguracja oznacza, że możesz lepiej są chronione przed atakami ukierunkowanymi.

### <a name="all-access-is-outbound"></a>Wszelki dostęp jest wychodzący 

Nie trzeba otwierać połączenia przychodzące do sieci firmowej.

Łączników serwera Proxy aplikacji używać tylko połączeń wychodzących usługi serwera Proxy aplikacji usługi Azure AD, co oznacza, że nie ma potrzeby otwierania portów zapory dla połączeń przychodzących. Tradycyjne serwery proxy wymagane w sieci obwodowej (znany także jako *DMZ*, *strefą zdemilitaryzowaną*, lub *podsiecią ekranowaną*) i mieć dostęp do nieuwierzytelnione połączenia na granicy sieci. W tym scenariuszu wymagane inwestycje w produkty zapory aplikacji sieci web, do analizowania ruchu i ochrony środowiska. Dzięki serwerowi Proxy aplikacji nie potrzebujesz sieci obwodowej polecenie wszystkich połączeń wychodzących, a miejsce za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji na temat łączników, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analizy skali chmury i uczenia maszynowego 

Uzyskaj zaawansowane zabezpieczenia.

Ponieważ jest częścią usługi Azure Active Directory, mogą korzystać z serwera Proxy aplikacji [usługi Azure AD Identity Protection](../active-directory-identityprotection.md), przy użyciu danych z Microsoft Security Response Center i Digital Crimes Unit. Razem możemy aktywnego identyfikowania zagrożonych konta i oferuje ochronę przed wysokiego ryzyka logowania. Firma Microsoft wziąć pod uwagę wiele czynników, w celu określenia, które prób logowania są o wysokim ryzyku. Czynniki te obejmują flag zainfekowanych urządzeń, sieci zachowywanie anonimowości i nietypowe lub mało prawdopodobne lokalizacji.

Wiele z tych raportów i zdarzeń są już dostępne za pośrednictwem interfejsu API w celu integracji z usługą security information and event management (SIEM) systemów.

### <a name="remote-access-as-a-service"></a>Dostęp zdalny w trybie usługi

Nie trzeba martwić się o zachowaniu i instalowanie poprawek na serwerach lokalnych.

Nadal oprogramowania bez konta dla dużej liczby ataków. Serwer Proxy aplikacji usługi Azure AD jest usługą skali Internetu, która jest właścicielem firmy Microsoft, dzięki czemu zawsze uzyskać najnowsze poprawki zabezpieczeń i uaktualnień.

Aby zwiększyć bezpieczeństwo aplikacji opublikowanych przez serwer Proxy aplikacji usługi Azure AD, możemy zablokować robotów przeszukiwarkę sieci web do indeksowania i archiwizowania aplikacji. Każdorazowo robota przeszukiwarkę sieci web próbuje pobrać ustawień robota dla opublikowanej aplikacji serwera Proxy aplikacji odpowiada z plikiem robots.txt, która obejmuje `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Zapobieganie przed atakami DDOS

Aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji są chronione przed atakami rozproszonej typu "odmowa" dla usługi (DDOS).

Usługa serwera Proxy aplikacji monitoruje ilość ruchu, które chcesz połączyć swoje aplikacje i sieć. Jeśli gwałtowne wzrosty liczby urządzeń żądania dostępu zdalnego do aplikacji, Microsoft ogranicza dostęp do sieci. 

Microsoft obserwuje wzorców ruchu dla poszczególnych aplikacji i dla Twojej subskrypcji, jako całości. Jeśli jedna aplikacja odbiera wyższa niż normalna żądań, żądań dostępu do tej aplikacji są odrzucane przez krótki okres czasu. Jeśli pojawi się wyższa niż normalna żądań w ramach całej subskrypcji, wnioski o udostępnienie wszelkie aplikacje są odrzucane. Ta miara zapobiegawczych przechowuje serwerów aplikacji z on przeciążony przez żądania dostępu zdalnego, aby użytkowników lokalnych można zachować dostęp do swoich aplikacji. 

## <a name="under-the-hood"></a>Kulisy

Serwer Proxy aplikacji usługi AD Azure składa się z dwóch części:

* Usługa oparta na chmurze: Ta usługa działa na platformie Azure i to, gdzie zostały wprowadzone połączeń klienta/użytkownika zewnętrznego.
* [Lokalny łącznik](application-proxy-connectors.md): Komponent środowiska lokalnego łącznika nasłuchuje żądań z serwera Proxy aplikacji usługi Azure AD service i obsługuje połączenia wewnętrzne aplikacje. 

Przepływ między łącznika i usługę serwera Proxy aplikacji jest ustanawiane po:

* Najpierw skonfigurowano łącznik.
* Łącznik ściąga informacje o konfiguracji z serwera Proxy aplikacji usługi.
* Użytkownik uzyskuje dostęp do opublikowanej aplikacji.

>[!NOTE]
>Cała komunikacja odbywa się za pośrednictwem protokołu SSL i zawsze pochodzą one na łącznik do usługi serwera Proxy aplikacji. Usługa jest tylko ruchu wychodzącego.

Łącznik używa certyfikatu klienta do uwierzytelniania usługi Serwer Proxy aplikacji dla niemal wszystkich wywołań. Jedynym wyjątkiem od tego procesu jest kroku konfiguracji początkowej, której certyfikat klienta zostanie nawiązane.

### <a name="installing-the-connector"></a>Instalowanie łącznika

Jeśli najpierw skonfigurowano łącznik następujących zdarzeń przepływu wykonane:

1. Rejestracja łącznika z usługą odbywa się w ramach instalacji łącznika. Użytkownicy są monitowani o podanie poświadczeń administratora usługi Azure AD. Token uzyskanych z tego uwierzytelnienia zostanie przedstawiony w usłudze usługi serwera Proxy aplikacji usługi Azure AD.
2. Usługa serwera Proxy aplikacji oblicza token. Sprawdza, czy użytkownik jest administratorem firmy w dzierżawie. Jeśli użytkownik nie jest administratorem, proces zostanie zakończony.
3. Łącznik generuje żądanie certyfikatu klienta i przekazuje je, wraz z tokenem z usługą serwera Proxy aplikacji. Usługa z kolei weryfikuje token i podpisuje żądanie certyfikatu klienta.
4. Łącznik używa certyfikatu klienta dla przyszłych komunikuje się z usługą serwera Proxy aplikacji.
5. Łącznik wykonuje początkowej ściągania danych konfiguracji systemu z tą usługą przy użyciu swojego certyfikatu klienta, i jest teraz gotowy do żądania.

### <a name="updating-the-configuration-settings"></a>Trwa aktualizowanie ustawień konfiguracji

Zawsze, gdy usługa serwera Proxy aplikacji do aktualizacji ustawień konfiguracji, następujące zdarzenia przepływu wykonane:

1. Łącznik łączy do konfiguracji punktu końcowego w ramach usługi Serwer Proxy aplikacji za pomocą swojego certyfikatu klienta.
2. Po zweryfikowaniu certyfikat klienta, usługa serwera Proxy aplikacji zwraca dane konfiguracji do łącznika (na przykład grupa łącznika, łącznik powinien być częścią).
3. Jeśli bieżący certyfikat jest ponad 180 dni, łącznik generuje żądania nowego certyfikatu, który skutecznie aktualizuje certyfikat klienta na 180 dni.

### <a name="accessing-published-applications"></a>Uzyskiwanie dostępu do opublikowanych aplikacji

Gdy użytkownicy uzyskują dostęp do opublikowanej aplikacji, następujące zdarzenia miejsce między usługą Serwer Proxy aplikacji i łącznik serwera Proxy aplikacji:

1. Usługa uwierzytelnia użytkownika dla aplikacji
2. Usługa stosuje żądania w kolejce łącznika
3. Łącznik przetwarza żądania z kolejki
4. Łącznik czeka na odpowiedź
5. Usługa strumieni danych użytkownika

Aby dowiedzieć się, co ma miejsce w każdym z tych kroków, zachować odczytu.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Usługa uwierzytelnia użytkownika dla aplikacji

Jeśli skonfigurowano aplikację do używania przekazywanie jako metody uwierzytelniania wstępnego w krokach w tej sekcji są pomijane.

Jeśli skonfigurowano aplikację, aby preauthenticate z usługą Azure AD, użytkownicy są przekierowywani do platformy Azure tokenu Zabezpieczającego usług AD do uwierzytelniania i wykonane następujące czynności:

1. Serwer Proxy aplikacji sprawdza, czy wszystkie wymagania dotyczące zasad dostępu warunkowego dla określonej aplikacji. Ten krok zapewnia, że użytkownik został przypisany do aplikacji. Jeśli wymagana jest weryfikacja dwuetapowa, sekwencji uwierzytelniania monit dla drugiej metody uwierzytelniania.

2. Po upływie wszystkie testy usługi Azure AD, Usługa STS wystawia podpisany token dla aplikacji i przekierowuje użytkownika z powrotem do usługi serwera Proxy aplikacji.

3. Serwer Proxy aplikacji weryfikuje, czy token został wystawiony do właściwej aplikacji. Inne procesy kontrolne, wykonuje też, takie jak zagwarantowanie, że token został podpisany przez usługę Azure AD i jest nadal mieszczą się w oknie prawidłowe.

4. Ustawia serwer Proxy aplikacji, pliku cookie uwierzytelniania szyfrowanego, aby wskazać, że uwierzytelnianie w aplikacji wystąpił. Plik cookie zawiera znacznik czasu wygaśnięcia, oparty na token z usługi Azure AD i innych danych, takie jak nazwa użytkownika, na podstawie uwierzytelniania. Plik cookie jest zaszyfrowany przy użyciu klucza prywatnego, znanego tylko usługę Serwer Proxy aplikacji.

5. Serwer Proxy aplikacji przekierowuje użytkownika do pierwotnie żądanego adresu URL.

Jeśli jakakolwiek część kroki wstępnego uwierzytelniania nie powiedzie się, użytkownika żądanie zostanie odrzucone, a użytkownika jest wyświetlany komunikat informujący o źródła problemu.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Usługa stosuje żądania w kolejce łącznika

Łączniki nie zamykaj połączenie wychodzące do usługi serwera Proxy aplikacji. Gdy nadejdzie żądanie, usługa kolejkuje żądanie na jednym z otwartych połączeń dla łącznika do pobrania.

Żądanie zawiera elementy z aplikacji, takich jak nagłówki żądania danych z zaszyfrowanego pliku cookie użytkownika zgłaszającego żądanie i identyfikator żądania. Mimo że danych z zaszyfrowanego pliku cookie są wysyłane z tym żądaniem, nie jest samego pliku cookie uwierzytelniania.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Łącznik przetwarza żądania z kolejki. 

Na podstawie żądania, serwer Proxy aplikacji wykonuje jedną z następujących czynności:

* Jeśli żądanie jest prostą operacją (na przykład, nie ma żadnych danych w treści, ponieważ jest zgodne ze specyfikacją REST *UZYSKAĆ* żądania), łącznik nawiązuje połączenie wewnętrzne zasobu docelowego, a następnie czeka na odpowiedź.

* Jeśli żądanie ma danych skojarzonych z nim w treści (na przykład zgodne ze specyfikacją REST *WPIS* operacji), łącznik sprawia, że połączenie wychodzące przy użyciu certyfikatu klienta do wystąpienia serwera Proxy aplikacji. Ułatwia to połączenie z żądania danych i nawiązać połączenie z zasobu wewnętrznego. Po otrzymaniu żądania z łącznika usługi Serwer Proxy aplikacji rozpoczyna się akceptowanie w treści podane przez użytkownika i przekazuje dane do łącznika. Łącznik z kolei przekazuje dane do zasobów wewnętrznych.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Łącznik czeka na odpowiedź.

Po ukończeniu żądania i przekazania całej zawartości z zapleczem łącznik czeka na odpowiedź.

Po otrzymaniu odpowiedzi, łącznik sprawia, że połączenie wychodzące do usługi serwera Proxy aplikacji, zwracają szczegółowe informacje o nagłówku i rozpocząć przesyłanie strumieniowe danych zwrotnych.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Usługa strumieni danych użytkownika. 

Część przetwarzania aplikacji, może wystąpić w tym miejscu. Jeśli skonfigurowano serwer Proxy aplikacji do translacji nagłówków lub adresów URL w aplikacji, przetworzenia odbywa się zgodnie z potrzebami, w tym kroku.


## <a name="next-steps"></a>Kolejne kroki

[Zagadnienia dotyczące topologii sieci przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-network-topology.md)

[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
