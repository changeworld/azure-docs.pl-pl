---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712558"
---
`ApplicationInsights` Ustawienie pozwala na dodawanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera. Usługa Application Insights zapewnia szczegółowe monitorowanie kontenera. Możesz łatwo monitorować dostępność, wydajność i użycie kontenera. Możesz również szybko identyfikować i diagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia konfiguracji objęte `ApplicationInsights` sekcji.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienie usługi Application Insights do telemetrii, które są wysyłane dane w kontenerze. Aby uzyskać więcej informacji, zobacz [usługi Application Insights dla platformy ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Przykład:<br>`InstrumentationKey=123456789`|

