---
title: Interfejs wiersza polecenia usługi Azure Service Fabric (sfctl) — przykładowy skrypt usuwania
description: Usuwanie aplikacji z klastra Azure Service Fabric przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 83f2fc52debd24afd97a391466cb5a0b1a8cd93c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642720"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Usuwanie aplikacji z klastra Service Fabric

Ten przykładowy skrypt usuwa uruchomione wystąpienie aplikacji usługi Service Fabric, a następnie wyrejestrowuje typ i wersję aplikacji z klastra.  Usuwanie wystąpienia aplikacji spowoduje usunięcie wszystkich uruchomionych wystąpień usługi skojarzonych z aplikacją. Następnie pliki aplikacji są usuwane z magazynu obrazów. 

Jeśli wystąpi taka potrzeba, zainstaluj [interfejs wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
