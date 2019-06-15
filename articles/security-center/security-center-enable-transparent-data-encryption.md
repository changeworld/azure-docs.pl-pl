---
title: Włączanie technologii Transparent Data Encryption w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Włączanie funkcji Transparent Data Encryption**.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704036"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Włączanie technologii Transparent Data Encryption w usłudze Azure Security Center
Usługa Azure Security Center zaleci, Włącz przezroczystego szyfrowania danych (TDE) dla baz danych SQL, jeśli funkcja TDE nie jest już włączona. Funkcja TDE chroni dane i ułatwia spełnianie wymagań dotyczących zgodności przez szyfrowanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji. Aby dowiedzieć się więcej, zobacz [funkcji Transparent Data Encryption z usługą Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

To zalecenie dotyczy tylko usługi Azure SQL nie obejmuje środowisko SQL uruchomionych na maszynach wirtualnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Włączanie funkcji Transparent Data Encryption**.
   ![Włączanie technologii Transparent Data Encryption][1]
2. Spowoduje to otwarcie **Włączanie funkcji Transparent Data Encryption w bazach danych SQL** bloku. Wybierz, aby włączyć funkcję TDE w bazie danych SQL.
   ![Wybierz bazy danych SQL, aby włączyć funkcję TDE w][2]
3. Na **technologii Transparent data encryption** bloku wybierz **ON** szyfrowania danych i wybierz **Zapisz** na Wstążce górnej części bloku.
   ![Włączanie funkcji TDE][3]

   Po włączeniu funkcji TDE w wybranej bazie danych SQL **stanu szyfrowania** zmieni się na **zaszyfrowane**.    

   ![Stan szyfrowania][4]

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Włącz Transparent Data Encryption". Aby dowiedzieć się więcej na temat funkcji SQL TDE, zobacz następujące tematy:

* [Przezroczyste szyfrowanie danych za pomocą usługi Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Rozpoczynanie pracy z przezroczystego szyfrowania danych (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
