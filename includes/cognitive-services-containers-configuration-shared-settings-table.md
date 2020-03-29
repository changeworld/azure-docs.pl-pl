---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484106"
---
Kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[Klawisz ApiKey](#apikey-configuration-setting)|Śledzi informacje rozliczeniowe.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Biegle](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, dane metryki na serwerze Fluentd.|
|Nie|HTTP Proxy|Konfiguruje serwer proxy HTTP do wysyłania żądań wychodzących.|
|Nie|[Rejestrowanie](#logging-settings)|Zapewnia ASP.NET podstawową obsługę rejestrowania kontenera. |
|Nie|[Wierzchowce](#mount-settings)|Odczytuje i zapisuje dane z komputera-hosta do kontenera i z kontenera z powrotem do komputera hosta.|
