---
title: Funkcje szablonu Menedżer zasobów | Microsoft Docs
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pobierania wartości, pracy z ciągami i wartościami liczbowymi oraz pobierania informacji o wdrożeniu.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 04358f21af79838f894dc50d5d9c165da67a332c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894158"
---
# <a name="azure-resource-manager-template-functions"></a>Funkcje szablonu Azure Resource Manager

W tym artykule opisano wszystkie funkcje, których można użyć w szablonie Azure Resource Manager. Aby uzyskać informacje o używaniu funkcji w szablonie, zobacz [składnia szablonu](template-expressions.md).

Aby utworzyć własne funkcje, zobacz [funkcje zdefiniowane przez użytkownika](resource-group-authoring-templates.md#functions).

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funkcje tablicy i obiektów
Menedżer zasobów udostępnia kilka funkcji do pracy z tablicami i obiektami.

* [macierzy](resource-group-template-functions-array.md#array)
* [łączonych](resource-group-template-functions-array.md#coalesce)
* [Concat](resource-group-template-functions-array.md#concat)
* [wyświetlana](resource-group-template-functions-array.md#contains)
* [przearray](resource-group-template-functions-array.md#createarray)
* [ciągiem](resource-group-template-functions-array.md#empty)
* [pierwszego](resource-group-template-functions-array.md#first)
* [część wspólną](resource-group-template-functions-array.md#intersection)
* [kodu](resource-group-template-functions-array.md#json)
* [ostatniego](resource-group-template-functions-array.md#last)
* [Długość](resource-group-template-functions-array.md#length)
* [długości](resource-group-template-functions-array.md#min)
* [Maksymalny](resource-group-template-functions-array.md#max)
* [zakresu](resource-group-template-functions-array.md#range)
* [Skocz](resource-group-template-functions-array.md#skip)
* [czasochłonn](resource-group-template-functions-array.md#take)
* [Unii](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funkcje porównania
Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [ubiegł](resource-group-template-functions-comparison.md#equals)
* [wcześniejsz](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [mniejszą](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funkcje wartości wdrożenia
Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości z sekcji szablonu i wartości związanych ze wdrożeniem:

* [mieszczeniu](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [modyfikacj](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funkcje logiczne
Menedżer zasobów udostępnia następujące funkcje do pracy z warunkami logicznymi:

* [lub](resource-group-template-functions-logical.md#and)
* [logiczna](resource-group-template-functions-logical.md#bool)
* [przypadku](resource-group-template-functions-logical.md#if)
* [niemożliwe](resource-group-template-functions-logical.md#not)
* [oraz](resource-group-template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Funkcje liczbowe
Menedżer zasobów udostępnia następujące funkcje do pracy z liczbami całkowitymi:

* [dodana](resource-group-template-functions-numeric.md#add)
* [Funkcji copyindex](resource-group-template-functions-numeric.md#copyindex)
* [służąc](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [ZAOKR](resource-group-template-functions-numeric.md#int)
* [długości](resource-group-template-functions-numeric.md#min)
* [Maksymalny](resource-group-template-functions-numeric.md#max)
* [Funkcja](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [Sub](resource-group-template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Funkcje zasobów
Menedżer zasobów udostępnia następujące funkcje pobierania wartości zasobów:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [staw](resource-group-template-functions-resource.md#list)
* [udostępnia](resource-group-template-functions-resource.md#providers)
* [odwoła](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [Identyfikator](resource-group-template-functions-resource.md#resourceid)
* [Subskrypcja](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Funkcje ciągów
Menedżer zasobów udostępnia następujące funkcje do pracy z ciągami:

* [zakodowan](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [Concat](resource-group-template-functions-string.md#concat)
* [wyświetlana](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [ciągiem](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [pierwszego](resource-group-template-functions-string.md#first)
* [Formatowanie](resource-group-template-functions-string.md#format)
* [ident](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [ostatniego](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [Długość](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [stępować](resource-group-template-functions-string.md#replace)
* [Skocz](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [parametry](resource-group-template-functions-string.md#string)
* [podciąg](resource-group-template-functions-string.md#substring)
* [czasochłonn](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [Trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [adresu](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [tworzenie Azure Resource Manager szablonów](resource-group-authoring-templates.md) .
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z Azure Resource Manager](resource-group-linked-templates.md)
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](resource-group-create-multiple.md)
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy.md)
