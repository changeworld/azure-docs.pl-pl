---
title: Tworzenie i integracja interfejsu API usługi Azure ExpressRoute CrossConnnections
description: Ten artykuł zawiera szczegółowe omówienie dla partnerów usługi ExpressRoute o typie zasobu usługi expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187025"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Tworzenie i integracja interfejsu API usługi ExpressRoute CrossConnnections

Interfejs API programu ExpressRoute Partner Resource Manager umożliwia partnerom usługi ExpressRoute zarządzanie konfiguracją obwodów usługi ExpressRoute dla warstwy 2 i warstwy 3. Interfejs API menedżera zasobów partnera usługi ExpressRoute wprowadza nowy typ zasobu **expressRouteCrossConnections**. Partnerzy używają tego zasobu do zarządzania obwodami usługi ExpressRoute klienta.

## <a name="workflow"></a>Przepływ pracy

Zasób expressRouteCrossConnections jest zasobem cienia obwodu usługi ExpressRoute. Gdy klient platformy Azure tworzy obwód usługi ExpressRoute i wybiera określonego partnera usługi ExpressRoute, firma Microsoft tworzy zasób expressRouteCrossConnections w subskrypcji zarządzania usługą Azure ExpressRoute partnera. W ten sposób firma Microsoft definiuje grupę zasobów do utworzenia zasobu expressRouteCrossConnections w. Standardem nazewnictwa dla grupy zasobów jest **CrossConnection-* PeeringLocation***; gdzie usługa PeeringLocation = lokalizacja usługi ExpressRoute. Na przykład jeśli klient utworzy obwód usługi ExpressRoute w Denver, crossconnection zostanie utworzony w subskrypcji platformy Azure partnera w następującej grupie zasobów: **CrossConnnection-Denver**.

Partnerzy usługi ExpressRoute zarządzają konfiguracją warstwy 2 i warstwy 3, wystawiając operacje REST względem zasobu expressRouteCrossConnections.

## <a name="benefits"></a>Korzyści

Korzyści z przejścia do zasobu expressRouteCrossConnections:

* Wszelkie przyszłe ulepszenia dla partnerów usługi ExpressRoute zostaną udostępnione w zasobie Usługi ExpressRouteCrossConnection.

* Partnerzy mogą stosować [kontrolę dostępu opartą na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) do zasobu expressRouteCrossConnection. Te formanty mogą definiować uprawnienia, dla których konta użytkowników mogą modyfikować zasób expressRouteCrossConnection i dodawać/aktualizować/usuwać konfiguracje komunikacji równorzędnej.

* Zasób expressRouteCrossConnection udostępnia interfejsy API, które mogą być pomocne w rozwiązywaniu problemów z połączeniami usługi ExpressRoute. Obejmuje to tabelę ARP, podsumowanie tabeli trasy BGP i szczegóły tabeli trasy BGP. Ta funkcja nie jest obsługiwana przez klasyczne interfejsy API wdrażania.

* Partnerzy mogą również wyszukać anonsowane społeczności w komunikacji równorzędnej firmy Microsoft przy użyciu zasobu *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Kroki tworzenia i integracji API

Aby rozwijać się w interfejsie API partnera, partnerzy usługi ExpressRoute wykorzystują konfigurację klienta testowego i partnera testowego. Konfiguracja klienta testowego będzie używana do tworzenia obwodów usługi ExpressRoute w testowych lokalizacjach komunikacji równorzędnej, które są mapowane na fikcyjne urządzenia i porty. Konfiguracja partnera testowego służy do zarządzania obwodami usługi ExpressRoute utworzonymi w lokalizacji komunikacji równorzędnej testów.

### <a name="1-enlist-subscriptions"></a>1. Zaciągnij subskrypcje

Aby zażądać konfiguracji partnera testowego i przetestować klienta, należy zarejestrować dwie subskrypcje usługi Pay-As-You-Go na kontakt inżynieryjny usługi ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Ta subskrypcja będzie używana do zarządzania obwodami usługi ExpressRoute utworzonymi w testowych lokalizacjach komunikacji równorzędnej na fikcyjnych urządzeniach i portach.

* **ExpressRoute_API_Dev_Customer_Sub:** Ta subskrypcja będzie używana do tworzenia obwodów usługi ExpressRoute w testowych lokalizacjach komunikacji równorzędnej, które są mapowane na fikcyjne urządzenia i porty.

Lokalizacje komunikacji równorzędnej testów: fikcyjne urządzenia i porty nie są domyślnie narażone na działanie klientów produkcyjnych. Aby utworzyć obwody usługi ExpressRoute, które są mapowane do konfiguracji testowej, należy włączyć flagę funkcji subskrypcji.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Zarejestruj subskrypcję Dev_Provider, aby uzyskać dostęp do api expressRouteCrossConnections

Aby uzyskać dostęp do interfejsu API expressRouteCrossConnections, subskrypcja partnera musi być zarejestrowana w **programie Microsoft.Network Resource Provider**. Wykonaj kroki w dostawcy [zasobów platformy Azure i typy](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) artykułu, aby zakończyć proces rejestracji.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Konfigurowanie uwierzytelniania dla wywołań interfejsu API REST usługi Azure Resource Manager

Większość usług platformy Azure wymaga kodu klienta do uwierzytelniania za pomocą Menedżera zasobów przy użyciu prawidłowych poświadczeń przed wywołaniem interfejsów API usługi. Uwierzytelnianie jest koordynowane między różnymi podmiotami przez usługę Azure AD i zapewnia klientowi token dostępu jako dowód uwierzytelniania.

Proces uwierzytelniania obejmuje dwa główne etapy:

1. [Zarejestruj klienta](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Utwórz żądanie dostępu](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Zapewnij uprawnienie współpracownika sieci do aplikacji klienckiej

Po pomyślnym skonfigurowaniu uwierzytelniania należy udzielić współpracownikowi sieci dostępu do aplikacji klienckiej w obszarze Dev_Provider_Sub. Aby udzielić uprawnień, zaloguj się do [witryny Azure portal](https://ms.portal.azure.com/#home) i wykonaj następujące kroki:

1. Przejdź do pozycji Subskrypcje i wybierz Dev_Provider_Sub
2. Przejdź do kontroli dostępu (IAM)
3. Dodawanie przypisania roli
4. Wybieranie roli współautora sieci
5. Przypisywanie dostępu do użytkownika, grupy lub jednostki usługi Azure AD
6. Wybierz aplikację kliencką
7. Zapisz zmiany

### <a name="5-develop"></a>5. Opracowanie

Opracowanie względem [interfejsu API expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>Interfejs API REST

Zobacz [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) dokumentacji interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wszystkich interfejsów API REST usługi ExpressRoute, zobacz [Interfejsy API REST usługi ExpressRoute](https://docs.microsoft.com/rest/api/expressroute/).