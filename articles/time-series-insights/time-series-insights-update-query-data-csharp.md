---
title: Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights w C# wersji zapoznawczej przy użyciu kodu | Microsoft Docs
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
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6d2395dcfae5d512412048a683a7f1397967499e
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978652"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights w wersji zapoznawczej przy użyciuC#

W C# tym przykładzie pokazano, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights wersja zapoznawcza.

W przykładzie przedstawiono kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowania Uzyskaj token dostępu za pomocą interfejsu API Azure Active Directory. Przekaż ten token w nagłówku `Authorization` każdego żądania interfejsu API zapytań. Aby skonfigurować aplikacje nieinteraktywne, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Upewnij się również, że wszystkie stałe zdefiniowane na początku próbki są prawidłowo ustawione.
1. Zostanie uzyskana lista środowisk, do których użytkownik ma dostęp. Jedno z tych środowisk jest wybierane jako interesujące środowisko i dla tego środowiska są badane dalsze dane.
1. Przykładem żądania protokołu HTTPS jest żądanie danych dostępności dla interesującego Cię środowiska.
1. Przykładem żądania usługi Web Socket jest żądanie danych agregowania zdarzeń dla interesującego Cię środowiska. Dla całego zakresu czasu dostępności żądano danych.

> [!NOTE]
> Ten przykładowy kod jest również dostępny w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#przyklad

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Przykładowy kod można uruchomić bez zmiany domyślnych wartości środowiskowych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.