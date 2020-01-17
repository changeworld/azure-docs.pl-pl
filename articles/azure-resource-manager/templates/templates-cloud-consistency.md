---
title: Ponowne używanie szablonów w chmurach
description: Tworzenie szablonów usługi Azure Resource Manager, stale działające dla środowisk inną chmurę. Tworzenie nowego elementu lub aktualizowanie istniejących szablonów dla usługi Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 0c69c90410aab7fa37ab87e82314c53e4459ca25
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155659"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Tworzenie szablonów usługi Azure Resource Manager w celu zachowania spójności w chmurze

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Najważniejszą korzyścią z platformy Azure jest spójność. Programowanie inwestycje w jednej lokalizacji są wielokrotnego użytku w innym. Szablon sprawia, że wdrożenia spójnego i powtarzalnego wszystkich środowisk, w tym global Azure, chmurach suwerennych platformy Azure i usługi Azure Stack. Aby ponownie użyć szablonów dla chmur, należy jednak wziąć pod uwagę zależności specyficzne dla chmury, ponieważ w tym przewodniku wyjaśniono.

Firma Microsoft oferuje usługi w chmurze inteligentną i w przedsiębiorstwach w wielu lokalizacjach, w tym:

* Globalne platformy Azure, obsługiwana przez rozrastającą się sieć zarządzanych przez firmę Microsoft centrów danych w regionach na całym świecie.
* Izolowane suwerenne chmury, takie jak Azure (Niemcy, Azure Government i Chiny). Suwerenne chmury zapewnić spójną platformę z większością te same atrakcyjne funkcje, które globalnych klientów platformy Azure mają dostęp do.
* Usługi Azure Stack to platforma chmury hybrydowej, która umożliwia dostarczanie usług platformy Azure z centrum danych Twojej organizacji. Przedsiębiorstwa można skonfigurować usługi Azure Stack w centrach danych na ich własnych lub korzystanie z usług platformy Azure od dostawców usług, uruchamianie usługi Azure Stack w ich urządzenia (czasem nazywane obsługiwanych regionach).

Sercem te chmury usługi Azure Resource Manager zapewnia interfejs API, który umożliwia szeroką gamę interfejsy użytkownika do komunikowania się z platformą Azure. Ten interfejs API zapewnia zaawansowane możliwości infrastruktury jako kodu. Dowolnego typu zasobu, który jest dostępny na platformie Azure w chmurze można wdrożyć i konfigurować za pomocą usługi Azure Resource Manager. Pojedynczy szablon można wdrożyć i skonfigurować pełną aplikacji do stanu końcowego operacyjnej.

![Środowiska platformy Azure](./media/templates-cloud-consistency/environments.png)

Spójność global Azure i suwerennych chmurach hostowanej chmury i chmury w centrum danych pomaga korzystać z usługi Azure Resource Manager. Po skonfigurowaniu zasób oparty na szablonie, wdrażanie i konfigurację można ponownie użyć inwestycji w rozwoju w tych chmurach.

Jednak mimo że globalna suwerennych, hostowaną i chmur hybrydowych świadczenia usług spójne, nie wszystkie chmury są identyczne. W rezultacie można utworzyć szablon z zależnościami w funkcje, które są dostępne tylko w określonej chmurze.

W pozostałej części tego przewodnika opisano obszary, które należy wziąć pod uwagę podczas planowania do tworzenia nowych lub aktualizowanie istniejących szablonów usługi Azure Resource Manager dla usługi Azure Stack. Ogólnie rzecz biorąc z listy kontrolnej powinien zawierać następujące elementy:

* Sprawdź funkcje, punkty końcowe, usług i innych zasobów w szablonie są dostępne w lokalizacjach docelowych wdrożenia.
* Store zagnieżdżonych szablonów i artefaktów konfiguracji dostępnych lokalizacji, na zapewnienie im dostępu w ramach wielu chmur.
* Użyj odwołania dynamicznego zamiast kodować łączy i elementów.
* Upewnij się, że parametry szablonu, którego używasz pracować w chmurach programu docelowego.
* Sprawdź, że dostępne są właściwości specyficzne dla zasobu chmury docelowego.

Wprowadzenie do szablonów usługi Azure Resource Manager, zobacz [wdrożenie szablonu](overview.md).

## <a name="ensure-template-functions-work"></a>Upewnij się, że praca template — funkcje

Podstawowa składnia szablonu usługi Resource Manager jest JSON. Szablony korzystać z nadzbioru JSON, rozszerzając składnię, używając wyrażeń i funkcji. Procesor języka szablonu jest często aktualizowana do obsługi funkcji dodatkowe szablony. Aby uzyskać szczegółowy opis funkcji dostępnych szablonów, zobacz [funkcje szablonu usługi Azure Resource Manager](template-functions.md).

