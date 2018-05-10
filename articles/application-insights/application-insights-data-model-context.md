---
title: Modelu danych Telemetrii wgląd aplikacji Azure — kontekstu Telemetrii | Dokumentacja firmy Microsoft
description: Model danych kontekstu aplikacji Insights telemetrii
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz; mbullwin
ms.openlocfilehash: f6654dc869b6c1a3f91ee8dee44dcdbf4bdfc5fd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontekst danych telemetrii: model danych usługi Application Insights

Każdy element danych telemetrycznych może mieć pól jednoznacznie kontekstu. Każde pole umożliwia scenariusza monitorowania. Przechowywać informacje kontekstowe niestandardowych lub specyficznych dla aplikacji, należy użyć kolekcji właściwości niestandardowych.


## <a name="application-version"></a>Wersja aplikacji

Informacje w polach kontekstu aplikacji jest zawsze o aplikacji, który wysyła dane telemetryczne. Wersja aplikacji jest używany do analizowania trend zmiany zachowania aplikacji i jej korelacji do wdrożenia.

Maksymalna długość: 1024


## <a name="client-ip-address"></a>Adres IP klienta

Adres IP urządzenia klienckiego. IPv4 i IPv6 są obsługiwane. Podczas wysyłania danych telemetrycznych z usługą, kontekst lokalizacji jest dotyczące użytkownika, który zainicjował operację usługi. Usługa Application Insights wyodrębnienia informacji o lokalizacji geograficznej z adresu IP klienta i następnie obcięcie go. Dlatego IP klienta samodzielnie nie można użyć jako informacje umożliwiające identyfikację użytkownika końcowego. 

Maksymalna długość: 46


## <a name="device-type"></a>Typ urządzenia

Pierwotnie to pole zostało służy do wskazania typu urządzenia, które jest używane przez użytkownika końcowego aplikacji. Obecnie jest używany głównie w celu odróżnienia telemetrii JavaScript z typem urządzenia telemetrii po stronie serwera z urządzeniem "Browser" typu "Komputer".

Maksymalna długość: 64


## <a name="operation-id"></a>Identyfikator operacji

Unikatowy identyfikator działania głównego. Ten identyfikator umożliwia telemetrii grupy między kilka składników. Zobacz [korelacji telemetrii](application-insights-correlation.md) szczegółowe informacje. Identyfikator operacji jest tworzony przez żądanie lub widok strony. Wszystkie inne dane telemetryczne ustawia tego pola wartość dla widoku zawierającego żądanie lub strony. 

Maksymalna długość: 128


## <a name="parent-operation-id"></a>Identyfikator działania nadrzędnego

Unikatowy identyfikator elementu telemetrii natychmiastowego nadrzędnego. Zobacz [korelacji telemetrii](application-insights-correlation.md) szczegółowe informacje.

Maksymalna długość: 128


## <a name="operation-name"></a>Nazwa operacji

Nazwa (grupa) operacji. Nazwa operacji jest tworzony przez żądanie lub widok strony. Wszystkie pozostałe elementy danych telemetrycznych ustawioną wartość dla widoku zawierającego żądanie lub strony w tym polu. Nazwa operacji służy do znajdowania wszystkie elementy danych telemetrycznych dla grupy działań (na przykład "GET głównej/Index"). Ta właściwość kontekstu jest używana w odpowiedzi na pytania, takich jak "jakie są typowe wyjątki wyrzucane na tej stronie."

Maksymalna długość: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetyczne źródło operacji

Nazwa źródła syntetycznych. Niektóre dane telemetryczne z aplikacji może reprezentować ruchu syntetycznego. Może to być indeksowania witryny sieci web, testy dostępności lokacji lub śladów z diagnostycznych bibliotek, takich jak Insights zestaw SDK usługi Application sam przeszukiwarkę sieci web.

Maksymalna długość: 1024


## <a name="session-id"></a>Identyfikator sesji

Identyfikator sesji - wystąpienie interakcji z aplikacją. Informacje w polach kontekstu sesji jest zawsze o użytkownika końcowego. Po wysłaniu danych telemetrycznych z usługą kontekstu sesji dotyczy użytkownika, który zainicjował operację usługi.

Maksymalna długość: 64


## <a name="anonymous-user-id"></a>Identyfikator użytkownika anonimowego

Identyfikator użytkownika anonimowego. Reprezentuje użytkownika końcowego w aplikacji. Po wysłaniu danych telemetrycznych z usługą kontekstu użytkownika dotyczy użytkownika, który zainicjował operację usługi.

[Próbkowanie](app-insights-sampling.md) jest jedną z metod, aby zminimalizować ilość zbieranych danych telemetrycznych. Algorytm próbkowania podejmuje próbę albo próbki przychodzący lub wychodzący wszystkie dane telemetryczne skorelowane. Identyfikator użytkownika anonimowego jest używana do próbkowania generowania oceny. Dlatego użytkownik anonimowy identyfikator powinien być wystarczająco losowych wartości. 

Za pomocą identyfikatora użytkownika anonimowego do przechowywania nazwy użytkownika jest nieprawidłowe użycie pola. Za pomocą identyfikatora użytkownika uwierzytelnione.

Maksymalna długość: 128


## <a name="authenticated-user-id"></a>Identyfikator użytkownika uwierzytelnionego

Identyfikator uwierzytelnionego użytkownika. Identyfikator użytkownika anonimowego i na odwrót to pole reprezentuje użytkownika o przyjaznej nazwie. Od momentu jego osobowe go nie są zbierane domyślnie przez większość zestaw SDK.

Maksymalna długość: 1024


## <a name="account-id"></a>Identyfikator konta

W aplikacjach wielodostępne jest identyfikator konta lub nazwa użytkownika jest działające z. Przykładami mogą być identyfikator subskrypcji platformy Azure portalu lub blogu nazwa obsługi blogów.

Maksymalna długość: 1024


## <a name="cloud-role"></a>Rola w chmurze

Nazwa roli aplikacji jest częścią. Mapy bezpośrednio do nazw ról na platformie azure. Można również odróżnienie micro usług, które są częścią pojedynczej aplikacji.

Maksymalna długość: 256


## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa wystąpienia, w którym aplikacja jest uruchomiona. Nazwa komputera lokalnego, aby uzyskać nazwę wystąpienia dla platformy Azure.

Maksymalna długość: 256


## <a name="internal-sdk-version"></a>Wewnętrzne: Wersja zestawu SDK

Wersja zestawu SDK. Zobacz https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification informacji.

Maksymalna długość: 64


## <a name="internal-node-name"></a>Wewnętrzne: Nazwa węzła

To pole reprezentuje nazwę węzła używana do celów rozliczeń. Użyj go, aby przesłonić standardowe wykrywanie węzłów.

Maksymalna długość: 256


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [rozszerzanie i filtrować dane telemetryczne](app-insights-api-filtering-sampling.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- Zapoznaj się z kolekcji właściwości kontekstu standardowe [konfiguracji](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
