---
title: Samouczek — Tworzenie budżetów platformy Azure i zarządzanie nimi | Microsoft Docs
description: Ten samouczek ułatwia planowanie i uwzględnianie kosztów używanych usług platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8c3c0574389fc9808af3cd70c928ede82d375076
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720712"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one informowanie innych o wydatkach o proaktywnie zarządzanie kosztami oraz monitorowanie postępu w czasie. Po przekroczeniu progów budżetu zostaną wyzwolone tylko powiadomienia. Nie ma to żadnego oddziaływania i Twoje użycie nie zostało zatrzymane. Budżetów można używać do porównywania i śledzenia wydatków podczas analizowania kosztów.

Dane dotyczące kosztów i użycia są zwykle dostępne w ciągu 12-16 godzin, a budżety są oceniane względem tych kosztów co cztery godziny. Powiadomienia e-mail są zwykle odbierane w ciągu 12-16 godzin.

Budżety są automatycznie resetowane na koniec okresu (co miesiąc, co kwartał lub co rok) dla tej samej kwoty budżetu w przypadku wybrania w przyszłości daty wygaśnięcia. Ponieważ resetuje się z tą samą kwotą budżetu, należy utworzyć oddzielne budżety, gdy budżetowane kwoty waluty różnią się w przyszłości.

Przykłady w tym samouczku przeprowadzą Cię przez proces tworzenia i edytowania budżetu dla subskrypcji usługi Azure Umowa Enterprise (EA).

Obejrzyj, [jak utworzyć budżet do monitorowania wydatków dzięki Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) wideo, aby dowiedzieć się, jak tworzyć budżety na platformie Azure w celu monitorowania wydatków.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz budżet w Azure Portal
> * Edytuj budżet

## <a name="prerequisites"></a>Wymagania wstępne

Budżety są obsługiwane dla różnych typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić budżety, wymagany jest co najmniej dostęp do odczytu dla Twojego konta platformy Azure.

 W przypadku subskrypcji Azure EA należy mieć dostęp do odczytu w celu wyświetlenia budżetów. Aby tworzyć budżety i zarządzać nimi, musisz mieć uprawnienie współautor. Można tworzyć poszczególne budżety dla subskrypcji EA i grup zasobów. Nie można jednak tworzyć budżetów dla kont z rozliczeniami EA.

