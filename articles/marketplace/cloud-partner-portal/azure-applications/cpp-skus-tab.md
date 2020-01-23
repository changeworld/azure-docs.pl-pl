---
title: Skonfiguruj jednostki SKU dla oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Jak skonfigurować jednostki SKU dla aplikacji zarządzanej przez platformę Azure i szablonu rozwiązania platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543158"
---
# <a name="azure-application-skus-tab"></a>Karta jednostki SKU aplikacji platformy Azure

W tym artykule opisano, jak za pomocą karty SKU utworzyć jednostki SKU dla aplikacji platformy Azure. 

> [!IMPORTANT]
> Kroki konfigurowania jednostki SKU różnią się w zależności od aplikacji zarządzanej i oferty szablonu rozwiązania. Te różnice opisano w tym artykule. 

## <a name="configure-azure-application-skus"></a>Konfigurowanie jednostek SKU aplikacji platformy Azure

### <a name="create-a-new-sku"></a>Utwórz nową jednostkę SKU

Wykonaj następujące kroki, aby utworzyć nową jednostkę SKU:

1. Wybierz kartę **jednostki SKU** .
2. W obszarze jednostki SKU wybierz pozycję **+ Nowa jednostka SKU**.

    ![Nowy monit jednostki SKU](./media/azureapp-plus-sku.png)

3. W oknie podręcznym nowe jednostki SKU wpisz **Identyfikator jednostki SKU**. Ten identyfikator jest ograniczony do 50 znaków i musi zawierać tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. Identyfikator jednostki SKU nie może kończyć się znakiem kreski.
4. Identyfikator jednostki SKU jest widoczny dla klientów w adresach URL produktów, Menedżer zasobów szablonów (jeśli dotyczy) i raportów dotyczących rozliczeń. Nie można zmodyfikować tego identyfikatora po opublikowaniu oferty.

### <a name="sku-details-for-a-solution-template"></a>Szczegóły jednostki SKU dla szablonu rozwiązania

Przechwytywanie następnego ekranu pokazuje formularz szczegóły jednostki SKU dla szablonu rozwiązania.

![Formularz szczegółów jednostki SKU dla szablonu rozwiązania](./media/azureapp-sku-details-solutiontemplate.png)

