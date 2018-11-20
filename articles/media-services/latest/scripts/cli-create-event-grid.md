---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie subskrypcji usługi Azure Event Grid | Microsoft Docs
description: Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób tworzenia subskrypcji usługi Event Grid na poziomie konta na potrzeby zmian stanów zadań.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: a3cff649001adf569f1454d16a2a97b32972ef00
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612627"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Przykład interfejsu wiersza polecenia: tworzenie subskrypcji usługi Azure Event Grid 

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia subskrypcji usługi Event Grid na poziomie konta na potrzeby zmian stanów zadań.

## <a name="prerequisites"></a>Wymagania wstępne 

- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](../create-account-cli-how-to.md).

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
