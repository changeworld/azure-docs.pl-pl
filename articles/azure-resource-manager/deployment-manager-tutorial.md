---
title: Korzystanie z usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów | Microsoft Docs
description: Aby wdrożyć zasoby platformy Azure, użyj szablonów Menedżer zasobów w usłudze Azure Menedżer wdrażania.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 97d9aa1ed9440011fdaab3aa8eb9d3942b5a8acf
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170358"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Samouczek: korzystanie z usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów (publiczna wersja zapoznawcza)

Dowiedz się, jak wdrażać aplikacje w wielu regionach za pomocą [usługi Azure Menedżer wdrażania](./deployment-manager-overview.md) . Jeśli wolisz szybszym podejściem, [usługa Azure Menedżer wdrażania szybki start](https://github.com/Azure-Samples/adm-quickstart) tworzy wymagane konfiguracje w ramach subskrypcji i dostosowuje artefakty do wdrożenia aplikacji w wielu regionach. Przewodnik Szybki Start wykonuje te same zadania, co w tym samouczku.

Aby użyć Menedżer wdrażania, musisz utworzyć dwa szablony:

* **Szablon topologii**: opisuje zasoby platformy Azure, które tworzą aplikacje, oraz miejsce ich wdrażania.
* **Szablon wdrożenia**: zawiera opis czynności, które należy wykonać podczas wdrażania aplikacji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja jest oznaczona jako przeznaczona do testowania nowych funkcji platformy Azure, możesz użyć usługi Azure Menedżer wdrażania tylko do wdrożenia w regionach Kanaryjskich. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Zapoznaj się z scenariuszem
> * Pobierz pliki samouczka
> * Przygotuj artefakty
> * Tworzenie tożsamości zarządzanej zdefiniowanej przez użytkownika
> * Tworzenie szablonu topologii usług
> * Utwórz szablon wdrożenia
> * Wdrażanie szablonów
> * Weryfikowanie wdrożenia
> * Wdróż nowszą wersję
> * Czyszczenie zasobów

Dodatkowe zasoby:

* [Dokumentacja interfejsu API REST usługi Azure Menedżer wdrażania](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Menedżer wdrażania](./deployment-manager-tutorial-health-check.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu są potrzebne następujące czynności:

* Niektóre doświadczenie związane z tworzeniem [szablonów Azure Resource Manager](./resource-group-overview.md).
* Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Polecenia cmdlet Menedżer wdrażania. Aby zainstalować te polecenia cmdlet w wersji wstępnej, potrzebna jest Najnowsza wersja programu PowerShellGet. Aby uzyskać najnowszą wersję, zobacz [Installing PowerShellGet](/powershell/gallery/installing-psget). Po zainstalowaniu PowerShellGet Zamknij okno programu PowerShell. Otwórz nowe okno programu PowerShell z podwyższonym poziomem uprawnień i użyj następującego polecenia:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

* [Eksplorator usługi Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Eksplorator usługi Azure Storage nie jest wymagana, ale ułatwia wykonywanie zadań.

## <a name="understand-the-scenario"></a>Zapoznaj się z scenariuszem

Szablon topologii usług zawiera opis zasobów platformy Azure, które składają się na usługę i gdzie należy je wdrożyć. Definicja topologii usług ma następującą hierarchię:

* Topologia usługi
  * Usługi
    * Jednostki usługi

Na poniższym diagramie przedstawiono topologię usługi używaną w tym samouczku:

![Diagram scenariusza samouczka usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

W zachodnich stanach USA i regionach Wschodnie stany USA są przydzieleni dwie usługi.  Każda usługa ma dwie jednostki usługi — fronton aplikacji sieci Web i konto magazynu dla zaplecza. Definicje jednostek usługi zawierają linki do plików szablonów i parametrów służących do tworzenia aplikacji sieci Web i kont magazynu.

## <a name="download-the-tutorial-files"></a>Pobierz pliki samouczka

1. Pobierz [Szablony i artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) używane w tym samouczku.
2. Rozpakuj pliki na komputer lokalizacji.

W folderze głównym istnieją dwa foldery:

* **ADMTemplates**: zawiera szablony Menedżer wdrażania, które obejmują:
  * CreateADMServiceTopology. JSON
  * CreateADMServiceTopology. Parameters. JSON
  * CreateADMRollout. JSON
  * CreateADMRollout. Parameters. JSON
* **ArtifactStore**: zawiera zarówno artefakty szablonu, jak i artefakty binarne. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).

Należy zauważyć, że istnieją dwa zestawy szablonów.  Jeden zestaw to Menedżer wdrażania szablonów, które są używane do wdrożenia topologii usługi i wdrożenia; drugi zestaw jest wywoływany z jednostek usługi w celu utworzenia usług sieci Web i kont magazynu.

## <a name="prepare-the-artifacts"></a>Przygotuj artefakty

Folder ArtifactStore z pobierania zawiera dwa foldery:

![Diagram źródła artefaktów samouczka usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Folder **szablonów** : zawiera artefakty szablonu. **1.0.0.0** i **1.0.0.1** reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje folder dla każdej usługi (Wschodnie stany USA i usługi zachodnie stany USA). Każda usługa ma parę plików szablonów i parametrów do tworzenia konta magazynu, a druga para do tworzenia aplikacji sieci Web. Szablon aplikacji sieci Web wywołuje skompresowany pakiet, który zawiera pliki aplikacji sieci Web. Skompresowany plik to binarny artefakt przechowywany w folderze plików binarnych.
* Folder **plików binarnych** : zawiera artefakty binarne. **1.0.0.0** i **1.0.0.1** reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje jeden plik zip służący do tworzenia aplikacji sieci Web w lokalizacji zachodnie stany USA, a drugi plik zip do tworzenia aplikacji sieci Web w lokalizacji Wschodnie stany USA.

Dwie wersje (1.0.0.0 i 1.0.0.1) są przeznaczone do [wdrożenia poprawki](#deploy-the-revision). Chociaż zarówno artefakty szablonu, jak i artefakty binarne mają dwie wersje, tylko artefakty binarne różnią się między tymi dwiema wersjami. W przypadku artefaktów binarnych są aktualizowane częściej niż w przypadku artefaktów szablonu.

1. Otwórz **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.JSON** w edytorze tekstu. Jest to podstawowy szablon służący do tworzenia konta magazynu.
2. Otwórz **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.JSON**.

    ![Samouczek dotyczący tworzenia szablonu aplikacji sieci Web w usłudze Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Szablon wywołuje pakiet wdrożeniowy, który zawiera pliki aplikacji sieci Web. W tym samouczku skompresowany pakiet zawiera tylko plik index. html.
3. Otwórz **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.JSON**.

    ![Samouczek usługi Azure Menedżer wdrażania Tworzenie parametrów szablonu aplikacji sieci Web containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Wartość deployPackageUri jest ścieżką do pakietu wdrożeniowego. Parametr zawiera zmienną **$containerRoot** . Wartość $containerRoot jest udostępniana w [szablonie wdrożenia](#create-the-rollout-template) przez połączenie lokalizacji SAS źródła artefaktu, elementu głównego artefaktu i deployPackageUri.
4. Otwórz **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    Kod HTML pokazuje lokalizację i informacje o wersji. Plik binarny w folderze 1.0.0.1 zawiera wartość "Version 1.0.0.1". Po wdrożeniu usługi możesz przejść do tej strony.
5. Zapoznaj się z innymi plikami artefaktów. Ułatwia zrozumienie scenariusza.

Artefakty szablonu są używane przez szablon topologii usługi i artefakty binarne są używane przez szablon wdrożenia. Zarówno szablon topologii, jak i szablon wdrożenia definiują zasób platformy Azure źródła artefaktu, który jest zasobem używanym do wskazywania Menedżer zasobów do szablonu i artefaktów binarnych używanych we wdrożeniu. Aby uprościć samouczek, jedno konto magazynu jest używane do przechowywania zarówno artefaktów szablonu, jak i artefaktów binarnych. Oba źródła artefaktów wskazują na to samo konto magazynu.

1. Utwórz konto usługi Azure Storage. Aby uzyskać instrukcje, zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Utwórz kontener obiektów BLOB na koncie magazynu.
3. Skopiuj dwa foldery (pliki binarne i szablony) oraz zawartość tych dwóch folderów do kontenera obiektów BLOB. [Eksplorator usługi Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) obsługuje funkcję przeciągania i upuszczania.
4. Pobierz lokalizację sygnatury dostępu współdzielonego kontenera, wykonując następujące instrukcje:

    1. W obszarze Eksplorator usługi Azure Storage przejdź do kontenera obiektów BLOB.
    2. Kliknij prawym przyciskiem myszy kontener obiektów BLOB w okienku po lewej stronie, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.
    3. Skonfiguruj **czas rozpoczęcia** i **czas wygaśnięcia**.
    4. Wybierz pozycję **Utwórz**.
    5. Utwórz kopię adresu URL. Ten adres URL jest wymagany do wypełnienia pola w pliku z dwoma parametrami, [plikiem parametrów topologii](#topology-parameters-file) i [plikiem parametrów wdrożenia](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

W dalszej części tego samouczka wdrożono wdrożenie. Tożsamość zarządzana przypisana przez użytkownika jest wymagana do wykonania akcji wdrażania (na przykład wdrożenia aplikacji sieci Web i konta magazynu). Ta tożsamość musi mieć udzielony dostęp do subskrypcji platformy Azure, w której jest wdrażana usługa, i mieć wystarczające uprawnienia do ukończenia wdrożenia artefaktu.

Należy utworzyć tożsamość zarządzaną przypisaną przez użytkownika i skonfigurować kontrolę dostępu dla subskrypcji.

> [!IMPORTANT]
> Tożsamość zarządzana przypisana przez użytkownika musi znajdować się w tej samej lokalizacji co [wdrożenie.](#create-the-rollout-template) Obecnie Menedżer wdrażania zasoby, w tym wdrażanie, można utworzyć tylko w środkach środkowe stany USA lub Wschodnie stany USA 2. Jest to jednak tylko prawdziwe dla zasobów Menedżer wdrażania (takich jak topologia usług, usługi, jednostki usług, wdrożenia i kroki). Zasoby docelowe można wdrożyć w dowolnym obsługiwanym regionie platformy Azure. Na przykład w tym samouczku zasoby Menedżer wdrażania są wdrażane w środkowych stanach USA, ale usługi są wdrażane w regionach Wschodnie stany USA i zachodnie stany USA. To ograniczenie zostanie zniesione w przyszłości.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. W portalu wybierz pozycję **subskrypcje** z menu po lewej stronie, a następnie wybierz swoją subskrypcję.
4. Wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz pozycję **Dodaj przypisanie roli**.
5. Wprowadź lub wybierz następujące wartości:

    ![Samouczek usługi Azure Menedżer wdrażania — zarządzana kontrola dostępu do tożsamości przypisana przez użytkownika](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rola**: nadaj wystarczające uprawnienia do ukończenia wdrożenia artefaktu (aplikacji sieci Web i kont magazynu). W tym samouczku wybierz pozycję **współautor** . W tym celu należy ograniczyć uprawnienia do minimum.
    * **Przypisany dostęp do**: wybierz opcję **tożsamość zarządzana przypisana przez użytkownika**.
    * Wybierz tożsamość zarządzaną przypisaną przez użytkownika utworzoną wcześniej w samouczku.
6. Wybierz pozycję **Zapisz**.

## <a name="create-the-service-topology-template"></a>Tworzenie szablonu topologii usług

Otwórz **\ADMTemplates\CreateADMServiceTopology.JSON**.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

![Parametry szablonu topologii samouczka usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

* **namePrefix**: ten prefiks służy do tworzenia nazw dla zasobów Menedżer wdrażania. Na przykład przy użyciu prefiksu "JKowalski" Nazwa topologii usługi to **JKowalski**servicetopology.  Nazwy zasobów są zdefiniowane w sekcji zmienne tego szablonu.
* **azureResourcelocation**: Aby uprościć samouczek, wszystkie zasoby współużytkują tę lokalizację, chyba że zostanie określona inaczej. Obecnie zasoby usługi Azure Menedżer wdrażania mogą być tworzone tylko w **środkach środkowe stany USA** lub **Wschodnie stany USA 2**.
* **artifactSourceSASLocation**: identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów blob, w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* **templateArtifactRoot**: ścieżka przesunięcia z kontenera obiektów blob, w którym są przechowywane szablony i parametry. Wartość domyślna to **templates/1.0.0.0**. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w temacie [przygotowanie artefaktów](#prepare-the-artifacts). Ścieżki względne są używane w tym samouczku.  Pełna ścieżka jest zbudowana przez złączenie **artifactSourceSASLocation**, **templateArtifactRoot**i **templateArtifactSourceRelativePath** (lub **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: Identyfikator subskrypcji, do której mają zostać wdrożone i rozliczane zasoby Menedżer wdrażania. Użyj identyfikatora subskrypcji w tym samouczku.

### <a name="the-variables"></a>Zmienne

Sekcja zmienne definiuje nazwy zasobów, lokalizacje platformy Azure dla dwóch usług: **Service WUS** i **Service EUS**oraz ścieżki artefaktów:

![Zmienne szablonu topologii samouczka usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porównaj ścieżki artefaktów z strukturą folderów, która została przekazana do konta magazynu. Zauważ, że ścieżki artefaktu są ścieżkami względnymi. Pełna ścieżka jest zbudowana przez złączenie **artifactSourceSASLocation**, **templateArtifactRoot**i **templateArtifactSourceRelativePath** (lub **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Zasoby

Na poziomie głównym istnieją dwa zdefiniowane zasoby: *Źródło artefaktu*i *topologia usługi*.

Definicja źródła artefaktu:

![Źródło artefaktów zasobów szablonu topologii samouczka usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Poniższy zrzut ekranu przedstawia tylko niektóre części definicji topologii usług, usług i jednostek usługi:

![Topologia usługi zasobów szablonów topologii samouczka Menedżer wdrażania platformy Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** jest używany do kojarzenia zasobu źródłowego artefaktu z zasobem topologii usługi.
* **dependsOn**: wszystkie zasoby topologii usług są zależne od zasobu źródłowego artefaktu.
* **artefakty** wskazują na artefakty szablonu.  Tutaj używane są ścieżki względne. Pełna ścieżka jest zbudowana przez złączenie artifactSourceSASLocation (zdefiniowane w źródle artefaktu), artifactRoot (zdefiniowane w źródle artefaktu) i templateArtifactSourceRelativePath (lub parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Plik parametrów topologii

Tworzysz plik parametrów używany z szablonem topologii.

1. Otwórz **\ADMTemplates\CreateADMServiceTopology.Parameters** w Visual Studio Code lub dowolnym edytorze tekstu.
2. Wypełnij wartości parametrów:

    * **namePrefix**: wprowadź ciąg z 4-5 znaków. Ten prefiks służy do tworzenia unikatowych nazw zasobów platformy Azure.
    * **azureResourceLocation**.: Jeśli nie znasz lokalizacji platformy Azure, użyj **centralnie** w tym samouczku.
    * **artifactSourceSASLocation**: Wprowadź identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    * **templateArtifactRoot**: chyba że zmienisz strukturę folderów artefaktów, użyj **szablonów/1.0.0.0** w tym samouczku.
    * **targetScriptionID**: Wprowadź identyfikator subskrypcji platformy Azure.

> [!IMPORTANT]
> Szablon topologii i szablon wdrożenia mają kilka typowych parametrów. Te parametry muszą mieć takie same wartości. Parametry te to: **namePrefix**, **azureResourceLocation**i **artifactSourceSASLocation** (oba źródła artefaktów współużytkują to samo konto magazynu w tym samouczku).

## <a name="create-the-rollout-template"></a>Utwórz szablon wdrożenia

Otwórz **\ADMTemplates\CreateADMRollout.JSON**.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

![Samouczek usługi Azure Menedżer wdrażania wdrażanie parametrów szablonu](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **namePrefix**: ten prefiks służy do tworzenia nazw dla zasobów Menedżer wdrażania. Na przykład przy użyciu prefiksu "JKowalski" nazwa wdrożenia to **JKowalski**.  Nazwy są zdefiniowane w sekcji zmienne szablonu.
* **azureResourcelocation**: Aby uprościć samouczek, wszystkie zasoby Menedżer wdrażania współużytkują tę lokalizację, chyba że zostanie określona inaczej. Obecnie zasoby usługi Azure Menedżer wdrażania mogą być tworzone tylko w **środkach środkowe stany USA** lub **Wschodnie stany USA 2**.
* **artifactSourceSASLocation**: identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* **binaryArtifactRoot**: wartość domyślna to **plików binarnych/1.0.0.0**. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w temacie [przygotowanie artefaktów](#prepare-the-artifacts). Ścieżki względne są używane w tym samouczku.  Pełna ścieżka jest zbudowana przez złączenie **artifactSourceSASLocation**, **binaryArtifactRoot**i **deployPackageUri** określonych w pliku CreateWebApplicationParameters. JSON.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* **managedIdentityID**: tożsamość zarządzana przypisana przez użytkownika, która wykonuje akcje wdrożenia. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Zmienne

Sekcja zmienne definiuje nazwy zasobów. Upewnij się, że nazwa topologii usługi, nazwy usług i nazwy jednostek usługi są zgodne z nazwami zdefiniowanymi w [szablonie topologii](#create-the-service-topology-template).

![Tworzenie zmiennych szablonu w samouczku Menedżer wdrażania platformy Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Zasoby

Na poziomie głównym istnieją trzy zdefiniowane zasoby: Źródło artefaktu, krok i wdrożenie.

Definicja źródła artefaktu jest taka sama jak zdefiniowana w szablonie topologii.  Aby uzyskać więcej informacji, zobacz [Tworzenie szablonu topologii usług](#create-the-service-topology-template) .

Poniższy zrzut ekranu przedstawia definicję kroku oczekiwania:

![Samouczek usługi Azure Menedżer wdrażania wdrażanie zasobów szablonu krok oczekiwania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Czas trwania korzysta ze [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (wymagane są wielkie litery) to przykład oczekiwania na 1 minutę.

Poniższy zrzut ekranu przedstawia tylko niektóre części definicji wdrożenia:

![Samouczek Menedżer wdrażania platformy Azure — wdrażanie zasobów szablonu](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: zasób wdrażania zależy od zasobu źródłowego artefaktu i dowolnego zdefiniowanego kroku.
* **artifactSourceId**: służy do kojarzenia zasobu źródłowego artefaktu z zasobem wdrażania.
* **targetServiceTopologyId**: służy do kojarzenia zasobu topologii usługi z zasobem wdrażania.
* **deploymentTargetId**: jest identyfikatorem zasobu jednostki usługi dla zasobu topologii usługi.
* **preDeploymentSteps** i **postDeploymentSteps**: zawiera kroki wdrażania. Krok oczekiwania jest wywoływany w szablonie.
* **dependsOnStepGroups**: Skonfiguruj zależności między grupami kroków.

### <a name="rollout-parameters-file"></a>Plik parametrów wdrożenia

Tworzysz plik parametrów używany z szablonem wdrożenia.

1. Otwórz **\ADMTemplates\CreateADMRollout.Parameters** w Visual Studio Code lub dowolnym edytorze tekstu.
2. Wypełnij wartości parametrów:

    * **namePrefix**: wprowadź ciąg z 4-5 znaków. Ten prefiks służy do tworzenia unikatowych nazw zasobów platformy Azure.
    * **azureResourceLocation**: obecnie zasoby platformy Azure Menedżer wdrażania mogą być tworzone tylko w **środkach środkowe stany USA** lub **Wschodnie stany USA 2**.
    * **artifactSourceSASLocation**: Wprowadź identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    * **binaryArtifactRoot**: chyba że zmienisz strukturę folderów artefaktów, użyj **plików binarnych/1.0.0.0** w tym samouczku.
    * **managedIdentityID**: wprowadź tożsamość zarządzaną przypisaną przez użytkownika. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity). Składnia jest następująca:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Szablon topologii i szablon wdrożenia mają kilka typowych parametrów. Te parametry muszą mieć takie same wartości. Parametry te to: **namePrefix**, **azureResourceLocation**i **artifactSourceSASLocation** (oba źródła artefaktów współużytkują to samo konto magazynu w tym samouczku).

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Azure PowerShell można użyć do wdrożenia szablonów.

1. Uruchom skrypt, aby wdrożyć topologię usługi.

    ```azurepowershell
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"

    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    > [!NOTE]
    > `New-AzResourceGroupDeployment` to wywołanie asynchroniczne. Komunikat o powodzeniu oznacza, że wdrożenie zostało pomyślnie rozpoczęte. Aby sprawdzić wdrożenie, zobacz krok 2 i krok 4 tej procedury.

2. Sprawdź, czy topologia usługi i podkreślone zasoby zostały pomyślnie utworzone przy użyciu Azure Portal:

    ![Samouczek usługi Azure Menedżer wdrażania wdrożony zasoby topologii usług](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Aby wyświetlić zasoby, należy wybrać **Wyświetlanie typów ukrytych** .

3. <a id="deploy-the-rollout-template"></a>Wdróż szablon wdrożenia:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Sprawdź postęp wdrażania przy użyciu następującego skryptu programu PowerShell:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Aby można było uruchomić to polecenie cmdlet, należy zainstalować polecenia cmdlet programu PowerShell Menedżer wdrażania. Zobacz wymagania wstępne. Przełącznik-verbose może służyć do wyświetlania całego danych wyjściowych.

    Poniższy przykład pokazuje stan uruchomienia:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Po pomyślnym wdrożeniu wdrożenia widoczne są dwie inne grupy zasobów, po jednej dla każdej usługi.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz [Azure Portal](https://portal.azure.com).
2. Przejdź do nowo utworzonych aplikacji sieci Web w ramach nowych grup zasobów utworzonych przez wdrożenie wdrożenia.
3. Otwórz aplikację sieci Web w przeglądarce sieci Web. Sprawdź lokalizację i wersję pliku index. html.

## <a name="deploy-the-revision"></a>Wdróż poprawkę

Jeśli masz nową wersję (1.0.0.1) dla aplikacji sieci Web. Aby ponownie wdrożyć aplikację sieci Web, można użyć poniższej procedury.

1. Otwórz plik CreateADMRollout. Parameters. JSON.
2. Zaktualizuj **binaryArtifactRoot** do **plików binarnych/1.0.0.1**.
3. Wdróż ponownie wdrożenie zgodnie z instrukcjami w temacie [wdrażanie szablonów](#deploy-the-rollout-template).
4. Sprawdź wdrożenie zgodnie z instrukcjami w temacie [Weryfikowanie wdrożenia](#verify-the-deployment). Na stronie sieci Web zostanie wyświetlona wersja 1.0.0.1.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Użyj pola **Filtruj według nazwy** , aby zawęzić grupy zasobów utworzone w tym samouczku. Powinna być 3-4:

    * **&lt;namePrefix > RG**: zawiera zasoby Menedżer wdrażania.
    * **&lt;namePrefix > ServiceWUSrg**: zawiera zasoby zdefiniowane przez ServiceWUS.
    * **&lt;namePrefix > ServiceEUSrg**: zawiera zasoby zdefiniowane przez ServiceEUS.
    * Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .
5. Powtórz ostatnie dwa kroki, aby usunąć inne grupy zasobów utworzone w tym samouczku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z usługi Azure Menedżer wdrażania. Aby zintegrować monitorowanie kondycji w usłudze Azure Menedżer wdrażania, zobacz [Samouczek: używanie sprawdzania kondycji w usłudze azure Menedżer wdrażania](./deployment-manager-tutorial-health-check.md).
