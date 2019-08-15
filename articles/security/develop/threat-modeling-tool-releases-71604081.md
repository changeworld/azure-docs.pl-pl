---
title: Threat Modeling Tool releases — Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Dokumentowanie informacji o wersji dla narzędzia do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934803"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool Update Release 7.1.60408.1 — 4/9/2019

Wersja 7.1.60408.1 Microsoft Threat Modeling Tool (TMT) została wydana w kwietniu 9 2019 i zawiera następujące zmiany:

- Nowe wzorniki dla Azure Key Vault i platformy Azure Traffic Manager
- TMT numer wersji jest teraz pokazywany na ekranie głównym
- Linki pomocy technicznej zostały zaktualizowane
- Poprawki błędów

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nowe wzorniki dla Azure Key Vault i platformy Azure Traffic Manager

![Wzornik Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nowe wzorniki i zagrożenia dla Azure Key Vault i Traffic Manager platformy Azure zostały dodane do zestawu wzorników platformy Azure. Podczas otwierania modeli opartych na zestawie wzorników platformy Azure użytkownicy otrzymają monit o zaktualizowanie szablonu skojarzonego z modelem. Aktualizowanie modelu opartego na zestawie wzorników platformy Azure można również zainicjować ręcznie przy użyciu polecenia "Zastosuj szablon" w menu "plik" i ponownie zastosować najnowszą wersję pliku Cloud Services. tb7 systemu Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT numer wersji jest teraz pokazywany na ekranie głównym

Wersja klienta Threat Modeling Tool jest teraz wyświetlana na ekranie głównym aplikacji w celu ułatwienia dostępu.

![Wzornik Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Linki pomocy technicznej zostały zaktualizowane

Wszystkie linki pomocy technicznej w narzędziu zostały zaktualizowane w celu przekierowania [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) użytkownikom do forum MSDN.

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](threat-modeling-tool.md)i zawiera informacje na [temat korzystania z narzędzia](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
