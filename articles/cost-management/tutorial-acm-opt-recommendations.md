---
title: Samouczek — ograniczyć koszty platformy Azure z zaleceniami dotyczącymi optymalizacji | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia obniżenie kosztów platformy Azure, gdy działa zgodnie z zaleceniami optymalizacji.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229848"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Samouczek: Optymalizowanie kosztów od zalecanych

Usługa Azure Cost Management współdziała z usługą Azure Advisor podczas ustalania zaleceń dotyczących optymalizacji kosztów. Narzędzie Azure Advisor pomaga przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. W tym samouczku przedstawiono przykład gdzie identyfikowanie niedostatecznie używanych zasobów platformy Azure, a następnie podjęcia działania w celu ograniczenia kosztów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie kosztów optymalizacji zalecenia, aby wyświetlić potencjalnych przypadków niewydajnego użycia
> * Zajmującym się zalecenie, aby zmienić rozmiar maszyny wirtualnej do bardziej ekonomiczna opcja
> * Sprawdź akcji, aby upewnić się, że maszyna wirtualna został pomyślnie zmieniony

## <a name="prerequisites"></a>Wymagania wstępne
Zalecenia są dostępne dla różnych zakresów i typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlać dane kosztów, musisz mieć co najmniej prawa dostępu do odczytu co najmniej jednego z poniższych zakresów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

- Subscription
- Grupa zasobów

Konieczne jest posiadanie aktywnej maszyny wirtualne z co najmniej 14 dni aktywności.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Wyświetl zalecenia dotyczące optymalizacji kosztów

Aby wyświetlić zalecenia dotyczące optymalizacji kosztów dla subskrypcji, otwórz żądany zakres w Azure Portal i wybierz zalecenia usługi **Advisor**.

Aby wyświetlić zalecenia dotyczące grupy zarządzania, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu, takiego jak grupa zarządzania. Wybierz opcję **rekomendacje klasyfikatora** w menu. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

