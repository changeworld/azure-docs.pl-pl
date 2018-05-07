---
title: Monitorowanie i śledzenie użycia z bezpłatnych usług - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się sprawdzić użycia bezpłatnych usług. Użyj Azure portalu i użycie woluminów csv.
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
ms.author: amberb
ms.openlocfilehash: 560137d92491da46fda950dec84a0a7caa3ed720
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdź użycie bezpłatnych usług dołączonego bezpłatne konto platformy Azure 

To nie naliczane opłaty za usługi objęte bezpłatnie bezpłatne konto platformy Azure, o ile nie przekraczają limitów tych usług. Aby utrzymać z ograniczeniami, albo można portalu Azure lub pliku użycia monitorować i śledzić użycie bezpłatnych usług. 

## <a name="check-usage-on-the-azure-portal"></a>Sprawdź użycie w portalu Azure

1.  Zaloguj się do witryny [Azure Portal]( http://portal.azure.com).

2.  W obszarze nawigacji po lewej stronie, wybierz **wszystkie usługi**.

3.  Wybierz **subskrypcje**.

4.  Wybierz subskrypcję, utworzony po utworzeniu konta bezpłatne konto.

    ![Zrzut ekranu pokazujący wszystkie subskrypcje](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  W sekcji Przegląd zawiera ważne informacje dotyczące Twojej subskrypcji, takich jak identyfikator subskrypcji oferują typ i Nazwa subskrypcji. Można również znaleźć informacje o wygaśnięcia środków bezpłatne konto.

    ![Zrzut ekranu pokazujący podstawowych informacji o subskrypcji](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Przewiń w dół do wyszukiwania informacji na temat koszt bieżących i prognozowanych. Koszt obejmuje użycie usługi nie dołączono bezpłatne konto i użycie przekroczenie limitu bezpłatnych usług. 

    ![Zrzut ekranu pokazujący informacje o kosztach subskrypcji](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Ostatnia część sekcji Przegląd zawiera tabelę o użyciu bezpłatnych usług. 

    ![Zrzut ekranu pokazujący użycia bezpłatnych usług](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Nazwa licznika:** identyfikuje jednostki miary dla pomiaru są używane. Aby dowiedzieć się więcej o usłudze Mapowanie miernika, zobacz [zrozumieć bezpłatnej usługi do pomiaru mapowania](billing-understand-free-service-meter-mapping.md). 
* **Użycie/Limit:** użycia i limit licznika bieżącego miesiąca. Informacje te można również znaleźć na pasku stanu.
* **Stan:** użycia stan licznika. W oparciu o Twoje wzorca użycia, może zawierać jeden Statutu.
  * **Nieużywany:** mierniku nie były używane lub użycie licznika nie osiągnęła systemów rozliczeniowych.
  * **Przekroczono na \<Data >:** Przekroczono limit licznika \<Data >.
  * **Prawdopodobnie nie będzie Exceed:** jest mało prawdopodobne przekracza limit dla licznika.
  * **Przekracza na \<Data >:** najprawdopodobniej będzie przekracza limit licznik na \<Data >.


## <a name="check-usage-through-the-usage-file"></a>Sprawdź użycie za pomocą pliku użycia

Użycie pliku zawiera szczegółowe informacje dotyczące subskrypcji platformy Azure. Twoje co miesiąc i codziennie użycia pliku można pobrać z Centrum konta platformy Azure. Aby dowiedzieć się, jak pobrać plik użycia i zrozumieć dostęp wymagany, zobacz [uzyskać faktury i użycie](billing-download-azure-invoice-daily-usage-date.md). Aby uzyskać informacje dotyczące kolumn w pliku użycia, zobacz [zrozumieć warunki na użycie](billing-understand-your-usage.md). 

Plik użycia zawiera informacje o użyciu zarówno bezpłatnych i płatnych usług. Liczniki bezpłatnej usługi będzie zawierał **wolne** dołączany na końcu nazwy licznika. Można znaleźć wolnej liczników, otwórz plik w formacie programu excel i filtr **kolumny kategorii licznika** dla komórek zawierających tekst **— wolne** (Filtry tekstu użyj &rarr; filtr zawierania) &nbsp;

![Zrzut ekranu pokazujący użycia bezpłatnych usług](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
