---
title: Włączanie kompleksowej usługi SSL w usłudze Azure Application Gateway
description: W tym artykule przedstawiono omówienie usługi Application Gateway obsługuje kompleksowej usługi SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ee901fdcae9717cc6d03d7653bcaacc0c32518e0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254320"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Omówienie kończenia żądań SSL i kompleksowej usługi SSL z usługą Application Gateway

Secure Sockets Layer (SSL) to technologia standardowych zabezpieczeń, do nawiązywania zaszyfrowanego połączenia między serwera sieci web i przeglądarką. Ten link gwarantuje, że wszystkie dane są przekazywane między serwerem sieci web i przeglądarek pozostają prywatne i szyfrowana. Usługa Application gateway obsługuje zarówno kończenia żądań SSL bramy, jak również kompleksowe szyfrowanie SSL.

## <a name="ssl-termination"></a>Kończenie żądań protokołu SSL

Usługa Application Gateway obsługuje przerywanie połączenia SSL na bramie, po którym ruch na ogół płynie niezaszyfrowany do serwerów zaplecza. Istnieje wiele z korzyści wynikających z wykonywania kończenia żądań SSL na bramie aplikacji:

- **Zwiększona wydajność** — największa wydajność osiągnięty podczas odszyfrowywania protokołu SSL jest początkowa uzgadniania. W celu poprawy wydajności serwera, wykonując odszyfrowywania buforuje identyfikatory sesji SSL i zarządza biletami sesji TLS. Jeśli zostanie to zrobione w usłudze application gateway, wszystkie żądania z tego samego klienta można użyć wartości z pamięci podręcznej. Jeśli to się robi na serwerach wewnętrznej bazy danych, następnie każdorazowo żądania tego klienta przejdź do innego serwera, klienta musi re‑authenticate. Korzystanie z protokołu TLS biletów może pomóc rozwiązać ten problem, ale nie są obsługiwane przez wszystkich klientów i mogą być trudne do konfigurowania i zarządzania nimi.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — SSL/TLS przetwarzania jest bardzo intensywnie z Procesora i staje się coraz bardziej intensywnie, zwiększania rozmiaru klucza. Usuwanie tę pracę z serwerów zaplecza pozwala skoncentrować się na jakie są najbardziej efektywny sposób na dostarczania zawartości.
- **Inteligentnego routingu** — odszyfrowywania ruchu, brama aplikacji ma dostęp do zawartości żądania, takie jak nagłówki, identyfikator URI i tak dalej i może użyć tych danych w celu kierowania żądań.
- **Zarządzanie certyfikatami** — certyfikaty tylko muszą zostać zakupione i zainstalowane na bramie aplikacji i nie wszystkie serwery zaplecza. Spowoduje to zapisanie czas i pieniądze.

Aby skonfigurować kończenia żądań SSL, certyfikat SSL jest wymagany do dodania do odbiornika do włączenia do wyprowadzenia klucza symetrycznego zgodnie z specyfikacją protokołu SSL bramy aplikacji. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruch wysyłany do bramy. Certyfikat SSL musi mieć format wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, wymagane przez tę bramę aplikacji z realizacją szyfrowania i odszyfrowywania ruchu.

> [!NOTE] 
>
> Usługa Application gateway nie zapewnia możliwość tworzenia nowego certyfikatu lub Wyślij żądanie certyfikatu do urzędu certyfikacji.

Dla połączenia SSL do pracy należy upewnić się, że certyfikat SSL spełnia następujące warunki:

- Bieżąca data i godzina to "Ważny od" i "Ważny do" zakres dat w certyfikacie.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Obsługiwane w przypadku kończenia żądań SSL certyfikatów

