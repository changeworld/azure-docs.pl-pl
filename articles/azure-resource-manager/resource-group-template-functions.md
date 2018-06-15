---
title: Funkcje szablonu usługi Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje funkcje do użycia w szablonie usługi Azure Resource Manager pobierania wartości, Praca z ciągów i numeryczne i pobierania informacji o wdrożeniu.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: tomfitz
ms.openlocfilehash: fce3fd03f22c7e2d1c1524b9deb1f6935dbc3755
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359496"
---
# <a name="azure-resource-manager-template-functions"></a>Funkcje szablonów usługi Azure Resource Manager
W tym artykule opisano wszystkie funkcje, których można użyć w szablonie usługi Azure Resource Manager.

Dodawanie funkcji w szablonach ujęte w nawiasy kwadratowe: `[` i `]`odpowiednio. Wyrażenie jest obliczane podczas wdrażania. Podczas zapisywania jako literału ciągu, wynik obliczania wyrażenia może być innego typu JSON, takich jak tablicy, obiektu lub liczbą całkowitą. Po prostu, tak jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)`. Możesz odwoływać się do właściwości przy użyciu operatorów kropka i [Indeks].

Wyrażenia szablonu nie może przekraczać 24 576 znaków.

Funkcje szablonów i ich parametry jest rozróżniana wielkość liter. Na przykład usługi Resource Manager rozpoznaje **variables('var1')** i **VARIABLES('VAR1')** jako takie same. Podczas szacowania, chyba że funkcja modyfikuje wyraźnie liter (na przykład toUpper lub toLower), funkcja zachowuje wielkość liter. Niektóre typy zasobów mogą mieć wielkość wymagania niezależnie od tego, jak są analizowane funkcje.

Aby utworzyć własnych funkcji, zobacz [funkcje zdefiniowane przez użytkownika](resource-group-authoring-templates.md#functions).

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="json" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>Funkcje tablicy i obiektów
Menedżer zasobów zawiera kilka funkcji do pracy z tablicami i obiektami.

* [Tablica](resource-group-template-functions-array.md#array)
* [połączenie](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [zawiera](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [pusty](resource-group-template-functions-array.md#empty)
* [pierwszy](resource-group-template-functions-array.md#first)
* [część wspólną](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [ostatni](resource-group-template-functions-array.md#last)
* [długość](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [Zakres](resource-group-template-functions-array.md#range)
* [Pomiń](resource-group-template-functions-array.md#skip)
* [podejmij](resource-group-template-functions-array.md#take)
* [Unii](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Porównanie funkcji
Resource Manager zapewnia kilka funkcji dla porównywanie w szablonach.

* [równa się](resource-group-template-functions-comparison.md#equals)
* [mniej](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [większa](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Funkcje wartość wdrożenia
Pobieranie wartości z części szablonu i wartości dotyczące wdrażania Menedżera zasobów zawiera następujące funkcje:

* [wdrożenia](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [zmienne](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>Funkcje logiczne
Usługa Resource Manager zapewnia następujące funkcje do pracy z warunków logicznych:

* [I](resource-group-template-functions-logical.md#and)
* [wartość logiczna](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [lub](resource-group-template-functions-logical.md#or)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="numeric-functions"></a>Funkcje numeryczne
Usługa Resource Manager zapewnia następujące funkcje do pracy z liczbami całkowitymi:

* [Dodaj](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [Float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Funkcje zasobów
Usługa Resource Manager zapewnia następujące funkcje do pobierania wartości zasobu:

* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [Lista *](resource-group-template-functions-resource.md#list)
* [dostawców](resource-group-template-functions-resource.md#providers)
* [Odwołanie](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [Subskrypcja](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="guid" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>Funkcje ciągów
Usługa Resource Manager zapewnia następujące funkcje do pracy z ciągami:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [zawiera](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [pusty](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [pierwszy](resource-group-template-functions-string.md#first)
* [Identyfikator GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [ostatni](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [długość](resource-group-template-functions-string.md#length)
* [PadLeft](resource-group-template-functions-string.md#padleft)
* [Zamień](resource-group-template-functions-string.md#replace)
* [Pomiń](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [Ciąg](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [podejmij](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [TRIM](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [Identyfikator URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Kolejne kroki
* Opis części szablonu usługi Azure Resource Manager, zobacz [szablonów Authoring Azure Resource Manager](resource-group-authoring-templates.md)
* Aby scalić wiele szablonów, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md)
* Do wykonywania iteracji określoną liczbę razy podczas tworzenia typu zasobu, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md)
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md)
