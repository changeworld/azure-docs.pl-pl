---
title: Podsumowanie rekomendacji dla usługi Azure Advisor
description: Uzyskaj okresowe podsumowanie aktywnych rekomendacji
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502466"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Konfigurowanie okresowego podsumowania rekomendacji

**Skróty rekomendacji** advisor zapewniają łatwy i proaktywny sposób, aby być na bieżąco z aktywnymi zaleceniami w różnych kategoriach. Funkcja ta umożliwia konfigurowanie okresowych powiadomień dla podsumowania wszystkich aktywnych rekomendacji w różnych kategoriach. Możesz wybrać żądany kanał dla powiadomień, takich jak e-mail, SMS lub inne, za pomocą grup akcji. W tym artykule pokazano, jak skonfigurować **podsumowanie rekomendacji** dla zaleceń doradcy.


## <a name="setting-up-your-recommendation-digest"></a>Konfigurowanie skrótu rekomendacji 

Środowisko tworzenia **skrótu rekomendacji** pomaga skonfigurować podsumowanie. Poniżej można wybrać parametry konfiguracji:
1. Kategoria: Mamy kategorie rekomendacji, takie jak koszt, wysoka dostępność, wydajność i doskonałość operacyjna. Ta funkcja nie jest jeszcze dostępna dla zaleceń dotyczących zabezpieczeń.
2. Częstotliwość trawienia: Częstotliwość powiadomień podsumowujących może być tygodniowa, dwutygodniowa i miesięczna.
3. Grupa akcji: można wybrać istniejącą grupę akcji lub utworzyć nową grupę akcji. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Język do podsumowania
5. Nazwa skrótu rekomendacji: Można użyć przyjaznego dla użytkownika ciągu, aby lepiej śledzić i monitorować skróty.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Kroki tworzenia skrótu rekomendacji w witrynie Azure portal

Oto kroki, aby utworzyć **podsumowanie rekomendacji:**
* **Krok 1:** W witrynie Azure portal przejdź do **sekcji Poradnika** i w sekcji **Monitorowanie** wybierz pozycję **Podsumowanie rekomendacji** 

   ![Punkt wejścia skrótu rekomendacji](./media/digest-0.png)

* **Krok 2:** Wybierz **nowe podsumowanie rekomendacji** z górnego paska, jak poniżej:

   ![Utwórz podsumowanie rekomendacji](./media/digest-5.png)

* **Krok 3:** W sekcji **zakres** wybierz **subskrypcję** dla skrótu

   ![Podaj dane wejściowe skrótu rekomendacji](./media/digest-1.png)

* **Krok 4:** W sekcji **warunek** wybierz konfiguracje, takie jak **kategoria,** **częstotliwość** i **język**

   ![Zapewnienie warunków wprowadzania skrótu rekomendacji](./media/digest-2.png)

* **Krok 5:** W sekcji **grupa akcji** wybierz **grupę akcji** dla skrótu. Więcej informacji można znaleźć tutaj — [tworzenie grup akcji i zarządzanie nimi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Podaj grupę akcji wprowadzania skrótu rekomendacji](./media/digest-3.png)

* **Krok 6:** W tej ostatniej sekcji **dla szczegółów skrótu**można przypisać nazwę i stan do skrótu rekomendacji. Naciśnij **utwórz skrót rekomendacji,** aby zakończyć konfigurację.
   ![Kompletne tworzenie skrótów rekomendacji](./media/digest-4.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń advisor, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST doradcy](https://docs.microsoft.com/rest/api/advisor/)
