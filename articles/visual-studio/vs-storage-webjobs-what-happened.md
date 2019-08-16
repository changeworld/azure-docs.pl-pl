---
title: Co się stało z moim projektem WebJob (usługa połączona usługi Visual Studio Azure Storage)? | Microsoft Docs
description: Opisuje, co się stało w projekcie WebJob platformy Azure po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
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
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510475"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem WebJob (usługa połączona usługi Visual Studio Azure Storage)?
## <a name="references-added"></a>Dodane odwołania
Pakiet NuGet usługi Azure Storage został dodany do projektu programu Visual Studio lub zaktualizowany w nim.  
Ten pakiet dodaje następujące odwołania platformy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku App. config projektu wpisy **AzureWebJobsStorage** i **AzureWebJobsDashboard** zostały zaktualizowane o parametry i klucz połączenia wybranego konta magazynu.

Aby uzyskać więcej informacji, zobacz [Azure WebJobs zasoby dokumentacji](https://go.microsoft.com/fwlink/?linkid=390226).

