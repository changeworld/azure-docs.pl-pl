---
title: Ładowanie aplikacji Proxy aplikacji trwa zbyt długo | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wydajnością ładowania strony za pomocą serwera proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65782647"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ładowanie aplikacji Proxy aplikacji trwa zbyt długo

Ten artykuł pomaga zrozumieć, dlaczego aplikacja serwera proxy aplikacji usługi Azure AD może zająć dużo czasu, aby załadować. Wyjaśniono również, co można zrobić, aby rozwiązać ten problem.

## <a name="overview"></a>Omówienie
Mimo że aplikacje działają, mogą wystąpić duże opóźnienia. Mogą istnieć poprawki topologii sieci, które można wprowadzić, aby poprawić szybkość. Aby uzyskać ocenę różnych topologii, zobacz [dokument zagadnienia dotyczące sieci](application-proxy-network-topology.md).

Oprócz topologii sieci, obecnie nie ma żadnych dalszych zaleceń dotyczących dostrajania wydajności. W miarę rozszerzania usługi Proxy aplikacji może dojść do centrum danych, które jest fizycznie bliżej. Bliższa bliskość może pomóc w opóźnieniu. Aby uzyskać listę centrów danych platformy Azure, zobacz [stronę testu opóźnienia](http://www.azurespeed.com/Azure/Latency). 

Centra danych z usługą Proxy aplikacji można znaleźć za pomocą [narzędzia testowego Porty złączy](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Opinie na temat lokalizacji centrum danych serwera proxy aplikacji 
Mogą istnieć centra danych platformy Azure, które nie zawierają jeszcze serwera proxy aplikacji, ale mogą prowadzić do znacznego opóźnienia dla Ciebie. Wyślij lokalizację centrum aadapfeedback@microsoft.comdanych do . Firma Microsoft używa opinii użytkownika do celów planów ekspansji.

Firma Microsoft pracuje nad dodatkowymi możliwościami w celu zwiększenia opóźnienia. Jak tylko te ulepszenia będą dostępne, dokumentacja zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki
[Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
