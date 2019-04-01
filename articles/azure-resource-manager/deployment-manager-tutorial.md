---
title: Używanie usługi Azure Deployment Manager z szablonami usługi Resource Manager | Microsoft Docs
description: Użyj szablonów usługi Resource Manager z usługą Azure Deployment Manager, aby wdrażać zasoby platformy Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 161eb302dfa1eb002a49afcd08da1a2795bc81ed
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649428"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-private-preview"></a>Samouczek: Używanie usługi Azure Deployment Manager z szablonami usługi Resource Manager (prywatna wersja zapoznawcza)

Dowiedz się, jak używać usługi [Azure Deployment Manager](./deployment-manager-overview.md), aby wdrażać aplikacje w wielu regionach. Aby użyć usługi Deployment Manager, musisz utworzyć dwa szablony:

* **Szablon topologii**: opisuje zasoby platformy Azure składające się na aplikację oraz miejsce ich wdrożenia.
* **Szablon wprowadzania**: opisuje kroki do wykonania podczas wdrażania aplikacji.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Omówienie scenariusza
> * Pobieranie plików samouczka
> * Przygotowywanie artefaktów
> * Tworzenie tożsamości zarządzanej zdefiniowanej przez użytkownika
> * Tworzenie szablonu topologii usługi
> * Tworzenie szablonu wprowadzania
> * Wdrażanie szablonów
> * Weryfikowanie wdrożenia
> * Wdrażanie nowszej wersji
> * Oczyszczanie zasobów

