---
title: Wykonywanie zapytań dotyczących danych ze środowiska GA C# przy użyciu kodu-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights przy użyciu aplikacji C#niestandardowej nadanej w programie.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3729bedf7591ffecc558b88660486f7e336fa717
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705907"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights GA przy użyciuC#

W C# tym przykładzie przedstawiono sposób wykonywania zapytań dotyczących danych w środowisku Azure Time Series Insights ga.

Przykładowy kod zawiera kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowania Uzyskaj token dostępu za pomocą interfejsu API Azure Active Directory. Przekaż ten token w nagłówku `Authorization` każdego żądania interfejsu API zapytań. Aby skonfigurować aplikacje nieinteraktywne, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Upewnij się również, że wszystkie stałe zdefiniowane na początku próbki są prawidłowo ustawione.
1. Zostanie uzyskana lista środowisk, do których użytkownik ma dostęp. Jedno z tych środowisk jest wybierane jako interesujące środowisko i dla tego środowiska są badane dalsze dane.
1. Przykładem żądania HTTPS może być żądanie danych dostępności dla odpowiedniego środowiska.
1. Przykładem żądania gniazda sieci Web może być żądanie danych agregowania zdarzeń dla odpowiedniego środowiska. Żądanie danych obejmuje cały zakres czasu dostępności.

> [!NOTE]
> Przykładowy kod jest dostępny w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Zależności projektu

Dodaj pakiety NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` i `Newtonsoft.Json`.

## <a name="c-example"></a>C#przyklad

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.