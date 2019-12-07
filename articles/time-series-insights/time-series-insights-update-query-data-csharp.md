---
title: Wykonywanie zapytań dotyczących danych ze środowiska w C# wersji zapoznawczej przy użyciu polecenia-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights przy użyciu aplikacji C#, która została zapisywana w.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 20c1f1f9a8b0b0ef105893e44c9daaeae68604db
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889757"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights w wersji zapoznawczej przy użyciuC#

W C# tym przykładzie pokazano, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights wersja zapoznawcza.

Przykładowy kod zawiera kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowania Uzyskaj token dostępu za pomocą interfejsu API Azure Active Directory. Przekaż ten token w nagłówku `Authorization` każdego żądania interfejsu API zapytań. Aby skonfigurować aplikacje nieinteraktywne, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Upewnij się również, że wszystkie stałe zdefiniowane na początku próbki są prawidłowo ustawione.
1. Zostanie uzyskana lista środowisk, do których użytkownik ma dostęp. Jedno z tych środowisk jest wybierane jako interesujące środowisko i dla tego środowiska są badane dalsze dane.
1. Przykładem żądania HTTPS może być żądanie danych dostępności dla odpowiedniego środowiska.
1. Zawiera przykład obsługi autogeneracji zestawu SDK z [usługi Azure AutoRest](https://github.com/Azure/AutoRest).

> [!NOTE]
> Przykładowy kod oraz czynności do skompilowania i uruchomienia są dostępne w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#przyklad

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Przykładowy kod można uruchomić bez zmiany domyślnych wartości środowiskowych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.
