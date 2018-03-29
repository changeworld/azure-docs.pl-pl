---
title: Wprowadzenie do usługi Advisor Azure | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Advisor Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: cd64515beabec43a5209d62dccf2b55b21702c16
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się, jak uzyskać dostępu do usługi Advisor za pośrednictwem portalu Azure, Uzyskaj zalecenia i wdrażanie zaleceń.

## <a name="get-advisor-recommendations"></a>Uzyskuj zalecenia usługi Advisor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie kliknij **Advisor**.  Jeśli nie ma usługi Advisor w okienku po lewej stronie, kliknij przycisk **wszystkie usługi**.  W okienku usługi menu w obszarze **monitorowanie i zarządzanie**, kliknij przycisk **Advisor**.
 Zostanie wyświetlony pulpit nawigacyjny usługi Advisor.

   ![Klasyfikator Azure dostępu przy użyciu portalu Azure](./media/advisor-get-started/advisor-portal-menu.png) 

4. Pulpit nawigacyjny usługi Advisor wyświetli podsumowanie zalecenia dla wszystkich wybranych subskrypcji.  Możesz wybrać subskrypcje, które mają zalecenia, który będzie wyświetlany dla korzystanie z subskrypcji filtrowanie listy rozwijanej.

5. Aby uzyskać zalecenia dotyczące określonej kategorii, kliknij jedną z kart: **wysokiej dostępności**, **zabezpieczeń**, **wydajności**, lub **koszt**. 

  ![Azure Advisor pulpitu nawigacyjnego](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Pobierz szczegóły zalecenia usługi Advisor i wdrożyć rozwiązanie

Możesz wybrać zalecenie klasyfikatora w celu wyświetlenia dodatkowych szczegółów — takie jak akcje zalecenia i wpływ na zasoby — i aby zaimplementować to rozwiązanie do zalecenia.  

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Wybierz kategorię zalecenie, aby wyświetlić listę zaleceń w ramach tej kategorii, lub wybierz **wszystkie** kartę, aby wyświetlić wszystkie zalecenia.

3. Kliknij, aby przejrzeć szczegółowe zalecenia.

4. Przejrzyj informacje na temat zalecenia i zasobów, których dotyczy zalecenie.

5. Polecenie **zalecana Akcja** wykonania zalecenia.

## <a name="filter-advisor-recommendations"></a>Zalecenia doradcy w zakresie filtru

Można filtrować zaleceń, które następnie przejść do najważniejszych dla Ciebie.  Można filtrować według subskrypcji, typ zasobu lub stan zalecenia.  

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Użyj list rozwijanych na pulpicie nawigacyjnym usługi Advisor umożliwia filtrowanie według subskrypcji, typ zasobu lub stan zalecenia.

    ![Kryteria filtru wyszukiwania usługi Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Odłóż lub odrzucić zalecenia doradcy w zakresie

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Przejdź do zalecenia, które chcesz odłożyć lub odrzucić.

3. Kliknij zalecenie.

4. Kliknij przycisk **odłożyć**. 

5. Określ czas Odłóż lub wybierz **nigdy** aby odrzucić zalecenia.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Wyklucz ze Advisor subskrypcji lub grupy zasobów

Masz grupy zasobów lub subskrypcji, dla których nie chcesz otrzymywać zalecenia doradcy w zakresie — takich jak zasoby "test".  Można skonfigurować usługi Advisor można generować tylko zalecenia dla określonej subskrypcji i grupy zasobów.

> [!NOTE]
> Aby dołączyć lub wykluczyć z Advisor subskrypcji lub grupy zasobów, musi być właściciela subskrypcji.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcja Dołącz lub wyklucz go jest wyłączone w interfejsie użytkownika.

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Konfiguruj** na pasku akcji.

3. Usuń zaznaczenie pola wyboru żadnych subskrypcji lub nie chcesz otrzymywać zalecenia doradcy w zakresie grup zasobów.

    ![Klasyfikator skonfigurować przykład zasobów](./media/advisor-get-started/advisor-configure-resources.png)

4. Kliknij przycisk **Zastosuj** przycisku.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Konfigurowanie średni reguły wykorzystania procesora CPU dla zalecenie niskie obciążenie maszyny wirtualnej

Klasyfikator monitoruje użycie maszyny wirtualnej w ciągu ostatnich 14 dni, a następnie identyfikuje niskiego wykorzystania maszyn wirtualnych. Maszyny wirtualne, których średnie wykorzystanie procesora CPU wynosi 5% lub mniej i użycie sieci jest 7 MB lub mniej przez cztery lub więcej dni są traktowane jako niskiego wykorzystania maszyn wirtualnych.

Jeśli chcesz być skuteczniejsze na określenie mniejszej aktywności maszyn wirtualnych, można dostosować średni reguły wykorzystanie Procesora na podstawie subskrypcji na.  Średnia reguły wykorzystanie procesora CPU można ustawić na 5%, 10%, 15% lub 20%.

> [!NOTE]
> Aby dostosować średni reguły wykorzystanie procesora CPU do identyfikacji maszyn wirtualnych mniejszej aktywności, musi być subskrypcją *właściciela*.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcja Dołącz lub wyklucz go zostanie wyłączone w interfejsie użytkownika. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Konfiguruj** na pasku akcji.

3. Kliknij przycisk **reguły** kartę.

4. Wybierz subskrypcje chcesz dostosować średni reguła wykorzystanie procesora CPU dla, a następnie kliknij przycisk **Edytuj**.

5. Wybierz żądaną wartość średnia wykorzystania Procesora, a następnie kliknij przycisk **Zastosuj**.

6. Kliknij przycisk **Odśwież zalecenia** do zaktualizowania istniejącej zalecenia do używania nowej reguły średni wykorzystanie procesora CPU. 

   ![Przykład reguły zalecenie skonfigurować Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Pobierz zalecenia usługi Advisor

Klasyfikator umożliwia pobieranie podsumowanie zalecenia.  Możesz pobrać zalecenia jako plik PDF lub pliku CSV.  Pobieranie zalecenia pozwala łatwo udostępniać współpracownikom lub przeprowadzanie analizy na podstawie danych zalecenia.

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Pobierz jako CSV** lub **Pobierz jako plik PDF** na pasku akcji.

Opcja pobierania uwzględnia wszystkie filtry, które zostały zastosowane do pulpitu nawigacyjnego usługi Advisor.  Jeśli zostanie wybrana opcja pobierania podczas wyświetlania zalecenie określonych kategorii lub zalecenia, pobranego Podsumowanie zawiera tylko informacje dotyczące tej kategorii lub zalecenia. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o usłudze Advisor, zobacz:
* [Wprowadzenie do usługi Advisor Azure](advisor-overview.md)
* [Zalecenia doradcy w zakresie wysokiej dostępności](advisor-high-availability-recommendations.md)
* [Zalecenia doradcy w zakresie zabezpieczeń](advisor-security-recommendations.md)
-  [Zalecenia doradcy w zakresie wydajności](advisor-performance-recommendations.md)
* [Zalecenia doradcy w zakresie koszt](advisor-performance-recommendations.md)
