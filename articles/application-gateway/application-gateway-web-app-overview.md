---
title: Omówienie wielodostępnych zapleczy, takich jak usługi Azure App service, za pomocą usługi Azure Application Gateway
description: Ta strona zawiera omówienie obsługi wielodostępnych zapleczy w usłudze Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 256fb42be8fec056ed7d10cfc4197a1b5a33fac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807168"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Pomocy technicznej dla wielodostępnych aplikacji bramy kończy się takie jak App service

W projektach architektury wielodostępnej na serwerach sieci web wielu witryn sieci Web są uruchomione na tym samym wystąpieniu serwera sieci web. Nazwy hostów są używane do rozróżnienia między różnymi aplikacjami, które są obsługiwane. Domyślnie usługa Application Gateway nie zmienia przychodzącego nagłówka hosta HTTP z klienta i wysyła niezmieniony nagłówek do zaplecza. Ta działa dobrze w przypadku składowych puli zaplecza, takie jak karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP wewnętrznych adresów IP i nazwy FQDN, jak te nie należy polegać na konkretnym nagłówku hosta lub rozszerzeniu SNI przy rozpoznawaniu właściwego punktu końcowego. Istnieją jednak wiele usług, takich jak aplikacje sieci web usługi aplikacji Azure i usługi Azure API management, które są wielodostępne z natury i polegają na konkretnym nagłówku hosta lub rozszerzeniu SNI przy rozpoznawaniu właściwego punktu końcowego. Zwykle nazwę DNS na aplikację, która z kolei jest nazwa DNS skojarzone z usługą application gateway, różni się od nazwy domeny usługi wewnętrznej bazy danych. W związku z nagłówkiem hosta w oryginalne żądanie odebrane przez usługę application gateway nie jest taka sama jak nazwa hosta usługi wewnętrznej bazy danych. W związku z tym chyba, że nagłówek hosta w żądaniu z bramy aplikacji z zapleczem usługi zostanie zmieniona na nazwę hosta usługi wewnętrznej bazy danych zaplecza w aplikacji wielodostępnej nie będą mogli rozpoznać żądania do właściwego punktu końcowego. 

Usługa Application Gateway umożliwia użytkownikom zastępowanie nagłówka hosta HTTP żądania na podstawie nazwy hosta zaplecza. Ta funkcja umożliwia obsługę zapleczy wielu dzierżaw, na przykład aplikacji internetowych usługi Azure App Service i interfejsu API zarządzania. Ta możliwość jest dostępna dla standardowych jednostek SKU i jednostek SKU zapory aplikacji internetowych w wersjach 1 oraz 2. 

