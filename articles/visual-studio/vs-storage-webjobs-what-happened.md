---
title: Co się stało z moim projektem WebJob (Visual Studio Azure Storage)?
description: W tym artykule opisano, co się stało w projekcie usługi Azure WebJob po nawiązaniu połączenia z kontem magazynu przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298732"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem WebJob (usługa połączona z usługą Visual Studio Azure Storage)?
## <a name="references-added"></a>Dodano referencje
Pakiet Azure Storage NuGet został dodany lub zaktualizowany w projekcie programu Visual Studio.  
Ten pakiet dodaje następujące odwołania .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Klient**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Przestrzenny**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku App.config **projektu, AzureWebJobsStorage** i **AzureWebJobsDashboard** wpisy zostały zaktualizowane o ciąg połączenia i klucz wybranego konta magazynu.

Aby uzyskać więcej informacji, zobacz [zasoby dokumentacji usługi Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

