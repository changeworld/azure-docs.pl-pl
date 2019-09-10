---
title: Samouczek — tworzenie i zarządzanie nimi budżetów platformy Azure | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia planu i konta koszty usług systemu Azure, które zostaną zużyte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 59ae3b587751bd4af2c9e5ab0abefb8a5c3bf8e6
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843939"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one poinformować inne osoby o ich wydatków, aby aktywnie zarządzać kosztami i monitorować, jak wydatków w miarę wraz z upływem czasu. Po przekroczeniu progów budżetu, który został utworzony, są wyzwalane tylko powiadomienia. Wpływają na żaden z zasobów i nie zostanie ono zatrzymane swoje użycie. Budżetów służy do porównywania i śledzić wydatki jako analizując koszty.

Miesięczne budżety są oceniane w odniesieniu do wydatków co cztery godziny. Jednak dane dla używanych zasobów są dostępne w ciągu ośmiu godzin. Po spełnieniu progu budżetowego powiadomienia e-mail są zwykle odbierane w czasie krótszym niż 12 godzin. 

Budżetów automatycznie resetować na końcu okresu (co miesiąc, co kwartał lub rocznie) na tym samym kwotę budżetu po wybraniu przyszłą datę wygaśnięcia. Ponieważ są one resetowane sama kwota budżetu, musisz utworzyć oddzielne budżety, gdy budżet kwot różnią się w przyszłych okresach.

W przykładach w tym samouczku prowadzą użytkownika przez proces tworzenia i edytowania budżetu w celu uzyskania subskrypcji Azure Enterprise Agreement (EA).

Obejrzyj, [jak utworzyć budżet do monitorowania wydatków dzięki Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) wideo, aby dowiedzieć się, jak tworzyć budżety na platformie Azure w celu monitorowania wydatków.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

## <a name="prerequisites"></a>Wymagania wstępne

Budżety są obsługiwane dla różnych typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić budżety, wymagany jest co najmniej dostęp do odczytu dla Twojego konta platformy Azure.

 W przypadku subskrypcji Azure EA należy mieć dostęp do odczytu w celu wyświetlenia budżetów. Tworzenie i zarządzanie nimi budżety, musi mieć uprawnienia współautora. Można utworzyć indywidualne budżetów dla subskrypcji EA i grupy zasobów. Jednak nie można utworzyć budżetów dla rozliczeń konta EA.

