---
title: Włącz przezroczystego szyfrowania danych w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób wykonania zalecenia Centrum zabezpieczeń Azure ** włączyć przezroczysty danych szyfrowania **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866296"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Włącz przezroczystego szyfrowania danych w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure zaleca Włącz funkcji przezroczystego szyfrowania danych (TDE) baz danych SQL, jeśli nie jest już włączona funkcji TDE. Funkcji TDE chroni dane i pomaga spełnić wymagania zgodności przez zaszyfrowanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku, bez konieczności wprowadzania zmian w aplikacji. Aby dowiedzieć się więcej, zobacz [przezroczystego szyfrowania danych z bazy danych SQL Azure](https://msdn.microsoft.com/library/dn948096).

To zalecenie stosuje się do usługi Azure SQL. nie zawiera SQL uruchomionych na maszynach wirtualnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W **zalecenia** bloku, wybierz opcję **włączyć przezroczystego szyfrowania danych**.
   ![Włączanie technologii Transparent Data Encryption][1]
2. Spowoduje to otwarcie **włączyć przezroczystego szyfrowania danych w bazach danych SQL** bloku. Wybierz bazę danych SQL, Włącz funkcji TDE.
   ![Wybierz bazy danych SQL, Włącz funkcji TDE][2]
3. Na **przezroczystego szyfrowania danych** bloku, wybierz opcję **ON** szyfrowania danych i wybierz **zapisać** na Wstążce górnej części bloku.
   ![Włączanie funkcji TDE][3]

   Po włączeniu funkcji TDE na wybranej bazy danych SQL **stanu szyfrowania** zmieni się na **zaszyfrowane**.    

   ![Stan szyfrowania][4]

## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób wykonania zalecenia Centrum zabezpieczeń "Włącz przezroczystego szyfrowania danych." Aby dowiedzieć się więcej na temat funkcji SQL TDE, zobacz następujące tematy:

* [Przezroczystego szyfrowania danych z bazy danych Azure SQL](https://msdn.microsoft.com/library/dn948096)
* [Rozpoczynanie pracy z przezroczystym danych szyfrowania (funkcji TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje zabezpieczeń platformy Azure i informacje.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
