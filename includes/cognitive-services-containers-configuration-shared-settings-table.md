---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: 4f9300ab1d688e1f5043f5b919e70c5a36c7c0e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064010"
---
Kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Śledzi informacje dotyczące rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Billing](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Yes|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|Serwer Proxy http|Konfiguruje serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Nie|[Mounts](#mount-settings)|Odczytuje i zapisuje dane z komputera hosta do kontenera i z kontenera, wróć do komputera hosta.|
