---
title: Wyszukiwanie w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure Security Center używa wyszukiwania dzienników usługi Azure Monitor do pobierania i analizowania danych zabezpieczeń.
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
ms.openlocfilehash: 90dbb33fa516d3a831d4e60969ac6b6c8312d539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574145"
---
# <a name="azure-security-center-search"></a>Wyszukiwanie w usłudze Azure Security Center

> [!NOTE]
> Security Center wyszukiwanie z pulpitu nawigacyjnego zostaną wycofane z dniem 31 lipca 2019 r. Aby uzyskać więcej informacji i alternatywne usług zobacz [wycofywania Centrum zabezpieczeń funkcji (2019 lipca)](security-center-features-retirement-july2019.md#menu_search).

Usługa Azure Security Center używa [usługi Azure Monitor dzienniki wyszukiwania](../log-analytics/log-analytics-log-searches.md) pobieranie i analizowanie danych zabezpieczeń. Dzienniki platformy Azure Monitor obejmuje język zapytań, można szybko pobierać i konsolidować dane. W usłudze Security Center możesz korzystać z wyszukiwania dzienników usługi Azure Monitor do tworzenia zapytań i analizowania zebranych danych.

Wyszukiwanie jest dostępne w warstwie bezpłatnej i warstwie standardowa usługi Security Center.  Dostępne w Twojej wyszukiwań w dziennikach danych jest zależny od poziomów warstw zastosowane do Twojego obszaru roboczego.  Odwiedź Centrum zabezpieczeń [stronę z cennikiem](../security-center/security-center-pricing.md) Aby uzyskać więcej informacji.


> [!NOTE]
> Usługa Security Center nie powoduje zapisania danych zabezpieczeń dla obszaru roboczego w ramach warstwy bezpłatna. Różne dzienniki można wysyłać do obszaru roboczego w ramach warstwy bezpłatna i wyszukiwania na tych danych, ale wyniki wyszukiwania nie zawierają danych z usługi Security Center. Usługa Security Center tylko zapisuje dane do obszaru roboczego w ramach warstwy Standard.
>
>

## <a name="access-search"></a>Dostęp do wyszukiwania
1. W menu głównym usługi Security Center wybierz **wyszukiwania**.

   ![Wybierz wyszukiwanie w Dzienniku][1]

2. Usługa Security Center wyświetla listę wszystkich obszarów roboczych w ramach subskrypcji platformy Azure. Wybierz obszar roboczy. (Jeśli masz tylko jeden obszar roboczy, nie ma tego selektora obszaru roboczego.)

   ![Wybierz obszar roboczy][2]

3. **Wyszukiwania w dzienniku** zostanie otwarty. Aby wysłać zapytanie do większej ilości danych w ramach wybranego obszaru roboczego, należy wprowadzić to przykładowe zapytanie:

   SecurityEvent | gdzie EventID == 4625 | Podsumowanie count() by TargetAccount

   Wynik zawiera wszystkie konta, których nie udało się zalogować (zdarzenie 4625).

   ![Wyniki wyszukiwania][3]

Zobacz [język zapytania Kusto](../log-analytics/log-analytics-search-reference.md) Aby uzyskać więcej informacji na temat wykonywania zapytań dla danych w ramach wybranego obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób dostępu do wyszukiwania w usłudze Security Center. Centrum zabezpieczeń używa usługi Azure Monitor dzienniki wyszukiwania. Aby dowiedzieć się więcej na temat wyszukiwania dzienników usługi Azure Monitor, zobacz:

- [Co to jest dzienniki usługi Azure Monitor?](../log-analytics/log-analytics-overview.md) — Rejestruje Omówienie usługi Azure Monitor
- [Understanding log wyszukiwania w dziennikach w usłudze Azure Monitor](../log-analytics/log-analytics-log-search-new.md) — zawiera opis używania wyszukiwań w dziennikach w usłudze Azure Monitor i oferuje pojęcia, które należy rozumieć, przed utworzeniem przeszukiwania dzienników
- [Wyszukiwanie danych przy użyciu wyszukiwań w dziennikach w usłudze Azure Monitor](../log-analytics/log-analytics-log-searches.md) — samouczek na temat korzystania z wyszukiwania w Dzienniku
- [Dokumentacja wyszukiwania Kusto](../log-analytics/log-analytics-search-reference.md) — w tym artykule opisano język zapytań w dziennikach w usłudze Azure Monitor

Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

- [Security Center — Przegląd](security-center-intro.md) — Centrum zabezpieczeń w tym artykule opisano kluczowe funkcje

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
