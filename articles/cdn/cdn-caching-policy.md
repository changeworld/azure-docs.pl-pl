---
title: Zarządzanie Azure zasady buforowania usługi CDN w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Azure zasady buforowania usługi CDN w usłudze Azure Media Services.
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
ms.openlocfilehash: 516df2f6177303987fc0354dde647c1fc26820ef
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918284"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Zarządzanie Azure zasady buforowania usługi CDN w usłudze Azure Media Services
Usługa Azure Media Services udostępnia oparty na protokole HTTP adaptacyjnego przesyłania strumieniowego i pobierania progresywnego. Wysoka skalowalność dzięki korzyści z pamięci podręcznej serwera proxy i warstwy sieci CDN, a także buforowaniem po stronie klienta jest oparty na protokole HTTP, przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego zapewnia ogólne możliwości przesyłania strumieniowego, a także konfiguracji dla protokołu HTTP, nagłówków pamięci podręcznej. Punkty końcowe przesyłania strumieniowego ustawia HTTP Cache-Control: maksymalny wiek i nagłówków Expires. Można uzyskać więcej informacji na nagłówki cache HTTP od [adresem W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Domyślne nagłówki pamięć podręczna
Domyślnie — punkty końcowe przesyłania strumieniowego mają zastosowanie nagłówki pamięci podręcznej 3 dni dla danych przesyłania strumieniowego na żądanie (multimedialna fragmenty/fragmentów) i manifest(playlist). Transmisją strumieniową na żywo, punkty końcowe przesyłania strumieniowego Zastosuj nagłówki pamięci podręcznej 3 dni dla danych (multimedialna fragmenty/fragmentów), a nagłówek żądania manifest(playlist) 2 sekundy w pamięci podręcznej. Gdy program na żywo przechodzi na żądanie (dynamiczne archiwum), mają zastosowanie nagłówki pamięci podręcznej z przesyłania strumieniowego na żądanie.

## <a name="azure-cdn-integration"></a>Integracja z usługą Azure CDN
Usługa Azure Media Services udostępnia [zintegrowane usługi CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) dla — punkty końcowe przesyłania strumieniowego. Nagłówki Cache-control ma zastosowanie w ten sam sposób, jako punkty końcowe przesyłania strumieniowego do usługi CDN włączone punkty końcowe przesyłania strumieniowego. Azure używa usługi CDN, punkt końcowy przesyłania strumieniowego skonfigurowane wartości pamięci podręcznej, aby zdefiniować okres istnienia obiektów wewnętrznie buforowane, a także wykorzystuje tę wartość można ustawić dostarczanie nagłówki cache. Gdy za pomocą sieci CDN włączone punkty końcowe przesyłania strumieniowego jest niezalecane do ustawiania wartości małych pamięci podręcznej. Ustawianie wartości małych zmniejszyć wydajność i zmniejszyć zaletą usługi CDN. Ustawianie nagłówków pamięci podręcznej jest mniejszy niż 600 sekund w przypadku sieci CDN włączone punkty końcowe przesyłania strumieniowego jest niedozwolone.

> [!IMPORTANT]
>Usługa Azure Media Services zawiera pełną integrację z usługą Azure CDN. Za pomocą jednego kliknięcia możesz zintegrować wszystkich dostępnych dostawców usługi Azure CDN do punktu końcowego przesyłania strumieniowego w tym produktów w warstwach standardowa i premium. Aby uzyskać więcej informacji, zobacz ten [ogłoszenie](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Opłaty za dane z przesyłania strumieniowego punktu końcowego usługi CDN pobiera wyłączyć tylko po włączeniu usługi CDN za pośrednictwem interfejsów API punktu końcowego przesyłania strumieniowego lub przy użyciu sekcji punkt końcowy przesyłania strumieniowego w portalu Azure. Integracja ręczna lub bezpośrednio Tworzenie punktu końcowego usługi CDN, za pomocą interfejsów API usługi CDN lub sekcji portalu nie Wyłącz opłaty za dane.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurowanie nagłówków pamięci podręcznej przy użyciu usługi Azure Media Services
Witryny Azure portal lub interfejsów API usługi Azure Media Services umożliwiają skonfigurowanie wartości nagłówka pamięci podręcznej.

1. Aby skonfigurować nagłówki cache przy użyciu witryny Azure portal, zapoznaj się [jak zarządzanie punktami końcowymi przesyłania strumieniowego](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) sekcji konfigurowania punktu końcowego przesyłania strumieniowego.
2. W przypadku interfejsu API REST usługi Azure Media Services [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Zestaw .NET SDK usługi Azure Media Services [właściwości StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Kolejność pierwszeństwa konfiguracji pamięci podręcznej
1. Wartość pamięci podręcznej w usłudze Azure Media Services skonfigurowane zastępuje wartości domyślne.
2. W przypadku nie ręcznej konfiguracji, mają zastosowanie wartości domyślnych.
3. Przez domyślny 2 sekundy w pamięci podręcznej nagłówki mają zastosowanie do rzeczywistego transmisji strumieniowej manifest(playlist) niezależnie od konfiguracji multimediów Azure lub usługi Azure Storage i zastąpienie tej wartości nie jest dostępna.

