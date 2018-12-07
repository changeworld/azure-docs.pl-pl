---
title: Samouczek — tworzenie i zarządzanie nimi budżetów platformy Azure | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia planu i konta koszty usług systemu Azure, które zostaną zużyte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: f64a82cc3096c3c4d2a47568f4d6d516c685ee04
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52991749"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie i zarządzanie nimi budżetów platformy Azure

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one poinformować inne osoby o ich wydatków, aby aktywnie zarządzać kosztami i monitorować, jak wydatków w miarę wraz z upływem czasu. Po przekroczeniu progów budżetu, który został utworzony, są wyzwalane tylko powiadomienia. Wpływają na żaden z zasobów i nie zostanie ono zatrzymane swoje użycie. Budżetów służy do porównywania i śledzić wydatki jako analizując koszty.

Budżetów automatycznie resetować na końcu okresu (co miesiąc, co kwartał lub rocznie) na tym samym kwotę budżetu po wybraniu przyszłą datę wygaśnięcia. Ponieważ są one resetowane sama kwota budżetu, musisz utworzyć oddzielne budżety, gdy budżet kwot różnią się w przyszłych okresach.

W przykładach w tym samouczku prowadzą użytkownika przez proces tworzenia i edytowania budżetu w celu uzyskania subskrypcji Azure Enterprise Agreement (EA).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

## <a name="prerequisites"></a>Wymagania wstępne

Budżetów są dostępne dla wszystkich klientów z umowami EA platformy Azure. Musi mieć dostęp do odczytu do budżetów widoku subskrypcji umowy EA platformy Azure. Tworzenie i zarządzanie nimi budżety, musi mieć uprawnienia współautora. Można utworzyć indywidualne budżetów dla subskrypcji EA i grupy zasobów. Jednak nie można utworzyć budżetów dla rozliczeń konta EA.

Następujące uprawnienia platformy Azure są obsługiwane na subskrypcję dla budżetów przez użytkowników i grup:

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor i współautor Cost Management — można utworzyć, zmodyfikować lub usunąć swoje własne budżetów. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytnik i czytnika Cost Management — można wyświetlić budżety, które mają uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do danych rozwiązania Cost Management, zobacz [przypisywanie dostępu do danych rozwiązania Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Tworzenie budżetu w witrynie Azure portal

W okresie miesięczne, kwartałów lub można utworzyć budżet subskrypcji platformy Azure. Nawigacyjne zawartości w witrynie Azure portal Określa, czy utworzyć budżetu dla subskrypcji lub grupy zasobów.

W witrynie Azure portal przejdź do **Zarządzanie kosztami i rozliczenia** &gt; **subskrypcje** &gt; Wybierz subskrypcję, &gt; **budżetów**. W tym przykładzie budżet, tworzona jest wybrana subskrypcja.

Po utworzeniu budżetów pokazują widok prosty bieżące wydatki względem nich.

Kliknij pozycję **Add** (Dodaj).

![Zarządzanie budżetów kosztów](./media/tutorial-acm-create-budgets/budgets01.png)

W **budżetu Utwórz** okna, wprowadź nazwę budżetu i budżetu. Następnie wybierz, co miesiąc, co kwartał, lub roczna czasu trwania. Następnie wybierz datę zakończenia. Budżetów wymagają co najmniej jeden koszt progu (% budżetu) i odpowiedniego adresu e-mail. Możesz opcjonalnie dołączyć maksymalnie pięć progi i pięciu adresów e-mail w jednym budżetu. Po spełnieniu próg budżetu powiadomienia e-mail są zwykle odbierane w mniej niż osiem godzin.

Oto przykład tworzenia miesięcznego budżetu dla 4500 zł. Wiadomość e-mail z alertem pobiera wygenerowany, gdy zostanie osiągnięta 90% budżetu.

![Miesięczne przykład budżetu](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po utworzeniu co kwartał budżetu działa w taki sam sposób jak miesięcznego budżetu. Różnica polega na tym, że kwota budżetu na kwartał jest równomiernie podzielone między trzy miesiące kwartału. Zgodnie z oczekiwaniami, roczne kwota budżetu jest równomiernie podzielone między wszystkie 12 miesięcy w roku kalendarzowego.

Bieżących wydatków w porównaniu do budżetów jest aktualizowana po każdym Cost Management otrzymuje zaktualizowane dane rozliczeń. Zwykle, codziennie.

![Bieżąca wydatków w porównaniu do budżetów](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Po utworzeniu budżetu, jest wyświetlana na analizy kosztów. Przeglądanie budżetu względem Twojej trendu wydatków jest jednym z pierwszych kroków, gdy rozpocznie się [analizowanie koszty i wydatki](quick-acm-cost-analysis.md).

![Budżet objętego analiza kosztów](./media/tutorial-acm-create-budgets/cost-analysis.png)

W powyższym przykładzie utworzono budżetu na subskrypcję. Jednak można również utworzyć budżetu dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, przejdź do **Zarządzanie kosztami i rozliczenia** &gt; **subskrypcje** &gt; wybrać subskrypcję > **zasobów grupy** > Wybierz grupę zasobów > **budżetów** > a następnie **Dodaj** budżetu.

## <a name="edit-a-budget"></a>Edytuj budżetu

W zależności od poziomu dostępu użytkownika można edytować budżetu, aby zmienić jego właściwości. W poniższym przykładzie niektóre właściwości są tylko do odczytu, ponieważ użytkownik ma tylko uprawnienia współautora do subskrypcji. Obecnie **datę wygaśnięcia** jest wyłączona i nie może być modyfikowany po ustawieniu.

![Edytuj budżet — uprawnienia współautora](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

Przejdź do następnego samouczka, aby utworzyć cykliczne eksportu dla usługi danych rozwiązania cost management.

> [!div class="nextstepaction"]
> [Tworzenie i zarządzanie nimi wyeksportowanych danych](tutorial-export-acm-data.md)
