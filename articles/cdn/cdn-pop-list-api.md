---
title: Pobranie aktualnej listy Verizon POP dla usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać bieżącą listę Verizon POP przy użyciu interfejsu API REST.
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
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c8316b994dac6b859f019bea1aac6b6a5c2c5b2d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593558"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Pobranie aktualnej listy Verizon POP dla usługi Azure CDN

Aby pobrać zestaw adresów IP dla firmy Verizon punktu obecności (POP) serwerów, można użyć interfejsu API REST. Te serwery POP wysyłać żądania do serwerów źródłowych, które są skojarzone z punktami końcowymi usługi Azure Content Delivery Network (CDN), w przypadku profilu firmy Verizon (**Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon**). Należy pamiętać, że ten zestaw adresów IP różni się od adresów IP, który klient będzie widoczne podczas wysyłania żądań do lokalizacji POP. 

Wyświetlić składnię operacji interfejsu API REST do pobierania listy punktów obecności, zobacz [węzłów brzegowych — lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typowy przypadek użycia

Ze względów bezpieczeństwa można użyć tej listy adresów IP do wymuszania, że żądania do serwera pochodzenia składają się tylko z prawidłową POP Verizon. Na przykład jeśli ktoś odnalezione nazwę hosta lub adres IP serwera źródłowego punktu końcowego usługi CDN, jeden wysyłać żądania bezpośrednio do serwera pochodzenia, w związku z tym pomijanie skalowanie i możliwości zabezpieczeń oferowane przez sieć CDN systemu Azure. Przez ustawienie adresów IP na liście zwracanych, ponieważ tylko adresy IP na serwer pochodzenia, w tym scenariuszu można zapobiec. Aby upewnić się, że masz najnowszą listę punktów POP, pobierz go co najmniej raz dziennie. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o interfejsie API REST, zobacz [interfejsu API REST usługi Azure CDN](https://docs.microsoft.com/rest/api/cdn/).
