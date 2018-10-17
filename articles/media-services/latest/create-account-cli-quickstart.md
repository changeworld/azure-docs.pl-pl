---
title: Szybki start — tworzenie konta usługi Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: abed9fd8d466b582b534b365f4be4257f4986435
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736006"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Szybki start: tworzenie konta usługi Azure Media Services

> [!NOTE]
> Najnowsza wersja usługi Azure Media Services (2018-03-30) jest w wersji zapoznawczej. Ta wersja jest również nazywana wersją 3. 

Niezależnie od tego, czy jesteś deweloperem, czy też twórcą zawartości multimedialnej, w celu przechowywania, szyfrowania, kodowania i przesyłania strumieniowego zawartości multimedialnej oraz zarządzania nią na platformie Azure musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać identyfikator zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Ten zasób konta magazynu musi znajdować się w tym samym regionie geograficznym co konto usługi Media Services.  

W tym przewodniku Szybki start zostały opisane kroki tworzenia nowego konta usługi Azure Media Services przy użyciu interfejsu wiersza polecenia platformy Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się w witrynie [Azure Portal](http://portal.azure.com), a następnie uruchom usługę **CloudShell**, aby wykonywać polecenia interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

Poniższe polecenie tworzy grupę zasobów, w której chcesz mieć konto usługi Storage i Media Services. Zamień symbol zastępczy *myresourcegroup* na nazwę, której chcesz użyć dla swojej grupy zasobów.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Podczas tworzenia konta usługi Media Services musisz podać identyfikator zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **ogólnego przeznaczenia, wersja 2** i **ogólnego przeznaczenia, wersja 1**. Konta usługi Blob Storage nie są dozwolone jako **główne**. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview (Omówienie konta usługi Azure Storage)](../../storage/common/storage-account-overview.md). 

Poniższe polecenie tworzy konto usługi Storage, które ma być skojarzone z kontem usługi Media Services (głównym). W poniższym skrypcie zamień symbol zastępczy *storageaccountforams*. Wartość „nazwa_konta” musi mieć długość mniejszą niż 24.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Poniżej możesz znaleźć polecenia interfejsu wiersza polecenia platformy Azure, które tworzą nowe konto usługi Media Services. Wystarczy zastąpić następujące wyróżnione wartości:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto usługi Media Services utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów.

W usłudze **CloudShell** uruchom następujące polecenie:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