Nowe funkcje szablonu, które zostały wprowadzone do usługi Azure Resource Manager nie są natychmiast dostępne w chmurach suwerennych lub usługi Azure Stack. Aby pomyślnie wdrożyć szablon, wszystkie funkcje, do którego odwołuje się szablonu musi być dostępny w chmurę docelową.

Możliwości usługi Azure Resource Manager będzie zawsze wprowadzane do globalnej platformy Azure najpierw. Poniższy skrypt programu PowerShell służy do Sprawdź, czy nowo wprowadzonych template — funkcje są również dostępne w usłudze Azure Stack:

1. Wprowadzić klona repozytorium GitHub: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Gdy masz lokalnego klona repozytorium, podłącz do docelowej usługi Azure Resource Manager przy użyciu programu PowerShell.

1. Zaimportuj moduł PSM1 i wykonaj polecenie cmdlet Test-AzureRmureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skrypt wdroży wiele szablonów, z których każdy zawiera tylko unikatowe template — funkcje zminimalizowane. Dane wyjściowe skryptu raporty funkcje szablonu obsługiwane i dostępne.

## <a name="working-with-linked-artifacts"></a>Praca z połączonych artefaktów

Szablon może zawierać odwołania do połączonych artefaktów i zawiera zasobu wdrożenia, który stanowi łącze do innego szablonu. Połączone szablony (nazywane również zagnieżdżonych szablonów) są pobierane przez usługę Resource Manager w środowisku uruchomieniowym. Szablon może również zawierać odwołania do artefaktów dla rozszerzeń maszyny wirtualnej (VM). Te artefakty są pobierane przez rozszerzenie maszyny Wirtualnej działają w ramach maszyny Wirtualnej na potrzeby konfiguracji rozszerzenia maszyn wirtualnych podczas wdrażania szablonu.

W poniższych sekcjach opisano zagadnienia dotyczące spójności chmury podczas tworzenia szablonów obejmujących artefaktów, które są zewnętrzne w stosunku do szablonu wdrożenia głównego.

### <a name="use-nested-templates-across-regions"></a>Korzystanie z szablonów zagnieżdżonych w wielu regionach

Szablony mogą być rozłożone na małe, wielokrotnego użytku szablonów, z których każdy ma określonego celu i mogą być ponownie używane w scenariuszach wdrażania. Aby wykonać wdrożenie, należy określić pojedynczy szablon znane jako szablon głównego lub wzorca. Określa zasoby w celu wdrożenia, takie jak sieci wirtualne, maszyny wirtualne i aplikacje sieci web. Szablon głównego może również zawierać link do innego szablonu, co oznacza, że można zagnieżdżać szablonów. Podobnie zagnieżdżonych szablonów mogą zawierać łącza do innych szablonów. Można zagnieżdżać maksymalnie pięć poziomów w głąb.

Poniższy kod pokazuje, jak parametr templateLink odwołuje się do zagnieżdżonych szablonów:

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

Usługa Azure Resource Manager ocenia głównego szablonu w czasie wykonywania i pobiera i ocenia każdy zagnieżdżonych szablonów. Po wszystkich zagnieżdżone szablony są pobierane, szablon jest spłaszczany i inicjowane jest dalsze przetwarzanie.

### <a name="make-linked-templates-accessible-across-clouds"></a>Udostępnienie chmurach połączonymi szablonami

Należy wziąć pod uwagę, gdzie i jak do przechowywania wszelkich połączone szablony, można użyć. W czasie wykonywania, usługi Azure Resource Manager pobiera — i dlatego wymaga bezpośredniego dostępu do — wszystkie połączone szablony. Powszechną praktyką jest usługa GitHub umożliwia przechowywanie zagnieżdżonych szablonów. Repozytorium GitHub może zawierać pliki, które są dostępne publicznie przy użyciu adresu URL. Mimo że ta technika działa dobrze w przypadku chmury publicznej i suwerennych chmur, środowiska Azure Stack może być umieszczona w sieci firmowej lub w odłączonej lokalizacji zdalnej, bez żadnych ruch wychodzący do Internetu. W takich przypadkach usługi Azure Resource Manager zakończy się niepowodzeniem, można pobrać zagnieżdżonych szablonów.

Lepsze rozwiązanie w przypadku wdrożeń w wielu chmur, jest połączone szablony są przechowywane w lokalizacji dostępnej dla docelowej chmury. W idealnym wszystkich artefaktów wdrożenia są obsługiwane w i wdrażanych za pomocą potoku ciągłej integracji/ciągłego rozwoju (CI/CD). Alternatywnie szablonów zagnieżdżonych można przechowywać w kontenera magazynu obiektów blob, w którym usługi Azure Resource Manager można je pobrać.

Ponieważ magazynu obiektów blob na każdej z nich korzysta z innym punktem końcowym pełni kwalifikowaną nazwę domeny (FQDN), należy skonfigurować szablon z lokalizacji połączonymi szablonami z dwoma parametrami. Parametry mogą akceptować dane wejściowe użytkownika w czasie wdrażania. Szablony są zazwyczaj tworzone i współużytkowane przez wiele osób, dlatego najlepszym rozwiązaniem jest użycie standardowej nazwy dla tych parametrów. Konwencje nazewnictwa sprawić, że szablony bardziej wielokrotnego użytku różnych regionach, chmur i autora.

