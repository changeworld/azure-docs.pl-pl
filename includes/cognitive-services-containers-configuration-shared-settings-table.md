---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479015"
---
Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Rozliczenia](#billing-setting)|Określa punkt końcowy identyfikator URI zasobu usługi na platformie Azure.|
|Yes|[Umowa licencyjna](#eula-setting)| Wskazuje, zaakceptowane licencji dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer Proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Rejestrowanie](#logging-settings)|Udostępnia platformy ASP.NET Core rejestrowania pomocy technicznej dla usługi kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i kontener wróć do komputera-hosta.|
