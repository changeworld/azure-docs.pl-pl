---
title: Ustawić kolejność wdrażania zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu ustawiania jeden zasób zależny od innego zasobu podczas wdrażania, aby upewnić się, że zasoby są wdrażane w odpowiedniej kolejności.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: d5a9bde85e894f2f4283348771dc5cacc7a08f23
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824659"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Zdefiniuj kolejność wdrażania zasobów w szablonach Menedżera zasobów Azure
Dla danego zasobu może być inne zasoby, które muszą istnieć przed wdrożeniem zasobu. Na przykład serwer SQL musi istnieć przed podjęciem próby wdrożenia bazy danych SQL. Ta relacja należy zdefiniować oznaczając jednego zasobu jako zależny od innego zasobu. Definiowanie zależności z **dependsOn** elementu, lub za pomocą **odwołania** funkcji. 

Menedżer zasobów ocenia zależności między zasobami i wdraża je w porządku zależnych. Gdy zasoby nie są zależne od siebie, Menedżer zasobów wdraża je równolegle. Wystarczy Definiowanie zależności dla zasobów, które zostały wdrożone w tym samym szablonie. 

## <a name="dependson"></a>dependsOn
W szablonie dependsOn element umożliwia zdefiniowanie jednego zasobu jako zależną na jeden lub więcej zasobów. Wartość może być rozdzielana przecinkami lista nazw zasobów. 

W poniższym przykładzie przedstawiono zestaw skali maszyny wirtualnej, która zależy od usługi równoważenia obciążenia, sieć wirtualną i pętli, które tworzy wiele kont magazynu. Te inne zasoby nie są wyświetlane w poniższym przykładzie, ale musi istnieć w innym miejscu w szablonie.

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

W powyższym przykładzie zależności znajduje się na zasoby, które są tworzone przez pętlę kopiowania o nazwie **storageLoop**. Na przykład zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md).

