---
title: Konfigurowanie jednostek SKU dla oferty aplikacji platformy Azure | Azure Marketplace
description: Jak skonfigurować jednostki SKU dla aplikacji zarządzanej platformy Azure i szablonu rozwiązania platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289077"
---
# <a name="azure-application-skus-tab"></a>Karta Jednostki SKU aplikacji platformy Azure

W tym artykule opisano sposób używania karty Jednostki SKU do tworzenia jednostek SKU dla aplikacji platformy Azure. 

> [!IMPORTANT]
> Kroki konfigurowania jednostki SKU są różne dla oferty aplikacji zarządzanej i oferty szablonu rozwiązania. Różnice te są udokumentowane w tym artykule. 

## <a name="configure-azure-application-skus"></a>Konfigurowanie jednostek SKU aplikacji platformy Azure

### <a name="create-a-new-sku"></a>Tworzenie nowej jednostki SKU

Wykonaj następujące kroki, aby utworzyć nową jednostkę SKU:

1. Wybierz kartę **Jednostki SKU.**
2. W obszarze Jednostki SKU wybierz **pozycję + Nowa jednostka SKU**.

    ![Nowy monit sku](./media/azureapp-plus-sku.png)

3. W oknie podręcznym Nowa jednostka SKU wpisz identyfikator **jednostki SKU**. Ten identyfikator jest ograniczony do 50 znaków i musi składać się tylko z małych liter, znaków alfanumerycznych, kresek lub podkreśleń. Identyfikator jednostki SKU nie może zakończyć się kreską.
4. Identyfikator jednostki SKU jest widoczny dla klientów w adresach URL produktów, szablonach Menedżera zasobów (jeśli dotyczy) i raportach rozliczeniowych. Nie można zmodyfikować tego identyfikatora po opublikowaniu oferty.

### <a name="sku-details-for-a-solution-template"></a>Szczegółowe informacje o jednostce SKU dla szablonu rozwiązania

Przechwytywanie następnego ekranu pokazuje formularz Szczegóły jednostki SKU dla szablonu rozwiązania.

![Formularz szczegółów jednostki SKU dla szablonu rozwiązania](./media/azureapp-sku-details-solutiontemplate.png)

