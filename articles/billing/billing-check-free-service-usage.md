---
title: Monitorowanie i śledzenie użycia usług platformy Azure bezpłatne | Dokumentacja firmy Microsoft
description: Dowiedz się sprawdzić użycie w przypadku usług bezpłatnych. Za pomocą usługi Azure portal i użycia woluminów csv.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: cwatson
ms.openlocfilehash: cb3584101dea4dc8d8d888632175415480a6a1b3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581532"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdź użycie usług bezpłatnych, dołączone do bezpłatnego konta platformy Azure 

Nie są naliczane dla usług bezpłatnych bezpłatnego konta platformy Azure, o ile nie zostaną przekroczone tych usług. Aby utrzymać z ograniczeniami, możesz użyć witryny Azure portal lub plik użycia do monitorowania i śledzenie użycia w przypadku usług bezpłatnych. 

## <a name="check-usage-on-the-azure-portal"></a>Sprawdź użycie w witrynie Azure portal

1.  Zaloguj się do witryny [Azure Portal]( http://portal.azure.com).

2.  W obszarze nawigacji po lewej stronie wybierz **wszystkich usług**.

3.  Wybierz pozycję **Subskrypcje**.

4.  Wybierz subskrypcję, który został utworzony podczas tworzenia konta bezpłatne konto.

    ![Zrzut ekranu przedstawiający wszystkie subskrypcje](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  W sekcji Przegląd zawiera podstawowe informacje dotyczące subskrypcji, takie jak identyfikator subskrypcji, oferują typu i nazwy subskrypcji. Można również znaleźć informacji na temat wygaśnięcia swoje środki na bezpłatne konto.

    ![Zrzut ekranu pokazujący podstawowe informacje o subskrypcji](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Przewiń w dół do wyszukiwania informacji na temat Twoje bieżące i prognozowane koszty. Koszt obejmuje korzystanie z usług, które nie są uwzględnione w ramach bezpłatnego konta i użycie przekraczające granice bezpłatnych usług. 

    ![Zrzut ekranu przedstawiający informacje o kosztach subskrypcji](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Ostatnia część sekcji Przegląd ma jedną tabelę na korzystanie z bezpłatnych usług. 

    ![Zrzut ekranu pokazujący korzystanie z bezpłatnych usług](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Nazwa licznika:** Określa jednostkę miary dla mierników, są używane. Aby dowiedzieć się więcej o usłudze do mapowania liczników, zobacz [omówienie mapowania miernika bezpłatnej usługi](billing-understand-free-service-meter-mapping.md). 
* **Użycie/Limit:** użycia i limit dla licznika bieżącego miesiąca. Te informacje można również znaleźć na pasku stanu.
* **Stan:** stan zużycia licznika. Na podstawie Twojego użycia wzorca, użytkownik może mieć jedną z Statutu.
  * **Nieużywany:** licznik nie były używane lub użycie licznika nie dotarła do systemów rozliczeniowych.
  * **Przekroczono \<daty >:** Przekroczono limit dla licznika \<daty >.
  * **Prawdopodobnie nie będzie Exceed:** użytkownik prawdopodobnie nie przekracza maksymalną wartość licznika.
  * **Przekracza \<daty >:** prawdopodobnie przekracza limit dla licznika na \<daty >.


## <a name="check-usage-through-the-usage-file"></a>Sprawdź użycie za pomocą pliku użycia

Użycie pliku zawiera szczegółowe informacje dotyczące subskrypcji platformy Azure. Swoje miesięczne i codziennie pliku użycia można pobrać z Centrum konta platformy Azure. Aby dowiedzieć się, jak pobrać plik użycia i zrozumienie dostęp wymagany, zobacz [pobieranie faktur i danych użycia](billing-download-azure-invoice-daily-usage-date.md). Aby dowiedzieć się więcej na temat kolumny w pliku użycia, zobacz [zrozumieć warunki na wykorzystanie](billing-understand-your-usage.md). 

Użycie pliku zawiera informacje dotyczące użycia dla usług bezpłatnych i płatnych. Liczniki bezpłatnej usługi, musi **bezpłatna** dołączany na końcu nazwy miernika. Można znaleźć bezpłatne liczniki, otwórz plik programu excel i filtr **kolumny Kategoria licznika** komórek, które zawierają tekst **— bezpłatne** (Użyj filtrów tekstu &rarr; filtr zawierania) &nbsp;

![Zrzut ekranu pokazujący korzystanie z bezpłatnych usług](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).