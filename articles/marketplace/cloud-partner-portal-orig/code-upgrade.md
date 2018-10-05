---
title: Uaktualnienie kodu do najnowszej wersji platformy | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób uaktualniania usługi Microsoft Dynamics 365 for Operations, wersja platformy do najnowszej wersji platformy
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810619"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Uaktualnienie kodu do najnowszej wersji platformy

W tym artykule opisano sposób uaktualniania usługi Microsoft Dynamics 365 for Operations, wersja platformy do najnowszej wersji platformy.

## <a name="overview"></a>Przegląd

Dynamics 365 firmy Microsoft dla platformy operacje składa się z następujących składników:

Dynamics 365 dla danych binarnych platformy operacje, takie jak serwer aplikacji (AOS), platformę zarządzania danych, raportowania i business intelligence (BI) framework, narzędzia programistyczne i usługi analizy. Następujące pakiety drzewa obiektów aplikacji (AOT):

1. Platforma aplikacji
2. Application Foundation
3. Podstawy testów

**Ważne**: Aby przejść do najnowszej Dynamics 365 dla platformy, operacje, usługi Dynamics 365 do wykonania operacji nie może zawierać wszystkie dostosowania (overlayering) żadnych pakietów drzewa obiektów aplikacji, które należą do korzystania z platformy. To ograniczenie wprowadzono w programie platformy, aktualizacja 3, umożliwiając bezproblemowe ciągłych aktualizacji może się do korzystania z platformy. Jeśli są uruchomione na platformie, która jest starsza niż platformy z aktualizacją 3, zobacz uaktualnianie do platformy, aktualizacja 3 z wcześniejszej sekcji kompilacji na końcu tego artykułu.

Aby uzyskać więcej informacji dotyczących uaktualniania kodu, można znaleźć [tutaj](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).