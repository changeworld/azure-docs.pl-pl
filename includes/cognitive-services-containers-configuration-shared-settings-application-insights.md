---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712558"
---
To `ApplicationInsights` ustawienie umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera. Usługa Application Insights zapewnia szczegółowe monitorowanie kontenera. Kontener można łatwo monitorować pod kątem dostępności, wydajności i użycia. Można również szybko zidentyfikować i zdiagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia `ApplicationInsights` konfiguracji obsługiwane w sekcji.

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz instrumentacji wystąpienia usługi Application Insights, do którego są wysyłane dane telemetryczne dla kontenera. Aby uzyskać więcej informacji, zobacz [Usługa Application Insights dla ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Przykład:<br>`InstrumentationKey=123456789`|

