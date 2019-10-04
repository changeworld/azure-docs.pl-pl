---
title: Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights GA C# przy użyciu kodu | Microsoft Docs
description: W tym artykule opisano sposób wykonywania zapytań dotyczących danych ze środowiska Azure Time Series Insights przez kodowanie niestandardowej aplikacji zapisaną w C# języku .NET (C-Sharp).
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 353e3463c2be552210847ac3fe17bbfe2cec58c7
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958128"
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

- Przeczytaj, jak [połączyć aplikację jednostronicową JavaScript](tutorial-create-tsi-sample-spa.md) z Time Series Insights.