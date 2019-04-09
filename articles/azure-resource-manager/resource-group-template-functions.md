---
title: Funkcje szablonu usługi Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje funkcje na potrzeby szablonu usługi Azure Resource Manager w stanie odzyskać wartości, pracować z parametrów i wartości numeryczne i pobierania informacji o wdrożeniu.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: b5a1f12a877008a3ce2ff7bd9635b9ed47b379f7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280319"
---
# <a name="azure-resource-manager-template-functions"></a>Funkcje szablonu usługi Azure Resource Manager
W tym artykule opisano wszystkie funkcje, których można użyć w szablonie usługi Azure Resource Manager. Aby dowiedzieć się, jak za pomocą funkcji w szablonie, zobacz [składni szablonu](resource-group-authoring-templates.md#syntax).

Aby utworzyć własne funkcje, zobacz [funkcje zdefiniowane przez użytkownika](resource-group-authoring-templates.md#functions).

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
Resource Manager zapewnia kilka funkcji do pracy z tablicami i obiektami.

* [tablica](resource-group-template-functions-array.md#array)
* [COALESCE](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [zawiera](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [pusty](resource-group-template-functions-array.md#empty)
* [pierwszy](resource-group-template-functions-array.md#first)
* [Część wspólna](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [ostatni](resource-group-template-functions-array.md#last)
* [Długość](resource-group-template-functions-array.md#length)
* [min.](resource-group-template-functions-array.md#min)
* [maks.](resource-group-template-functions-array.md#max)
* [Zakres](resource-group-template-functions-array.md#range)
* [pomiń](resource-group-template-functions-array.md#skip)
* [Wypełnij](resource-group-template-functions-array.md#take)
* [Unia](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Funkcje porównania
Resource Manager udostępnia kilka funkcji składania porównania w szablonach.

* [równa się](resource-group-template-functions-comparison.md#equals)
* [mniej](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [większa](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Funkcje wartość wdrożenia
Pobieranie wartości z części szablonu i wartości związane z wdrażaniem usługi Resource Manager zawiera następujące funkcje:

* [wdrożenie](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [Zmienne](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>Funkcje logiczne
Usługa Resource Manager zapewnia następujące funkcje do pracy za pomocą warunków logicznych:

* [i](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [nie](resource-group-template-functions-logical.md#not)
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

## <a name="numeric-functions"></a>Funkcje liczbowe
Usługa Resource Manager zapewnia następujące funkcje do pracy z liczb całkowitych:

* [add](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min.](resource-group-template-functions-numeric.md#min)
* [maks.](resource-group-template-functions-numeric.md#max)
* [dzielenie modulo](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [Sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Funkcje zasobów
Usługa Resource Manager zapewnia następujące funkcje w celu uzyskania wartości zasobu:

* [listAccountSas](resource-group-template-functions-resource.md#list)
* [klucze list](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [Lista *](resource-group-template-functions-resource.md#list)
* [dostawcy](resource-group-template-functions-resource.md#providers)
* [Odwołanie](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [subskrypcja](resource-group-template-functions-resource.md#subscription)

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

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [zawiera](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [pusty](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [pierwszy](resource-group-template-functions-string.md#first)
* [format](resource-group-template-functions-string.md#format)
* [Identyfikator GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [ostatni](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [Długość](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [Zastąp](resource-group-template-functions-string.md#replace)
* [pomiń](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [string](resource-group-template-functions-string.md#string)
* [podciąg](resource-group-template-functions-string.md#substring)
* [Wypełnij](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [TRIM](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [identyfikator URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md)
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md)
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md)
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md)
