---
title: Wprowadzenie do zapory aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web platformy Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851187"
---
# <a name="what-is-azure-web-application-firewall"></a>Co to jest usługa Azure Web Application Firewall?

Zapora aplikacji internetowej zapewnia centralną ochronę aplikacji internetowych przez typowymi atakami wykorzystującymi luki i lukami w zabezpieczeniach. Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Iniekcja kodu SQL i skryptów między lokacjami są między Najczęściej spotykanymi atakami.

![WAF — Omówienie](media/overview/wafoverview.png)

Zapobieganie takim atakom w kodzie aplikacji jest trudne. Może to wymagać rygorystycznej konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana Zapora aplikacji sieci Web pomaga uprościć zarządzanie zabezpieczeniami. WAF zapewnia także administratorom aplikacji lepszą ochronę przed zagrożeniami i włamaniami.

Rozwiązanie WAF może szybko reagować na zagrożenia bezpieczeństwa dzięki scentralizowanej poprawkom znanej luki w zabezpieczeniach, zamiast zabezpieczaniu każdej indywidualnej aplikacji sieci Web.

## <a name="supported-services"></a>Obsługiwane usługi

WAF można wdrożyć za pomocą [platformy azure Application Gateway](../application-gateway/overview.md) i [usługi Azure front-drzwi](../frontdoor/front-door-overview.md). Obie usługi są modułami równoważenia obciążenia w warstwie 7 (HTTP/S), ale Application Gateway to usługa regionalna, a przód drzwi to usługa globalna. WAF zawiera funkcje, które są dostosowane do poszczególnych usług.

Aby uzyskać więcej informacji, zobacz Omówienie WAF dla każdej usługi.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web na Application Gateway, zobacz [Zapora aplikacji sieci Web na platformie Azure Application Gateway](./ag/ag-overview.md).
- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web w usłudze Azure Front-drzwiczk, zobacz [Zapora aplikacji sieci Web w usłudze Azure front-drzwi](./afds/afds-overview.md).
