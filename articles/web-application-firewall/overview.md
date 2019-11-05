---
title: Wprowadzenie do zapory aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web platformy Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502044"
---
# <a name="what-is-azure-web-application-firewall"></a>Co to jest usługa Azure Web Application Firewall?

Zapora aplikacji sieci Web (WAF) zapewnia centralną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Iniekcja kodu SQL i skryptów między lokacjami są między Najczęściej spotykanymi atakami.

![WAF — Omówienie](media/overview/wafoverview.png)

Zapobieganie takim atakom w kodzie aplikacji jest trudne. Może to wymagać rygorystycznej konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana Zapora aplikacji sieci Web pomaga uprościć zarządzanie zabezpieczeniami. WAF zapewnia także administratorom aplikacji lepszą ochronę przed zagrożeniami i włamaniami.

Rozwiązanie WAF może szybko reagować na zagrożenia bezpieczeństwa dzięki scentralizowanej poprawkom znanej luki w zabezpieczeniach, zamiast zabezpieczaniu każdej indywidualnej aplikacji sieci Web.

WAF można wdrożyć za pomocą platformy Azure Application Gateway i usługi Azure front-drzwi. Obecnie WAF ma funkcje, które są dostosowane do poszczególnych usług. Aby uzyskać więcej informacji na temat funkcji WAF dla każdej usługi, zobacz Omówienie każdej usługi.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web na Application Gateway, zobacz [Zapora aplikacji sieci Web na platformie Azure Application Gateway](./ag/ag-overview.md).
- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web w usłudze Azure front-drzwi, zobacz [Zapora aplikacji sieci Web w usłudze Azure front-drzwi](./afds/afds-overview.md).
