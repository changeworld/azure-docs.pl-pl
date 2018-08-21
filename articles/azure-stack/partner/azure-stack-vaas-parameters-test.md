---
title: Testowanie parametrów do weryfikacji jako usługi Azure Stack | Dokumentacja firmy Microsoft
description: Temat informacje o pliku konfiguracji i testowania należy przekazać parametry do weryfikacji jako usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235098"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Parametry testu do weryfikacji jako usługi Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Przed wykonaniem dowolny pakiet testów z operacji sprawdzania poprawności jako usługa (VaaS), wybierz rozwiązanie, a następnie utworzyć przebiegu testu.

- Zaloguj się przy użyciu swojej zarejestrowanej VaaS poświadczenia dzierżawy.
- Utwórz nowe rozwiązanie (wybierając **Dodaj rozwiązanie**) lub wybierz istniejące.
- Wybierz **Start** przycisk **przebiegów testowych** kafelka przepływu pracy.

> [!Note]  
> Utwórz nowy wpis rozwiązania dla każdej konfiguracji zestawu/sprzętu unikatowych maszyn, masz sprawdzania poprawności, a nowy test zakończy się pomyślnie dla każdego wdrożenia kompilacji w tym zestawie maszyn.

Będzie konieczne wprowadzenie parametrów wymaganych do uruchomienia testów **przekazać informacje o testach** strony. Podczas uruchamiania nowych przebiegu testu lub przebiegu weryfikacji, należy podać te parametry. Większość parametry mają takie same wartości, które podano podczas wdrażania usługi Azure Stack.

Można wprowadzić wartości ręcznie na liście [tabeli parametrów testu](#test-parameters), lub Przekaż plik konfiguracji wdrożenia, który zawiera pełne informacje sygnatury usługi Azure Stack. Po przekazaniu plików portalu ładuje wartości z pliku.

> [!Note]  
> Możesz wyszukać i wpisując wartości parametrów testu, wyszukując i ładowania pliku konfiguracji do portalu.

## <a name="export-the-test-parameters-using-powershell"></a>Eksportuj parametry testu przy użyciu programu PowerShell

1. Zaloguj się do komputera Menedżer DVM lub dowolnym komputerze, który ma dostęp do środowiska usługi Azure Stack.
2. Otwórz okno programu PowerShell z podwyższonym i uruchom:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Przekaż **stampinfoproperties.json** do portalu VaaS.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>Znajdowanie parametrów testu w pliku konfiguracji

Wartości parametrów testu można również znaleźć, otwierając ONZ **pliku konfiguracyjnego**. Można znaleźć pliku w następującej ścieżce na komputerze Menedżer DVM:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Parametry testu 

| Parametr    | Opis |
|-------------|-----------------|
| Usługa Azure Stack kompilacji                      | Azure Stack tworzenie ani numeru wersji, który został wdrożony. na przykład, 1.0.170330.0 | 
| Identyfikator dzierżawy                              | Usługa Azure dzierżawy usługi Active Directory określony podczas wdrażania usługi Azure Stack. Wyszukaj **AADTenant** w pliku konfiguracji i wybierz pozycję **GUID** wartość w `Id` tagu. | 
| Region                                 | Region wdrożenia platformy Azure Stack. Wyszukaj **Region** w pliku konfiguracji. | 
| Menedżer zasobów dzierżawy                | Na przykład dzierżawy punktu końcowego usługi Azure Resource Manager `https://management.<ExternalFqdn>` | 
| Menedżer zasobów administratora                 | Punkt końcowy administratora usługi Azure Resource Manager. Na przykład `https://adminmanagement.<ExternalFqdn>` | 
| Nazwa FQDN zewnętrznej                          | Zewnętrzne FQDN środowiska. Wyszukaj **ExternalFqdn** w pliku konfiguracji. | 
| Użytkownik dzierżawy                            | Usługa Azure dzierżawy administrator usługi Active Directory, albo została już przygotowana, lub musi być obsługiwana przez administratora usługi w katalogu usługi Azure AD. Zobacz szczegółowe informacje na temat inicjowania obsługi administracyjnej konta dzierżawy [dodać nowe konto dzierżawy usługi Azure Stack w usłudze Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Ta wartość jest używana przez test do wykonywania operacji na poziomie dzierżawy takich jak wdrażanie szablonów do aprowizacji zasobów (maszyn wirtualnych, magazynu kont itp.), a następnie wykonaj obciążeń. | 
| Użytkownik Administrator usługi             | Usługa Azure administrator usługi Active Directory dzierżawy usługi Azure AD Directory określony podczas wdrażania usługi Azure Stack. Wyszukaj **AADTenant** w konfiguracji pliku i wybierz wartość `UniqueName` znacznika w pliku konfiguracji. | 

## <a name="checks-before-starting-the-tests"></a>Sprawdzanie przed uruchomieniem testów

Testy wykonują operacje zdalnego. Komputer który uruchamia testy musi mieć dostęp do punktów końcowych usługi Azure Stack. W przeciwnym razie testu nie będą działać. Jeśli używasz VaaS agenta lokalnie, należy korzystać z maszyn, których będzie uruchamiany agent. Aby sprawdzić, czy komputer ma dostęp do punktów usługi Azure Stack, uruchamiając następujące testy.

1. Sprawdź, czy można połączyć podstawowy identyfikator URI. Otwórz wiersz polecenia lub powłokę bash i uruchom następujące polecenie:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otwórz przeglądarkę internetową i przejdź do `https://adminportal.<EXTERNALFQDN>` w celu sprawdzenia, czy można połączyć z portalu MAS.

3. Zalogować się przy użyciu usługi Azure AD usługi administratora nazwy i hasła podanych wartości podczas tworzenia przebiegu testu.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
