---
title: Włączanie end-to end SSL w usłudze Azure Application Gateway
description: Ten artykuł jest omówieniem obsługi SSL bramy aplikacji od końca do końca.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 9c4e6124acdbb35233f8e829f43d2665fd4a5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284810"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Omówienie zakończenia i zakończenia ssl ssl z bramą aplikacji

Secure Sockets Layer (SSL) to standardowa technologia zabezpieczeń umożliwiająca ustanowienie zaszyfrowanego łącza między serwerem www a przeglądarką. To łącze gwarantuje, że wszystkie dane przekazywane między serwerem sieci web a przeglądarkami pozostają prywatne i zaszyfrowane. Brama aplikacji obsługuje zarówno zakończenie SSL na bramie, jak i szyfrowanie SSL od końca do końca.

## <a name="ssl-termination"></a>Kończenie żądań protokołu SSL

Usługa Application Gateway obsługuje przerywanie połączenia SSL na bramie, po którym ruch na ogół płynie niezaszyfrowany do serwerów zaplecza. Istnieje wiele zalet wykonywania zakończenia SSL na bramie aplikacji:

- **Zwiększona wydajność** — największą wydajnością podczas odszyfrowywania SSL jest początkowy uścisk dłoni. Aby zwiększyć wydajność, serwer wykonujący odszyfrowywanie buforuje identyfikatory sesji SSL i zarządza biletami sesji TLS. Jeśli odbywa się to w bramie aplikacji, wszystkie żądania od tego samego klienta można użyć wartości buforowanych. Jeśli odbywa się to na serwerach wewnętrznej bazy danych, a następnie za każdym razem, gdy żądania klienta przejść do innego serwera klient musi ponownie uwierzytelnić. Korzystanie z biletów TLS może pomóc złagodzić ten problem, ale nie są obsługiwane przez wszystkich klientów i może być trudne do skonfigurowania i zarządzania.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — przetwarzanie SSL/TLS jest bardzo intensywne procesora i staje się coraz bardziej intensywne wraz ze wzrostem rozmiarów kluczy. Usunięcie tej pracy z serwerów wewnętrznej bazy danych pozwala im skupić się na tym, w czym są najbardziej wydajne, dostarczając zawartość.
- **Inteligentna routing** — odszyfrowywanie ruchu, brama aplikacji ma dostęp do zawartości żądania, takich jak nagłówki, identyfikator URI i tak dalej, i może używać tych danych do kierowania żądań.
- **Zarządzanie certyfikatami** — certyfikaty muszą być kupowane i instalowane tylko na bramie aplikacji, a nie na wszystkich serwerach zaplecza. Pozwala to zaoszczędzić czas i pieniądze.

Aby skonfigurować zakończenie protokołu SSL, do odbiornika należy dodać certyfikat SSL, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego zgodnie ze specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat SSL musi być w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, który jest wymagany przez bramę aplikacji do wykonywania szyfrowania i odszyfrowywania ruchu.

> [!NOTE] 
>
> Brama aplikacji nie zapewnia możliwości tworzenia nowego certyfikatu ani wysyłania żądania certyfikatu do urzędu certyfikacji.

Aby połączenie SSL działało, należy upewnić się, że certyfikat SSL spełnia następujące warunki:

- Ta bieżąca data i godzina mieści się w zakresie dat "Ważne od" i "Ważne do" na certyfikacie.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certyfikaty obsługiwane dla zakończenia SSL

