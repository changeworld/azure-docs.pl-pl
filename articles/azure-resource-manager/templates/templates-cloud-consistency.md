---
title: Ponowne używanie szablonów w chmurach
description: Twórz szablony usługi Azure Resource Manager, które działają spójnie w różnych środowiskach chmury. Tworzenie nowych lub aktualizowanie istniejących szablonów dla usługi Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156110"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Tworzenie szablonów ARM dla spójności chmury

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Kluczową zaletą platformy Azure jest spójność. Inwestycje dewelopercze dla jednej lokalizacji nadają się do ponownego użytku w innej. Szablon usługi Azure Resource Manager (ARM) sprawia, że wdrożenia są spójne i powtarzalne w różnych środowiskach, w tym na globalnej platformie Azure, suwerennych chmurach platformy Azure i usłudze Azure Stack. Aby ponownie użyć szablonów w chmurach, należy jednak wziąć pod uwagę zależności specyficzne dla chmury, jak wyjaśniono w tym przewodniku.

Firma Microsoft oferuje inteligentne usługi w chmurze gotowe dla przedsiębiorstw w wielu lokalizacjach, w tym:

* Globalna platforma platformy Azure obsługiwana przez rosnącą sieć centrów danych zarządzanych przez firmę Microsoft w regionach na całym świecie.
* Odizolowane suwerenne chmury, takie jak Azure Germany, Azure Government i Azure China 21Vianet. Suwerenne chmury zapewniają spójną platformę z większością tych samych wspaniałych funkcji, do których mają dostęp globalni klienci platformy Azure.
* Azure Stack — hybrydowa platforma chmurowa, która umożliwia dostarczanie usług platformy Azure z centrum danych organizacji. Przedsiębiorstwa mogą skonfigurować usługę Azure Stack we własnych centrach danych lub korzystać z usług Platformy Azure od dostawców usług, uruchamiając usługę Azure Stack w swoich obiektach (czasami nazywanych regionami hostowanymi).

W centrum wszystkich tych chmur usługa Azure Resource Manager udostępnia interfejs API, który umożliwia szeroką gamę interfejsów użytkownika do komunikowania się z platformą Azure. Ten interfejs API zapewnia zaawansowane możliwości infrastruktury jako kodu. Każdy typ zasobu, który jest dostępny na platformie chmury platformy Azure można wdrożyć i skonfigurować za pomocą usługi Azure Resource Manager. Za pomocą jednego szablonu można wdrożyć i skonfigurować pełną aplikację do operacyjnego stanu końcowego.

![Środowiska platformy Azure](./media/templates-cloud-consistency/environments.png)

Spójność globalnej platformy Azure, suwerennych chmur, chmur hostowanych i chmury w centrum danych ułatwia korzystanie z usługi Azure Resource Manager. Podczas konfigurowania wdrożenia i konfiguracji zasobów opartych na szablonach można ponownie użyć inwestycji deweloperskich w tych chmurach.

Jednak mimo że globalne, suwerenne, hostowane i hybrydowe chmury zapewniają spójne usługi, nie wszystkie chmury są identyczne. W rezultacie można utworzyć szablon z zależnościami od funkcji dostępnych tylko w określonej chmurze.

W dalszej części tego przewodnika opisano obszary, które należy wziąć pod uwagę podczas planowania tworzenia nowych lub aktualizowania istniejących szablonów ARM dla usługi Azure Stack. Ogólnie rzecz biorąc, lista kontrolna powinna zawierać następujące elementy:

* Sprawdź, czy funkcje, punkty końcowe, usługi i inne zasoby w szablonie są dostępne w lokalizacjach wdrożenia docelowego.
* Przechowuj zagnieżdżone szablony i artefakty konfiguracji w dostępnych lokalizacjach, zapewniając dostęp między chmurami.
* Użyj odwołań dynamicznych zamiast twardych kodowania łączy i elementów.
* Upewnij się, że parametry szablonu, których używasz, działają w chmurach docelowych.
* Sprawdź, czy właściwości specyficzne dla zasobów są dostępne w chmurach docelowych.

Aby zapoznać się z wprowadzeniem do szablonów ARM, zobacz [Wdrażanie szablonów](overview.md).

## <a name="ensure-template-functions-work"></a>Upewnij się, że funkcje szablonu działają

Podstawową składnią szablonu ARM jest JSON. Szablony używają nadzbioru JSON, rozszerzając składnię za pomocą wyrażeń i funkcji. Procesor języka szablonu jest często aktualizowany w celu obsługi dodatkowych funkcji szablonu. Szczegółowe informacje na temat dostępnych funkcji szablonu można znaleźć w części [Funkcje szablonu ARM](template-functions.md).

