---
title: Dostarczanie zawartości w Chinach za pomocą usługi Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się więcej o używaniu usługi Azure Content Delivery Network (CDN) do dostarczania zawartości użytkownikom z Chin.
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
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: a63914116f5ef9922ac05745764e0ad505dc3c91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593949"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Dostarczanie zawartości w Chinach za pomocą usługi Azure CDN

Usługa Azure Content Delivery Network (CDN) global może wyświetlać zawartość użytkownikom w Chinach z lokalizacjami punktu obecności (POP) w pobliżu Chin lub dowolnym punktem obecności, który zapewnia najlepszą wydajność żądań pochodzących z Chin. Jeśli jednak Chiny są znaczącym rynkiem dla klientów i potrzebują szybkiej wydajności, rozważ użycie usługi Azure CDN China.

Usługa Azure CDN China różni się od globalnej usługi Azure CDN tym, że dostarcza zawartość z dostawców usług Y WZ w Chinach, współpracując z wieloma lokalnymi dostawcami. Ze względu na chińskie przestrzeganie i regulacje należy zarejestrować oddzielną subskrypcję, aby korzystać z usługi Azure CDN China, a witryny sieci Web muszą mieć licencję ICP. Środowisko portalu i interfejsu API umożliwiające dostarczanie zawartości i zarządzanie nim jest identyczne między globalną usługą Azure CDN a usługą Azure CDN w Chinach.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Porównanie globalnej usługi Azure CDN i usługi Azure CDN w Chinach

Usługa Azure CDN global i Azure CDN China mają następujące funkcje:

- Globalna usługa Azure CDN:

     - Portal:https://portal.azure.com  

     - Realizuje dostarczanie treści poza Chinami

     - Cztery warstwy cenowe: standard Microsoft, standard Verizon, verizon premium i standard Akamai

     - [Dokumentacja](https://docs.microsoft.com/azure/cdn/)

- Usługi Azure CDN w Chinach:

     - Portal:https://portal.azure.cn

     - Wykonuje dostarczanie treści w Chinach

     - Dwie warstwy cenowe: Standard i premium

     - [Dokumentacja](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure CDN w Chinach, zobacz:

- [Funkcje sieci dostarczania zawartości](https://www.azure.cn/en-us/home/features/cdn/)

- [Omówienie sieci dostarczania zawartości platformy Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Korzystanie z sieci dostarczania zawartości platformy Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Dostępność usług platformy Azure w Chinach](https://docs.microsoft.com/azure/china/concepts-service-availability)



