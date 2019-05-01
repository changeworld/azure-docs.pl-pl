---
title: Konfigurowanie jednostki SKU dla oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Jak skonfigurować jednostki SKU dla platformy Azure zarządzanych aplikacji i szablon rozwiązania platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: ef4ea2419c64d0376023ea5d291460df48a51c63
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943448"
---
# <a name="azure-application-skus-tab"></a>Karta jednostki SKU aplikacji platformy Azure

W tym artykule opisano, jak utworzyć jednostki SKU dla aplikacji platformy Azure przy użyciu karty jednostki SKU. 

> [!IMPORTANT]
> Procedura konfigurowania jednostki SKU są różne dla oferty aplikacji zarządzanych i oferty szablonu rozwiązania. Te różnice są opisane w tym artykule. 

## <a name="configure-azure-application-skus"></a>Konfigurowanie aplikacji platformy Azure jednostki SKU

### <a name="create-a-new-sku"></a>Tworzenie nowej jednostki SKU

Wykonaj następujące kroki, aby utworzyć nowe jednostki SKU:

1. Wybierz **jednostki SKU** kartę.
2. W obszarze jednostek SKU, wybierz **+ nowej jednostki SKU**.

    ![Nowy wiersz w jednostce SKU](./media/azureapp-plus-sku.png)

3. W oknie podręcznym nowej jednostki SKU wpisz **identyfikator jednostki SKU**. Ten identyfikator jest ograniczona do 50 znaków i musi zawierać tylko znaków alfanumerycznych, małe litery, kreski i znaki podkreślenia. Identyfikator jednostki SKU nie może kończyć się kreską.
4. Identyfikator jednostki SKU jest widoczne dla klientów w adresach URL produktu, szablonów usługi Resource Manager (jeśli dotyczy) i raporty rozliczeń. Nie można zmodyfikować tego identyfikatora, po jego opublikowaniu oferty.

### <a name="sku-details-for-a-solution-template"></a>Szczegóły jednostki SKU dla szablonu rozwiązania

Następny zrzut ekranu przedstawia formularz szczegółów jednostki SKU dla szablonu rozwiązania.

![Formularz szczegółów jednostki SKU, szablon rozwiązania](./media/azureapp-sku-details-solutiontemplate.png)

