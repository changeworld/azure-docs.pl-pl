---
title: Samouczek — tworzenie i zarządzanie nimi budżetów platformy Azure | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia planu i konta koszty usług systemu Azure, które zostaną zużyte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: eab45948b5f931377396d93d93e8955ba0f3e767
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792851"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie i zarządzanie nimi budżetów platformy Azure

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one poinformować inne osoby o ich wydatków, aby aktywnie zarządzać kosztami i monitorować, jak wydatków w miarę wraz z upływem czasu. Po przekroczeniu progów budżetu, który został utworzony, są wyzwalane tylko powiadomienia. Wpływają na żaden z zasobów i nie zostanie ono zatrzymane swoje użycie. Budżetów służy do porównywania i śledzić wydatki jako analizując koszty.

Miesięczne budżetów są sprawdzane pod kątem wydatków co cztery godziny. Jednak dane i powiadomienia dotyczące wykorzystanych zasobów są dostępne w ciągu ośmiu godzin.  

Budżetów automatycznie resetować na końcu okresu (co miesiąc, co kwartał lub rocznie) na tym samym kwotę budżetu po wybraniu przyszłą datę wygaśnięcia. Ponieważ są one resetowane sama kwota budżetu, musisz utworzyć oddzielne budżety, gdy budżet kwot różnią się w przyszłych okresach.

W przykładach w tym samouczku prowadzą użytkownika przez proces tworzenia i edytowania budżetu w celu uzyskania subskrypcji Azure Enterprise Agreement (EA).

Obejrzyj [sposób tworzenia budżetu monitorowanie wydatków za pomocą usługi Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) film, aby zobaczyć, jak utworzyć budżet na platformie Azure, aby monitorować wydatków.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

## <a name="prerequisites"></a>Wymagania wstępne

Budżetów są obsługiwane w przypadku różnych typów konta platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić budżety, muszą mieć co najmniej odczytu dostępu dla konta platformy Azure.

 W przypadku subskrypcji umowy EA platformy Azure musi mieć dostęp do odczytu, aby wyświetlić budżet. Tworzenie i zarządzanie nimi budżety, musi mieć uprawnienia współautora. Można utworzyć indywidualne budżetów dla subskrypcji EA i grupy zasobów. Jednak nie można utworzyć budżetów dla rozliczeń konta EA.

