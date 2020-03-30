---
title: Model danych telemetrycznych usługi Azure Application Insights — kontekst telemetrii | Dokumenty firmy Microsoft
description: Model danych kontekstu telemetryki usługi Application Insights
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671867"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontekst telemetrii: model danych usługi Application Insights

Każdy element telemetrii może mieć silnie wpisane pola kontekstu. Każde pole umożliwia określony scenariusz monitorowania. Kolekcji właściwości niestandardowych służy do przechowywania informacji kontekstowych niestandardowych lub specyficznych dla aplikacji.


## <a name="application-version"></a>Wersja aplikacji

Informacje w polach kontekstu aplikacji jest zawsze o aplikacji, która wysyła dane telemetryczne. Wersja aplikacji służy do analizowania zmian trendu w zachowaniu aplikacji i jego korelacji z wdrożeniami.

Maksymalna długość: 1024


## <a name="client-ip-address"></a>Adres IP klienta

Adres IP urządzenia klienckiego. Obsługiwane są protokoły IPv4 i IPv6. Gdy dane telemetryczne są wysyłane z usługi, kontekst lokalizacji dotyczy użytkownika, który zainicjował operację w usłudze. Usługa Application Insights wyodrębnia informacje o lokalizacji geograficznej z adresu IP klienta, a następnie obcina ich. Dlatego ip klienta sam nie może być używany jako informacje użytkownika końcowego identyfikowalne. 

Maksymalna długość: 46


## <a name="device-type"></a>Typ urządzenia

Pierwotnie to pole było używane do wskazania typu urządzenia używanego przez użytkownika końcowego aplikacji. Dziś służy głównie do odróżniania danych telemetrycznych JavaScript z typem urządzenia "Przeglądarka" od telemetrii po stronie serwera z typem urządzenia "PC".

Maksymalna długość: 64


## <a name="operation-id"></a>Identyfikator operacji

Unikatowy identyfikator operacji głównej. Ten identyfikator umożliwia grupowanie danych telemetrycznych w wielu składnikach. Zobacz [korelacji telemetrii, aby](../../azure-monitor/app/correlation.md) uzyskać szczegółowe informacje. Identyfikator operacji jest tworzony przez żądanie lub widok strony. Wszystkie inne dane telemetryczne ustawiają to pole na wartość dla zawierającego żądania lub widoku strony. 

Maksymalna długość: 128


## <a name="parent-operation-id"></a>Identyfikator operacji nadrzędnej

Unikatowy identyfikator bezpośredniego elementu nadrzędnego elementu telemetrycznego. Zobacz [korelacji telemetrii, aby](../../azure-monitor/app/correlation.md) uzyskać szczegółowe informacje.

Maksymalna długość: 128


## <a name="operation-name"></a>Nazwa operacji

Nazwa (grupa) operacji. Nazwa operacji jest tworzona przez żądanie lub widok strony. Wszystkie inne elementy telemetryczne ustawiają to pole na wartość zawierającego żądanie lub widok strony. Nazwa operacji służy do znajdowania wszystkich elementów telemetrii dla grupy operacji (na przykład "GET Home/Index"). Ta właściwość kontekstu służy do odpowiedzi na pytania, takie jak "jakie są typowe wyjątki generowane na tej stronie."

Maksymalna długość: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetyczne źródło operacji

Nazwa źródła syntetycznego. Niektóre dane telemetryczne z aplikacji mogą reprezentować ruch syntetyczny. Może to być indeksowanie witryny sieci Web, testy dostępności witryny lub ślady z bibliotek diagnostycznych, takich jak sam SDK usługi Application Insights.

Maksymalna długość: 1024


## <a name="session-id"></a>Identyfikator sesji

Identyfikator sesji — wystąpienie interakcji użytkownika z aplikacją. Informacje w polach kontekstu sesji są zawsze informacje o użytkowniku końcowym. Gdy dane telemetryczne są wysyłane z usługi, kontekst sesji dotyczy użytkownika, który zainicjował operację w usłudze.

Maksymalna długość: 64


## <a name="anonymous-user-id"></a>Anonimowy identyfikator użytkownika

Anonimowy identyfikator użytkownika. Reprezentuje użytkownika końcowego aplikacji. Gdy dane telemetryczne są wysyłane z usługi, kontekst użytkownika dotyczy użytkownika, który zainicjował operację w usłudze.

[Próbkowanie](../../azure-monitor/app/sampling.md) jest jedną z technik minimalizowania ilości zebranych danych telemetrycznych. Algorytm próbkowania próbuje albo próbki lub obecnie wszystkie skorelowane dane telemetryczne. Anonimowy identyfikator użytkownika jest używany do generowania wyników próbkowania. Tak więc anonimowy identyfikator użytkownika powinien być wystarczająco losową wartością. 

Używanie anonimowego identyfikatora użytkownika do przechowywania nazwy użytkownika jest niewłaściwym użyciem tego pola. Użyj uwierzytelnionego identyfikatora użytkownika.

Maksymalna długość: 128


## <a name="authenticated-user-id"></a>Uwierzytelniony identyfikator użytkownika

Uwierzytelniony identyfikator użytkownika. Przeciwieństwem anonimowego identyfikatora użytkownika, to pole reprezentuje użytkownika o przyjaznej nazwie. Ponieważ informacje o pii nie są zbierane domyślnie przez większość SDK.

Maksymalna długość: 1024


## <a name="account-id"></a>Identyfikator konta

W aplikacjach z wieloma dzierżawami jest to identyfikator konta lub nazwa, z którą działa użytkownik. Przykładami może być identyfikator subskrypcji dla witryny Azure portal lub nazwa bloga dla platformy blogów.

Maksymalna długość: 1024


## <a name="cloud-role"></a>Rola w chmurze

Nazwa roli, do. Mapy bezpośrednio do nazwy roli na platformie Azure. Może być również używany do rozróżniania mikro usług, które są częścią jednej aplikacji.

Maksymalna długość: 256


## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa wystąpienia, w którym aplikacja jest uruchomiona. Nazwa komputera dla lokalnego, nazwa wystąpienia platformy Azure.

Maksymalna długość: 256


## <a name="internal-sdk-version"></a>Wewnętrzna: wersja SDK

wersja SDK. Zobacz [ten artykuł,](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) aby uzyskać informacje.

Maksymalna długość: 64


## <a name="internal-node-name"></a>Wewnętrzna: nazwa węzła

To pole reprezentuje nazwę węzła używaną do celów rozliczeniowych. Użyj go, aby zastąpić standardowe wykrywanie węzłów.

Maksymalna długość: 256


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozszerzyć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- Zapoznaj się ze standardową [konfiguracją](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)kolekcji właściwości kontekstu .
