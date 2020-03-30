---
title: Zagadnienia dotyczące zabezpieczeń serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Obejmuje zagadnienia dotyczące zabezpieczeń dotyczące korzystania z serwera proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481232"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Zagadnienia dotyczące zabezpieczeń dotyczące zdalnego uzyskiwania dostępu do aplikacji za pomocą serwera proxy aplikacji usługi Azure AD

W tym artykule opisano składniki, które działają w celu zachowania bezpieczeństwa użytkowników i aplikacji podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory.

Na poniższym diagramie pokazano, jak usługa Azure AD umożliwia bezpieczny dostęp zdalny do aplikacji lokalnych.

 ![Diagram bezpiecznego dostępu zdalnego za pośrednictwem serwera proxy aplikacji usługi Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Korzyści z bezpieczeństwa

Usługa Azure AD Application Proxy oferuje następujące korzyści w zakresie zabezpieczeń:

### <a name="authenticated-access"></a>Uwierzytelniony dostęp 

Jeśli zdecydujesz się użyć preauthentication usługi Azure Active Directory, a następnie tylko uwierzytelnione połączenia mogą uzyskać dostęp do sieci.

Serwer proxy aplikacji usługi Azure AD opiera się na usłudze tokenu zabezpieczeń usługi Azure AD (STS) dla wszystkich uwierzytelniania.  Preauthentication, ze swej natury, blokuje znaczną liczbę ataków anonimowych, ponieważ tylko uwierzytelnione tożsamości mogą uzyskać dostęp do aplikacji zaplecza.

Jeśli wybierzesz Passthrough jako metodę preauthentication, nie otrzymasz tej korzyści. 

### <a name="conditional-access"></a>Dostęp warunkowy

Stosowanie bogatszych formantów zasad przed nawiązaniem połączeń z siecią.

Za pomocą [dostępu warunkowego](../conditional-access/overview.md)można zdefiniować ograniczenia dotyczące ruchu, który może uzyskać dostęp do aplikacji zaplecza. Można tworzyć zasady, które ograniczają logowania na podstawie lokalizacji, siły uwierzytelniania i profilu ryzyka użytkownika.

Za pomocą dostępu warunkowego można również skonfigurować zasady uwierzytelniania wieloskładnikowego, dodając kolejną warstwę zabezpieczeń do uwierzytelniania użytkowników. Ponadto aplikacje mogą być również kierowane do usługi Microsoft Cloud App Security za pośrednictwem usługi Azure AD Dostęp warunkowy w celu zapewnienia monitorowania i kontroli w czasie rzeczywistym, za pośrednictwem zasad [dostępu](https://docs.microsoft.com/cloud-app-security/access-policy-aad) i [sesji](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Zakończenie ruchu

Cały ruch jest zakończony w chmurze.

Ponieważ serwer proxy aplikacji usługi Azure AD jest serwerem proxy odwrotnym, cały ruch do aplikacji zaplecza jest zakończony w usłudze. Sesja może zostać przywrócona tylko za pomocą serwera zaplecza, co oznacza, że serwery zaplecza nie są narażone na bezpośredni ruch HTTP. Ta konfiguracja oznacza, że są lepiej chronione przed atakami ukierunkowanymi.

### <a name="all-access-is-outbound"></a>Cały dostęp jest wychodzący 

Nie trzeba otwierać połączeń przychodzących z siecią firmową.

Łączniki serwera proxy aplikacji używają tylko połączeń wychodzących z usługą serwera proxy aplikacji usługi Azure AD, co oznacza, że nie ma potrzeby otwierania portów zapory dla połączeń przychodzących. Tradycyjne serwery proxy wymagały sieci obwodowej (znanej również jako *DMZ,* *strefa zdemilitaryzowana*lub *podsieci ekranowanej)* i zezwoliły na dostęp do nieuwierzytezowanych połączeń na krawędzi sieci. W tym scenariuszu wymagane inwestycje w produkty zapory aplikacji sieci web do analizowania ruchu i ochrony środowiska. Za pomocą serwera proxy aplikacji nie potrzebujesz sieci obwodowej, ponieważ wszystkie połączenia są wychodzące i odbywają się za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji na temat łączników, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analityka w skali chmury i uczenie maszynowe 

Uzyskaj najnowocześniejszą ochronę bezpieczeństwa.

Ponieważ jest częścią usługi Azure Active Directory, serwer proxy aplikacji może korzystać z [usługi Azure AD Identity Protection](../active-directory-identityprotection.md), z danymi z Centrum reagowania na zabezpieczenia firmy Microsoft i jednostki przestępstw cyfrowych. Wspólnie aktywnie identyfikujemy konta, których bezpieczeństwo zostało naruszone i zapewniamy ochronę przed logowaniami wysokiego ryzyka. Bierzemy pod uwagę wiele czynników, aby określić, które próby logowania są wysokie ryzyko. Czynniki te obejmują oznaczanie zainfekowanych urządzeń, anonimizację sieci oraz nietypowe lub mało prawdopodobne lokalizacje.

Wiele z tych raportów i zdarzeń są już dostępne za pośrednictwem interfejsu API do integracji z informacjami o zabezpieczeniach i systemów zarządzania zdarzeniami (SIEM).

### <a name="remote-access-as-a-service"></a>Dostęp zdalny jako usługa

Nie musisz się martwić o utrzymywanie i poprawianie serwerów lokalnych.

Niezałatane oprogramowanie nadal stanowi dużą liczbę ataków. Usługa Azure AD Application Proxy to usługa w skali internetowej, której właścicielem jest firma Microsoft, dzięki czemu zawsze można uzyskać najnowsze poprawki zabezpieczeń i uaktualnienia.

Aby zwiększyć bezpieczeństwo aplikacji publikowanych przez usługę Azure AD Application Proxy, blokujemy roboty robotów robotów indeksowania sieci web przed indeksowania i archiwizowania aplikacji. Za każdym razem, gdy robot robota przeszukiwania sieci Web próbuje pobrać ustawienia robota dla opublikowanej `User-agent: * Disallow: /`aplikacji, pełnomocnik aplikacji odpowiada plikiem robots.txt, który zawiera .

#### <a name="azure-ddos-protection-service"></a>Usługa ochrony DDoS platformy Azure

Aplikacje publikowane za pośrednictwem serwera proxy aplikacji są chronione przed atakami DDoS (Distributed Denial of Service). **Ochrona DDoS platformy Azure** to usługa oferowana za pomocą platformy Azure w celu ochrony zasobów platformy Azure przed atakami typu "odmowa usługi". Warstwa usług **Basic** jest automatycznie włączona, zapewniając zawsze włączone monitorowanie ruchu i łagodzenie typowych ataków na poziomie sieci w czasie rzeczywistym. Warstwa **standardowa** jest również dostępna, oferując dodatkowe możliwości ograniczania ryzyka, które są dostrojone specjalnie do zasobów sieci wirtualnej platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Omówienie standardu ochrony przed atakami DDoS platformy Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

## <a name="under-the-hood"></a>Kulisy

Usługa Azure AD Application Proxy składa się z dwóch części:

* Usługa oparta na chmurze: Ta usługa jest uruchamiana na platformie Azure i jest miejscem, w którym są nawiązywają połączenia klienta/użytkownika zewnętrznego.
* [Łącznik lokalny:](application-proxy-connectors.md)składnik lokalny, łącznik nasłuchuje żądań z usługi Azure APPLICATION Application Proxy i obsługuje połączenia z aplikacjami wewnętrznymi. 

Przepływ między łącznikiem a usługą proxy aplikacji jest ustanawiany, gdy:

* Łącznik jest najpierw skonfigurowany.
* Łącznik pobiera informacje o konfiguracji z usługi Proxy aplikacji.
* Użytkownik uzyskuje dostęp do opublikowanej aplikacji.

>[!NOTE]
>Wszystkie komunikaty występują za 100 000 000 000 000 000 000 000 000 000 000 000 000 000 000 Usługa jest tylko wychodząca.

Łącznik używa certyfikatu klienta do uwierzytelniania w usłudze proxy aplikacji dla prawie wszystkich wywołań. Jedynym wyjątkiem od tego procesu jest początkowy krok konfiguracji, w którym ustanowiono certyfikat klienta.

### <a name="installing-the-connector"></a>Instalowanie złącza

Podczas pierwszej konfiguracji łącznika odbywają się następujące zdarzenia przepływu:

1. Rejestracja łącznika do usługi odbywa się w ramach instalacji łącznika. Użytkownicy są monitowani o wprowadzenie poświadczeń administratora usługi Azure AD.Token uzyskany z tego uwierzytelniania jest następnie prezentowany w usłudze serwera proxy aplikacji usługi Azure AD.
2. Usługa serwera proxy aplikacji ocenia token. Sprawdza, czy użytkownik jest administratorem firmy w dzierżawie.Jeśli użytkownik nie jest administratorem, proces zostanie zakończony.
3. Łącznik generuje żądanie certyfikatu klienta i przekazuje go wraz z tokenem do usługi proxy aplikacji. Usługa z kolei weryfikuje token i podpisuje żądanie certyfikatu klienta.
4. Łącznik używa certyfikatu klienta do przyszłej komunikacji z usługą proxy aplikacji.
5. Łącznik wykonuje początkowe ściąganie danych konfiguracji systemu z usługi przy użyciu certyfikatu klienta i jest teraz gotowy do żądania.

### <a name="updating-the-configuration-settings"></a>Aktualizowanie ustawień konfiguracyjnych

Za każdym razem, gdy usługa proxy aplikacji aktualizuje ustawienia konfiguracji, odbywają się następujące zdarzenia przepływu:

1. Łącznik łączy się z punktem końcowym konfiguracji w usłudze Proxy aplikacji przy użyciu certyfikatu klienta.
2. Po sprawdzeniu poprawności certyfikatu klienta usługa proxy aplikacji zwraca dane konfiguracyjne do łącznika (na przykład grupy łączników, do których łącznik powinien być częścią).
3. Jeśli bieżący certyfikat ma więcej niż 180 dni, łącznik generuje nowe żądanie certyfikatu, które skutecznie aktualizuje certyfikat klienta co 180 dni.

### <a name="accessing-published-applications"></a>Uzyskiwanie dostępu do opublikowanych aplikacji

Gdy użytkownicy uzyskują dostęp do opublikowanej aplikacji, między usługą proxy aplikacji a łącznikiem serwera proxy aplikacji mają miejsce następujące zdarzenia:

1. Usługa uwierzytelnia użytkownika dla aplikacji
2. Usługa umieszcza żądanie w kolejce łącznika
3. Łącznik przetwarza żądanie z kolejki
4. Łącznik czeka na odpowiedź
5. Usługa przesyła dane do użytkownika

Aby dowiedzieć się więcej o tym, co dzieje się w każdym z tych kroków, czytaj dalej.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. Usługa uwierzytelnia użytkownika dla aplikacji

Jeśli aplikacja została skonfigurowana do używania passthrough jako metody wstępnej uwierzytelniania, kroki opisane w tej sekcji zostaną pominięte.

Jeśli aplikacja została skonfigurowana do wstępnego uwierzenia za pomocą usługi Azure AD, użytkownicy są przekierowywani do usługi Azure AD STS w celu uwierzytelnienia i zostaną wprowadzone następujące kroki:

1. Serwer proxy aplikacji sprawdza wszystkie wymagania zasad dostępu warunkowego dla określonej aplikacji. Ten krok gwarantuje, że użytkownik został przypisany do aplikacji. Jeśli wymagana jest weryfikacja dwuetapowa, sekwencja uwierzytelniania monituje użytkownika o drugą metodę uwierzytelniania.

2. Po przekazaniu wszystkich kontroli usługa Azure AD STS wystawia podpisany token dla aplikacji i przekierowuje użytkownika z powrotem do usługi proxy aplikacji.

3. Serwer proxy aplikacji sprawdza, czy token został wystawiony do poprawnej aplikacji. Wykonuje również inne kontrole, takie jak zapewnienie, że token został podpisany przez usługę Azure AD i że nadal znajduje się w prawidłowym oknie.

4. Serwer proxy aplikacji ustawia zaszyfrowany plik cookie uwierzytelniania, aby wskazać, że wystąpiło uwierzytelnienie aplikacji. Plik cookie zawiera sygnaturę czasową wygaśnięcia, która jest oparta na tokenie z usługi Azure AD i innych danych, takich jak nazwa użytkownika, na której opiera się uwierzytelnianie. Plik cookie jest szyfrowany za pomocą klucza prywatnego znanego tylko usłudze proxy aplikacji.

5. Serwer proxy aplikacji przekierowuje użytkownika z powrotem do pierwotnie żądanego adresu URL.

Jeśli którakolwiek część kroków preauthentication nie powiedzie się, żądanie użytkownika zostanie odrzucone, a użytkownik jest wyświetlany komunikat wskazujący źródło problemu.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. Usługa umieszcza żądanie w kolejce łącznika

Łączniki utrzymują połączenie wychodzące otwarte dla usługi Proxy aplikacji. Gdy pojawi się żądanie, usługa kolejkuje żądanie na jednym z otwartych połączeń dla łącznika do pobrania.

Żądanie zawiera elementy z aplikacji, takie jak nagłówki żądań, dane z zaszyfrowanego pliku cookie, użytkownik składający żądanie i identyfikator żądania. Chociaż dane z zaszyfrowanego pliku cookie są wysyłane z żądaniem, sam plik cookie uwierzytelniania nie jest.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Łącznik przetwarza żądanie z kolejki. 

Na podstawie żądania serwer proxy aplikacji wykonuje jedną z następujących akcji:

* Jeśli żądanie jest prostą operacją (na przykład nie ma żadnych danych w treści, jak w przypadku żądania RESTful *GET),* łącznik nawiązuje połączenie z docelowym zasobem wewnętrznym, a następnie czeka na odpowiedź.

* Jeśli żądanie ma dane skojarzone z nim w treści (na przykład operacji RESTful *POST),* łącznik nawiązuje połączenie wychodzące przy użyciu certyfikatu klienta do wystąpienia serwera proxy aplikacji. To sprawia, że to połączenie, aby zażądać danych i otworzyć połączenie z zasobem wewnętrznym. Po odebraniu żądania z łącznika usługa serwera proxy aplikacji rozpoczyna akceptowanie zawartości od użytkownika i przekazuje dane do łącznika. Łącznik z kolei przekazuje dane do zasobu wewnętrznego.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Łącznik czeka na odpowiedź.

Po zakończeniu żądania i transmisji całej zawartości do zaplecza łącznik czeka na odpowiedź.

Po odebraniu odpowiedzi łącznik nawiązuje połączenie wychodzące z usługą Proxy aplikacji, aby zwrócić szczegóły nagłówka i rozpocząć przesyłanie strumieniowe danych zwrotnych.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. Usługa przesyła dane do użytkownika. 

Niektóre przetwarzania aplikacji może wystąpić tutaj. Jeśli skonfigurowano serwer proxy aplikacji do tłumaczenia nagłówków lub adresów URL w aplikacji, przetwarzanie odbywa się zgodnie z potrzebami podczas tego kroku.


## <a name="next-steps"></a>Następne kroki

[Zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure AD](application-proxy-network-topology.md)

[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
