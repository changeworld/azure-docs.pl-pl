---
title: Informacje o rachunku Azure Enterprise | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć użycia i faktury dla klientów platformy Azure przy użyciu umowy Enterprise Agreement
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: b724fc7a887550b4115a988149b4b7a6c95de830
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584472"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Opis zawartości rachunku dla klientów platformy Azure z umową Enterprise Agreement

Klienci platformy Azure z umową Enterprise Agreement otrzymywanie faktury przekraczają środków w organizacji lub korzystania z usług, które nie są objęte środków. 

Środki do Twojej organizacji obejmuje zobowiązania pieniężnego. Zobowiązanie pieniężne to Twoja organizacja płatną z góry za użycie usług platformy Azure. Kontaktując się z kierownikiem firmy Microsoft lub odsprzedawcy, można dodać środków zobowiązania pieniężnego do umowy Enterprise Agreement.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Gdy przekroczony lub nie ma zastosowania

Co najmniej jeden faktur można uzyskać w następujących przypadkach:

- **Nadwyżka usługi**: opłaty za użycie Twojej organizacji przekracza saldo środków.
- **Opłaty są naliczane oddzielnie**: usługi organizacji używane nie są objęte środków. Niezależnie od tego, Saldo środków, są fakturowane dla następujących usług:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop 
    - Zarejestrowany użytkownik
    - Openlogic
    - Dostęp zdalny praw usługa XenApp Essentials zarejestrowany użytkownik
    - Ubuntu Advantage
    - Visual Studio Enterprise (co miesiąc)
    - Visual Studio Enterprise (roczna)
    - Visual Studio Professional (miesięcznie)
    - Visual Studio Professional (roczna)
- **Opłatach w portalu Marketplace**: zakupów w portalu Azure Marketplace i użycia nie są objęte środków Twojej organizacji i są rozliczane osobno. Administrator przedsiębiorstwa może włączać i wyłączać zakupów w portalu Marketplace w organizacji w witrynie Enterprise Portal. 

Jeśli masz opłat należnych nadwyżka usługi i opłaty rozliczane oddzielnie w trakcie okresu rozliczeniowego, otrzymasz jedną fakturę, który zawiera oba rodzaje opłaty. Opłaty za pośrednictwem platform handlowych zawsze są fakturowane oddzielnie.

## <a name="review-charges"></a>Opłaty za przeglądu

Aby przejrzeć i sprawdzić opłat na fakturze, musi być administratorem przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Enterprise Agreement platformy Azure zrozumienie ról administracyjnych na platformie Azure](billing-understand-ea-roles.md). Jeśli nie wiesz, będącego przez administratora przedsiębiorstwa dla całej organizacji, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Przegląd usługi nadwyżkowe faktury

Porównanie z portalu dla przedsiębiorstw w wynikające z kwoty całkowite użycie **raporty** > **Podsumowanie użycia** za pomocą usługi fakturze za użycie nadmiarowe. Faktury za użycie nadmiarowe usługi obejmuje użycie przekraczające środki Twojej organizacji i/lub usług, które nie są objęte środków. Kwoty **Podsumowanie użycia** nie zawierają podatku. 

1. Zaloguj się do [witryny Enterprise portal](https://ea.azure.com).
1. Wybierz **raporty**.
1. W prawym górnym rogu karty, Przełącz widok z **M** do **C** i sprostania szczytowemu okres na fakturze.
 
   ![Zrzut ekranu pokazujący M + C opcji na podsumowanie użycia.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Całkowite użycie** wielkość powinna odpowiadać **łącznej kwoty rozszerzone** na fakturze za użycie nadmiarowe usługi. W poniższej tabeli wymieniono warunki i opisy umieszczone na fakturze i na **Podsumowanie użycia** w witrynie Enterprise portal:

   |Termin faktury|Termin Podsumowanie użycia|Opis|
   |---|---|---|
   |Łączna liczba rozszerzonych kwota|Łączne użycie|Łączne użycie przed opodatkowaniem opłata za okres przed zastosowaniem środków.|
   |Zobowiązań|Zobowiązań|Środki, zastosowane w tym określonym okresie.|
   |Łączna sprzedaż|Łączna nadwyżka|Opłaty całkowite użycie przekraczające kwotę środków. Ta kwota nie zawierają podatku.|
   |Kwota podatku|Nie dotyczy|Podatku, która dotyczy kwota łączna sprzedaż dla określonego okresu.|
   |Łączna kwota|Nie dotyczy|Kwota należności faktur, po zastosowaniu kredytu i podatek zostanie dodany.|
1. Aby uzyskać więcej informacji na temat opłat, przejdź do **Pobierz dane użycia** > **pobieranie raportu zaawansowanego**. Ten raport nie obejmuje podatków lub opłat za rezerwacji lub opłatach w portalu marketplace.

      ![Pobierz zrzut ekranu przedstawiający raport Zaawansowane na karcie użycie pobierania.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Przegląd fakturach witryny marketplace

Porównaj całkowitą na Azure Marketplace **raporty** > **Podsumowanie użycia** w witrynie Enterprise portal, za pomocą usługi fakturach witryny marketplace. Fakturach witryny marketplace jest tylko w przypadku zakupów w portalu Azure Marketplace i użycia. Kwoty **Podsumowanie użycia** nie zawierają podatku. 

1. Zaloguj się do [witryny Enterprise portal](https://ea.azure.com).
1. Wybierz **raporty**.
1. W prawym górnym rogu karty, Przełącz widok z **M** do **C** i sprostania szczytowemu okres na fakturze.

     ![Zrzut ekranu pokazujący M + C opcji na podsumowanie użycia.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Portalu Azure Marketplace** łącznie powinny odpowiadać **łączna sprzedaż** na fakturze portalu marketplace.
1. Aby uzyskać więcej informacji na temat opłat na podstawie użycia, przejdź do **Pobierz dane użycia**. W obszarze **opłatach w portalu Marketplace**, wybierz opcję **Pobierz**. Ten raport nie uwzględniają podatków, lub Pokaż jednorazowe zakupów.

     ![Zrzut ekranu pokazujący Pobierz opcję w obszarze opłatach w portalu Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
