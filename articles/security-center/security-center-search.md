---
title: Wyszukiwanie w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure Security Center używa wyszukiwania usługi Log Analytics do pobierania i analizowania danych zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 19aed3d3da1250e6ad47bf9266f2e57aa255156a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963781"
---
# <a name="azure-security-center-search"></a>Wyszukiwanie w usłudze Azure Security Center
Usługa Azure Security Center używa [wyszukiwania usługi Log Analytics](../log-analytics/log-analytics-log-searches.md) pobieranie i analizowanie danych zabezpieczeń. Usługa log Analytics obejmuje język zapytań, można szybko pobierać i konsolidować dane. W usłudze Security Center możesz korzystać z wyszukiwania usługi Log Analytics w celu utworzenia zapytań i analizowania zebranych danych.

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

  Wynik zawiera wszystkie konta, które nie powiodło się zalogowanie (zdarzenie 4625).

  ![Wyniki wyszukiwania][3]

Zobacz [języka zapytań usługi Log Analytics](../log-analytics/log-analytics-search-reference.md) Aby uzyskać więcej informacji na temat wykonywania zapytań dla danych w ramach wybranego obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób dostępu do wyszukiwania w usłudze Security Center. Centrum zabezpieczeń używa wyszukiwania usługi Log Analytics. Aby dowiedzieć się więcej na temat wyszukiwania usługi Log Analytics, zobacz:

- [Co to jest usługa Log Analytics?](../log-analytics/log-analytics-overview.md) — Omówienie w usłudze Log Analytics
- [Understanding log wyszukiwania w usłudze Log Analytics](../log-analytics/log-analytics-log-search-new.md) — zawiera opis używania przeszukiwania dzienników w usłudze Log Analytics i zapewnia pojęcia, które należy rozumieć, przed utworzeniem przeszukiwania dzienników
- [Wyszukiwanie danych przy użyciu przeszukiwania dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md) — samouczek na temat korzystania z wyszukiwania w Dzienniku
- [Usługa log Analytics Wyszukaj odwołanie](../log-analytics/log-analytics-search-reference.md) — w tym artykule opisano język zapytań w usłudze Log Analytics

Aby dowiedzieć się więcej o usłudze Security Center, zobacz:

- [Security Center — Przegląd](security-center-intro.md) — Centrum zabezpieczeń w tym artykule opisano kluczowe funkcje

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