Brama aplikacji obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: Certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji
- Certyfikat EV (Extended Validation): Certyfikat EV jest certyfikatem zgodnym ze standardowymi wytycznymi dotyczącymi certyfikatów branżowych. Spowoduje to zielone zabark lokalizatora przeglądarki i opublikowanie nazwy firmy.
- Certyfikat symboli wieloznacznych: Ten certyfikat obsługuje dowolną liczbę poddomen opartych na *.site.com, gdzie poddomena zastąpi *. Nie obsługuje jednak site.com, więc w przypadku, gdy użytkownicy uzyskują dostęp do Twojej witryny bez wpisywania wiodącego "www", certyfikat z symbolami wieloznacznych nie obejmie tego.
- Certyfikaty z podpisem własnym: przeglądarki klienckie nie ufają tym certyfikatom i ostrzegają użytkownika, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty z podpisem własnym są dobre do testowania lub środowisk, w których administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny używać certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [konfigurowanie zakończenia SSL za pomocą bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź sekcję [Limity bramy aplikacji,](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) aby poznać maksymalny obsługiwany rozmiar certyfikatu SSL.

## <a name="end-to-end-ssl-encryption"></a>Szyfrowanie SSL od końca do końca

Niektórzy klienci mogą nie chcieć niezaszyfrowanej komunikacji z serwerami zaplecza. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji brama aplikacji obsługuje kompleksowe szyfrowanie SSL.

Kompleksowa usługa SSL pozwala na bezpieczne przesyłanie zaszyfrowanych danych poufnych do zaplecza, umożliwiając jednocześnie korzystanie z funkcji równoważenia obciążenia warstwy 7, które oferuje usługa Application Gateway. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu kompleksowego trybu komunikacji SSL usługa Application Gateway kończy sesje SSL na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie SSL z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. End to end SSL jest włączona przez ustawienie protokołu w [ustawieniu HTTP wewnętrznej bazy danych](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na HTTPS, które jest następnie stosowane do puli wewnętrznej bazy danych.

Zasady SSL ma zastosowanie zarówno do frontendu i backend ruchu. Na froncie brama aplikacji działa jako serwer i wymusza zasady. W wewnętrznej bazy danych brama aplikacji działa jako klient i wysyła informacje o protokole/szyfru jako preferencję podczas uzgadniania SSL.

Brama aplikacji komunikuje się tylko z tymi wystąpieniami wewnętrznej bazy danych, które mają na białej liście swój certyfikat z bramą aplikacji lub których certyfikaty są podpisane przez dobrze znane urzędy certyfikacji, gdzie certyfikat CN pasuje do nazwy hosta w http ustawień zaplecza. Należą do nich zaufane usługi platformy Azure, takie jak aplikacje sieci web usługi Azure App i usługi Azure API Management.

Jeśli certyfikaty elementów członkowskich w puli wewnętrznej bazy danych nie są podpisane przez dobrze znane urzędy certyfikacji, każde wystąpienie w puli wewnętrznej bazy danych z włączonym ssl end-to-end musi być skonfigurowane przy pomocą certyfikatu, aby umożliwić bezpieczną komunikację. Dodanie certyfikatu gwarantuje, że brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. To dodatkowo zabezpiecza komunikację end-to-end.

> [!NOTE] 
>
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak aplikacje sieci web usługi Azure App i usługa Azure API Management.

> [!NOTE] 
>
> Certyfikat dodany do **ustawienia HTTP wewnętrznej bazy danych** w celu uwierzytelnienia serwerów wewnętrznej bazy danych może być taki sam jak certyfikat dodany do **odbiornika** w celu zakończenia protokołu SSL w bramie aplikacji lub inny w celu zwiększenia zabezpieczeń.

![Scenariusz kompleksowej usługi SSL][1]

W tym przykładzie żądania używające protokołu TLS 1.2 są kierowane do serwerów zaplecza w puli Pula1 za pomocą kompleksowej usługi SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Kompleksowa usługa SSL i lista dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty muszą być umieszczane na białej liście z bramą aplikacji, jak opisano w poprzednich krokach, zanim będą mogły być używane.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak usługa Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>End-end SSL z sku v2

Certyfikaty uwierzytelniania zostały przestarzałe i zastąpione zaufanymi certyfikatami głównymi w jednostce SKU bramy aplikacji w wersji 2. Działają one podobnie do certyfikatów uwierzytelniania z kilkoma kluczowymi różnicami:

- Certyfikaty podpisane przez dobrze znane urzędy certyfikacji, których CN pasuje do nazwy hosta w ustawieniach wewnętrznej bazy danych HTTP, nie wymagają żadnego dodatkowego kroku, aby zakończyć ssl do pracy. 

   Na przykład jeśli certyfikaty wewnętrznej bazy danych są wystawiane przez dobrze znany urząd certyfikacji i ma CN contoso.com, a pole hosta ustawienia http wewnętrznej bazy danych jest również ustawione na contoso.com, nie są wymagane żadne dodatkowe kroki. Można ustawić protokół ustawień http wewnętrznej bazy danych na HTTPS, a zarówno sonda kondycji, jak i ścieżka danych będą włączone SSL. Jeśli używasz usługi Azure App Service lub innych usług sieci Web platformy Azure jako wewnętrznej bazy danych, są one niejawnie zaufane, a także i nie są wymagane żadne dalsze kroki dla end to end SSL.
   
> [!NOTE] 
>
> Aby certyfikat SSL mógł być zaufany, ten certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie bramy aplikacji.Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, brama aplikacji sprawdzi aby sprawdzić, czy certyfikat wystawiającego urzędu certyfikacji został wystawiony przez zaufany urząd certyfikacji i tak dalej, dopóki nie zostanie znaleziony zaufany urząd certyfikacji (w którym to momencie zostanie ustanowione zaufane, bezpieczne połączenie) lub nie można znaleźć zaufanego urzędu certyfikacji (w tym momencie brama aplikacji oznaczy zaplecze niezdrowe). W związku z tym zaleca się certyfikat serwera wewnętrznej bazy danych zawierają zarówno główne, jak i pośrednie urzędy certyfikacji.

- Jeśli certyfikat jest podpisany samodzielnie lub podpisany przez nieznanych pośredników, aby włączyć end to end SSL w jednostce SKU w wersji 2, należy zdefiniować zaufany certyfikat główny. Brama aplikacji będzie komunikować się tylko z zapleczem, których certyfikat główny certyfikatu serwera jest zgodny z jedną z listy zaufanych certyfikatów głównych w ustawieniu http wewnętrznej bazy danych skojarzonego z pulą.

> [!NOTE] 
>
> Certyfikat z podpisem własnym musi być częścią łańcucha certyfikatów. Pojedynczy certyfikat z podpisem własnym bez łańcucha nie jest obsługiwany w jednostce SKU w wersji 2.

- Oprócz dopasowania certyfikatu głównego brama aplikacji sprawdza również, czy ustawienie hosta określone w ustawieniu http wewnętrznej bazy danych jest zgodne z ustawieniem nazwy pospolitej (CN) przedstawionej przez certyfikat SSL serwera wewnętrznej bazy danych. Podczas próby ustanowienia połączenia SSL z zapleczem brama aplikacji ustawia rozszerzenie wskazanie nazwy serwera (SNI) na hosta określonego w ustawieniu http wewnętrznej bazy danych.
- Jeśli wybierz **adres hosta z adresu wewnętrznej bazy danych** zamiast pola Host w ustawieniu http wewnętrznej bazy danych, nagłówek SNI jest zawsze ustawiony na numer FQDN puli wewnętrznej bazy danych, a CN na certyfikacie SSL serwera wewnętrznej bazy danych musi być zgodne z jego numerem FQDN. Członkowie puli wewnętrznej bazy danych z usługami IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny jest zakodowanym certyfikatem głównym base64 z certyfikatów serwera wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z zakończeniem do końca SSL, przejdź do [Konfigurowanie end to end SSL za pomocą bramy aplikacji z programem PowerShell,](application-gateway-end-to-end-ssl-powershell.md) aby utworzyć bramę aplikacji przy użyciu end to end SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
