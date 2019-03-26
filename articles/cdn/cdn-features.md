---
title: Porównanie funkcji produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Informacje o funkcjach, które obsługuje każdy produkt Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/25/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 0d0b3b38fe39e32c6a3c7c39de5cbed4f7400d92
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435896"
---
# <a name="compare-azure-cdn-product-features"></a>Porównanie funkcji produktu Azure CDN

Istnieją cztery produkty Azure Content Delivery Network (CDN): **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon**. Aby uzyskać informacje na temat migracji profilu **Azure CDN Standard from Verizon** do profilu **Azure CDN Premium from Verizon**, zobacz artykuł [Migrate an Azure CDN profile from Standard Verizon to Premium Verizon (Migrowanie profilu usługi Azure CDN firmy Verizon z warstwy Standardowa do warstwy Premium)](cdn-migrate.md).

W poniższej tabeli przedstawiono porównanie funkcji dostępnych w poszczególnych produktach.

| **Funkcje wydajności i optymalizacje** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Przyspieszanie witryn dynamicznych](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferowane za pośrednictwem [usługi platformy Azure drzwi](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Adaptive Image Compression (Adaptacyjna kompresja obrazu)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Przyspieszanie witryn dynamicznych — Object Prefetch (Pobieranie obiektów z wyprzedzeniem)](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optymalizacja dostarczania ogólnych sieci web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Wybierz ten typ optymalizacji, jeśli Twoja średni rozmiar plików jest mniejsza niż 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optymalizacja przesyłania strumieniowego wideo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | za pomocą dostarczania w sieci Web ogólne | **&#x2713;**  | za pomocą dostarczania w sieci Web ogólne |  za pomocą dostarczania w sieci Web ogólne |
| [Optymalizacja dużych plików](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | za pomocą dostarczania w sieci Web ogólne | **&#x2713;**, Wybierz ten typ optymalizacji, jeśli Twoja średni rozmiar pliku jest większy niż 10 MB   | za pomocą dostarczania w sieci Web ogólne |  za pomocą dostarczania w sieci Web ogólne |
| Zmień typ optymalizacji | |**&#x2713;** | | |
| Port źródła |Wszystkie porty TCP |[Porty dozwolone pochodzenie](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Wszystkie porty TCP |Wszystkie porty TCP |
| [Globalne równoważenia obciążenia serwera (usługa GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Szybkie przeczyszczanie](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Przeczyść wszystkie i przeczyszczanie symboli wieloznacznych nie są obsługiwane przez usługę Azure CDN from Akamai obecnie |**&#x2713;** |**&#x2713;** |
| [Wstępne ładowanie zasobów](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [reguł buforowania](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Możliwego do dostosowania reguł na podstawie aparat dostarczania zawartości (przy użyciu [aparat reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Ustawienia pamięci podręcznej/nagłówka (przy użyciu [aparatu reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Adres URL przekierowania/ponownego napisania (przy użyciu [aparat reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Zasady urządzeń przenośnych (przy użyciu [aparatu reguł](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Buforowanie ciągu zapytania](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Podwójny stos protokołów IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Obsługa protokołu HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Bezpieczeństwo** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Obsługa protokołu HTTPS z punktem końcowym usługi CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Protokół HTTPS domen niestandardowych](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Obsługa niestandardowych nazw domen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrowanie geograficzne](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrona przed atakami DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Użyj własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
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
| Zarządzanie za pomocą [interfejsu API REST](https://msdn.microsoft.com/library/mt634456.aspx), [platformy .NET](cdn-app-dev-net.md), [środowiska Node.js](cdn-app-dev-node.md) lub [programu PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Typy MIME kompresji](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Domyślnie tylko |Można konfigurować |Można konfigurować  |Można konfigurować  |
| Kompresja kodowania  |gzip, brotli |Gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






