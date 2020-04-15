---
title: Tylne końce z wieloma dzierżawami
titleSuffix: Azure Application Gateway
description: Ta strona zawiera omówienie obsługi wielodostępnych zapleczy w usłudze Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: a171dc795e685655b5a3c73d088d3963c2aaa4ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312318"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Obsługa bramy aplikacji dla zaplecza wielu dzierżawców, takich jak usługa aplikacji

W projektach architektonicznych z wieloma dzierżawcami na serwerach sieci web wiele witryn sieci Web jest uruchomionych w tym samym wystąpieniu serwera sieci Web. Nazwy hostów są używane do rozróżniania różnych aplikacji, które są hostowane. Domyślnie usługa Application Gateway nie zmienia przychodzącego nagłówka hosta HTTP z klienta i wysyła niezmieniony nagłówek do zaplecza. Działa to dobrze w przypadku elementów członkowskich puli wewnętrznej bazy danych, takich jak karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP i FQDN, ponieważ nie są one zależne od określonego nagłówka hosta lub rozszerzenia SNI w celu rozpoznania prawidłowego punktu końcowego. Istnieje jednak wiele usług, takich jak aplikacje sieci web usługi Azure App i zarządzanie interfejsami API platformy Azure, które mają charakter wielodostępny i opierają się na określonym nagłówku hosta lub rozszerzeniu SNI, aby rozpoznać do prawidłowego punktu końcowego. Zazwyczaj nazwa DNS aplikacji, która z kolei jest nazwą DNS skojarzoną z bramą aplikacji, różni się od nazwy domeny usługi wewnętrznej bazy danych. W związku z tym nagłówek hosta w oryginalnym żądaniu odebranym przez bramę aplikacji nie jest taki sam jak nazwa hosta usługi wewnętrznej bazy danych. Z tego powodu, chyba że nagłówek hosta w żądaniu z bramy aplikacji do wewnętrznej bazy danych zostanie zmieniona na nazwę hosta usługi wewnętrznej bazy danych, zaplecze wielu dzierżawców nie są w stanie rozpoznać żądania do prawidłowego punktu końcowego. 

Usługa Application Gateway umożliwia użytkownikom zastępowanie nagłówka hosta HTTP żądania na podstawie nazwy hosta zaplecza. Ta funkcja umożliwia obsługę zapleczy wielu dzierżaw, na przykład aplikacji internetowych usługi Azure App Service i interfejsu API zarządzania. Ta możliwość jest dostępna dla standardowych jednostek SKU i jednostek SKU zapory aplikacji internetowych w wersjach 1 oraz 2. 

