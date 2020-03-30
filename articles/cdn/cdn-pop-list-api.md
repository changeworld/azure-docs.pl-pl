---
title: Pobieranie bieżącej listy adresów IP pop dla usługi Azure CDN| Dokumenty firmy Microsoft
description: Dowiedz się, jak pobrać bieżącą listę pop.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299238"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Pobieranie bieżącej listy adresów IP pop dla usługi Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Pobieranie bieżącej listy adresów IP verizon POP dla usługi Azure CDN

Za pomocą interfejsu API REST można pobrać zestaw serwerów IP dla serwerów punktu obecności Verizon (POP). Te serwery POP żądają serwerów pochodzenia skojarzonych z punktami końcowymi usługi Azure Content Delivery Network (CDN) w profilu Verizon **(Azure CDN Standard firmy Verizon** lub **Azure CDN Premium firmy Verizon).** Należy zauważyć, że ten zestaw adresów IP różni się od adresów IP, które klient zobaczy podczas składania żądań do adresów grupy leceń. 

Aby zapoznać się ze składnią operacji interfejsu API REST do pobierania listy POP, zobacz [Węzeł krawędzi — lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Pobieranie bieżącej listy adresów IP pop firmy Microsoft dla usługi Azure CDN

Aby zablokować aplikację, aby akceptować ruch tylko z usługi Azure CDN firmy Microsoft, należy skonfigurować listy ACL adresów IP dla wewnętrznej bazy danych. Można również ograniczyć zestaw akceptowanych wartości dla nagłówka "X-Forwarded-Host" wysyłanego przez usługę Azure CDN firmy Microsoft. Poniższe kroki są wyszczególnione w następujący sposób:

Skonfiguruj protokół IP ACLing dla zaplecza, aby akceptować ruch z usługi Azure CDN z wewnętrznej bazy danych IP przestrzeni adresowej i tylko usług infrastruktury platformy Azure. 

* Usługa Azure CDN z przestrzeni adresów IP zaplecza IPv4 firmy Microsoft: 147.243.0.0/16
* Usługa Azure CDN z obszaru ip zaplecza IPv6 firmy Microsoft: 2a01:111:2050::/44

Zakresy adresów IP i tagi usług dla usług firmy Microsoft można znaleźć [tutaj](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Typowy przypadek użycia

Ze względów bezpieczeństwa można użyć tej listy adresów IP, aby wymusić, że żądania do serwera pochodzenia są dokonywane tylko z prawidłowego verizon POP. Na przykład jeśli ktoś wykrył adres hosta lub adres IP serwera źródłowego sieci CDN, można składać żądania bezpośrednio do serwera pochodzenia, w związku z czym z pominięciem możliwości skalowania i zabezpieczeń dostarczonych przez usługę Azure CDN. Ustawiając adresy IP na liście zwracanej jako jedyne dozwolone adresy IP na serwerze pochodzenia, można zapobiec temu scenariuszowi. Aby upewnić się, że masz najnowszą listę pop, pobierz ją co najmniej raz dziennie. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat interfejsu API REST, zobacz [Interfejs API REST usługi Azure CDN](https://docs.microsoft.com/rest/api/cdn/).