Podaj następujące wartości jednostki SKU.  Wymagane są pola dołączany gwiazdką.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **Tytuł\***     | Tytuł dla jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość wynosi 100 znaków).  |
| **Opis elementu\*** | Szczegółowy opis jednostki SKU. Podstawowe HTML jest obsługiwane.                 | 
| **Typ jednostki SKU\***   | Typ rozwiązania aplikacji platformy Azure, wybierz pozycję ***szablon rozwiązania** dla tego scenariusza. |
| **Dostępności w chmurze\*** | Lokalizacja jednostki SKU. Wartość domyślna to **publicznej Azure**.  <b/>   **Publiczna platforma Azure** — aplikacja będzie można wdrożyć dla klientów we wszystkich publicznych regionach platformy Azure, które mają integrację portalu marketplace.  <b/>   **Chmury platformy Azure dla instytucji rządowych** — aplikacja zostanie wdrożona w chmurze Azure Government. Przed opublikowaniem do [Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), firma Microsoft zaleca wydawców, testowanie i weryfikowanie ich działa zgodnie z oczekiwaniami, w tym środowisku. Przygotuj i przetestować, żądania [konto próbne](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Jest to prywatne jednostki SKU\*** | Wybierz **tak** Jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy klientów. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure dla instytucji rządowych to chmura dla instytucji rządowych i społeczności, przy użyciu kontrolowanego dostępu dla klientów z USA federalnych, stan, lokalnej lub plemiennej partnerów i kwalifikujących się do obsługi tych jednostek.


### <a name="sku-details-for-managed-application"></a>Szczegóły jednostki SKU dla aplikacji zarządzanej

Następny zrzut ekranu przedstawia formularz szczegółów jednostki SKU dla zarządzanej aplikacji.

   ![Formularz szczegółów jednostki SKU dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapplication.png)

Skonfiguruj następujące ustawienia jednostki SKU. Wymagane są pola dołączany gwiazdką.

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
|  **Tytuł\***     | Tytuł dla jednostki SKU. Ten tytuł jest wyświetlany w galerii dla tego elementu.   |
| **Podsumowanie\***    | Krótki opis podsumowania jednostki SKU. (Maksymalna długość wynosi 100 znaków).  |
| **Opis elementu\*** | Szczegółowy opis jednostki SKU. Podstawowe HTML jest obsługiwane.                 | 
| **Typ jednostki SKU\***   | Typ rozwiązania aplikacji platformy Azure, wybierz pozycję ***zarządzanej aplikacji** dla tego scenariusza. 
| **Dostępności w chmurze\*** | Lokalizacja jednostki SKU. Wartość domyślna to **publicznej Azure**.  <b/>   **Publiczna platforma Azure** — aplikacja będzie można wdrożyć dla klientów we wszystkich publicznych regionach platformy Azure, które mają integrację portalu marketplace.  <b/>   **Chmury platformy Azure dla instytucji rządowych** — aplikacja zostanie wdrożona w chmurze Azure Government. Przed opublikowaniem do [Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), firma Microsoft zaleca wydawców, testowanie i weryfikowanie ich działa zgodnie z oczekiwaniami, w tym środowisku. Przygotuj i przetestować, żądania [konto próbne](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure dla instytucji rządowych to chmura dla instytucji rządowych i społeczności, przy użyciu kontrolowanego dostępu dla klientów z USA federalnych, stan, lokalnej lub plemiennej partnerów i kwalifikujących się do obsługi tych jednostek. |
| **Jest to prywatne jednostki SKU\*** | Wybierz **tak** Jeśli ta jednostka SKU jest dostępna tylko dla wybranej grupy klientów. |
| **Dostępność Kraj/Region\*** | Użyj **Wybierz regiony** Aby wyświetlić listę krajów/regionów, które są dostępne. Sprawdź każdego kraju/regionu, a następnie wybierz **OK** można zapisać swoje propozycje.  <b/>   ![Lista dostępności krajów i regionów](./media/azure-app-select-country-region.png)  |
| **Stary ceny\*** | Cena dla jednostki SKU w USD miesięcznie. Ceny są ustawiane w walucie lokalnej przy użyciu bieżących kursów wymiany podczas konfiguracji. Zweryfikuj je, ponieważ ostatecznie samodzielnie tych ustawień. Aby ustawić lub wyświetlić poszczególne cena każdego kraju/regionu, eksportować arkusz cen i zaimportuj z ceny niestandardowe.  Musisz zapisać zmiany cen, aby włączyć eksportu/importu danych cen.  |
| **Cennik uproszczone waluty\*** | Cena dla jednostki SKU w USD miesięcznie. Musi to być takie same jak ceny stary. Aby uzyskać więcej informacji, zobacz [uproszczony ceny waluty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Szczegóły pakietu szablon rozwiązania

   ![Szczegóły pakietu szablon rozwiązania](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Podaj następujące **szczegóły pakietu** wartości.  Wymagane są pola dołączany gwiazdką.

- **Wersja\***  — wersja pakietu, który trzeba będzie przekazać. Tagi wersji musi być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- **Plik pakietu (zip)\***  — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - MainTemplate.json — pliki szablonu wdrożenia, który jest używany do wdrożenia rozwiązanie/aplikację i utworzyć zasoby definiowane dla rozwiązania. Aby uzyskać więcej informacji, zobacz [jak tworzyć pliki szablonu wdrożenia](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json — ten plik jest używany przez witryny Azure portal do wygenerowania interfejsu użytkownika do obsługi administracyjnej rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [interfejs użytkownika portalu tworzenie platformy Azure dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Ten pakiet powinien zawierać wszystkie zagnieżdżone szablony lub skrypty, które są potrzebne do aprowizowania tej aplikacji. Plik MainTemplate.json i plik createUIDefinition.json musi być w folderze głównym.


### <a name="package-details-for-managed-application"></a>Szczegóły pakietu aplikacji zarządzanej

   ![Szczegóły pakietu aplikacji zarządzanej](./media/azureapp-sku-pkgdetails-managedapplication.png)

Podaj następujące szczegóły pakietu.  Wymagane są pola dołączany gwiazdką.

- **Wersja\***  — wersja pakietu, który trzeba będzie przekazać. Tagi wersji musi być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- **Plik pakietu (zip)\***  — ten pakiet zawiera następujące pliki zapisane w pliku zip.
  - applianceMainTemplate.json — pliki szablonu wdrożenia, który służy do wdrażania rozwiązania/aplikacji oraz tworzenia zasobów, które są zdefiniowane. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json — ten plik jest używany przez witryny Azure portal do wygenerowania interfejsu użytkownika do obsługi administracyjnej rozwiązania/aplikacji. Aby uzyskać więcej informacji, zobacz [interfejs użytkownika portalu tworzenie platformy Azure dla aplikacji zarządzanej](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json — plik szablonu, który zawiera tylko zasób Microsoft.Solution/appliances. Aby uzyskać więcej informacji, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Należy zwrócić uwagę następujących właściwości kluczy tego zasobu:
    - "kind" — w przypadku aplikacji zarządzanych w witrynie Marketplace. wartość powinna być "Marketplace".
    - "ManagedResourceGroupId" — grupę zasobów w subskrypcji klienta wdrożonym wszystkie zasoby, które są zdefiniowane w applianceMainTemplate.json.
    - Ciąg "elementu PublisherPackageId", który unikatowo identyfikuje pakiet. Ta wartość musi być zbudowany w następujący sposób: jest łączenie [publisherId]. [Identyfikatora oferty]-[SKUID] w wersji zapoznawczej. [PackageVersion].

  >[!IMPORTANT] 
  >Ten pakiet powinien zawierać wszystkie zagnieżdżone szablony lub skrypty, które są potrzebne do aprowizowania tej aplikacji. Pliki te muszą znajdować się w folderze głównym:  MainTemplate.json applianceMainTemplate.json i applianceCreateUIDefinition.json.

- **Identyfikator dzierżawy\***  — identyfikator dzierżawy usługi Azure Active Directory w Twojej organizacji.
- **Włącz dostęp JIT? \***  — Wybierz **tak** umożliwiające Just-In-Time dostępu do zarządzania w przypadku wdrożeń klientów przy użyciu tej oferty.

  >[!NOTE] 
  >Jeśli włączysz JIT, należy zaktualizować plik CreateUiDefinition.json do obsługi dostępu JIT.

Dla aplikacji zarządzanej należy skonfigurować ustawienia zasad i autoryzacji.


#### <a name="authorization"></a>Autoryzacja

Dodawanie usługi Azure Active Directory identyfikator użytkownika, grupę lub aplikację, do którego chcesz udzielić uprawnień do zarządzanej grupy zasobów. Uprawnienia, które otrzymuje jest wskazywany przez identyfikator definicji roli Może to być właścicielem, współautorem lub dowolną rolę niestandardową.


#### <a name="policy-settings"></a>Ustawienia zasad

Dodaj zasady, które spełnia aplikacji zarządzanej. Dowiedz się więcej na temat zasad zasobów platformy Azure, zobacz [co to jest usługa Azure Policy?](../../../governance/policy/overview.md)

   ![Autoryzacja i zasady ustawień dla aplikacji zarządzanej](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Aby utworzyć nowy autoryzacji:**

1. W obszarze **autoryzacji**, wybierz opcję **+ nowe autoryzacji**.
2. Aby uzyskać **identyfikator podmiotu zabezpieczeń**, wpisz usługi Azure Active Directory identyfikator użytkownika, grupy lub aplikacji, do którego chcesz udzielić uprawnień do zarządzanej grupy zasobów. Uprawnienia, które otrzymuje jest wskazywany przez definicji roli.
3. Aby uzyskać **definicji roli**, wybierz jedną z następujących opcji z listy rozwijanej:  Właścicielem lub współautorem. Aby uzyskać więcej informacji, zobacz [wbudowane role zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Można dodać wiele autoryzacji. Jednak zaleca się utworzenie grupy użytkowników usługi Active Directory i określ jej identyfikator w "PrincipalId". Umożliwi to dodanie większej liczby użytkowników do grupy użytkowników, bez konieczności aktualizowania jednostki SKU.

**Aby utworzyć nowe zasady:**

1. W obszarze **ustawienia zasad**, wybierz opcję **+ nowe zasady**.
2. Aby uzyskać **Nazwa_zasad**, wprowadź nazwę dla zasad. Maksymalna długość nazwy to 50 znaków.
3. Aby uzyskać **zasady**, wybierz jedną z opcji z listy rozwijanej. Wybierz zasady, które chce można włączyć, jeśli aplikacja używa danych dostawcy danych. Aby uzyskać więcej informacji, zobacz [przykładów dla platformy Azure zasad](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Ustawienia zasad dla aplikacji zarządzanej](./media/azureapp-sku-policy-settings.png)

4. Aby uzyskać **jednostka SKU zasad**, wybierz jako zasady typ jednostki SKU bezpłatna lub standardowa. Standardowa jednostka SKU jest wymagana dla zasady inspekcji.


## <a name="next-steps"></a>Kolejne kroki

Zostaną dodatkowo opisać Twoją ofertę i dostarczania zasobów marketingowych w [kartę Marketplace](./cpp-marketplace-tab.md). 