Podaj następujące wartości jednostki SKU.  Pola dołączone do gwiazdki są wymagane.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **\* tytułu**     | Tytuł jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość to 100 znaków).  |
| **\* opisu** | Szczegółowy opis jednostki SKU. Podstawowy kod HTML jest obsługiwany.                 | 
| **\* typu jednostki SKU**   | Typ rozwiązania aplikacji platformy Azure, wybierz pozycję ***szablon rozwiązania** dla tego scenariusza. |
| **Dostępność w chmurze\*** | Lokalizacja jednostki SKU. Wartość domyślna to **publiczna platforma Azure**.  <b/>**publiczną platformę Azure** — aplikacja zostanie wdrożona dla klientów we wszystkich publicznych regionach świadczenia usługi Azure z integracją z portalu Marketplace.  <b/>**Azure Government Cloud** — aplikacja zostanie wdrożona w chmurze Azure Government. Przed opublikowaniem w [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), firma Microsoft zaleca, aby wydawcy przetestować i sprawdzali, czy rozwiązanie działa zgodnie z oczekiwaniami w tym środowisku. Aby przygotować się do etapu i przetestowania, zażądaj [konta próbnego](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Czy jest to prywatna jednostka SKU?\*** | Wybierz opcję **tak** , jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy klientów. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government to chmura dla instytucji rządowych, której dostęp jest kontrolowany przez klientów ze stanów USA, stan, lokalny lub plemienne oraz partnerzy kwalifikowania się do korzystania z tych jednostek.


### <a name="sku-details-for-managed-application"></a>Szczegóły jednostki SKU dla aplikacji zarządzanej

Przechwytywanie następnego ekranu pokazuje formularz szczegóły jednostki SKU dla aplikacji zarządzanej.

   ![Formularz szczegółów jednostki SKU dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapplication.png)

Skonfiguruj następujące ustawienia jednostki SKU. Pola dołączone do gwiazdki są wymagane.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **\* tytułu**     | Tytuł jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość to 100 znaków).  |
| **\* opisu** | Szczegółowy opis jednostki SKU. Podstawowy kod HTML jest obsługiwany.                 | 
| **\* typu jednostki SKU**   | Typ rozwiązania aplikacji platformy Azure, wybierz pozycję ***zarządzana aplikacja** dla tego scenariusza. 
| **Dostępność w chmurze\*** | Lokalizacja jednostki SKU. Wartość domyślna to **publiczna platforma Azure**.  <b/>**publiczną platformę Azure** — aplikacja zostanie wdrożona dla klientów we wszystkich publicznych regionach świadczenia usługi Azure z integracją z portalu Marketplace.  <b/>**Azure Government Cloud** — aplikacja zostanie wdrożona w chmurze Azure Government. Przed opublikowaniem w [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), firma Microsoft zaleca, aby wydawcy przetestować i sprawdzali, czy rozwiązanie działa zgodnie z oczekiwaniami w tym środowisku. Aby przygotować się do etapu i przetestowania, zażądaj [konta próbnego](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government to chmura dla instytucji rządowych, której dostęp jest kontrolowany przez klientów ze stanów USA, stan, lokalny lub plemienne oraz partnerzy kwalifikowania się do korzystania z tych jednostek. |
| **Czy jest to prywatna jednostka SKU?\*** | Wybierz opcję **tak** , jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy klientów. |
| **\* dostępności kraju/regionu** | Użyj **opcji wybierz regiony** , aby wyświetlić listę dostępnych krajów/regionów. Sprawdź każdy kraj/region, a następnie wybierz pozycję **OK** , aby zapisać swoje propozycje.  <b/>![listy dostępności kraju i regionu](./media/azure-app-select-country-region.png)  |
| **Stara\* cenowe** | Cena jednostki SKU w USD miesięcznie. Ceny są ustawiane w walucie lokalnej przy użyciu bieżących kursów wymiany podczas konfiguracji. Sprawdź poprawność tych ustawień, ponieważ są one ostatecznie własne. Aby ustawić lub wyświetlić poszczególne ceny poszczególnych krajów/regionów, należy wyeksportować arkusz kalkulacyjny z cennikiem i zaimportować go przy użyciu cennika niestandardowego.  Musisz zapisać zmiany cen, aby włączyć eksportowanie/Importowanie danych cen.  |
| **Uproszczone ceny walutowe\*** | Cena jednostki SKU w USD miesięcznie. Ta wartość musi być taka sama jak w przypadku starych cen. Aby uzyskać więcej informacji, zobacz [uproszczone ceny walutowe](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Szczegóły pakietu dla szablonu rozwiązania

![Szczegóły pakietu dla szablonu rozwiązania](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Podaj następujące wartości **szczegółów pakietu** .  Pola dołączone do gwiazdki są wymagane.

- **Wersja\*** — wersja pakietu, który zostanie przekazany. Tagi wersji muszą mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi.
- **\*pliku pakietu (zip)** — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - **mainTemplate. json\*** — plik szablonu wdrożenia używany do wdrożenia rozwiązania/aplikacji i tworzenia zasobów zdefiniowanych dla rozwiązania. Aby uzyskać więcej informacji, zobacz [jak tworzyć pliki szablonów wdrożenia](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition. json\*** — ten plik jest używany przez Azure Portal do generowania interfejsu użytkownika na potrzeby aprowizacji tego rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika Azure Portal dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Skrypty (jeśli są wymagane) — wszelkie dodatkowe skrypty, które mogą być wymagane podczas uruchamiania szablonu, na przykład `Microsoft.Compute/virtualMachines/extensions`.
  - Szablony zagnieżdżone (w razie potrzeby) — wszystkie dodatkowe zagnieżdżone szablony.

  > [!IMPORTANT] 
  > Ten pakiet powinien zawierać wszelkie zagnieżdżone szablony lub skrypty, które są potrzebne do aprowizacji tej aplikacji. Plik mainTemplate. JSON i plik createUIDefinition. JSON muszą znajdować się w folderze głównym. Aby uzyskać więcej informacji na temat artefaktów wdrożenia, zobacz [szablony Azure Resource Manager — przewodnik po najlepszych rozwiązaniach](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Szczegóły pakietu dla aplikacji zarządzanej

   ![Szczegóły pakietu dla aplikacji zarządzanej](./media/azureapp-sku-pkgdetails-managedapplication.png)

Podaj następujące szczegóły pakietu.  Pola dołączone do gwiazdki są wymagane.

- **Wersja\*** — wersja pakietu, który zostanie przekazany. Tagi wersji muszą mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi.
- **\*pliku pakietu (zip)** — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - applianceMainTemplate. JSON — plik szablonu wdrożenia używany do wdrożenia rozwiązania/aplikacji i tworzenia zdefiniowanych zasobów. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON — ten plik jest używany przez Azure Portal do generowania interfejsu użytkownika na potrzeby aprowizacji tego rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika Azure Portal dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate. JSON — plik szablonu zawierający tylko zasób Microsoft. Solution/urządzenia. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Zwróć uwagę na następujące właściwości klucza tego zasobu:
    - "Kind" — wartość powinna być "Marketplace" w przypadku aplikacji zarządzanej przez witrynę Marketplace.
    - "ManagedResourceGroupId" — Grupa zasobów w subskrypcji klienta, w której zostaną wdrożone wszystkie zasoby zdefiniowane w pliku applianceMainTemplate. JSON.
    - "PublisherPackageId" — ciąg, który jednoznacznie identyfikuje pakiet. Ta wartość musi być skonstruowana w następujący sposób: jest to połączenie [publisherId]. [OfferId] — wersja zapoznawcza [identyfikatora SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Ten pakiet powinien zawierać wszelkie zagnieżdżone szablony lub skrypty, które są potrzebne do aprowizacji tej aplikacji. Te pliki muszą znajdować się w folderze głównym: MainTemplate. JSON, applianceMainTemplate. JSON i applianceCreateUIDefinition. JSON.

- **Identyfikator dzierżawy\*** — identyfikator dzierżawy Azure Active Directory organizacji.
- **Włączyć dostęp JIT?\*** -wybierz opcję **tak** , aby włączyć dostęp do usługi zarządzania just-in-Time dla wdrożeń klientów przy użyciu tej oferty.

  >[!NOTE] 
  >W przypadku włączenia JIT należy zaktualizować plik CreateUiDefinition. JSON, aby obsługiwał dostęp JIT.

W przypadku aplikacji zarządzanej należy skonfigurować ustawienia autoryzacji i zasad.


#### <a name="authorization"></a>Autoryzacja

Dodaj Azure Active Directory identyfikator użytkownika, grupy lub aplikacji, do której chcesz udzielić uprawnienia do zarządzanej grupy zasobów. Przyznane uprawnienie jest wskazywane przez identyfikator definicji roli. Może być właścicielem, współautorem lub dowolną rolą niestandardową.


#### <a name="policy-settings"></a>Ustawienia zasad

Dodaj zasady, z którymi jest zgodna aplikacja zarządzana. Dowiedz się więcej na temat zasad zasobów platformy Azure, zobacz [co to jest Azure Policy?](../../../governance/policy/overview.md)

   ![Ustawienia autoryzacji i zasad dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Aby utworzyć nową autoryzację:**

1. W obszarze **autoryzacja**wybierz pozycję **+ Nowa autoryzacja**.
2. Dla **identyfikatora podmiotu zabezpieczeń**wpisz identyfikator Azure Active Directory użytkownika, grupy lub aplikacji, do której chcesz udzielić uprawnienia do zarządzanej grupy zasobów. Przyznane uprawnienie jest wskazywane przez definicję roli.
3. W obszarze **definicja roli**wybierz jedną z opcji z listy rozwijanej: właściciel lub współautor. Aby uzyskać więcej informacji, zobacz temat [Wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Można dodać wiele autoryzacji. Zaleca się jednak utworzenie grupy użytkowników Active Directory i określenie jej identyfikatora w "PrincipalId". Umożliwi to dodanie większej liczby użytkowników do grupy użytkowników bez konieczności aktualizowania jednostki SKU.

**Aby utworzyć nowe zasady:**

1. W obszarze **Ustawienia zasad**wybierz pozycję **+ nowe zasady**.
2. W polu **Nazwa zasad**wprowadź nazwę zasady. Maksymalna długość nazwy to 50 znaków.
3. W przypadku **zasad**wybierz jedną z opcji z listy rozwijanej. Wybierz zasady, które dostawca danych chce włączyć, gdy aplikacja korzysta z danych. Aby uzyskać więcej informacji, zobacz [przykłady Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Ustawienia zasad dla aplikacji zarządzanej](./media/azureapp-sku-policy-settings.png)

4. W przypadku **jednostki SKU zasad**wybierz opcję bezpłatna lub standardowa jako typ jednostki SKU zasad. Dla zasad inspekcji wymagana jest standardowa jednostka SKU.


## <a name="next-steps"></a>Następne kroki

Na [karcie Marketplace](./cpp-marketplace-tab.md)znajdziesz dokładniejsze informacje o ofercie i dostarczenie zasobów marketingowych. 
