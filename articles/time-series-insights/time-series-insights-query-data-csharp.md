---
title: Wykonywanie zapytań o dane ze środowiska Azure czas serii szczegółowych informacji w wersji Ogólnodostępnej przy użyciu C# kodu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wykonywać zapytania o dane ze środowiska usługi Azure Time Series Insights, tworząc niestandardową aplikację w języku C# (C sharp) platformy .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 250dd691c3ef3146d6768123de52bf0628b10e42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66728966"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Wykonywanie zapytań dotyczących danych w środowisku Azure czas serii szczegółowych informacji w wersji Ogólnodostępnej przy użyciuC#

To C# przykład pokazuje, jak wykonywać zapytania o dane ze środowiska Azure czas serii szczegółowych informacji w wersji Ogólnodostępnej.

Przykładowy kod zawiera kilka podstawowych przykładów użycia interfejsu API zapytań:

1. Jako krok przygotowywania należy uzyskać token dostępu za pośrednictwem interfejsu API usługi Azure Active Directory. Przekazać ten token w `Authorization` nagłówku każdego żądania interfejsu API zapytań. Do konfigurowania nieinterakcyjnych aplikacji, zobacz [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). Ponadto upewnij się, że wszystkie stałe, które są zdefiniowane na początku próbki są poprawnie ustawione.
1. Uzyskiwana jest lista środowisk, w których użytkownik ma dostęp. Jedno z tych środowisk zostaje pobrana jako odpowiedniego środowiska, a dodatkowo danych jest wysyłane zapytanie dla tego środowiska.
1. Przykładem żądania HTTPS może być żądanie danych dostępności dla odpowiedniego środowiska.
1. Przykładem żądania gniazda sieci Web może być żądanie danych agregowania zdarzeń dla odpowiedniego środowiska. Żądanie danych obejmuje cały zakres czasu dostępności.

> [!NOTE]
> Przykładowy kod znajduje się w temacie [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Zależności projektu

Dodawanie pakietów NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` i `Newtonsoft.Json`.

## <a name="c-example"></a>Przykład w języku C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wykonywania zapytań, przeczytaj [dokumentację interfejsu API zapytań](/rest/api/time-series-insights/ga-query-api).

- Przeczytaj, jak do [łączenie aplikacji jednostronicowej JavaScript](tutorial-create-tsi-sample-spa.md) do usługi Time Series Insights.