---
title: Samouczek — redukcja kosztów platformy Azure z zaleceniami dotyczącymi optymalizacji | Microsoft Docs
description: Ten samouczek ułatwia obniżenie kosztów platformy Azure w przypadku wykonywania zaleceń dotyczących optymalizacji.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935827"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Samouczek: Optymalizacja kosztów z zaleceń

Usługa Azure Cost Management współdziała z usługą Azure Advisor podczas ustalania zaleceń dotyczących optymalizacji kosztów. Narzędzie Azure Advisor pomaga przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Ten samouczek przeprowadzi Cię przez przykład w celu zidentyfikowania nieużywanych zasobów platformy Azure, a następnie podjęcia działań w celu obniżenia kosztów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetl zalecenia dotyczące optymalizacji kosztów, aby wyświetlić potencjalne nieefektywność użycia
> * Wykonaj zalecenie dotyczące zmiany rozmiaru maszyny wirtualnej na bardziej opłacalną
> * Sprawdź akcję, aby upewnić się, że rozmiar maszyny wirtualnej został pomyślnie zmieniony

## <a name="prerequisites"></a>Wymagania wstępne
Zalecenia są dostępne dla różnych zakresów i typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlać dane kosztów, musisz mieć co najmniej prawa dostępu do odczytu co najmniej jednego z poniższych zakresów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

- Subskrypcja
- Grupa zasobów

Musisz mieć aktywne maszyny wirtualne z co najmniej 14 dni działania.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Wyświetlanie zaleceń dotyczących optymalizacji kosztów

Aby wyświetlić zalecenia dotyczące optymalizacji kosztów dla subskrypcji, otwórz żądany zakres w Azure Portal i wybierz zalecenia usługi **Advisor**.

Aby wyświetlić zalecenia dotyczące grupy zarządzania, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu, takiego jak grupa zarządzania. Wybierz opcję **rekomendacje klasyfikatora** w menu. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