![zastępowanie hosta](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Nie ma to zastosowania do środowiska usługi Azure App Service (ASE), ponieważ środowisko ASE jest zasobem dedykowanym w przeciwieństwie do usługi Azure App, która jest zasobem wielodostępnym.

## <a name="override-host-header-in-the-request"></a>Zastępowanie nagłówka hosta w żądaniu

Możliwość określenia zastąpienia hosta jest zdefiniowana w [ustawieniach HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) i może być stosowana do dowolnej puli zaplecza podczas tworzenia reguł. Obsługiwane są następujące dwa sposoby zastępowania nagłówka hosta i rozszerzenia SNI dla zaplecza wielu dzierżawców:

- Możliwość ustawienia nazwy hosta na stałą wartość jawnie wprowadzona w ustawieniach HTTP. Ta funkcja zapewnia, że nagłówek hosta jest zastępowany do tej wartości dla całego ruchu do puli zaplecza, gdzie są stosowane określone ustawienia HTTP. W przypadku korzystania z end to end TLS, ta zastąpiona nazwa hosta jest używana w rozszerzeniu SNI. Ta funkcja umożliwia scenariusze, w których farma puli zaplecza oczekuje nagłówka hosta, który różni się od nagłówka hosta przychodzącego klienta.

- Możliwość wyprowadzenia nazwy hosta z adresu IP lub nazwy FQDN członków puli zaplecza. Ustawienia HTTP zapewniają również opcję dynamicznego wybierania nazwy hosta z nazwy FQDN członka puli zaplecza, jeśli jest skonfigurowana z opcją wyprowadzania nazwy hosta od pojedynczego członka puli zaplecza. Podczas korzystania z end to end TLS, ta nazwa hosta pochodzi od nazwy FQDN i jest używana w rozszerzeniu SNI. Ta funkcja umożliwia scenariusze, w których pula zaplecza może mieć dwie lub więcej usług PaaS z wieloma dzierżawcami, takich jak aplikacje sieci Web platformy Azure, a nagłówek hosta żądania dla każdego elementu członkowskiego zawiera nazwę hosta pochodzącą od jego nazwy FQDN. Do implementowania tego scenariusza używamy przełącznika w ustawieniach HTTP o nazwie [Wybierz nazwę hosta z adresu wewnętrznej bazy danych,](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) który dynamicznie zastępuje nagłówek hosta w oryginalnym żądaniu do tego, o którym mowa w puli wewnętrznej bazy danych.  Na przykład jeśli nazwa FQDN puli wewnętrznej bazy danych zawiera "contoso11.azurewebsites.net" i "contoso22.azurewebsites.net", nagłówek hosta oryginalnego żądania, który jest contoso.com zostanie zastąpiony contoso11.azurewebsites.net lub contoso22.azurewebsites.net, gdy żądanie zostanie wysłane do odpowiedniego serwera wewnętrznej bazy danych. 

  ![Scenariusz aplikacji internetowej](./media/application-gateway-web-app-overview/scenario.png)

Korzystając z tej funkcji, klienci mogą określić opcje w ustawieniach HTTP i niestandardowe sondy na potrzeby prawidłowej konfiguracji. To ustawienie jest następnie powiązane z odbiornikiem i pulą zaplecza przy użyciu reguły.

## <a name="special-considerations"></a>Uwagi szczególne

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>Zakończenie protokołu TLS i zakończenie tls z usługami wielodostępczymi

Zarówno szyfrowanie TLS, jak i end-to-end TLS jest obsługiwane przez usługi z wieloma dzierżawcami. W przypadku zakończenia protokołu TLS w bramie aplikacji certyfikat TLS nadal musi być dodawany do odbiornika bramy aplikacji. Jednak w przypadku TLS od końca do końca zaufane usługi platformy Azure, takie jak aplikacje sieci web usługi Azure App, nie wymagają umieszczania na białej liście zaplecza w bramie aplikacji. W związku z tym nie ma potrzeby dodawania żadnych certyfikatów uwierzytelniania. 

![od końca do końca TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Należy zauważyć, że na powyższej ilustracji nie ma wymogu dodawania certyfikatów uwierzytelniania, gdy usługa aplikacji jest zaznaczona jako zaplecze.

### <a name="health-probe"></a>Sonda kondycji

Zastępowanie nagłówka hosta w **ustawieniach HTTP** ma wpływ tylko na żądanie i jego routing. nie ma wpływu na zachowanie sondy kondycji. Aby kompleksowa funkcja działała, ustawienia sondowania i ustawienia HTTP muszą zostać zmodyfikowane w celu odzwierciedlenia prawidłowej konfiguracji. Oprócz zapewnienia możliwości określenia nagłówka hosta w konfiguracji sondy, niestandardowe sondy obsługują również możliwość uzyskania nagłówka hosta z aktualnie skonfigurowanych ustawień HTTP. Tę konfigurację można określić za pomocą parametru `PickHostNameFromBackendHttpSettings` w konfiguracji sondowania.

### <a name="redirection-to-app-services-url-scenario"></a>Scenariusz przekierowania do adresu URL usługi App Service

Mogą istnieć scenariusze, w których nazwa hosta w odpowiedzi z usługi app może kierować przeglądarkę użytkownika końcowego do nazwy hosta *.azurewebsites.net zamiast do domeny skojarzonej z bramą aplikacji. Ten problem może wystąpić, gdy:

- Przekierowanie zostało skonfigurowane w usłudze app service. Przekierowanie może być tak proste, jak dodanie końcowego ukośnika do żądania.
- Masz uwierzytelnianie usługi Azure AD, które powoduje przekierowanie.

Aby rozwiązać takie przypadki, zobacz [Rozwiązywanie problemów z przekierowaniem do adresu URL usługi aplikacji](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować bramę aplikacji za pomocą aplikacji z wieloma dzierżawcami, takiej jak aplikacja internetowa usługi Azure App jako członek puli zaplecza, odwiedzając pozycję [Konfigurowanie aplikacji sieci Web usługi App Service za pomocą bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
