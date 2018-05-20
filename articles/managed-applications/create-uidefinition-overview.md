---
title: Zrozumienie Tworzenie interfejsu użytkownika definicji dla platformy Azure zarządzanych aplikacji | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia definicji interfejsu użytkownika dla aplikacji Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 59003e71324f5342cb2b724f670603fd6b67afe4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Tworzenie interfejsu użytkownika portalu Azure w aplikacji zarządzanej
Tym dokumencie przedstawiono podstawowe koncepcje createUiDefinition.json pliku. Azure portal używa tego pliku do generowania interfejsu użytkownika do tworzenia zarządzanej aplikacji.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition zawsze zawiera trzy właściwości: 

* Program obsługi
* wersja
* parameters

Dla zarządzanych aplikacji obsługi powinna zawsze być `Microsoft.Compute.MultiVm`, a najnowszą obsługiwaną wersję `0.1.2-preview`.

Schemat właściwości parametrów zależy od kombinacja określona Obsługa i wersji. Dla zarządzanych aplikacji są obsługiwane właściwości `basics`, `steps`, i `outputs`. Zawiera właściwości podstawy i kroki _elementy_ — takich jak pola tekstowe i listę rozwijaną - mają być wyświetlane w portalu Azure. Właściwość wyniki służy do mapowania wartości określonych elementów danych wyjściowych do parametrów szablonu wdrożenia usługi Azure Resource Manager.

W tym `$schema` jest zalecane, ale opcjonalne. Jeśli określona wartość `version` musi być zgodna z wersją w `$schema` identyfikatora URI.

## <a name="basics"></a>Podstawy
Krok podstawy zawsze jest to pierwszy etap kreatora generowane, gdy plik zostanie przetworzony portalu Azure. Oprócz wyświetlania elementów określonych w `basics`, portalu injects elementy użytkownicy wybiorą subskrypcji, grupy zasobów i lokalizacji dla wdrożenia. Ogólnie rzecz biorąc elementy, które kwerendy parametry dla całego wdrożenia, takie jak nazwa klastra lub administratora poświadczeń, należy go w tym kroku.

Jeśli element zachowanie zależy od subskrypcji, grupy zasobów lub lokalizacji użytkownika, tego elementu nie można użyć w podstawy. Na przykład **Microsoft.Compute.SizeSelector** jest zależny od subskrypcji i lokalizacji, aby określić listę dostępnych rozmiarów użytkownika. W związku z tym **Microsoft.Compute.SizeSelector** można używać tylko w krokach. Ogólnie rzecz biorąc, tylko elementy w **Microsoft.Common** przestrzeni nazw mogą być używane w podstawy. Mimo że niektóre elementy w innych przestrzeniach nazw (takich jak **Microsoft.Compute.Credentials**) które nie są zależne od kontekstu użytkownika, nadal jest dozwolone.

## <a name="steps"></a>Kroki
Właściwość czynności może zawierać zero lub więcej dodatkowych czynności w celu wyświetlenia po podstawy, z których każdy zawiera jeden lub więcej elementów. Rozważ dodanie czynności na roli lub warstwy aplikacji wdrażany. Na przykład dodać krok dla danych wejściowych dla węzłów głównych i krok dla węzłów procesu roboczego w klastrze.

## <a name="outputs"></a>Dane wyjściowe
Azure portal używa `outputs` właściwości, aby zamapować elementy z `basics` i `steps` parametrów szablonu wdrażania Menedżera zasobów Azure. Klucze tego słownika są nazwy parametrów szablonu, a wartości to właściwości obiektów danych wyjściowych z elementów.

Aby ustawić nazwę zasobów zarządzanych aplikacji, musi zawierać wartość o nazwie `applicationResourceName` we właściwości danych wyjściowych. Jeśli ta wartość nie jest ustawiona, aplikacja przypisuje identyfikator GUID dla nazwy. Pole tekstowe można uwzględnić w interfejsie użytkownika żąda nazwę użytkownika.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Funkcje
Podobnie jak szablonu funkcji w usłudze Azure Resource Manager (zarówno w składni i funkcji), CreateUiDefinition zapewnia funkcje do pracy z elementów danych wejściowych i wyjściowych, jak również funkcje, takie jak warunkowe instrukcje.

## <a name="next-steps"></a>Kolejne kroki
Sam plik createUiDefinition.json ma prosty schematu. Rzeczywistą liczbę pochodzi z wszystkie obsługiwane elementy i funkcje. Te elementy są opisane bardziej szczegółowo na:

- [Elementy](create-uidefinition-elements.md)
- [Funkcje](create-uidefinition-functions.md)

Bieżącego schematu JSON dla createUiDefinition jest dostępnych tutaj: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Aby uzyskać przykładowy plik interfejsu użytkownika, zobacz [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
