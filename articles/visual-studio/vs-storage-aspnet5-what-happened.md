---
title: Co się stało z moim projektem ASP.NET 5 (usługi połączone z programem Visual Studio)
description: W tym artykule opisano, co dzieje się po nawiązaniu połączenia z kontem magazynu platformy Azure w projekcie programu Visual Studio ASP.NET 5 przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300004"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co się stało z moim projektem ASP.NET 5 (usługi połączone z usługami usługi Visual Studio Azure Storage)?
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

Ponadto dodano pakiet NuGet **Microsoft.Framework.Configuration.Json.**

## <a name="connection-string-for-azure-storage-added"></a>Dodano parametry połączenia dla usługi Azure Storage
W pliku config.json projektu utworzono element z kontem wybranego konta magazynu i kluczem.

Aby uzyskać więcej informacji, zobacz [ASP.NET 5](https://www.asp.net/vnext).

