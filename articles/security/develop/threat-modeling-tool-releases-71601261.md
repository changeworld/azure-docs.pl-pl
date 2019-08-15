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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: d9c91752a599debcd8fda6c8396141e6a92e0f06
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928097"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.60126.1 — 2019-01-29

Wersja 7.1.60126.1 Microsoft Threat Modeling Tool została wydana w styczniu 29 2019 i zawiera następujące zmiany:

- Minimalna wymagana wersja platformy .NET została zwiększona do [programu .NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimalna wymagana wersja systemu Windows została zwiększona do rocznicowej [aktualizacji systemu Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ze względu na zależność programu .NET.
- Funkcja przełączania walidacji modelu została dodana do menu Opcje narzędzia.
- Zaktualizowano kilka linków we właściwościach zagrożeń.
- Drobne zmiany środowiska użytkownika na ekranie głównym narzędzia.
- Threat Modeling Tool teraz dziedziczy ustawienia protokołu TLS systemu operacyjnego hosta i jest obsługiwane w środowiskach wymagających protokołu TLS 1,2 lub nowszego.

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="model-validation-option"></a>Opcja walidacji modelu

Na podstawie opinii klientów dodano opcję do narzędzia w celu włączenia lub wyłączenia walidacji modelu. Wcześniej, jeśli szablon używał pojedynczego przepływu danych jednokierunkowe między dwoma obiektami, w ramce komunikatów może zostać wyświetlony komunikat o błędzie z informacją: Obiektname wymaga co najmniej jednego "any". Wyłączenie walidacji modelu uniemożliwi wyświetlanie tych ostrzeżeń w widoku.

Opcja przełączania i wyłączania walidacji modelu znajduje się w menu > Ustawienia > Opcje. Wartość domyślna tego ustawienia jest wyłączona.

![Opcja walidacji modelu](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="known-issues"></a>Znane problemy

### <a name="unsupported-systems"></a>Nieobsługiwane systemy

#### <a name="issue"></a>Problem

Użytkownicy systemów Windows 10, którzy nie mogą zainstalować programu .NET 4.7.1 lub nowszego, na przykład Windows 10 Enterprise LTSB (wersja 1507), nie będą mogli otworzyć narzędzia po uaktualnieniu. Te starsze wersje systemu Windows nie są już obsługiwanymi platformami dla Threat Modeling Tool i nie powinny instalować najnowszych aktualizacji.

#### <a name="workaround"></a>Obejście

Użytkownicy systemu Windows 10 Enterprise LTSB (wersja 1507) z zainstalowaną najnowszą aktualizacją mogą przywrócić poprzednią wersję Threat Modeling Tool za pomocą okna dialogowego odinstalowywania w obszarze Aplikacje & funkcje.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](threat-modeling-tool.md)i zawiera informacje na [temat korzystania z narzędzia](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
