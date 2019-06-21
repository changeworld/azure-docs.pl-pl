---
title: Ustaw kolejność wdrażania zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak ustawić jeden zasób jako zależny od innego zasobu podczas wdrażania, aby upewnić się, że zasoby są wdrażane w odpowiedniej kolejności.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 32b2b41e47fe089da70d82e6049d0139795df88a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204227"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Zdefiniuj kolejność wdrażania zasobów w szablonach usługi Resource Manager platformy Azure
Dla danego zasobu może być inne zasoby, które muszą istnieć przed wdrożeniem tego zasobu. Na przykład programu SQL server musi istnieć przed podjęciem próby wdrożenia bazy danych SQL. Należy zdefiniować tę relację, oznaczając jeden zasób jako zależny od innego zasobu. Definiowanie zależności za pomocą **dependsOn** elementu, lub za pomocą **odwołania** funkcji. 

Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Wystarczy Definiowanie zależności dla zasobów, które są wdrażane w tym samym szablonie. 

Aby zapoznać się z samouczkiem, zobacz [samouczek: Tworzenie szablonów usługi Azure Resource Manager przy użyciu zasobów zależnych](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
W ramach szablonu dependsOn element umożliwia zdefiniowanie jednego zasobu jako zależną na co najmniej jednego zasobu. Wartość może być rozdzielaną przecinkami listę nazw zasobów. 

Poniższy przykład przedstawia zestaw skalowania maszyn wirtualnych, który zależy od modułu równoważenia obciążenia, sieć wirtualną i pętli, która tworzy wiele kont magazynu. Inne zasoby nie są wyświetlane w poniższym przykładzie, ale muszą istnieć w innym miejscu w szablonie.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

W powyższym przykładzie zależność znajduje się na zasoby, które są tworzone przez pętlę kopiowania, o nazwie **storageLoop**. Aby uzyskać przykład, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).

Podczas definiowania zależności, może zawierać przestrzeń nazw dostawcy zasobów i typu zasobu, aby uniknąć niejednoznaczności. Na przykład aby wyjaśnić, moduł równoważenia obciążenia i sieć wirtualną, która może mieć takie same nazwy inne zasoby, należy użyć następującego formatu:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Chociaż może żądać Mapowanie relacji między zasobami za pomocą dependsOn, jest ważne, aby zrozumieć, dlaczego wykonujesz go. Na przykład do dokumentów, jak zasoby są ze sobą połączone, dependsOn nie ma właściwej metody postępowania. Nie można wykonać zapytania, które zasoby zostały zdefiniowane w elemencie dependsOn po wdrożeniu. Za pomocą dependsOn, możesz mogłoby to wpłynąć na czas wdrażania, ponieważ Menedżer zasobów nie wdroży w równoległych dwa zasoby, które mają zależności. 

## <a name="child-resources"></a>Zasoby podrzędne
Właściwość zasobów pozwala określić zasoby podrzędne, that are related to zasób jest zdefiniowany. Zasoby podrzędne mogą być tylko zdefiniowanych pięć poziomów w głąb. Należy zauważyć, że zależności niejawne wdrożenia nie jest tworzone między zasobu podrzędnego i zasobu nadrzędnego. Jeśli potrzebujesz zasobu podrzędnego do wdrożenia po zasobu nadrzędnego, musi jawnie określać tej zależności z właściwością dependsOn. 

Każdy zasób nadrzędny akceptuje tylko niektóre typy zasobów jako zasoby podrzędne. Typy zasobów akceptowane są określone w [schemat szablonu](https://github.com/Azure/azure-resource-manager-schemas) zasobu nadrzędnego. Nazwa typu zasobu podrzędnego obejmuje takie jak nazwa typu zasobu nadrzędnego **Microsoft.Web/sites/config** i **Microsoft.Web/sites/extensions** są zasobami podrzędnymi zarówno **Microsoft.Web/sites**.

Poniższy przykład pokazuje, SQL server i bazy danych SQL. Zwróć uwagę, jawne zależności jest zdefiniowany między bazą danych SQL i programu SQL server, nawet jeśli baza danych jest elementem podrzędnym serwerze.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>Funkcje odwołań i listy
[Odwoływać się do funkcji](resource-group-template-functions-resource.md#reference) umożliwia wyprowadzanie wartości z innych par nazw i wartości JSON lub zasobów w czasie wykonywania wyrażenia. [Listy * funkcje](resource-group-template-functions-resource.md#list) wartości zwracane dla zasobu z listy operacji.  Odwołanie i lista wyrażeń niejawnie zadeklarować, że jeden zasób zależy od drugiej, jeśli przywoływany zasób został wdrożony w tym samym szablonie i określonego przez nazwę (a nie identyfikator zasobu). Identyfikator zasobu w przypadku przekazania do funkcji odwołanie lub listę, niejawne odwołanie nie jest tworzony.

Jest ogólny format odwołanie funkcji:

```json
reference('resourceName').propertyPath
```

Ogólny format funkcji listKeys jest:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

W poniższym przykładzie punktu końcowego usługi CDN jawnie zależy od profilu usługi CDN i niejawnie zależy od aplikacji sieci web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Można użyć tego elementu lub elementu dependsOn, aby określić zależności, ale nie trzeba używać ich obu zasobów zależnych. Jeśli to możliwe, umożliwia niejawne odwołanie unikać dodawania niepotrzebne zależności.

Aby dowiedzieć się więcej, zobacz [odwoływać się do funkcji](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Zależności cykliczne

Menedżer zasobów identyfikuje zależności cykliczne podczas weryfikowania szablonu. Jeśli zostanie wyświetlony komunikat o błędzie informujący, że istnieje zależność cykliczną, ocenić szablonu, czy jakieś zależności, nie są potrzebne i można je usunąć. Jeśli usuwanie zależności nie rozwiąże problemu, możesz uniknąć zależności cykliczne, przenosząc pewne operacje wdrażania w zasoby podrzędne, które są wdrażane po zasoby, które mają zależności cyklicznej. Na przykład załóżmy, że wdrożysz dwie maszyny wirtualne, ale należy ustawić właściwości na każdym z nich, które odwołują się do drugiego. Można je wdrożyć w następującej kolejności:

1. vm1
2. vm2
3. Rozszerzenie maszyny vm1 zależy od tego, vm1 i vm2. Rozszerzenie ustawia wartości maszyny vm1, który otrzymuje od maszyny vm2.
4. Rozszerzenia dla maszyny vm2, zależy od vm1 i vm2. Rozszerzenie ustawia wartości dla maszyny vm2, która otrzymuje od vm1.

Aby uzyskać informacji o ocenie kolejność wdrażania i rozwiązywanie błędów zależności, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby wykonać kroki samouczka, zobacz [samouczek: Tworzenie szablonów usługi Azure Resource Manager przy użyciu zasobów zależnych](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Zalecenia dotyczące podczas ustawiania zależności w temacie [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
* Aby uzyskać informacje dotyczące rozwiązywania problemów zależności podczas wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).

