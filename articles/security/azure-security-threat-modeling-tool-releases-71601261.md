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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586244"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.60126.1 — 2019-01-29

Narzędzie do modelowania zagrożeń firmy Microsoft w wersji 7.1.60126.1 został wydany 2019 29 stycznia i zawiera następujące zmiany:

- Minimalna wymagana wersja programu .NET został zwiększony do [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimalną wymaganą wersją Windows została zwiększona do [Rocznicowej aktualizacji systemu Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) z powodu zależności platformy .NET.
- Funkcja Przełącz Walidacja modelu została dodana do menu Opcje narzędzia.
- Linki we właściwościach zagrożeń zostały zaktualizowane.
- UX drobne zmiany do ekranu głównego narzędzia.
- Narzędzie do modelowania zagrożeń teraz dziedziczy ustawienia protokołu TLS w systemie operacyjnym hosta i jest obsługiwane w środowiskach, które wymagały protokołu TLS 1.2 lub nowszej.

## <a name="feature-changes"></a>Funkcja zmiany

### <a name="model-validation-option"></a>Opcja weryfikacji modelu

Na podstawie opinii klientów, dodano opcję do narzędzia, aby włączyć lub wyłączyć sprawdzanie poprawności modelu. Wcześniej Jeśli szablon przepływu danych z jednego jednokierunkowe pomiędzy dwoma obiektami, może trafiła do Ciebie komunikat o błędzie stwierdzający ramki wiadomości: ObjectsName wymaga co najmniej jednego "Any". Wyłączenie sprawdzania poprawności modelu uniemożliwia te ostrzeżenia z wyświetlonymi w widoku.

Opcja przełączania weryfikacji modelu, włączać i wyłączać można znaleźć w pliku -> Ustawienia -> Opcje menu. Wartość domyślna to ustawienie jest wyłączone.

![Opcja weryfikacji modelu](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Program Microsoft Windows Update rozliczenia 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowszej
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszej
- Wymagania dodatkowe
  - Aby otrzymywać aktualizacje do narzędzia, jak również szablony, wymagane jest połączenie internetowe.

## <a name="known-issues"></a>Znane problemy

### <a name="unsupported-systems"></a>Nieobsługiwana systemów

#### <a name="issue"></a>Problem

Użytkownicy systemów Windows 10, których nie można zainstalować program .NET 4.7.1 lub nowszym, takich jak Windows 10 Enterprise LTSB (w wersji 1507), będzie mógł otworzyć narzędzie po uaktualnieniu. Tych starszych wersjach systemu Windows nie są już obsługiwane platformy pod kątem narzędzie do modelowania zagrożeń i nie należy instalować najnowszej aktualizacji.

#### <a name="workaround"></a>Obejście

Użytkownicy systemu Windows 10 Enterprise LTSB (w wersji 1507), na których zainstalowano najnowszą aktualizację można przywrócić do poprzedniej wersji narzędzia do modelowania zagrożeń za pomocą okna dialogowego Odinstaluj w aplikacje i funkcje.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja dla narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje o [dotyczące korzystania z narzędzia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Kolejne kroki

Pobierz najnowszą wersję [narzędzie do modelowania zagrożeń firmy Microsoft](https://aka.ms/threatmodelingtool).
