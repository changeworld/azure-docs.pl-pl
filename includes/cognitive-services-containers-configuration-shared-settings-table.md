---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 57e54c4c3b8ae44d42051c87356f1feeaa5a3f10
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977212"
---
Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Rozliczenia](#billing-setting)|Określa punkt końcowy identyfikator URI zasobu usługi na platformie Azure.|
|Yes|[Umowa licencyjna](#eula-setting)| Wskazuje, zaakceptowane licencji dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Rejestrowanie](#logging-settings)|Udostępnia platformy ASP.NET Core rejestrowania pomocy technicznej dla usługi kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i kontener wróć do komputera-hosta.|
