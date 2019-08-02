---
title: Safelist adresy URL Azure Portal | Microsoft Docs
description: Dodaj te adresy URL do obejścia serwera proxy, aby komunikować się z Azure Portalą i jej usługami
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667474"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal na zaporze lub serwerze proxy

Aby uzyskać dobrą wydajność i łączność między siecią lokalną lub rozległą i chmurą platformy Azure, należy skonfigurować lokalne urządzenia zabezpieczeń w celu obejścia ograniczeń zabezpieczeń dla adresów URL Azure Portal. Administratorzy sieci często wdrażają serwery proxy, zapory lub inne urządzenia, aby pomóc w zabezpieczeniu i zapewnić kontrolę nad sposobem uzyskiwania dostępu do Internetu przez użytkowników. Jednak reguły mające na celu ochronę użytkowników mogą czasami blokować lub spowalniać słuszny ruch internetowy związany z działalnością, w tym z komunikacją między użytkownikami i platformą Azure. Aby zoptymalizować łączność między siecią a Azure Portal i jej usługami, zalecamy dodanie Azure Portal adresów URL do safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Adresy URL Azure Portal dla obejścia serwera proxy

Dodaj następującą listę adresów URL do serwera proxy lub zapory, aby zezwolić na ruch sieciowy do tych punktów końcowych w celu obejścia ograniczeń:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Ruch do tych punktów końcowych używa standardowych portów TCP dla protokołu HTTP (80) i HTTPS (443).
>
>
## <a name="next-steps"></a>Następne kroki

* Chcesz Safelist adresy IP? Pobierz listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Inne usługi firmy Microsoft używają dodatkowych adresów URL i adresów IP na potrzeby łączności. Aby zoptymalizować łączność sieciową dla usług Microsoft 365 Services, zobacz [Konfigurowanie sieci dla pakietu Office 365](/office365/enterprise/set-up-network-for-office-365).
