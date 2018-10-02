---
title: Samouczek — tworzenie i zarządzanie nimi budżetów platformy Azure | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia planu i konta koszty usług systemu Azure, które zostaną zużyte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/01/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 50bd22559c3695ac4161932652eb191084e2b46e
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017366"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie i zarządzanie nimi budżetów platformy Azure

Budżetów w Cost Management ułatwiają planowanie i dysku świadomości finansowej w organizacji. Za pomocą budżety może uwzględnić usług platformy Azure, używają lub subskrybować w określonym czasie. Ułatwiają one poinformować inne osoby o ich wydatków, aby aktywnie zarządzać kosztami i monitorować, jak wydatków w miarę wraz z upływem czasu. W miarę jak wydatków można zobaczyć, wraz z upływem czasu. Po przekroczeniu progów budżetu, który został utworzony, są wyzwalane tylko powiadomienia. Wpływają na żaden z zasobów i nie zostanie ono zatrzymane swoje użycie. Budżetów służy do porównywania i śledzić wydatki jako analizując koszty.

Budżetów automatycznie resetować na końcu okresu (co miesiąc, co kwartał lub rocznie) na tym samym kwotę budżetu po wybraniu datę wygaśnięcia w przyszłości. Ponieważ są one resetowane sama kwota budżetu, musisz utworzyć oddzielne budżety, gdy budżet kwot różnią się w przyszłych okresach.

W przykładach w tym samouczku ilustrują tworzenie i edytowanie budżetu w celu uzyskania subskrypcji Azure Enterprise Agreement (EA).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure portal
> * Edytuj budżetu

## <a name="prerequisites"></a>Wymagania wstępne

Budżetów są dostępne dla wszystkich klientów z umowami EA platformy Azure. Musi mieć dostęp do odczytu do subskrypcji umowy EA platformy Azure do tworzenia i zarządzania budżet. Konta rozliczeniowego EA nie są obsługiwane przez budżet.

Na subskrypcję lub na poziomie grupy zasobów, budżety są tworzone pojedynczo. Następujące uprawnienia platformy Azure są obsługiwane na subskrypcję dla budżetów przez użytkowników i grup:

- Właściciel — może tworzyć, modyfikować ani usuwać budżetów dla subskrypcji.
- Współautor — może tworzyć, modyfikować ani usuwać własne budżety. Można modyfikować wielkość budżetu dla budżetów tworzonych przez innych użytkowników.
- Czytelnik — można wyświetlić budżety, które mają uprawnienia.

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
