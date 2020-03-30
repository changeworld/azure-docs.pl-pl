---
title: Microsoft Threat Modeling Tool wydanie 1/29/2019
titleSuffix: Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269772"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.60126.1 — 2019-01-29

Wersja 7.1.60126.1 narzędzia Microsoft Threat Modeling Tool została wydana 29 stycznia 2019 r. i zawiera następujące zmiany:

- Minimalna wymagana wersja platformy .NET została zwiększona do [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimalna wymagana wersja systemu Windows została zwiększona do [aktualizacji rocznicowej systemu Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ze względu na zależność .NET.
- Funkcja przełączania sprawdzania poprawności modelu została dodana do menu Opcje narzędzia.
- Zaktualizowano kilka łączy we właściwościach zagrożenia.
- Drobne zmiany ux na ekranie głównym narzędzia.
- Narzędzie do modelowania zagrożeń dziedziczy teraz ustawienia TLS systemu operacyjnego hosta i jest obsługiwane w środowiskach wymagających protokołu TLS 1.2 lub nowszych.

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="model-validation-option"></a>Opcja sprawdzania poprawności modelu

Na podstawie opinii klientów dodano opcję do narzędzia, aby włączyć lub wyłączyć sprawdzanie poprawności modelu. Wcześniej, jeśli szablon używany pojedynczy jednokierunkowy przepływ danych między dwoma obiektami, być może otrzymałeś komunikat o błędzie w ramce Wiadomości z informacją: ObjectsName wymaga co najmniej jednego "Any". Wyłączenie sprawdzania poprawności modelu uniemożliwi wyświetlanie tych ostrzeżeń w widoku.

Opcję włączania i wyłączania sprawdzania poprawności modelu można znaleźć w menu Opcje >ustawienia >plików. Domyślną wartością tego ustawienia jest Wyłączona.

![Opcja sprawdzania poprawności modelu](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa aktualizacja systemu Microsoft Windows 10 lub nowsza](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Wymagana wersja .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowsze
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów wymagane jest połączenie z Internetem.

## <a name="known-issues"></a>Znane problemy

### <a name="unsupported-systems"></a>Systemy nieobsługiwały

#### <a name="issue"></a>Problem

Użytkownicy systemów Windows 10, których nie można zainstalować w systemie .NET 4.7.1 lub nowszym, takich jak Windows 10 Enterprise LTSB (wersja 1507), nie będą mogli otworzyć narzędzia po uaktualnieniu. Te starsze wersje systemu Windows nie są już obsługiwane platformy dla narzędzia do modelowania zagrożeń i nie należy instalować najnowszej aktualizacji.

#### <a name="workaround"></a>Obejście

Użytkownicy systemu Windows 10 Enterprise LTSB (wersja 1507), którzy zainstalowali najnowszą aktualizację, mogą powrócić do poprzedniej wersji narzędzia do modelowania zagrożeń za pomocą okna dialogowego odinstalowywania w aplikacji & funkcje.

## <a name="documentation-and-feedback"></a>Dokumentacja i informacje zwrotne

- Dokumentacja narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](threat-modeling-tool.md)i zawiera informacje [o użyciu narzędzia.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [narzędzia Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
