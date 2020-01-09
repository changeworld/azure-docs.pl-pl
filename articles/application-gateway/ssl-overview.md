---
title: Włączanie kompleksowego protokołu SSL na platformie Azure Application Gateway
description: Ten artykuł zawiera omówienie kompleksowej obsługi protokołu SSL w Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 64b90afd598b96604fc9c3ddc4bc10586e714363
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75657985"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Omówienie kończenia protokołu SSL i kompleksowej usługi SSL z usługą Application Gateway

SSL (SSL) to standardowa technologia zabezpieczeń służąca do nawiązywania szyfrowanego połączenia między serwerem sieci Web a przeglądarką. Ten link zapewnia, że wszystkie dane przesyłane między serwerem sieci Web i przeglądarkami pozostają w trybie prywatnym i zaszyfrowanym. Brama Application Gateway obsługuje zarówno zakończenie protokołu SSL w bramie, jak i kompleksowe szyfrowanie SSL.

## <a name="ssl-termination"></a>Kończenie żądań protokołu SSL

Usługa Application Gateway obsługuje przerywanie połączenia SSL na bramie, po którym ruch na ogół płynie niezaszyfrowany do serwerów zaplecza. Istnieje wiele zalet zakończenia protokołu SSL w bramie aplikacji:

- **Zwiększona wydajność** — największe zwiększenie wydajności podczas odszyfrowywania protokołu SSL jest początkową uzgadnianiem. Aby zwiększyć wydajność, serwer wykonujący odszyfrowywanie buforuje identyfikatory sesji SSL i zarządza biletami sesji TLS. W takim przypadku wszystkie żądania od tego samego klienta mogą korzystać z buforowanych wartości. Jeśli jest to wykonywane na serwerach zaplecza, za każdym razem, gdy żądania klienta przejdą do innego serwera, klient musi ponownie przeprowadzić uwierzytelnienie. Korzystanie z biletów TLS może pomóc w ograniczeniu tego problemu, ale nie są one obsługiwane przez wszystkich klientów i mogą być trudne do skonfigurowania i zarządzania.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — przetwarzanie za pośrednictwem protokołu SSL/TLS jest bardzo czasochłonne i zwiększa się w miarę wzrostu rozmiaru kluczy. Usunięcie tej pracy z serwerów zaplecza pozwala im skupić się na tym, co są najbardziej wydajne, dostarczając zawartość.
- **Inteligentne Routing** — przez odszyfrowanie ruchu, Brama aplikacji ma dostęp do zawartości żądania, takiej jak nagłówki, identyfikator URI itd., i może używać tych danych do przesyłania żądań.
- **Zarządzanie certyfikatami** — certyfikaty muszą być zakupione i zainstalowane tylko w bramie aplikacji, a nie na wszystkich serwerach zaplecza. Pozwala to zaoszczędzić czas i pieniądze.

Aby skonfigurować zakończenie protokołu SSL, do odbiornika musi zostać dodany certyfikat SSL, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego zgodnie ze specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat SSL musi znajdować się w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia wyeksportowanie klucza prywatnego wymaganego przez bramę aplikacji w celu przeprowadzenia szyfrowania i odszyfrowywania ruchu.

> [!NOTE] 
>
> Usługa Application Gateway nie oferuje żadnej możliwości tworzenia nowego certyfikatu ani wysyłania żądania certyfikatu do urzędu certyfikacji.

Aby połączenie SSL działało, należy się upewnić, że certyfikat SSL spełnia następujące warunki:

- Bieżąca data i godzina przypada w zakresie dat "ważny od" i "ważny do" w odniesieniu do certyfikatu.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certyfikaty obsługiwane przez zakończenie protokołu SSL

