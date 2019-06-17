---
title: Co się stało z moim projektem zadania WebJob (usługa połączona programu Visual Studio usługi Azure Storage)? | Microsoft Docs
description: W tym artykule opisano, co wydarzyło się w projekcie zadań WebJob platformy Azure po łączenie z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: fa152d8b88254a35d00b91537bf1001ea1130e57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60722618"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem zadania WebJob (usługa połączona programu Visual Studio usługi Azure Storage)?
## <a name="references-added"></a>Odwołania dodane
Pakiet NuGet usługi Azure Storage zostały dodane do lub zaktualizowane w projekcie programu Visual Studio.  
Ten pakiet dodaje następujące odwołania do platformy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Parametry połączenia dla usługi Azure Storage dodane
W pliku App.config projektu **AzureWebJobsStorage** i **AzureWebJobsDashboard** zaktualizowanych pozycji konto magazynu wybrane parametry połączenia i kluczem.

Aby uzyskać więcej informacji, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

