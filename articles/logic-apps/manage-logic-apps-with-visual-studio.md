---
title: Zarządzanie aplikacjami logiki w usłudze Visual Studio — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie logiki aplikacji i innych zasobów platformy Azure z programu Visual Studio Cloud Explorer
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 7914bce6ca71b1b3f00c69fb6f33154f0f52dc7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Zarządzanie aplikacjami logiki z programem Visual Studio

Mimo że można tworzyć, edytować, zarządzanie i wdrażanie aplikacji logiki w <a href="https://portal.azure.com" target="_blank">portalu Azure</a>, można również użyć programu Visual Studio umożliwia dodawanie aplikacji logiki do kontroli źródła, publikować różne wersje i tworzenia [zasobów Azure Menedżer](../azure-resource-manager/resource-group-overview.md) szablonów dla wdrożenia w różnych środowiskach. Z programu Visual Studio Cloud Explorer można znaleźć i zarządzania aplikacjami logiki oraz innych zasobów platformy Azure. Na przykład można otworzyć, Pobierz, edytować, uruchomić, wyświetlanie historii wykonywania, wyłącz i Włącz logikę aplikacji, które są już wdrożone w portalu Azure. Jeśli masz doświadczenia w pracy z usługą Azure Logic Apps w programie Visual Studio, Dowiedz się [sposób tworzenia aplikacji logiki z programem Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Wdrażanie i publikowanie aplikacji logiki w programie Visual Studio zastępuje z wersji tej aplikacji w portalu Azure. Jeśli wprowadzisz zmiany w portalu Azure, które chcesz zachować, upewnij się, że możesz [Odśwież aplikację logiki w programie Visual Studio](#refresh) z portalu Azure przed następnym wdrażania lub opublikować w programie Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 lub Visual Studio 2015 — Community Edition lub nowszy</a>. 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Zestaw Azure SDK (2.9.1 lub nowszy)</a> i program <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * Narzędzia <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Azure Logic Apps Tools for Visual Studio 2017</a> lub program <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a> 
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">jak zainstalować to rozszerzenie z poziomu programu Visual Studio</a>. 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do sieci web podczas korzystania z projektanta aplikacji logiki osadzonych

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Znajdowanie aplikacji logiki

W programie Visual Studio można znaleźć wszystkie aplikacje logiki, które są skojarzone z subskrypcją platformy Azure i są wdrożone w portalu Azure za pomocą Eksploratora chmury.

1. Otwórz program Visual Studio. Na **widoku** menu, wybierz opcję **Eksplorator chmury**.

2. W Eksploratorze chmury, wybierz **zarządzania kontami**. Wybierz subskrypcję platformy Azure skojarzone z aplikacji logiki, a następnie wybierz pozycję **Zastosuj**. Na przykład:

   ![Wybierz pozycję "Zarządzanie kontem"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Na podstawie tego, czy w przypadku wyszukiwania według **grup zasobów** lub **typów zasobów**, wykonaj następujące kroki:

   * **Grupy zasobów**: w ramach Twojej subskrypcji platformy Azure, Eksplorator chmury zawiera wszystkich grup zasobów, które są skojarzone z jego subskrypcją. 
   Rozwiń grupę zasobów, która zawiera aplikację logiki, a następnie wybierz aplikację logiki.

   * **Typy zasobów**: w ramach Twojej subskrypcji platformy Azure, rozwiń węzeł **Logic Apps**. Po Eksplorator chmury zawiera wszystkie aplikacje wdrożone logiki, które są skojarzone z subskrypcją, wybierz aplikację logiki.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otwórz w programie Visual Studio

W programie Visual Studio możesz otworzyć aplikacje logiki wcześniej utworzyć i wdrożyć bezpośrednio za pomocą portalu Azure lub jako projektów usługi Azure Resource Manager z programem Visual Studio.

1. Otwórz Eksplorator chmury i Znajdź aplikację logiki. 

2. Menu skrótów aplikacji logiki, wybierz **otwarty przy użyciu edytora aplikacji logiki**.

   Ten przykład przedstawia aplikacje logiki według typów zasobów, aby aplikacje logiki były wyświetlane w obszarze **Logic Apps** sekcji.

  ![Otwórz logiki wdrożonej aplikacji z portalu Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otwarciu aplikacji logiki w Projektancie aplikacji logiki, w dolnej części projektanta, można wybrać **widoku kodu** tak, aby przejrzeć wewnętrzna struktura definicji aplikacji logiki. 
   Dowiedz się, jeśli chcesz utworzyć szablon wdrożenia dla aplikacji logiki, [sposobu pobierania szablonu usługi Azure Resource Manager](#download-logic-app) dla danej aplikacji logiki. Dowiedz się więcej o [szablonów Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Pobierz z platformy Azure

Możesz pobrać aplikacje logiki z <a href="https://portal.azure.com" target="_blank">portalu Azure</a> i zapisać je jako [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) szablonów. Można lokalnie Edytuj szablony w programie Visual Studio i dostosowywanie aplikacji logiki do wdrożenia w różnych środowiskach. Pobieranie aplikacji logiki automatycznie *parameterizes* ich definicje wewnątrz [szablonów Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), które także używają JavaScript Object Notation (JSON).

1. W programie Visual Studio Otwórz Eksplorator chmury, a następnie znajdź i wybierz aplikację logiki, która ma zostać pobrana z platformy Azure.

2. Menu skrótów danej aplikacji, wybierz **otwarty przy użyciu edytora aplikacji logiki**.

   Projektant aplikacji logiki otwiera i pokazuje aplikacji logiki. 
   Aby przejrzeć podstawowej definicji aplikacji logiki i struktury w dolnej części projektanta, wybierz **widoku kodu**. 

3. Na pasku narzędzi projektanta, wybierz **Pobierz**.

   ![Wybierz opcję "Pobierz"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Gdy zostanie wyświetlony monit o lokalizację, przejdź do tej lokalizacji i zapisać szablon usługi Resource Manager definicję aplikacji logiki w formacie JSON (JSON). 

Definicja aplikacji logiki pojawia się w `resources` podsekcji wewnątrz szablonu usługi Resource Manager. Możesz edytować definicję aplikacji logiki i szablonu usługi Resource Manager z programem Visual Studio. Szablon jako projekt usługi Azure Resource Manager można również dodać do rozwiązania Visual Studio. Dowiedz się więcej o [projektów Menedżera zasobów dla aplikacji logiki w Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Odśwież z platformy Azure

Jeśli edytować aplikację logiki w portalu Azure i chcesz zachować te zmiany, upewnij się, że możesz odświeżyć wersji tej aplikacji w programie Visual Studio z tymi zmianami. 

* W programie Visual Studio na pasku narzędzi Projektanta aplikacji logiki, wybierz **Odśwież**.

  — lub —

* W Eksploratorze chmury programu Visual Studio, Otwórz aplikację logiki menu skrótów i wybierz **Odśwież**. 

![Odśwież aplikację logiki z aktualizacjami](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikowanie aktualizacji aplikacji logiki

Gdy wszystko jest gotowe do wdrożenia logiki aplikacji aktualizacje z programu Visual Studio na platformie Azure, na pasku narzędzi Projektanta aplikacji logiki, wybierz pozycję **publikowania**.

![Publikowanie aplikacji logiki zaktualizowane](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ręcznie uruchom aplikację logiki

Można ręcznie uruchomić aplikację logiki, wdrożona na platformie Azure w programie Visual Studio. Na pasku narzędzi Projektanta aplikacji logiki, wybierz **uruchomić wyzwalacz**.

![Ręcznie uruchom aplikację logiki](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Aby sprawdzić stan i diagnozowanie problemów z uruchamia aplikację logiki, można przejrzeć szczegółowe informacje, takie jak wejściami i wyjściami tych działa w programie Visual Studio.

1. W Eksploratorze chmury Otwórz menu skrótów aplikację logiki, a następnie wybierz **Historia uruchomień Otwórz**.

   ![Otwórz Historia uruchomień](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Aby wyświetlić szczegóły dla określonego przebiegu, kliknij dwukrotnie opcję Uruchom. Na przykład:

   ![Szczegółowej historii wykonywania](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Do sortowania tabeli za pomocą właściwości, wybierz nagłówek kolumny dla tej właściwości. 

3. Rozwiń węzeł kroków, których wejściach i wyjściach, aby przejrzeć. Na przykład:

   ![Wyświetlanie danych wejściowych i wyjściowych dla każdego kroku](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Bez usuwania aplikację logiki, można wyłączyć wyzwalacza z wyzwalania następnym razem po spełnieniu warunku wyzwalania. Wyłączenie aplikacji logiki uniemożliwia tworzenie i uruchamianie wystąpień przyszłych przepływu pracy aplikacji logiki aparatu Logic Apps.
W Eksploratorze chmury Otwórz menu skrótów aplikację logiki, a następnie wybierz **wyłączyć**.

![Wyłącz aplikację logiki](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Jeśli wszystko jest gotowe do aplikację logiki, aby wznowić działanie, mogą ponownie aktywować aplikację logiki. W Eksploratorze chmury Otwórz menu skrótów aplikację logiki, a następnie wybierz **włączyć**.

![Umożliwianie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Usuwanie aplikacji logiki

Aby usunąć aplikację logiki w portalu Azure, w Eksploratorze chmury Otwórz menu skrótów aplikację logiki, a następnie wybierz **usunąć**.

![Usuwanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób zarządzania logiki wdrożonej aplikacji za pomocą programu Visual Studio. Następnie Dowiedz się więcej o dostosowywaniu definicjami aplikacji logiki do wdrożenia:

> [!div class="nextstepaction"]
> [Autor definicjami aplikacji logiki w formacie JSON](../logic-apps/logic-apps-author-definitions.md)
