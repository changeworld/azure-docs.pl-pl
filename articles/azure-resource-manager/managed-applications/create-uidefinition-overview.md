---
title: Plik CreateUiDefinition.json dla okienka portalu
description: W tym artykule opisano sposób tworzenia definicji interfejsu użytkownika dla witryny Azure portal. Używane podczas definiowania aplikacji zarządzanych platformy Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294888"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Plik CreateUiDefinition.json dla środowiska tworzenia aplikacji zarządzanej platformy Azure

W tym dokumencie przedstawiono podstawowe pojęcia pliku **createUiDefinition.json,** którego usługa Azure Portal używa do definiowania interfejsu użytkownika podczas tworzenia aplikacji zarządzanej.

Szablon jest następujący

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

A CreateUiDefinition zawsze zawiera trzy właściwości: 

* Obsługi
* version
* parameters

Program obsługi powinien `Microsoft.Azure.CreateUIDef`być zawsze , a `0.1.2-preview`najnowsza obsługiwana wersja to .

Schemat właściwości parametrów zależy od kombinacji określonego programu obsługi i wersji. W przypadku aplikacji zarządzanych obsługiwane `basics` `steps`właściwości `outputs`to , i . Podstawowe i kroki właściwości zawierają [elementy](create-uidefinition-elements.md) — takie jak pola tekstowe i listy rozwijane — które mają być wyświetlane w witrynie Azure portal. Właściwość outputs służy do mapowania wartości wyjściowych określonych elementów na parametry szablonu wdrażania usługi Azure Resource Manager.

W `$schema` tym jest zalecane, ale opcjonalne. Jeśli określono, wartość `version` dla musi odpowiadać wersji w identyfikatorze `$schema` URI.

Za pomocą edytora JSON można utworzyć createUiDefinition, a następnie przetestować go w [createUiDefinition Sandbox,](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) aby wyświetlić podgląd. Aby uzyskać więcej informacji na temat piaskownicy, zobacz [Testowanie interfejsu portalu dla aplikacji zarządzanych platformy Azure](test-createuidefinition.md).

## <a name="basics"></a>Podstawy

Podstawy jest pierwszym krokiem generowanym podczas analiz pliku w portalu Azure. Oprócz wyświetlania elementów określonych `basics`w portalu wstrzykuje elementy dla użytkowników, aby wybrać subskrypcję, grupę zasobów i lokalizację dla wdrożenia. Jeśli to możliwe, elementy, które kwerendy parametrów całego wdrożenia, takich jak nazwa klastra lub poświadczenia administratora, należy przejść w tym kroku.

## <a name="steps"></a>Kroki

Właściwość steps może zawierać zero lub więcej dodatkowych kroków do wyświetlenia po podstawach, z których każdy zawiera jeden lub więcej elementów. Należy rozważyć dodanie kroków na rolę lub warstwę wdrażanej aplikacji. Na przykład dodaj krok dla danych wejściowych węzła głównego i krok dla węzłów procesu roboczego w klastrze.

## <a name="outputs"></a>Dane wyjściowe

Portal Azure używa `outputs` właściwości do mapowania elementów z `basics` i `steps` do parametrów szablonu wdrażania usługi Azure Resource Manager. Klucze tego słownika są nazwami parametrów szablonu, a wartości są właściwościami obiektów wyjściowych z elementów, do których istnieje odwołanie.

Aby ustawić nazwę zasobu aplikacji zarządzanej, `applicationResourceName` należy dołączyć wartość o nazwie we właściwości outputs. Jeśli ta wartość nie zostanie ustawiona, aplikacja przypisze identyfikator GUID dla tej nazwy. W interfejsie użytkownika można dołączyć pole tekstowe, które żąda nazwy od użytkownika.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Typy zasobów

Aby filtrować dostępne lokalizacje tylko do tych lokalizacji, które obsługują typy zasobów do wdrożenia, podaj tablicę typów zasobów. Jeśli podasz więcej niż jeden typ zasobu, zwracane są tylko te lokalizacje, które obsługują wszystkie typy zasobów. Ta właściwość jest opcjonalna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funkcje

CreateUiDefinition zapewnia [funkcje](create-uidefinition-functions.md) do pracy z wejściami i wyjściami elementów oraz funkcjami, takimi jak warunki. Te funkcje są podobne zarówno w składni, jak i w funkcji do funkcji szablonu usługi Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

Sam plik createUiDefinition.json ma prosty schemat. Rzeczywista głębia pochodzi ze wszystkich obsługiwanych elementów i funkcji. Elementy te są opisane bardziej szczegółowo na:

- [Elementy](create-uidefinition-elements.md)
- [Funkcje](create-uidefinition-functions.md)

Bieżący schemat JSON dla createUiDefinition `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`jest dostępny tutaj: .

Przykładowy plik interfejsu użytkownika można znaleźć w pliku [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
