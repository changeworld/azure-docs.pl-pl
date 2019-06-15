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
ms.author: banders
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617834"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdź użycie usług bezpłatnych, dołączone do bezpłatnego konta platformy Azure 

Opłaty nie są naliczane dla usług bezpłatnych bezpłatnego konta platformy Azure, o ile nie zostaną przekroczone tych usług. Aby utrzymać z ograniczeniami, możesz użyć witryny Azure portal lub plik użycia do monitorowania i śledzenie użycia w przypadku usług bezpłatnych. 

## <a name="check-usage-on-the-azure-portal"></a>Sprawdź użycie w witrynie Azure portal

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

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
* **Użycie/Limit:** Bieżący miesiąc użycia i limit dla licznika. Te informacje można również znaleźć na pasku stanu.
* **Stan:** Stan użycia licznika. Na podstawie Twojego użycia wzorca, użytkownik może mieć jedną z Statutu.
  * **Nieużywany:** Licznik nie były używane lub użycie licznika nie osiągnięto systemów rozliczeniowych.
  * **Przekroczono \<daty >:** Przekroczono limit dla licznika \<daty >.
  * **Małe prawdopodobieństwo przekroczenia:** Możesz teraz małe prawdopodobieństwo przekroczenia limitu dla licznika.
  * **Przekracza \<daty >:** Prawdopodobnie przekracza limit dla licznika na \<daty >.

## <a name="check-usage-through-the-usage-file"></a>Sprawdź użycie za pomocą pliku użycia

Użycie pliku zawiera szczegółowe informacje dotyczące subskrypcji platformy Azure. Swoje miesięczne i codziennie pliku użycia można pobrać z Centrum konta platformy Azure. Aby dowiedzieć się, jak pobrać plik użycia i zrozumienie dostęp wymagany, zobacz [pobieranie faktur i danych użycia](billing-download-azure-invoice-daily-usage-date.md). Aby dowiedzieć się więcej na temat kolumny w pliku użycia, zobacz [zrozumieć warunki na wykorzystanie](billing-understand-your-usage.md).

Plik użycia zawiera informacje o użyciu dla usług bezpłatnych i płatnych. Liczniki bezpłatnej usługi, musi **bezpłatna** dołączany na końcu nazwy miernika. Można znaleźć bezpłatne liczniki, otwórz plik programu excel i filtr **kolumny Kategoria licznika** komórek, które mają tekst **— bezpłatne** (Użyj filtrów tekstu &rarr; filtr zawierania) &nbsp;

![Zrzut ekranu pokazujący korzystanie z bezpłatnych usług](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).