Usługa Application gateway obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji (urzędu certyfikacji): Certyfikat urzędu certyfikacji jest certyfikat wystawiony przez urząd certyfikacji (CA)
- Certyfikat z Weryfikacją (Extended weryfikacji): Certyfikat Weryfikacją to wytyczne dotyczące branży Standardowy certyfikat. Spowoduje to kolor na zielony pasek lokalizatora przeglądarki i publikowania również nazwę firmy.
- Certyfikat uniwersalny: Ten certyfikat obsługuje dowolną liczbę poddomen, na podstawie *. site.com, gdzie Twoje poddomeny spowodowałoby zastąpienie *. Nie obsługuje jednak site.com, dzięki czemu w przypadku, gdy użytkownicy uzyskują dostęp do witryny sieci Web bez wpisywania wiodących "www", certyfikat uniwersalny nie zapewnią który.
- Certyfikaty z podpisem własnym: Przeglądarki klienta nie zaufać tym certyfikatom i powiadomi użytkownika czy certyfikatu usługi wirtualnego nie jest częścią łańcuch zaufania. Certyfikaty z podpisem własnym są odpowiednie do testowania lub środowiska, w której administratorzy kontrolować klientów i bezpiecznie pominąć alerty zabezpieczeń w przeglądarce. Obciążenia produkcyjne nigdy nie należy używać certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [skonfigurować kończenia żądań SSL z usługą application gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź [ogranicza Application Gateway](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) sekcji, aby wiedzieć, maksymalna SSL certyfikatu obsługiwany rozmiar.

## <a name="end-to-end-ssl-encryption"></a>Kompleksową usługę szyfrowania SSL

Niektórzy klienci mogą nie pozwalają nieszyfrowana komunikacja z serwerami wewnętrznej bazy danych. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji brama aplikacji obsługuje kompleksowe szyfrowanie SSL.

Kompleksowa usługa SSL pozwala na bezpieczne przesyłanie zaszyfrowanych danych poufnych do zaplecza, umożliwiając jednocześnie korzystanie z funkcji równoważenia obciążenia warstwy 7, które oferuje usługa Application Gateway. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu kompleksowego trybu komunikacji SSL usługa Application Gateway kończy sesje SSL na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie SSL z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. Kompleksowa usługa SSL jest włączona konfigurując dla ustawienia protokołu [ustawienia HTTP zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) HTTPS, których są następnie stosowane do puli zaplecza.

Zasady SSL dotyczy ruchu frontonu i wewnętrznej bazy danych. We frontonie usługa Application Gateway działa jako serwer i wymusza zasady. Do wewnętrznej bazy danych usługa Application Gateway działa jako klient i wysyła informacje o protokole/szyfrowania jako preferencji podczas uzgadniania protokołu SSL.

Usługa Application gateway komunikuje się tylko z tych wystąpień zaplecza, które mają albo na liście dozwolonych certyfikatów przy użyciu bramy aplikacji lub których certyfikaty są podpisywane przez dobrze znanych urzędów certyfikacji, jeśli nazwa Pospolita certyfikatu odpowiada nazwie hosta w protokole HTTP Ustawienia zaplecza. Należą do zaufanych usług platformy Azure, takich jak aplikacje sieci web usługi aplikacji platformy Azure i usługi Azure API Management.

Jeśli certyfikaty elementów członkowskich w puli zaplecza nie są podpisane przez dobrze znanych urzędów certyfikacji, poszczególnych wystąpień w puli zaplecza przy użyciu kompleksowej usługi SSL włączone, musi być skonfigurowane przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację. Dodawanie certyfikatu gwarantuje, że bramy application gateway komunikuje się tylko ze znanych wystąpień zaplecza. W ten sposób dalszej komunikacji end-to-end.

> [!NOTE] 
>
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak aplikacje sieci web usługi aplikacji platformy Azure i usługi Azure API Management.

> [!NOTE] 
>
> Certyfikat dodany do **ustawienia HTTP zaplecza** do uwierzytelniania zaplecza serwery mogą być takie same jak w przypadku certyfikatu dodana do **odbiornika** dla kończenia żądań SSL na bramie aplikacji lub inne w przypadku zwiększone zabezpieczenia.

![Scenariusz kompleksowej usługi SSL][1]

W tym przykładzie żądania używające protokołu TLS 1.2 są kierowane do serwerów zaplecza w puli Pula1 za pomocą kompleksowej usługi SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Kompleksowa usługa SSL i lista dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty musi być na liście dozwolonych usługi application gateway, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak usługa Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Kompleksowa usługa SSL z jednostką SKU v2

Certyfikaty uwierzytelniania zostały przestarzały i zastąpiony przez zaufanych certyfikatów głównych, w ramach jednostki SKU v2 Application Gateway. Działają podobnie do uwierzytelniania certyfikatów z niewielkimi różnicami klucza:

- Certyfikaty podpisana przez dobrze znanych urzędów certyfikacji którego CN jest zgodna z nazwą hosta, w ustawieniach HTTP zaplecza nie wymagają żadnych dodatkowych czynności dla kompleksowej usługi SSL do pracy. 

   Na przykład jeśli certyfikaty wewnętrznej bazy danych są wydawane przez dobrze znanego urzędu certyfikacji i ma CN contoso.com, a pole host ustawienia http zaplecza jest również ustawiona do domeny contoso.com, następnie żadne dodatkowe kroki są wymagane. Można ustawić http zaplecza, ustawienie protokołu HTTPS, a oba kondycji badania i danych ścieżka powinna mieć włączony protokół SSL. Jeśli używasz usługi Azure App Service lub innych usług sieci web platformy Azure jako zaplecza są niejawnie zaufanych również i nie trzeba wykonywać dalszych czynności są wymagane do kompleksowej usługi SSL.
- Jeśli certyfikat z podpisem własnym lub jest podpisany przez nieznany pośredników, następnie włączyć kompleksowej usługi SSL w jednostce SKU v2 zaufany certyfikat główny musi być zdefiniowany. Usługa Application Gateway komunikuje się tylko za pomocą którego certyfikat serwera, certyfikat główny pasuje do listy zaufanych certyfikatów głównych w ustawienia http zaplecza, które są skojarzone z pulą zaplecza.
- Oprócz dopasowania certyfikatu głównego usługa Application Gateway również sprawdza, czy ustawienie określonego w ustawieniach http zaplecza hosta jest zgodny z typem nazwa pospolita (CN) prezentowany przez certyfikat SSL serwera wewnętrznej bazy danych. Podczas próby nawiązania połączenia SSL z zapleczem usługi, usługa Application Gateway Ustawia rozszerzenie oznaczaniem nazwy serwera (SNI) do hosta określona w ustawieniu protokołu http zaplecza.
- Jeśli **wybierz nazwę hosta z adresów zaplecza** jest wybierany zamiast pole Host w ustawieniach http zaplecza, a następnie nagłówka SNI ma zawsze wartość do puli zaplecza, nazwy FQDN i nazwa Pospolita certyfikatu musi być zgodna nazwy FQDN na serwerze wewnętrznej bazy danych protokołu SSL. Składowych puli zaplecza przy użyciu adresów IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny jest certyfikatu głównego zakodowany base64 z certyfikatów serwera zaplecza.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się kompleksowej usługi SSL, przejdź do [Konfigurowanie kompleksowej usługi SSL przy użyciu bramy aplikacji przy użyciu programu PowerShell](application-gateway-end-to-end-ssl-powershell.md) utworzyć bramę aplikacji korzystającą z kompleksowej usługi SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