Brama aplikacji obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji (CA)
- Certyfikat EV (rozszerzone sprawdzanie poprawności): certyfikat EV jest standardową zasadą certyfikatu branżowego. Spowoduje to również przekształcenie paska lokalizatora przeglądarki na zieloną i opublikowanie nazwy firmy.
- Certyfikat z symbolami wieloznacznymi: ten certyfikat obsługuje dowolną liczbę poddomen opartych na *. site.com, gdzie poddomena zastąpi *. Nie jest to jednak obsługiwane w site.com, więc w przypadku, gdy użytkownicy uzyskują dostęp do witryny sieci Web bez konieczności pisania wiodącego "www", certyfikat wieloznaczny nie będzie uwzględniał tego.
- Certyfikaty z podpisem własnym: przeglądarki klienta nie ufają tym certyfikatom i ostrzegają użytkownika o tym, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty z podpisem własnym są przydatne w przypadku testowania lub środowisk, w których Administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny korzystać z certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zakończenia protokołu SSL w usłudze Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź sekcję [limity Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) , aby poznać maksymalny obsługiwany rozmiar certyfikatu SSL.

## <a name="end-to-end-ssl-encryption"></a>Kompleksowe szyfrowanie SSL

Niektórzy klienci mogą nie chcieć nieszyfrowanej komunikacji z serwerami zaplecza. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji brama aplikacji obsługuje kompleksowe szyfrowanie SSL.

