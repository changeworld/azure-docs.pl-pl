---
title: Model danych Telemetrii aplikacji platformy Azure szczegółowe informacje — kontekstu danych Telemetrycznych | Dokumentacja firmy Microsoft
description: Model danych kontekstu danych telemetrycznych Insights aplikacji
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: b6cfae20f09b19a57cf411777e78abb1dbbf0484
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649646"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontekst danych telemetrycznych: model danych usługi Application Insights

Każdego elementu telemetrii mogą mieć pola silnie typizowaną kontekstu. Każde pole umożliwia konkretnego scenariusza monitorowania. Użyj kolekcji właściwości niestandardowych do przechowywania informacji kontekstowych o niestandardowych lub specyficznych dla aplikacji.


## <a name="application-version"></a>Wersja aplikacji

Informacje w polach kontekst aplikacji jest zawsze dotyczące aplikacji, która wysyła dane telemetryczne. Wersja aplikacji jest używany do analizowania trend zmiany zachowania aplikacji i jej korelacji do wdrożeń.

Maksymalna długość: 1024


## <a name="client-ip-address"></a>Adres IP klienta

Adres IP urządzenia klienckiego. IPv4 i IPv6 są obsługiwane. Gdy dane telemetryczne są wysyłane z usługi, kontekst lokalizacji jest o użytkowniku, który zainicjował operację usługi. Usługa Application Insights wyodrębnić informacji o lokalizacji geograficznej z adresu IP klienta, a następnie go obciąć. Dlatego adres IP klienta przez siebie nie można użyć jako informacje umożliwiające identyfikację użytkownika końcowego. 

Maksymalna długość: 46


## <a name="device-type"></a>Typ urządzenia

Pierwotnie to pole było używane do wskazać typ urządzenia, które użytkownik aplikacji za pomocą. Obecnie używane głównie w celu odróżnienia JavaScript dane telemetryczne z typem urządzenia "Przeglądarka" z telemetrii po stronie serwera za pomocą urządzenia typu "Komputer".

Maksymalna długość: 64


## <a name="operation-id"></a>Identyfikator operacji

Unikatowy identyfikator operacji głównej. Ten identyfikator umożliwia do telemetrii grupy między kilka składników. Zobacz [korelacja telemetrii](application-insights-correlation.md) Aby uzyskać szczegółowe informacje. Identyfikator operacji jest tworzony przez żądanie lub wyświetlenia strony. Wszystkie inne dane telemetryczne ustawia tego pola wartość zawierającą żądanie lub wyświetlenie strony. 

Maksymalna długość: 128


## <a name="parent-operation-id"></a>Identyfikator operacji nadrzędnej

Unikatowy identyfikator elementu telemetrii bezpośredni obiekt nadrzędny. Zobacz [korelacja telemetrii](application-insights-correlation.md) Aby uzyskać szczegółowe informacje.

Maksymalna długość: 128


## <a name="operation-name"></a>Nazwa operacji

Nazwa (grupa) operacji. Nazwa operacji jest tworzony przez żądanie lub wyświetlenia strony. Wszystkie pozostałe elementy danych telemetrycznych ustaw wartość zawierającą żądanie lub wyświetlenie strony tego pola. Nazwa operacji jest używana do znajdowania wszystkich elementów danych telemetrycznych dla grupy operacji (na przykład "GET Home/Index"). Ta właściwość kontekstu jest używana w odpowiedzi na pytania, takie jak "co to są typowe wyjątki generowane na tej stronie."

Maksymalna długość: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetyczne źródło działania

Nazwa źródła syntetycznych. Dane telemetryczne z aplikacji może reprezentować ruchu syntetycznego. Może to być przeszukiwarkę sieci web indeksowania witryny sieci web, testy dostępności witryny lub śladów od bibliotek diagnostycznych, takich jak zestaw SDK Application Insights sam.

Maksymalna długość: 1024


## <a name="session-id"></a>Identyfikator sesji

Identyfikator sesji — wystąpienie interakcji użytkownika z aplikacją. Informacje w polach kontekstu sesji jest zawsze o użytkownika końcowego. Gdy dane telemetryczne są wysyłane z usługi, kontekstu sesji dotyczy użytkownika, który zainicjował operację usługi.

Maksymalna długość: 64


## <a name="anonymous-user-id"></a>Identyfikator użytkownika anonimowego

Identyfikator użytkownika anonimowego. Reprezentuje użytkownika końcowego aplikacji. Gdy dane telemetryczne są wysyłane z usługi, kontekstu użytkownika dotyczy użytkownika, który zainicjował operację usługi.

[Próbkowanie](app-insights-sampling.md) to jedna z technik, aby zminimalizować ilość zebranych danych telemetrycznych. Algorytm próbkowania podejmuje próbę albo przykładowe wewnątrz lub na zewnątrz skorelowana telemetria. Identyfikator użytkownika anonimowego jest używany do pobierania próbek generowania oceny. Dlatego użytkownik anonimowy identyfikator powinien być wystarczająco losową wartość. 

Przy użyciu identyfikatora użytkownika anonimowego do przechowywania nazwy użytkownika jest nadużycie pola. Użyj uwierzytelniony identyfikator użytkownika.

Maksymalna długość: 128


## <a name="authenticated-user-id"></a>Identyfikator użytkownika uwierzytelnionego

Identyfikator użytkownika uwierzytelnionego. Identyfikator użytkownika anonimowego i na odwrót to pole reprezentuje użytkownika o przyjaznej nazwie. Od momentu jego osobowe go nie są zbierane Domyślnie większość zestawu SDK.

Maksymalna długość: 1024


## <a name="account-id"></a>Identyfikator konta

W aplikacjach wielodostępnych jest to identyfikator konta lub nazwę, którą działa użytkownik za pomocą. Przykładami mogą być identyfikator subskrypcji platformy Azure portal lub w blogu nazwy do obsługi blogów.

Maksymalna długość: 1024


## <a name="cloud-role"></a>Rola w chmurze

Nazwa roli aplikacji jest częścią. Mapowania bezpośrednio na nazwę roli na platformie azure. Można również do odróżnienia wczesnych usług, które są częścią pojedynczej aplikacji.

Maksymalna długość: 256


## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa wystąpienia, gdzie aplikacja jest uruchomiona. Nazwa komputera dla lokalnego, nazwę wystąpienia dla platformy Azure.

Maksymalna długość: 256


## <a name="internal-sdk-version"></a>Wewnętrzne: Wersja zestawu SDK

Wersja zestawu SDK. Zobacz https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification informacji.

Maksymalna długość: 64


## <a name="internal-node-name"></a>Wewnętrzne: Nazwa węzła

To pole reprezentuje nazwę węzła używane na potrzeby rozliczeń. Dzięki niemu przesłonić standardowe wykrywanie węzłów.

Maksymalna długość: 256


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [rozszerzyć i filtrowanie danych telemetrycznych](app-insights-api-filtering-sampling.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.
- Zapoznaj się z kolekcji właściwości kontekstu standardowa [konfiguracji](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