W poniższym kodzie `_artifactsLocation` służy do punktu w jednym miejscu, zawierający wszystkie artefakty związanych z wdrażaniem. Należy zauważyć, że podano wartość domyślną. W czasie wdrażania, jeśli nie wartości wejściowej jest określona dla `_artifactsLocation`, zostanie użyta domyślna wartość. `_artifactsLocationSasToken` Jest używany jako dane wejściowe na potrzeby `sasToken`. Domyślna wartość powinna być ciągiem pustym dla scenariuszy gdzie `_artifactsLocation` nie jest zabezpieczony — na przykład publicznego repozytorium GitHub.

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

W szablonie, łącza są generowane, łącząc podstawowy identyfikator URI (z `_artifactsLocation` parametr) ze ścieżką względnego artefaktów i `_artifactsLocationSasToken`. Poniższy kod pokazuje, jak podaj łącze do zagnieżdżonych szablonów przy użyciu funkcji szablon identyfikatora uri:

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

Za pomocą tej metody, wartość domyślna `_artifactsLocation` parametr jest używany. Jeśli połączonymi szablonami, należy pobrać z innej lokalizacji, parametr wejściowy może służyć w czasie wdrażania, aby zastąpić wartość domyślną — bez zmian do szablonu jest wymagana.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Użyj _artifactsLocation zamiast hardcoding łącza

Oprócz pełnienia dla zagnieżdżonych szablonów, adres URL w `_artifactsLocation` parametr jest używany jako podstawa dla wszystkich powiązanych artefaktów Szablon wdrożenia. Niektóre rozszerzenia maszyny Wirtualnej zawiera łącza do skryptu przechowywane poza szablonu. Aby zainstalować te rozszerzenia należy nie umieszczaj łącza. Na przykład rozszerzeń niestandardowych skryptów i programu PowerShell DSC może połączyć skryptu zewnętrznego w serwisie GitHub, jak pokazano:

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

Hardcoding łącza do skryptu potencjalnie zapobiega szablon wdrażania pomyślnie do innej lokalizacji. Podczas konfigurowania zasobu maszyny Wirtualnej agenta maszyn wirtualnych uruchomionych na maszynie Wirtualnej inicjuje pobieranie wszystkie skrypty, które są połączone w rozszerzeniu maszyny Wirtualnej, a następnie przechowanie skrypty na lokalnym dysku maszyny Wirtualnej. Funkcje tego podejścia, takie jak linki zagnieżdżonych szablonów wcześniej przedstawionych w sekcji "Użyj nested szablonów w regionach".

Usługa Resource Manager pobiera zagnieżdżone szablony w czasie wykonywania. Rozszerzenia maszyny Wirtualnej pobieranie wszelkie artefakty zewnętrznych jest wykonywane przez agenta maszyny Wirtualnej. Oprócz różnych inicjatora pobieranie artefaktu rozwiązania w definicji szablonu jest taka sama. Użyj parametru _artifactsLocation o wartości domyślnej ścieżki podstawowej, w którym przechowywane są wszystkie artefakty (w tym skrypty rozszerzenia maszyny Wirtualnej) i `_artifactsLocationSasToken` parametr dla danych wejściowych dla sasToken.

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

Do konstruowania bezwzględny identyfikator URI artefaktu, preferowaną metodą jest użycie funkcji szablon identyfikatora uri, zamiast funkcji szablonu concat. Za pomocą zastąpienia zapisane na stałe linki do skryptów w rozszerzeniu maszyny Wirtualnej za pomocą funkcji szablon identyfikatora uri, tej funkcji w szablonie jest skonfigurowany w celu zachowania spójności w chmurze.

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

W przypadku tej metody wszystkie artefakty wdrożenia, w tym skrypty konfiguracji mogą być przechowywane w tej samej lokalizacji za pomocą samego szablonu. Aby zmienić lokalizację wszystkich linków, należy określić inny podstawowy adres URL dla _parametrów artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Wziąć pod uwagę różne możliwości regionalne

