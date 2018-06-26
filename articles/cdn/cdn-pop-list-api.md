---
title: Pobieranie bieżącej listy Verizon POP dla usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać bieżącą listę Verizon POP za pomocą interfejsu API REST.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754672"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Pobieranie bieżącej listy Verizon POP dla usługi Azure CDN

Aby pobrać zestaw adresów IP w Verizon punktu obecności (POP) serwerów, można użyć interfejsu API REST. Te serwery POP wysyłanie żądań do serwerów źródła, które są skojarzone z punktami końcowymi Azure sieci dostarczania zawartości (CDN) w profilu Verizon (**Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon**). Należy pamiętać, że to zbiór adresów IP różni się od adresów IP, który klient będzie widoczne podczas wysyłania żądań do lokalizacji POP. 

Informacje na temat składni operacji interfejsu API REST do pobierania listy POP, zobacz [krawędzi węzłów — lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typowy przypadek użycia

Ze względów bezpieczeństwa można użyć tej listy IP do wymuszania, że żądania do serwera pochodzenia składają się tylko z prawidłową POP Verizon. Na przykład jeśli ktoś odnaleziony nazwa hosta lub adres IP serwera źródłowego punktu końcowego usługi CDN, co należy żądań bezpośrednio na serwerze źródłowym, w związku z tym pomijanie skalowanie i zabezpieczeń możliwości oferowane przez usługi Azure CDN. Przez ustawienie adresy IP na liście zwracanych, ponieważ jedyną dozwoloną adresów IP na serwerze źródłowym, w tym scenariuszu można zapobiec. Aby zapewnić ich masz najnowszą listę POP, pobrać co najmniej raz dziennie. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące interfejsu API REST, zobacz [API REST usługi Azure CDN](https://docs.microsoft.com/en-us/rest/api/cdn/).