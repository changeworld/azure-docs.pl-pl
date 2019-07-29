---
title: Interfejs wiersza polecenia usługi Azure Service Fabric (sfctl) — przykładowy skrypt usuwania
description: Usuwanie aplikacji z klastra Azure Service Fabric przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7c3739a2e5e15e77cb88ffb9d3effe9abdd0b848
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592231"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Usuwanie aplikacji z klastra Service Fabric

Ten przykładowy skrypt usuwa uruchomione wystąpienie aplikacji usługi Service Fabric, a następnie wyrejestrowuje typ i wersję aplikacji z klastra.  Usuwanie wystąpienia aplikacji spowoduje usunięcie wszystkich uruchomionych wystąpień usługi skojarzonych z aplikacją. Następnie pliki aplikacji są usuwane z magazynu obrazów. 

Jeśli wystąpi taka potrzeba, zainstaluj [interfejs wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
