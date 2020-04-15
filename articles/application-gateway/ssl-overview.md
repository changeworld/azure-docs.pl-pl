---
title: Włączanie end-to end TLS w usłudze Azure Application Gateway
description: Ten artykuł jest omówieniem obsługi TLS bramy aplikacji od końca do końca.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311861"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Omówienie zakończenia protokołu TLS i końcowego protokołu TLS z bramą aplikacji

Transport Layer Security (TLS), wcześniej znany jako Secure Sockets Layer (SSL), jest standardową technologią zabezpieczeń do ustanawiania zaszyfrowanego łącza między serwerem sieci web a przeglądarką. To łącze gwarantuje, że wszystkie dane przekazywane między serwerem sieci web a przeglądarkami pozostają prywatne i zaszyfrowane. Brama aplikacji obsługuje zarówno zakończenie protokołu TLS na bramie, jak i szyfrowanie TLS od końca do końca.

## <a name="tls-termination"></a>Zakończenie protokołu TLS

Brama aplikacji obsługuje zakończenie protokołu TLS w bramie, po czym ruch zazwyczaj przepływa niezaszyfrowany do serwerów wewnętrznej bazy danych. Istnieje wiele zalet wykonywania zakończenia TLS na bramie aplikacji:

- **Zwiększona wydajność** — największą wydajnością podczas odszyfrowywania TLS jest początkowe uzgadnianie. Aby zwiększyć wydajność, serwer wykonujący odszyfrowywanie buforuje identyfikatory sesji TLS i zarządza biletami sesji TLS. Jeśli odbywa się to w bramie aplikacji, wszystkie żądania od tego samego klienta można użyć wartości buforowanych. Jeśli odbywa się to na serwerach wewnętrznej bazy danych, a następnie za każdym razem, gdy żądania klienta przejść do innego serwera klient musi ponownie uwierzytelnić. Korzystanie z biletów TLS może pomóc złagodzić ten problem, ale nie są obsługiwane przez wszystkich klientów i może być trudne do skonfigurowania i zarządzania.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — przetwarzanie SSL/TLS jest bardzo intensywne procesora i staje się coraz bardziej intensywne wraz ze wzrostem rozmiarów kluczy. Usunięcie tej pracy z serwerów wewnętrznej bazy danych pozwala im skupić się na tym, w czym są najbardziej wydajne, dostarczając zawartość.
- **Inteligentna routing** — odszyfrowywanie ruchu, brama aplikacji ma dostęp do zawartości żądania, takich jak nagłówki, identyfikator URI i tak dalej, i może używać tych danych do kierowania żądań.
- **Zarządzanie certyfikatami** — certyfikaty muszą być kupowane i instalowane tylko na bramie aplikacji, a nie na wszystkich serwerach zaplecza. Pozwala to zaoszczędzić czas i pieniądze.

Aby skonfigurować zakończenie protokołu TLS, do odbiornika należy dodać certyfikat TLS/SSL, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego zgodnie ze specyfikacją protokołu TLS/SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat TLS/SSL musi być w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, który jest wymagany przez bramę aplikacji do wykonywania szyfrowania i odszyfrowywania ruchu.

> [!NOTE] 
>
> Brama aplikacji nie zapewnia możliwości tworzenia nowego certyfikatu ani wysyłania żądania certyfikatu do urzędu certyfikacji.

Aby połączenie TLS działało, należy upewnić się, że certyfikat TLS/SSL spełnia następujące warunki:

- Ta bieżąca data i godzina mieści się w zakresie dat "Ważne od" i "Ważne do" na certyfikacie.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certyfikaty obsługiwane dla zakończenia protokołu TLS

