---
title: Włączanie kompleksowej usługi SSL w usłudze Azure Application Gateway
description: W tym artykule przedstawiono omówienie usługi Application Gateway obsługuje kompleksowej usługi SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: 626db07a81c6482a689329b8cddc9f40b464bb7e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649078"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Omówienie kompleksowej usługi SSL z usługą Application Gateway

Usługa Application Gateway obsługuje kończenia żądań SSL na bramie, po której ruch na ogół płynie niezaszyfrowany do serwerów wewnętrznej bazy danych. Ta funkcja umożliwia odciążenie serwerów sieci Web z nadmiaru kosztownych operacji szyfrowania i odszyfrowywania. Jednak dla niektórych klientów nieszyfrowana komunikacja z serwerami zaplecza jest opcją niemożliwą do zaakceptowania. Nieszyfrowana komunikacja może być spowodowana przez wymagania dotyczące zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji brama aplikacji obsługuje kompleksowe szyfrowanie SSL.

Kompleksowa usługa SSL pozwala na bezpieczne przesyłanie danych poufnych na zaplecze, szyfrowane, gdy nadal trwa, korzystając z zalet funkcji równoważenia obciążenia warstwy 7 której usługa application gateway zapewnia. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu kompleksowego trybu komunikacji SSL usługa Application Gateway kończy sesje SSL na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie SSL z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Kompleksowa usługa SSL jest włączona konfigurując dla ustawienia protokołu **parametr BackendHTTPSetting** HTTPS, których są następnie stosowane do puli zaplecza. Każdy serwer zaplecza w puli zaplecza z włączoną kompleksową usługą SSL należy skonfigurować przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację.

![Scenariusz kompleksowej usługi SSL][1]

W tym przykładzie żądania używające protokołu TLS 1.2 są kierowane do serwerów zaplecza w puli Pula1 za pomocą kompleksowej usługi SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Kompleksowa usługa SSL i lista dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty musi być na liście dozwolonych usługi application gateway, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak Azure Web Apps.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Kompleksowa usługa SSL z jednostką SKU v2

Certyfikaty uwierzytelniania zostały przestarzały i zastąpiony przez zaufanych certyfikatów głównych, w ramach jednostki SKU v2 Application Gateway. Działają podobnie do uwierzytelniania certyfikatów z niewielkimi różnicami klucza:

- Certyfikaty podpisana przez dobrze znanych urzędów certyfikacji którego CN jest zgodna z nazwą hosta, w ustawieniach HTTP zaplecza nie wymagają żadnych dodatkowych czynności dla kompleksowej usługi SSL do pracy. 

   Na przykład jeśli certyfikaty wewnętrznej bazy danych są wydawane przez dobrze znanego urzędu certyfikacji i ma CN contoso.com, a pole host ustawienia http zaplecza jest również ustawiona do domeny contoso.com, następnie żadne dodatkowe kroki są wymagane. Można ustawić http zaplecza, ustawienie protokołu HTTPS, a oba kondycji badania i danych ścieżka powinna mieć włączony protokół SSL. Jeśli używasz usługi Azure Web Apps lub innych usług sieci web platformy Azure jako zaplecza są niejawnie zaufanych również i nie trzeba wykonywać dalszych czynności są wymagane do kompleksowej usługi SSL.
- Jeśli certyfikat z podpisem własnym lub jest podpisany przez nieznany pośredników, następnie włączyć kompleksowej usługi SSL w jednostce SKU v2 zaufany certyfikat główny musi być zdefiniowany. Usługa Application Gateway komunikuje się tylko za pomocą którego certyfikat serwera, certyfikat główny pasuje do listy zaufanych certyfikatów głównych w ustawienia http zaplecza, które są skojarzone z pulą zaplecza.
- Oprócz dopasowania certyfikatu głównego usługa Application Gateway również sprawdza, czy ustawienie określonego w ustawieniach http zaplecza hosta jest zgodny z typem nazwa pospolita (CN) prezentowany przez certyfikat SSL serwera wewnętrznej bazy danych. Podczas próby nawiązania połączenia SSL z zapleczem usługi, usługa Application Gateway Ustawia rozszerzenie oznaczaniem nazwy serwera (SNI) do hosta określona w ustawieniu protokołu http zaplecza.
- Jeśli **wybierz nazwę hosta z adresów zaplecza** jest wybierany zamiast pole Host w ustawieniach http zaplecza, a następnie nagłówka SNI ma zawsze wartość do puli zaplecza, nazwy FQDN i nazwa Pospolita certyfikatu musi być zgodna nazwy FQDN na serwerze wewnętrznej bazy danych protokołu SSL. Składowych puli zaplecza przy użyciu adresów IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny jest certyfikatu głównego zakodowany base64 z certyfikatów serwera zaplecza.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z kompleksową usługą SSL przejdź do tematu [Configure an application gateway with SSL termination using the Azure portal (Konfigurowanie bramy aplikacji z kończeniem żądań SSL za pomocą witryny Azure Portal)](create-ssl-portal.md), aby utworzyć bramę aplikacji korzystającą z kompleksowej usługi SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
