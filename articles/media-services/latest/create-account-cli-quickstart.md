---
title: Szybki start — tworzenie konta usługi Azure Media Services za pomocą interfejsu wiersza polecenia w wersji 2.0 | Microsoft Docs
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
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Szybki start: tworzenie konta usługi Azure Media Services

> [!NOTE]
> Najnowsza wersja usługi Azure Media Services (2018-03-30) jest w wersji zapoznawczej. Ta wersja jest również nazywana wersją 3. 

Niezależnie od tego, czy jesteś deweloperem, czy też twórcą zawartości multimedialnej, w celu przechowywania, szyfrowania, kodowania i przesyłania strumieniowego zawartości multimedialnej oraz zarządzania nią na platformie Azure musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać identyfikator zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Ten zasób konta magazynu musi znajdować się w tym samym regionie geograficznym co konto usługi Media Services.  

W tym przewodniku Szybki start zostały opisane kroki tworzenia nowego konta usługi Azure Media Services przy użyciu interfejsu wiersza polecenia w wersji 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się w witrynie [Azure Portal](http://portal.azure.com), a następnie uruchom usługę **CloudShell**, aby wykonywać polecenia interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

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

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów blob nie są dozwolone jako **główne**. Jeśli chcesz dowiedzieć się więcej o kontach magazynu, zobacz [Opcje konta usługi Azure Storage](../../storage/common/storage-account-options.md). 

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
