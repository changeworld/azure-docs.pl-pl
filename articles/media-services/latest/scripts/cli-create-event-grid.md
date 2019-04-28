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
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762032"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Przykład użycia interfejsu wiersza polecenia: Tworzenie subskrypcji usługi Azure Event Grid 

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia subskrypcji usługi Event Grid na poziomie konta na potrzeby zmian stanów zadań.

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../cli-samples.md).
