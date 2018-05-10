---
title: Zagadnienia dotyczące zabezpieczeń dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Obejmuje zagadnienia dotyczące zabezpieczeń dla przy użyciu serwera Proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7f12d34218f880a6f8ad0c8f4de717868e0d89d1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Zagadnienia dotyczące zabezpieczeń w celu uzyskania dostępu do aplikacji zdalnie z serwera Proxy aplikacji usługi Azure AD

W tym artykule opisano składniki, które współpracują, aby zabezpieczyć użytkowników i aplikacje użycia serwera Proxy usługi Azure Active Directory aplikacji.

Na poniższym diagramie przedstawiono sposób usługi Azure AD zapewnia bezpieczny dostęp zdalny do aplikacji lokalnych.

 ![Diagram bezpieczny dostęp zdalny za pośrednictwem serwera Proxy aplikacji usługi Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>Korzyści w zakresie zabezpieczeń

Serwer Proxy aplikacji usługi Azure AD zapewnia następujące korzyści zabezpieczeń:

### <a name="authenticated-access"></a>Uwierzytelniony dostęp 

Jeśli chcesz użyć usługi Azure Active Directory wstępnego uwierzytelniania, następnie tylko uwierzytelnionego połączenia można uzyskać dostęp do sieci.

Serwer Proxy aplikacji usługi Azure AD zależy od usługi Azure AD usługi tokenu zabezpieczającego (STS) do wszystkich uwierzytelniania.  Wstępnego uwierzytelniania, ze swej natury blokuje znaczących anonimowe ataki, ponieważ tylko uwierzytelnionych tożsamości można uzyskać dostępu do aplikacji zaplecza.

Jeśli wybierzesz Passthrough jako metody uwierzytelniania wstępnego nie pobieraj takich korzyści. 

### <a name="conditional-access"></a>Dostęp warunkowy

Bardziej zaawansowane funkcje kontroli zasad należy zastosować przed ustanowiono połączenia z siecią.

Z [dostępu warunkowego](active-directory-conditional-access-azure-portal-get-started.md), można definiować ograniczenia, w jaki ruch jest dozwolony dostęp do Twojej aplikacji zaplecza. Można utworzyć zasady, które ograniczają logowania na podstawie lokalizacji, siły uwierzytelniania i profil użytkownika ryzyka.

Dostęp warunkowy umożliwia również konfigurowanie zasad uwierzytelniania wieloskładnikowego, dodawanie dodatkową warstwę zabezpieczeń do użytkownika uwierzytelnienia użytkownika. 

### <a name="traffic-termination"></a>Zakończenie ruchu

Cały ruch zakończeniem w chmurze.

Ponieważ serwer Proxy aplikacji usługi Azure AD jest wstecznego serwera proxy, cały ruch do zaplecza aplikacji jest kończona na usługę. Sesja może pobrać przywróciła tylko na serwerze zaplecza, co oznacza, że serwerów zaplecza nie są widoczne dla ruchu HTTP na bezpośrednie. Ta konfiguracja oznacza lepszej ochrony przed atakami ukierunkowanymi.

### <a name="all-access-is-outbound"></a>Dostęp jest wychodzący 

Nie trzeba otworzyć połączenia przychodzące do sieci firmowej.

Łączniki serwera Proxy aplikacji należy używać tylko na połączenia wychodzące do usługi serwera Proxy aplikacji usługi Azure AD, co oznacza, że nie istnieje potrzeba aby otworzyć porty zapory dla połączeń przychodzących. Serwery proxy tradycyjnych wymagane sieci obwodowej (znanej także jako *DMZ*, *strefą zdemilitaryzowaną*, lub *podsiecią ekranowaną*) i zezwolenie na dostęp do nieuwierzytelnione połączenia na brzegu sieci. W tym scenariuszu wymagane inwestycje w produktach zapory aplikacji sieci web do analizowania ruchu i ochrony środowiska. Przy użyciu serwera Proxy aplikacji nie wymagają sieci obwodowej, ponieważ wszystkie połączenia są wychodzących i odbywać za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji na temat łączników, zobacz [łączniki serwera Proxy aplikacji usługi AD zrozumieć Azure](application-proxy-understand-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analizy skali chmury i uczenia maszynowego 

Pobierz najnowocześniejsze zabezpieczenia.

Ponieważ jest częścią usługi Azure Active Directory, mogą korzystać z serwera Proxy aplikacji [Azure AD Identity Protection](active-directory-identityprotection.md), przy użyciu danych z programu Microsoft Security Response Center i jednostki. Razem możemy aktywne identyfikowanie złamany kont i zapewniają ochronę z logowania o wysokim ryzyku. Firma Microsoft uwzględniać wiele czynników, aby ustalić, które attemps logowania są wysokiego ryzyka. Czynniki te obejmują flagowania zainfekowanych urządzeń, nadanie sieci i lokalizacje nietypowe lub mało prawdopodobne.

Wiele z tych raportów i zdarzenia są już dostępne za pośrednictwem interfejsu API dla integracji z informacji o zabezpieczeniach i systemami zarządzania (SIEM) zdarzenia.

### <a name="remote-access-as-a-service"></a>Dostęp zdalny w trybie usługi

Nie trzeba martwić się o zachowaniu i stosowanie poprawek serwerów lokalnych.

Konta nadal atakowany oprogramowania dla dużej liczby ataków. Serwer Proxy aplikacji usługi Azure AD jest usługi Internet skalowania, która jest właścicielem firmy Microsoft, dzięki czemu zawsze uzyskać najnowsze poprawki zabezpieczeń i aktualizacje.

Aby zwiększyć bezpieczeństwo aplikacji opublikowanych przez serwer Proxy aplikacji usługi Azure AD, firma Microsoft zablokować robotów przez przeszukiwarkę sieci web do indeksowania i archiwizowania aplikacji. Zawsze robota przez przeszukiwarkę sieci web próbuje pobrać ustawienia robota opublikowanej aplikacji, serwer Proxy aplikacji odpowiedzi z plikiem robots.txt, która obejmuje `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Zapobieganie DDOS

Aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji są chronione przed atakami rozproszonych odmowa programu Service (DDOS).

Serwer Proxy aplikacji monitoruje ilość ruchu sieciowego chcesz połączyć aplikacje i sieci. Jeśli liczba urządzeń żąda zdalnego dostępu do aplikacji wzrósł, Microsoft ogranicza dostęp do sieci. 

Microsoft Obserwujący wzorce ruchu sieciowego dla poszczególnych aplikacji, jak i dla całej subskrypcji. Jeśli jedna aplikacja odbiera wyższe niż normalne żądania, żądania dostępu do tej aplikacji nie będzie mógł krótki okres czasu. Jeśli pojawi się wyższe niż normalne żądań w całej subskrypcji, żądania ma dostęp do wszystkich aplikacji są odrzucane. Tych środków zapobiegawczych zachowuje serwerów aplikacji przeciążaniu przez żądania dostępu zdalnego, dzięki czemu użytkowników lokalnych można zachować dostęp do swoich aplikacji. 

## <a name="under-the-hood"></a>Kulisy

Serwer Proxy aplikacji usługi Azure AD składa się z dwóch części:

* Usługa oparta na chmurze: ta usługa działa na platformie Azure i jest, w których są wykonane połączeń zewnętrznych klienta/użytkownika.
* [Łącznik lokalnego](application-proxy-understand-connectors.md): składnik lokalnego łącznika nasłuchuje żądań z serwera Proxy aplikacji usługi Azure AD połączenia usługi i dojść do wewnętrznych aplikacji. 

Przepływ między łącznika i usługę serwera Proxy aplikacji jest ustanawiane po:

* Najpierw skonfigurowano łącznik.
* Łącznik pobiera informacje o konfiguracji z serwera Proxy aplikacji usługi.
* Użytkownik uzyskuje dostęp do opublikowanej aplikacji.

>[!NOTE]
>Cała komunikacja odbywa się za pośrednictwem protokołu SSL, a pochodzą zawsze na łącznika serwera Proxy aplikacji usługi. Usługa jest tylko ruchu wychodzącego.

Łącznik korzysta z certyfikatu klienta do uwierzytelniania serwera Proxy aplikacji usługi dla prawie wszystkich wywołań. Jedynym wyjątkiem od tego procesu jest kroku konfiguracji początkowej, gdy zostanie nawiązane certyfikatu klienta.

### <a name="installing-the-connector"></a>Instalowanie łącznika

Jeśli najpierw skonfigurowano łącznik następujące zdarzenia przepływu miejsce:

1. Rejestracja łącznika z usługą odbywa się w ramach instalacji łącznika. Użytkownicy są monitowani o wprowadzenie poświadczeń administratora usługi Azure AD. Token z tego uwierzytelnienia zostanie przedstawiony w usłudze usługi serwera Proxy aplikacji usługi Azure AD.
2. Usługa Serwer Proxy aplikacji oblicza tokenu. Sprawdza, czy użytkownik jest administratorem przedsiębiorstwa w dzierżawie. Jeśli użytkownik nie jest administratorem, proces zostanie zakończony.
3. Łącznik generuje żądanie certyfikatu klienta i przekazuje je, wraz z token do serwera Proxy aplikacji usługi. Usługa z kolei weryfikuje token i podpisuje żądanie certyfikatu klienta.
4. Łącznik korzysta z certyfikatu klienta dla przyszłych komunikacji z usługą serwera Proxy aplikacji.
5. Łącznik wykonuje początkowej ściągania danych konfiguracji systemu przez usługę za pomocą swojego certyfikatu klienta, a jest teraz gotowa do sporządzenia żądania.

### <a name="updating-the-configuration-settings"></a>Aktualizowanie ustawień konfiguracji

Zawsze, gdy usługa serwera Proxy aplikacji aktualizuje ustawienia konfiguracji, następujące zdarzenia przepływu miejsce:

1. Łącznik nawiązuje połączenie z punktem końcowym konfiguracji w ramach usługi Serwer Proxy aplikacji za pomocą swojego certyfikatu klienta.
2. Po zweryfikowaniu certyfikatu klienta usługi Serwer Proxy aplikacji zwraca dane konfiguracji łącznika (na przykład łącznik grupą, których łącznik powinien być częścią).
3. Jeśli bieżący certyfikat jest więcej niż 180 dni, łącznik generuje żądania nowego certyfikatu, które skutecznie aktualizuje certyfikat klienta na 180 dni.

### <a name="accessing-published-applications"></a>Uzyskiwanie dostępu do opublikowanych aplikacji

Gdy użytkownicy uzyskują dostęp do opublikowanej aplikacji, następujące zdarzenia miejsce między usługą Serwer Proxy aplikacji i łącznika serwera Proxy aplikacji:

1. [Usługa uwierzytelnia użytkownika dla aplikacji](#the-service-checks-the-configuration-settings-for-the-app)
2. [Usługa umieszcza żądania w kolejce łącznika](#The-service-places-a-request-in-the-connector-queue)
3. [Łącznik przetworzy żądanie z kolejki](#the-connector-receives-the-request-from-the-queue)
4. [Łącznik czeka na odpowiedź](#the-connector-waits-for-a-response)
5. [Usługa strumieni danych do użytkownika](#the-service-streams-data-to-the-user)

Aby dowiedzieć się więcej na temat co ma miejsce w każdej z tych kroków, Zachowaj odczytu.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Usługa uwierzytelnia użytkownika dla aplikacji

Jeśli skonfigurowano aplikację do używania Passthrough jako metody uwierzytelniania wstępnego, są pomijane kroki opisane w tej sekcji.

Po skonfigurowaniu aplikacji preauthenticate z usługą Azure AD, użytkownicy są przekierowywani do usługi STS Azure AD do uwierzytelniania i przeprowadzać następujące kroki:

1. Serwer Proxy aplikacji sprawdza, czy wszystkie wymagania dotyczące zasad dostępu warunkowego dla określonej aplikacji. Ten krok zapewnia, że użytkownik został przypisany do aplikacji. Jeśli wymagana jest weryfikacja dwuetapowa, sekwencji uwierzytelniania monitu o drugiej metody uwierzytelniania.

2. Po upływie wszystkie testy, programu Azure AD STS wystawia podpisany token aplikacji i przekierowuje użytkownika z powrotem do serwera Proxy aplikacji usługi.

3. Serwer Proxy aplikacji sprawdza, czy token został wystawiony dla Popraw aplikacji. Inne kontrole wykonuje również, takich jak sprawdzeniu, czy token został podpisany przez usługę Azure AD i jest nadal w oknie prawidłowe.

4. Ustawia serwera Proxy aplikacji wystąpił pliku cookie uwierzytelniania szyfrowanego, aby wskazać, że do uwierzytelniania do aplikacji. Plik cookie zawiera znacznik czasu wygaśnięcia, oparty na token z usługi Azure AD i innych danych, takie jak nazwa użytkownika, na podstawie uwierzytelniania. Plik cookie jest zaszyfrowany przy użyciu klucza prywatnego znany tylko usługi Serwer Proxy aplikacji.

5. Serwer Proxy aplikacji przekierowuje użytkownika do pierwotnie żądanego adresu URL.

W przypadku niepowodzenia dowolnej części kroki wstępnego uwierzytelniania użytkownika, żądanie zostanie odrzucone i użytkownika jest wyświetlany komunikat informujący o źródła problemu.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Usługa umieszcza żądania w kolejce łącznika

Łączniki nie zamykaj wychodzące połączenie z usługą serwera Proxy aplikacji. Gdy nadejdzie żądanie, usługa kolejkuje żądanie na jednym z otwartych połączeń dla łącznika do pobrania.

Żądanie zawiera elementy z aplikacji, takich jak nagłówki żądania, danych z zaszyfrowanego pliku cookie użytkownika, dzięki czemu żądania i identyfikator żądania. Mimo że wysłaniu danych z zaszyfrowanego pliku cookie z żądaniem nie jest samego pliku cookie uwierzytelniania.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Łącznik przetworzy żądanie z kolejki. 

Na podstawie żądania, serwer Proxy aplikacji wykonuje jeden z następujących czynności:

* Jeśli żądanie jest prostą operacją (na przykład, Brak danych w treści, ponieważ jest z RESTful *UZYSKAĆ* żądania), łącznik nawiązuje połączenie wewnętrzne zasobu docelowego i czeka na odpowiedź.

* Jeśli żądanie ma danych skojarzonych z nim w treści (na przykład RESTful *POST* operacji), łącznik nawiązuje wychodzące połączenie przy użyciu certyfikatu klienta do wystąpienia serwera Proxy aplikacji. Powoduje to połączenie żądania danych i Otwórz połączenie do zasobów wewnętrznych. Po otrzymaniu żądania z łącznika usługi Serwer Proxy aplikacji rozpoczyna się akceptowanie zawartości od użytkownika i przekazuje dane do łącznika. Łącznik, z kolei przesyła dane do zasobów wewnętrznych.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Łącznik czeka na odpowiedź.

Po zakończeniu żądań i przekazywanie całej zawartości wewnętrznej łącznik czeka na odpowiedź.

Po otrzymaniu odpowiedzi łącznika sprawia, że wychodzące połączenie z usługą serwera Proxy aplikacji, aby powrócić szczegółów nagłówka i rozpocząć przesyłanie strumieniowe danych zwrotnych.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Usługa strumieni danych do użytkownika. 

Przetwarza aplikacji może występować w tym miejscu. Jeśli skonfigurowano serwer Proxy aplikacji do tłumaczenia nagłówków lub adresy URL w aplikacji, przetwarzanie odbywa się zgodnie z potrzebami w tym kroku.


## <a name="next-steps"></a>Kolejne kroki

[Zagadnienia dotyczące topologii sieci przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-network-topology-considerations.md)

[Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-understand-connectors.md)
