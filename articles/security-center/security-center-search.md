---
title: Centrum zabezpieczeń Azure search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Centrum zabezpieczeń Azure używa wyszukiwania analizy dzienników do pobierania i analizowania danych zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866401"
---
# <a name="azure-security-center-search"></a>Centrum zabezpieczeń Azure search
Centrum zabezpieczeń Azure używa [wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md) pobierania i analizowania danych zabezpieczeń. Log Analytics obejmuje język kwerendy szybkie pobieranie i skonsolidować danych. Centrum zabezpieczeń można wykorzystać analizy dzienników wyszukiwania można tworzyć zapytania i analizowanie zebranych danych.

Wyszukiwanie jest dostępne w warstwie bezpłatna i warstwy standardowa, Centrum zabezpieczeń.  Danych dostępnych w wyszukiwaniach dziennika jest zależna od poziomu warstwy stosowane do swojego obszaru roboczego.  Zobacz temat w Centrum zabezpieczeń [cennikiem](../security-center/security-center-pricing.md) Aby uzyskać więcej informacji.


> [!NOTE]
> Centrum zabezpieczeń nie są zapisywane dane zabezpieczeń dla obszaru roboczego w warstwie bezpłatna. Różne dzienniki mogą wysyłać do obszaru roboczego w warstwie bezpłatna i wyszukiwania danych, ale wyniki wyszukiwania nie zawierają danych z Centrum zabezpieczeń. Centrum zabezpieczeń tylko zapisuje dane do obszaru roboczego w warstwie standardowa.
>
>

## <a name="access-search"></a>Dostęp do wyszukiwania
1. W menu głównym Centrum zabezpieczeń, wybierz **wyszukiwania**.

  ![Wybierz dziennik wyszukiwania][1]

2. Centrum zabezpieczeń zawiera listę wszystkich obszarów roboczych w obszarze subskrypcji platformy Azure. Wybierz obszar roboczy. (Jeśli masz tylko jeden obszar roboczy selektor ten obszar roboczy nie uwzględnia).

  ![Wybierz obszar roboczy][2]

3. **Zaloguj się wyszukiwania** otwiera. Aby wyszukać więcej danych w ramach wybranego obszaru roboczego, wpisz to przykładowe zapytanie:

  SecurityEvent | gdzie EventID == 4625 | Podsumowanie funkcji count() przez TargetAccount

  Wynik zawiera wszystkie konta, które nie powiodło się zalogowanie (zdarzenie 4625).

  ![Wyniki wyszukiwania][3]

Zobacz [języka zapytań usługi Analiza dzienników](../log-analytics/log-analytics-search-reference.md) Aby uzyskać więcej informacji na temat sposobu zapytania dla danych w ramach wybranego obszaru roboczego.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób wyszukiwania w Centrum zabezpieczeń dostępu. Centrum zabezpieczeń używa analizy dzienników wyszukiwania. Aby dowiedzieć się więcej na temat wyszukiwania analizy dzienników, zobacz:

- [Co to jest usługa Log Analytics?](../log-analytics/log-analytics-overview.md) — Przegląd na analizy dzienników
- [Opis dziennika przeszukuje analizy dzienników](../log-analytics/log-analytics-log-search-new.md) — w tym artykule opisano, jak dziennik wyszukiwania są używane w analizy dzienników i udostępnia pojęcia, które należy zrozumieć przed utworzeniem wyszukiwania dziennika
- [Wyszukiwanie danych przy użyciu dziennika wyszukiwania w analizy dzienników](../log-analytics/log-analytics-log-searches.md) — samouczek przy użyciu wyszukiwania dziennika
- [Odwołanie wyszukiwania analizy dzienników](../log-analytics/log-analytics-search-reference.md) — w tym artykule opisano język zapytań w analizy dzienników

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz:

- [Omówienie Centrum zabezpieczeń](security-center-intro.md) — Centrum zabezpieczeń w tym artykule opisano kluczowe możliwości

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
