---
title: Co się stało z projektu programu ASP.NET? | Microsoft Docs
description: W tym artykule opisano, co się stanie po Dodawanie magazynu Azure do projektu ASP.NET, za pomocą programu Visual Studio połączenia usługi
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 84b608d335dc26198c3af6f89407758fd1d020dc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791777"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co się stało z projektu programu ASP.NET (usługi magazynu Azure w usłudze Visual Studio połączony)?
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
W pliku web.config projektu element został utworzony z wybranego konta magazynu przez ciąg połączenia i kluczem.

Aby uzyskać więcej informacji, zobacz [ASP.NET](http://www.asp.net).

