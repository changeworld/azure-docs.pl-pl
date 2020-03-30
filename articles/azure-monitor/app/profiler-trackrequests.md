---
title: Pisanie kodu w celu śledzenia żądań za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Napisz kod do śledzenia żądań za pomocą usługi Application Insights, dzięki czemu można uzyskać profile dla żądań.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671600"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Pisanie kodu w celu śledzenia żądań za pomocą usługi Application Insights

Aby wyświetlić profile aplikacji na stronie Wydajność usługi Azure Application Insights, usługa Azure Application Insights musi śledzić żądania dotyczące aplikacji. Usługa Application Insights może automatycznie śledzić żądania dla aplikacji, które są oparte na już instrumentowanych ramach. Dwa przykłady to ASP.NET i ASP.NET Core. 

W przypadku innych aplikacji, takich jak role procesowe usług Azure Cloud Services i bezstanowe interfejsy API sieci szkieletowej usług, należy napisać kod, aby poinformować usługę Application Insights, gdzie żądania rozpoczynają się i kończą. Po napisaniu tego kodu żądania danych telemetrycznych są wysyłane do usługi Application Insights. Można wyświetlić dane telemetryczne na stronie Wydajność, a profile są zbierane dla tych żądań. 

Aby ręcznie śledzić żądania, wykonaj następujące czynności:

  1. Na początku okresu istnienia aplikacji dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat tej globalnej konfiguracji klucza instrumentacji, zobacz [Używanie sieci szkieletowej usług z aplikacją Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Dla każdego fragmentu kodu, który chcesz `StartOperation<RequestTelemetry>` zainstruować, dodaj **instrukcję using** wokół niego, jak pokazano w poniższym przykładzie:

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

        Wywołanie `StartOperation<RequestTelemetry>` `StartOperation<RequestTelemetry>` w innym zakresie nie jest obsługiwane. Zamiast tego `StartOperation<DependencyTelemetry>` można użyć w zakresie zagnieżdżonego. Przykład:  
        
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