Następujące uprawnienia platformy Azure lub zakresów, są obsługiwane na subskrypcję dla budżetów przez użytkowników i grup. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor i współautor Cost Management — można utworzyć, zmodyfikować lub usunąć swoje własne budżetów. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytnik i czytnika Cost Management — można wyświetlić budżety, które mają uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do danych rozwiązania Cost Management, zobacz [przypisywanie dostępu do danych rozwiązania Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Tworzenie budżetu w witrynie Azure portal

W okresie miesięczne, kwartałów lub można utworzyć budżet subskrypcji platformy Azure. Nawigacyjne zawartości w witrynie Azure portal Określa, czy utworzyć budżetu dla subskrypcji lub grupy zarządzania.

Aby utworzyć lub wyświetlić budżetu, otwórz żądany zakres w witrynie Azure portal i wybierz pozycję **budżetów** w menu. Na przykład, przejdź do **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz **budżetów** w menu. Użyj **zakres** skażone, aby przełączyć się do innego zakresu, takich jak grupy zarządzania, w budżet. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

Po utworzeniu budżetów pokazują widok prosty bieżące wydatki względem nich.

Kliknij pozycję **Add** (Dodaj).

![Koszt zarządzania budżetów wyświetlane w witrynie Azure portal](./media/tutorial-acm-create-budgets/budgets01.png)

W **budżetu Utwórz** okna, wprowadź nazwę budżetu i budżetu. Następnie wybierz, co miesiąc, co kwartał, lub roczna czasu trwania. Następnie wybierz datę zakończenia. Budżetów wymagają co najmniej jeden koszt progu (% budżetu) i odpowiedniego adresu e-mail. Możesz opcjonalnie dołączyć maksymalnie pięć progi i pięciu adresów e-mail w jednym budżetu. Po spełnieniu próg budżetu powiadomienia e-mail są zwykle odbierane w mniej niż osiem godzin. Aby uzyskać więcej informacji na temat powiadomień, zobacz [Użyj koszt alerty](cost-mgt-alerts-monitor-usage-spending.md).

Jeśli masz subskrypcję płatność za rzeczywiste użycie, MSDN lub Visual Studio, okresu rozliczeniowego faktur mogą być niewyrównane z miesiącem kalendarzowym. Dla tych typów subskrypcji i grup zasobów można utworzyć budżetu, który jest wyrównany do okresu faktury lub miesiące. Aby utworzyć budżetu wyrównane do okresu faktury, wybierz okres resetowania rozliczeń miesiąca, kwartału rozliczeń lub rozliczeń roku. Do utworzenia budżetu wyrównane z miesiącem kalendarzowym, wybierz okres resetowania co miesiąc, co kwartał lub rocznie.

Oto przykład tworzenia miesięcznego budżetu dla 4500 zł. Wiadomość e-mail z alertem pobiera wygenerowany, gdy zostanie osiągnięta 90% budżetu.

![Przykładowe informacje wyświetlane w polu Utwórz budżetu](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po utworzeniu co kwartał budżetu działa w taki sam sposób jak miesięcznego budżetu. Różnica polega na tym, że kwota budżetu na kwartał jest równomiernie podzielone między trzy miesiące kwartału. Zgodnie z oczekiwaniami, roczne kwota budżetu jest równomiernie podzielone między wszystkie 12 miesięcy w roku kalendarzowego.

Bieżących wydatków w porównaniu do budżetów jest aktualizowana po każdym Cost Management otrzymuje zaktualizowane dane rozliczeń. Zwykle, codziennie.

![Przykładowe informacje wyświetlane bieżącego wydatków w porównaniu do budżetów](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Po utworzeniu budżetu, jest wyświetlana na analizy kosztów. Przeglądanie budżetu względem Twojej trendu wydatków jest jednym z pierwszych kroków, gdy rozpocznie się [analizowanie koszty i wydatki](quick-acm-cost-analysis.md).

![Przykład budżetu i wydatków objętego analiza kosztów](./media/tutorial-acm-create-budgets/cost-analysis.png)

W powyższym przykładzie utworzono budżetu na subskrypcję. Jednak można również utworzyć budżetu dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, przejdź do **Zarządzanie kosztami i rozliczenia** &gt; **subskrypcje** &gt; wybrać subskrypcję > **zasobów grupy** > Wybierz grupę zasobów > **budżetów** > a następnie **Dodaj** budżetu.

## <a name="edit-a-budget"></a>Edytuj budżetu

W zależności od poziomu dostępu użytkownika można edytować budżetu, aby zmienić jego właściwości. W poniższym przykładzie niektóre właściwości są tylko do odczytu, ponieważ użytkownik ma tylko uprawnienia współautora do subskrypcji. Obecnie **datę wygaśnięcia** jest wyłączona i nie może być modyfikowany po ustawieniu.

![Przykład edycji budżetu można zmieniać właściwości](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Wyzwalacz grupy akcji

Podczas tworzenia lub edytowania budżetu dla subskrypcji lub w zakresie grupy zasobów, można skonfigurować go do wywoływania grupy akcji. Grupy akcji można wykonać szereg różnych działań, gdy spełnione jest próg budżetu. Aby uzyskać więcej informacji o grupach akcji, zobacz [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](../azure-monitor/platform/action-groups.md). Aby uzyskać więcej informacji o korzystaniu z budżetu na podstawie automation z grup akcji, zobacz [Zarządzanie kosztami przy użyciu platformy Azure budżetów](../billing/billing-cost-management-budget-scenario.md).

Aby utworzyć lub zaktualizować grupy akcji, kliknij przycisk **Zarządzanie grupami działań** podczas tworzenia lub edytowania budżetu.

![Przykład tworzenia budżetu, aby wyświetlić grupy akcji zarządzania](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Następnie kliknij przycisk **Dodaj grupę akcji** i Utwórz grupę akcji.


![Obraz przedstawiający okno Dodawanie grupy akcji](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po wykonaniu akcji zostanie utworzona grupa, zamknij okno, aby powrócić do Twojego budżetu.

Skonfiguruj swój budżet, aby użyć grupy akcji po osiągnięciu progu indywidualnych. Maksymalnie pięciu różnych wartości progowe są obsługiwane.

![Przykład przedstawiający wybór grupy akcji dla stanu alertu](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Poniższy przykład pokazuje progów budżetu równa 50%, 75% do 100%. Każdy jest skonfigurowany do wyzwalania określonych akcji w obrębie grupy wyznaczonym akcji.

![Przykład przedstawiający warunki alertu skonfigurowane przy użyciu różnych grup akcji i typ akcji](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

Przejdź do następnego samouczka, aby utworzyć cykliczne eksportu dla usługi danych rozwiązania cost management.

> [!div class="nextstepaction"]
> [Tworzenie i zarządzanie nimi wyeksportowanych danych](tutorial-export-acm-data.md)