Podczas definiowania zależności, mogą obejmować przestrzeń nazw dostawcy zasobów i typu zasobu, aby uniknąć niejednoznaczności. Na przykład aby wyjaśnić, usługi równoważenia obciążenia i sieci wirtualnej, która może mieć tej samej nazwy jak inne zasoby, użyj następującego formatu:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Gdy może żądać na potrzeby mapowania relacje między zasobami dependsOn, ważne jest zrozumienie, dlaczego podczas jej wykonywania. Na przykład do dokumentów, jak zasoby są połączone ze sobą, dependsOn nie jest podejście. Nie można wykonać zapytania, które zasoby zostały zdefiniowane w elemencie dependsOn po wdrożeniu. Za pomocą dependsOn, możesz potencjalnie wpływ czas wdrażania, ponieważ Menedżer zasobów nie wdrożyć w równoległych dwa zasoby, które mają zależności. Relacje między zasobami dokumentu, zamiast tego użyć [łączenia zasobów](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Zasoby podrzędne
Właściwość zasobów pozwala określ zasoby podrzędne, które odnoszą się do zasobu został określony. Zasoby podrzędne może być tylko zdefiniowanych głębokości pięciu poziomów. Należy pamiętać, że niejawne zależności nie jest tworzone między zasobu podrzędnego i zasobu nadrzędnego. Jeśli potrzebujesz zasobu podrzędnego do wdrożenia po zasobu nadrzędnego musi jawnie określać tej zależności z właściwością dependsOn. 

Każdy zasób nadrzędnego akceptuje tylko niektóre typy zasobów jako zasoby podrzędne. Typy zasobów akceptowane są określone w [schematu szablonu](https://github.com/Azure/azure-resource-manager-schemas) zasobu nadrzędnego. Nazwa typu zasobu podrzędnego zawiera nazwę typu zasobu nadrzędnego, takich jak **Microsoft.Web/sites/config** i **Microsoft.Web/sites/extensions** są oba zasoby podrzędne o **Microsoft.Web/sites**.

W poniższym przykładzie pokazano, SQL server i bazy danych SQL. Zwróć uwagę zdefiniowania jawne zależności między bazy danych SQL i programu SQL server, nawet, jeśli baza danych jest elementem podrzędnym serwera.

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

## <a name="reference-and-list-functions"></a>Funkcje dokumentacja i listy
[Odwołania funkcja](resource-group-template-functions-resource.md#reference) umożliwia wyrażenie ma być wartość z innych pary nazw i wartości JSON lub zasobów środowiska uruchomieniowego. [Listy * funkcje](resource-group-template-functions-resource.md#listkeys-listsecrets-and-list) zwracane wartości dla zasobu z listy operacji.  Odwołanie i listy wyrażeń niejawnie zadeklarować, że jeden zasób jest zależny od innego, po wdrożeniu w tym samym szablonie żądanego zasobu i określone na podstawie swojej nazwy (nie identyfikator zasobu). Jeśli identyfikator zasobu jest przekazywane do odwołania lub listę funkcji, nie jest tworzona niejawne odwołanie.

Jest ogólny format odwołanie funkcji:

```json
reference('resourceName').propertyPath
```

Funkcja listKeys formatu jest:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

W poniższym przykładzie punktu końcowego usługi CDN jawnie zależy od profilu CDN i niejawnie zależy od aplikacji sieci web.

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

Aby określić zależności można użyć tego elementu lub dependsOn element, ale nie trzeba używać obu zasobów zależnych. Jeśli to możliwe, użyj niejawne odwołanie, aby uniknąć, dodawanie niepotrzebnych zależności.

Aby dowiedzieć się więcej, zobacz [odwołania funkcja](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Zalecenia dotyczące ustawiania zależności

Podczas podejmowania decyzji o zależności, które można ustawić, skorzystaj z poniższych wskazówek:

* Jak to możliwe, należy ustawić jako kilka zależności.
* Ustaw zasobu podrzędnego jako zależy od jego zasobu nadrzędnego.
* Użyj **odwołania** funkcji i podaj nazwę zasobu, aby ustawić niejawne zależności między zasobami, które należy udostępnić właściwości. Nie dodawaj jawne zależności (**dependsOn**) Jeśli zdefiniowano niejawne zależności. Takie podejście zmniejsza ryzyko niepotrzebnych zależności. 
* Ustaw zależność zasobu nie może być **utworzony** bez funkcji z innego zasobu. Nie należy ustawiać zależność, jeśli zasoby komunikować się tylko po wdrożeniu.
* Let zależności cascade bez jawnie ich ustawienia. Na przykład na komputerze wirtualnym zależy od interfejsu sieci wirtualnej, a interfejs sieci wirtualnej jest zależny od sieci wirtualnej i publiczne adresy IP. W związku z tym maszyny wirtualnej jest wdrożone zasoby po wszystkich trzech, ale nie jest jawnie ustawiana maszynę wirtualną jako zależne od wszystkich trzech zasobów. Takie podejście wyjaśnia, kolejność zależności i ułatwia później zmienić szablonu.
* Jeśli wartość można określić przed wdrożeniem, spróbuj wdrażanie zasobu bez zależności. Na przykład jeśli wartość konfiguracji wymaga innego zasobu, może być konieczne nie zależności. W tych wskazówkach nie zawsze działa, ponieważ niektóre zasoby sprawdzenia istnienia innego zasobu. Jeśli wystąpi błąd, należy dodać zależności. 

Menedżer zasobów identyfikuje zależności cykliczne podczas walidacji szablonu. Jeśli zostanie wyświetlony komunikat o błędzie informujący, że istnieje zależność cykliczną, ocenić szablonu czy wszelkie zależności nie są potrzebne i mogą zostać usunięte. Jeśli usuwanie zależności nie działa, można uniknąć zależności cykliczne przez przeniesienie niektórych operacji rozmieszczania w zasoby podrzędne, które są wdrażane po zasoby, które mają zależność cykliczną. Na przykład wdrażasz dwóch maszyn wirtualnych, ale należy ustawić na każdej z nich, która odwołuje się do innych właściwości. Można je wdrożyć w następującej kolejności:

1. vm1
2. vm2
3. Rozszerzenie na vm1 zależy od vm1 i maszyny vm2. Rozszerzenie ustawia wartości vm1, który otrzymuje od maszyny vm2.
4. Rozszerzenie maszyny vm2 zależy od vm1 i maszyny vm2. Rozszerzenie ustawia wartości maszyny vm2, który otrzymuje od vm1.

Informacje o ocenie kolejność wdrażania i rozwiązywaniu problemów z błędami zależności, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące rozwiązywania problemów zależności podczas wdrażania, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Azure Resource Manager, zobacz [tworzenia szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [szablonu funkcji](resource-group-template-functions.md).

