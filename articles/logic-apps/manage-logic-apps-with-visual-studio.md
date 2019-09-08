---
title: Zarządzanie aplikacjami logiki przy użyciu programu Visual Studio — Azure Logic Apps
description: Zarządzanie aplikacjami logiki i innymi zasobami platformy Azure za pomocą programu Visual Studio Cloud Explorer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: dd6cd16302c69266a954816868c04c8507762717
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801271"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Zarządzanie aplikacjami logiki za pomocą programu Visual Studio

Chociaż można tworzyć, edytować i wdrażać aplikacje logiki w [Azure Portal](https://portal.azure.com), można także użyć programu Visual Studio, jeśli chcesz dodać aplikacje logiki do kontroli źródła, opublikować różne wersje i utworzyć szablony [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) dla różne środowiska wdrażania. Za pomocą programu Visual Studio Cloud Explorer można znajdować aplikacje logiki i zarządzać nimi wraz z innymi zasobami platformy Azure. Można na przykład otwierać, pobierać, edytować, uruchamiać, wyświetlać historię uruchamiania, wyłączać i włączać Aplikacje logiki, które są już wdrożone w Azure Portal. Jeśli dopiero zaczynasz pracę z Azure Logic Apps w programie Visual Studio, Dowiedz się, [jak tworzyć aplikacje logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Wdrożenie lub opublikowanie aplikacji logiki z programu Visual Studio zastępuje wersję tej aplikacji w Azure Portal. Dlatego jeśli wprowadzisz zmiany w Azure Portal, które chcesz zachować, upewnij się, że [aplikacja logiki została odświeżona w programie Visual Studio](#refresh) z Azure Portal przed następnym wdrożeniem lub opublikowaniem w programie Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * [Visual Studio 2019, 2017 lub 2015 — wersja Community lub nowsza](https://aka.ms/download-visual-studio). 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

    > [!IMPORTANT]
    > Po zainstalowaniu programu Visual Studio 2019 lub 2017 upewnij się, że wybrano obciążenie **Programowanie na platformie Azure** .
    > Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w programie Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Aby zainstalować program Cloud Explorer dla programu Visual Studio 2015, [Pobierz program Cloud Explorer z Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami skojarzonymi z kontami platformy Azure w programie Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Zestaw Azure SDK (2.9.1 lub nowszy)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps narzędzia dla wersji programu Visual Studio, którą chcesz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, [jak zainstalować to rozszerzenie z poziomu programu Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Dostęp do sieci Web przy użyciu osadzonego projektanta Logic Apps

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Znajdowanie aplikacji logiki

W programie Visual Studio można znaleźć wszystkie aplikacje logiki skojarzone z subskrypcją platformy Azure i wdrożone w Azure Portal przy użyciu programu Cloud Explorer.

1. Otwórz program Visual Studio. W menu **Widok** wybierz opcję **Eksplorator chmury**.

1. W programie Cloud Explorer wybierz pozycję **Zarządzanie kontem**. Wybierz subskrypcję platformy Azure skojarzoną z aplikacjami logiki, a następnie wybierz pozycję **Zastosuj**. Na przykład:

   ![Wybierz pozycję "Zarządzanie kontami"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. W zależności od tego, czy wyszukujesz według **grup zasobów** czy **typów zasobów**, wykonaj następujące kroki:

   * **Grupy zasobów**: W ramach subskrypcji platformy Azure w Eksploratorze chmury są wyświetlane wszystkie grupy zasobów skojarzone z daną subskrypcją. 
   Rozwiń grupę zasobów zawierającą aplikację logiki, a następnie wybierz aplikację logiki.

   * **Typy zasobów**: W ramach subskrypcji platformy Azure rozwiń węzeł **Logic Apps**. Po wyświetleniu wszystkich wdrożonych aplikacji logiki skojarzonych z subskrypcją w programie Cloud Explorer wybierz aplikację logiki.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Otwórz w programie Visual Studio

W programie Visual Studio można otwierać aplikacje logiki wcześniej utworzone i wdrożone bezpośrednio za pomocą Azure Portal lub projektów grupy zasobów platformy Azure z programem Visual Studio.

1. Otwórz program Cloud Explorer i Znajdź aplikację logiki. 

1. W menu skrótów aplikacji logiki wybierz pozycję **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Ten przykład pokazuje Aplikacje logiki według typu zasobu, dzięki czemu Aplikacje logiki są wyświetlane w sekcji **Logic Apps** .

   ![Otwórz wdrożoną aplikację logiki z Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otwarciu aplikacji logiki w programie Logic Apps Designer u dołu projektanta można wybrać **Widok kod** , aby można było przejrzeć podstawową strukturę definicji aplikacji logiki. 
   Jeśli chcesz utworzyć szablon wdrożenia dla aplikacji logiki, Dowiedz się, [jak pobrać szablon Azure Resource Manager](#download-logic-app) dla tej aplikacji logiki. Dowiedz się więcej o [szablonach Menedżer zasobów](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Pobierz z platformy Azure

Aplikacje logiki można pobrać z [Azure Portal](https://portal.azure.com) i zapisać je jako szablony [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) . Następnie można edytować szablony lokalnie za pomocą programu Visual Studio i dostosowywać Aplikacje logiki do różnych środowisk wdrażania. Pobieranie aplikacji logiki automatycznie *parameterizes* ich definicje w [szablonach Menedżer zasobów](../azure-resource-manager/template-deployment-overview.md), które również używają JavaScript Object Notation (JSON).

1. W programie Visual Studio Otwórz program Cloud Explorer, a następnie Znajdź i wybierz aplikację logiki, którą chcesz pobrać z platformy Azure.

2. W menu skrótów tej aplikacji wybierz pozycję **Otwórz za pomocą edytora aplikacji logiki**.

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

   Zostanie otwarty projektant aplikacji logiki i zostanie wyświetlona aplikacja logiki. 
   Aby przejrzeć podstawową definicję i strukturę aplikacji logiki, w dolnej części projektanta wybierz **Widok kod**. 

3. Na pasku narzędzi projektanta wybierz pozycję **Pobierz**.

   ![Wybierz pozycję "Pobierz"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Po wyświetleniu monitu o lokalizację przejdź do tej lokalizacji i Zapisz szablon Menedżer zasobów dla definicji aplikacji logiki w formacie pliku JSON (JSON). 

Definicja aplikacji logiki zostanie wyświetlona `resources` w podsekcji wewnątrz szablonu Menedżer zasobów. Teraz można edytować definicję aplikacji logiki i szablon Menedżer zasobów za pomocą programu Visual Studio. Możesz również dodać szablon jako [projekt grupy zasobów platformy Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do rozwiązania programu Visual Studio. Dowiedz się więcej o [projektach grup zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Link do konta integracji

Aby tworzyć aplikacje logiki dla scenariuszy integracji z przedsiębiorstwem B2B (Business-to-Business), możesz połączyć aplikację logiki z wcześniej utworzonym [kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , które istnieje w tym samym regionie, w którym znajduje się aplikacja logiki. Konto integracji zawiera artefakty B2B, takie jak partnerzy handlowi, umowy, schematy i mapy, a także umożliwia aplikacji logiki używanie łączników B2B do sprawdzania poprawności kodu XML i prostego kodowania plików lub dekodowania. Chociaż można [utworzyć ten link za pomocą Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), można również użyć programu Visual Studio po spełnieniu [wymagań wstępnych](#requirements), a aplikacja LOGIKI istnieje jako plik JSON (JSON) w [projekcie grupy zasobów platformy Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Dowiedz się więcej o [projektach grup zasobów platformy Azure dla aplikacji logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. W programie Visual Studio Otwórz projekt grupy zasobów platformy Azure, który zawiera aplikację logiki.

1. W Eksplorator rozwiązań otwórz menu skrótów **< logiki-App-name >. JSON** , a następnie wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**. Klawiatury Ctrl+L)

   ![Otwórz plik JSON aplikacji logiki przy użyciu projektanta aplikacji logiki](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

1. Aby upewnić się, że projektant aplikacji logiki ma fokus, wybierz kartę lub powierzchnię projektanta, aby okienko właściwości pokazywało Właściwość **konta integracji** dla aplikacji logiki.

   ![W okienku właściwości jest wyświetlana właściwość "konto integracji"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties.png)

1. Otwórz listę **konto integracji** i wybierz konto integracji, które chcesz połączyć z aplikacją logiki, na przykład:

   ![Otwórz listę właściwości "konto integracji"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Gdy skończysz, pamiętaj, aby zapisać rozwiązanie programu Visual Studio.

Po ustawieniu właściwości **konto integracji** w programie Visual Studio i zapisaniu aplikacji logiki jako szablonu Azure Resource Manager ten szablon zawiera również deklarację parametru dla wybranego konta integracji. Aby uzyskać więcej informacji na temat parametrów szablonu i usługi Logic [Apps, zobacz Omówienie: Automatyzowanie wdrażania](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)aplikacji logiki.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Odświeżanie z platformy Azure

Jeśli edytujesz aplikację logiki w Azure Portal i chcesz zachować te zmiany, pamiętaj, aby odświeżyć wersję tej aplikacji w programie Visual Studio przy użyciu tych zmian. 

* W programie Visual Studio na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Odśwież**.

  —lub—

* W programie Visual Studio Cloud Explorer otwórz menu skrótów aplikacji logiki i wybierz polecenie **Odśwież**.

![Odśwież aplikację logiki przy użyciu aktualizacji](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publikowanie aktualizacji aplikacji logiki

Gdy wszystko będzie gotowe do wdrożenia aktualizacji aplikacji logiki z programu Visual Studio na platformie Azure, na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Publikuj**.

![Opublikuj zaktualizowaną aplikację logiki](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ręczne uruchamianie aplikacji logiki

Możesz ręcznie wyzwolić aplikację logiki wdrożoną na platformie Azure z programu Visual Studio. Na pasku narzędzi projektanta aplikacji logiki wybierz **Uruchom wyzwalacz**.

![Ręcznie uruchom aplikację logiki](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Przeglądać historię uruchamiania

Aby sprawdzić stan i zdiagnozować problemy z uruchomieniami aplikacji logiki, można przejrzeć szczegóły, takie jak dane wejściowe i wyjściowe, dla tych przebiegów w programie Visual Studio.

1. W programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Otwórz historię uruchamiania**.

   ![Otwórz historię uruchamiania](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Aby wyświetlić szczegóły określonego uruchomienia, kliknij dwukrotnie przebieg. Na przykład:

   ![Szczegółowa historia przebiegów](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Aby posortować tabelę według właściwości, wybierz nagłówek kolumny dla tej właściwości. 

1. Rozwiń kroki, których dane wejściowe i wyjściowe chcesz przejrzeć. Na przykład:

   ![Wyświetl dane wejściowe i wyjściowe dla każdego kroku](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Bez usuwania aplikacji logiki można zatrzymać wywoływanie wyzwalacza przy następnym uruchomieniu warunku wyzwalacza. Wyłączenie aplikacji logiki uniemożliwia aparatowi Logic Apps tworzenie i uruchamianie przyszłych wystąpień przepływu pracy dla aplikacji logiki.
W programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Wyłącz**.

![Wyłączanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Po wyłączeniu aplikacji logiki nie są tworzone żadne nowe uruchomienia. Wszystkie w toku i oczekujące przebiegi będą kontynuowane do czasu zakończenia, co może zająć trochę czasu. 

Gdy wszystko będzie gotowe do wznowienia działania aplikacji logiki, możesz ponownie aktywować aplikację logiki. W programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Włącz**.

![Włączanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Usuwanie aplikacji logiki

Aby usunąć aplikację logiki z Azure Portal, w programie Cloud Explorer otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Usuń**.

![Usuwanie aplikacji logiki](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po otwarciu projektu aplikacji logiki w projektancie Logic Apps można nie uzyskać opcji wyboru subskrypcji platformy Azure. Zamiast tego aplikacja logiki zostanie otwarta z subskrypcją platformy Azure, która nie jest tą, której chcesz użyć. Takie zachowanie występuje, ponieważ po otwarciu pliku JSON aplikacji logiki program Visual Studio buforuje pierwszą wybraną subskrypcję do użytku w przyszłości. Aby rozwiązać ten problem, spróbuj wykonać jedną z następujących czynności:

* Zmień nazwę pliku JSON aplikacji logiki. Pamięć podręczna subskrypcji zależy od nazwy pliku.

* Aby usunąć wcześniej wybrane subskrypcje dla *wszystkich* aplikacji logiki w rozwiązaniu, Usuń ukryty folder ustawień programu Visual Studio (. vs) w katalogu rozwiązania. Ta lokalizacja przechowuje informacje o subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zarządzania wdrożonymi aplikacjami logiki w programie Visual Studio. Następnie Dowiedz się więcej na temat dostosowywania definicji aplikacji logiki na potrzeby wdrożenia:

> [!div class="nextstepaction"]
> [Tworzenie definicji aplikacji logiki w formacie JSON](../logic-apps/logic-apps-author-definitions.md)