![Zastąp hosta](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> To nie ma zastosowania do usługi Azure App service environment (ASE), ponieważ środowisko ASE jest dedykowanym zasobem w przeciwieństwie do usługi aplikacji Azure, która jest zasobem wielodostępnych.

## <a name="override-host-header-in-the-request"></a>Zastąp nagłówka hosta w żądaniu

Możliwość określenia przesłonięcia hosta jest zdefiniowany w [ustawienia HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) i mogą być stosowane do każdej puli zaplecza podczas tworzenia reguł. Następujące dwa sposoby zastępowania nagłówka hosta i rozszerzenia SNI dla wielodostępnych zapleczy są obsługiwane następujące scenariusze:

- Możliwość ustawienia nazwy hosta na stałą wartość jawnie wprowadzony w ustawieniach protokołu HTTP. Ta funkcja zapewnia, że nagłówek hosta zostanie zastąpiony podaną wartością dla całego ruchu do puli zaplecza, w których są stosowane określone ustawienia protokołu HTTP. W przypadku korzystania z kompleksowej usługi SSL ta zastąpiona nazwa hosta jest używana w rozszerzeniu SNI. Ta funkcja umożliwia realizację scenariuszy, w których farma puli zaplecza oczekuje nagłówka hosta, który różni się od przychodzącego nagłówka hosta klienta.

- Możliwość uzyskiwania nazwy hosta z adresu IP lub nazwa FQDN składowych puli zaplecza. Ustawienia HTTP udostępniają również opcję, aby dynamicznie wyboru nazwy hosta z nazwy FQDN składowej puli zaplecza, jeśli skonfigurowane z opcją pobierania nazwy hosta z elementu członkowskiego poszczególnych puli zaplecza. W przypadku korzystania z kompleksowej usługi SSL ta nazwa hosta jest określana na podstawie nazwy FQDN i używana w rozszerzeniu SNI. Ta funkcja umożliwia obsługę scenariuszy, w których pula zaplecza może mieć dwóch lub więcej wielodostępnych usług PaaS w takich jak aplikacje sieci web platformy Azure i nagłówek hosta żądania do każdego członka zawiera nazwę hosta pochodzącą od jego nazwy FQDN. Dotyczące realizacji tego scenariusza, możemy użyć przełącznika w ustawieniach protokołu HTTP o nazwie [wybierz nazwę hosta z adresów zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) które dynamicznie spowoduje przesłonięcie nagłówka hosta w żądaniu oryginalnego, do wymienionej w puli zaplecza.  Na przykład jeśli nazwy FQDN puli zaplecza zawiera "contoso11.azurewebsites.net" i "contoso22.azurewebsites.net", oryginalne żądanie nagłówek hosta, który jest contoso.com zostanie zastąpiona w celu contoso11.azurewebsites.net lub contoso22.azurewebsites.net Jeśli żądanie jest wysyłane do serwera odpowiedniego serwera zaplecza. 

  ![Scenariusz aplikacji internetowej](./media/application-gateway-web-app-overview/scenario.png)

Korzystając z tej funkcji, klienci mogą określić opcje w ustawieniach HTTP i niestandardowe sondy na potrzeby prawidłowej konfiguracji. To ustawienie jest następnie wiązane z odbiornikiem i pulą zaplecza przy użyciu reguły.

## <a name="special-considerations"></a>Specjalne uwagi

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Kończenie żądań SSL i kompleksowej usługi SSL z wieloma dzierżawami usługi

Kończenie żądań SSL i kompleksową usługę SSL szyfrowania jest obsługiwane w przypadku wielodostępnych usług. Kończenie żądań SSL na bramie aplikacji, aby uzyskać certyfikat SSL jest nadal wymagane do dodania do odbiornika bramy aplikacji. Jednak w przypadku typu end to end SSL, Azure zaufane usługi, takie jak sieci web Azure aplikacji usługi App service nie wymagają umieszczania na białej liście zapleczy w usłudze application gateway. W związku z tym nie ma potrzeby można dodać certyfikatów uwierzytelniania. 

![kompleksowej usługi SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Należy zauważyć, że na powyższym obrazie nie jest wymagane do dodania certyfikaty uwierzytelniania, po wybraniu usługi App service jako zaplecza.

### <a name="health-probe"></a>Sonda kondycji

Zastępowanie nagłówka hosta w **ustawienia HTTP** ma wpływ tylko na żądanie i routingu. nie ma wpływu na zachowanie sondy kondycji. Aby kompleksowa funkcja działała, ustawienia sondowania i ustawienia HTTP muszą zostać zmodyfikowane w celu odzwierciedlenia prawidłowej konfiguracji. Ponadto, aby zapewnić możliwość określenia nagłówka hosta w konfiguracji sondowania, niestandardowe sondy obsługują możliwość uzyskiwania nagłówek hosta z aktualnie skonfigurowanych ustawień HTTP. Tę konfigurację można określić za pomocą parametru `PickHostNameFromBackendHttpSettings` w konfiguracji sondowania.

### <a name="redirection-to-app-services-url-scenario"></a>Przekierowanie do scenariusza adres URL usługi App Service

Może istnieć scenariusze, w której nazwy hosta w odpowiedzi z usługi App service mogą być wskazówką przeglądarki przez użytkownika końcowego do *. azurewebsites.net hostname zamiast domeny skojarzone z usługą Application Gateway. Ten problem może się zdarzyć, gdy:

- Masz przekierowania skonfigurowane w usłudze App Service. Przekierowywanie można tak proste, jak dodawanie znaku ukośnika na końcu żądania.
- Masz uwierzytelniania usługi Azure AD, co powoduje, że przekierowanie.

Aby rozwiązać takich przypadkach, zobacz [Rozwiązywanie problemów z przekierowania do wystawienia adres URL usługi App service](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować bramę aplikacji z aplikacji z wieloma dzierżawami, takich jak usługi Azure app service Web Apps jako składową puli zaplecza, odwiedzając stronę [aplikacji sieci web Konfigurowanie usługi App Service z usługą Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-web-app)
