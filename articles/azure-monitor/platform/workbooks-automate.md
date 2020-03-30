---
title: Skoroszyty monitora platformy Azure i szablony usługi Azure Resource Manager
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom usługi Azure Monitor wdrożonym za pośrednictwem szablonów usługi Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658408"
---
# <a name="programmatically-manage-workbooks"></a>Programowo zarządzaj skoroszytami

Właściciele zasobów mają możliwość programowego tworzenia skoroszytów i zarządzania nimi za pomocą szablonów Menedżera zasobów. 

Może to być przydatne w scenariuszach, takich jak:
* Wdrażanie raportów analitycznych specyficznych dla organizacji lub domeny wraz z wdrożeniami zasobów. Na przykład można wdrożyć skoroszyty wydajności i awarii specyficzne dla organizacji dla nowych aplikacji lub maszyn wirtualnych.
* Wdrażanie standardowych raportów lub pulpitów nawigacyjnych przy użyciu skoroszytów dla istniejących zasobów.

Skoroszyt zostanie utworzony w żądanej grupie podrzędnej/zasobów i z zawartością określoną w szablonach Menedżera zasobów.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Szablon usługi Azure Resource Manager do wdrażania skoroszytów
1. Otwórz skoroszyt, który chcesz wdrożyć programowo.
2. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
3. Otwórz _Edytor zaawansowany_ _</>_ za pomocą przycisku na pasku narzędzi.
4. W edytorze przełącz _szablon Type szablonu_ do _szablonu Menedżera zasobów_.
5. Szablon Menedżera zasobów do tworzenia jest wyświetlany w edytorze. Skopiuj zawartość i użyj jej jako — jest lub scal ją z większym szablonem, który również wdraża zasób docelowy.

    ![Obraz przedstawiający sposób uzyskania szablonu Menedżera zasobów z poziomu interfejsu użytkownika skoroszytu](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Przykładowy szablon usługi Azure Resource Manager
Ten szablon pokazuje, jak wdrożyć prosty skoroszyt, który wyświetla "Hello World!".
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parametry szablonu

| Parametr | Wyjaśnienie |
| :------------- |:-------------|
| `workbookDisplayName` | Przyjazna nazwa skoroszytu używanego w Galerii lub Zapisanej liście. Musi być unikatowy w zakresie grupy zasobów i źródła |
| `workbookType` | Galeria, w obszarze w których będzie wyświetlany skoroszyt. Obsługiwane wartości obejmują skoroszyt, `tsg`azure monitor itp. |
| `workbookSourceId` | Identyfikator wystąpienia zasobu, z którym będzie skojarzony skoroszyt. Nowy skoroszyt będzie wyświetlał się powiązany z tym wystąpieniem zasobu — na przykład w tabeli zawartości zasobu w obszarze _Skoroszyt_. Jeśli chcesz, aby skoroszyt był widoczny w galerii skoroszytu w usłudze Azure Monitor, użyj ciągu _Usługi Azure Monitor_ zamiast identyfikatora zasobu. |
| `workbookId` | Unikatowy identyfikator guid dla tego wystąpienia skoroszytu. Użyj _[newGuid()],_ aby automatycznie utworzyć nowy identyfikator guid. |
| `kind` | Służy do określania, czy utworzony skoroszyt jest udostępniony, czy prywatny. Użyj wartości _udostępnionej_ dla skoroszytów udostępnionych i _dla użytkowników_ prywatnych. |
| `location` | Lokalizacja platformy Azure, w której zostanie utworzony skoroszyt. Użyj _funkcji [resourceGroup().location],_ aby utworzyć ją w tej samej lokalizacji co grupa zasobów |
| `serializedData` | Zawiera zawartość lub ładunek, który ma być używany w skoroszycie. Aby uzyskać wartość, użyj szablonu Menedżera zasobów z interfejsu użytkownika skoroszytów |

### <a name="workbook-types"></a>Typy skoroszytów
Typy skoroszytów określają, w obszarze której galeria skoroszytu wpisze się, w której pojawi się nowe wystąpienie skoroszytu. Dostępne są następujące opcje:

| Typ | Lokalizacja galerii |
| :------------- |:-------------|
| `workbook` | Wartość domyślna używana w większości raportów, w tym w galerii skoroszytów w usłudze Application Insights, usłudze Azure Monitor itp.  |
| `tsg` | Galeria Przewodniki rozwiązywania problemów w aplikacji Usługi Insights |
| `usage` | Galeria _Więcej_ w obszarze _Użycie_ w usłudze Application Insights |

### <a name="limitations"></a>Ograniczenia
Z technicznego powodu tego mechanizmu nie można użyć do tworzenia wystąpień skoroszytu w galerii _skoroszytów_ usługi Application Insights. Pracujemy nad zajęciem się tym ograniczeniem. W międzyczasie zaleca się użycie galerii Przewodnik po rozwiązywaniu `tsg`problemów (typ skoroszytu: ) w celu wdrożenia skoroszytów związanych z usługą Application Insights.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skoroszyty są używane do zasilania nowego [środowiska usługi Azure Monitor for Storage.](../insights/storage-insights-overview.md)

