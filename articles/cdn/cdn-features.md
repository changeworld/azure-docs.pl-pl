---
title: Funkcje produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Informacje o funkcjach, które obsługuje każdy produkt Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/25/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: ae57d4e9feb23f67bb4f05148d87fec96f055a7f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cdn-product-features"></a>Funkcje produktu Azure CDN

Usługa Azure Content Delivery Network (CDN) obejmuje cztery produkty: **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon**. W poniższej tabeli przedstawiono porównanie funkcji dostępnych w poszczególnych produktach.

| **Funkcje wydajności i optymalizacje** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Przyspieszanie witryn dynamicznych](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Adaptive Image Compression (Adaptacyjna kompresja obrazu)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Object Prefetch (Pobieranie obiektów z wyprzedzeniem)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optymalizacja przesyłania strumieniowego wideo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Optymalizacja dużych plików](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Globalne równoważenia obciążenia serwera (usługa GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Szybkie przeczyszczanie](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Wstępne ładowanie zasobów](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [reguł buforowania](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [aparatu reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Buforowanie ciągu zapytania](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Buforowanie regionalne  |**&#x2713;** |  |  |  |
| Podwójny stos protokołów IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Obsługa protokołu HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Bezpieczeństwo** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Obsługa protokołu HTTPS z punktem końcowym usługi CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Protokół HTTPS domen niestandardowych](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Obsługa niestandardowych nazw domen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrowanie geograficzne](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrona przed atakami DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Użyj własnego certyfikatu](cdn-custom-ssl.md#option-2-enable-the-https-feature-with-your-own-certificate) |**&#x2713;** |  |  |  |
||||
| **Analiza i raportowanie** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Raporty podstawowe z usługi Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Wydajność węzła brzegowego](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alerty w czasie rzeczywistym](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Łatwość obsługi** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Łatwa integracja z usługami platformy Azure, takimi jak [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md) i [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Zarządzanie za pomocą [interfejsu API REST](https://msdn.microsoft.com/library/mt634456.aspx), [platformy .NET](cdn-app-dev-net.md), [środowiska Node.js](cdn-app-dev-node.md) lub [programu PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Dostosowywalny, oparty na regułach aparat dostarczania zawartości](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Adres URL przekierowania/ponownego napisania (przy użyciu [aparatu reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Zasady urządzeń przenośnych (przy użyciu [aparatu reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* Firmy Microsoft i Verizon obsługują bezpośrednie dostarczanie dużych plików i multimediów za pomocą optymalizacji ogólnego dostarczania w Internecie.



