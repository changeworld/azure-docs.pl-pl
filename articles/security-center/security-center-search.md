---
title: Wyszukiwanie Azure Security Center | Microsoft Docs
description: Dowiedz się, jak Azure Security Center używa wyszukiwania dzienników Azure Monitor do pobierania i analizowania danych zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662924"
---
# <a name="azure-security-center-search-retired"></a>Wyszukiwanie Azure Security Center (wycofane)

> [!NOTE]
> Pulpit nawigacyjny wyszukiwania Security Center został wycofany 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_search).

Azure Security Center używa [wyszukiwania dzienników Azure monitor](../log-analytics/log-analytics-log-searches.md) do pobierania i analizowania danych zabezpieczeń. Dzienniki Azure Monitor zawierają język zapytań umożliwiający szybkie pobieranie i konsolidowanie danych. W Security Center można wykorzystać wyszukiwanie dzienników Azure Monitor do konstruowania zapytań i analizowania zebranych danych.

Wyszukiwanie jest dostępne zarówno w warstwie Bezpłatna, jak i w warstwie Standardowa Security Center.  Dane dostępne w przeszukiwaniu dzienników są zależne od poziomu warstwy zastosowanej do obszaru roboczego.  Aby uzyskać więcej informacji, zobacz [stronę](../security-center/security-center-pricing.md) z cennikiem Security Center.


> [!NOTE]
> Security Center nie zapisuje danych zabezpieczeń dla obszaru roboczego w ramach warstwy Bezpłatna. Można wysłać wiele dzienników do obszaru roboczego w ramach warstwy Bezpłatna i przeszukać te dane, ale wyniki wyszukiwania nie zawierają danych z Security Center. Security Center zapisywanie danych tylko w obszarze roboczym w warstwie Standardowa.
>
>

## <a name="access-search"></a>Wyszukiwanie dostępu
1. W menu głównym Security Center wybierz pozycję **Wyszukaj**.

   ![Wybieranie przeszukiwania dzienników][1]

2. Security Center wyświetla listę wszystkich obszarów roboczych w ramach subskrypcji platformy Azure. Wybierz obszar roboczy. (Jeśli masz tylko jeden obszar roboczy, ten selektor obszaru roboczego nie jest wyświetlany).

   ![Wybierz obszar roboczy][2]

3. Zostanie otwarte **Wyszukiwanie w dzienniku** . Aby wykonać zapytanie o więcej danych w wybranym obszarze roboczym, wprowadź następujące przykładowe zapytanie:

   SecurityEvent | gdzie EventID = = 4625 | Sumuj liczbę () według TargetAccount

   Wyniki przedstawiają wszystkie konta, które nie zakończyły się logowaniem (zdarzenie 4625).

   ![Wyniki wyszukiwania][3]

Zobacz [język zapytań Kusto](../log-analytics/log-analytics-search-reference.md) , aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących danych w wybranym obszarze roboczym.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób uzyskiwania dostępu do wyszukiwania w Security Center. Security Center używa wyszukiwania dzienników Azure Monitor. Aby dowiedzieć się więcej o wyszukiwaniu dzienników Azure Monitor, zobacz:

- [Co to jest Azure Monitor Logs?](../log-analytics/log-analytics-overview.md) — Omówienie dzienników Azure Monitor
- [Informacje o przeszukiwaniu dzienników w](../log-analytics/log-analytics-log-search-new.md) dziennikach Azure monitor — opis sposobu używania wyszukiwania w dziennikach w dziennikach Azure monitor i przedstawiono pojęcia, które należy zrozumieć przed utworzeniem przeszukiwania dzienników
- Wyszukiwanie [danych przy użyciu wyszukiwania dzienników w](../log-analytics/log-analytics-log-searches.md) dziennikach Azure monitor — samouczek dotyczący korzystania z przeszukiwania dzienników
- [Odwołanie wyszukiwania Kusto](../log-analytics/log-analytics-search-reference.md) — opisuje język zapytań w dziennikach Azure monitor

Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

- [Omówienie Security Center](security-center-intro.md) — opisuje kluczowe możliwości Security Center

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
