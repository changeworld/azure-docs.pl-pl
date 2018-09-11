---
title: Tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS w usłudze Azure DevTest Labs przy użyciu szablonu usługi Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: e53e3e551041ed5bb04ae8e692a3fe3baf2521f8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302243"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager

[Witryny Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) pozwala na łatwe [Dodaj jedną maszynę Wirtualną w czasie do laboratorium](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Jednak jeśli środowisko zawiera wiele maszyn wirtualnych, każda maszyna wirtualna musi indywidualnie utworzone. W takich scenariuszach wielowarstwową aplikację sieci Web lub farmy programu SharePoint mechanizm jest wymagane w celu umożliwienia tworzenia wielu maszyn wirtualnych w jednym kroku. Za pomocą szablonów usługi Azure Resource Manager, możesz teraz zdefiniować infrastruktury i konfiguracji rozwiązania platformy Azure i wielokrotnie wdrażać wiele maszyn wirtualnych w spójnym stanie. Ta funkcja zapewnia następujące korzyści:

- Szablony usługi Azure Resource Manager są ładowane bezpośrednio z repozytorium kontroli źródła (GitHub lub repozytorium Git usługi Azure DevOps Services).
- Po skonfigurowaniu użytkownicy mogą tworzyć środowiska, wybierając szablon usługi Azure Resource Manager w witrynie Azure portal, tak samo jak z innymi rodzajami [bazami maszyn wirtualnych](./devtest-lab-comparing-vm-base-image-types.md).
- Zasoby usługi Azure PaaS mogą być udostępniane w środowisku przy użyciu szablonu usługi Azure Resource Manager oprócz maszyn wirtualnych IaaS.
- Koszty środowisk można śledzić w laboratorium, oprócz poszczególne maszyny wirtualne utworzone przez innych typów baz.
- Zasoby PaaS są tworzone i pojawi się na śledzenie; kosztów Wyłączenie automatycznego maszyny Wirtualnej nie ma zastosowania do zasobów PaaS.
- Użytkownicy mają tego samego kontroli zasad maszyny Wirtualnej dla środowisk skończona, gdyż dla maszyn wirtualnych w jednym laboratorium.

Dowiedz się więcej o wiele [korzyści z używania szablonów usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) do wdrożenia, aktualizowanie lub usuwanie wszystkich zasobów laboratorium w ramach jednej operacji.

> [!NOTE]
> Gdy używasz szablonu usługi Resource Manager jako podstawy do tworzenia laboratorium więcej maszyn wirtualnych, istnieją pewne różnice, o których należy pamiętać, czy tworzysz Multi-maszyn wirtualnych lub maszyn wirtualnych na jednym. [Użyj szablonu usługi Azure Resource Manager maszynę wirtualną](devtest-lab-use-resource-manager-template.md) wyjaśnia różnice te bardziej szczegółowo.
>

## <a name="devtest-labs-public-environments"></a>Środowiskach publicznych DevTest Labs
Usługa Azure DevTest Labs ma [publicznego repozytorium szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) używanego do tworzenia środowisk bez konieczności nawiązywania połączenia z zewnętrznym źródłem GitHub samodzielnie. To repozytorium zawiera często używane szablony, takich jak Azure Web Apps, klaster usługi Service Fabric i Programowanie w środowisku farmy programu SharePoint. Ta funkcja jest podobne do publicznego repozytorium artefaktów, które jest uwzględniany w przypadku każdego tworzenia laboratorium. Repozytorium środowiska umożliwia szybkie rozpoczynanie pracy z szablonami wstępnie przygotowane środowiska minimalne parametrów wejściowych do przedstawienia bezproblemowe środowisko dla zasobów PaaS w labs. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i używanie środowiskach publicznych w usłudze DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Konfigurowanie repozytoriów szablonu
Jako jedna z najlepszych rozwiązań przy użyciu infrastruktury jako kodu i konfiguracji jako kodu szablonów środowiska powinny być zarządzane w kontroli źródła. Usługa Azure DevTest Labs następuje tej praktyką i ładuje wszystkie szablony usługi Azure Resource Manager bezpośrednio z repozytoriami GitHub lub Git usług DevOps platformy Azure. W rezultacie szablonów usługi Resource Manager może służyć cyklu całego procesu zarządzania wersjami ze środowiska testowego do środowiska produkcyjnego.

Zapoznaj się z szablonów utworzonych przez zespół usługi DevTest Labs w [publicznego repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Environments). W tym publicznym repozytorium można wyświetlić szablony udostępnione przez innych użytkowników można używać bezpośrednio lub dostosować je do swoich potrzeb. Po utworzeniu szablonu, należy go przechowywać w repozytorium, aby udostępnić go innym osobom. Można też skonfigurować własne repozytorium Git przy użyciu szablonów, które mogą służyć do konfigurowania środowisk w chmurze. 

Istnieje kilka reguł, które trzeba wykonać, aby zorganizować szablonów usługi Azure Resource Manager w repozytorium:

- Plik szablonu musi mieć nazwę `azuredeploy.json`. 

    ![Pliki szablonów klucza usługi Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Jeśli chcesz użyć wartości parametrów zdefiniowanych w pliku parametrów, musi mieć nazwę pliku parametrów `azuredeploy.parameters.json`.
- Można używać parametrów `_artifactsLocation` i `_artifactsLocationSasToken` do konstruowania parametersLink wartość identyfikatora URI, dzięki czemu usługa DevTest Labs do automatycznego zarządzania zagnieżdżonych szablonów. Aby uzyskać więcej informacji, zobacz [jak usługi Azure DevTest Labs ułatwia zagnieżdżonych usługi Resource Manager Szablon wdrożenia dla środowisk testowych](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Można zdefiniować metadane, aby określić nazwę wyświetlaną szablonu i opis. Te metadane musi znajdować się w pliku o nazwie `metadata.json`. Następujący przykład pliku metadanych przedstawia sposób określić nazwę wyświetlaną i opis: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

Poniższe kroki prowadzą przez proces dodawania repozytorium do laboratorium przy użyciu witryny Azure portal. 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Z listy labs wybierz żądane laboratorium.   
1. W laboratorium **Przegląd** okienku wybierz **konfiguracji i zasad**.

    ![Konfiguracja i zasady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z **konfiguracji i zasad** Ustawienia wybierz pozycję **repozytoriów**. **Repozytoriów** okienku są wyświetlane repozytoria, które zostały dodane do laboratorium. Repozytorium o nazwie `Public Repo` jest generowane automatycznie dla wszystkich labs i łączy się z [repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-devtestlab) zawiera kilka artefaktów maszyny Wirtualnej do użycia.

    ![Publiczne repozytorium](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Wybierz **Dodaj +** dodać repozytorium szablonów usługi Azure Resource Manager.
1. Podczas drugiego **repozytoriów** zostanie otwarte okienko, wprowadź wymagane informacje w następujący sposób:
    - **Nazwa** — wprowadź nazwę repozytorium, która jest używana w środowisku laboratoryjnym.
    - **Adres URL klonowania Git** — wprowadź adres URL klonowania GIT HTTPS z usługi GitHub lub usługom DevOps platformy Azure.  
    - **Gałąź** — wprowadź nazwę gałęzi, aby uzyskiwać dostęp do definicji szablonu usługi Azure Resource Manager. 
    - **Osobisty token dostępu** -osobisty token dostępu jest używany do bezpiecznego dostępu do repozytorium. Aby uzyskać token z usługi DevOps platformy Azure, wybierz pozycję  **&lt;twojanazwa >> Mój profil > Zabezpieczenia > token dostępu publicznego**. Aby uzyskać token z usługi GitHub, wybierz swój awatar, a następnie wybierając **Ustawienia > token dostępu publicznego**. 
    - **Ścieżka folderu** — przy użyciu jednej z dwóch pól danych wejściowych, wpisz ścieżkę do folderu, rozpoczyna się od ukośnika, - i -, która jest względem usługi Git clone URI do jednej definicji artefaktu (pierwsze pole wejściowe) lub do definicji szablonu usługi Azure Resource Manager .   
    
        ![Publiczne repozytorium](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Gdy wszystkie wymagane pola są wprowadzane i pomyślnie weryfikacji, wybierz **Zapisz**.

Następna sekcja przeprowadzi Cię tworzenie środowisk przy użyciu szablonu usługi Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Utwórz środowisko na podstawie szablonu usługi Resource Manager przy użyciu witryny Azure portal

Po skonfigurowaniu repozytorium szablonów usługi Azure Resource Manager w środowisku laboratoryjnym użytkownicy laboratorium mogą tworzyć środowiska przy użyciu witryny Azure portal wykonując następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Z listy labs wybierz żądane laboratorium.   
1. W okienku laboratorium wybierz **Dodaj +**.
1. **Wybierz podstawowej** okienko Wyświetla podstawowe obrazy za pomocą szablonów usługi Azure Resource Manager wymienione jako pierwsze. Wybierz żądany szablon usługi Azure Resource Manager.

    ![Wybieranie bazy](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Na **Dodaj** okienku, wprowadź **Nazwa środowiska** wartość. Nazwa środowiska to, co jest wyświetlane użytkownikom w środowisku laboratoryjnym. Pozostałe pola wejściowe są zdefiniowane w szablonie usługi Azure Resource Manager. Jeśli wartości domyślne są zdefiniowane w szablonie lub `azuredeploy.parameter.json` pliku, wartości domyślne są wyświetlane w tych pól wejściowych. Dla parametrów typu *bezpieczny ciąg*, można użyć kluczy tajnych przechowywanych w magazynie kluczy Azure. Aby dowiedzieć się więcej na temat zapisywania wpisów tajnych w magazynie kluczy i korzystanie z nich podczas tworzenia zasobów laboratorium, zobacz [Store wpisów tajnych w usłudze Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Dodawanie okienek](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Istnieje kilka wartości parametrów, które — nawet jeśli określona — są wyświetlane jako puste wartości. W związku z tym jeśli użytkownicy przypisywać te wartości parametrów w szablonie usługi Azure Resource Manager, usługa DevTest Labs nie wyświetla wartości. Zamiast tego puste pola wejściowe są wyświetlane, gdy użytkownicy laboratorium należy wprowadzić wartość podczas tworzenia środowiska.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Wybierz **Dodaj** do tworzenia środowiska. Środowisko rozpoczyna się natychmiast aprowizacji z wyświetlaniem stanu w **Moje maszyny wirtualne** listy. Nowa grupa zasobów jest automatycznie tworzona przez laboratorium, aby aprowizować wszystkie zasoby, które są zdefiniowane w szablonie usługi Azure Resource Manager.
1. Po utworzeniu środowiska wybierz środowisko, które w **Moje maszyny wirtualne** listy, aby otworzyć okienko grupy zasobów, a następnie przejdź do wszystkich zasobów aprowizowanych w środowisku.
    
    ![Moja lista maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Można również rozwinąć środowisko Aby wyświetlić tylko listy maszyn wirtualnych, które są udostępniane w środowisku.
    
    ![Moja lista maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Kliknij dowolny z tych środowisk, aby wyświetlić dostępne akcje — takich jak stosowanie artefaktów i dołączanie dysków danych, zmiany czasu automatycznego zamykania i nie tylko.

    ![Akcje środowiska](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Wdrażanie szablonu usługi Resource Manager, aby utworzyć Maszynę wirtualną
Po zapisać szablon usługi Resource Manager i dostosować go do swoich potrzeb, można użyć go do zautomatyzowania tworzenia maszyny Wirtualnej. 
- [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) opisuje sposób używania programu Azure PowerShell przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure. 
- [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) opisuje sposób używania interfejsu wiersza polecenia platformy Azure przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure.

> [!NOTE]
> Tylko użytkownik z uprawnieniami właściciela laboratorium można tworzyć maszyny wirtualne za pomocą szablonu usługi Resource Manager przy użyciu programu Azure PowerShell. Jeśli chcesz zautomatyzować tworzenie maszyny Wirtualnej przy użyciu szablonu usługi Resource Manager i masz tylko uprawnienia użytkownika, możesz użyć [ **tworzenie az lab vm** polecenia interfejsu wiersza polecenia w pliku](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Ogólne ograniczenia 

Gdy w usłudze DevTest Labs przy użyciu szablonu usługi Resource Manager, należy wziąć pod uwagę następujące ograniczenia:

- Wszelkie tworzonego szablonu usługi Resource Manager nie może odwoływać się do istniejących zasobów. może odnosić się tylko do nowych zasobów. Ponadto jeśli masz istniejący szablon usługi Resource Manager, zwykle wdrożyć poza DevTest Labs, i zawiera odwołania do istniejących zasobów, nie można użyć w środowisku laboratoryjnym.

   Jedynym wyjątkiem jest to, że możesz **można** odwoływać się do istniejącej sieci wirtualnej. 

- Nie można utworzyć formuły z laboratorium, maszyny wirtualne, które zostały utworzone przy użyciu szablonu usługi Resource Manager. 

- Nie można utworzyć niestandardowe obrazy z laboratorium, maszyny wirtualne, które zostały utworzone przy użyciu szablonu usługi Resource Manager. 

- Większość zasad nie są oceniane podczas wdrażania szablonów usługi Resource Manager.

   Na przykład Niewykluczone, że zasady z laboratorium określenie, czy użytkownika można tworzyć tylko pięciu maszyn wirtualnych. Jednak użytkownika można wdrożyć szablon usługi Resource Manager, który tworzy kilkadziesiąt maszyn wirtualnych. Zasady, które nie są sprawdzane obejmują:

   - Liczba maszyn wirtualnych na użytkownika
   - Liczba maszyn wirtualnych usługi premium dla poszczególnych użytkowników laboratorium
   - Liczba dysków w warstwie premium dla poszczególnych użytkowników laboratorium


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Konfigurowanie praw dostępu grupy zasobów środowisko dla użytkowników laboratorium

Użytkownicy laboratorium mogą wdrażać szablon usługi Resource Manager. Jednak domyślnie mają oni uprawnienia dostępu czytnika, co oznacza, że nie mogą zmieniać zasoby w grupie zasobów w środowisku. Na przykład nie można zatrzymać lub uruchomić ich zasobów.

Wykonaj następujące kroki, aby zapewnić użytkownikom laboratorium prawa dostępu współautora. Następnie po wdrożeniu przez nich szablon usługi Resource Manager, będą mogli edytować zasoby w tym środowisku. 


1. W środowisku laboratoryjnym **Przegląd** okienku wybierz **konfiguracji i zasad**.
1. Wybierz **ustawienia Lab**.
1. W okienku ustawienia Lab wybierz **Współautor** udzielenia uprawnienia do zapisu dla użytkowników w laboratorium.

    ![Konfigurowanie praw użytkownika, dostęp do laboratorium](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
* Po utworzeniu maszyny Wirtualnej można nawiązać maszyny Wirtualnej, wybierając **Connect** w okienku zarządzania maszyny Wirtualnej.
* Wyświetlanie zasobów i zarządzanie nimi w środowisku, wybierając środowiska w **Moje maszyny wirtualne** listy w środowisku laboratoryjnym. 
* Zapoznaj się z [szablony usługi Azure Resource Manager z galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
