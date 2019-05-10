---
title: Wykonywanie zapytań o dane ze środowiska Azure czas Series Insights w wersji zapoznawczej za pomocą C# kodu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wykonywać zapytania o dane ze środowiska usługi Azure Time Series Insights, tworząc niestandardową aplikację w języku C# (C sharp) platformy .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: ebd5cb92b510da56446ca9e559b03a56cb2af7cf
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515282"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Wykonywanie zapytań dotyczących danych z przy użyciu środowiska Azure czas Series Insights w wersji zapoznawczejC#

To C# przykład pokazuje, jak wykonywać zapytania o dane ze środowiska Azure czas Series Insights w wersji zapoznawczej.

Przykładowy kod zawiera kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowywania należy uzyskać token dostępu za pośrednictwem interfejsu API usługi Azure Active Directory. Przekazać ten token w `Authorization` nagłówku każdego żądania interfejsu API zapytań. Do konfigurowania nieinterakcyjnych aplikacji, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Ponadto upewnij się, że wszystkie stałe, które są zdefiniowane na początku próbki są poprawnie ustawione.
1. Uzyskiwana jest lista środowisk, w których użytkownik ma dostęp. Jedno z tych środowisk zostaje pobrana jako odpowiedniego środowiska, a dodatkowo danych jest wysyłane zapytanie dla tego środowiska.
1. Przykładem żądania HTTPS może być żądanie danych dostępności dla odpowiedniego środowiska.
1. Przykładem żądania gniazda sieci Web może być żądanie danych agregowania zdarzeń dla odpowiedniego środowiska. Żądanie danych obejmuje cały zakres czasu dostępności.

> [!NOTE]
> Ten przykładowy kod jest również dostępna w [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Przykład w języku C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Powyższym przykładowym kodzie można uruchomić bez zmiany ich wartości domyślne środowisko.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wykonywania zapytań, przeczytaj [dokumentację interfejsu API zapytań](/rest/api/time-series-insights/preview-query).

- Przeczytaj, jak do [łączenie aplikacji jednostronicowej JavaScript](tutorial-create-tsi-sample-spa.md) do usługi Time Series Insights.