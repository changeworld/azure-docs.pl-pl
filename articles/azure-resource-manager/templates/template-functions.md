---
title: Funkcje szablonów
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pobierania wartości, pracy z ciągami i numerykami oraz pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982382"
---
# <a name="arm-template-functions"></a>Funkcje szablonu ARM

W tym artykule opisano wszystkie funkcje, których można używać w szablonie usługi Azure Resource Manager (ARM). Aby uzyskać informacje dotyczące korzystania z funkcji w szablonie, zobacz [składnię szablonu](template-expressions.md).

Aby utworzyć własne funkcje, zobacz [Funkcje zdefiniowane przez użytkownika](template-syntax.md#functions).

Większość funkcji działa tak samo po wdrożeniu do grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawy. Kilka funkcji nie może być używanych we wszystkich zakresach. Są one odnotowane na poniższych listach.

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

* [tablica](template-functions-array.md#array)
* [Łączonej](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [Zawiera](template-functions-array.md#contains)
* [tworzenieArray](template-functions-array.md#createarray)
* [Pusty](template-functions-array.md#empty)
* [Pierwszym](template-functions-array.md#first)
* [Przecięcia](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Ostatnio](template-functions-array.md#last)
* [Długość](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [Zakres](template-functions-array.md#range)
* [Pominąć](template-functions-array.md#skip)
* [wziąć](template-functions-array.md#take)
* [Unii](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funkcje porównania

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [equals](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Funkcje daty

Menedżer zasobów udostępnia następujące funkcje do pracy z datami.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [Utcnow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funkcje wartości wdrażania

Menedżer zasobów udostępnia następujące funkcje uzyskiwania wartości z sekcji szablonu i wartości związanych z wdrożeniem:

* [Wdrażania](template-functions-deployment.md#deployment)
* [Środowiska](template-functions-deployment.md#environment)
* [Parametry](template-functions-deployment.md#parameters)
* [Zmiennych](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funkcje logiczne

Menedżer zasobów udostępnia następujące funkcje do pracy z warunkami logicznymi:

* [I](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [Nie](template-functions-logical.md#not)
* [Lub](template-functions-logical.md#or)

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

* [add](template-functions-numeric.md#add)
* [copyIndex (copyIndex)](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [Mul](template-functions-numeric.md#mul)
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

Menedżer zasobów udostępnia następujące funkcje uzyskiwania wartości zasobów:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listaKontakonsa](template-functions-resource.md#list)
* [klawisze list](template-functions-resource.md#listkeys)
* [listaKryrets](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [Dostawców](template-functions-resource.md#providers)
* [Odwołanie](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - może być używany tylko we wdrożeniach do grupy zasobów.
* [resourceId](template-functions-resource.md#resourceid) - może być używany w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach do grupy zasobów lub subskrypcji.
* [identyfikator zasobów subskrypcji](template-functions-resource.md#subscriptionresourceid)
* [identyfikator źródłu zewnętrznego tenant](template-functions-resource.md#tenantresourceid)

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

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [Zawiera](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Pusty](template-functions-string.md#empty)
* [Endswith](template-functions-string.md#endswith)
* [Pierwszym](template-functions-string.md#first)
* [Formacie](template-functions-string.md#format)
* [Identyfikator guid](template-functions-string.md#guid)
* [Indexof](template-functions-string.md#indexof)
* [Ostatnio](template-functions-string.md#last)
* [Lastindexof](template-functions-string.md#lastindexof)
* [Długość](template-functions-string.md#length)
* [nowyGuid](template-functions-string.md#newguid)
* [Padleft](template-functions-string.md#padleft)
* [Zastąpić](template-functions-string.md#replace)
* [Pominąć](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [Startswith](template-functions-string.md#startswith)
* [Ciąg](template-functions-string.md#string)
* [Podciąg](template-functions-string.md#substring)
* [wziąć](template-functions-string.md#take)
* [Tolower](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Przycinanie](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Identyfikator uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać opis sekcji w szablonie ARM, zobacz [Tworzenie szablonów ARM](template-syntax.md)
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md)
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji z szablonami ARM](deploy-powershell.md)