Następujące uprawnienia lub zakresy platformy Azure są obsługiwane na subskrypcję dla budżetów przez użytkownika i grupę. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor i współautor Cost Management — można utworzyć, zmodyfikować lub usunąć swoje własne budżetów. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytnik i czytnika Cost Management — można wyświetlić budżety, które mają uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do danych rozwiązania Cost Management, zobacz [przypisywanie dostępu do danych rozwiązania Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Tworzenie budżetu w witrynie Azure portal

W okresie miesięczne, kwartałów lub można utworzyć budżet subskrypcji platformy Azure. Zawartość nawigacyjna w Azure Portal określa, czy należy utworzyć budżet dla subskrypcji lub grupy zarządzania.

Aby utworzyć lub wyświetlić budżet, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **budżety** w menu. Na przykład przejdź do **subskrypcji**, wybierz subskrypcję z listy, a następnie wybierz pozycję **budżety** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu, takiego jak grupa zarządzania, w obszarze budżetów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

Po utworzeniu budżetów pokazują widok prosty bieżące wydatki względem nich.

Kliknij przycisk **Dodaj**.

![Przykład przedstawiający już utworzoną listę budżetów](./media/tutorial-acm-create-budgets/budgets01.png)

Upewnij się, że w oknie **Tworzenie budżetu** podano prawidłowy zakres. Wybierz wszystkie filtry, które chcesz dodać. Filtry umożliwiają tworzenie budżetów dla określonych kosztów, takich jak grupy zasobów w ramach subskrypcji lub usługi, takie jak maszyny wirtualne. Dowolny filtr, którego można użyć w analizie kosztów, można również zastosować do budżetu.

Po zidentyfikowaniu zakresu i filtrów wpisz nazwę budżetu. Następnie wybierz miesięczny, kwartalny lub roczny okres resetowania budżetu. Ten okres resetowania określa przedział czasu, który jest analizowany przez budżet. Koszt oceniany przez budżet rozpoczyna się od zera na początku każdego nowego okresu. Po utworzeniu co kwartał budżetu działa w taki sam sposób jak miesięcznego budżetu. Różnica polega na tym, że kwota budżetu na kwartał jest równomiernie podzielone między trzy miesiące kwartału. Roczna kwota budżetu jest równomiernie dzielona między wszystkie 12 miesięcy roku kalendarzowego.

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub Visual Studio, okres rozliczeniowy faktury może nie być wyrównany do miesiąca kalendarzowego. Dla tych typów subskrypcji i grup zasobów można utworzyć budżet wyrównany do okresu faktury lub miesięcy kalendarzowych. Aby utworzyć budżet wyrównany do okresu faktury, wybierz okres resetowania **miesiąca rozliczeniowego**, **kwartału rozliczeniowego**lub **roku rozliczeniowego**. Aby utworzyć budżet wyrównany do miesiąca kalendarzowego, wybierz okres resetowania **co miesiąc**, **co kwartał**lub co **rok**.

Następnie ustal datę wygaśnięcia budżetu, gdy budżet przestanie być ważny, a następnie Zakończ szacowanie kosztów.

W oparciu o pola wybrane w budżecie do tej pory wykres jest widoczny, aby ułatwić wybranie progu do użycia dla budżetu. Sugerowany Budżet bazuje na najwyższym prognozowanym koszcie, który można nawiązać w przyszłych okresach. Kwotę budżetu można zmienić.

![Przykład przedstawiający tworzenie budżetu z danymi kosztu miesięcznego ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po skonfigurowaniu kwoty budżetu kliknij przycisk **dalej** , aby skonfigurować alerty budżetu. Budżetów wymagają co najmniej jeden koszt progu (% budżetu) i odpowiedniego adresu e-mail. Możesz opcjonalnie dołączyć maksymalnie pięć progi i pięciu adresów e-mail w jednym budżetu. Po spełnieniu próg budżetu powiadomienia e-mail są zwykle odbierane w mniej niż osiem godzin. Aby uzyskać więcej informacji na temat powiadomień, zobacz [Korzystanie z alertów dotyczących kosztów](cost-mgt-alerts-monitor-usage-spending.md). W poniższym przykładzie zostanie wygenerowany alert e-mail, gdy zostanie osiągnięty limit 90% budżetu.

![Przykład pokazujący warunki alertu](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Po utworzeniu budżetu, jest wyświetlana na analizy kosztów. Przeglądanie budżetu względem Twojej trendu wydatków jest jednym z pierwszych kroków, gdy rozpocznie się [analizowanie koszty i wydatki](quick-acm-cost-analysis.md).

![Przykład budżetu i wydatków objętego analiza kosztów](./media/tutorial-acm-create-budgets/cost-analysis.png)

W powyższym przykładzie utworzono budżetu na subskrypcję. Jednak można również utworzyć budżetu dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, przejdź do **Zarządzanie kosztami i rozliczenia** &gt; **subskrypcje** &gt; wybrać subskrypcję > **zasobów grupy** > Wybierz grupę zasobów > **budżetów** > a następnie **Dodaj** budżetu.

## <a name="trigger-an-action-group"></a>Wyzwalanie grupy akcji

Podczas tworzenia lub edytowania budżetu dla zakresu subskrypcji lub grupy zasobów można skonfigurować go do wywoływania grupy akcji. Grupa akcji może wykonywać różne różne akcje po spełnieniu progu budżetu. Aby uzyskać więcej informacji na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](../azure-monitor/platform/action-groups.md). Aby uzyskać więcej informacji na temat używania automatyzacji opartego na budżecie z grupami akcji, zobacz [Zarządzanie kosztami przy użyciu budżetów platformy Azure](../billing/billing-cost-management-budget-scenario.md).

Aby utworzyć lub zaktualizować grupy akcji, kliknij przycisk **Zarządzaj grupami akcji** podczas tworzenia lub edytowania budżetu.

![Przykład tworzenia budżetu do wyświetlania grup akcji zarządzania](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Następnie kliknij pozycję **Dodaj grupę akcji** i Utwórz grupę akcji.


![Obraz pola Dodaj grupę akcji](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po utworzeniu grupy akcji Zamknij pole, aby wrócić do swojego budżetu.

Skonfiguruj budżet tak, aby korzystał z grupy akcji po spełnieniu pojedynczego progu. Obsługiwane są maksymalnie pięć różnych progów.

![Przykład pokazujący wybór grupy akcji dla warunku alertu](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

W poniższym przykładzie przedstawiono wartości progowe budżetu ustawione na 50%, 75% i 100%. Każdy z nich jest skonfigurowany do wyzwalania określonych akcji w ramach wyznaczonych grup akcji.

![Przykład pokazujący warunki alertów skonfigurowane z różnymi grupami akcji i typem akcji](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

Przejdź do następnego samouczka, aby utworzyć cykliczne eksportu dla usługi danych rozwiązania cost management.

> [!div class="nextstepaction"]
> [Tworzenie i zarządzanie nimi wyeksportowanych danych](tutorial-export-acm-data.md)
