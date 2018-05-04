---
title: Co się stało z projektu programu ASP.NET 5 (Visual Studio połączenia usług) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się stanie po połączeniu z kontem magazynu platformy Azure w projekcie programu Visual Studio ASP.NET 5 za pomocą programu Visual Studio połączenia usługi
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f99ba4b6c954ae9faa87b9604c06e94c56e4f631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co się stało z projektu programu ASP.NET 5 (Visual Studio usługi Azure Storage połączenia usług)?
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

Ponadto pakiet NuGet **Microsoft.Framework.Configuration.Json** został dodany.

## <a name="connection-string-for-azure-storage-added"></a>Parametry połączenia dla usługi Azure Storage dodane
W pliku config.json projektu element został utworzony z parametrów połączenia i kluczem konta magazynu wybranego.

Aby uzyskać więcej informacji, zobacz [ASP.NET 5](http://www.asp.net/vnext).

