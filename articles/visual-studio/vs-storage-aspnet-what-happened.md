---
title: Co się stało z moim projektem ASP.NET?
description: W tym artykule opisano, co dzieje się po dodaniu usługi Azure Storage do projektu ASP.NET przy użyciu połączonych usług programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300044"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co się stało z moim projektem ASP.NET (usługa połączona z usługą Visual Studio Azure Storage)?
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
W pliku web.config projektu utworzono element z ciągiem połączenia i kluczem wybranego konta magazynu.

Aby uzyskać więcej informacji, zobacz [ASP.NET](https://www.asp.net).

