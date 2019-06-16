---
title: Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 5b41af83122d74fc766e6c5179d98803979269f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704660"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL w usłudze Azure Security Center
Usługa Azure Security Center zaleci, Włącz inspekcję i wykrywanie zagrożeń dla wszystkich baz danych na serwerach Azure SQL, jeśli inspekcji nie jest włączony. Inspekcja i ochronę przed zagrożeniami wykrywania może pomóc zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

Po włączeniu inspekcji można skonfigurować ustawień wykrywania zagrożeń i wiadomości e-mail, aby otrzymywać alerty zabezpieczeń. Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. Umożliwia to wykrywanie oraz reagowanie na potencjalne zagrożenia w miarę ich występowania.

To zalecenie dotyczy tylko usługi Azure SQL nie zawiera programu SQL Server uruchomionego na maszynach wirtualnych w usługach infrastruktury platformy Azure (IaaS platformy Azure).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL**.  Spowoduje to otwarcie **Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL** bloku.

   ![Włączanie inspekcji dla serwerów SQL][1]
2. Wybierz serwer SQL, aby włączyć inspekcję i wykrywanie zagrożeń na. Spowoduje to otwarcie **Inspekcja i wykrywanie zagrożeń** bloku.

3. Na **Inspekcja i wykrywanie zagrożeń** bloku wybierz **ON** w obszarze **inspekcji**.

   ![Włącz ustawienia inspekcji][2]
4. Postępuj zgodnie z instrukcjami w [inspekcji usługi SQL database w witrynie Azure portal](../sql-database/sql-database-auditing-portal.md) skonfigurować Magazyn przechowywania dzienników inspekcji. Konto magazynu dla subskrypcji do zbierania danych jest domyślne konto magazynu.
5. Postępuj zgodnie z instrukcjami w [Rozpoczynanie pracy z usługą wykrywania zagrożeń bazy danych SQL](../sql-database/sql-database-threat-detection.md) można włączać i konfigurować wykrywanie zagrożeń i skonfigurować listę adresów e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano sposób implementacji usługi Security Center zaleceń "Włącz inspekcję i wykrywanie zagrożeń na serwerach SQL." Aby dowiedzieć się więcej na temat zabezpieczania bazy danych SQL, zobacz następujące tematy:

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
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
