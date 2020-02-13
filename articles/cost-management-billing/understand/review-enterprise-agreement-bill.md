---
title: Przegląd rachunku za umowę Enterprise platformy Azure
description: Dowiedz się, jak czytać i rozumieć dane użycia oraz rachunek za umowę Enterprise platformy Azure.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2020
ms.author: banders
ms.openlocfilehash: 8e674ec1c2d4c47d3b4e56bd788c963c81ea23e9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016208"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Omówienie rachunku za umowę Enterprise platformy Azure

Klienci mający umowę Enterprise platformy Azure otrzymują fakturę w przypadku przekroczenia dostępnych środków w organizacji lub korzystania z usług, które nie są pokrywane z tych środków.

Środki dostępne w organizacji obejmują jej zobowiązanie pieniężne. Zobowiązanie pieniężne to kwota zapłacona z góry przez organizację na poczet korzystania z usług platformy Azure. Można dodać środki ze zobowiązania pieniężnego do umowy Enterprise, kontaktując się z kierownikiem ds. klientów w firmie Microsoft lub odsprzedawcą.

Ten samouczek dotyczy tylko klientów platformy Azure z umową Enterprise platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych opłat
> * Przeglądanie opłat za nadwyżkowe użycie usług
> * Przeglądanie faktur z witryny Marketplace

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejrzeć i zweryfikować opłaty uwzględnione na fakturze, musisz być administratorem przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](../manage/understand-ea-roles.md). Jeśli nie wiesz, kto jest administratorem przedsiębiorstwa w Twojej organizacji, [skontaktuj się z zespołem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Przeglądanie zafakturowanych opłat dla większości klientów

Ta sekcja nie dotyczy klientów platformy Azure w Australii, Japonii i Singapurze.

Otrzymasz fakturę za korzystanie z platformy Azure, jeśli w okresie rozliczeniowym nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżkowe użycie usług**: Opłaty za użycie w organizacji przekraczają saldo dostępnych środków.
- **Opłaty rozliczane oddzielnie**: Niektóre usługi używane przez organizację nie są pokrywane z dostępnych środków. Niezależnie od salda środków otrzymasz faktury za następujące usługi:
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
- **Opłaty z platformy Marketplace**: Zakup i korzystanie z usług z platformy Azure Marketplace nie są pokrywane ze środków organizacji. Dlatego niezależnie od salda środków otrzymasz faktury za opłaty z platformy Marketplace. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Na fakturze wykazane jest całe użycie platformy Azure, a następnie wszelkie opłaty z platformy Marketplace. Jeśli masz dostępne saldo środków, zostanie ono wykorzystane do pokrycia opłat za użycie platformy Azure.

Możesz porównać łączną kwotę widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za platformę Azure. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com). Następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Suma kwot **Łączne użycie** i **Azure Marketplace** powinna odpowiadać **łącznej wartości** na fakturze. Aby uzyskać więcej informacji na temat opłat, wybierz pozycję **Pobierz zestawienie użycia**.  

![Zrzut ekranu przedstawiający kartę Pobierz zestawienie użycia](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Przeglądanie zafakturowanych opłat dla pozostałych klientów

Ta sekcja dotyczy tylko klientów platformy Azure w Australii, Japonii lub Singapurze.

Otrzymasz co najmniej jedną fakturę za korzystanie z platformy Azure, jeśli nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżkowe użycie usług**: Opłaty za użycie w organizacji przekraczają saldo dostępnych środków.
- **Opłaty rozliczane oddzielnie**: Niektóre usługi używane przez organizację nie są pokrywane z dostępnych środków. Otrzymasz fakturę za następujące usługi:
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
- **Opłaty z platformy Marketplace**: Zakup i korzystanie z usług z platformy Azure Marketplace nie są pokrywane ze środków organizacji i są fakturowane osobno. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Jeśli w okresie rozliczeniowym zostaną naliczone opłaty za nadwyżkowe użycie usług oraz opłaty rozliczane oddzielnie, otrzymasz jedną fakturę. Będzie ona zawierać oba typy opłat. Opłaty z platformy Marketplace są zawsze fakturowane osobno.

## <a name="review-service-overage-charges-for-other-customers"></a>Przeglądanie opłat za użycie nadwyżkowe usług dla pozostałych klientów

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Możesz porównać łączną kwotę za użycie widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za użycie nadwyżkowe. Faktura za użycie nadwyżkowe uwzględnia opłaty za użycie przekraczające dostępne środki organizacji oraz za użycie usług, które nie są pokrywane z dostępnych środków. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com), a następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Kwota **Łączne użycie** powinna odpowiadać **łącznej wartości** na fakturze za nadwyżkowe użycie usług. Aby uzyskać więcej informacji o opłatach, przejdź do pozycji **Pobierz zestawienie użycia** > **Pobierz raport zaawansowany**. Ten raport nie obejmuje podatków, opłat za rezerwacje ani opłat z platformy Marketplace.  

![Zrzut ekranu przedstawiający pozycję Pobierz raport zaawansowany na karcie Pobierz zestawienie użycia.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

W poniższej tabeli przedstawiono i wyjaśniono terminy używane na fakturze oraz w raporcie **Podsumowanie użycia** w witrynie Enterprise Portal:

|Termin na fakturze|Termin w raporcie Podsumowanie użycia|Opis|
|---|---|---|
|Łączna wartość|Łączne użycie|Łączna opłata za użycie w danym okresie przed zastosowaniem środków, bez uwzględnienia podatku.|
|Użycie zobowiązania|Użycie zobowiązania|Środki zastosowane w danym okresie.|
|Łączna sprzedaż|Łączna nadwyżka|Łączna opłata za użycie przekraczające kwotę dostępnych środków. Ta kwota nie obejmuje podatku.|
|Kwota podatku|Nie dotyczy|Podatek naliczony od łącznej kwoty sprzedaży w danym okresie.|
|Łączna kwota|Nie dotyczy|Kwota do zapłaty z tytułu faktury, po zastosowaniu środków i uwzględnieniu podatku.|

### <a name="review-marketplace-invoice"></a>Przeglądanie faktur z witryny Marketplace

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Porównaj sumę opłat z platformy Azure Marketplace w sekcji **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z fakturą dotyczącą platformy Marketplace. Faktura witryny Marketplace dotyczy tylko zakupów w witrynie Azure Marketplace i jej użycia. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Enterprise Portal](https://ea.azure.com), a następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Łączna kwota w polu **Azure Marketplace** powinna odpowiadać **łącznej sprzedaży** na fakturze dotyczącej platformy Marketplace. Aby uzyskać więcej informacji o opłatach za użycie, przejdź do pozycji **Pobierz zestawienie użycia**. W obszarze **Opłaty za korzystanie z witryny Marketplace** wybierz pozycję **Pobierz**. Cena w witrynie Marketplace obejmuje podatek określony przez wydawcę. Klienci nie otrzymają oddzielnej faktury od wydawcy w celu pobrania podatku od transakcji.

![Zrzut ekranu przedstawiający opcję pobierania w obszarze opłat za korzystanie z witryny Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych opłat
> * Przeglądanie opłat za nadwyżkowe użycie usług
> * Przeglądanie faktur z witryny Marketplace

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat korzystania z witryny Azure EA Portal.

> [!div class="nextstepaction"]
> [Wprowadzenie do witryny Azure EA Portal](../manage/ea-portal-get-started.md)
