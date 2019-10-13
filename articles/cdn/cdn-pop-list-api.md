---
title: Pobierz bieżącą listę punktów obecności IP dla Azure CDN | Microsoft Docs
description: Dowiedz się, jak pobrać bieżącą listę punktów obecności.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299238"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Pobierz bieżącą listę punktów obecności IP dla Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Pobierz bieżącą listę punktów obecności Verizon IP dla Azure CDN

Za pomocą interfejsu API REST można pobrać zestaw adresów IP dla serwerów punktu obecności (POP) Verizon. Te serwery POP wysyłają żądania do serwerów pochodzenia, które są skojarzone z punktami końcowymi usługi Azure Content Delivery Network (CDN) w profilu Verizon (**Azure CDN Standard z Verizon** lub **Azure CDN Premium from Verizon**). Należy zauważyć, że ten zestaw adresów IP różni się od adresów IP, które zobaczy klient podczas wykonywania żądań do punktów obecności. 

Aby zapoznać się z składnią operacji interfejsu API REST na potrzeby pobierania listy punktów obecności, zobacz [Edge nodes-list](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Pobierz bieżącą listę adresów POP firmy Microsoft dla Azure CDN

Aby zablokować aplikację w celu akceptowania tylko ruchu z Azure CDN firmy Microsoft, musisz skonfigurować listy ACL adresów IP dla zaplecza. Możesz również ograniczyć zestaw akceptowanych wartości dla nagłówka "X-Forward-host" wysyłanego przez Azure CDN od firmy Microsoft. Poniższe kroki są szczegółowo opisane poniżej:

Skonfiguruj ACLing IP dla zaplecza, aby akceptować ruch z Azure CDN z przestrzeni adresów IP zaplecza firmy Microsoft i usług infrastruktury platformy Azure. 

* Azure CDN ze przestrzeni adresów IP zaplecza protokołu IPv4 firmy Microsoft: 147.243.0.0/16
* Azure CDN ze przestrzeni adresów IP zaplecza protokołu IPv6 firmy Microsoft: 2a01:111:2050::/44

Zakresy adresów IP i Tagi usług dla usług firmy Microsoft można znaleźć [tutaj](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Typowy przypadek użycia

Ze względów bezpieczeństwa można użyć tej listy adresów IP, aby wymusić, że żądania kierowane do serwera pochodzenia są wykonywane tylko z prawidłowego punktu obecności Verizon. Na przykład, jeśli ktoś ododnalazł nazwę hosta lub adres IP dla serwera pochodzenia punktu końcowego usługi CDN, może to oznaczać żądania bezpośrednio do serwera źródłowego, dlatego pomijając możliwości skalowania i zabezpieczeń zapewniane przez Azure CDN. W przypadku ustawienia adresów IP na liście zwracanych jako jedyne dozwolone adresy IP na serwerze pochodzenia można zablokować ten scenariusz. Aby upewnić się, że masz najnowszą listę punktów obecności, pobierz ją co najmniej raz dziennie. 

## <a name="next-steps"></a>Następne kroki

Informacje o interfejsie API REST znajdują się w temacie [Azure CDN API REST](https://docs.microsoft.com/rest/api/cdn/).
