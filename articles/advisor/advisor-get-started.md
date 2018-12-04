---
title: Wprowadzenie do usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Azure Advisor.
services: advisor
documentationcenter: NA
author: kasparks
manager: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: kasparks
ms.openlocfilehash: 6e66fed21223701cd6c61bd1e903b4e7d7fbe0d0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850097"
---
# <a name="get-started-with-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się, jak dostęp do usługi Advisor w witrynie Azure portal, Uzyskaj zalecenia i zalecenia dotyczące implementowania.

## <a name="get-advisor-recommendations"></a>Uzyskuj zalecenia usługi Advisor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie kliknij **Advisor**.  Jeśli nie widzisz usługi Advisor w okienku po lewej stronie, kliknij przycisk **wszystkich usług**.  W okienku menu usług w obszarze **monitorowanie i zarządzanie**, kliknij przycisk **Advisor**.
 Zostanie wyświetlony pulpit nawigacyjny usługi Advisor.

   ![Dostęp do usługi Azure Advisor w witrynie Azure portal](./media/advisor-get-started/advisor-portal-menu.png) 

4. Pulpit nawigacyjny usługi Advisor wyświetli podsumowanie zaleceń dla wszystkich wybranych subskrypcji.  Możesz wybrać, czy lista rozwijana filtru subskrypcji ma zaleceń, które ma być wyświetlany dla przy użyciu subskrypcji.

5. Aby uzyskać zalecenia dotyczące określonej kategorii, kliknij jedną z kart: **wysokiej dostępności**, **zabezpieczeń**, **wydajności**, lub **koszt**. 

  ![Pulpit nawigacyjny usługi Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Pobierz szczegóły zalecenia usługi Advisor i zaimplementować rozwiązanie

W można wybierać zalecenia usługi Advisor w celu wyświetlenia dodatkowych szczegółów — na przykład zalecenie akcji i zasoby nimi objęte — i aby zaimplementować to rozwiązanie do zalecenia.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Wybierz kategorię zalecenie, aby wyświetlić listę zaleceń w ramach tej kategorii, lub wybierz **wszystkich** kartę, aby wyświetlić wszystkie zalecenia.

3. Kliknij przycisk rekomendacji, który chcesz przejrzeć szczegółowe.

4. Przejrzyj informacje na temat zalecenia i zasobów, które dotyczą zalecenie.

5. Kliknij pozycję **zalecana Akcja** wykonania zalecenia.

## <a name="filter-advisor-recommendations"></a>Filtruj zalecenia usługi Advisor

Można filtrować zalecenia, aby przejść do szczegółów najważniejszych dla Ciebie.  Można filtrować według subskrypcji, typ zasobu lub stan zalecenia.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Użyj menu rozwijanych na pulpit nawigacyjny usługi Advisor, aby filtrować według subskrypcji, typ zasobu lub stan zalecenia.

    ![Kryteria filtru wyszukiwania usługi Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Odłóż lub odrzucać zalecenia usługi Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Przejdź do zalecenia, które chcesz odłożyć lub odrzucić.

3. Kliknij przycisk rekomendacji.

4. Kliknij przycisk **odłożyć**. 

5. Określ czas Odłóż lub wybierz **nigdy** odrzucać zalecenia.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Wykluczanie grup zasobów lub subskrypcji usługi Advisor

Może mieć grup zasobów lub subskrypcji, której nie chcesz otrzymywać zalecenia usługi Advisor — takich jak zasoby "test".  Można skonfigurować usługę Advisor, aby generować jedynie zaleceń dotyczących określonej subskrypcji i grup zasobów.

> [!NOTE]
> Aby dołączyć lub wykluczyć subskrypcji lub grupy zasobów usługi Advisor, musi być właścicielem subskrypcji.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcję, aby uwzględnić lub wykluczyć go jest wyłączona w interfejsie użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Konfiguruj** na pasku akcji.

3. Usuń zaznaczenie pola wyboru żadnych subskrypcji lub grupy zasobów, które nie chcesz otrzymywać zalecenia usługi Advisor dla.

    ![Klasyfikator skonfigurować przytoczonym przykładzie](./media/advisor-get-started/advisor-configure-resources.png)

4. Kliknij przycisk **Zastosuj** przycisku.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Konfigurowanie średni reguły wykorzystanie procesora CPU zalecenia o niskim użyciu maszyny wirtualnej

Klasyfikator monitoruje wykorzystanie maszyn wirtualnych przez 14 dni, a następnie identyfikuje niskiego wykorzystania maszyn wirtualnych. Maszyny wirtualne, których średnie wykorzystanie procesora CPU wynosi 5 procent lub mniej i użycie sieci to 7 MB lub mniej przez cztery lub więcej dni są traktowane jako niskie użycie maszyn wirtualnych.

Jeśli chcesz mieć wyższe w identyfikacji maszyn wirtualnych o niskim użyciu, można dostosować średni reguły wykorzystanie procesora CPU na podstawie każdej subskrypcji.  Średnia reguły wykorzystanie procesora CPU można ustawić 5%, 10%, 15% lub 20%.

> [!NOTE]
> Aby dostosować średni reguły wykorzystanie procesora CPU do identyfikowania maszyny wirtualne o niskim użyciu, musi być subskrypcji *właściciela*.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcję, aby uwzględnić lub wykluczyć je zostanie wyłączona w interfejsie użytkownika. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Konfiguruj** na pasku akcji.

3. Kliknij przycisk **reguły** kartę.

4. Wybierz subskrypcję, czy chcesz dostosować średni reguła wykorzystanie procesora CPU dla, a następnie kliknij przycisk **Edytuj**.

5. Wybierz żądaną wartość średnie wykorzystanie procesora CPU, a następnie kliknij przycisk **Zastosuj**.

6. Kliknij przycisk **Odśwież zalecenia** można zaktualizować istniejące zalecenia, aby użyć nowej reguły średnie wykorzystanie procesora CPU. 

   ![Klasyfikator skonfigurować przykład reguły rekomendacji](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Pobierz zalecenia usługi Advisor

Usługi Advisor umożliwia Pobierz podsumowanie zaleceń.  Możesz pobrać zalecenia jako plik PDF lub pliku CSV.  Pobieranie zalecenia pozwala łatwo udostępniać współpracownikom lub przeprowadzać analizy danych zalecenia.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2. Kliknij przycisk **Pobierz jako plik CSV** lub **Pobierz jako plik PDF** na pasku akcji.

Opcja pobierania uwzględnia wszystkie filtry, które zostały zastosowane do pulpit nawigacyjny usługi Advisor.  Wybranie opcji pobierania, podczas wyświetlania kategorii konkretne zalecenie lub zalecenia pobrany Podsumowanie zawiera tylko informacje dotyczące tej kategorii lub zalecenia. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o usłudze Advisor, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
-  [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Rekomendacji dotyczących kosztu usługi Advisor](advisor-performance-recommendations.md)
