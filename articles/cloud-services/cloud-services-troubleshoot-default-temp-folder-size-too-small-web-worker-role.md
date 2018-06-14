---
title: Domyślny rozmiar folderu tymczasowego jest za mały dla roli | Dokumentacja firmy Microsoft
description: Rola usługi chmury ma ograniczoną ilość miejsca do folderu tymczasowego. Ten artykuł zawiera wskazówki dotyczące zapobiegania wyczerpaniu miejsca.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 177438c4d6b416584f1797ec1ce744fc484ef688
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154532"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Domyślny rozmiar folderu tymczasowego jest za mały dla roli sieć web/proces roboczy usługi chmury
Domyślny katalog tymczasowy roli procesu roboczego lub sieci web usługi chmury ma maksymalny rozmiar 100 MB, co może stać się pełna w pewnym momencie. W tym artykule opisano sposób zapobiega wyczerpaniu się miejsce dla katalogu tymczasowego.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Dlaczego zabraknie miejsca?
Standardowa zmiennych środowiskowych systemu Windows TEMP i TMP są dostępne dla kodu uruchamianego w aplikacji. Zarówno TEMP i TMP wskaż jeden katalog, który zawiera maksymalny rozmiar 100 MB. Wszystkie dane są przechowywane w tym katalogu nie jest zachowywany po cyklem życia usługi w chmurze; Jeśli wystąpień roli w usłudze w chmurze są odzyskiwane, katalog jest czyszczona.

## <a name="suggestion-to-fix-the-problem"></a>Aby rozwiązać ten problem
Implementować jeden z następujących opcji:

* Konfigurowanie zasobów magazynu lokalnego, a dostęp do niego bezpośrednio zamiast TEMP lub TMP. Aby uzyskać dostęp do zasobów magazynu lokalnego z kodu uruchamianego w aplikacji, należy wywołać [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metody.
* Konfigurowanie zasobów magazynu lokalnego, a punkt katalogów TEMP i TMP wskaż ścieżkę do zasobu magazynu lokalnego. Ta modyfikacja powinny być wykonywane w ramach [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metody.

Następujący przykładowy kod przedstawia sposób modyfikowania katalogów docelowych TEMP i TMP z wewnątrz metody OnStart:

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

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj blog, który opisuje [jak zwiększyć rozmiar folderu Azure Web roli ASP.NET tymczasowego](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Wyświetl więcej [Rozwiązywanie problemów z artykułów](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy roli usługi w chmurze przy użyciu danych diagnostycznych Azure PaaS komputera, Wyświetl [serii blogu Kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