Nowe funkcje szablonów, które są wprowadzane do usługi Azure Resource Manager nie są natychmiast dostępne w suwerennych chmur lub usługi Azure Stack. Aby pomyślnie wdrożyć szablon, wszystkie funkcje, do których odwołuje się szablon, muszą być dostępne w chmurze docelowej.

Możliwości usługi Azure Resource Manager będą zawsze wprowadzane najpierw do globalnej platformy Azure. Aby sprawdzić, czy nowo wprowadzone funkcje szablonu są również dostępne w usłudze Azure Stack, można użyć następującego skryptu programu PowerShell:

1. Zrób klon repozytorium GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Po lokalnym klon repozytorium, połącz się z usługą Azure Resource Manager miejsca docelowego z programem PowerShell.

1. Zaimportuj moduł psm1 i wykonaj polecenie cmdlet Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skrypt wdraża wiele, zminimalizowanych szablonów, z których każdy zawiera tylko unikatowe funkcje szablonu. Dane wyjściowe skryptu raportuje obsługiwane i niedostępne funkcje szablonu.

## <a name="working-with-linked-artifacts"></a>Praca z połączonymi artefaktami

Szablon może zawierać odwołania do połączonych artefaktów i zawierać zasób wdrożenia, który łączy się z innym szablonem. Połączone szablony (nazywane również szablonem zagnieżdżanym) są pobierane przez Menedżera zasobów w czasie wykonywania. Szablon może również zawierać odwołania do artefaktów dla rozszerzeń maszyny wirtualnej. Te artefakty są pobierane przez rozszerzenie maszyny Wirtualnej uruchomione wewnątrz maszyny Wirtualnej w celu skonfigurowania rozszerzenia maszyny Wirtualnej podczas wdrażania szablonu.

W poniższych sekcjach opisano zagadnienia dotyczące spójności chmury podczas tworzenia szablonów, które zawierają artefakty, które są zewnętrzne do głównego szablonu wdrożenia.

### <a name="use-nested-templates-across-regions"></a>Używanie szablonów zagnieżdżonych w różnych regionach

Szablony można rozłożyć na małe szablony wielokrotnego użytku, z których każdy ma określony cel i może być ponownie użyty w scenariuszach wdrażania. Aby wykonać wdrożenie, należy określić pojedynczy szablon znany jako szablon główny lub główny. Określa zasoby do wdrożenia, takie jak sieci wirtualne, maszyny wirtualne i aplikacje sieci web. Szablon główny może również zawierać łącze do innego szablonu, co oznacza, że można zagnieżdżać szablony. Podobnie szablon zagnieżdżony może zawierać łącza do innych szablonów. Można zagnieździć do pięciu poziomów głębokości.

Poniższy kod pokazuje, jak parametr templateLink odnosi się do szablonu zagnieżdżonego:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Usługa Azure Resource Manager ocenia główny szablon w czasie wykonywania i pobiera i ocenia każdy szablon zagnieżdżony. Po pobraniu wszystkich zagnieżdżonych szablonów szablon jest spłaszczany i inicjowane jest dalsze przetwarzanie.

### <a name="make-linked-templates-accessible-across-clouds"></a>Udostępnij połączone szablony w chmurach

Zastanów się, gdzie i jak przechowywać używane połączone szablony. W czasie wykonywania usługa Azure Resource Manager pobiera — i dlatego wymaga bezpośredniego dostępu do wszystkich połączonych szablonów. Powszechną praktyką jest użycie gitHub do przechowywania szablonów zagnieżdżonych. Repozytorium GitHub może zawierać pliki, które są publicznie dostępne za pośrednictwem adresu URL. Mimo że ta technika działa dobrze dla chmury publicznej i chmur suwerennych, środowisko usługi Azure Stack może znajdować się w sieci firmowej lub w lokalizacji zdalnej rozłączone, bez wychodzącego dostępu do Internetu. W takich przypadkach usługa Azure Resource Manager nie może pobrać szablonów zagnieżdżonych.

Lepszą praktyką dla wdrożeń między chmurami jest przechowywanie połączonych szablonów w lokalizacji dostępnej dla chmury docelowej. W idealnym przypadku wszystkie artefakty wdrażania są obsługiwane i wdrażane z potoku ciągłej integracji/ciągłego programowania (CI/CD). Alternatywnie można przechowywać szablony zagnieżdżone w kontenerze magazynu obiektów blob, z którego usługa Azure Resource Manager może je pobrać.

Ponieważ magazyn obiektów blob w każdej chmurze używa innej w pełni kwalifikowanej nazwy domeny punktu końcowego (FQDN), skonfiguruj szablon z lokalizacją połączonych szablonów z dwoma parametrami. Parametry mogą akceptować dane wejściowe użytkownika w czasie wdrażania. Szablony są zazwyczaj autorstwa i udostępniane przez wiele osób, więc najlepszym rozwiązaniem jest użycie nazwy standardowej dla tych parametrów. Konwencje nazewnictwa ułatwiają tworzenie szablonów na wiele różnych regionów, chmur i autorów.

