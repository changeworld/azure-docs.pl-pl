---
title: Tworzenie aplikacji logiki, które automatyzują przepływy pracy z programem Visual Studio — usługa Azure Logic Apps | Microsoft Docs
description: W tym przewodniku Szybki start przedstawiono automatyzację zadań, procesów i przepływów pracy z usługą Azure Logic Apps w programie Visual Studio
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/15/2018
ms.topic: quickstart
ms.custom: mvc
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: a35c46bd67a157416844c4054b75f993304298b4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300756"
---
# <a name="quickstart-create-and-automate-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Szybki start: tworzenie i automatyzacja zadań, procesów i przepływów pracy z usługą Azure Logic Apps — w programie Visual Studio

Za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i programu Visual Studio można utworzyć przepływy pracy do automatyzacji zadań i procesów, które integrują aplikacje, dane, systemy i usługi w przedsiębiorstwach i organizacjach. W tym przewodniku Szybki start przedstawiono sposób projektowania i kompilowania tych przepływów pracy przez tworzenie aplikacji logiki w programie Visual Studio i wdrażanie ich na platformie <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> w chmurze. I chociaż można wykonać te zadania w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, program Visual Studio umożliwia dodawanie aplikacji logiki do kontroli źródła, publikowanie różnych wersji i tworzenie szablonów usługi Azure Resource Manager dla różnych środowisk wdrożeń. 

