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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 66c79bed59c8966156d6f000e74e5300edc0245a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883921"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights GA przy użyciuC#

W C# tym przykładzie przedstawiono sposób wykonywania zapytań dotyczących danych w środowisku Azure Time Series Insights ga.

Przykładowy kod zawiera kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowania Uzyskaj token dostępu za pomocą interfejsu API Azure Active Directory. Przekaż ten token w `Authorization` nagłówku każdego żądania interfejsu API zapytania. Aby skonfigurować aplikacje nieinteraktywne, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Upewnij się również, że wszystkie stałe zdefiniowane na początku próbki są prawidłowo ustawione.
1. Zostanie uzyskana lista środowisk, do których użytkownik ma dostęp. Jedno z tych środowisk jest wybierane jako interesujące środowisko i dla tego środowiska są badane dalsze dane.
1. Przykładem żądania HTTPS może być żądanie danych dostępności dla odpowiedniego środowiska.
1. Przykładem żądania gniazda sieci Web może być żądanie danych agregowania zdarzeń dla odpowiedniego środowiska. Żądanie danych obejmuje cały zakres czasu dostępności.

> [!NOTE]
> Przykładowy kod jest dostępny pod adresem [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Zależności projektu

Dodaj pakiety `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet i `Newtonsoft.Json`.

## <a name="c-example"></a>C#przyklad

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Przeczytaj, jak [połączyć aplikację jednostronicową JavaScript](tutorial-create-tsi-sample-spa.md) z Time Series Insights.