---
title: Narzędzie do modelowania zagrożeń zwalnia — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dokumentowanie informacje o wersji przed zagrożeniami, narzędzie do modelowania
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798251"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Wydanie aktualizacji narzędzie do modelowania zagrożeń 7.1.60408.1 - 4/9/2019 r

Wersja 7.1.60408.1 Microsoft zagrożeń Modeling Tool (TMT) został wydany 2019 9 kwietnia i zawiera następujące zmiany:

- Nowe wzorników dla usługi Azure Key Vault i usługa Azure Traffic Manager
- Numer wersji TMT są teraz wyświetlane na ekranie głównym
- Pomoc techniczna — linki zostały zaktualizowane.
- Poprawki błędów

## <a name="feature-changes"></a>Funkcja zmiany

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nowe wzorników dla usługi Azure Key Vault i usługa Azure Traffic Manager

![Usługa Azure Key Vault wzornika](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Do zestawu Azure wzornika zostały dodane nowe wzorników i zagrożeniami dla usługi Azure Key Vault i Azure Traffic Manager. Podczas otwierania modeli na podstawie zestawu wzornika platformy Azure, użytkownicy otrzymują monit Aby zaktualizować szablon skojarzonego z tym modelem. Aktualizowanie modelu na podstawie zestawu Azure wzornika można także ręcznie inicjować przy użyciu polecenia "Zastosuj szablon" w menu "File" i ponowne zastosowanie najnowszego pliku Services.tb7 chmury platformy Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Numer wersji TMT są teraz wyświetlane na ekranie głównym

Wersja klienta, narzędzia do modelowania zagrożeń są teraz wyświetlane na ekranie głównym aplikacji w celu ułatwienia dostępu.

![Usługa Azure Key Vault wzornika](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Pomoc techniczna — linki zostały zaktualizowane.

Wszystkie łącza pomocy technicznej w narzędziu zostały zaktualizowane, udostępniając użytkownikom [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) zamiast MSDN forum.

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Program Microsoft Windows Update rozliczenia 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowszej
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) lub nowszej
- Wymagania dodatkowe
  - Aby otrzymywać aktualizacje do narzędzia, jak również szablony, wymagane jest połączenie internetowe.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja dla narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje o [dotyczące korzystania z narzędzia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Kolejne kroki

Pobierz najnowszą wersję [narzędzie do modelowania zagrożeń firmy Microsoft](https://aka.ms/threatmodelingtool).