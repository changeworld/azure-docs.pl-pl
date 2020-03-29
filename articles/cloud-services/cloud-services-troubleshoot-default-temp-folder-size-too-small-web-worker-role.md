---
title: Domyślny rozmiar folderu TEMP jest za mały dla roli | Dokumenty firmy Microsoft
description: Rola usługi w chmurze ma ograniczoną ilość miejsca dla folderu TEMP. W tym artykule przedstawiono kilka sugestii, jak uniknąć wyczerpania miejsca.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154998"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Domyślny rozmiar folderu TEMP jest za mały w roli usługi w chmurze/procesu roboczego
Domyślny katalog tymczasowy pracownika usługi w chmurze lub roli sieci web ma maksymalny rozmiar 100 MB, który może stać się pełny w pewnym momencie. W tym artykule opisano, jak uniknąć wyczerpania miejsca dla katalogu tymczasowego.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Dlaczego zabrakło mi miejsca?
Standardowe zmienne środowiskowe systemu Windows TEMP i TMP są dostępne dla kodu, który jest uruchomiony w aplikacji. Zarówno TEMP, jak i TMP wskazują pojedynczy katalog o maksymalnym rozmiarze 100 MB. Wszelkie dane, które są przechowywane w tym katalogu nie jest zachowywany w całym cyklu życia usługi w chmurze; Jeśli wystąpienia roli w usłudze w chmurze są poddane recyklingowi, katalog jest czyszczony.

## <a name="suggestion-to-fix-the-problem"></a>Sugestia rozwiązania problemu
Zaimplementuj jedną z następujących alternatyw:

* Skonfiguruj zasób magazynu lokalnego i uzyskaj do niego dostęp bezpośrednio zamiastnika temp lub TMP. Aby uzyskać dostęp do zasobu magazynu lokalnego z kodu, który jest uruchomiony w aplikacji, wywołać [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metody.
* Skonfiguruj lokalny zasób magazynu i skieruj katalogi TEMP i TMP, aby wskazywały ścieżkę zasobu magazynu lokalnego. Ta modyfikacja powinna być wykonana w ramach [metody RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

Poniższy przykład kodu pokazuje, jak zmodyfikować katalogi docelowe temp i TMP z poziomu OnStart metody:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Przeczytaj blog, w tym [jak zwiększyć rozmiar folderu tymczasowego roli sieci Web platformy Azure ASP.NET](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Wyświetl więcej [artykułów dotyczących rozwiązywania problemów](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) z usługami w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy z rolami usługi w chmurze przy użyciu danych diagnostycznych komputera usługi Azure PaaS, zobacz [serię blogów Kevina Williamsona.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
