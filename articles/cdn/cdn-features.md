---
title: Porównanie funkcji produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Informacje o funkcjach, które obsługuje każdy produkt Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 540dd51abf8b832194c3814c8198cb72cc60d348
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745454"
---
# <a name="compare-azure-cdn-product-features"></a>Porównanie funkcji produktu Azure CDN

Usługa Azure Content Delivery Network (CDN) obejmuje cztery produkty: **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon**. Aby uzyskać informacje na temat migracji profilu **Azure CDN Standard from Verizon** do profilu **Azure CDN Premium from Verizon**, zobacz artykuł [Migrate an Azure CDN profile from Standard Verizon to Premium Verizon (Migrowanie profilu usługi Azure CDN firmy Verizon z warstwy Standardowa do warstwy Premium)](cdn-migrate.md). Należy pamiętać, że chociaż istnieje ścieżka uaktualnienia od Standard Verizon do Premium Verizon, nie ma mechanizmu konwersji między innymi produktami w tej chwili.

W poniższej tabeli przedstawiono porównanie funkcji dostępnych w poszczególnych produktach.

| **Funkcje wydajności i optymalizacje** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamiczne przyspieszanie witryny](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferowane za pośrednictwem [usługi Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Adaptive Image Compression (Adaptacyjna kompresja obrazu)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Object Prefetch (Pobieranie obiektów z wyprzedzeniem)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Ogólna optymalizacja dostarczania w sieci Web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest mniejszy niż 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optymalizacja przesyłania strumieniowego wideo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | za pośrednictwem ogólnego dostarczania w sieci Web | **&#x2713;**  | za pośrednictwem ogólnego dostarczania w sieci Web |  za pośrednictwem ogólnego dostarczania w sieci Web |
| [Optymalizacja dużych plików](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | za pośrednictwem ogólnego dostarczania w sieci Web | **&#x2713;**, Wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest większy niż 10 MB   | za pośrednictwem ogólnego dostarczania w sieci Web |  za pośrednictwem ogólnego dostarczania w sieci Web |
| Zmień typ optymalizacji | |**&#x2713;** | | |
| Port pochodzenia |Wszystkie porty TCP |[Dozwolone porty początku](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Wszystkie porty TCP |Wszystkie porty TCP |
| [Globalne równoważenia obciążenia serwera (usługa GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Szybkie przeczyszczanie](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Przeczyść wszystkie i przeczyszczanie symboli wieloznacznych nie są obecnie obsługiwane przez usługę Azure CDN firmy Akamai |**&#x2713;** |**&#x2713;** |
| [Wstępne ładowanie zasobów](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [reguł buforowania](cdn-caching-rules.md))  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Konfigurowalny, oparty na regułach aparat dostarczania zawartości |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  | | |**&#x2713;** przy użyciu [aparatu reguł](cdn-rules-engine.md) |
| Ustawienia pamięci podręcznej/nagłówka  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md) | | |**&#x2713;** przy użyciu [silnika reguł Premium](cdn-rules-engine.md) |
| Przekierowanie/przepisanie adresu URL |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md)  | | |**&#x2713;** przy użyciu [silnika reguł Premium](cdn-rules-engine.md) |
| Reguły urządzeń przenośnych  |**&#x2713;** przy użyciu [aparatu reguł standardowych](cdn-standard-rules-engine.md) | | |**&#x2713;** przy użyciu [silnika reguł Premium](cdn-rules-engine.md) |
| [Buforowanie ciągu zapytania](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Podwójny stos protokołów IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Obsługa protokołu HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpieczenia** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Obsługa protokołu HTTPS z punktem końcowym usługi CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Protokół HTTPS domen niestandardowych](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, wymaga bezpośredniego CNAME, aby włączyć |**&#x2713;** |**&#x2713;** |
| [Obsługa niestandardowych nazw domen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrowanie geograficzne](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrona przed DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Użyj własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Obsługiwane wersje TLS | TLS 1.2, TLS 1.0/1.1 - [Konfigurowalny](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
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
| Łatwa integracja z usługami platformy Azure, takimi jak [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) i [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Zarządzanie za pomocą [interfejsu API REST](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)lub [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Typy MIME kompresji](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Tylko domyślnie |Możliwość konfiguracji |Możliwość konfiguracji  |Możliwość konfiguracji  |
| Kodowanie kompresji  |gzip, brotli |Gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






