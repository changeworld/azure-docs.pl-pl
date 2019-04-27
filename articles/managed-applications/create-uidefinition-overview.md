---
title: Omówienie tworzenia interfejsu użytkownika aplikacji zarządzanych definicji dla platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu tworzenia definicji interfejsu użytkownika usługi Azure Managed Applications
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
ms.openlocfilehash: ab777b487159b009bf2cac6086bb09cc71714b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587754"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Tworzenie interfejsu użytkownika witryny Azure portal dla aplikacji zarządzanej
Ten dokument wprowadza podstawowe pojęcia dotyczące pliku createUiDefinition.json. Azure portal używa tego pliku do generowania interfejsu użytkownika do tworzenia aplikacji zarządzanej.

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
* version
* parameters

Dla zarządzanych aplikacji, procedura obsługi powinna zawsze być `Microsoft.Compute.MultiVm`, a najnowszą obsługiwaną wersję `0.1.2-preview`.

Schemat właściwości parametrów zależy od kombinacji obsługi i wersji. Dla zarządzanych aplikacji, są obsługiwane właściwości `basics`, `steps`, i `outputs`. Właściwości podstawy i kroki zawierają _elementy_ — takich jak pola tekstowe i list rozwijanych - mają być wyświetlane w witrynie Azure portal. Dane wyjściowe jest używana do mapowania wartości danych wyjściowych o określonych elementów do parametrów szablonu wdrożenia usługi Azure Resource Manager.

W tym `$schema` jest zalecane, ale opcjonalne. Jeśli zostanie określony, wartość `version` musi odpowiadać wersji w ramach `$schema` identyfikatora URI.

## <a name="basics"></a>Podstawy
Krok podstawy zawsze stanowi pierwszy krok kreatora generowane, gdy w witrynie Azure portal analizuje plik. Oprócz wyświetlania elementów wymienionych w `basics`, portalu wprowadza elementy użytkownikom na wybór subskrypcji, grupy zasobów i lokalizacji wdrożenia. Ogólnie rzecz biorąc elementy, które zapytania dla parametrów całe wdrożenie, takie jak nazwa poświadczenia klastra lub administrator powinien przeprowadzić w tym kroku.

Jeśli zachowanie zależy od subskrypcji użytkownika, grupy zasobów lub lokalizacji, ten element, nie można użyć w podstawy. Na przykład **Microsoft.Compute.SizeSelector** zależy od użytkownika subskrypcji i lokalizacji, aby określić listę dostępnych rozmiarów. W związku z tym **Microsoft.Compute.SizeSelector** należy używać tylko w krokach. Ogólnie rzecz biorąc, tylko elementy w **Microsoft.Common** przestrzeni nazw mogą być używane w podstawy. Mimo że niektóre elementy w innych przestrzeniach nazw (takich jak **Microsoft.Compute.Credentials**), nie należy polegać na kontekście użytkownika, są nadal dozwolone.

## <a name="steps"></a>Kroki
Właściwość kroków może zawierać zero lub więcej dodatkowych czynności w celu wyświetlenia od podstaw, z których każdy zawiera jeden lub więcej elementów. Rozważ dodanie czynności dla roli lub warstwy wdrażana aplikacja. Na przykład dodać krok dla danych wejściowych dla węzłów głównych i krok dla węzłów procesu roboczego w klastrze.

## <a name="outputs"></a>Dane wyjściowe
Korzysta z witryny Azure portal `outputs` właściwość elementy z mapy `basics` i `steps` parametry szablonu wdrożenia usługi Azure Resource Manager. Klucze tego słownika są nazwy parametrów szablonu, a wartości są właściwości obiektów danych wyjściowych z elementów.

Aby ustawić nazwę zasobu aplikacji zarządzanej, musi zawierać wartość o nazwie `applicationResourceName` we właściwości danych wyjściowych. Jeśli ta wartość nie jest ustawiona, aplikacji przypisuje identyfikator GUID dla nazwy. Pole tekstowe można uwzględnić w interfejsie użytkownika, który żąda nazwę użytkownika.

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
Podobnie jak funkcje szablonu w usłudze Azure Resource Manager (zarówno w składni i funkcji), CreateUiDefinition udostępnia funkcje do pracy z elementów danych wejściowych i danych wyjściowych, a także funkcje takie jak warunkowych.

## <a name="next-steps"></a>Kolejne kroki
Sam plik createUiDefinition.json ma prosty schemat. Rzeczywiste głębokość pochodzi z wszystkie obsługiwane elementy i funkcje. Te elementy są opisane bardziej szczegółowo na:

- [Elementy](create-uidefinition-elements.md)
- [Funkcje](create-uidefinition-functions.md)

Bieżący schemat JSON dla createUiDefinition jest dostępny tutaj: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Aby uzyskać przykładowy plik interfejsu użytkownika, zobacz [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
