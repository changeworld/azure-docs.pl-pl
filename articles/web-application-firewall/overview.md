---
title: Wprowadzenie do Zapory aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web platformy Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475791"
---
# <a name="what-is-azure-web-application-firewall"></a>Co to jest usługa Azure Web Application Firewall?

Zapora aplikacji internetowej zapewnia centralną ochronę aplikacji internetowych przez typowymi atakami wykorzystującymi luki i lukami w zabezpieczeniach. Aplikacje sieci Web są coraz częściej celem złośliwych ataków, które wykorzystują powszechnie znane luki w zabezpieczeniach. Iniekcji SQL i skryptów między witrynami są jednymi z najczęstszych ataków.

![Przegląd WAF](media/overview/wafoverview.png)

Zapobieganie takim atakom w kodzie aplikacji jest trudne. Może wymagać rygorystycznej konserwacji, poprawek i monitorowania w wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji sieci web znacznie ułatwia zarządzanie zabezpieczeniami. WAF daje również administratorom aplikacji lepszą pewność ochrony przed zagrożeniami i włamaniami.

Rozwiązanie WAF może szybciej reagować na zagrożenie bezpieczeństwa, centralnie łatając znaną lukę, zamiast zabezpieczać każdą aplikację sieci web.

## <a name="supported-service"></a>Obsługiwana usługa

WAF można wdrożyć za pomocą usługi Azure Application Gateway, Azure Front Door i Azure Content Delivery Network (CDN) firmy Microsoft. WAF na platformie Azure CDN jest obecnie w publicznej wersji zapoznawczej.  WAF ma funkcje, które są dostosowane do każdej konkretnej usługi. Aby uzyskać więcej informacji na temat funkcji WAF dla każdej usługi, zobacz omówienie dla każdej usługi.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat Zapory aplikacji sieci Web w bramie aplikacji, zobacz [Zapora aplikacji sieci Web w bramie aplikacji platformy Azure](./ag/ag-overview.md).
- Aby uzyskać więcej informacji na temat Zapory aplikacji sieci Web w usłudze Azure Front Door Service, zobacz [Zapora aplikacji sieci Web w usłudze Azure Front Door Service](./afds/afds-overview.md).
- Aby uzyskać więcej informacji na temat Zapory aplikacji sieci Web w usłudze usługi Azure CDN, zobacz [Zapora aplikacji sieci Web w usłudze usługi Azure CDN](./cdn/cdn-overview.md)
