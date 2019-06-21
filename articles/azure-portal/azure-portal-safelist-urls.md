---
title: Witryny Azure portal w bezpiecznej liście adresów URL | Dokumentacja firmy Microsoft
description: Dodaj te adresy URL do obejścia serwera proxy do komunikowania się z witryny Azure portal i usług
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305050"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Witryny Azure portal w bezpiecznej liście adresów URL na serwerze zapory lub serwera proxy

W przypadku dobrą wydajność i łączność między sieci lokalnej lub całego obszaru i w chmurze Azure, skonfigurować lokalnych urządzeń zabezpieczeń do obejścia ograniczeń zabezpieczeń dla witryny Azure portal adresów URL. Administratorzy sieci często wdrażają serwerów proxy, zaporach lub innych urządzeń w celu zabezpieczania oraz kontrolę nad jak użytkownicy uzyskują dostęp przez internet. Jednak czasami zasad zaprojektowanych w celu ochrony użytkowników zablokować lub spowolnić uzasadnione związanych z firmą ruch internetowy, łącznie z łączności między użytkownikiem i Azure. W celu zoptymalizowania łączności między siecią i witryny Azure portal i swoich usług, firma Microsoft zaleca, możesz dodać w witrynie Azure portal adresów URL do bezpiecznej swojej liście.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Obejście adresy URL dla serwera proxy w witrynie Azure portal

Dodaj poniższą listę adresów URL do serwera proxy lub zapory tak, aby zezwolić na ruch sieciowy z tymi punktami końcowymi w celu obejścia ograniczeń:

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
* *.wpc.azureedge.net

> [!NOTE]
> Ruch z tymi punktami końcowymi używa standardowych portów TCP dla protokołu HTTP (80) i protokołu HTTPS (443).
>
>
## <a name="next-steps"></a>Kolejne kroki

* Mają adresy IP bezpiecznej liście? Pobierz listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Inne użytku firmy Microsoft usług dodatkowych adresów URL i adresy IP dla połączenia. Aby zoptymalizować łącznością sieciową na potrzeby usługi Microsoft 365, zobacz [konfiguracji sieci dla usługi Office 365](/office365/enterprise/set-up-network-for-office-365).
