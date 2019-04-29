---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: ed512c0f56d8da5cb8e47b20f2495054fdedf020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815400"
---
Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Billing](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Yes|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer Proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Nie|[Mounts](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i z kontenera do komputera hosta.|
