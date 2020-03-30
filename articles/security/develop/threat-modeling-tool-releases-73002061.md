---
title: Microsoft Threat Modeling Tool release 02/11/2020 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624841"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Aktualizacja narzędzia do modelowania zagrożeń wersja 7.3.00206.1 - 11.020

Wersja 7.3.00206.1 narzędzia Microsoft Threat Modeling Tool (TMT) została wydana 11 lutego 2020 r. i zawiera następujące zmiany:

- Poprawki błędów

## <a name="notable-bug-fixes"></a>Godne uwagi poprawki błędów

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Błędy związane z wartościami priorytetów poza oczekiwanymi zakresami

Niektórzy klienci zgłaszali, że podczas otwierania plików utworzonych w "Narzędziu do modelowania zagrożeń 2016" lub szablonach niestandardowych odeszło następujący komunikat o błędzie:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Ten problem został rozwiązany w tej wersji.

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