![Klasyfikator zarządzania zalecenia dotyczące kosztów wyświetlane w witrynie Azure portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Lista zaleceń identyfikuje przypadków niewydajnego użycia lub pokazuje, że zalecenia zakupu, które mogą pomóc Ci zaoszczędzić dodatkowe. Łączne **potencjalne oszczędności roczne** przedstawiają łączną kwotę, którą można zapisać w przypadku wyłączenia lub cofnięcia przydziału wszystkich maszyn wirtualnych spełniających reguły rekomendacji. Jeśli nie chcesz je wyłączyć, należy rozważyć zmianę rozmiaru ich do mniej kosztowne jednostki SKU maszyny Wirtualnej.

Kategoria **wpływ** , wraz z **potencjalnymi oszczędnościami rocznymi**, została zaprojektowana w celu ułatwienia identyfikacji zaleceń, które mogą zaoszczędzić możliwie największej ilości.

Zalecenia dotyczące wysokiego wpływu obejmują:
- [Kup zarezerwowane wystąpienia maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatność zgodnie z rzeczywistym użyciem](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optymalizowanie wydatków maszyn wirtualnych przez zmianę rozmiarów lub zamykanie nieużywanych wystąpień](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Przechowywanie Managed Disks migawek przy użyciu magazynu w warstwie Standardowa](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Zalecenia dotyczące średniego wpływu:
- [Usuń potoki Azure Data Factory, które kończą się niepowodzeniem](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Zmniejsz koszty, eliminując nieobsługiwane obwody usługi ExpressRoute](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Obniżenie kosztów przez usunięcie lub ponowne skonfigurowanie bezczynnych bram sieci wirtualnej](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Działają zgodnie z zaleceniem

Azure Advisor monitoruje użycie maszyn wirtualnych przez siedem dni, a następnie identyfikuje nieużywane maszyny wirtualne. Maszyny wirtualne, których użycie procesora CPU osiągnie wartość 5% lub mniej, a użycie sieci to 7 MB lub mniej przez cztery lub więcej dni są traktowane jako niskie użycie maszyn wirtualnych.

5% lub mniej ustawienie wykorzystanie procesora CPU jest ustawieniem domyślnym, ale można dostosować ustawienia. Aby uzyskać więcej informacji na temat dostosowywania ustawienia, zobacz [Konfigurowanie średniej zasady użycia procesora CPU lub zalecenia dotyczącego niskiego użycia maszyny wirtualnej](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Chociaż w niektórych scenariuszach może spowodować niewielkie wykorzystanie zgodnie z projektem, często można oszczędzić pieniądze, zmieniając rozmiar maszyn wirtualnych do rozmiarów mniej kosztowne. Rzeczywiste oszczędności mogą się różnić, jeśli akcja zmiany rozmiaru. Przejdźmy teraz przez przykład zmiany rozmiaru maszyny wirtualnej.

Na liście zaleceń kliknij odpowiednie zalecenie dotyczące **niedostatecznego rozmiaru lub zamknięcia nieużywanych maszyn wirtualnych** . Na liście kandydatów maszyny wirtualnej wybierz maszynę wirtualną, aby zmienić rozmiar, a następnie kliknij maszynę wirtualną. Szczegóły maszyny wirtualnej są wyświetlane, dzięki czemu można sprawdzić metryki wykorzystania. Wartość możliwego **oszczędności rocznego** to to, co można zapisać w przypadku wyłączenia lub usunięcia maszyny wirtualnej. Zmiana rozmiaru maszyny Wirtualnej będzie prawdopodobnie zaoszczędzić pieniądze, ale nie zapisuje cała kwota potencjalne oszczędności roczne.

![Przykład szczegóły zalecenia](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Szczegóły maszyny Wirtualnej Sprawdź wykorzystanie maszyny wirtualnej, aby potwierdzić, że jest kandydat odpowiedniego rozmiaru.

![Maszyna wirtualna w poniższym przykładzie omówiono przedstawiający wykorzystanie historycznych](./media/tutorial-acm-opt-recommendations/vm-details.png)

Należy pamiętać, rozmiar bieżącej maszyny wirtualnej. Po upewnieniu się, że powinno zmienić rozmiar maszyny wirtualnej, należy zamknąć szczegóły maszyny Wirtualnej tak, aby wyświetlić listę maszyn wirtualnych.

Na liście kandydatów do zamknięcia lub zmiany rozmiaru wybierz pozycję * * Zmień rozmiar *&lt;FromVirtualMachineSKU&gt;* na *&lt;ToVirtualMachineSKU&gt;* * *.
![przykładowe zalecenie z opcją zmiany rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Następnie zobaczysz listę opcji dostępnych zmiany rozmiaru. Wybierz ten, który zapewni najlepszą wydajność i efektywność kosztową dla danego scenariusza. W poniższym przykładzie wybrana opcja zmienia rozmiar z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Przykład listę dostępnych rozmiarów maszyn wirtualnych, w którym można wybrać rozmiar](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po wybraniu odpowiedniego rozmiaru kliknij pozycję **Zmień** rozmiar, aby uruchomić akcję Zmień rozmiar.

Zmiana rozmiaru wymaga aktywnie uruchomionej maszyny wirtualnej, aby ponownie uruchomić. Jeśli maszyna wirtualna znajduje się w środowisku produkcyjnym, zaleca się uruchomienie operacji zmiany rozmiaru po godzinach pracy. Planowanie ponownego uruchomienia, można zmniejszyć spowodowany przez niedostępność chwilowo przerw w działaniu.

## <a name="verify-the-action"></a>Sprawdź akcji

Podczas zmiany rozmiaru maszyny Wirtualnej zakończy się pomyślnie, wyświetlane jest powiadomienie dotyczące urządzenia Azure.

![Pomyślnie zmieniono rozmiar maszyny wirtualnej powiadomień](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie kosztów optymalizacji zalecenia, aby wyświetlić potencjalnych przypadków niewydajnego użycia
> * Zajmującym się zalecenie, aby zmienić rozmiar maszyny wirtualnej do bardziej ekonomiczna opcja
> * Sprawdź akcji, aby upewnić się, że maszyna wirtualna został pomyślnie zmieniony

Jeśli nie zostały już przeczytane Cost Management najlepsze rozwiązania dotyczące, zawiera ogólne wskazówki i zasady, które należy wziąć pod uwagę, aby pomóc w zarządzaniu kosztami.

> [!div class="nextstepaction"]
> [Cost Management najlepszych praktyk](cost-mgt-best-practices.md)
