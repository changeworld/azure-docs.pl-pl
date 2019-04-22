---
title: Domyślny rozmiar folderu TEMP jest zbyt mały dla roli | Dokumentacja firmy Microsoft
description: Rola usługi w chmurze ma ograniczoną ilość miejsca dla folderu TEMP. Ten artykuł zawiera wskazówki na temat sposobu uniknięcia wyczerpaniem przestrzeni.
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
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7862e4d5c4dd603dacf5784df6c4194392ebc351
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783689"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Domyślny rozmiar folderu TEMP jest zbyt mały dla roli sieć web/proces roboczy usług chmury
Domyślny katalog tymczasowy rola usługi w chmurze sieci web lub proces roboczy ma maksymalnym rozmiarze 100 MB, co może stać się pełną w pewnym momencie. W tym artykule opisano sposoby unikania ilość miejsca dla katalogu tymczasowego.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Dlaczego zabraknie miejsca
Standardowa Windows zmienne środowiskowe TEMP i TMP są dostępne do kodu, który jest uruchomiony w aplikacji. Zarówno TEMP i TMP wskaż jeden katalog, który ma maksymalnym rozmiarze 100 MB. Wszelkie dane, które są przechowywane w tym katalogu nie są utrwalane w całym cyklu życia usługi w chmurze; w przypadku odtwarzania wystąpień roli w usłudze w chmurze jest czyszczona katalogu.

## <a name="suggestion-to-fix-the-problem"></a>Sugestia, aby rozwiązać ten problem
Implementować jeden z następujących opcji:

* Konfigurowanie zasobów magazynu lokalnego i uzyskać do niego dostęp bezpośrednio zamiast przy użyciu TEMP i TMP. Aby uzyskać dostęp do zasobów magazynu lokalnego, od kodu, który jest uruchomiony w ramach aplikacji, należy wywołać [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metody.
* Konfigurowanie zasobów magazynu lokalnego, a punktu katalogi TEMP i TMP wskaż ścieżkę zasobu magazynu lokalnego. Ta modyfikacja powinny być wykonywane w ramach [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metody.

Poniższy przykład kodu pokazuje, jak zmodyfikować katalog docelowy dla TEMP i TMP z wewnątrz metody OnStart:

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
Przeczytaj wpis w blogu, który opisuje [sposobu zwiększenia rozmiaru platformy Azure w sieci Web roli folderu tymczasowego środowiska ASP.NET](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Wyświetl więcej [artykuły dotyczące rozwiązywania problemów](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy dotyczące ról usługi chmury przy użyciu danych diagnostycznych na komputerze modelu PaaS platformy Azure, Wyświetl [serię wpisów w blogu Kevina Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
