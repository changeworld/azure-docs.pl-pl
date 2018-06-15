---
title: Chin dostarczania zawartości z usługi Azure CDN | Dokumentacja firmy Microsoft
description: Informacje o używaniu Azure Content Delivery Network (CDN) do dostarczania zawartości dla użytkowników w Chinach.
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
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260228"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Chin dostarczania zawartości z usługi Azure CDN

Usługa Azure Content Delivery Network (CDN) globalnego może obsługiwać zawartość użytkownikom Chin z lokalizacji punktu z obecności (POP) obok Chin lub punktów POP, zapewnia najlepszą wydajność na żądania pochodzące z Chin. Jednak jeśli Chin jest znaczący rynku dla klientów i muszą duża wydajność, chińskiej wersji platformy Azure CDN zamiast tego Rozważ użycie.

Azure CDN Chin różni się od globalny usługi Azure CDN, w tym dostarcza zawartość z punktów POP w Chinach we współpracy z wielu dostawców lokalnego. Ze względu na zgodność języka chińskiego i rozporządzenia należy zarejestrować oddzielnej subskrypcji do użycia usługi Azure CDN Chin i witryny sieci Web muszą mieć licencję ICP. Portal i interfejsu API doświadczenia, Włącz i zarządzaniu zawartością dostarczania jest taka sama, jak Azure CDN globalne i Chińskiej wersji platformy Azure CDN.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Porównanie usługi Azure CDN globalne i Chińskiej wersji platformy Azure CDN

Azure CDN globalne i Chińskiej wersji platformy Azure CDN mają następujące funkcje:

- Globalny usługi Azure CDN:

     - Portalu: https://portal.azure.com  

     - Wykonuje dostarczania zawartości poza Chin

     - Cztery warstwy cenowe: Microsoft standard, standardowe Verizon Verizon premium i Akamai standard

     - [Dokumentacja](https://docs.microsoft.com/en-us/azure/cdn/)

- Chin usługi Azure CDN:

     - Portalu: https://portal.azure.cn

     - Wykonuje dostarczania zawartości w Chinach

     - Dwie warstwy cenowej: Standard i premium

     - [Dokumentacja](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o chińskiej wersji platformy Azure CDN, zobacz:

- [Funkcje sieci dostarczania zawartości](https://www.azure.cn/en-us/home/features/cdn/)

- [Omówienie usługi Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Użyj usługi Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Dostępność usługi Azure w Chinach](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability)



