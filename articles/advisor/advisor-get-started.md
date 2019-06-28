---
title: Wprowadzenie do usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Azure Advisor.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: f91e48a532a278c95d50775e135ac6379e8d8070
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332056"
---
# <a name="get-started-with-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się, jak dostęp do usługi Advisor w witrynie Azure portal, Uzyskaj zalecenia i zalecenia dotyczące implementowania.

> [!NOTE]
> Usługa Azure Advisor automatycznie uruchamia w tle do nowo utworzone zasoby. Może upłynąć do 24 godzin, aby przedstawić zalecenia w zakresie w odniesieniu do tych zasobów.

## <a name="get-recommendations"></a>Uzyskaj zalecenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie kliknij **Advisor**.  Jeśli nie widzisz usługi Advisor w okienku po lewej stronie, kliknij przycisk **wszystkich usług**.  W okienku menu usług w obszarze **monitorowanie i zarządzanie**, kliknij przycisk **Advisor**. Zostanie wyświetlony pulpit nawigacyjny usługi Advisor.

   ![Dostęp do usługi Azure Advisor w witrynie Azure portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Pulpit nawigacyjny usługi Advisor wyświetli podsumowanie zaleceń dla wszystkich wybranych subskrypcji.  Możesz wybrać, czy lista rozwijana filtru subskrypcji ma zaleceń, które ma być wyświetlany dla przy użyciu subskrypcji.

1. Aby uzyskać zalecenia dotyczące określonej kategorii, kliknij jedną z kart: **Wysoka dostępność**, **zabezpieczeń**, **wydajności**, lub **koszt**. 

   ![Pulpit nawigacyjny usługi Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Pobierz szczegóły zalecenia i zaimplementować rozwiązanie

W można wybierać zalecenia usługi Advisor w celu wyświetlenia dodatkowych szczegółów — na przykład zalecenie akcji i zasoby nimi objęte — i aby zaimplementować to rozwiązanie do zalecenia.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Wybierz kategorię zalecenie, aby wyświetlić listę zaleceń w ramach tej kategorii, lub wybierz **wszystkich** kartę, aby wyświetlić wszystkie zalecenia.

1. Kliknij przycisk rekomendacji, który chcesz przejrzeć szczegółowe.

1. Przejrzyj informacje na temat zalecenia i zasobów, które dotyczą zalecenie.

1. Kliknij pozycję **zalecana Akcja** wykonania zalecenia.

## <a name="filter-recommendations"></a>Zalecenia dotyczące filtru

Można filtrować zalecenia, aby przejść do szczegółów najważniejszych dla Ciebie.  Można filtrować według subskrypcji, typ zasobu lub stan zalecenia.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Użyj menu rozwijanych na pulpit nawigacyjny usługi Advisor, aby filtrować według subskrypcji, typ zasobu lub stan zalecenia.

    ![Kryteria filtru wyszukiwania usługi Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Odłóż lub odrzucania zaleceń

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Przejdź do zalecenia, które chcesz odłożyć lub odrzucić.

1. Kliknij przycisk rekomendacji.

1. Kliknij przycisk **odłożyć**. 

1. Określ czas Odłóż lub wybierz **nigdy** odrzucać zalecenia.

## <a name="exclude-subscriptions-or-resource-groups"></a>Wykluczanie grup zasobów lub subskrypcji

Może mieć grup zasobów lub subskrypcji, której nie chcesz otrzymywać zalecenia usługi Advisor — takich jak zasoby "test".  Można skonfigurować usługę Advisor, aby generować jedynie zaleceń dotyczących określonej subskrypcji i grup zasobów.

> [!NOTE]
> Aby dołączyć lub wykluczyć subskrypcji lub grupy zasobów usługi Advisor, musi być właścicielem subskrypcji.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcję, aby uwzględnić lub wykluczyć go jest wyłączona w interfejsie użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij przycisk **Konfiguruj** na pasku akcji.

1. Usuń zaznaczenie pola wyboru żadnych subskrypcji lub grupy zasobów, które nie chcesz otrzymywać zalecenia usługi Advisor dla.

    ![Klasyfikator skonfigurować przytoczonym przykładzie](./media/advisor-get-started/advisor-configure-resources.png)

1. Następnie kliknij przycisk **Apply** (Zastosuj).

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurowanie o niskim użyciu zalecenie dotyczące maszyny Wirtualnej

Ta procedura umożliwia skonfigurowanie średni reguły wykorzystanie procesora CPU dla zaleceń dotyczących maszyny wirtualnej o niskim użyciu.

Klasyfikator monitoruje wykorzystanie maszyny wirtualnej przez 7 dni, a następnie identyfikuje niskiego wykorzystania maszyn wirtualnych. Wirtualne wykorzystanie niski, jeśli użycie Procesora wynosi 5% lub mniej, są traktowane jako maszyn i ich wykorzystania sieci jest mniejsza niż % 2, lub jeśli bieżące obciążenie może zostać uwzględniony w obrębie mniejszego rozmiaru maszyny wirtualnej.

Jeśli chcesz mieć wyższe w identyfikacji maszyn wirtualnych o niskim użyciu, można dostosować średni reguły wykorzystanie procesora CPU na podstawie każdej subskrypcji.  Można ustawić reguły wykorzystanie procesora CPU do 5%, 10%, 15% lub 20%.

> [!NOTE]
> Aby dostosować średni reguły wykorzystanie procesora CPU do identyfikowania maszyny wirtualne o niskim użyciu, musi być subskrypcji *właściciela*.  Jeśli nie masz wymaganych uprawnień do subskrypcji lub grupy zasobów, opcję, aby uwzględnić lub wykluczyć je zostanie wyłączona w interfejsie użytkownika. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij przycisk **Konfiguruj** na pasku akcji.

1. Kliknij przycisk **reguły** kartę.

1. Wybierz subskrypcję, czy chcesz dostosować średni reguła wykorzystanie procesora CPU dla, a następnie kliknij przycisk **Edytuj**.

1. Wybierz żądaną wartość średnie wykorzystanie procesora CPU, a następnie kliknij przycisk **Zastosuj**.

1. Kliknij przycisk **Odśwież zalecenia** można zaktualizować istniejące zalecenia, aby użyć nowej reguły średnie wykorzystanie procesora CPU. 

   ![Klasyfikator skonfigurować przykład reguły rekomendacji](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Pobierz zalecenia

Usługi Advisor umożliwia Pobierz podsumowanie zaleceń.  Możesz pobrać zalecenia jako plik PDF lub pliku CSV.  Pobieranie zalecenia pozwala łatwo udostępniać współpracownikom lub przeprowadzać analizy danych zalecenia.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij przycisk **Pobierz jako plik CSV** lub **Pobierz jako plik PDF** na pasku akcji.

Opcja pobierania uwzględnia wszystkie filtry, które zostały zastosowane do pulpit nawigacyjny usługi Advisor.  Wybranie opcji pobierania, podczas wyświetlania kategorii konkretne zalecenie lub zalecenia pobrany Podsumowanie zawiera tylko informacje dotyczące tej kategorii lub zalecenia. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o usłudze Advisor, zobacz:

- [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
- [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
- [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
- [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
- [Rekomendacji dotyczących kosztu usługi Advisor](advisor-performance-recommendations.md)