![Zalecenia dotyczące Cost Management Advisor pokazane w Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Lista zaleceń określa nieefektywność użycia lub zawiera zalecenia dotyczące zakupu, które mogą pomóc w zapisaniu dodatkowych pieniędzy. Łączne **potencjalne oszczędności roczne** przedstawiają łączną kwotę, którą można zapisać w przypadku wyłączenia lub cofnięcia przydziału wszystkich maszyn wirtualnych spełniających reguły rekomendacji. Jeśli nie chcesz ich wyłączać, Rozważ zmianę ich rozmiarów na tańszą jednostkę SKU maszyny wirtualnej.

Kategoria **wpływ** , wraz z **potencjalnymi oszczędnościami rocznymi**, została zaprojektowana w celu ułatwienia identyfikacji zaleceń, które mogą zaoszczędzić możliwie największej ilości.

Zalecenia dotyczące wysokiego wpływu obejmują:
- [Kup zarezerwowane wystąpienia maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatność zgodnie z rzeczywistym użyciem](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optymalizowanie wydatków maszyn wirtualnych przez zmianę rozmiarów lub zamykanie nieużywanych wystąpień](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Przechowywanie Managed Disks migawek przy użyciu magazynu w warstwie Standardowa](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Zalecenia dotyczące średniego wpływu:
- [Usuń potoki Azure Data Factory, które kończą się niepowodzeniem](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Zmniejsz koszty, eliminując nieobsługiwane obwody usługi ExpressRoute](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Obniżenie kosztów przez usunięcie lub ponowne skonfigurowanie bezczynnych bram sieci wirtualnej](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Wykonaj zalecenie

Azure Advisor monitoruje użycie maszyn wirtualnych przez siedem dni, a następnie identyfikuje nieużywane maszyny wirtualne. Maszyny wirtualne, których wykorzystanie procesora CPU wynosi pięć% lub mniej, a użycie sieci wynosi siedem MB lub mniej przez cztery lub więcej dni, są uznawane za maszyny wirtualne o niskim obciążeniu.

Wartość domyślna to 5% lub mniejsze użycie procesora CPU, ale można dostosować ustawienia. Aby uzyskać więcej informacji na temat dostosowywania ustawienia, zobacz [Konfigurowanie średniej zasady użycia procesora CPU lub zalecenia dotyczącego niskiego użycia maszyny wirtualnej](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Mimo że niektóre scenariusze mogą spowodować niskie wykorzystanie według projektu, często można zaoszczędzić pieniądze, zmieniając rozmiar maszyn wirtualnych na mniej kosztowne. Rzeczywiste oszczędności mogą się różnić w przypadku wybrania akcji zmiany rozmiaru. Zapoznaj się z przykładem zmiany rozmiarów maszyny wirtualnej.

Na liście zaleceń kliknij odpowiednie zalecenie dotyczące **niedostatecznego rozmiaru lub zamknięcia nieużywanych maszyn wirtualnych** . Na liście kandydatów maszyn wirtualnych wybierz maszynę wirtualną, której rozmiar chcesz zmienić, a następnie kliknij maszynę wirtualną. Szczegóły maszyny wirtualnej są wyświetlane, aby można było sprawdzić metryki wykorzystania. Wartość możliwego **oszczędności rocznego** to to, co można zapisać w przypadku wyłączenia lub usunięcia maszyny wirtualnej. Zmianę rozmiaru maszyny wirtualnej prawdopodobnie spowoduje zaoszczędzenie pieniędzy, ale nie będzie można zaoszczędzić pełnych oszczędności rocznie.

![Przykład szczegółów rekomendacji](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Sprawdź, czy w obszarze Szczegóły maszyny wirtualnej jest używane maszyna wirtualna, aby upewnić się, że jest to odpowiedni kandydat zmiany rozmiaru.

![Przykładowe szczegóły maszyny wirtualnej przedstawiające wykorzystanie historyczne](./media/tutorial-acm-opt-recommendations/vm-details.png)

Zanotuj rozmiar bieżącej maszyny wirtualnej. Po zweryfikowaniu, że rozmiar maszyny wirtualnej powinien zostać zmieniony, Zamknij Szczegóły maszyny wirtualnej, aby wyświetlić listę maszyn wirtualnych.

Na liście kandydatów do zamknięcia lub zmiany rozmiaru wybierz pozycję * * Zmień rozmiar *&lt;FromVirtualMachineSKU&gt;* na *&lt;ToVirtualMachineSKU&gt;* * *.
![przykładowe zalecenie z opcją zmiany rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Następnie zostanie wyświetlona lista dostępnych opcji zmiany rozmiaru. Wybierz ten, który zapewni najlepszą wydajność i efektywność dla Twojego scenariusza. W poniższym przykładzie wybrana opcja zmienia rozmiar z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Przykładowa lista dostępnych rozmiarów maszyn wirtualnych, w których można wybrać rozmiar](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po wybraniu odpowiedniego rozmiaru kliknij pozycję **Zmień** rozmiar, aby uruchomić akcję Zmień rozmiar.

Zmiany rozmiarów wymagają uruchomionej aktywnej maszyny wirtualnej w celu ponownego uruchomienia. Jeśli maszyna wirtualna znajduje się w środowisku produkcyjnym, zalecamy wykonanie operacji zmiany rozmiaru po godzinach pracy. Zaplanowanie ponownego uruchomienia może zmniejszyć liczbę przerw spowodowanych przez chwilę niedostępności.

## <a name="verify-the-action"></a>Weryfikuj akcję

Po pomyślnym zakończeniu zmiany rozmiarów maszyny wirtualnej zostanie wyświetlone powiadomienie platformy Azure.

![Powiadomienie o pomyślnym zmianie rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetl zalecenia dotyczące optymalizacji kosztów, aby wyświetlić potencjalne nieefektywność użycia
> * Wykonaj zalecenie dotyczące zmiany rozmiaru maszyny wirtualnej na bardziej opłacalną
> * Sprawdź akcję, aby upewnić się, że rozmiar maszyny wirtualnej został pomyślnie zmieniony

Jeśli nie odczytano jeszcze artykułu Cost Management Best Practices, przedstawiono wskazówki i zasady wysokiego poziomu, które należy wziąć pod uwagę w celu ułatwienia zarządzania kosztami.

> [!div class="nextstepaction"]
> [Cost Management najlepszych praktyk](cost-mgt-best-practices.md)
