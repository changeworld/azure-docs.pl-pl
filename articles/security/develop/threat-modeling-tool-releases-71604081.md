---
title: Microsoft Threat Modeling Tool wydanie 4/9/2019
titleSuffix: Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269728"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Aktualizacja narzędzia do modelowania zagrożeń 7.1.60408.1 - 4/9/2019

Wersja 7.1.60408.1 narzędzia Microsoft Threat Modeling Tool (TMT) została wydana 9 kwietnia 2019 r. i zawiera następujące zmiany:

- Nowe wzorniki dla usługi Azure Key Vault i usługi Azure Traffic Manager
- Numer wersji TMT jest teraz wyświetlany na ekranie głównym
- Zaktualizowano łącza pomocy technicznej
- Poprawki błędów

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nowe wzorniki dla usługi Azure Key Vault i usługi Azure Traffic Manager

![Wzornik magazynu kluczy platformy Azure](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nowe wzorniki i zagrożenia dla usługi Azure Key Vault i usługi Azure Traffic Manager zostały dodane do zestawu wzorników platformy Azure. Podczas otwierania modeli na podstawie zestawu wzorników platformy Azure użytkownicy zostaną poproszeni o zaktualizowanie szablonu skojarzonego z modelem. Aktualizowanie modelu opartego na zestawie wzorników platformy Azure można również zainicjować ręcznie przy użyciu polecenia "Zastosuj szablon" w menu "Plik" i ponownego zastosowania najnowszego pliku usługi Azure Cloud Services.tb7.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Numer wersji TMT jest teraz wyświetlany na ekranie głównym

Wersja klienta narzędzia do modelowania zagrożeń jest teraz wyświetlana na ekranie głównym aplikacji dla ułatwienia dostępu.

![Wzornik magazynu kluczy platformy Azure](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Zaktualizowano łącza pomocy technicznej

Wszystkie linki pomocy technicznej w ramach narzędzia [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) zostały zaktualizowane, aby kierować użytkowników do, a nie do forum MSDN.

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa aktualizacja systemu Microsoft Windows 10 lub nowsza](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Wymagana wersja .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowsze
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów wymagane jest połączenie z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i informacje zwrotne

- Dokumentacja narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](threat-modeling-tool.md)i zawiera informacje [o użyciu narzędzia.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [narzędzia Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
