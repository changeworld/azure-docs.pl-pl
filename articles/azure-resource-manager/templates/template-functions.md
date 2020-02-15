---
title: Funkcje szablonów
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pobierania wartości, pracy z ciągami i wartościami liczbowymi oraz pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a9d10ad4899f35acd45069cb3d351a60632fed3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207046"
---
# <a name="azure-resource-manager-template-functions"></a>Funkcje szablonu Azure Resource Manager

W tym artykule opisano wszystkie funkcje, których można użyć w szablonie Azure Resource Manager. Aby uzyskać informacje o używaniu funkcji w szablonie, zobacz [składnia szablonu](template-expressions.md).

Aby utworzyć własne funkcje, zobacz [funkcje zdefiniowane przez użytkownika](template-syntax.md#functions).

Większość funkcji działa tak samo w przypadku wdrożenia w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. Nie można używać kilku funkcji we wszystkich zakresach. Są one wymienione na poniższej liście.

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

* [macierzy](template-functions-array.md#array)
* [łączonych](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [wyświetlana](template-functions-array.md#contains)
* [przearray](template-functions-array.md#createarray)
* [ciągiem](template-functions-array.md#empty)
* [pierwszego](template-functions-array.md#first)
* [część wspólną](template-functions-array.md#intersection)
* [kodu](template-functions-array.md#json)
* [ostatniego](template-functions-array.md#last)
* [Długość](template-functions-array.md#length)
* [długości](template-functions-array.md#min)
* [Maksymalny](template-functions-array.md#max)
* [zakresu](template-functions-array.md#range)
* [Skocz](template-functions-array.md#skip)
* [czasochłonn](template-functions-array.md#take)
* [Unii](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funkcje porównania

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [ubiegł](template-functions-comparison.md#equals)
* [wcześniejsz](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [mniejszą](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funkcje wartości wdrożenia

Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości z sekcji szablonu i wartości związanych ze wdrożeniem:

* [mieszczeniu](template-functions-deployment.md#deployment)
* [naturalne](template-functions-deployment.md#environment)
* [parameters](template-functions-deployment.md#parameters)
* [modyfikacj](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funkcje logiczne

Menedżer zasobów udostępnia następujące funkcje do pracy z warunkami logicznymi:

* [lub](template-functions-logical.md#and)
* [logiczna](template-functions-logical.md#bool)
* [przypadku](template-functions-logical.md#if)
* [niemożliwe](template-functions-logical.md#not)
* [lub](template-functions-logical.md#or)

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

* [dodana](template-functions-numeric.md#add)
* [Funkcji copyindex](template-functions-numeric.md#copyindex)
* [służąc](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [ZAOKR](template-functions-numeric.md#int)
* [długości](template-functions-numeric.md#min)
* [Maksymalny](template-functions-numeric.md#max)
* [Funkcja](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

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

Usługa Resource Manager zapewnia następujące funkcje w celu uzyskania wartości zasobu:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [staw](template-functions-resource.md#list)
* [udostępnia](template-functions-resource.md#providers)
* [odwoła](template-functions-resource.md#reference)
* Grupa [zasobów — może](template-functions-resource.md#resourcegroup) być używana tylko we wdrożeniach w ramach grupy.
* [ResourceID](template-functions-resource.md#resourceid) — można użyć w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach w grupie zasobów lub subskrypcji.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

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

* [zakodowan](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [wyświetlana](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [ciągiem](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [pierwszego](template-functions-string.md#first)
* [Formatowanie](template-functions-string.md#format)
* [ident](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [ostatniego](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [Długość](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [stępować](template-functions-string.md#replace)
* [Skocz](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [parametry](template-functions-string.md#string)
* [podciąg](template-functions-string.md#substring)
* [czasochłonn](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [adresu](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [tworzenie Azure Resource Manager szablonów](template-syntax.md) .
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z Azure Resource Manager](linked-templates.md)
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md)
