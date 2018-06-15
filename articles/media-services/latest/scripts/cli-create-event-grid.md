---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie subskrypcji usługi Azure Event Grid | Microsoft Docs
description: Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób tworzenia subskrypcji usługi Event Grid na poziomie konta na potrzeby zmian stanów zadań.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: b46cd5cf8f730a680078dde06b58eb31846e0a76
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161316"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Przykład interfejsu wiersza polecenia: tworzenie subskrypcji usługi Azure Event Grid 

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia subskrypcji usługi Event Grid na poziomie konta na potrzeby zmian stanów zadań.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
