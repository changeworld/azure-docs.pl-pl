---
title: Aplikacja serwera Proxy aplikacji trwa zbyt długo, aby załadować | Dokumentacja firmy Microsoft
description: Strona obciążenia Rozwiązywanie problemów z wydajnością z serwer Proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 26acc620184b51719a2ee55b75bd01966d225b8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330931"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Aplikacja serwera Proxy aplikacji trwa zbyt długo, aby załadować

Ten artykuł pomaga zrozumieć, dlaczego aplikacja serwera Proxy aplikacji usługi Azure AD może zająć dużo czasu ładowania. Wyjaśniono również, co możesz zrobić, aby rozwiązać ten problem.

## <a name="overview"></a>Przegląd
Mimo że działają aplikacje, może być długi czas oczekiwania. Może to być ulepszeń topologii sieci, wchodzące w celu zwiększenia szybkości. W wersji ewaluacyjnej topologii, zobacz [dokumentu zagadnienia dotyczące sieci](manage-apps/application-proxy-network-topology.md).

Oprócz topologii sieci obecnie nie istnieją żadne dodatkowe zalecenia dotyczące dostrajania wydajności. Jako serwer Proxy aplikacji usługi rozszerzeniu może pochodzić centrum danych, który znajduje się fizycznie bliżej. Zbliża mogą ułatwić z opóźnieniem. Aby uzyskać listę centrach danych platformy Azure, zobacz [stronę testową opóźnienia](http://www.azurespeed.com/Azure/Latency). 

Centra danych z serwera Proxy aplikacji usługi można znaleźć z [narzędzia Test porty łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Opinię na lokalizacje centrum danych serwer Proxy aplikacji 
Może to być centrach danych platformy Azure, które nie zostały jeszcze zawiera serwer Proxy aplikacji, ale może doprowadzić do poprawy opóźnienia doskonały dla Ciebie. Wyślij lokalizacji do centrum danych aadapfeedback@microsoft.com. Firma Microsoft używa swoją opinię dla planów rozszerzenia.

Firma Microsoft pracuje dodatkowe funkcje zwiększające opóźnienia. Jak te usprawnienia są dostępne, dokumentacja zostanie zaktualizowana.

## <a name="next-steps"></a>Kolejne kroki
[Praca z istniejącym lokalnych serwerów proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