Jeśli jesteś nowym użytkownikiem usługi Azure Logic Apps i potrzebujesz dostępu do podstawowych pojęć, wypróbuj w zamian [przewodnik Szybki Start dotyczący tworzenia aplikacji logiki w witrynie Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Projektant aplikacji logiki w witrynie Azure Portal i programie Visual Studio działa podobnie. 

W tym miejscu tworzysz tę samą aplikację logiki, co w przewodniku Szybki start dla witryny Azure Portal, ale przy użyciu programu Visual Studio. Ta aplikacja logiki monitoruje kanał informacyjny RSS witryny internetowej i wysyła wiadomość e-mail dla każdego nowego elementu opublikowanego w witrynie. Po ukończeniu aplikacja logiki będzie wyglądać jak ten ogólny przepływ pracy:

![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 lub Visual Studio 2015 — Community Edition lub nowszy</a>. 
  Ten przewodnik Szybki start używa bezpłatnego Visual Studio Community 2017.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Zestaw Azure SDK (2.9.1 lub nowszy)</a> i program <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * Narzędzia <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Azure Logic Apps Tools for Visual Studio 2017</a> lub program <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a>
  
    Możesz pobrać i zainstalować narzędzia Azure Logic Apps Tools bezpośrednio z witryny Visual Studio Marketplace lub dowiedzieć się, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">jak zainstalować to rozszerzenie z poziomu programu Visual Studio</a>. 
    Upewnij się, że po zakończeniu instalacji program Visual Studio zostanie ponownie uruchomiony.

* Konto e-mail obsługiwane przez usługę Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców <a href="https://docs.microsoft.com/connectors/" target="_blank">przejrzyj tę listę łączników</a>. Ta aplikacja logiki korzysta z programu Office 365 Outlook. Jeśli używasz innego dostawcy, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

* Dostęp do Internetu podczas korzystania z osadzonego Projektanta aplikacji logiki

  Projektant wymaga połączenia internetowego do tworzenia zasobów na platformie Azure oraz odczytywania właściwości i danych z łączników w aplikacji logiki. 
  Jeśli na przykład używany jest łącznik usługi Dynamics CRM Online, projektant wyszukuje dostępne właściwości domyślne i niestandardowe w wystąpieniu CRM.

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

Aby rozpocząć, utwórz [projekt grupy zasobów platformy Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Dowiedz się więcej o [zasobach i grupach zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).

1. Uruchom program Visual Studio i zaloguj się przy użyciu konta platformy Azure.

2. W menu **Plik** wybierz pozycję **Nowy** > **Projekt**. (Klawiatura: Ctrl+Shift+N)

   ![W menu „File” wybierz pozycję „Nowy” > „Projekt”](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

3. W obszarze **Zainstalowane** wybierz pozycję **Visual C#** lub **Visual Basic**. Wybierz pozycję **Chmura** > **Grupa zasobów platformy Azure**. Nazwij projekt, na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

4. Wybierz szablon **Aplikacja logiki**. 

   ![Wybieranie szablonu aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Po utworzeniu projektu w programie Visual Studio Eksplorator rozwiązań otwiera i pokazuje rozwiązanie. 

   ![Eksplorator rozwiązań pokazuje nowe rozwiązanie aplikacji logiki i pliku wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

   W rozwiązaniu plik **LogicApp.json** nie tylko przechowuje dane definicji dla aplikacji logiki, ale jest również szablonem usługi Azure Resource Manager, który można skonfigurować na potrzeby wdrożenia.

## <a name="create-blank-logic-app"></a>Tworzenie pustej aplikacji logiki

Po utworzeniu projektu grupy zasobów platformy Azure utwórz i skompiluj aplikację logiki, zaczynając od szablonu **pustej aplikacji logiki**.

1. W Eksploratorze rozwiązań otwórz menu skrótów pliku **LogicApp.json**. Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**. (Klawiatura: Ctrl+L)

   ![Otwieranie pliku JSON aplikacji logiki za pomocą Projektanta aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

2. W polu **Subskrypcja** wybierz subskrypcję platformy Azure, która ma być używana. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową...** w celu utworzenia nowej grupy zasobów platformy Azure. 

   ![Wybieranie subskrypcji platformy Azure, grupy zasobów i lokalizacji zasobu](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   Program Visual Studio potrzebuje subskrypcji i grupy zasobów platformy Azure do tworzenia i wdrażania zasobów skojarzonych z aplikacją logiki i połączeniami. 

   | Ustawienie | Przykładowa wartość | Opis | 
   | ------- | ------------- | ----------- | 
   | Lista profilów użytkowników | Contoso <br> jamalhartnett@contoso.com | Domyślnie konto używane do logowania | 
   | **Subskrypcja** | Płatność zgodnie z rzeczywistym użyciem <br> (jamalhartnett@contoso.com) | Nazwa Twojej subskrypcji platformy Azure i skojarzone konto |
   | **Grupa zasobów** | MyLogicApp-RG <br> (Zachodnie stany USA) | Grupa zasobów i lokalizacja platformy Azure na potrzeby przechowywania i wdrażania zasobów aplikacji logiki | 
   | **Lokalizacja** | MyLogicApp-RG2 <br> (Zachodnie stany USA) | Inna lokalizacja, jeśli nie chcesz używać lokalizacji grupy zasobów |
   ||||

3. Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. Przewiń poza wideo i wyzwalacze. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pozycji „Pusta aplikacja logiki”](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Kompilowanie przepływu pracy aplikacji logiki

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie aktywowany po pojawieniu się nowego elementu w kanale informacyjnym RSS. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza uruchamianego po spełnieniu określonych kryteriów spełnione. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej przepływ pracy.

1. W projektancie aplikacji logiki wprowadź ciąg „rss” w polu wyszukiwania. Wybierz następujący wyzwalacz: **RSS — Gdy element kanału informacyjnego jest publikowany**

   ![Kompilowanie aplikacji logiki przez dodanie wyzwalacza i akcji](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

   Wyzwalacz jest teraz wyświetlany w projektancie:

   ![Wyzwalacz RSS jest wyświetlany w projektancie aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/rss-trigger-logic-app.png)

2. Aby zakończyć kompilowanie aplikacji logiki, wykonaj kroki dotyczące przepływu pracy z [przewodnika Szybki start dla witryny Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), a następnie wróć do tego artykułu.

   Gdy wszystko będzie gotowe, aplikacja logiki będzie wyglądać następująco: 

   ![Ukończona aplikacja logiki](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

3. Aby zapisać aplikację logiki, zapisz rozwiązanie programu Visual Studio. (Klawiatura: Ctrl+S)

Teraz przed przetestowaniem aplikacji logiki wdróż aplikację na platformie Azure.

## <a name="deploy-logic-app-to-azure"></a>Wdrażanie aplikacji logiki na platformie Azure

Przed uruchomieniem aplikacji logiki wdróż aplikację z programu Visual Studio na platformie Azure. Ten proces składa się z zaledwie kilku kroków.

1. W Eksploratorze rozwiązań w menu skrótów projektu wybierz pozycję **Wdróż** > **Nowy...**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

   ![Tworzenie wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

2. W przypadku tego wdrożenia zachowaj subskrypcję platformy Azure, grupę zasobów i inne ustawienia domyślne. Gdy wszystko będzie gotowe, wybierz pozycję **Wdróż**. 

   ![Wdrażanie aplikacji logiki do grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

3. Jeśli zostanie wyświetlone okno **Edytuj parametry**, podaj nazwę zasobu aplikacji logiki do użycia we wdrożeniu, a następnie zapisz ustawienia, na przykład:

   ![Podawanie nazwy wdrożenia aplikacji logiki](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. 
   Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure.

   ![Dane wyjściowe stanu wdrożenia](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Po zakończeniu wdrożenia aplikacja logiki jest aktywna w witrynie Azure Portal i sprawdza kanał informacyjny RSS w oparciu o wybrany harmonogram (co minutę). 
   Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. 
   W przeciwnym razie aplikacja logiki będzie czekać z ponownym sprawdzeniem do następnego interwału. 

   Oto przykładowe wiadomości e-mail wysyłane przez tę aplikację logiki. 
   Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. 

   ![Program Outlook wysyła wiadomość e-mail dla każdego nowego elementu danych RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

   Z technicznego punktu widzenia, gdy wyzwalacz sprawdza źródło danych RSS i znajduje nowe elementy, jest on wyzwalany, a aparat usługi Logic Apps tworzy wystąpienie przepływu pracy aplikacji logiki, które uruchamia akcje w ramach przepływu pracy.
   Jeśli wyzwalacz nie znajdzie nowych elementów, nie zostanie wyzwolony i pominie tworzenie wystąpienia przepływu pracy.

Gratulacje, aplikacja logiki została pomyślnie skompilowana i wdrożona przy użyciu programu Visual Studio. Aby zarządzać aplikacją logiki i przeglądać historię jej uruchomień, zobacz [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów zawierająca aplikację logiki i powiązane zasoby nie będzie już potrzebna, usuń ją.

1. Zaloguj się do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a> za pomocą konta używanego do tworzenia aplikacji logiki. 

2. Z głównego menu platformy Azure wybierz pozycję **Grupy zasobów**. Wybierz grupę zasobów aplikacji logiki.

3. Wybierz pozycję **Usuń grupę zasobów**. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   ![„Grupy zasobów” > „Przegląd” > „Usuń grupę zasobów”](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

4. Usuń rozwiązanie programu Visual Studio z komputera lokalnego.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="http://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Następne kroki

W tym artykule kompilowano, wdrożono i uruchomiono aplikację logiki przy użyciu programu Visual Studio. Aby dowiedzieć się więcej na temat wykonywania zaawansowanego wdrażania aplikacji logiki i zarządzania nim przy użyciu programu Visual Studio, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> * [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
> * [Create deployment templates for logic apps with Visual Studio](../logic-apps/logic-apps-create-deploy-template.md) (Tworzenie szablonów wdrożenia dla aplikacji logiki za pomocą programu Visual Studio)
