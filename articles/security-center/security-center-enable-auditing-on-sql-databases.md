---
title: Włącz inspekcję i wykrywania zagrożeń baz danych SQL w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób wykonania zalecenia Centrum zabezpieczeń Azure ** włączyć inspekcję i wykrywania zagrożeń na baz danych SQL **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866205"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Włącz inspekcję i wykrywania zagrożeń baz danych SQL w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure zostanie zaleca włączenie inspekcji i wykrywania zagrożeń dla wszystkich baz danych SQL, jeśli inspekcja i wykrywanie zagrożeń nie jest jeszcze włączone. Inspekcja i jakie wykrywania pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

Po włączeniu inspekcji można skonfigurować ustawienia wykrywanie zagrożeń i wiadomości e-mail, aby otrzymywać alerty zabezpieczeń. Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. Umożliwia to wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania.

To zalecenie stosuje się do usługi Azure SQL. nie ma wśród nich SQL uruchomionych na maszynach wirtualnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W **zalecenia** bloku, wybierz opcję **Włączanie inspekcji i wykrywanie zagrożeń baz danych SQL**.  Spowoduje to otwarcie **Włączanie inspekcji i wykrywanie zagrożeń baz danych SQL** bloku.

   ![Włączanie inspekcji dla baz danych SQL][1]
2. Wybierz bazy danych SQL, aby włączyć inspekcję. Spowoduje to otwarcie **Inspekcja i wykrywanie zagrożeń** bloku.

3. Na **Inspekcja i wykrywanie zagrożeń** bloku, wybierz opcję **ON** w obszarze **inspekcji**.

   ![Włączanie inspekcji i wykrywania zagrożeń][2]
4. Postępuj zgodnie z instrukcjami [wykrywanie zagrożeń bazy danych SQL w portalu Azure](../sql-database/sql-database-threat-detection-portal.md) włączać i konfigurować wykrywanie zagrożeń i skonfigurować listę wiadomości e-mail, które będą wysyłane alerty zabezpieczeń po wykryciu nietypowych działań.

## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób wykonania zalecenia Centrum zabezpieczeń "Włącz Inspekcja i wykrywanie zagrożeń baz danych SQL." Aby dowiedzieć się więcej na temat zabezpieczania bazy danych SQL, zobacz następujące tematy:

* [Zabezpieczanie bazy danych SQL](../sql-database/sql-database-security-overview.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
