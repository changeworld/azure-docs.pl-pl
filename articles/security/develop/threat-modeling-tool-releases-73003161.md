---
title: Microsoft Threat Modeling Tool release 03/22/2020 - Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146864"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Aktualizacja narzędzia do modelowania zagrożeń wersja 7.3.00316.1 - 22.03.2020

Wersja 7.3.00316.1 narzędzia Microsoft Threat Modeling Tool (TMT) została wydana 22 marca 2020 r. i zawiera następujące zmiany:

- Usprawnienia w zakresie ułatwień dostępu
- Poprawki błędów
- Nowa funkcja DiagramReader

## <a name="notable-bug-fixes"></a>Godne uwagi poprawki błędów

### <a name="exporting-the-threat-list-to-csv"></a>Eksportowanie listy zagrożeń do pliku CSV

Funkcja eksportu do pliku CSV niespójnie wybierała pola z listy zagrożeń, które miały być eksportowane. Teraz wszystkie pola z listy zagrożeń zostaną wyeksportowane do pliku CSV. 

### <a name="ux-bugs"></a>Błędy UX

- Menu pomocy w podstawowym przepływie pracy (tworzenie/otwieranie/analizowanie) i edytor szablonów mają teraz spójne opcje menu.
- Pasek wyszukiwania w okienku wzorników ma teraz standardowy kursor i dodano odpowiednie etykiety.

## <a name="new-features"></a>Nowe funkcje

### <a name="diagramreader-feature-has-been-added"></a>Dodano funkcję DiagramReader

Nowa funkcja DiagramReader została dodana w menu głównym, gdy model jest otwarty. Ta funkcja spowoduje przekształcenie graficznej reprezentacji modelu w narrację tekstową. 

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemu operacyjne:
  - [Rocznicowa aktualizacja systemu Microsoft Windows 10 lub nowsza](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Wymagana wersja .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowsze
- Dodatkowe wymagania:
  - Połączenie internetowe do odbierania aktualizacji narzędzia, a także szablonów

## <a name="documentation-and-feedback"></a>Dokumentacja i informacje zwrotne

- Dokumentacja narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje [o użyciu narzędzia.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [narzędzia Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
