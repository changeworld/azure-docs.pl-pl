---
title: Co się stało z moim projektem usługi w chmurze?
description: W tym artykule opisano, co dzieje się w projekcie usług w chmurze po nawiązaniu połączenia z kontem magazynu platformy Azure przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298767"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem usług w chmurze (usługa połączona z usługą Visual Studio Azure Storage)?
## <a name="references-added"></a>Dodano odwołania
Pakiet Azure Storage NuGet został dodany do projektu programu Visual Studio.  
Ten pakiet dodaje następujące odwołania .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Klient**
* **Microsoft.WindowsAzure.Konfiguracja**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Przestrzenny**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
Elementy zostały utworzone przy połączeniowym i kluczu wybranego konta magazynu. Wprowadzono modyfikacje następujących plików:

* **UsługaDefinicja.csdef**
* **UsługaConfiguration.Cloud.cscfg**
* **UsługaConfiguration.Local.cscfg**