Kompleksowa usługa SSL pozwala na bezpieczne przesyłanie zaszyfrowanych danych poufnych do zaplecza, umożliwiając jednocześnie korzystanie z funkcji równoważenia obciążenia warstwy 7, które oferuje usługa Application Gateway. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu kompleksowego trybu komunikacji SSL usługa Application Gateway kończy sesje SSL na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie SSL z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. Kompleksowa usługa SSL jest włączana przez ustawienie protokołu w ustawieniu protokół [http zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na https, który następnie jest stosowany do puli zaplecza.

Zasady protokołu SSL dotyczą zarówno ruchu frontonu, jak i zaplecza. Na frontonie Application Gateway działa jako serwer i wymusza zasady. W zapleczu Application Gateway pełni rolę klienta i wysyła informacje o protokole lub szyfru jako preferencję podczas uzgadniania protokołu SSL.

Brama Application Gateway komunikuje się tylko z tymi wystąpieniami zaplecza, które mają listy dozwolonych swój certyfikat z bramą aplikacji lub których certyfikaty są podpisane przez dobrze znane urzędy urzędów certyfikacji, w których nazwa POSPOLITa certyfikatu pasuje do nazwy hosta w protokole HTTP Ustawienia zaplecza. Należą do nich zaufane usługi platformy Azure, takie jak Azure App Service Web Apps i Azure API Management.

Jeśli certyfikaty członków w puli zaplecza nie są podpisane przez dobrze znane urzędy certyfikacji, każde wystąpienie w puli zaplecza z włączonym kompleksowym protokołem SSL musi być skonfigurowane przy użyciu certyfikatu w celu umożliwienia bezpiecznej komunikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Pozwala to na zapewnienie kompleksowej komunikacji.

> [!NOTE] 
>
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak usługa Azure App Service Web Apps i Azure API Management.

> [!NOTE] 
>
> Certyfikat dodany do **Ustawienia protokołu HTTP zaplecza** służącego do uwierzytelniania serwerów zaplecza może być taki sam jak certyfikat dodany do **odbiornika** w celu zakończenia protokołu SSL w usłudze Application Gateway lub inny w celu zwiększenia bezpieczeństwa.

![Scenariusz kompleksowej usługi SSL][1]

W tym przykładzie żądania używające protokołu TLS 1.2 są kierowane do serwerów zaplecza w puli Pula1 za pomocą kompleksowej usługi SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Kompleksowa usługa SSL i lista dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty muszą być listy dozwolonych z bramą aplikacji, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Kompleksowa wersja protokołu SSL z jednostką SKU v2

Certyfikaty uwierzytelniania zostały wycofane i zastąpione przez zaufane certyfikaty główne w jednostce SKU Application Gateway v2. Działają one podobnie jak w przypadku certyfikatów uwierzytelniania z kilkoma kluczowymi różnicami:

- Certyfikaty podpisane przez dobrze znane urzędy urzędów certyfikacji, których nazwy POSPOLITej są zgodne z nazwą hosta w ustawieniach zaplecza protokołu HTTP, nie wymagają żadnego dodatkowego kroku do pracy z kompleksowym protokołem SSL. 

   Na przykład, jeśli certyfikaty zaplecza są wystawiane przez dobrze znany urząd certyfikacji i ma nazwę POSPOLITą contoso.com, a pole hosta ustawienia http zaplecza ma również wartość contoso.com, nie są wymagane żadne dodatkowe kroki. Można ustawić protokół ustawień protokołu HTTP zaplecza na HTTPS, a zarówno sonda kondycji, jak i ścieżka danych będzie włączona protokół SSL. Jeśli używasz Azure App Service lub innych usług sieci Web platformy Azure jako zaplecza, to są one niejawnie zaufane i nie są wymagane żadne dalsze kroki do kompleksowego korzystania z protokołu SSL.
   
> [!NOTE] 
>
> Aby certyfikat SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji uwzględniony w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, Application Gateway sprawdzi Aby sprawdzić, czy certyfikat wystawiającego urzędu certyfikacji został wystawiony przez zaufany urząd certyfikacji i tak dalej, dopóki nie zostanie znaleziony zaufany urząd certyfikacji (w którym zostanie nawiązane zaufane, bezpieczne połączenie) lub nie zostanie znaleziony zaufany urząd certyfikacji (w tym momencie Application Gateway oznaczy zaplecza zła kondycja). W związku z tym zaleca się, aby certyfikat serwera wewnętrznej bazy danych zawierał zarówno główny, jak i intermidiate urzędy certyfikacji.

- Jeśli certyfikat jest podpisany z podpisem własnym lub podpisany przez nieznane osoby pośredniczące, a następnie aby włączyć kompleksową obsługę protokołu SSL w wersji 2, należy zdefiniować zaufany certyfikat główny. Application Gateway komunikuje się tylko z zapleczem, którego certyfikat główny certyfikatu serwera jest zgodny z jedną z listy zaufanych certyfikatów głównych w ustawieniu protokołu HTTP zaplecza skojarzonym z pulą.

> [!NOTE] 
>
> Certyfikat z podpisem własnym musi być częścią łańcucha certyfikatów. Jeden certyfikat z podpisem własnym bez łańcucha nie jest obsługiwany w jednostce SKU w wersji 2.

- Oprócz zgodności z certyfikatem głównym Application Gateway sprawdza także, czy ustawienie hosta określone w ustawieniu protokołu HTTP zaplecza pasuje do nazwy pospolitej (CN) przedstawionej przez certyfikat SSL serwera wewnętrznej bazy danych. Podczas próby nawiązania połączenia SSL z zapleczem Application Gateway ustawia rozszerzenie Oznaczanie nazwy serwera (SNI) na host określony w ustawieniu http zaplecza.
- Jeśli wybrano opcję **Wybierz nazwę hosta z adresu zaplecza** zamiast pola host w ustawieniu http zaplecza, nagłówek SNI jest zawsze ustawiany jako nazwa FQDN puli zaplecza, a CN w certyfikacie SSL serwera wewnętrznej bazy danych musi być zgodna z jego nazwą FQDN. Elementy członkowskie puli zaplecza z adresami IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny to zakodowany w formacie base64 certyfikat główny z certyfikatów serwera wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z kompleksowym protokołem SSL przejdź do strony [Konfigurowanie kompleksowej usługi SSL przy użyciu Application Gateway z programem PowerShell](application-gateway-end-to-end-ssl-powershell.md) , aby utworzyć bramę aplikacji przy użyciu kompleksowej usługi SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
