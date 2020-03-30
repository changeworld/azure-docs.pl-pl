---
title: Szybkie korygowanie dla zaleceń doradcy
description: Wykonywanie zbiorczego korygowania przy użyciu szybkiej poprawki w programie Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502492"
---
# <a name="quick-fix-remediation-for-advisor"></a>Szybkie korygowanie dla doradcy
**Quick Fix** umożliwia szybszy i łatwiejszy sposób korygowania dla zaleceń dotyczących wielu zasobów. Zapewnia możliwość zbiorczego korygowania zasobów i pomaga zoptymalizować subskrypcje szybciej z korygowania na dużą skalę dla zasobów.
Ta funkcja jest dostępna tylko dla niektórych zaleceń za pośrednictwem witryny Azure portal.


## <a name="steps-to-use-quick-fix"></a>Kroki, aby użyć "Quick Fix"

1. Z listy zaleceń, które mają etykietę **Quick Fix,** kliknij zalecenie.

   ![Szybka poprawka doradcy](./media/quick-fix-1.png)
   
   *Ceny na obrazie służą np.*

2. Na stronie Szczegóły rekomendacji zobaczysz listę zasobów, dla których masz to zalecenie. Wybierz wszystkie zasoby, które chcesz skorygować dla zalecenia.

   ![Szybka poprawka doradcy](./media/quick-fix-2.png)
   
   *Ceny na obrazie służą np.*

3. Po wybraniu zasobów kliknij przycisk **Szybka poprawka,** aby zbiorczo skorygować.

   > [!NOTE]
   > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień do ich modyfikowania.
   
   > [!NOTE]
   > Jeśli istnieją inne implikacje, oprócz świadczeń wymienionych w Doradcy, zostaniesz poinformowany w doświadczeniu, aby pomóc Ci w podejmowaniu świadomych decyzji naprawczych.
   
4. Otrzymasz powiadomienie o zakończeniu korygowania. Zostanie wyświetlony błąd, jeśli istnieją zasoby, które nie są korygowane i zasoby w wybranym trybie w widoku listy zasobów.  


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń advisor, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST doradcy](https://docs.microsoft.com/rest/api/advisor/)
