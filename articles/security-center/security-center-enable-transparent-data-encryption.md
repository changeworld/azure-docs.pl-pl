---
title: Włącz Transparent Data Encryption w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób implementacji zalecenia Azure Security Center **włączyć transparent Data Encryption**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704036"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Włącz Transparent Data Encryption w Azure Security Center
Azure Security Center zaleca włączenie Transparent Data Encryption (TDE) w bazach danych SQL, jeśli TDE nie jest jeszcze włączona. TDE chroni dane i pomaga spełnić wymagania dotyczące zgodności przez szyfrowanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w spoczynku, bez konieczności wprowadzania zmian w aplikacji. Aby dowiedzieć się więcej, zobacz [transparent Data Encryption z Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

To zalecenie ma zastosowanie tylko do usługi Azure SQL. nie obejmuje SQL uruchomionego na maszynach wirtualnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. W bloku **zalecenia** wybierz pozycję **Włącz transparent Data Encryption**.
   ![Włączanie technologii Transparent Data Encryption][1]
2. Spowoduje to otwarcie bloku **włączanie transparent Data Encryption w bazach danych SQL** . Wybierz bazę danych SQL, w której ma zostać włączone TDE.
   ![Wybierz bazę danych SQL, aby włączyć TDE][2]
3. W bloku **przezroczyste szyfrowanie danych** wybierz pozycję **włączone** w obszarze Szyfrowanie danych i wybierz pozycję **Zapisz** na górnej Wstążce bloku.
   ![Włącz TDE][3]

   Po włączeniu TDE w wybranej bazie danych SQL **Stan szyfrowania** zmieni się na **zaszyfrowany**.    

   ![Stan szyfrowania][4]

## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób implementacji zalecenia Security Center "Włącz Transparent Data Encryption". Aby dowiedzieć się więcej na temat programu SQL TDE, zobacz następujące tematy:

* [Transparent Data Encryption z Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Wprowadzenie do Transparent Data Encryption (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
