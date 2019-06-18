---
title: Napisz kod umożliwiający śledzenie żądań za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Napisz kod umożliwiający śledzenie żądań z usługą Application Insights, dzięki czemu możesz też uzyskać profile swoich żądań.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730858"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napisz kod umożliwiający śledzenie żądań z usługą Application Insights

Aby wyświetlić profile aplikacji, na stronie wydajności, usługi Azure Application Insights musi śledzić żądania dla aplikacji. Usługa Application Insights umożliwia automatyczne śledzenie żądań dla aplikacji, które są oparte na już zinstrumentowane struktur. Dwa przykłady to ASP.NET i ASP.NET Core. 

Dla innych aplikacji, takich jak role procesów roboczych usług Azure Cloud Services i interfejsów API bezstanowej usługi Service Fabric musisz napisać kod, aby poinformować usługę Application Insights, gdzie rozpocząć żądań i zakończenia. Po wpisaniu tego kodu, dane telemetryczne żądania są wysyłane do usługi Application Insights. Telemetrię można wyświetlić na stronie wydajność i profile są zbierane dla tych żądań. 

Aby ręcznie śledzić żądania, wykonaj następujące czynności:

  1. Na wczesnym etapie istnienia aplikacji Dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat tej konfiguracji klucza Instrumentacji globalne Zobacz [użycia usługi Service Fabric za pomocą usługi Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Dla dowolnego fragmentu kodu, który ma być instrumentacji, należy dodać `StartOperation<RequestTelemetry>` **przy użyciu** instrukcji wokół niego, jak pokazano w poniższym przykładzie:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Wywoływanie `StartOperation<RequestTelemetry>` w innym `StartOperation<RequestTelemetry>` zakres nie jest obsługiwany. Możesz użyć `StartOperation<DependencyTelemetry>` w zagnieżdżonej zamiast tego zakresu. Na przykład:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
