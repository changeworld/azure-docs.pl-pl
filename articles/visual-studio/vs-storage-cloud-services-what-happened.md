---
title: Co się stało mój projekt usługi w chmurze? | Microsoft Docs
description: W tym artykule opisano, co się stanie w projekcie usługi w chmurze po łączenie się z kontem magazynu platformy Azure przy użyciu programu Visual Studio połączenia usługi
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8bbb87c313957ee697866a82ddec4c61d1420939
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co się stało z projektu usługi w chmurze (usługi magazynu Azure w usłudze Visual Studio połączony)?
## <a name="references-added"></a>Odwołania dodane
Pakiet NuGet magazynu Azure został dodany do projektu programu Visual Studio.  
Ten pakiet zawiera następujące odwołania .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Parametry połączenia dla usługi Azure Storage dodane
Elementy zostały utworzone za pomocą wybranego konta magazynu przez ciąg połączenia i klucza. Następujące pliki zostały wprowadzone zmiany:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

