---
title: Aplikacje serwera Proxy aplikacji trwa zbyt długo, aby załadować | Dokumentacja firmy Microsoft
description: Strona obciążenie Rozwiązywanie problemów z wydajnością za pomocą serwera Proxy aplikacji usługi AD systemu Azure
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4110ceddb55de1990d85597f8c1a9618743f946
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292728"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Aplikacje serwera Proxy aplikacji trwa zbyt długo, aby załadować

Ten artykuł pomaga zrozumieć, dlaczego aplikację serwera Proxy aplikacji usługi Azure AD może zająć dużo czasu do załadowania. Wyjaśniono również, co można zrobić, aby rozwiązać ten problem.

## <a name="overview"></a>Omówienie
Mimo że aplikacje działają, może być długi czas oczekiwania. Może to być ulepszeń topologii sieci, wchodzące w celu zwiększenia szybkości. Aby uzyskać ocenę różnych topologii, zobacz [dokumentu zagadnienia dotyczące sieci](application-proxy-network-topology.md).

Oprócz topologii sieci są obecnie nie dalsze zalecenia dotyczące dostrajania wydajności. Jako serwer Proxy aplikacji, który rozwija usługi mogą pochodzić z centrum danych, który znajduje się fizycznie bliżej. Im bliżej odległości między elementami może pomóc z opóźnieniem. Aby uzyskać listę centrów danych platformy Azure, zobacz [strony testowej opóźnienie](http://www.azurespeed.com/Azure/Latency). 

Centra danych przy użyciu usługi Serwer Proxy aplikacji można znaleźć [narzędzie Test porty łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Opinie na temat lokalizacje centrów danych serwera Proxy aplikacji 
Może to być centrów danych platformy Azure, nie jeszcze dołączyć serwer Proxy aplikacji, które może doprowadzić do poprawy doskonałe opóźnienia dla Ciebie. Wyślij lokalizację centrum danych aadapfeedback@microsoft.com. Firma Microsoft używa swoją opinię w przypadku planów rozwoju.

Firma Microsoft pracuje na dodatkowe funkcje, aby poprawić czas oczekiwania. Jak te ulepszenia są dostępne, zostaną zaktualizowane dokumentacji.

## <a name="next-steps"></a>Kolejne kroki
[Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)
