---
title: Napisz kod umożliwiający śledzenie żądań za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Napisz kod umożliwiający śledzenie żądań z usługą Application Insights, dzięki czemu uzyskujesz profile dla Twojej reqeusts
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: fed8c6b694ca18f673f700302bf72f33df4edd93
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142422"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napisz kod umożliwiający śledzenie żądań z usługą Application Insights

Aby wyświetlić profile aplikacji, na stronie wydajności, Application Insights musi być śledzenia żądań dla aplikacji. Usługa Application Insights umożliwia automatyczne śledzenie żądań dla aplikacji, które są tworzone na platformach, które są już zinstrumentowane, takich jak ASP.net i ASP.Net Core. Ale w przypadku innych aplikacji, takich jak role procesów roboczych w usłudze Azure Cloud Services i bezstanowych interfejsów API usługi Service Fabric, trzeba napisać kod, aby poinformować usługę Application Insights, gdzie żądań rozpoczęcia i zakończenia. Po napisaniu ten kod, dane telemetryczne żądań, które będą wysyłane do usługi Application Insights i dane telemetryczne zostaną wyświetlone na stronie wydajność i profile, które zostaną zebrane dla tych żądań. 

Poniżej przedstawiono kroki, które należy wykonać, aby ręcznie śledzić żądania:


  1. Na wczesnym etapie istnienia aplikacji Dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat tej konfiguracji klucza Instrumentacji globalne Zobacz [użycia usługi Service Fabric za pomocą usługi Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Dla dowolnego fragmentu kodu, który ma być instrumentacji, należy dodać `StartOperation<RequestTelemetry>` **USING** instrukcji wokół niego, jak pokazano w poniższym przykładzie:

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
