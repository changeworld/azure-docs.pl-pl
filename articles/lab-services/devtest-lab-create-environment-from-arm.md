---
title: Tworzenie środowisk wielu maszyn wirtualnych i PaaS zasobów przy użyciu szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia środowisk wielu maszyn wirtualnych i zasoby PaaS w usłudze Azure DevTest Labs z szablonem usługi Azure Resource Manager
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 397ab30b252fbfa121b763b005907764d2b15f20
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Tworzenie środowisk wielu maszyn wirtualnych i PaaS zasobów przy użyciu szablonów usługi Azure Resource Manager

[Portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) umożliwia łatwe [Utwórz i Dodaj Maszynę wirtualną w laboratorium](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). To działa dobrze w przypadku tworzenia jednej maszyny Wirtualnej w czasie. Jednak jeśli środowisko zawiera wiele maszyn wirtualnych, każda maszyna wirtualna musi zostać indywidualnie utworzony. W takich scenariuszach wielowarstwową aplikację sieci Web lub farmy programu SharePoint mechanizm jest potrzebna, aby umożliwić tworzenie wielu maszyn wirtualnych w ramach jednego kroku. Przy użyciu szablonów usługi Azure Resource Manager, można definiować infrastrukturze i konfiguracji rozwiązania Azure i wielokrotnie wdrażać wiele maszyn wirtualnych w spójnym stanie. Ta funkcja zapewnia następujące korzyści:

- Szablony usługi Azure Resource Manager są ładowane bezpośrednio z Twoim repozytorium kontroli źródła (GitHub lub zespołu usługi Git).
- Po skonfigurowaniu użytkowników można utworzyć środowisko, wybierając szablon usługi Azure Resource Manager z portalu Azure, tak samo jak inne typy [maszyny Wirtualnej podstawy](./devtest-lab-comparing-vm-base-image-types.md).
- W środowisku z szablonem usługi Azure Resource Manager oprócz maszyny wirtualne IaaS można zainicjować obsługi administracyjnej zasobów PaaS platformy Azure.
- Koszt środowisk można śledzić w laboratorium, oprócz poszczególnych maszyn wirtualnych utworzonych przez inne typy zasad.
- PaaS zasoby są tworzone i będą widoczne w koszt śledzenia; jednak maszyna wirtualna automatycznego zamykania nie ma zastosowania do zasobów typu PaaS.
- Użytkownicy mają tej samej kontroli zasad maszyny Wirtualnej dla środowisk mają dla maszyn wirtualnych z jednego laboratorium.

Dowiedz się więcej o wielu [zalet przy użyciu szablonów usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) można wdrożyć, zaktualizować lub usunąć wszystkich zasobów laboratorium w ramach jednej operacji.

> [!NOTE]
> Gdy używasz szablonu usługi Resource Manager jako podstawy do tworzenia laboratorium więcej maszyn wirtualnych istnieją pewne różnice, które należy wziąć pod uwagę, czy podczas tworzenia maszyn wirtualnych na wielu lub maszyn wirtualnych na jednym. [Użyj szablonu usługi Azure Resource Manager maszynę wirtualną](devtest-lab-use-resource-manager-template.md) wyjaśniono różnice te bardziej szczegółowo.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Skonfiguruj repozytoria szablonu usługi Azure Resource Manager

Jako jeden z najlepszych rozwiązań z infrastruktury jako kod i kod jako konfiguracji środowiska szablony mają być zarządzane w kontroli źródła. Azure DevTest Labs następuje takie rozwiązanie i ładuje wszystkie szablony usługi Azure Resource Manager bezpośrednio z repozytoriami GitHub lub VSTS Git. W związku z tym Menedżer zasobów szablony mogą być używane w cyklu całej wersji ze środowiska testowego do środowiska produkcyjnego.

Istnieje kilka reguł, aby wykonać, aby uporządkować usługi Azure Resource Manager w repozytorium:

- Plik szablonu musi mieć nazwę `azuredeploy.json`. 

    ![Pliki szablonów klucza usługi Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Jeśli chcesz użyć wartości parametrów zdefiniowanych w pliku parametrów, musi mieć nazwę pliku parametrów `azuredeploy.parameters.json`.
- Można używać parametrów `_artifactsLocation` i `_artifactsLocationSasToken` do skonstruowania wartości identyfikatora URI parametersLink, dzięki czemu DevTest Labs automatycznie zarządzać zagnieżdżone szablony. Zobacz [jak Azure DevTest Labs ułatwia zagnieżdżonych Menedżera zasobów szablonu wdrożenia dla środowisk testowych](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/) Aby uzyskać więcej informacji.
- Aby określić nazwę wyświetlaną szablonu i opis można zdefiniować metadanych. Te metadane musi znajdować się w pliku o nazwie `metadata.json`. Następujący przykładowy plik metadanych ilustruje sposób określ nazwę wyświetlaną i opis: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

Następujące kroki prowadzące przez dodawanie repozytorium do laboratorium przy użyciu portalu Azure. 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz żądany laboratorium.   
1. W laboratorium **omówienie** okienku wybierz **konfiguracji i zasadach**.

    ![Konfiguracja i zasady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z **konfiguracji i zasadach** ustawienia z listy wybierz **repozytoria**. **Repozytoria** okienku listy repozytoriów, które zostały dodane do laboratorium. Repozytorium o nazwie `Public Repo` jest generowane automatycznie dla wszystkich laboratoriów i łączy się z [repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) zawierający kilku artefaktów maszyny Wirtualnej do użycia.

    ![Publicznego repozytorium](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Wybierz **Dodaj +** do dodania do repozytorium szablonu usługi Azure Resource Manager.
1. Gdy drugi **repozytoria** otwiera okienku, wprowadź niezbędne informacje w następujący sposób:
    - **Nazwa** — wprowadź nazwę repozytorium, która jest używana w środowisku laboratoryjnym.
    - **Adres URL klonowania Git** — wprowadź adres URL klonowania GIT HTTPS z usługi GitHub lub Visual Studio Team Services.  
    - **Gałąź** — wprowadź nazwę gałęzi, aby uzyskiwać dostęp do definicji szablonu usługi Azure Resource Manager. 
    - **Osobisty token dostępu** -osobisty token dostępu jest używany do bezpiecznego dostępu do repozytorium. Aby uzyskać token z Visual Studio Team Services, wybierz  **&lt;twojanazwa >> Mój profil > Zabezpieczenia > token dostępu publicznego**. Aby uzyskać token z serwisu GitHub, wybierz Awatar, a następnie wybierając **Ustawienia > token dostępu publicznego**. 
    - **Ścieżka folderu** — przy użyciu jednej z dwóch pól wejściowych, wprowadź ścieżkę folderu, który rozpoczyna się od ukośnika - i - i jest określana względem programu Git clone URI jednej definicji artefaktów (pierwsze pole wejściowe) lub do definicji szablonu usługi Azure Resource Manager .   
    
        ![Publicznego repozytorium](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Gdy wszystkie wymagane pola są wprowadzane i przeszedł sprawdzania poprawności, wybierz **zapisać**.

Następna sekcja przeprowadzi Cię przez proces tworzenia środowisk z szablonem usługi Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Utwórz środowisko z szablonem usługi Resource Manager przy użyciu portalu Azure

Repozytorium szablonu usługi Azure Resource Manager został skonfigurowany w laboratorium, użytkownicy laboratorium tworzyć środowiska przy użyciu portalu Azure następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz żądany laboratorium.   
1. W okienku laboratorium wybierz **Dodaj +**.
1. **Wybierz podstawowej** okienko zawierające obrazy podstawowe korzystania z szablonów usługi Azure Resource Manager wyświetlana na początku listy. Wybierz odpowiedniego szablonu usługi Azure Resource Manager.

    ![Wybierz podstawowej](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Na **Dodaj** okienku, wprowadź **Nazwa środowiska** wartość. Nazwa środowiska to, co jest wyświetlane użytkownikom w laboratorium. Pozostałe pola wejściowe są definiowane w szablonie usługi Azure Resource Manager. Jeśli wartości domyślne są zdefiniowane w szablonie lub `azuredeploy.parameter.json` plik jest obecny, wartości domyślne są wyświetlane w tych pól wejściowych. Dla parametrów typu *bezpieczny ciąg*, można użyć hasła przechowywane w laboratorium [magazynie osobistym tajny](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store).

    ![Dodawanie okienka](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Istnieje kilka wartości parametrów, które — nawet wtedy, gdy określono — są wyświetlane jako wartości puste. W związku z tym jeśli użytkownicy przypisać te wartości parametrów szablonu usługi Azure Resource Manager, DevTest Labs nie zawiera wartości. Zamiast tego puste pola wejściowe są wyświetlane, gdy laboratorium, użytkownicy muszą wprowadzić wartość, podczas tworzenia środowiska.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN--PUB-KLUCZA SSH
    > - GEN-PASSWORD 
 
1. Wybierz **Dodaj** można utworzyć środowiska. Środowisko rozpoczyna się natychmiast dostarczania z wyświetlanie stanu w **Moje maszyny wirtualne** listy. Nowa grupa zasobów jest tworzony automatycznie przez laboratorium w celu zapewnienia wszystkich zasobów, które są zdefiniowane w szablonie usługi Azure Resource Manager.
1. Po utworzeniu środowiska, wybierz środowisko w **Moje maszyny wirtualne** listy, aby otworzyć okienko grupy zasobów i Przeglądaj wszystkie zasoby udostępniane w środowisku.
    
    ![Lista maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Można również rozwinąć środowiska, aby wyświetlić tylko listę maszyn wirtualnych, które są udostępniane w środowisku.
    
    ![Lista maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Kliknij dowolny z tych środowisk, aby wyświetlić dostępne akcje — takie jak stosowanie artefakty dołączanie dysków z danymi, zmiana czasu automatycznego zamykania i inne.

    ![Akcje środowiska](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Wdrażanie szablonu usługi Resource Manager, aby utworzyć Maszynę wirtualną
Po zapisać szablon usługi Resource Manager i dostosować go do swoich potrzeb, można go zautomatyzować tworzenie maszyny Wirtualnej. 
- [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) opisano, jak wdrażanie zasobów na platformie Azure przy użyciu programu Azure PowerShell z szablonami usługi Resource Manager. 
- [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) opisano sposób użycia interfejsu wiersza polecenia Azure z szablonami usługi Resource Manager do wdrażania zasobów platformy Azure.

> [!NOTE]
> Tylko użytkownik z uprawnieniami właściciela laboratorium można utworzyć maszyny wirtualne z szablonem usługi Resource Manager przy użyciu programu Azure PowerShell. Jeśli chcesz zautomatyzować tworzenie maszyny Wirtualnej przy użyciu szablonu usługi Resource Manager i tylko mają uprawnienia użytkowników, można użyć [ **tworzenia maszyny wirtualnej laboratorium az** w interfejsu wiersza polecenia polecenie](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).

### <a name="general-limitations"></a>Ogólne ograniczenia 

Przy użyciu szablonu usługi Resource Manager w usłudze DevTest Labs, należy wziąć pod uwagę następujące ograniczenia:

- Wszelkie tworzonego szablonu usługi Resource Manager nie może odwoływać się do istniejących zasobów; go można odwoływać się tylko do nowych zasobów. Ponadto jeśli masz istniejący szablon usługi Resource Manager wdrażanego zwykle poza DevTest Labs i zawiera odwołania do istniejących zasobów, nie można użyć w środowisku laboratoryjnym.

   Jedynym wyjątkiem jest to, że możesz **można** odwołania istniejącej sieci wirtualnej. 

- Nie można utworzyć formuły z laboratorium maszyn wirtualnych, które zostały utworzone na podstawie szablonu usługi Resource Manager. 

- Nie można utworzyć niestandardowe obrazy z laboratorium maszyn wirtualnych, które zostały utworzone na podstawie szablonu usługi Resource Manager. 

- Większość zasad nie są oceniane podczas wdrażania usługi Resource Manager szablonów.

   Przykładowo jeśli masz zasady z laboratorium określenie, czy użytkownik może utworzyć tylko 5 maszyn wirtualnych. Jednak jeśli użytkownik wdraża szablonu usługi Resource Manager tworzącą dziesiątki maszyn wirtualnych, które jest dozwolone. Zasady, które nie są oceniane obejmują:

   - Liczba maszyn wirtualnych dla poszczególnych użytkowników
   - Liczba maszyn wirtualnych premium dla każdego użytkownika laboratorium
   - Liczba dysków w warstwie premium dla każdego użytkownika laboratorium


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Konfigurowanie praw dostępu grupy zasobów środowiska dla użytkowników laboratorium

Laboratorium użytkownicy będą mogli wdrażać szablonu usługi Resource Manager. Jednak domyślnie mają prawa dostępu czytelnika, co oznacza, że nie mogą zmienić zasoby w grupie zasobów w środowisku. Na przykład nie można zatrzymać lub uruchomić ich zasobów.

Wykonaj następujące kroki, aby zapewnić użytkownikom laboratorium współautora praw dostępu. Następnie po wdrożeniu przez nich szablonu usługi Resource Manager, będą oni mogli edytować zasobów w tym środowisku. 


1. Na środowiska laboratoryjnego **omówienie** okienku wybierz **konfiguracji i zasadach**.
1. Wybierz **ustawienia laboratorium**.
1. Wybierz w okienku ustawień laboratorium **współautora** udzielenia uprawnienia do zapisu użytkownikom laboratorium.

    ![Konfigurowanie uprawnień dostępu użytkownika laboratorium](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
* Po utworzeniu maszyny Wirtualnej można Połącz się z maszyną Wirtualną, wybierając **Connect** w okienku Zarządzanie maszyny Wirtualnej.
* Wyświetlanie i zarządzanie zasobami w środowisku, wybierając środowisko w **Moje maszyny wirtualne** listy w laboratorium. 
* Eksploruj [szablonów usługi Azure Resource Manager z galerii szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates).