Brama aplikacji obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: Certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji
- Certyfikat EV (Extended Validation): Certyfikat EV jest certyfikatem zgodnym ze standardowymi wytycznymi dotyczącymi certyfikatów branżowych. Spowoduje to zielone zabark lokalizatora przeglądarki i opublikowanie nazwy firmy.
- Certyfikat symboli wieloznacznych: Ten certyfikat obsługuje dowolną liczbę poddomen opartych na *.site.com, gdzie poddomena zastąpi *. Nie obsługuje jednak site.com, więc w przypadku, gdy użytkownicy uzyskują dostęp do Twojej witryny bez wpisywania wiodącego "www", certyfikat z symbolami wieloznacznych nie obejmie tego.
- Certyfikaty z podpisem własnym: przeglądarki klienckie nie ufają tym certyfikatom i ostrzegają użytkownika, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty z podpisem własnym są dobre do testowania lub środowisk, w których administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny używać certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [konfigurowanie zakończenia TLS za pomocą bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź sekcję [Limity bramy aplikacji,](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) aby poznać maksymalny obsługiwany rozmiar certyfikatu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Szyfrowanie TLS od końca do końca

Niektórzy klienci mogą nie chcieć niezaszyfrowanej komunikacji z serwerami zaplecza. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. W przypadku takich aplikacji brama aplikacji obsługuje szyfrowanie TLS typu end-to-end.

End-to-end TLS umożliwia bezpieczne przesyłanie poufnych danych do zaszyfrowanej wewnętrznej bazy danych, a jednocześnie korzystanie z zalet funkcji równoważenia obciążenia warstwy 7, które zapewnia brama aplikacji. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu z trybu komunikacji TLS od końca do końca brama aplikacji kończy sesje TLS na bramie i odszyfrowuje ruch użytkowników. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Brama aplikacji następnie inicjuje nowe połączenie TLS z serwerem wewnętrznej bazy danych i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera wewnętrznej bazy danych przed przesłaniem żądania do wewnętrznej bazy danych. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. End to end TLS jest włączona przez ustawienie protokołu w [ustawieniu HTTP wewnętrznej bazy danych](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na HTTPS, który jest następnie stosowany do puli wewnętrznej bazy danych.

Zasady TLS ma zastosowanie zarówno do frontendu i wewnętrznej bazy ruchu. Na froncie brama aplikacji działa jako serwer i wymusza zasady. W wewnętrznej bazy danych brama aplikacji działa jako klient i wysyła informacje o protokole/szyfru jako preferencję podczas uzgadniania TLS.

Brama aplikacji komunikuje się tylko z tymi wystąpieniami wewnętrznej bazy danych, które mają na białej liście swój certyfikat z bramą aplikacji lub których certyfikaty są podpisane przez dobrze znane urzędy certyfikacji, gdzie certyfikat CN pasuje do nazwy hosta w ustawieniach wewnętrznej bazy danych HTTP. Należą do nich zaufane usługi platformy Azure, takie jak aplikacje sieci web usługi Azure App i usługi Azure API Management.

Jeśli certyfikaty elementów członkowskich w puli wewnętrznej bazy danych nie są podpisane przez dobrze znane urzędy certyfikacji, każde wystąpienie w puli wewnętrznej bazy danych z włączoną funkcją TLS od początku do końca musi być skonfigurowane przy pomocą certyfikatu umożliwiającego bezpieczną komunikację. Dodanie certyfikatu gwarantuje, że brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. To dodatkowo zabezpiecza komunikację end-to-end.

> [!NOTE] 
>
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak aplikacje sieci web usługi Azure App i usługa Azure API Management.

> [!NOTE] 
>
> Certyfikat dodany do **ustawienia HTTP wewnętrznej bazy danych** w celu uwierzytelnienia serwerów wewnętrznej bazy danych może być taki sam jak certyfikat dodany do **odbiornika** w celu zakończenia protokołu TLS w bramie aplikacji lub inny w celu zwiększenia zabezpieczeń.

![scenariusz tls końca do końca][1]

W tym przykładzie żądania przy użyciu protokołu TLS1.2 są kierowane do serwerów wewnętrznej bazy danych w puli1 przy użyciu protokołu TLS od końca do końca.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Od końca do końca TLS i biała lista certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty muszą być umieszczane na białej liście z bramą aplikacji, jak opisano w poprzednich krokach, zanim będą mogły być używane.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak usługa Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End to end TLS z sku v2

Certyfikaty uwierzytelniania zostały przestarzałe i zastąpione zaufanymi certyfikatami głównymi w jednostce SKU bramy aplikacji w wersji 2. Działają one podobnie do certyfikatów uwierzytelniania z kilkoma kluczowymi różnicami:

- Certyfikaty podpisane przez dobrze znane urzędy certyfikacji, których CN pasuje do nazwy hosta w ustawieniach wewnętrznej bazy danych HTTP, nie wymagają żadnego dodatkowego kroku, aby zakończyć protokół TLS do pracy. 

   Na przykład jeśli certyfikaty wewnętrznej bazy danych są wystawiane przez dobrze znany urząd certyfikacji i ma CN contoso.com, a pole hosta ustawienia http wewnętrznej bazy danych jest również ustawione na contoso.com, nie są wymagane żadne dodatkowe kroki. Można ustawić protokół ustawień http wewnętrznej bazy danych na HTTPS, a zarówno sonda kondycji, jak i ścieżka danych będą włączone TLS. Jeśli używasz usługi Azure App Service lub innych usług sieci Web platformy Azure jako wewnętrznej bazy danych, są one niejawnie zaufane, a także i nie są wymagane żadne dalsze kroki dla końca do końca TLS.
   
> [!NOTE] 
>
> Aby certyfikat TLS/SSL mógł być zaufany, ten certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie bramy aplikacji.Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, Brama aplikacji sprawdzi następnie, czy certyfikat wystawiającego urzędu certyfikacji został wystawiony przez zaufany urząd certyfikacji i tak dalej, dopóki nie zostanie znaleziony zaufany urząd certyfikacji (w którym to momencie zostanie ustanowione zaufane, bezpieczne połączenie) lub nie można znaleźć zaufanego urzędu certyfikacji (w tym momencie brama aplikacji oznaczy w złej kondycji zaplecze). W związku z tym zaleca się certyfikat serwera wewnętrznej bazy danych zawierają zarówno główne, jak i pośrednie urzędy certyfikacji.

- Jeśli certyfikat jest podpisany samodzielnie lub podpisany przez nieznanych pośredników, aby włączyć końcowe do końca TLS w jednostce SKU w wersji 2, należy zdefiniować zaufany certyfikat główny. Brama aplikacji będzie komunikować się tylko z zapleczem, których certyfikat główny certyfikatu serwera jest zgodny z jedną z listy zaufanych certyfikatów głównych w ustawieniu http wewnętrznej bazy danych skojarzonego z pulą.

> [!NOTE] 
>
> Certyfikat z podpisem własnym musi być częścią łańcucha certyfikatów. Pojedynczy certyfikat z podpisem własnym bez łańcucha nie jest obsługiwany w jednostce SKU w wersji 2.

- Oprócz dopasowania certyfikatu głównego brama aplikacji sprawdza również, czy ustawienie hosta określone w ustawieniu http wewnętrznej bazy danych jest zgodne z ustawieniem nazwy pospolitej (CN) prezentowanej przez certyfikat TLS/SSL serwera wewnętrznej bazy danych. Podczas próby ustanowienia połączenia TLS z zapleczem brama aplikacji ustawia rozszerzenie Wskazanie nazwy serwera (SNI) na hosta określonego w ustawieniu http wewnętrznej bazy danych.
- Jeśli wybierz **adres hosta z adresu wewnętrznej bazy danych** zamiast pola Host w ustawieniu http wewnętrznej bazy danych, nagłówek SNI jest zawsze ustawiony na fqdn puli wewnętrznej bazy danych, a CN na serwerze wewnętrznej bazy danych TLS/SSL musi być zgodne z jego FQDN. Członkowie puli wewnętrznej bazy danych z usługami IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny jest zakodowanym certyfikatem głównym base64 z certyfikatów serwera wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z zakończeniem TLS, przejdź do [Konfigurowanie end to end TLS przy użyciu bramy aplikacji z programem PowerShell,](application-gateway-end-to-end-ssl-powershell.md) aby utworzyć bramę aplikacji przy użyciu end to end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
