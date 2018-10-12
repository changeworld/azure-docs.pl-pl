---
title: Pobranie aktualnej listy Verizon POP dla usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać bieżącą listę Verizon POP przy użyciu interfejsu API REST.
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
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 8a11f6cb634ca8986b463d815bd75e03b79b5706
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093990"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Pobranie aktualnej listy Verizon POP dla usługi Azure CDN

Aby pobrać zestaw adresów IP dla firmy Verizon punktu obecności (POP) serwerów, można użyć interfejsu API REST. Te serwery POP wysyłać żądania do serwerów źródłowych, które są skojarzone z punktami końcowymi usługi Azure Content Delivery Network (CDN), w przypadku profilu firmy Verizon (**Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon**). Należy pamiętać, że ten zestaw adresów IP różni się od adresów IP, który klient będzie widoczne podczas wysyłania żądań do lokalizacji POP. 

Wyświetlić składnię operacji interfejsu API REST do pobierania listy punktów obecności, zobacz [węzłów brzegowych — lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typowy przypadek użycia

Ze względów bezpieczeństwa można użyć tej listy adresów IP do wymuszania, że żądania do serwera pochodzenia składają się tylko z prawidłową POP Verizon. Na przykład jeśli ktoś odnalezione nazwę hosta lub adres IP serwera źródłowego punktu końcowego usługi CDN, jeden wysyłać żądania bezpośrednio do serwera pochodzenia, w związku z tym pomijanie skalowanie i możliwości zabezpieczeń oferowane przez sieć CDN systemu Azure. Przez ustawienie adresów IP na liście zwracanych, ponieważ tylko adresy IP na serwer pochodzenia, w tym scenariuszu można zapobiec. Aby zapewnić ich masz najnowszą listę POP, pobrać go co najmniej raz dziennie. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o interfejsie API REST, zobacz [interfejsu API REST usługi Azure CDN](https://docs.microsoft.com/rest/api/cdn/).