Programowanie metodą agile i ciągły przepływ aktualizacje i nowe usługi, wprowadzone do systemu Azure [regionach mogą różnić się](https://azure.microsoft.com/regions/services/) w dostępności usługi lub aktualizacji. Po rygorystyczne testy wewnętrzne, nowych usług lub aktualizacje do istniejących usług zwykle są wprowadzane do małych odbiorców klientów uczestniczących w programie sprawdzania poprawności. Po klient pomyślnie weryfikacji aktualizacji lub usług są dostępne w regionach platformy Azure przez, a następnie wprowadzone do większej liczbie regionów, wdrażane w suwerennych chmurach i potencjalnie udostępnione dla klientów usługi Azure Stack, jak również.

Wiedząc, że regiony platformy Azure i chmury może różnić się w ich dostępnych usług, można określeniu niektórych aktywnego szablonów. Jest dobrym miejscem do rozpoczęcia, sprawdzając dostępnych dostawców zasobów dla chmury. Dostawca zasobów informuje zestaw zasobów i operacji, które są dostępne dla usługi platformy Azure.

Szablon wdraża i konfiguruje zasoby. Typ zasobu jest dostarczana przez dostawcę zasobów. Na przykład dostawcy zasobów obliczeniowych (Microsoft.Compute) zawiera wiele typów zasobów, takich jak maszyn wirtualnych i availabilitySets. Każdy dostawca zasobów udostępnia interfejs API do usługi Azure Resource Manager zdefiniowane przez typowe umowy, umożliwiając spójny, jednolity środowisko tworzenia we wszystkich dostawców zasobów. Jednak dostawca zasobów, który jest dostępny w global Azure może nie być dostępne w regionie usługi Azure Stack lub należących do suwerennej chmury.

![Dostawcy zasobów](./media/templates-cloud-consistency/resource-providers.png)

Aby sprawdzić dostawców zasobów, które są dostępne w danej chmury, uruchom następujący skrypt w interfejsie wiersza polecenia platformy Azure ([interfejsu wiersza polecenia](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Następujące polecenie cmdlet programu PowerShell umożliwia również wyświetlić dostępnych dostawców zasobów:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Sprawdź wersję wszystkie typy zasobów

Zbiór właściwości jest wspólny dla wszystkich typów zasobów, ale każdy zasób ma również swój własny określonej właściwości. Nowe funkcje i powiązane właściwości są dodawane do istniejących typów zasobów w czasie za pomocą nowej wersji interfejsu API. Zasób w szablonie ma swój własny właściwość wersji API - `apiVersion`. Tej wersji zapewnia, że istniejącej konfiguracji zasobów w szablonie nie ma wpływu zmian na platformie.

Nowe wersje interfejsu API, wprowadzone do istniejących typów zasobów na platformie Azure globalne mogą natychmiast być niedostępne we wszystkich regionach, suwerenne chmury i usługi Azure Stack. Aby wyświetlić listę dostępnych dostawców zasobów, typy zasobów i wersje interfejsu API dla chmury, używając Eksploratora zasobów w witrynie Azure portal. Wyszukaj Eksploratora zasobów, w menu wszystkie usługi. Rozwiń węzeł dostawców w Eksploratorze zasobów do zwrócenia wszystkich dostępnych dostawców zasobów, typy zasobów i wersje interfejsu API w tej chmury.

Aby wyświetlić listę dostępnych wersji interfejsu API dla wszystkich typów zasobów w danej chmury w interfejsie wiersza polecenia platformy Azure, uruchom następujący skrypt:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Umożliwia także następujące polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Zapoznaj się lokalizacje zasobów za pomocą parametru

Szablon zawsze jest wdrażana w grupie zasobów, która znajduje się w regionie. Oprócz samego wdrożenia poszczególnych zasobów w szablonie ma właściwość lokalizacji, która służy do określania regionu do wdrożenia. Tworzenie szablonu w celu zachowania spójności w chmurze, należy dynamiczny sposób do odwoływania się do lokalizacji zasobu, ponieważ każdej usługi Azure Stack może zawierać nazwy unikatową lokalizację. Zazwyczaj są wdrażane zasoby w tym samym regionie co grupa zasobów, ale do obsługi scenariuszy, takich jak dostępność aplikacji między regionami, może być przydatne w celu rozłożenia zasobów między różnymi regionami.

Mimo, że możesz zakodować nazwy regionów podczas określania właściwości zasobów w szablonie, to podejście nie gwarantuje, że szablonu można wdrożyć w innych środowiskach Azure Stack, ponieważ nazwa regionu najprawdopodobniej nie istnieje istnieje.

Aby uwzględnić różnych regionach, należy dodać lokalizacją parametru wejściowego do szablonu z wartością domyślną. Jeśli nie określono wartości podczas wdrażania, zostanie użyta wartość domyślna.

Funkcja szablonu `[resourceGroup()]` zwraca obiekt, który zawiera następujące pary klucz/wartość:

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

Odwołując się do klucza lokalizacji obiektu w defaultValue parametru wejściowego, usługi Azure Resource Manager w czasie wykonywania, zastąpi `[resourceGroup().location]` funkcji szablonu o nazwie lokalizacja grupy zasobów do wdrażania szablonu.

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

Za pomocą tej funkcji szablonu można wdrożyć szablon z każdą chmurą bez nawet z wyprzedzeniem znajomości nazw regionów. Ponadto lokalizacji dla określonego zasobu w szablonie mogą się różnić od lokalizacji grupy zasobów. W takim przypadku można ją skonfigurować przy użyciu dodatkowych parametrów wejściowych dla tego określonego zasobu, podczas gdy inne zasoby w tym samym szablonie nadal używać parametru wejściowego początkową lokalizację.

### <a name="track-versions-using-api-profiles"></a>Śledzenie wersji przy użyciu profilów interfejsu API

Może być bardzo trudne do śledzenia wszystkich dostępnych dostawców zasobów i pokrewne wersje interfejsu API, które znajdują się w usłudze Azure Stack. Na przykład w czasie pisania, najnowsza wersja interfejsu API dla **Microsoft.Compute/availabilitySets** na platformie Azure to `2018-04-01`, gdy jest dostępna wersja interfejsu API wspólne dla platformy Azure i usługi Azure Stack `2016-03-30`. Typowe wersja interfejsu API dla **Microsoft.Storage/storageAccounts** współużytkowane przez wszystkie lokalizacje platformy Azure i usługi Azure Stack jest `2016-01-01`, a najnowsza wersja interfejsu API na platformie Azure to `2018-02-01`.

Z tego powodu usługi Resource Manager wprowadzono koncepcję profilami interfejsu API do szablonów. Bez profilu interfejsu API poszczególnych zasobów w szablonie jest skonfigurowany przy użyciu `apiVersion` element, który zawiera opis wersji interfejsu API dla tego określonego zasobu.

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

Wersja profilu interfejsu API działa jako alias dla jednej wersji interfejsu API na typ zasobu jest wspólny dla platformy Azure i usługi Azure Stack. Zamiast określania wersją interfejsu API dla każdego zasobu w szablonie, określ wersja profilu interfejsu API w elemencie głównym o nazwie `apiProfile` i pominąć `apiVersion` element dla poszczególnych zasobów.

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

Profil interfejsu API zapewnia, że wersje interfejsu API są dostępne w lokalizacjach, dzięki czemu nie trzeba ręcznie zweryfikować apiVersions, które są dostępne w określonej lokalizacji. Aby upewnić się, że wersje interfejsów API odwołuje się profil interfejsów API znajdują się w środowisku usługi Azure Stack, Operatorzy usługi Azure Stack musi przechowywać aktualne rozwiązania na podstawie zasad pomocy technicznej. Jeśli system jest nieaktualna, jest uznawane za niezgodne, a środowisko musi zostać zaktualizowana więcej niż sześć miesięcy.

Profil interfejsu API, który nie jest wymagany element w szablonie. Nawet jeśli dodasz element, tylko posłuży za zasoby, dla którego nie `apiVersion` jest określony. Ten element umożliwia stopniowe zmiany, ale nie wymaga żadnych zmian w istniejących szablonów.

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

## <a name="check-endpoint-references"></a>Sprawdź odwołania do punktu końcowego

Zasoby mogą mieć odwołania do innych usług na platformie. Na przykład publiczny adres IP może mieć publicznej nazwy DNS do niej przypisany. Chmura publiczna, suwerenne chmury i rozwiązań usługi Azure Stack mają własne przestrzenie nazw unikatowych punktu końcowego. W większości przypadków zasobu wymaga tylko prefiks jako dane wejściowe w szablonie. Podczas wykonywania usługi Azure Resource Manager dołącza wartość punktu końcowego do niego. Niektóre wartości punktu końcowego muszą być jawnie określone w szablonie.

> [!NOTE]
> Tworzenie szablonów w celu zachowania spójności w chmurze, nie umieszczaj punktu końcowego w przestrzeni nazw.

Dwa poniższe przykłady są wspólnej przestrzeni nazw punktu końcowego, które muszą być jawnie określone podczas tworzenia zasobu:

* Kont usługi Storage (blob, kolejki, tabela i plik)
* Parametry połączenia dla baz danych i pamięci podręcznej Azure redis Cache

Punkt końcowy w przestrzeni nazw można również w danych wyjściowych szablonu jako informacje dla użytkownika po ukończeniu wdrażania. Poniżej przedstawiono typowe przykłady:

* Kont usługi Storage (blob, kolejki, tabela i plik)
* Parametry połączenia (MySql, SQLServer, SQLAzure, niestandardowe, Centrum NotificationHub, magistrali usług, Centrum zdarzeń, ApiHub, DocDb, usługi RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel publiczny adres IP
* Korzystaj z zalet usług w chmurze

Ogólnie rzecz biorąc Unikaj endpoints zapisane na stałe w szablonie. Najlepszym rozwiązaniem jest używanie funkcji szablonu odwołania do dynamicznego pobierania punktów końcowych. Na przykład punkt końcowy najczęściej zapisane na stałe jest przestrzenią nazw punktu końcowego dla kont magazynu. Każde konto magazynu ma unikatową nazwę FQDN, który jest konstruowany, łącząc nazwę konta magazynu z przestrzenią nazw punktu końcowego. Konto magazynu obiektów blob o nazwie mystorageaccount1 wyników w różne nazwy FQDN, w zależności od tego, w chmurze:

* **mystorageaccount1.blob.Core.Windows.NET** podczas tworzenia w chmurze Azure, globalne.
* **mystorageaccount1.blob.Core.chinacloudapi.CN** utworzone w chmurze 21Vianet platformy Azure w Chinach.

Następujące odwołanie do funkcji szablonu pobiera przestrzeń nazw punktu końcowego z dostawcę zasobów magazynowych:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Przez zastąpienie wartości zapisane na stałe punktu końcowego konta magazynu przy użyciu `reference` funkcji szablonu umożliwia tego samego szablonu wdrożenia różnych środowiskach pomyślnie bez wprowadzania żadnych zmian do odwołania do punktu końcowego.

### <a name="refer-to-existing-resources-by-unique-id"></a>Zapoznaj się z istniejącymi zasobami przez unikatowy identyfikator

Można się również odwoływać do istniejącego zasobu z tego samego lub innego zasobu, grupy i w ramach tej samej subskrypcji lub innej subskrypcji w ramach tej samej dzierżawy w tej samej chmurze. Aby pobrać właściwości zasobu, należy użyć Unikatowy identyfikator dla samego zasobu. `resourceId` Funkcji szablonu pobiera unikatowy identyfikator zasobów takich jak SQL Server, co ilustruje poniższy kod:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Następnie można użyć `resourceId` działa wewnątrz `reference` funkcji szablonu, można pobrać właściwości bazy danych. Zwracany obiekt zawiera `fullyQualifiedDomainName` właściwość, która przechowuje wartość pełne punktu końcowego. Ta wartość jest pobierana w czasie wykonywania i zapewnia przestrzeń nazw z punktem końcowym specyficznym dla środowiska chmury. Aby zdefiniować parametry połączenia bez hardcoding przestrzeni nazw punktu końcowego, mogą odwoływać się do właściwości zwracanego obiektu bezpośrednio w parametrach połączenia, jak pokazano:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Należy wziąć pod uwagę właściwości zasobów

Określone zasoby w środowiskach usługi Azure Stack mają unikatowych właściwości, które należy wziąć pod uwagę w szablonie.

### <a name="ensure-vm-images-are-available"></a>Upewnij się, że dostępne obrazy maszyn wirtualnych

Platforma Azure zapewnia bogaty wybór obrazów maszyn wirtualnych. Te obrazy są tworzone i przygotowanie do wdrożenia przez firmę Microsoft i partnerów. Obrazy stanowią podstawę dla maszyn wirtualnych na platformie. Jednak spójnej chmurze szablonu powinni zapoznać się tylko dostępne parametry — w szczególności wydawcy, oferty i jednostki SKU dostępne dla globalnej platformy Azure, Azure należących do suwerennej chmury lub rozwiązanie usługi Azure Stack obrazów maszyn wirtualnych.

Aby pobrać listę dostępnych obrazów maszyn wirtualnych w lokalizacji, uruchom następujące polecenie z wiersza polecenia platformy Azure:

```azurecli-interactive
az vm image list -all
```

Możesz pobrać tę samą listę za pomocą polecenia cmdlet programu Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) i określ lokalizację, z `-Location` parametru. Przykład:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

To polecenie może zająć kilka minut, aby zwrócić wszystkie dostępne obrazy w regionie Europa Zachodnia, globalne chmury platformy Azure.

Jeśli zostanie udostępniona tych obrazów maszyn wirtualnych do usługi Azure Stack, dostępnego magazynu będzie używane. Aby uwzględnić nawet najmniejszy jednostki skalowania, usługi Azure Stack można wybrać obrazy, które chcesz dodać do środowiska.

Poniższy przykład kodu pokazuje spójne podejście do odwoływania się do wydawcy, oferty i jednostki SKU parametrów w szablonach usługi Azure Resource Manager:

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

### <a name="check-local-vm-sizes"></a>Sprawdź lokalne rozmiarów maszyn wirtualnych

Do tworzenia szablonu w celu zachowania spójności w chmurze, należy się upewnić, że odpowiedni rozmiar maszyny Wirtualnej jest dostępna we wszystkich środowiskach docelowych. Rozmiary maszyn wirtualnych są grupowania charakterystyki wydajności i możliwości. Przykładowe rozmiary maszyny Wirtualnej zależą od sprzętu, na uruchomiony na maszynie Wirtualnej. Na przykład jeśli chcesz wdrożyć maszyny Wirtualnej zoptymalizowane pod kątem procesora GPU, sprzęt, na którym działa funkcja hypervisor musi mieć sprzętu procesorów GPU.

Gdy firma Microsoft przedstawia nowy rozmiar maszyny Wirtualnej, która ma niektórych zależności sprzętu, rozmiar maszyny Wirtualnej jest zwykle udostępniane najpierw w mały podzbiór regiony w chmurze platformy Azure. Później staje się dostępny do innych regionów i chmury. Aby upewnić się, że rozmiar maszyny Wirtualnej znajduje się w każdej chmurze, w których wdrażana, możesz pobrać dostępne rozmiary, za pomocą następującego polecenia wiersza polecenia platformy Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Dla programu Azure PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Aby uzyskać pełną listę dostępnych usług, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Sprawdź użycie usługi Azure Managed Disks w usłudze Azure Stack

Zarządzane dyski uchwyt magazynu dla dzierżawy usługi Azure. Zamiast jawnie Tworzenie konta magazynu, a następnie określając identyfikator URI wirtualnego dysku twardego (VHD) umożliwia dysków zarządzanych niejawnie, gdy wdrożysz maszynę Wirtualną, wykonaj następujące akcje. Dyski zarządzane zwiększyć dostępność przez umieszczenie wszystkich dysków z maszyn wirtualnych w zestawie do innego magazynu jednostki dostępności. Ponadto istniejących wirtualnych dysków twardych można przekonwertować od planu Standard do usługi Premium storage znacznie mniej przestojów.

Mimo, że dyski zarządzane znajdują się na plan usługi Azure Stack, są obecnie nie są obsługiwane. Aż do chwili, możesz tworzyć szablony spójnej chmury dla usługi Azure Stack, jawnie określając przy użyciu wirtualnych dysków twardych `vhd` elementu w szablonie zasobu maszyny Wirtualnej, jak pokazano:

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

W przeciwieństwie do określenia konfiguracji dysku zarządzanego w szablonie, należy usunąć `vhd` elementu z konfiguracji dysku.

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

Te same zmiany mają zastosowanie również [dysków z danymi](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Sprawdź, czy rozszerzenia maszyn wirtualnych są dostępne w usłudze Azure Stack

Inną ważną kwestią dotyczącą chmury spójności polega na użyciu [rozszerzenia maszyny wirtualnej](../../virtual-machines/windows/extensions-features.md) do konfigurowania zasobów wewnątrz maszyny Wirtualnej. Nie wszystkie rozszerzenia maszyny Wirtualnej są dostępne w usłudze Azure Stack. Szablon można określić zasoby przeznaczone do rozszerzenia maszyny Wirtualnej, tworzenia zależności i warunków w ramach szablonu.

Na przykład jeśli chcesz skonfigurować maszynę Wirtualną z systemem Microsoft SQL Server, rozszerzenia maszyny Wirtualnej można skonfigurować programu SQL Server w ramach wdrożenia szablonu. Należy wziąć pod uwagę, co się stanie, jeśli wdrożenie zawiera również serwer aplikacji skonfigurowany tak, aby utworzyć bazę danych na maszynie Wirtualnej z uruchomionym programem SQL Server. Oprócz również za pomocą rozszerzenia maszyny Wirtualnej dla serwerów aplikacji, można skonfigurować zależności serwera aplikacji na pomyślne zwracany zasobów rozszerzenia maszyny Wirtualnej programu SQL Server. To podejście zapewnia, że maszyna wirtualna działa program SQL Server jest skonfigurowane i dostępne, gdy serwer aplikacji jest zobowiązany do utworzenia bazy danych.

Podejścia deklaratywnego szablonu umożliwia definiowanie stanu końcowego zasobów i ich wzajemnych zależności, podczas gdy platforma dba o logiki wymaganej zależności.

#### <a name="check-that-vm-extensions-are-available"></a>Sprawdź, czy są dostępne rozszerzenia maszyny Wirtualnej

Istnieje wiele rodzajów rozszerzeń maszyn wirtualnych. Podczas tworzenia szablonu w celu zachowania spójności w chmurze, pamiętaj użyć rozszerzeń, które są dostępne we wszystkich regionach docelowych szablonu.

Aby pobrać listę rozszerzeń maszyn wirtualnych, które są dostępne dla określonego regionu (w tym przykładzie `myLocation`), uruchom następujące polecenie z wiersza polecenia platformy Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Można również wykonać programu Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) polecenia cmdlet i użyj `-Location` do określenia lokalizacji obrazu maszyny wirtualnej. Przykład:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Upewnij się, że wersje są dostępne

Ponieważ rozszerzenia maszyn wirtualnych są zasobami usługi Resource Manager firmy Microsoft, mają własne wersji interfejsu API. Poniższy kod ilustruje typ rozszerzenia maszyny Wirtualnej jest zagnieżdżonych zasobów dostawcy zasobów Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Wersja interfejsu API zasobu rozszerzenia maszyny Wirtualnej musi być obecny we wszystkich lokalizacjach, które ma pod kątem z szablonem. Zależność lokalizacji działa jak dostawca zasobów interfejsu API w wersji dostępności wspomniano w sekcji "Zweryfikuj wersję wszystkich typów zasobów".

Aby pobrać listę dostępnych wersji interfejsu API dla zasobu rozszerzenia maszyny Wirtualnej, użyj [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) polecenia cmdlet z **Microsoft.Compute** dostawcy zasobów, jak pokazano:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Można również użyć rozszerzenia maszyn wirtualnych w zestawach skalowania maszyn wirtualnych. Mają zastosowanie te same warunki lokalizacji. Tworzenie szablonu w celu zachowania spójności w chmurze, upewnij się, że wersje interfejsu API są dostępne we wszystkich lokalizacjach, na którym planujesz wdrożenie. Aby pobrać wersje interfejsu API zasobu rozszerzenia maszyny Wirtualnej dla zestawów skalowania, użyj tego samego polecenia cmdlet jako przed, ale określ skalowania maszyn wirtualnych ustawia typ zasobu, jak pokazano:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Każde rozszerzenie określonych jest także numerów wersji. Ta wersja jest wyświetlana w `typeHandlerVersion` właściwości rozszerzenia maszyny Wirtualnej. Upewnij się, że wersja określona w `typeHandlerVersion` elementu rozszerzenia maszyny Wirtualnej z szablonu są dostępne w lokalizacjach, w którym planujesz wdrożyć szablon. Na przykład poniższy kod określa wersję 1.7:

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

Aby pobrać listę dostępnych wersji dla określonego rozszerzenia maszyny Wirtualnej, użyj [Get AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) polecenia cmdlet. Poniższy przykład pobiera dostępnych wersji dla rozszerzenia maszyny Wirtualnej w programie PowerShell DSC (Desired State Configuration) z **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Aby uzyskać listę wydawców, użyj [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) polecenia. Aby typ żądania, należy użyć [Get AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Porady dotyczące testowania i automatyzacja

To żądanie, aby śledzić wszystkie powiązane ustawienia, możliwości i ograniczeń podczas tworzenia szablonu. Typowym podejściem jest tworzenie i testowanie szablonów względem pojedynczej chmury przed są stosowane w innych lokalizacjach. Jednak wcześniej, które testy są wykonywane w procesie tworzenia, mniej rozwiązywania problemu i przebudowa kodu, Twój zespół deweloperów spowoduje, że. Wdrożenia, które się nie powieść z powodu zależności lokalizacji może być czasochłonne, rozwiązywać problemy. Dlatego zaleca się testowanie automatyczne możliwie jak najszybciej w cyklu tworzenia pakietów administracyjnych. Ostatecznie musisz mieć mniej czasu na programowanie i mniejszej ilości zasobów i spójnej chmurze artefaktów staną się bardziej wartościowe.

Na poniższej ilustracji przedstawiono typowy przykład procesu projektowania dla zespołu za pomocą zintegrowanym środowisku programistycznym (IDE). Na różnych etapach na osi czasu testu różne typy są wykonywane. W tym miejscu dwóch deweloperów pracuje na tym samym rozwiązaniu, ale w tym scenariuszu, stosuje się jednakowo do jednego dewelopera lub duży zespół. Każdy deweloper zazwyczaj tworzy kopię lokalną repozytorium centralnym, włączania każdej z nich do pracy nad lokalną kopię bez wywierania wpływu na pozostałe kto pracuje tych samych plików.

![Przepływ pracy](./media/templates-cloud-consistency/workflow.png)

Należy wziąć pod uwagę na poniższe porady dotyczące testowania i automatyzacji:

* Wprowadzić korzystanie z narzędzia do testowania. Na przykład programu Visual Studio Code i Visual Studio obejmują funkcję IntelliSense i inne funkcje, które mogą pomóc Ci sprawdzanie poprawności szablonów.
* Aby poprawić jakość kodu podczas programowania w lokalnym środowisku IDE, należy wykonać statycznej analizy kodu za pomocą testów jednostkowych i testów integracji.
* Jeszcze lepsze środowisko pracy podczas tworzenia początkowej, testy jednostkowe i testy integracji tylko ostrzega, gdy problem zostanie znaleziony i kontynuować wykonywanie testów. W ten sposób można zidentyfikować problemy, które zostały rozwiązane i ustalić ich priorytety kolejność zmiany, zwaną także wdrożenia oparte na testach (TDD).
* Należy pamiętać, że niektóre testy mogą być wykonywane bez połączenia do usługi Azure Resource Manager. Inne, takie jak testowanie wdrożenia szablonu wymagają usługi Resource Manager do wykonywania pewnych działań, które nie mogą być wykonywane w trybie offline.
* Testowanie Szablon wdrożenia względem walidacji interfejsu API nie jest równa rzeczywiste wdrożenie. Ponadto nawet wtedy, gdy należy wdrożyć szablon z pliku lokalnego, wszelkie odwołania do szablonów zagnieżdżonych w szablonie są pobierane przez usługę Resource Manager bezpośrednio i artefakty przywoływane przez rozszerzenia maszyny Wirtualnej są pobierane przez agenta maszyny Wirtualnej uruchomionej w wdrożonej maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące szablonów usługi Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Najlepsze rozwiązania dotyczące szablonów usługi Azure Resource Manager](template-syntax.md)
