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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776815"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Uaktualnienie kodu do najnowszej wersji platformy

W tym artykule opisano sposób uaktualniania usługi Microsoft Dynamics 365 for Operations, wersja platformy do najnowszej wersji platformy.

## <a name="overview"></a>Omówienie

Dynamics 365 firmy Microsoft dla platformy operacje składa się z następujących składników:

Dynamics 365 dla danych binarnych platformy operacje, takie jak serwer aplikacji (AOS), platformę zarządzania danych, raportowania i business intelligence (BI) framework, narzędzia programistyczne i usługi analizy. Następujące pakiety drzewa obiektów aplikacji (AOT):

1. Platforma aplikacji
2. Application Foundation
3. Test Essentials

**Ważne**: Aby przejść do najnowszej Dynamics 365 dla platformy, operacje, usługi Dynamics 365 do wykonania operacji nie może zawierać wszystkie dostosowania (nakładania) żadnych pakietów drzewa obiektów aplikacji, które należą do korzystania z platformy. To ograniczenie wprowadzono w programie platformy, aktualizacja 3, umożliwiając bezproblemowe ciągłych aktualizacji może się do korzystania z platformy. Jeśli są uruchomione na platformie, która jest starsza niż platformy z aktualizacją 3, zobacz uaktualnianie do platformy, aktualizacja 3 z wcześniejszej sekcji kompilacji na końcu tego artykułu.

Aby uzyskać więcej informacji dotyczących uaktualniania kodu, można znaleźć [tutaj](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).