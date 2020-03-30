---
title: Przycisk Wdróż na platformie Azure
description: Użyj przycisku, aby wdrożyć szablony usługi Azure Resource Manager z repozytorium Usługi GitHub.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109052"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Wdrażanie szablonów z repozytorium Usługi GitHub za pomocą przycisku wdrażania

W tym artykule opisano, jak użyć przycisku **Wdrażanie na platformie Azure** do wdrażania szablonów z repozytorium Usługi GitHub. Przycisk można dodać bezpośrednio do pliku README.md w repozytorium GitHub lub do strony sieci web, która odwołuje się do repozytorium.

## <a name="use-common-image"></a>Używanie wspólnego obrazu

Aby dodać przycisk do strony internetowej lub repozytorium, użyj następującego obrazu:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Obraz jest wyświetlany w następujący sposób:

![Przycisk Wdróż na platformie Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Tworzenie adresu URL do wdrażania szablonu

Aby utworzyć adres URL szablonu, zacznij od nieprzetworzonego adresu URL szablonu w repozytorium:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Następnie zakoduj go adresem URL. Można użyć kodera online lub uruchomić polecenie. W poniższym przykładzie programu PowerShell pokazano, jak kodować wartość według adresu URL.

```powershell
[uri]::EscapeDataString($url)
```

Przykładowy adres URL ma następującą wartość podczas kodowania adresu URL.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Każdy link zaczyna się od tego samego podstawowego adresu URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Dodaj link do szablonu zakodowanego w adresie URL na końcu podstawowego adresu URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Masz pełny adres URL linku.

## <a name="create-deploy-to-azure-button"></a>Przycisk Utwórz wdrażanie w usłudze Azure

Na koniec umieść link i obraz razem.

Aby dodać przycisk z Markdown w pliku README.md w repozytorium GitHub lub na stronie internetowej, użyj:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

W przypadku kodu HTML użyj:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby przetestować pełne rozwiązanie, wybierz następujący przycisk:

[![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Portal wyświetla okienko, które umożliwia łatwe podanie wartości parametrów. Parametry są wstępnie wypełnione wartościami domyślnymi z szablonu.

![Wdrażanie za pomocą portalu](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o szablonach, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
