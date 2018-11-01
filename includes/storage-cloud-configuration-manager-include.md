---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165591"
---
[Biblioteka programu Microsoft Azure Configuration Manager dla platformy .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji. [Klasa CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizuje ustawienia konfiguracji niezależnie od tego, czy aplikacja kliencka jest uruchomiona na komputerze, urządzeniu przenośnym, na maszynie wirtualnej platformy Azure, czy w usłudze w chmurze platformy Azure.

Aby zapewnić odwołanie do pakietu CloudConfigurationManager, dodaj następującą dyrektywę `using`:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Oto przykład, w którym przedstawiono, jak pobrać parametry połączenia z pliku konfiguracji:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Użycie programu Azure Configuration Manager jest opcjonalne. Możesz także użyć interfejsu API, np. klasy [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) programu .NET Framework.

