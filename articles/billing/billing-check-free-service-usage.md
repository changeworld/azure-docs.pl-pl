---
title: Monitorowanie i śledzenie użycia bezpłatnej usługi platformy Azure
description: Dowiedz się, jak sprawdzić użycie bezpłatnej usługi w witrynie Azure Portal i w pliku CSV użycia.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709762"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Sprawdzanie użycia bezpłatnej usługi działającej w ramach bezpłatnego konta platformy Azure

Za korzystanie z bezpłatnych usług działających w ramach bezpłatnego konta platformy Azure nie są naliczane opłaty, chyba że przekroczono limity dotyczące tych usług. Aby nie przekraczać tych limitów, można monitorować i śledzić użycie bezpłatnych usług za pomocą witryny Azure Portal lub pliku użycia.

## <a name="check-usage-in-the-azure-portal"></a>Sprawdzanie użycia w witrynie Azure Portal

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2.  W obszarze nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi**.

3.  Wybierz pozycję **Subskrypcje**.

4.  Wybierz subskrypcję utworzoną podczas tworzenia bezpłatnego konta.

    ![Zrzut ekranu przedstawiający wszystkie subskrypcje](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Sekcja przeglądu zawiera podstawowe informacje o subskrypcji. Na przykład identyfikator subskrypcji, typ oferty i nazwa subskrypcji. Można również tu znaleźć informacje o czasie wygaśnięcia środków bezpłatnego konta.

    ![Zrzut ekranu przedstawiający podstawowe informacje o subskrypcji](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Przewiń w dół, aby znaleźć informacje o bieżącym i prognozowanym koszcie. Koszt obejmuje użycie usług, które nie są uwzględnione w bezpłatnym koncie, i użycie przekraczające limity bezpłatnych usług.

    ![Zrzut ekranu przedstawiający informacje o koszcie subskrypcji](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Ostatnia część sekcji przeglądu zawiera tabelę przedstawiającą użycie bezpłatnych usług.

    ![Zrzut ekranu przedstawiający użycie bezpłatnych usług](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Nazwa miernika:** Określa jednostkę miary wykorzystywanego miernika. Aby dowiedzieć się więcej o mapowaniu usługi na miernik, zobacz [Omówienie mapowania bezpłatnych usług do mierników](billing-understand-free-service-meter-mapping.md).
* **Użycie/limit:** Użycie i limit dla miernika w bieżącym miesiącu. Te informacje można również znaleźć na pasku stanu.
* **Stan:** Stan użycia miernika. Na podstawie wzorca użycia jest określany jeden z następujących stanów:
  * **Nieużywany:** Nie użyto miernika lub użycie miernika nie dotarło do systemu rozliczeń.
  * **Przekroczono \<data>:** Przekroczono limit miernika dnia \<data>.
  * **Małe prawdopodobieństwo przekroczenia:** Przekroczenie limitu dla miernika jest mało prawdopodobne.
  * **Przekracza \<data>:** Prawdopodobnie nastąpi przekroczenie limitu dla miernika dnia \<data>.

## <a name="check-usage-with-the-usage-file"></a>Sprawdzanie użycia za pomocą pliku użycia

Plik użycia zawiera szczegółowe informacje dotyczące subskrypcji platformy Azure. Miesięczny i dzienny plik użycia można pobrać z Centrum konta platformy Azure. Aby dowiedzieć się, jak pobrać plik użycia i poznać wymagane uprawnienia dostępu, zobacz [Pobieranie faktur i danych użycia](billing-download-azure-invoice-daily-usage-date.md). Aby dowiedzieć się więcej na temat kolumn w pliku użycia, zobacz [Omówienie terminów występujących w informacjach o użyciu](billing-understand-your-usage.md).

Plik użycia zawiera informacje o użyciu dla usług bezpłatnych i płatnych. Mierniki dotyczące bezpłatnych usług mają w nazwie postfiks **Free** (Bezpłatny). Aby znaleźć mierniki bezpłatnych usług, otwórz plik w programie Excel i przefiltruj **kolumnę Kategoria miernika** pod kątem komórek zawierających tekst **- Free** (użyj opcji Filtry tekstu &rarr; filtr Zawiera).


![Zrzut ekranu przedstawiający użycie bezpłatnych usług](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Podwyższenie poziomu subskrypcji](billing-upgrade-azure-subscription.md)
