---
title: Należy monitorować i śledzić użycie platformy Azure, bezpłatna usługa
description: Dowiedz się, jak sprawdzić użycie bezpłatnej usługi Azure portal i użycie pliku CSV.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491423"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Sprawdź użycia bezpłatnej usługi, które obejmują przy użyciu bezpłatnego konta platformy Azure

Opłaty nie są naliczane dla usług bezpłatnych bezpłatnego konta platformy Azure, o ile nie zostaną przekroczone usług. Ma pozostawać w granicach, aby monitorować i śledzić użycie bezpłatnej usługi można użyć witryny Azure portal lub pliku użycia.

## <a name="check-usage-in-the-azure-portal"></a>Sprawdź użycie w witrynie Azure portal

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2.  W obszarze nawigacji po lewej stronie wybierz **wszystkich usług**.

3.  Wybierz pozycję **Subskrypcje**.

4.  Wybierz subskrypcję, który został utworzony podczas tworzenia konta bezpłatne konto.

    ![Zrzut ekranu przedstawiający wszystkie subskrypcje](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  W sekcji Przegląd zawiera podstawowe informacje dotyczące subskrypcji. Na przykład identyfikator subskrypcji, typ oferty i nazwy subskrypcji. Można również znaleźć informacje, po wygaśnięciu środków bezpłatnego konta.

    ![Zrzut ekranu pokazujący podstawowe informacje o subskrypcji](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Przewiń w dół, aby znaleźć informacje o Twoje bieżące i prognozowane koszty. Koszt obejmuje użycie usługi, które nie są dołączone swoje bezpłatne konto i użycie przekraczające granice bezpłatnych usług.

    ![Zrzut ekranu przedstawiający informacje o kosztach subskrypcji](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Ostatnia część sekcji Przegląd zawiera tabelę przedstawiającą użycia bezpłatnej usługi.

    ![Zrzut ekranu pokazujący korzystanie z bezpłatnych usług](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Nazwa licznika:** Określa jednostkę miary dla mierników, są używane. Aby dowiedzieć się więcej o usłudze do mapowania liczników, zobacz [omówienie mapowania miernika bezpłatnej usługi](billing-understand-free-service-meter-mapping.md).
* **Użycie/Limit:** Bieżący miesiąc użycia i limit dla licznika. Te informacje można również znaleźć na pasku stanu.
* **Stan:** Stan użycia licznika. Na podstawie Twojego użycia wzorca, użytkownik może mieć jeden jakimikolwiek następujące:
  * **Nieużywany:** Licznik nie były używane lub użycie licznika nie osiągnięto systemów rozliczeniowych.
  * **Przekroczono \<daty >:** Przekroczono limit dla licznika \<daty >.
  * **Małe prawdopodobieństwo przekroczenia:** Możesz teraz małe prawdopodobieństwo przekroczenia limitu dla licznika.
  * **Przekracza \<daty >:** Prawdopodobnie przekracza limit dla licznika na \<daty >.

## <a name="check-usage-with-the-usage-file"></a>Sprawdź użycie za pomocą pliku użycia

Użycie pliku zawiera szczegółowe informacje o Twojej subskrypcji platformy Azure. Możesz pobrać plik miesięczne i dzienne użycie z Centrum konta platformy Azure. Aby dowiedzieć się, jak pobrać plik użycia i zrozumienie dostęp wymagany, zobacz [pobieranie faktur i danych użycia](billing-download-azure-invoice-daily-usage-date.md). Aby dowiedzieć się więcej na temat kolumny w pliku użycia, zobacz [zrozumieć warunki na wykorzystanie](billing-understand-your-usage.md).

Plik użycia zawiera informacje o użyciu dla usług bezpłatnych i płatnych. Liczniki bezpłatnej usługi, musi **bezpłatna** dołączany na końcu nazwy miernika. Można znaleźć bezpłatne liczniki, otwórz plik programu excel i filtr **kolumny Kategoria licznika** komórek, które mają tekst **— bezpłatna** (Użyj filtrów tekstu &rarr; zawiera filtr).


![Zrzut ekranu pokazujący korzystanie z bezpłatnych usług](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki
- [Uaktualnij swoją subskrypcję](billing-upgrade-azure-subscription.md)