W poniższym `_artifactsLocation` kodzie jest używany do wskazywania jednej lokalizacji, zawierającej wszystkie artefakty związane z wdrażaniem. Należy zauważyć, że wartość domyślna jest podana. W czasie wdrażania, jeśli nie `_artifactsLocation`określono wartości wejściowej dla , używana jest wartość domyślna. Jest `_artifactsLocationSasToken` używany jako dane `sasToken`wejściowe dla . Wartość domyślna powinna być pustym ciągiem dla scenariuszy, w `_artifactsLocation` których nie jest zabezpieczony — na przykład publiczne repozytorium GitHub.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

W całym szablonie łącza są generowane przez połączenie podstawowego identyfikatora URI (z parametru) `_artifactsLocation` ze ścieżką względną artefaktu `_artifactsLocationSasToken`i . Poniższy kod pokazuje, jak określić łącze do szablonu zagnieżdżonego przy użyciu funkcji szablonu uri:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Przy użyciu tej metody używana `_artifactsLocation` jest wartość domyślna dla parametru. Jeśli połączone szablony muszą być pobierane z innej lokalizacji, dane wejściowe parametru mogą być używane w czasie wdrażania do zastąpienia wartości domyślnej — nie jest wymagana żadna zmiana samego szablonu.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Użyj _artifactsLocation zamiast linków do twardego kodowania

Oprócz używanego dla szablonów zagnieżdżonych, adres URL w parametrze `_artifactsLocation` jest używany jako podstawa dla wszystkich powiązanych artefaktów szablonu wdrożenia. Niektóre rozszerzenia maszyn wirtualnych zawierają łącze do skryptu przechowywanego poza szablonem. W przypadku tych rozszerzeń nie należy kodować na stałe łączy. Na przykład niestandardowe skrypty i rozszerzenia DSC programu PowerShell mogą łączyć się z zewnętrznym skryptem w usłudze GitHub, jak pokazano na rysunku:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Twarde kodowanie łączy do skryptu potencjalnie uniemożliwia szablonowi pomyślne wdrożenie w innej lokalizacji. Podczas konfigurowania zasobu maszyny Wirtualnej agent maszyny Wirtualnej działający wewnątrz maszyny Wirtualnej inicjuje pobieranie wszystkich skryptów połączonych w rozszerzeniu maszyny Wirtualnej, a następnie przechowuje skrypty na dysku lokalnym maszyny Wirtualnej. To podejście działa jak zagnieżdżone łącza szablonów wyjaśnione wcześniej w sekcji "Użyj szablonów zagnieżdżonych w różnych regionach".

Menedżer zasobów pobiera szablony zagnieżdżone w czasie wykonywania. W przypadku rozszerzeń maszyn wirtualnych pobieranie wszelkich zewnętrznych artefaktów jest wykonywane przez agenta maszyny Wirtualnej. Oprócz innego inicjatora pobierania artefaktów rozwiązanie w definicji szablonu jest takie samo. Użyj parametru _artifactsLocation z domyślną wartością ścieżki podstawowej, w której przechowywane są wszystkie artefakty `_artifactsLocationSasToken` (w tym skrypty rozszerzenia maszyny Wirtualnej) i parametrem dla danych wejściowych dla sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Aby skonstruować bezwzględny identyfikator URI artefaktu, preferowaną metodą jest użycie funkcji szablonu uri zamiast funkcji szablonu concat. Zastępując hardcoded łącza do skryptów w rozszerzeniu maszyny Wirtualnej z funkcją szablonu uri, ta funkcja w szablonie jest skonfigurowany dla spójności chmury.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Dzięki takiemu podejściu wszystkie artefakty wdrażania, w tym skrypty konfiguracji, mogą być przechowywane w tej samej lokalizacji z samym szablonem. Aby zmienić lokalizację wszystkich łączy, wystarczy określić inny podstawowy adres URL _parametrów artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Czynnikiem wpływającym na różne możliwości regionalne

