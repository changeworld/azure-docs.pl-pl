---
title: Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1108265101f37433860d0112e4e80aee0002ab5c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127229"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL w usłudze Azure Security Center
Usługa Azure Security Center zaleci, że włączenie inspekcji i wykrywania zagrożeń dla wszystkich baz danych SQL w przypadku inspekcji i wykrywania zagrożeń nie jest włączony. Inspekcja i ochronę przed zagrożeniami wykrywania może pomóc zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

Po włączeniu inspekcji można skonfigurować ustawień wykrywania zagrożeń i wiadomości e-mail, aby otrzymywać alerty zabezpieczeń. Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. Umożliwia to wykrywanie oraz reagowanie na potencjalne zagrożenia w miarę ich występowania.

To zalecenie dotyczy tylko usługi Azure SQL nie zawiera SQL uruchomionych na maszynach wirtualnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL**.  Spowoduje to otwarcie **Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL** bloku.

   ![Włączanie inspekcji dla baz danych SQL][1]
2. Wybierz bazę danych SQL do włączania inspekcji. Spowoduje to otwarcie **Inspekcja i wykrywanie zagrożeń** bloku.

3. Na **Inspekcja i wykrywanie zagrożeń** bloku wybierz **ON** w obszarze **inspekcji**.

   ![Włącz inspekcję i wykrywanie zagrożeń][2]
4. Postępuj zgodnie z instrukcjami w [wykrywania zagrożeń bazy danych SQL w witrynie Azure portal](../sql-database/sql-database-threat-detection-portal.md) można włączać i konfigurować wykrywanie zagrożeń i skonfigurować listę adresów e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL." Aby dowiedzieć się więcej na temat zabezpieczania bazy danych SQL, zobacz następujące tematy:

* [Zabezpieczanie bazy danych SQL](../sql-database/sql-database-security-overview.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
