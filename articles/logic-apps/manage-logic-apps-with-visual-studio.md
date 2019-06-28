---
title: Zarządzanie aplikacjami logiki w programie Visual Studio — Azure Logic Apps
description: Zarządzanie aplikacjami logiki i innych zasobów platformy Azure za pomocą Eksploratora chmury w usłudze Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: ecf7f11074b447e9f093e4fd6677a3d09d213b1d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295831"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Zarządzanie aplikacjami logiki w programie Visual Studio

Mimo że można tworzyć, edytować, zarządzanie i wdrażanie aplikacji logiki w [witryny Azure portal](https://portal.azure.com), można również używać Visual Studio, gdy chcesz dodać do kontroli źródła, publikowanie różnych wersji i tworzenia aplikacji logiki [platformy Azure Menedżer zasobów](../azure-resource-manager/resource-group-overview.md) szablonów dla różnych środowisk wdrożeń. Za pomocą Eksploratora chmury programu Visual Studio można znaleźć i zarządzanie aplikacjami logiki w taki sposób, wraz z innymi zasobami platformy Azure. Możesz na przykład, Otwórz, pobieranie, Edytuj, także uruchomić, wyświetlać historię uruchomień, wyłącz i Włącz aplikacje logiki, które są już wdrożone w witrynie Azure portal. Dowiedz się, jeśli dopiero zaczynasz pracę z usługą Azure Logic Apps w programie Visual Studio [sposób tworzenia aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Wdrażanie i publikowanie aplikacji logiki w programie Visual Studio zastępuje wersję tej aplikacji w witrynie Azure portal. Dlatego jeśli wprowadzisz zmiany w witrynie Azure portal, które chcesz zachować, upewnij się, że możesz [Odśwież aplikację logiki w programie Visual Studio](#refresh) w witrynie Azure portal przed następnym wdrażania lub opublikować z programu Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * [Program Visual Studio 2019 r, 2017 lub 2015 — Community edition lub nowszej](https://aka.ms/download-visual-studio). 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017, upewnij się, że wybrano **programowanie na platformie Azure** obciążenia.
    > Aby uzyskać więcej informacji, zobacz [Zarządzanie zasoby skojarzone z kontami platformy Azure w programie Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Do zainstalowania programu Cloud Explorer programu Visual Studio 2015, [Pobierz Eksplorator chmury z witryny Marketplace programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Aby uzyskać więcej informacji, zobacz [Zarządzanie zasoby skojarzone z kontami platformy Azure w Eksploratorze chmury w usłudze Visual Studio (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Zestaw Azure SDK (2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Narzędzia systemu Azure aplikacje logiki dla wersji programu Visual Studio, które mają:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do Internetu podczas korzystania z osadzonego projektanta aplikacji logiki

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Znajdź aplikacje logiki

W programie Visual Studio można znaleźć wszystkie aplikacje logiki, które są skojarzone z subskrypcją platformy Azure i są wdrażane w witrynie Azure portal za pomocą Eksploratora chmury.

1. Otwórz program Visual Studio. Na **widoku** menu, wybierz opcję **programu Cloud Explorer**.

1. W programie Cloud Explorer, wybierz **zarządzania kontami**. Wybierz subskrypcję platformy Azure skojarzone z aplikacjami logiki, a następnie wybierz **Zastosuj**. Na przykład:

   ![Wybierz pozycję "Zarządzanie kontem"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Oparte na tego, czy przeszukujesz przez **grup zasobów** lub **typów zasobów**, wykonaj następujące kroki:

   * **Grupy zasobów**: W ramach Twojej subskrypcji platformy Azure programu Cloud Explorer pokazuje wszystkie grupy zasobów, które są skojarzone z tą subskrypcją. 
   Rozwiń grupę zasobów, który zawiera aplikację logiki, a następnie wybierz swoją aplikację logiki.

   * **Typy zasobów**: W ramach Twojej subskrypcji platformy Azure, rozwiń węzeł **Logic Apps**. Po programie Cloud Explorer pokazuje wszystkie aplikacje wdrożone logiki, które są skojarzone z Twoją subskrypcją, wybierz swoją aplikację logiki.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otwórz w programie Visual Studio

W programie Visual Studio możesz otworzyć aplikacji logiki wcześniej utworzyć i wdrożyć bezpośrednio w witrynie Azure portal lub jako projektów usługi Azure Resource Manager za pomocą programu Visual Studio.

1. Otwórz Eksploratora chmury i Znajdź swoją aplikację logiki. 

1. W menu skrótów dla aplikacji logiki, wybierz **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019 r, sprawdź, czy najnowsze aktualizacje dla programu Visual Studio.

   W tym przykładzie aplikacji logiki według typów zasobów, dzięki czemu aplikacje logiki są wyświetlane w obszarze **Logic Apps** sekcji.

   ![Otwórz logiki wdrożonej aplikacji z witryny Azure portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otwarciu aplikacji logiki, w Projektancie aplikacji logiki, w dolnej części projektanta, możesz wybrać **widok kodu** tak, aby przejrzeć wewnętrzna struktura definicji aplikacji logiki. 
   Dowiedz się, jeśli chcesz utworzyć szablon wdrożenia dla aplikacji logiki [sposobu pobierania szablonu usługi Azure Resource Manager](#download-logic-app) dla tej aplikacji logiki. Dowiedz się więcej o [szablonów usługi Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Pobierz z platformy Azure

Można pobrać aplikacji logiki z [witryny Azure portal](https://portal.azure.com) i zapisz je jako [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) szablonów. Można następnie lokalnie edytować szablony z programem Visual Studio i dostosowywanie aplikacji logiki dla różnych środowisk wdrożeń. Automatyczne pobieranie aplikacji logiki *parametryzuje dane* ich definicje wewnątrz [szablonów usługi Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), które również użyć JavaScript Object Notation (JSON).

1. W programie Visual Studio Otwórz Eksploratora chmury, a następnie znajdź i wybierz aplikację logiki, która mają być pobierane z platformy Azure.

2. W menu skrótów dla tej aplikacji, wybierz **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019 r, sprawdź, czy najnowsze aktualizacje dla programu Visual Studio.

   Projektant aplikacji logiki otworzy i przedstawiono aplikację logiki. 
   Aby zapoznać się z podstawową definicję i struktury w dolnej części projektanta aplikacji logiki wybierz pozycję **widok kodu**. 

3. Na pasku narzędzi Projektanta wybierz **Pobierz**.

   ![Wybierz opcję "Pobierz"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Po wyświetleniu monitu o lokalizację, przejdź do tej lokalizacji i zapisać szablon usługi Resource Manager definicji aplikacji logiki w formacie JSON (plik JSON). 

Definicji aplikacji logiki, który pojawia się w `resources` podsekcji wewnątrz szablonu usługi Resource Manager. Teraz możesz edytować definicji aplikacji logiki i szablon usługi Resource Manager z programem Visual Studio. Można również dodać szablonu jako projektu usługi Azure Resource Manager, do rozwiązania programu Visual Studio. Dowiedz się więcej o [usługi Resource Manager projekty dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Odśwież na platformie Azure

Jeśli poddasz edycji aplikację logiki w witrynie Azure portal i chcesz zachować te zmiany, upewnij się, że możesz odświeżyć wersji tej aplikacji w programie Visual Studio z tymi zmianami. 

* W programie Visual Studio, na pasku narzędzi Projektanta aplikacji logiki wybierz **Odśwież**.

  —lub—

* W Eksploratorze chmury w usłudze Visual Studio, otwórz menu skrótów aplikację logiki, a następnie wybierz **Odśwież**.

![Odśwież aplikację logiki, za pomocą aktualizacji](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikowanie aktualizacji aplikacji logiki

Gdy wszystko będzie gotowe do wdrożenia usługi Logic Apps aktualizacje aplikacji z programu Visual Studio na platformie Azure, na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Publikuj**.

![Publikowanie aplikacji logiki zaktualizowane](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ręcznie uruchomić swoją aplikację logiki

Można ręcznie wyzwalać aplikacji logiki wdrożonych na platformie Azure z programu Visual Studio. Na pasku narzędzi Projektanta aplikacji logiki wybierz **uruchomienia wyzwalacza**.

![Ręcznie uruchomić aplikację logiki](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Aby sprawdzić stan i diagnozowania problemów z uruchomienia aplikacji logiki, możesz przejrzeć szczegółowe informacje, takie jak dane wejściowe i wyjściowe dla tych działa w programie Visual Studio.

1. W programie Cloud Explorer, otwórz menu skrótów aplikację logiki, a następnie wybierz **historii uruchamiania Open**.

   ![Otwórz historii uruchamiania](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Aby wyświetlić szczegóły dotyczące określonego uruchomienia, kliknij dwukrotnie przycisk Uruchom. Na przykład:

   ![Szczegółowa historia uruchamiania](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Aby posortować tabelę według właściwości, wybierz nagłówek kolumny, dla tej właściwości. 

1. Rozwiń kroki, których dane wejściowe i wyjściowe, o których chcesz przejrzeć. Na przykład:

   ![Wyświetl dane wejściowe i wyjściowe dla każdego kroku](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Bez usuwania aplikacji logiki, można wyłączyć wyzwalacza z wyzwalania następnym razem, gdy jest spełniony warunek wyzwalacza. Wyłączanie aplikacji logiki zapobiega aparat usługi Logic Apps, tworzenie i uruchamianie wystąpienia przyszłych przepływu pracy aplikacji logiki.
W programie Cloud Explorer, otwórz menu skrótów aplikację logiki, a następnie wybierz **wyłączyć**.

![Wyłączanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Po wyłączeniu aplikacji logiki są tworzone nie nowe przebiegi. Wszystkie w toku i oczekujących uruchomień będzie kontynuowane, dopóki kończą się, co może zająć czas trwania. 

Gdy wszystko będzie gotowe wznowić działanie aplikacji logiki, można ponownie aktywować swoją aplikację logiki. W programie Cloud Explorer, otwórz menu skrótów aplikację logiki, a następnie wybierz **Włącz**.

![Włączanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Usuwanie aplikacji logiki

Aby usunąć aplikację logiki w witrynie Azure portal, w programie Cloud Explorer, otwórz menu skrótów aplikację logiki i wybierz **Usuń**.

![Usuwanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po otwarciu projektu aplikacji logiki w Projektancie aplikacji logiki możesz nie otrzymać opcję wybierania subskrypcji platformy Azure. Zamiast tego aplikacja logiki zostanie otwarty z subskrypcją platformy Azure, który nie jest ten, którego chcesz użyć. To zachowanie ma miejsce, ponieważ po otwarciu pliku JSON aplikacji logiki, Visual Studio zapisuje w pamięci podręcznej pierwszy w wybranej subskrypcji do użytku w przyszłości. Aby rozwiązać ten problem, spróbuj wykonać jedną z następujących czynności:

* Zmień nazwę pliku JSON aplikacji logiki. Pamięć podręczna subskrypcji zależy od nazwy pliku.

* Aby usunąć wcześniej wybrane subskrypcje dla *wszystkich* logic apps w rozwiązaniu, Usuń ukryty folder programu Visual Studio ustawień (.vs) w katalogu tego rozwiązania. Ta lokalizacja przechowuje informacje o subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób Zarządzanie aplikacjami logiki wdrożone za pomocą programu Visual Studio. Następnie Dowiedz się więcej o dostosowywaniu definicji aplikacji logiki dla wdrożenia:

> [!div class="nextstepaction"]
> [Tworzenie definicji aplikacji logiki w formacie JSON](../logic-apps/logic-apps-author-definitions.md)