Dzięki elastycznemu programowi i ciągłemu przepływowi aktualizacji i nowych usług wprowadzonych na platformę Azure [regiony mogą różnić się](https://azure.microsoft.com/regions/services/) dostępnością usług lub aktualizacji. Po rygorystycznych testach wewnętrznych nowe usługi lub aktualizacje istniejących usług są zwykle wprowadzane do niewielkiej grupy odbiorców klientów uczestniczących w programie sprawdzania poprawności. Po pomyślnym weryfikacji klienta usługi lub aktualizacje są udostępniane w podzbiór regionów platformy Azure, a następnie wprowadzone do większej liczby regionów, wdrożone w chmurach suwerennych i potencjalnie udostępnione również klientom usługi Azure Stack.

Wiedząc, że regiony i chmury platformy Azure mogą się różnić w dostępnych usługach, można podjąć pewne proaktywne decyzje dotyczące szablonów. Dobrym miejscem do rozpoczęcia jest zbadanie dostępnych dostawców zasobów dla chmury. Dostawca zasobów informuje o zestawie zasobów i operacji, które są dostępne dla usługi platformy Azure.

Szablon wdraża i konfiguruje zasoby. Typ zasobu jest dostarczany przez dostawcę zasobów. Na przykład dostawca zasobów obliczeniowych (Microsoft.Compute) udostępnia wiele typów zasobów, takich jak virtualMachines i availabilitySets. Każdy dostawca zasobów udostępnia interfejs API usługi Azure Resource Manager zdefiniowany przez wspólny kontrakt, umożliwiając spójne, ujednolicone środowisko tworzenia we wszystkich dostawcach zasobów. Jednak dostawca zasobów, który jest dostępny na globalnej platformie Azure może nie być dostępny w suwerennej chmurze lub regionie usługi Azure Stack.

![Dostawcy zasobów](./media/templates-cloud-consistency/resource-providers.png)

Aby zweryfikować dostawców zasobów dostępnych w danej chmurze, uruchom następujący skrypt w interfejsie wiersza polecenia platformy Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Można również użyć następującego polecenia cmdlet programu PowerShell, aby wyświetlić dostępnych dostawców zasobów:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Sprawdzanie wersji wszystkich typów zasobów

Zestaw właściwości jest wspólny dla wszystkich typów zasobów, ale każdy zasób ma również własne właściwości określone. Nowe funkcje i powiązane właściwości są dodawane do istniejących typów zasobów czasami za pośrednictwem nowej wersji interfejsu API. Zasób w szablonie ma własną `apiVersion`właściwość wersji interfejsu API - . Ta wersja zapewnia, że istniejące konfiguracji zasobów w szablonie nie ma wpływu na zmiany na platformie.

Nowe wersje interfejsu API wprowadzone do istniejących typów zasobów na globalnej platformie Azure mogą nie być natychmiast dostępne we wszystkich regionach, chmurach suwerennych lub usłudze Azure Stack. Aby wyświetlić listę dostępnych dostawców zasobów, typów zasobów i wersji interfejsu API dla chmury, można użyć Eksploratora zasobów w witrynie Azure portal. Wyszukaj Eksploratora zasobów w menu Wszystkie usługi. Rozwiń węzeł Dostawcy w Eksploratorze zasobów, aby zwrócić wszystkich dostępnych dostawców zasobów, ich typy zasobów i wersje interfejsu API w tej chmurze.

Aby wyświetlić listę dostępnej wersji interfejsu API dla wszystkich typów zasobów w danej chmurze w interfejsie wiersza polecenia platformy Azure, uruchom następujący skrypt:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Można również użyć następującego polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Odwoływanie się do lokalizacji zasobów z parametrem

Szablon jest zawsze wdrażany w grupie zasobów, która znajduje się w regionie. Oprócz samego wdrożenia, każdy zasób w szablonie ma również właściwość lokalizacji, która służy do określania regionu do wdrożenia w. Aby opracować szablon dla spójności chmury, potrzebujesz dynamicznego sposobu odwoływania się do lokalizacji zasobów, ponieważ każdy stos azure może zawierać unikatowe nazwy lokalizacji. Zazwyczaj zasoby są wdrażane w tym samym regionie co grupa zasobów, ale do obsługi scenariuszy, takich jak dostępność aplikacji między regionami, może być przydatne do rozmieszczania zasobów w różnych regionach.

Mimo że można hardcode nazwy regionu podczas określania właściwości zasobów w szablonie, to podejście nie gwarantuje, że szablon można wdrożyć w innych środowiskach usługi Azure Stack, ponieważ nazwa regionu najprawdopodobniej nie istnieje tam.

Aby pomieścić różne regiony, dodaj lokalizację parametru wejściowego do szablonu z wartością domyślną. Wartość domyślna będzie używana, jeśli podczas wdrażania nie określono żadnej wartości.

Funkcja `[resourceGroup()]` szablonu zwraca obiekt zawierający następujące pary klucz/wartość:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Odwołując się do klucza lokalizacji obiektu w defaultValue parametru wejściowego, Usługa `[resourceGroup().location]` Azure Resource Manager zastąpi w czasie wykonywania funkcję szablonu nazwą lokalizacji grupy zasobów, do.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Dzięki tej funkcji szablonu można wdrożyć szablon w dowolnej chmurze, nie znając z wyprzedzeniem nazw regionów. Ponadto lokalizacja określonego zasobu w szablonie może się różnić od lokalizacji grupy zasobów. W takim przypadku można go skonfigurować przy użyciu dodatkowych parametrów wejściowych dla tego określonego zasobu, podczas gdy inne zasoby w tym samym szablonie nadal używają początkowego parametru wejściowego lokalizacji.

### <a name="track-versions-using-api-profiles"></a>Śledzenie wersji przy użyciu profilów interfejsu API

Śledzenie wszystkich dostępnych dostawców zasobów i powiązanych wersji interfejsu API, które są obecne w usłudze Azure Stack, może być bardzo trudne. Na przykład w momencie pisania najnowsza wersja interfejsu API dla **microsoft.compute/availabilitySets** na platformie Azure `2018-04-01` `2016-03-30`jest , podczas gdy dostępna wersja interfejsu API wspólne dla platformy Azure i usługi Azure Stack jest . Wspólna wersja interfejsu API dla **witryny Microsoft.Storage/storageKondykony** współużytkowane przez wszystkie lokalizacje platformy Azure i usługi Azure Stack to `2016-01-01`, podczas gdy najnowsza wersja interfejsu API na platformie Azure to `2018-02-01`.

Z tego powodu Menedżer zasobów wprowadził pojęcie profilów interfejsu API do szablonów. Bez profilów interfejsu API każdy zasób `apiVersion` w szablonie jest skonfigurowany z elementem opisującym wersję interfejsu API dla tego określonego zasobu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Wersja profilu interfejsu API działa jako alias dla pojedynczej wersji interfejsu API dla każdego typu zasobu wspólnego dla platformy Azure i usługi Azure Stack. Zamiast określać wersję interfejsu API dla każdego zasobu w szablonie, należy określić `apiProfile` tylko wersję `apiVersion` profilu interfejsu API w nowym elemencie głównym o nazwie i pominąć element dla poszczególnych zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Profil interfejsu API zapewnia, że wersje interfejsu API są dostępne w różnych lokalizacjach, więc nie trzeba ręcznie weryfikować apiVersions, które są dostępne w określonej lokalizacji. Aby upewnić się, że wersje interfejsu API, do których odwołuje się profil interfejsu API, są obecne w środowisku usługi Azure Stack, operatorzy usługi Azure Stack muszą aktualizować rozwiązanie na podstawie zasad pomocy technicznej. Jeśli system jest nieaktualny o więcej niż sześć miesięcy, jest uważany za niezgodny z przepisami, a środowisko musi zostać zaktualizowane.

Profil interfejsu API nie jest wymaganym elementem w szablonie. Nawet jeśli dodasz element, będzie on używany tylko `apiVersion` dla zasobów, dla których nie jest określony. Ten element umożliwia stopniowe zmiany, ale nie wymaga żadnych zmian w istniejących szablonach.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Sprawdzanie odwołań do punktów końcowych

Zasoby mogą mieć odwołania do innych usług na platformie. Na przykład publiczny adres IP może mieć przypisaną publiczną nazwę DNS. Chmura publiczna, suwerenne chmury i rozwiązania usługi Azure Stack mają własne odrębne obszary nazw punktów końcowych. W większości przypadków zasób wymaga tylko prefiksu jako dane wejściowe w szablonie. W czasie wykonywania usługa Azure Resource Manager dołącza do niego wartość punktu końcowego. Niektóre wartości punktu końcowego muszą być jawnie określone w szablonie.

> [!NOTE]
> Aby opracować szablony dla spójności chmury, nie należy zakodować obszarów nazw punktu końcowego.

Następujące dwa przykłady są wspólne obszary nazw punktu końcowego, które muszą być jawnie określone podczas tworzenia zasobu:

* Konta magazynu (obiekt blob, kolejka, tabela i plik)
* Parametry połączenia dla baz danych i pamięci podręcznej azure dla redis

Obszary nazw punktu końcowego mogą być również używane w danych wyjściowych szablonu jako informacje dla użytkownika po zakończeniu wdrażania. Poniżej przedstawiono typowe przykłady:

* Konta magazynu (obiekt blob, kolejka, tabela i plik)
* Parametry połączenia (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel publicznego adresu IP
* Usługi w chmurze

Ogólnie rzecz biorąc należy unikać hardcoded punktów końcowych w szablonie. Najlepszym rozwiązaniem jest użycie funkcji szablonu odwołania do dynamicznego pobierania punktów końcowych. Na przykład punkt końcowy najczęściej hardcoded jest obszar nazw punktu końcowego dla kont magazynu. Każde konto magazynu ma unikatową nazwę FQDN, która jest konstruowana przez łączenie nazwy konta magazynu z obszarem nazw punktu końcowego. Konto magazynu obiektów blob o nazwie mystorageaccount1 powoduje różne nazwy FQDN w zależności od chmury:

* **mystorageaccount1.blob.core.windows.net** po utworzeniu w globalnej chmurze platformy Azure.
* **mystorageaccount1.blob.core.chinacloudapi.cn** po utworzeniu w chmurze Azure China 21Vianet.

Następująca funkcja szablonu odwołania pobiera obszar nazw punktu końcowego od dostawcy zasobów magazynu:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Zastępując wartość punktu końcowego konta magazynu z `reference` funkcją szablonu, można użyć tego samego szablonu, aby pomyślnie wdrożyć go w różnych środowiskach bez wprowadzania żadnych zmian w odwołaniu do punktu końcowego.

### <a name="refer-to-existing-resources-by-unique-id"></a>Odwoływanie się do istniejących zasobów według unikatowego identyfikatora

Można również odwołać się do istniejącego zasobu z tej samej lub innej grupy zasobów i w ramach tej samej subskrypcji lub innej subskrypcji w ramach tej samej dzierżawy w tej samej chmurze. Aby pobrać właściwości zasobu, należy użyć unikatowego identyfikatora dla samego zasobu. Funkcja `resourceId` szablonu pobiera unikatowy identyfikator zasobu, takiego jak SQL Server, zgodnie z następującym kodem:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Następnie można użyć `resourceId` funkcji `reference` wewnątrz funkcji szablonu, aby pobrać właściwości bazy danych. Obiekt zwracany `fullyQualifiedDomainName` zawiera właściwość, która przechowuje pełną wartość punktu końcowego. Ta wartość jest pobierana w czasie wykonywania i zapewnia obszar nazw punktu końcowego specyficzne dla środowiska chmury. Aby zdefiniować ciąg połączenia bez twardego kodowania obszaru nazw punktu końcowego, można odwołać się do właściwości obiektu zwracanego bezpośrednio w ciągu połączenia, jak pokazano na rysunku:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Uwzględnianie właściwości zasobu

Określone zasoby w środowiskach usługi Azure Stack mają unikatowe właściwości, które należy wziąć pod uwagę w szablonie.

### <a name="ensure-vm-images-are-available"></a>Upewnij się, że obrazy maszyn wirtualnych są dostępne

Platforma Azure zapewnia bogaty wybór obrazów maszyn wirtualnych. Te obrazy są tworzone i przygotowywane do wdrożenia przez firmę Microsoft i partnerów. Obrazy stanowią podstawę dla maszyn wirtualnych na platformie. Jednak szablon spójny w chmurze powinien odwoływać się tylko do dostępnych parametrów — w szczególności wydawcy, oferty i jednostki SKU obrazów maszyn wirtualnych dostępnych dla globalnej platformy Azure, suwerennych chmur platformy Azure lub rozwiązania usługi Azure Stack.

Aby pobrać listę dostępnych obrazów maszyn wirtualnych w lokalizacji, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az vm image list -all
```

Tę samą listę można pobrać za pomocą polecenia cmdlet platformy Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) i określić lokalizację, która ma zostać określona za pomocą parametru. `-Location` Przykład:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

To polecenie trwa kilka minut, aby zwrócić wszystkie dostępne obrazy w regionie Europy Zachodniej globalnej chmury platformy Azure.

Jeśli te obrazy maszyn wirtualnych zostały udostępnione do usługi Azure Stack, wszystkie dostępnego magazynu zostaną wykorzystane. Aby pomieścić nawet najmniejszą jednostkę skalowania, usługa Azure Stack umożliwia wybranie obrazów, które chcesz dodać do środowiska.

Poniższy przykładowy kod przedstawia spójne podejście do odwoływania się do parametrów wydawcy, oferty i jednostki SKU w szablonach ARM:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Sprawdzanie lokalnych rozmiarów maszyn wirtualnych

Aby opracować szablon dla spójności chmury, należy upewnić się, że rozmiar maszyny Wirtualnej, który chcesz jest dostępny we wszystkich środowiskach docelowych. Rozmiary maszyn wirtualnych to grupowanie charakterystyk i możliwości wydajności. Niektóre rozmiary maszyn wirtualnych zależą od sprzętu, na który działa maszyna wirtualna. Na przykład jeśli chcesz wdrożyć maszynę wirtualną zoptymalizowaną pod kątem procesora GPU, sprzęt uruchamiany hipernadzorcą musi mieć sprzętowe procesory GPU.

Gdy firma Microsoft wprowadza nowy rozmiar maszyny Wirtualnej, który ma pewne zależności sprzętowe, rozmiar maszyny Wirtualnej jest zwykle udostępniany najpierw w małym podzbiorze regionów w chmurze platformy Azure. Później jest on udostępniany innym regionom i chmurom. Aby upewnić się, że rozmiar maszyny Wirtualnej istnieje w każdej chmurze, którą wdrażasz, można pobrać dostępne rozmiary za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

W przypadku programu Azure PowerShell użyj:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Aby uzyskać pełną listę dostępnych usług, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Sprawdzanie użycia dysków zarządzanych platformy Azure w usłudze Azure Stack

Dyski zarządzane obsługują magazyn dla dzierżawy platformy Azure. Zamiast jawnie utworzyć konto magazynu i określić identyfikator URI dla wirtualnego dysku twardego (VHD), można użyć dysków zarządzanych do niejawnego wykonywania tych akcji podczas wdrażania maszyny Wirtualnej. Dyski zarządzane zwiększają dostępność, umieszczając wszystkie dyski z maszyn wirtualnych w tej samej dostępności ustawionej w różnych jednostkach magazynu. Ponadto istniejące dyski VHD można konwertować ze standardowej pamięci masowej w wersji Premium ze znacznie krótszym czasem przestoju.

Chociaż dyski zarządzane znajdują się w planie działania usługi Azure Stack, obecnie nie są obsługiwane. Dopóki nie zostaną one utworzone, można tworzyć szablony spójne z chmurą `vhd` dla usługi Azure Stack, jawnie określając identyfikatory VHD przy użyciu elementu w szablonie zasobu maszyny Wirtualnej, jak pokazano na rysunku:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Z drugiej strony, aby określić konfigurację `vhd` dysku zarządzanego w szablonie, usuń element z konfiguracji dysku.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Te same zmiany dotyczą również [dysków z danymi](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Sprawdzanie, czy rozszerzenia maszyn wirtualnych są dostępne w usłudze Azure Stack

Inną kwestią spójności chmury jest użycie [rozszerzeń maszyn wirtualnych](../../virtual-machines/windows/extensions-features.md) do konfigurowania zasobów wewnątrz maszyny wirtualnej. Nie wszystkie rozszerzenia maszyn wirtualnych są dostępne w usłudze Azure Stack. Szablon można określić zasoby przeznaczone dla rozszerzenia maszyny Wirtualnej, tworzenie zależności i warunków w szablonie.

Na przykład jeśli chcesz skonfigurować maszynę wirtualną z systemem Microsoft SQL Server, rozszerzenie maszyny Wirtualnej można skonfigurować SQL Server jako część wdrożenia szablonu. Należy wziąć pod uwagę, co się stanie, jeśli szablon wdrożenia zawiera również serwer aplikacji skonfigurowany do tworzenia bazy danych na maszynie Wirtualnej z systemem SQL Server. Oprócz również przy użyciu rozszerzenia maszyny Wirtualnej dla serwerów aplikacji, można skonfigurować zależność serwera aplikacji na pomyślny powrót zasobu rozszerzenia maszyny Wirtualnej programu SQL Server. Takie podejście gwarantuje, że maszyna wirtualna z systemem SQL Server jest skonfigurowana i dostępna, gdy serwer aplikacji jest poinstruowany, aby utworzyć bazę danych.

Deklaratywne podejście szablonu umożliwia zdefiniowanie stanu końcowego zasobów i ich współzależności, podczas gdy platforma zajmuje się logiką wymaganą dla zależności.

#### <a name="check-that-vm-extensions-are-available"></a>Sprawdź, czy rozszerzenia maszyn wirtualnych są dostępne

Istnieje wiele typów rozszerzeń maszyn wirtualnych. Podczas tworzenia szablonu dla spójności chmury, upewnij się, że używa tylko rozszerzeń, które są dostępne we wszystkich regionach docelowych szablonu.

Aby pobrać listę rozszerzeń maszyn wirtualnych, które są dostępne `myLocation`dla określonego regionu (w tym przykładzie ), uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Można również wykonać polecenie cmdlet [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) platformy `-Location` Azure i użyć do określenia lokalizacji obrazu maszyny wirtualnej. Przykład:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Upewnij się, że wersje są dostępne

Ponieważ rozszerzenia maszyn wirtualnych są zasobami menedżera zasobów własnej firmy, mają własne wersje interfejsu API. Jak pokazano w poniższym kodzie, typ rozszerzenia maszyny Wirtualnej jest zagnieżdżonym zasobem w dostawcy zasobów Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Wersja interfejsu API zasobu rozszerzenia maszyny Wirtualnej musi znajdować się we wszystkich lokalizacjach, które mają być kierowane do szablonu. Zależność lokalizacji działa podobnie do dostępności wersji interfejsu API dostawcy zasobów omówione wcześniej w sekcji "Sprawdź wersję wszystkich typów zasobów".

Aby pobrać listę dostępnych wersji interfejsu API dla zasobu rozszerzenia maszyny Wirtualnej, użyj polecenia cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) z dostawcą zasobów **Microsoft.Compute,** jak pokazano na rysunku:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Można również użyć rozszerzeń maszyn wirtualnych w zestawach skalowania maszyny wirtualnej. Obowiązują te same warunki lokalizacji. Aby opracować szablon dla spójności chmury, upewnij się, że wersje interfejsu API są dostępne we wszystkich lokalizacjach, które planujesz wdrożyć. Aby pobrać wersje interfejsu API zasobu rozszerzenia maszyny Wirtualnej dla zestawów skalowania, należy użyć tego samego polecenia cmdlet, co poprzednio, ale określ typ zasobu zestawu wagowych maszyny wirtualnej, jak pokazano na rysunku:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Każde określone rozszerzenie jest również wersjona. Ta wersja jest `typeHandlerVersion` wyświetlana we właściwości rozszerzenia maszyny Wirtualnej. Upewnij się, że wersja `typeHandlerVersion` określona w elemencie rozszerzeń maszyn wirtualnych szablonu są dostępne w lokalizacjach, w których planujesz wdrożyć szablon. Na przykład następujący kod określa wersję 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Aby pobrać listę dostępnych wersji dla określonego rozszerzenia maszyny Wirtualnej, należy użyć polecenia cmdlet [Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) Poniższy przykład pobiera dostępne wersje rozszerzenia maszyny Wirtualnej dsc programu PowerShell (konfiguracja żądanego stanu) z **myLocation:**

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Aby uzyskać listę wydawców, należy użyć polecenia [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Aby zażądać typu, należy użyć [polecenia Get-AzureRmVMExtensionImageType.](/powershell/module/az.compute/get-azvmextensionimagetype)

## <a name="tips-for-testing-and-automation"></a>Wskazówki dotyczące testowania i automatyzacji

Jest to wyzwanie, aby śledzić wszystkie powiązane ustawienia, możliwości i ograniczenia podczas tworzenia szablonu. Typowym podejściem jest opracowanie i przetestowanie szablonów dla jednej chmury, zanim zostaną zaatakowane inne lokalizacje. Jednak wcześniej, że testy są wykonywane w procesie tworzenia, mniej rozwiązywania problemów i przepisywania kodu zespołu deweloperów będzie musiał zrobić. Wdrożenia, które nie powiodą się z powodu zależności lokalizacji, mogą być czasochłonne do rozwiązywania problemów. Dlatego zalecamy automatyczne testowanie tak wcześnie, jak to możliwe w cyklu tworzenia. Ostatecznie będziesz potrzebować mniej czasu na program i mniej zasobów, a artefakty spójne w chmurze staną się jeszcze bardziej wartościowe.

Na poniższej ilustracji przedstawiono typowy przykład procesu tworzenia dla zespołu przy użyciu zintegrowanego środowiska programistycznego (IDE). Na różnych etapach osi czasu wykonywane są różne typy testów. W tym miejscu dwóch deweloperów pracuje nad tym samym rozwiązaniem, ale ten scenariusz dotyczy jednakowo jednego dewelopera lub dużego zespołu. Każdy deweloper zazwyczaj tworzy lokalną kopię centralnego repozytorium, umożliwiając każdemu z nich pracę nad kopią lokalną bez wpływu na inne osoby, które mogą pracować nad tymi samymi plikami.

![Przepływ pracy](./media/templates-cloud-consistency/workflow.png)

Rozważmy następujące wskazówki dotyczące testowania i automatyzacji:

* Korzystaj z narzędzi testowych. Na przykład Visual Studio Kod i Visual Studio obejmują IntelliSense i inne funkcje, które mogą pomóc w weryfikacji szablonów.
* Aby poprawić jakość kodu podczas tworzenia lokalnego IDE, należy wykonać analizę kodu statycznego za pomocą testów jednostkowych i testów integracji.
* Aby uzyskać jeszcze lepsze środowisko podczas wstępnego rozwoju, testy jednostkowe i testy integracji powinny ostrzegać tylko wtedy, gdy problem zostanie znaleziony i kontynuować testy. W ten sposób można zidentyfikować problemy do rozwiązania i priorytet kolejność zmian, również określane jako wdrożenie oparte na testach (TDD).
* Należy pamiętać, że niektóre testy można wykonać bez połączenia z usługą Azure Resource Manager. Inne, takie jak testowanie wdrażania szablonów, wymagają Menedżera zasobów do wykonywania pewnych akcji, których nie można wykonać w trybie offline.
* Testowanie szablonu wdrożenia względem interfejsu API sprawdzania poprawności nie jest równe rzeczywistemu wdrożeniu. Ponadto nawet jeśli wdrożysz szablon z pliku lokalnego, wszelkie odwołania do szablonów zagnieżdżonych w szablonie są pobierane bezpośrednio przez Menedżera zasobów, a artefakty, do których odwołują się rozszerzenia maszyn wirtualnych, są pobierane przez agenta maszyny Wirtualnej działającego wewnątrz wdrożonej maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące szablonów usługi Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Najważniejsze wskazówki dotyczące szablonów ARM](template-syntax.md)
