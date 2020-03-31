---
title: Wprowadzenie do usługi Azure Advisor
description: Wprowadzenie do usługi Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259632"
---
# <a name="get-started-with-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się, jak uzyskać dostęp do klasyfikatora za pośrednictwem witryny Azure portal, uzyskać zalecenia i zaimplementować zalecenia.

> [!NOTE]
> Usługa Azure Advisor jest automatycznie uruchamiana w tle w celu znalezienia nowo utworzonych zasobów. Dostarczenie zaleceń dotyczących tych zasobów może potrwać do 24 godzin.

## <a name="get-recommendations"></a>Uzyskaj rekomendacje

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W lewym okienku kliknij pozycję **Doradca**.  Jeśli w lewym okienku nie widać funkcji Doradca, kliknij pozycję **Wszystkie usługi**.  W okienku menu usługi w obszarze **Monitorowanie i zarządzanie**kliknij pozycję **Doradca**. Zostanie wyświetlony pulpit nawigacyjny doradcy.

   ![Uzyskiwanie dostępu do usługi Azure Advisor przy użyciu witryny Azure portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Na pulpicie nawigacyjnym usługi Advisor zostanie wyświetlone podsumowanie zaleceń dla wszystkich wybranych subskrypcji.  Możesz wybrać subskrypcje, do których mają być wyświetlane zalecenia, przy użyciu listy rozwijanej filtru subskrypcji.

1. Aby uzyskać zalecenia dotyczące określonej kategorii, kliknij jedną z kart: **Wysoka dostępność,** **Bezpieczeństwo,** **Wydajność**lub **Koszt**. 

   ![Pulpit nawigacyjny usługi Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Uzyskaj szczegółowe informacje o rekomendacji i zaimplementuj rozwiązanie

Można wybrać zalecenie w programie Advisor, aby wyświetlić dodatkowe szczegóły — takie jak akcje rekomendacji i zasoby, których dotyczy problem — i wdrożyć rozwiązanie do zalecenia.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Wybierz kategorię rekomendacji, aby wyświetlić listę rekomendacji w tej kategorii, lub wybierz kartę **Wszystkie,** aby wyświetlić wszystkie rekomendacje.

1. Kliknij zalecenie, które chcesz szczegółowo przejrzeć.

1. Przejrzyj informacje o zaleceniu i zasobach, których dotyczy zalecenie.

1. Kliknij **polecane działanie,** aby wdrożyć zalecenie.

## <a name="filter-recommendations"></a>Zalecenia dotyczące filtrowania

Możesz filtrować zalecenia, aby przejść do szczegółów, co jest dla Ciebie najważniejsze.  Można filtrować według subskrypcji, typu zasobu lub stanu rekomendacji.  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Listy rozwijane na pulpicie nawigacyjnym klasyfikatora można filtrować według subskrypcji, typu zasobu lub stanu rekomendacji.

    ![Kryteria filtrowania wyszukiwania doradcy](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Odroczenie lub odrzucenie zaleceń

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Przejdź do zalecenia, które chcesz odroczyć lub odrzucić.

1. Kliknij zalecenie.

1. Kliknij pozycję **Odłóż**. 

1. Określ odroczenie okresu lub wybierz **opcję Nigdy,** aby odrzucić zalecenie.

## <a name="exclude-subscriptions-or-resource-groups"></a>Wykluczanie subskrypcji lub grup zasobów

Możesz mieć grupy zasobów lub subskrypcje, dla których nie chcesz otrzymywać zaleceń klasyfikatora — takich jak zasoby "testowe".  Klasyfikatora można skonfigurować tak, aby wygenerował tylko zalecenia dotyczące określonych subskrypcji i grup zasobów.

> [!NOTE]
> Aby uwzględnić lub wykluczyć subskrypcję lub grupę zasobów z klasyfikatora, musisz być właścicielem subskrypcji.  Jeśli nie masz wymaganych uprawnień dla subskrypcji lub grupy zasobów, opcja włączenia lub wykluczenia jest wyłączona w interfejsie użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij **pozycję Konfiguruj** na pasku akcji.

1. Odznacz wszystkie subskrypcje lub grupy zasobów, dla których nie chcesz otrzymywać zaleceń klasyfikatora.

    ![Przykład konfigurowania zasobów przez doradcę](./media/advisor-get-started/advisor-configure-resources.png)

1. Następnie kliknij przycisk **Apply** (Zastosuj).

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurowanie rekomendacji maszyny wirtualnej o niskim zużyciu

Ta procedura konfiguruje regułę średniego wykorzystania procesora CPU dla zalecenia maszyny wirtualnej niskiego użycia.

Doradca monitoruje użycie maszyny wirtualnej przez 7 dni, a następnie identyfikuje maszyny wirtualne o niskim wykorzystaniu. Maszyny wirtualne są uważane za niskie wykorzystanie, jeśli ich wykorzystanie procesora CPU wynosi 5% lub mniej, a ich wykorzystanie sieci jest mniejsze niż 2% lub jeśli bieżące obciążenie może być uwzględnione przez mniejszy rozmiar maszyny wirtualnej.

Jeśli chcesz być bardziej agresywny w identyfikowaniu maszyn wirtualnych o niskim użyciu, można dostosować regułę średniego wykorzystania procesora CPU na podstawie subskrypcji.  Regułę wykorzystania procesora CPU można ustawić na 5%, 10%, 15% lub 20%.

> [!NOTE]
> Aby dostosować regułę średniego wykorzystania procesora CPU do identyfikowania maszyn wirtualnych o niskim zużyciu, musisz być *właścicielem*subskrypcji .  Jeśli nie masz wymaganych uprawnień dla subskrypcji lub grupy zasobów, opcja włączenia lub wykluczenia zostanie wyłączona w interfejsie użytkownika. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij **pozycję Konfiguruj** na pasku akcji.

1. Kliknij kartę **Reguły.**

1. Wybierz subskrypcje, dla których chcesz dostosować średnią regułę wykorzystania procesora, a następnie kliknij przycisk **Edytuj**.

1. Wybierz żądaną średnią wartość wykorzystania procesora CPU i kliknij przycisk **Zastosuj**.

1. Kliknij przycisk **Odśwież zalecenia,** aby zaktualizować istniejące zalecenia, aby użyć nowej reguły średniego wykorzystania procesora CPU. 

   ![Przykład reguł konfigurowania przez doradcę](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Zalecenia dotyczące pobierania

Program Advisor umożliwia pobranie podsumowania rekomendacji.  Rekomendacje można pobrać jako plik PDF lub CSV.  Pobieranie rekomendacji umożliwia łatwe udostępnianie współpracownikom lub przeprowadzanie własnej analizy na podstawie danych rekomendacji.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij **pozycję Pobierz jako CSV** lub Pobierz jako plik **PDF** na pasku akcji.

Opcja pobierania jest zgodna z wszelkimi filtrami zastosowanymi do pulpitu nawigacyjnego klasyfikatora.  Jeśli wybierzesz opcję pobierania podczas wyświetlania określonej kategorii rekomendacji lub rekomendacji, pobrane podsumowanie zawiera tylko informacje dotyczące tej kategorii lub zalecenia. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o doradcy, zobacz:

- [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
- [Zalecenia dotyczące wysokiej dostępności advisor](advisor-high-availability-recommendations.md)
- [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
- [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
- [Rekomendacje dotyczące kosztów doradcy](advisor-performance-recommendations.md)