Dokumentację interfejsu REST API usługi Azure Deployment Manager można znaleźć [tutaj](https://docs.microsoft.com/rest/api/deploymentmanager/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Pewne doświadczenie w opracowywaniu [szablonów usługi Azure Resource Manager](./resource-group-overview.md).
* Usługa Azure Deployment Manager jest w prywatnej wersji zapoznawczej. Aby utworzyć konto przy użyciu usługi Azure Deployment Manager, wypełnij [arkusz tworzenia konta](https://aka.ms/admsignup). 
* Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Polecenia cmdlet usługi Deployment Manager. Aby zainstalować te polecenia cmdlet wersji wstępnej, potrzebujesz najnowszej wersji modułu PowerShellGet. Aby uzyskać najnowszą wersję, zobacz [Installing PowerShellGet (Instalowanie modułu PowerShellGet)](/powershell/gallery/installing-psget). Po zainstalowaniu modułu PowerShellGet zamknij okno programu PowerShell. Otwórz nowe okno programu PowerShell z podwyższonym poziomem uprawnień i użyj następującego polecenia:

    ```powershell
    Install-Module -Name Az.DeploymentManager -AllowPrerelease -AllowClobber -Force
    ```

* [Eksplorator usługi Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Eksplorator usługi Azure Storage nie jest wymagany, ale ułatwia działanie.

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Szablon topologii usługi opisuje zasoby platformy Azure składające się na usługę oraz miejsce ich wdrożenia. Definicja topologii usługi ma następującą hierarchię:

* Topologia usługi
    * Usługi
        * Jednostki usługi

Na poniższym diagramie przedstawiono topologię usługi używaną w tym samouczku:

![Diagram przedstawiający scenariusz samouczka dotyczącego usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Istnieją dwie usługi przydzielone w lokalizacjach Zachodnie stany USA oraz Wschodnie stany USA.  Każda usługa ma dwie jednostki usługi — fronton aplikacji internetowej oraz konto magazynu na potrzeby zaplecza. Definicje jednostki usługi zawierają linki do plików szablonu i parametrów na potrzeby tworzenia aplikacji internetowych oraz kont magazynu.

## <a name="download-the-tutorial-files"></a>Pobieranie plików samouczka

1. Pobierz [szablony i artefakty](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) używane w tym samouczku.
2. Rozpakuj pliki do lokalizacji na komputerze.

W folderze głównym znajdują się dwa foldery:

- **ADMTemplates**: zawiera szablony usługi Deployment Manager, które obejmują następujące pliki:
    - CreateADMServiceTopology.json
    - CreateADMServiceTopology.Parameters.json
    - CreateADMRollout.json
    - CreateADMRollout.Parameters.json
- **ArtifactStore**: zawiera artefakty szablonu oraz artefakty binarne. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).

Należy pamiętać, że istnieją dwa zestawy szablonów.  Jeden zestaw zawiera szablony usługi Deployment Manager, które są używane do wdrażania topologii usługi i wprowadzania; drugi zestaw jest wywoływany z jednostek usługi w celu tworzenia usług internetowych i kont magazynu.

## <a name="prepare-the-artifacts"></a>Przygotowywanie artefaktów

Folder ArtifactStore z plików do pobrania zawiera dwa foldery:

![Diagram przedstawiający źródło artefaktów samouczka dotyczącego usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

- Folder **templates**: zawiera artefakty szablonu. **1.0.0.0** i **1.0.0.1** reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje folder dla każdej usługi (usługa w regionie Wschodnie stany USA oraz usługa w regionie Zachodnie stany USA). Każda usługa ma parę plików — szablonu i parametrów — na potrzeby tworzenia konta magazynu, a także kolejną parę na potrzeby tworzenia aplikacji internetowej. Szablon aplikacji internetowej wywołuje skompresowany pakiet, który zawiera pliki aplikacji internetowej. Skompresowany plik to artefakt binarny przechowywany w folderze binaries.
- Folder **binaries**: zawiera artefakty binarne. **1.0.0.0** i **1.0.0.1** reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje jeden plik zip na potrzeby tworzenia aplikacji internetowej w lokalizacji Zachodnie stany USA oraz drugi plik zip na potrzeby tworzenia aplikacji internetowej w lokalizacji Wschodnie stany USA.

Dwie wersje (1.0.0.0 i 1.0.0.1) dotyczą [wdrażania poprawek](#deploy-the-revision). Chociaż artefakty szablonu oraz artefakty binarne mają dwie wersje, tylko artefakty binarne różnią się w tych dwóch wersjach. W praktyce artefakty binarne są aktualizowane częściej w porównaniu do artefaktów szablonu.

1. Otwórz plik **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** w edytorze tekstów. Jest to podstawowy szablon służący do tworzenia konta magazynu.  
2. Otwórz plik **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**. 

    ![Szablon tworzenia aplikacji internetowej w samouczku dotyczącym usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Szablon wywołuje pakiet wdrożeniowy, który zawiera pliki aplikacji internetowej. W tym samouczku skompresowany pakiet zawiera tylko plik index.html.
3. Otwórz plik **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**. 

    ![Szablon tworzenia aplikacji internetowej w samouczku dotyczącym usługi Azure Deployment Manager — parametry containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Wartość identyfikatora URI deployPackageUri to ścieżka do pakietu wdrożeniowego. Parametr zawiera zmienną **$containerRoot**. Wartość zmiennej $containerRoot jest zapewniana w [szablonie wprowadzania](#create-the-rollout-template) przez złączenie lokalizacji SAS źródła artefaktu, folderu głównego artefaktu oraz identyfikatora URI deployPackageUri.
4. Otwórz plik **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.  

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
    Kod HTML przedstawia informacje o lokalizacji i wersji. Plik binarny w folderze 1.0.0.1 pokazuje frazę „Version 1.0.0.1”. Po wdrożeniu usługi możesz przejść do tych stron.
5. Zapoznaj się z innymi plikami artefaktów. Dzięki temu lepiej zrozumiesz scenariusz.

Artefakty szablonu są używane przez szablon topologii usługi, a artefakty binarne są używane przez szablon wprowadzania. Szablony topologii i wprowadzania definiują zasób źródła artefaktu na platformie Azure, który jest zasobem używanym do kierowania usługi Resource Manager do szablonu i artefaktów binarnych używanych we wdrożeniu. Aby uprościć samouczek, jedno konto magazynu jest używane do przechowywania artefaktów szablonu i artefaktów binarnych. Oba źródła artefaktów wskazują na to samo konto magazynu.

1. Utworzenie konta magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Utwórz kontener obiektów blob na koncie magazynu.
3. Skopiuj dwa foldery (binaries i templates) oraz zawartość dwóch folderów do kontenera obiektów blob. [Eksplorator usługi Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) obsługuje funkcję przeciągania i upuszczania.
4. Pobierz lokalizację sygnatury dostępu współdzielonego przy użyciu następujących instrukcji:

    1. W Eksploratorze usługi Azure Storage przejdź do kontenera obiektów blob.
    2. Kliknij prawym przyciskiem myszy kontener obiektów blob w okienku po lewej stronie, a następnie wybierz opcję **Uzyskaj sygnaturę dostępu współdzielonego**.
    3. Skonfiguruj opcje **Czas rozpoczęcia** i **Czas wygaśnięcia**.
    4. Wybierz pozycję **Utwórz**.
    5. Utwórz kopię adresu URL. Ten adres URL jest potrzebny do wypełnienia pola w dwóch plikach parametrów, [pliku parametrów topologii](#topology-parameters-file) oraz [pliku parametrów wprowadzania](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

W dalszej części samouczka wdrożysz wprowadzenie. Tożsamość zarządzana przypisana przez użytkownika jest potrzebna do wykonania działań związanych z wdrożeniem (np. wdrożenia aplikacji internetowych i konta magazynu). Ta tożsamość musi uzyskać dostęp do subskrypcji platformy Azure, w której wdrażasz usługę, a także musi mieć wystarczające uprawnienia do ukończenia wdrażania artefaktu.

Musisz utworzyć tożsamość zarządzaną przypisaną przez użytkownika i skonfigurować kontrolę dostępu dla subskrypcji.

> [!IMPORTANT]
> Tożsamość zarządzana przypisana przez użytkownika musi znajdować się w tej samej lokalizacji, co [wprowadzanie](#create-the-rollout-template). Obecnie zasoby usługi Deployment Manager, w tym wprowadzanie, można tworzyć tylko w lokalizacji Środkowe stany USA lub Wschodnie stany USA 2. Ta reguła obowiązuje jednak tylko w przypadku zasobów programu Deployment Manager (na przykład topologii usługi, usług, jednostek usług, wdrożenia i kroków). Zasoby docelowe można wdrażać w dowolnym obsługiwanym regionie platformy Azure. Na przykład w tym samouczku zasoby programu Deployment Manager są wdrażane w regionie Środkowe stany USA, a usługi są wdrażane w regionach Wschodnie stany USA i Zachodnie stany USA. To ograniczenie zostanie zlikwidowane w przyszłości.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Utwórz [tożsamość zarządzaną przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. W portalu wybierz opcję **Subskrypcje** z menu po lewej stronie, a następnie wybierz swoją subskrypcję.
4. Wybierz opcję **Kontrola dostępu (IAM)**, a następnie wybierz opcję **Dodaj przypisanie roli**.
5. Wprowadź lub wybierz poniższe wartości:

    ![Samouczek dotyczący usługi Azure Deployment Manager — kontrola dostępu tożsamości zarządzanej przypisanej przez użytkownika](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    - **Rola**: udziel wystarczających uprawnień, aby ukończyć wdrażanie artefaktu (aplikacje internetowe i konta magazynu). Wybierz opcję **Współautor** w tym samouczku. W praktyce chcesz ograniczyć uprawnienia do minimum.
    - **Przypisany dostęp do**: wybierz opcję **Tożsamość zarządzana przypisana przez użytkownika**.
    - Wybierz tożsamość zarządzaną przypisaną przez użytkownika, która została utworzona wcześniej w tym samouczku.
6. Wybierz pozycję **Zapisz**.

## <a name="create-the-service-topology-template"></a>Tworzenie szablonu topologii usługi

Otwórz plik **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

![Samouczek dotyczący usługi Azure Deployment Manager — parametry szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

- **namePrefix**: Ten prefiks jest używany do tworzenia nazw zasobów usługi Deployment Manager. Na przykład, w przypadku użycia prefiksu „jdoe” nazwa topologii usługi będzie następująca: **jdoe**ServiceTopology.  Nazwy zasobów są definiowane w sekcji zmiennych tego szablonu.
- **azureResourcelocation**: Aby uprościć ten samouczek, wszystkie zasoby współdzielą tę lokalizację, chyba że określono inaczej. Obecnie zasoby usługi Azure Deployment Manager można tworzyć tylko w lokalizacji **Środkowe stany USA** lub **Wschodnie stany USA 2**.
- **artifactSourceSASLocation**: Identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów blob, w którym przechowuje się pliki szablonu i parametrów jednostki usługi na potrzeby wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
- **templateArtifactRoot**: Ścieżka przesunięcia z kontenera obiektów blob, w których przechowywane są szablony i parametry. Wartość domyślna to **templates/1.0.0.0**. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w sekcji [Przygotowywanie artefaktów](#prepare-the-artifacts). W tym samouczku używa się ścieżek względnych.  Pełna ścieżka jest tworzona przez połączenie elementów **artifactSourceSASLocation**, **templateArtifactRoot** oraz **templateArtifactSourceRelativePath** (lub **parametersArtifactSourceRelativePath**).
- **targetSubscriptionID**: Identyfikator subskrypcji, w której zasoby usługi Deployment Manager zostaną wdrożone i będą rozliczane. Użyj identyfikatora swojej subskrypcji w tym samouczku.

### <a name="the-variables"></a>Zmienne

Sekcja zmiennych definiuje nazwy zasobów, lokalizacje platformy Azure dla dwóch usług: **Service WUS** oraz **Service EUS**, a także ścieżki artefaktów:

![Samouczek dotyczący usługi Azure Deployment Manager — zmienne szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porównaj ścieżki artefaktów ze strukturą folderu przekazaną do konta magazynu. Należy zauważyć, że ścieżki artefaktów są ścieżkami względnymi. Pełna ścieżka jest tworzona przez połączenie elementów **artifactSourceSASLocation**, **templateArtifactRoot** oraz **templateArtifactSourceRelativePath** (lub **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Zasoby

Na poziomie głównym istnieją dwa zdefiniowane zasoby: *źródło artefaktu* oraz *topologia usługi*.

Definicja źródła artefaktu to:

![Samouczek dotyczący usługi Azure Deployment Manager — źródło artefaktu w zasobach szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Poniższy zrzut ekranu przedstawia tylko pewne części definicji topologii usługi, usług oraz jednostek usługi:

![Samouczek dotyczący usługi Azure Deployment Manager — topologia usługi w zasobach szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

- **artifactSourceId** to identyfikator używany do kojarzenia zasobu źródła artefaktu z zasobem topologii usługi.
- **dependsOn**: Wszystkie zasoby topologii usługi zależą od zasobu źródła artefaktu.
- **artifacts** wskazuje artefakty szablonu.  W tym miejscu używa się ścieżek względnych. Pełna ścieżka jest tworzona przez połączenie elementów artifactSourceSASLocation (lokalizacja definiowana w źródle artefaktu), artifactRoot (lokalizacja definiowana w źródle artefaktu) oraz templateArtifactSourceRelativePath (lub parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Plik parametrów topologii

Możesz utworzyć plik parametrów używany z szablonem topologii.

1. Otwórz plik **\ADMTemplates\CreateADMServiceTopology.Parameters** w programie Visual Studio Code lub dowolnym edytorze tekstów.
2. Wprowadź wartości parametrów:

    - **namePrefix**: Wprowadź ciąg zawierający 4–5 znaków. Ten prefiks jest używany do tworzenia unikatowych nazw zasobów platformy Azure.
    - **azureResourceLocation**: Jeśli nie znasz się na lokalizacjach platformy Azure, użyj lokalizacji **centralus** w tym samouczku.
    - **artifactSourceSASLocation**: Identyfikator URI sygnatury dostępu współdzielonego do folderu głównego (kontenera obiektów blob), w którym przechowuje się pliki szablonu i parametrów jednostki usługi na potrzeby wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    - **templateArtifactRoot**: Użyj wartości **templates/1.0.0.0** w tym samouczku, chyba że chcesz zmienić strukturę folderu artefaktów.
    - **targetScriptionID**: Wprowadź identyfikator subskrypcji platformy Azure.

> [!IMPORTANT]
> Szablon topologii oraz szablon wprowadzania współdzielą niektóre parametry. Te parametry muszą mieć takie same wartości. Te parametry są następujące: **namePrefix**, **azureResourceLocation** oraz **artifactSourceSASLocation** (oba źródła artefaktów współdzielą to samo konto magazynu w tym samouczku).

## <a name="create-the-rollout-template"></a>Tworzenie szablonu wprowadzania

Otwórz plik **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

![Samouczek dotyczący usługi Azure Deployment Manager — parametry szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

- **namePrefix**: Ten prefiks jest używany do tworzenia nazw zasobów usługi Deployment Manager. Na przykład w przypadku użycia prefiksu „jdoe” nazwa wprowadzania będzie następująca: **jdoe**Rollout.  Nazwy są definiowane w sekcji zmiennych szablonu.
- **azureResourcelocation**: Aby uprościć ten samouczek, wszystkie zasoby usługi Deployment Manager współdzielą tę lokalizację, chyba że określono inaczej. Obecnie zasoby usługi Azure Deployment Manager można tworzyć tylko w lokalizacji **Środkowe stany USA** lub **Wschodnie stany USA 2**.
- **artifactSourceSASLocation**: Identyfikator URI sygnatury dostępu współdzielonego do folderu głównego (kontenera obiektów blob), w którym przechowuje się pliki szablonu i parametrów jednostki usługi na potrzeby wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
- **binaryArtifactRoot**:  Wartość domyślna to **binaries/1.0.0.0**. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w sekcji [Przygotowywanie artefaktów](#prepare-the-artifacts). W tym samouczku używa się ścieżek względnych.  Pełna ścieżka jest tworzona przez połączenie elementów **artifactSourceSASLocation**, **binaryArtifactRoot** oraz **deployPackageUri** określonych w pliku CreateWebApplicationParameters.json.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
- **managedIdentityID**: Tożsamość zarządzana przypisana przez użytkownika, która wykonuje akcje wdrażania. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Zmienne

Sekcja zmiennych definiuje nazwy zasobów. Upewnij się, że nazwa topologii usługi, nazwy usług oraz nazwy jednostek usług pasują do nazw określonych w [szablonie topologii](#create-the-service-topology-template).

![Samouczek dotyczący usługi Azure Deployment Manager — zmienne szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Zasoby

Na poziomie głównym istnieją trzy zdefiniowane zasoby: źródło artefaktu, krok oraz wprowadzenie.

Definicja źródła artefaktu jest taka sama, jak definicja określona w szablonie topologii.  Zobacz [Tworzenie szablonu topologii usługi](#create-the-service-topology-template), aby uzyskać więcej informacji.

Poniższy zrzut ekranu przedstawia definicję kroku oczekiwania:

![Samouczek dotyczący usługi Azure Deployment Manager — krok oczekiwania zasobów szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Czas trwania używa [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (wymagane są wielkie litery) to przykład 1-minutowego oczekiwania. 

Poniższy zrzut ekranu przedstawia tylko niektóre części definicji wprowadzania:

![Samouczek dotyczący usługi Azure Deployment Manager — wprowadzanie zasobów szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

- **dependsOn**: Zasób wprowadzania zależy od zasobu źródła artefaktu oraz wszelkich zdefiniowanych kroków.
- **artifactSourceId**: identyfikator używany do kojarzenia zasobu źródła artefaktu z zasobem wprowadzania.
- **targetServiceTopologyId**: identyfikator używany do kojarzenia zasobu topologii usługi z zasobem wprowadzania.
- **deploymentTargetId**: Jest to identyfikator zasobu jednostki usługi zasobu topologii usługi.
- **preDeploymentSteps** i **postDeploymentSteps**: zawierają kroki wprowadzania. W szablonie wywoływany jest krok oczekiwania.
- **dependsOnStepGroups**: umożliwia konfigurację zależności między grupami kroków.

### <a name="rollout-parameters-file"></a>Plik parametrów wprowadzania

Możesz utworzyć plik parametrów używany z szablonem wprowadzania.

1. Otwórz plik **\ADMTemplates\CreateADMRollout.Parameters** w programie Visual Studio Code lub dowolnym edytorze tekstów.
2. Wprowadź wartości parametrów:

    - **namePrefix**: Wprowadź ciąg zawierający 4–5 znaków. Ten prefiks jest używany do tworzenia unikatowych nazw zasobów platformy Azure.
    - **azureResourceLocation**: Obecnie zasoby usługi Azure Deployment Manager można tworzyć tylko w lokalizacji Środkowe stany USA lub **Wschodnie stany USA 2**.
    - **artifactSourceSASLocation**: Identyfikator URI sygnatury dostępu współdzielonego do folderu głównego (kontenera obiektów blob), w którym przechowuje się pliki szablonu i parametrów jednostki usługi na potrzeby wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    - **binaryArtifactRoot**: Użyj wartości **binaries/1.0.0.0** w tym samouczku, chyba że chcesz zmienić strukturę folderu artefaktów.
    - **managedIdentityID**: Wprowadź tożsamość zarządzaną przypisaną przez użytkownika. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity). Składnia jest następująca:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Szablon topologii oraz szablon wprowadzania współdzielą niektóre parametry. Te parametry muszą mieć takie same wartości. Te parametry są następujące: **namePrefix**, **azureResourceLocation** oraz **artifactSourceSASLocation** (oba źródła artefaktów współdzielą to samo konto magazynu w tym samouczku).

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Programu Azure PowerShell można użyć do wdrażania szablonów. 

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

2. Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

    ![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

3. <a id="deploy-the-rollout-template"></a>Wdrożenie szablonu wprowadzania:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Sprawdź postęp wprowadzania przy użyciu następującego skryptu programu PowerShell:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Należy zainstalować polecenia cmdlet programu PowerShell usługi Deployment Manager przed uruchomieniem tego polecenia cmdlet. Zobacz Wymagania wstępne. Verbose przełącznika może służyć do Zobacz całego danych wyjściowych.

    Następujący przykład przedstawia stan działania:
    
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

    Po pomyślnym wdrożeniu wprowadzenia powinny być widoczne dwie dodatkowe utworzone grupy zasobów, jedna dla każdej usługi.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz [portal Azure](https://portal.azure.com).
2. Przejdź do nowo utworzonych aplikacji internetowych w obrębie nowych grup zasobów utworzonych przez wdrożenie wprowadzania.
3. Otwórz aplikację internetową w przeglądarce internetowej. Sprawdź lokalizację i wersję w pliku index.html.

## <a name="deploy-the-revision"></a>Wdrażanie poprawki

Jeśli masz nową wersję (1.0.0.1) aplikacji internetowej. Możesz użyć poniższej procedury, aby ponownie wdrożyć aplikację internetową.

1. Otwórz plik CreateADMRollout.Parameters.json.
2. Zaktualizuj parametr **binaryArtifactRoot** do wartości **binaries/1.0.0.1**.
3. Ponownie wdróż wprowadzanie zgodnie z instrukcjami w sekcji [Wdrażanie szablonów](#deploy-the-rollout-template).
4. Sprawdź wdrożenie zgodnie z instrukcjami w sekcji [Weryfikowanie wdrożenia](#verify-the-deployment). Strona internetowa powinna teraz wyświetlać wersję 1.0.0.1.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Użyj pola **Filtruj według nazwy**, aby zawęzić listę grup zasobów utworzonych w tym samouczku. Powinny istnieć 3–4 grupy:

    - **&lt;namePrefix>rg**: zawiera zasoby usługi Deployment Manager.
    - **&lt;namePrefix>ServiceWUSrg**: zawiera zasoby zdefiniowane przez usługę ServiceWUS.
    - **&lt;namePrefix>ServiceEUSrg**: zawiera zasoby zdefiniowane przez usługę ServiceEUS.
    - Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
3. Wybierz nazwę grupy zasobów.  
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.
5. Powtórz dwa ostatnie kroki, aby usunąć inne grupy zasobów utworzone w ramach tego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia usługi Azure Deployment Manager. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
