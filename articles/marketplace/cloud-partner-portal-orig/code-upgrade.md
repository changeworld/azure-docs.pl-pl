---
title: Uaktualnienie kodu do najnowszej wersji platformy | Portal Azure Marketplace
description: W tym temacie opisano sposób uaktualniania usługi Microsoft Dynamics 365 for Operations, wersja platformy do najnowszej wersji platformy
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935279"
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