Podaj następujące wartości jednostek SKU.  Pola dołączone do gwiazdki są wymagane.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **Tytuł\***     | Tytuł jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość to 100 znaków).  |
| **Opis\*** | Szczegółowy opis jednostki SKU. Podstawowy kod HTML jest obsługiwany.                 | 
| **Typ jednostki SKU\***   | Typ rozwiązania aplikacji platformy Azure, wybierz ***Szablon rozwiązania** dla tego scenariusza. |
| **Dostępność chmury\*** | Lokalizacja jednostki SKU. Wartością domyślną jest **publiczna platforma Azure**.  <b/>   **Publiczna platforma Azure** — aplikacja będzie można wdrożyć dla klientów we wszystkich regionach platformy Azure publicznych, które mają integrację z rynkiem.  <b/>   **Usługa Azure Government Cloud** — aplikacja zostanie wdrożona w chmurze azure dla instytucji rządowych. Przed opublikowaniem na [platformie Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)firma Microsoft zaleca wydawcom przetestowanie i sprawdzenie poprawności ich rozwiązania zgodnie z oczekiwaniami w tym środowisku. Aby rozpocząć i przetestować, poproś o [konto próbne](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Czy jest to prywatna jednostka SKU?\*** | Wybierz **opcję Tak,** jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy odbiorców. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government to chmura społeczności rządowej z kontrolowanym dostępem dla klientów z amerykańskich partnerów federalnych, stanowych, lokalnych lub plemiennych oraz uprawnionych do obsługi tych jednostek.


### <a name="sku-details-for-managed-application"></a>Szczegóły usługi SKU dla aplikacji zarządzanej

Przechwytywanie następnego ekranu pokazuje formularz Szczegóły jednostki SKU dla aplikacji zarządzanej.

   ![Formularz szczegółów jednostki SKU dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapplication.png)

Skonfiguruj następujące ustawienia jednostki SKU. Pola dołączone do gwiazdki są wymagane.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **Tytuł\***     | Tytuł jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość to 100 znaków).  |
| **Opis\*** | Szczegółowy opis jednostki SKU. Podstawowy kod HTML jest obsługiwany.                 | 
| **Typ jednostki SKU\***   | Typ rozwiązania aplikacji platformy Azure, wybierz ***aplikacja zarządzana** dla tego scenariusza. 
| **Dostępność chmury\*** | Lokalizacja jednostki SKU. Wartością domyślną jest **publiczna platforma Azure**.  <b/>   **Publiczna platforma Azure** — aplikacja będzie można wdrożyć dla klientów we wszystkich regionach platformy Azure publicznych, które mają integrację z rynkiem.  <b/>   **Usługa Azure Government Cloud** — aplikacja zostanie wdrożona w chmurze azure dla instytucji rządowych. Przed opublikowaniem na [platformie Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)firma Microsoft zaleca wydawcom przetestowanie i sprawdzenie poprawności ich rozwiązania zgodnie z oczekiwaniami w tym środowisku. Aby rozpocząć i przetestować, poproś o [konto próbne](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government to chmura społeczności rządowej z kontrolowanym dostępem dla klientów z amerykańskich partnerów federalnych, stanowych, lokalnych lub plemiennych oraz uprawnionych do obsługi tych jednostek. |
| **Czy jest to prywatna jednostka SKU?\*** | Wybierz **opcję Tak,** jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy odbiorców. |
| **Dostępność kraju/regionu\*** | Użyj **wybierz regiony,** aby wyświetlić listę krajów/regionów, które są dostępne. Sprawdź każdy kraj/region, a następnie wybierz **przycisk OK,** aby zapisać swoje typy.  <b/>   ![Lista dostępności krajów i regionów](./media/azure-app-select-country-region.png)  |
| **Stare ceny\*** | Cena jednostki SKU w USD miesięcznie. Ceny są ustalane w walucie lokalnej przy użyciu bieżących kursów wymiany walut po konfiguracji. Sprawdź je, ponieważ ostatecznie właścicielem tych ustawień. Aby ustawić lub wyświetlić cenę każdego kraju/regionu indywidualnie, eksportuj arkusz kalkulacyjny cen i importuj z cenami niestandardowymi.  Aby włączyć eksport/import danych cenowych, należy zapisać zmiany cen.  |
| **Uproszczone ceny walut\*** | Cena jednostki SKU w USD miesięcznie. Musi to być taka sama jak stare ceny. Aby uzyskać więcej informacji, zobacz [Uproszczone ceny walut](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Szczegóły pakietu dla szablonu rozwiązania

![Szczegóły pakietu dla szablonu rozwiązania](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Podaj następujące wartości **szczegółów pakietu.**  Pola dołączone do gwiazdki są wymagane.

- **Wersja\* ** — wersja pakietu, który zostanie przesłany. Tagi wersji muszą mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- **Plik pakietu (.zip)\* ** — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - **mainTemplate.json\* ** — plik szablonu wdrożenia, który jest używany do wdrażania rozwiązania/aplikacji i tworzenia zasobów zdefiniowanych dla rozwiązania. Aby uzyskać więcej informacji, zobacz [Jak tworzyć pliki szablonów wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition.json\* ** — ten plik jest używany przez portal Azure do generowania interfejsu użytkownika do inicjowania obsługi administracyjnej tego rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika portalu Azure dla zarządzanej aplikacji](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Skrypty (jeśli jest to wymagane) — wszelkie dodatkowe skrypty, które `Microsoft.Compute/virtualMachines/extensions`mogą być wymagane podczas uruchamiania szablonu, na przykład .
  - Szablony zagnieżdżone (jeśli jest to wymagane) — wszelkie dodatkowe szablony zagnieżdżone.

  > [!IMPORTANT] 
  > Ten pakiet powinien zawierać wszystkie zagnieżdżone szablony lub skrypty, które są potrzebne do inicjowania obsługi administracyjnej tej aplikacji. Plik mainTemplate.json i plik createUIDefinition.json muszą znajdować się w folderze głównym. Aby uzyskać więcej informacji na temat artefaktów wdrażania, zobacz [Przewodnik po szablonach usługi Azure Resource Manager — przewodnik po sprawdzonych praktykach](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Szczegóły pakietu dla aplikacji zarządzanej

   ![Szczegóły pakietu dla aplikacji zarządzanej](./media/azureapp-sku-pkgdetails-managedapplication.png)

Podaj następujące szczegóły pakietu.  Pola dołączone do gwiazdki są wymagane.

- **Wersja\* ** — wersja pakietu, który zostanie przesłany. Tagi wersji muszą mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- **Plik pakietu (.zip)\* ** — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - applianceMainTemplate.json — plik szablonu wdrażania, który jest używany do wdrażania rozwiązania/aplikacji i tworzenia zasobów, które są zdefiniowane. Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json — ten plik jest używany przez portal Azure do generowania interfejsu użytkownika do inicjowania obsługi administracyjnej tego rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika portalu Azure dla zarządzanej aplikacji](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json — plik szablonu zawierający tylko zasób Microsoft.Solution/appliances. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Należy zwrócić uwagę na następujące właściwości klucza tego zasobu:
    - "kind" — wartość powinna być "Marketplace" w przypadku aplikacji zarządzanej przez Marketplace.
    - "ManagedResourceGroupId" — grupa zasobów w subskrypcji klienta, w której zostaną wdrożone wszystkie zasoby zdefiniowane w urządzeniuMainTemplate.json.
    - "PublisherPackageId"- Ciąg, który jednoznacznie identyfikuje pakiet. Ta wartość musi być skonstruowana w następujący sposób: jest to łączenie [publisherId]. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Ten pakiet powinien zawierać wszystkie zagnieżdżone szablony lub skrypty, które są potrzebne do inicjowania obsługi administracyjnej tej aplikacji. Pliki te muszą znajdować się w folderze głównym: MainTemplate.json, applianceMainTemplate.json i applianceCreateUIDefinition.json.

- **\* Identyfikator dzierżawy** — identyfikator dzierżawy usługi Azure Active Directory w organizacji.
- **Czy włączyć dostęp JIT? \* ** - Wybierz **tak,** aby włączyć dostęp do zarządzania just-in-time dla wdrożeń klientów korzystających z tej oferty.

  >[!NOTE] 
  >Jeśli włączysz JIT, należy zaktualizować plik CreateUiDefinition.json do obsługi dostępu JIT.

W przypadku aplikacji zarządzanej należy skonfigurować ustawienia autoryzacji i zasad.


#### <a name="authorization"></a>Autoryzacja

Dodaj identyfikator usługi Azure Active Directory użytkownika, grupy lub aplikacji, do której chcesz udzielić uprawnień do zarządzanej grupy zasobów. Uprawnienie, które jest przyznawane jest wskazywane przez identyfikator definicji roli. Może to być właściciel, współautor lub dowolnej roli niestandardowej.


#### <a name="policy-settings"></a>Zasady Ustawienia

Dodaj zasady, które jest zgodne z aplikacją zarządzana. Dowiedz się więcej o zasadach dotyczących zasobów platformy Azure, zobacz [Co to jest zasady platformy Azure?](../../../governance/policy/overview.md)

   ![Ustawienia autoryzacji i zasad dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Aby utworzyć nową autoryzację:**

1. W obszarze **Autoryzacja**wybierz **pozycję + Nowa autoryzacja**.
2. W przypadku **identyfikatora zabezpieczeń**wpisz identyfikator użytkownika, grupy lub aplikacji usługi Azure Active Directory, do której chcesz udzielić uprawnień do zarządzanej grupy zasobów. Uprawnienie, które jest przyznawane jest wskazywane przez definicję roli.
3. W przypadku **opcji Definicja roli**wybierz jedną z tych opcji z listy rozwijanej: Właściciel lub Współautor. Aby uzyskać więcej informacji, zobacz temat [Wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Można dodać wiele autoryzacji. Jednak zaleca się utworzenie grupy użytkowników usługi Active Directory i określenie jej identyfikatora w "PrincipalId". Umożliwi to dodanie większej liczby użytkowników do grupy użytkowników bez konieczności aktualizowania jednostki SKU.

**Aby utworzyć nową zasadę:**

1. W obszarze **Ustawienia zasad**wybierz pozycję + **Nowe zasady**.
2. W **ywanie Nazwa zasad**wprowadź nazwę zasad. Maksymalna długość nazwy wynosi 50 znaków.
3. W obszarze **Zasady**wybierz jedną z opcji z listy rozwijanej. Wybierz zasady, które dostawca danych chce włączyć, gdy aplikacja używa danych. Aby uzyskać więcej informacji, zobacz [przykłady zasad platformy Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Ustawienia zasad dla aplikacji zarządzanej](./media/azureapp-sku-policy-settings.png)

4. W przypadku **jednostki SKU zasad**wybierz opcję Bezpłatna lub Standardowa jako typ jednostki SKU zasad. Standardowa jednostka SKU jest wymagana dla zasad inspekcji.


## <a name="next-steps"></a>Następne kroki

Dalsze opisywać swoją ofertę i dostarczać zasoby marketingowe na [karcie Marketplace](./cpp-marketplace-tab.md). 