Następujące uprawnienia lub zakresy platformy Azure są obsługiwane na subskrypcję dla budżetów przez użytkownika i grupę. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor współautora i Cost Management — umożliwia tworzenie, modyfikowanie i usuwanie własnych budżetów. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytnik i Cost Management Reader — mogą wyświetlać budżety, do których mają uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do Cost Management danych, zobacz [Przypisywanie dostępu do Cost Management danych](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Utwórz budżet w Azure Portal

Budżet subskrypcji platformy Azure można utworzyć co miesiąc, co kwartał lub przez okres roczny. Zawartość nawigacyjna w Azure Portal określa, czy należy utworzyć budżet dla subskrypcji lub grupy zarządzania.

Aby utworzyć lub wyświetlić budżet, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **budżety** w menu. Na przykład przejdź do **subskrypcji**, wybierz subskrypcję z listy, a następnie wybierz pozycję **budżety** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu, takiego jak grupa zarządzania, w obszarze budżetów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

Po utworzeniu budżetów pokazuje prosty Widok bieżących wydatków.

Kliknij pozycję **Dodaj**.

![Przykład przedstawiający już utworzoną listę budżetów](./media/tutorial-acm-create-budgets/budgets01.png)

Upewnij się, że w oknie **Tworzenie budżetu** podano prawidłowy zakres. Wybierz wszystkie filtry, które chcesz dodać. Filtry umożliwiają tworzenie budżetów dla określonych kosztów, takich jak grupy zasobów w ramach subskrypcji lub usługi, takie jak maszyny wirtualne. Dowolny filtr, którego można użyć w analizie kosztów, można również zastosować do budżetu.

Po zidentyfikowaniu zakresu i filtrów wpisz nazwę budżetu. Następnie wybierz miesięczny, kwartalny lub roczny okres resetowania budżetu. Ten okres resetowania określa przedział czasu, który jest analizowany przez budżet. Koszt oceniany przez budżet rozpoczyna się od zera na początku każdego nowego okresu. Podczas tworzenia budżetu kwartalnego działa on w ten sam sposób co miesięczny budżet. Różnica polega na tym, że kwota budżetu kwartału jest równomiernie dzielona między trzy miesiące kwartału. Roczna kwota budżetu jest równomiernie dzielona między wszystkie 12 miesięcy roku kalendarzowego.

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub Visual Studio, okres rozliczeniowy faktury może nie być wyrównany do miesiąca kalendarzowego. Dla tych typów subskrypcji i grup zasobów można utworzyć budżet wyrównany do okresu faktury lub miesięcy kalendarzowych. Aby utworzyć budżet wyrównany do okresu faktury, wybierz okres resetowania **miesiąca rozliczeniowego**, **kwartału rozliczeniowego**lub **roku rozliczeniowego**. Aby utworzyć budżet wyrównany do miesiąca kalendarzowego, wybierz okres resetowania **co miesiąc**, **co kwartał**lub co **rok**.

Następnie ustal datę wygaśnięcia budżetu, gdy budżet przestanie być ważny, a następnie Zakończ szacowanie kosztów.

W oparciu o pola wybrane w budżecie do tej pory wykres jest widoczny, aby ułatwić wybranie progu do użycia dla budżetu. Sugerowany Budżet bazuje na najwyższym prognozowanym koszcie, który można nawiązać w przyszłych okresach. Kwotę budżetu można zmienić.

![Przykład przedstawiający tworzenie budżetu z danymi kosztu miesięcznego ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po skonfigurowaniu kwoty budżetu kliknij przycisk **dalej** , aby skonfigurować alerty budżetu. Budżety wymagają co najmniej jednego progu kosztu (% budżetu) i odpowiadającego mu adresu e-mail. Opcjonalnie możesz uwzględnić maksymalnie pięć progów i pięć adresów e-mail w ramach jednego budżetu. Po spełnieniu progu budżetowego powiadomienia e-mail są zwykle odbierane w czasie krótszym niż 20 godzin. Aby uzyskać więcej informacji na temat powiadomień, zobacz [Korzystanie z alertów dotyczących kosztów](cost-mgt-alerts-monitor-usage-spending.md). W poniższym przykładzie zostanie wygenerowany alert e-mail, gdy zostanie osiągnięty limit 90% budżetu. W przypadku tworzenia budżetu za pomocą interfejsu API budżetów można także przypisać role do osób, które będą otrzymywać alerty. Przypisywanie ról do osób nie jest obsługiwane w Azure Portal. Aby uzyskać więcej informacji o interfejsie API budżetów platformy Azure, zobacz temat [budżety interfejsu API](/rest/api/consumption/budgets).

![Przykład pokazujący warunki alertu](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Po utworzeniu budżetu jest on pokazywany w analizie kosztów. Wyświetlanie budżetu w odniesieniu do trendu wydatków jest jednym z pierwszych kroków po rozpoczęciu [analizy kosztów i wydatków](quick-acm-cost-analysis.md).

![Przykład budżetu i wydatków przedstawionych w analizie kosztów](./media/tutorial-acm-create-budgets/cost-analysis.png)

W poprzednim przykładzie utworzono budżet dla subskrypcji. Można jednak również utworzyć budżet dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, przejdź do **Cost Management i Rozliczeń** &gt; **subskrypcje** &gt; wybierz subskrypcję > **grupy zasobów** > Wybierz grupę zasobów > **budżety** > a następnie **Dodaj** budżet.

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

Integracja budżetu z grupami akcji działa tylko dla grup akcji, dla których jest wyłączony wspólny schemat alertów. Aby uzyskać więcej informacji na temat wyłączania schematu, zobacz [Jak mogę włączyć wspólny schemat alertów?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz budżet w Azure Portal
> * Edytuj budżet

Przejdź do następnego samouczka, aby utworzyć cykliczny eksport danych związanych z zarządzaniem kosztami.

> [!div class="nextstepaction"]
> [Tworzenie eksportowanych danych i zarządzanie nimi](tutorial-export-acm-data.md)
