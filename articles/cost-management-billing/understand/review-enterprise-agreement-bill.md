---
title: Przejrzyj rachunek na korzystanie z platformy Azure Umowa Enterprise
description: Dowiedz się, jak czytać i rozumieć dane użycia oraz rachunek za umowę Enterprise platformy Azure.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: 2beb1d3e4468ff96f629dd6ec7015376a7f3a904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987264"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Omówienie rachunku za umowę Enterprise platformy Azure

Klienci mający umowę Enterprise platformy Azure otrzymują fakturę w przypadku przekroczenia dostępnych środków w organizacji lub korzystania z usług, które nie są pokrywane z tych środków.

Środki dostępne w organizacji obejmują jej zobowiązanie pieniężne. Zobowiązanie pieniężne to kwota zapłacona z góry przez organizację na poczet korzystania z usług platformy Azure. Można dodać środki ze zobowiązania pieniężnego do umowy Enterprise, kontaktując się z kierownikiem ds. klientów w firmie Microsoft lub odsprzedawcą.

Ten samouczek dotyczy tylko klientów platformy Azure z Umowa Enterprise platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj zafakturowane opłaty
> * Przejrzyj opłaty za użycie nadwyżkowe usługi
> * Przejrzyj fakturę w witrynie Marketplace

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejrzeć i zweryfikować opłaty uwzględnione na fakturze, musisz być administratorem przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](../manage/understand-ea-roles.md). Jeśli nie wiesz, kto jest administratorem przedsiębiorstwa w Twojej organizacji, [skontaktuj się z zespołem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Przejrzyj zafakturowane opłaty dla większości klientów

Ta sekcja nie dotyczy klientów platformy Azure w Australii, Japonii i Singapurze.

Otrzymasz fakturę za korzystanie z platformy Azure, jeśli w okresie rozliczeniowym nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżka usługi**: opłaty za użycie w organizacji przekraczają saldo kredytowe.
- **Opłaty**są naliczane osobno: usługi używane przez organizację nie są objęte środkiem. Niezależnie od salda środków otrzymasz faktury za następujące usługi:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (miesięczny)
    - Visual Studio Enterprise (roczny)
    - Visual Studio Professional (miesięczny)
    - Visual Studio Professional (roczny)
- **Opłaty w witrynie Marketplace**: zakupy i użycie w portalu Azure Marketplace nie są objęte środkiem kredytowym organizacji. Dlatego niezależnie od salda środków otrzymasz faktury za opłaty z platformy Marketplace. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Na fakturze wykazane jest całe użycie platformy Azure, a następnie wszelkie opłaty z platformy Marketplace. Jeśli masz dostępne saldo środków, zostanie ono wykorzystane do pokrycia opłat za użycie platformy Azure.

Możesz porównać łączną kwotę widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za platformę Azure. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do [portalu Azure EA](https://ea.azure.com). Następnie wybierz pozycję **raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu pokazujący opcję M + C w podsumowaniu użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Suma kwot **Łączne użycie** i **Azure Marketplace** powinna odpowiadać **łącznej wartości** na fakturze. Aby uzyskać więcej informacji na temat opłat, wybierz pozycję **Pobierz zestawienie użycia**.  

![Zrzut ekranu przedstawiający kartę pobieranie użycia](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Przejrzyj zafakturowane opłaty dla innych klientów

Ta sekcja dotyczy tylko klientów platformy Azure w Australii, Japonii lub Singapurze.

Otrzymasz co najmniej jedną fakturę za korzystanie z platformy Azure, jeśli nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżka usługi**: opłaty za użycie w organizacji przekraczają saldo kredytowe.
- **Opłaty**są naliczane osobno: usługi używane przez organizację nie są objęte środkiem. Otrzymasz fakturę za następujące usługi:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (miesięczny)
    - Visual Studio Enterprise (roczny)
    - Visual Studio Professional (miesięczny)
    - Visual Studio Professional (roczny)
- **Opłaty w witrynie Marketplace**: zakupy i użycie w portalu Azure Marketplace nie są objęte środkiem kredytowym organizacji i są rozliczane osobno. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Jeśli w okresie rozliczeniowym zostaną naliczone opłaty za nadwyżkowe użycie usług oraz opłaty rozliczane oddzielnie, otrzymasz jedną fakturę. Będzie ona zawierać oba typy opłat. Opłaty z platformy Marketplace są zawsze fakturowane osobno.

## <a name="review-service-overage-charges-for-other-customers"></a>Przejrzyj opłaty za użycie nadwyżkowe usługi dla innych klientów

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Możesz porównać łączną kwotę za użycie widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za użycie nadwyżkowe. Faktura za użycie nadwyżkowe uwzględnia opłaty za użycie przekraczające dostępne środki organizacji oraz za użycie usług, które nie są pokrywane z dostępnych środków. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do [portalu Azure EA](https://ea.azure.com) , a następnie wybierz pozycję **raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu pokazujący opcję M + C w podsumowaniu użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Kwota **Łączne użycie** powinna odpowiadać **łącznej wartości** na fakturze za nadwyżkowe użycie usług. Aby uzyskać więcej informacji o opłatach, przejdź do pozycji **Pobierz zestawienie użycia** > **Pobierz raport zaawansowany**. Ten raport nie obejmuje podatków, opłat za rezerwacje ani opłat z platformy Marketplace.  

![Zrzut ekranu przedstawiający zaawansowane pobieranie raportów na karcie użycie pobierania.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

W poniższej tabeli przedstawiono i wyjaśniono terminy używane na fakturze oraz w raporcie **Podsumowanie użycia** w witrynie Enterprise Portal:

|Termin na fakturze|Termin w raporcie Podsumowanie użycia|Opis|
|---|---|---|
|Łączna wartość|Łączne użycie|Łączna opłata za użycie w danym okresie przed zastosowaniem środków, bez uwzględnienia podatku.|
|Użycie zobowiązania|Użycie zobowiązania|Środki zastosowane w danym okresie.|
|Łączna sprzedaż|Łączna nadwyżka|Łączna opłata za użycie przekraczające kwotę dostępnych środków. Ta kwota nie obejmuje podatku.|
|Kwota podatku|Nie dotyczy|Podatek naliczony od łącznej kwoty sprzedaży w danym okresie.|
|Łączna kwota|Nie dotyczy|Kwota do zapłaty z tytułu faktury, po zastosowaniu środków i uwzględnieniu podatku.|

### <a name="review-marketplace-invoice"></a>Przejrzyj fakturę w witrynie Marketplace

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Porównaj sumę opłat z platformy Azure Marketplace w sekcji **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z fakturą dotyczącą platformy Marketplace. Faktura witryny Marketplace dotyczy tylko zakupów w witrynie Azure Marketplace i jej użycia. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Enterprise Portal](https://ea.azure.com) , a następnie wybierz pozycję **raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu pokazujący opcję M + C w podsumowaniu użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Łączna kwota w polu **Azure Marketplace** powinna odpowiadać **łącznej sprzedaży** na fakturze dotyczącej platformy Marketplace. Aby uzyskać więcej informacji o opłatach za użycie, przejdź do pozycji **Pobierz zestawienie użycia**. W obszarze **Opłaty za korzystanie z witryny Marketplace** wybierz pozycję **Pobierz**. Ten raport nie uwzględnia podatków ani jednorazowych zakupów.  

![Zrzut ekranu przedstawiający opcję pobierania w obszarze opłaty dla witryny Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj zafakturowane opłaty
> * Przejrzyj opłaty za użycie nadwyżkowe usługi
> * Przejrzyj fakturę w witrynie Marketplace

Przejdź do następnego artykułu, aby dowiedzieć się więcej przy użyciu portalu Azure EA.

> [!div class="nextstepaction"]
> [Wprowadzenie do witryny Azure EA Portal](../manage/ea-portal-get-started.md)
