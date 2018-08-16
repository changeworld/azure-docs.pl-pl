---
title: Przewodnik Szybki start platformy Azure — przetwarzanie strumieni zdarzeń przy użyciu programu PowerShell | Microsoft Docs
description: Ten przewodnik Szybki start opisuje sposób wysyłania i odbierania zdarzeń z usługi Azure Event Hubs przy użyciu programu PowerShell i przykładowej aplikacji .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: fd2b8b2e8bd075e029a07519ced186424798fd9c
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003614"
---
# <a name="quickstart-process-event-streams-using-powershell-and-net-standard"></a>Szybki start: przetwarzanie strumieni zdarzeń przy użyciu programów PowerShell i .NET Standard

Azure Event Hubs to wysoce skalowalna platforma do strumieniowego przesyłania danych i usługa pozyskiwania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Ten przewodnik Szybki start przedstawia tworzenie centrum zdarzeń za pomocą programu Azure PowerShell oraz wysyłanie i odbieranie danych z centrum zdarzeń za pomocą zestawu .NET Standard SDK.

Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto][].

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](http://www.visualstudio.com/vs) lub nowszy.
- [Zestaw .NET Standard SDK](https://www.microsoft.com/net/download/windows) w wersji 2.0 lub nowszej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli używasz programu PowerShell lokalnie, musisz uruchomić najnowszą wersję programu PowerShell, aby ukończyć ten przewodnik Szybki start. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Inicjowanie zasobów

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczna kolekcja zasobów platformy Azure i jest ona potrzebna, aby utworzyć centrum zdarzeń. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Wschodnie stany USA. Zastąp ciąg `myResourceGroup` nazwą grupy zasobów, której chcesz używać:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Po utworzeniu grupy zasobów utwórz przestrzeń nazw usługi Event Hubs w ramach tej grupy zasobów. Przestrzeń nazw usługi Event Hubs zapewnia unikatową, w pełni kwalifikowaną nazwę domeny, w której można utworzyć centrum zdarzeń. Zastąp ciąg `namespace_name` unikatową nazwą swojej przestrzeni nazw:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Teraz, gdy masz przestrzeń nazw usługi Event Hubs, utwórz centrum zdarzeń w ramach tej przestrzeni nazw:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń

Host procesora zdarzeń upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie punktami kontrolnymi i odbiornikami równoległymi. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Aby utworzyć konto magazynu i uzyskać jego klucze, uruchom następujące polecenia:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Do nawiązania połączenia z centrum zdarzeń i przetwarzania zdarzeń wymagane są parametry połączenia. Aby uzyskać parametry połączenia, uruchom:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

Możesz teraz rozpocząć przesyłanie strumieniowe do usługi Event Hubs. Przykłady można pobrać lub sklonować do usługi Git z [repozytorium usługi Event Hubs](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Pozyskiwanie zdarzeń

Aby rozpocząć przesyłanie strumieniowe zdarzeń, pobierz przykład [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) z usługi GitHub lub sklonuj [repozytorium GitHub usługi Event Hubs](https://github.com/Azure/azure-event-hubs) przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Przejdź do folderu \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender i załaduj plik SampleSender.sln do programu Visual Studio.

Następnie dodaj pakiet NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) do projektu.

W pliku Program.cs zastąp następujące symbole zastępcze swoją nazwą centrum zdarzeń i parametrami połączenia:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Następnie skompiluj i uruchom przykład. Wyświetlany będzie proces pozyskiwania zdarzeń do centrum zdarzeń:

![][3]

### <a name="receive-and-process-events"></a>Odbieranie i przetwarzanie zdarzeń

Teraz pobierz przykład odbiornika hosta procesora zdarzeń, który będzie odbierać wysłane komunikaty. Pobierz przykład [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) z usługi GitHub lub sklonuj [repozytorium GitHub usługi Event Hubs](https://github.com/Azure/azure-event-hubs) przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Przejdź do folderu \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver i załaduj plik rozwiązania SampleEphReceiver.sln do programu Visual Studio.

Następnie dodaj do projektu pakiety NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) i [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/).

W pliku Program.cs zastąp następujące stałe odpowiadającymi im wartościami:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Następnie skompiluj i uruchom przykład. Zobaczysz, jak przykładowa aplikacja będzie odbierać zdarzenia:

![][4]

W witrynie Azure Portal można w następujący sposób wyświetlić szybkość, z jaką zdarzenia są przetwarzane w danej przestrzeni nazw usługi Event Hubs:

![][5]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki start możesz usunąć swoją grupę zasobów oraz przestrzeń nazw, konto magazynu i centrum zdarzeń w ramach tej grupy. Zastąp ciąg `myResourceGroup` nazwą utworzonej grupy zasobów. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono przestrzeń nazw usługi Event Hubs oraz inne zasoby wymagane do wysyłania zdarzeń do centrum zdarzeń i odbierania stamtąd zdarzeń. Aby dowiedzieć się więcej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Wizualizowanie danych dotyczących anomalii w strumieniach danych usługi Event Hubs](event-hubs-tutorial-visualize-anomalies.md)

[utwórz bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
