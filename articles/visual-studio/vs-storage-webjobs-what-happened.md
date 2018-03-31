---
title: Co się stało z projektu zadania WebJob (usługi magazynu Azure w usłudze Visual Studio połączony)? | Microsoft Docs
description: W tym artykule opisano, co się stało z projektu zadania WebJob Azure po nawiązywania połączenia z kontem magazynu za pomocą programu Visual Studio połączenia usługi
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b7bebd7801e102b9a3173841ce2289ac575cd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co się stało z projektu zadania WebJob (usługi magazynu Azure w usłudze Visual Studio połączony)?
## <a name="references-added"></a>Odwołania dodane
Pakiet NuGet magazynu Azure zostały dodane do lub zaktualizowane w projekcie programu Visual Studio.  
Ten pakiet zawiera następujące odwołania .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Parametry połączenia dla usługi Azure Storage dodane
W pliku App.config projektu **AzureWebJobsStorage** i **AzureWebJobsDashboard** wpisy zostały zaktualizowane z wybranego konta magazynu przez ciąg połączenia i kluczem.

Aby uzyskać więcej informacji, zobacz [zasoby dokumentacji zadań Webjob Azure](http://go.microsoft.com/fwlink/?linkid=390226).

