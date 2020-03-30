---
title: Microsoft Threat Modeling Tool release 10/16/2019 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552053"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.61015.1 — 2019-10-16

Wersja 7.1.61015.1 narzędzia Microsoft Threat Modeling Tool (TMT) została wydana 16 października 2019 r. i zawiera następujące zmiany:

- Usprawnienia w zakresie ułatwień dostępu
- Poprawki błędów
- Nowe wzorniki dla aplikacji logiki azure i Eksploratora danych platformy Azure

## <a name="notable-bug-fixes"></a>Godne uwagi poprawki błędów

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Poprawiono zgodność wsteczną z plikami utworzonymi w "Threat Modeling Tool 2016"

Naprawiono kilka błędów związanych z otwieraniem lub wyświetlaniem plików modelu zagrożeń utworzonych w "Threat Modeling Tool 2016".

## <a name="feature-enhancements"></a>Ulepszenia funkcji

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nowe wzorniki dla aplikacji logiki azure i Eksploratora danych platformy Azure

Nowe wzorniki dla usługi Azure Logic Apps i Usługi Azure Data Explorer zostały dodane do wzornika platformy Azure wraz z powiązanymi zagrożeniami i ograniczeniami.

![Wzorniki azure logic apps i usługi Azure Data Explorer](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Znane problemy

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Błędy związane z wartościami priorytetów poza oczekiwanymi zakresami

Niektórzy klienci zgłaszali, że podczas otwierania plików utworzonych w "Narzędziu do modelowania zagrożeń 2016" lub szablonach niestandardowych odebywa się następujący komunikat o błędzie:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Kwestia ta jest przedmiotem dochodzenia

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa aktualizacja systemu Microsoft Windows 10 lub nowsza](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Wymagana wersja .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowsze
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów wymagane jest połączenie z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i informacje zwrotne

- Dokumentacja narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje [o użyciu narzędzia.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [narzędzia Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
