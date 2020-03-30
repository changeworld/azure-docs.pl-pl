---
title: Zarządzanie zasadami buforowania usługi Azure CDN w usłudze Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zarządzać zasadami buforowania usługi Azure CDN w usłudze Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74892586"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Zarządzanie zasadami buforowania usługi Azure CDN w usłudze Azure Media Services
Usługa Azure Media Services zapewnia adaptacyjne przesyłanie strumieniowe oparte na protokoczowym strumieniu i pobieranie progresywne. Przesyłanie strumieniowe oparte na protokoii HTTP jest wysoce skalowalne dzięki korzyściom związanym z buforowaniem w warstwach proxy i CDN, a także buforowaniem po stronie klienta. Punkty końcowe przesyłania strumieniowego zapewnia ogólne możliwości przesyłania strumieniowego, a także konfigurację nagłówków pamięci podręcznej HTTP. Punkty końcowe przesyłania strumieniowego ustawia HTTP Cache-Control: max-age i Wygasa nagłówki. Więcej informacji na temat nagłówków pamięci podręcznej HTTP można uzyskać z [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Domyślne nagłówki buforowania
Domyślnie punkty końcowe przesyłania strumieniowego stosują 3-dniowe nagłówki pamięci podręcznej dla danych przesyłania strumieniowego na żądanie (rzeczywiste fragmenty/fragmenty nośnika) i manifestu(listy odtwarzania). W przypadku przesyłania strumieniowego na żywo punkty końcowe przesyłania strumieniowego stosują 3-dniowe nagłówki pamięci podręcznej dla danych (rzeczywiste fragmenty multimediów/fragmentów) i 2-sekundowy nagłówek pamięci podręcznej dla żądań manifestu (listy odtwarzania). Gdy program na żywo włącza się na żądanie (archiwum na żywo), a następnie na żądanie nagłówki pamięci podręcznej przesyłania strumieniowego mają zastosowanie.

## <a name="azure-cdn-integration"></a>Integracja z usługą Azure CDN
Usługa Azure Media Services udostępnia [zintegrowaną sieć CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) dla punktów końcowych przesyłania strumieniowego. Nagłówki kontroli pamięci podręcznej stosuje się w taki sam sposób, jak punkty końcowe przesyłania strumieniowego do punktów końcowych obsługujących usługę CDN przesyłania strumieniowego. Usługa Azure CDN używa wartości pamięci podręcznej skonfigurowanego punktu końcowego przesyłania strumieniowego do definiowania czasu życia wewnętrznie buforowanych obiektów, a także używa tej wartości do ustawiania nagłówków pamięci podręcznej dostarczania. Podczas korzystania z cdn włączone przesyłania strumieniowego punktów końcowych nie jest zalecane, aby ustawić małe wartości pamięci podręcznej. Ustawienie małych wartości zmniejsza wydajność i zmniejsza korzyści z sieci CDN. Nie można ustawić nagłówków pamięci podręcznej mniejszych niż 600 sekund dla punktów końcowych obsługujących usługę CDN przesyłania strumieniowego.

> [!IMPORTANT]
>Usługa Azure Media Services ma pełną integrację z usługą Azure CDN. Za pomocą jednego kliknięcia można zintegrować wszystkich dostępnych dostawców usługi Azure CDN do punktu końcowego przesyłania strumieniowego, w tym produktów standardowych i premium. Aby uzyskać więcej informacji, zobacz to [ogłoszenie](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Opłaty za dane z punktu końcowego przesyłania strumieniowego do usługi CDN są wyłączone tylko wtedy, gdy sieć CDN jest włączona za pośrednictwem interfejsów API punktu końcowego przesyłania strumieniowego lub przy użyciu sekcji punktu końcowego przesyłania strumieniowego usługi Azure portal. Ręczna integracja lub bezpośrednie tworzenie punktu końcowego sieci CDN przy użyciu interfejsów API sieci CDN lub sekcji portalu nie powoduje wyłączenia opłat za dane.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurowanie nagłówków pamięci podręcznej za pomocą usługi Azure Media Services
Za pomocą witryny Azure portal lub interfejsów API usługi Azure Media Services można skonfigurować wartości nagłówka pamięci podręcznej.

1. Aby skonfigurować nagłówki pamięci podręcznej przy użyciu witryny Azure Portal, zobacz [Jak zarządzać punktami końcowymi przesyłania strumieniowego](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) sekcji Konfigurowanie punktu końcowego przesyłania strumieniowego.
2. Interfejs API REST usługi Azure Media Services, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Zestaw SDK usługi Azure Media Services .NET, [właściwości StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Kolejność pierwszeństwa konfiguracji pamięci podręcznej
1. Wartość pamięci podręcznej skonfigurowana przez usługę Azure Media Services zastępuje wartość domyślną.
2. Jeśli konfiguracja nie jest ręczna, obowiązują wartości domyślne.
3. Domyślnie nagłówki pamięci podręcznej 2 sekundy ma zastosowanie do manifestu(listy odtwarzania) przesyłania strumieniowego na żywo, niezależnie od konfiguracji usługi Azure Media lub usługi Azure Storage i zastępowanie tej wartości nie jest dostępne.

