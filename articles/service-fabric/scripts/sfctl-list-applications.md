---
title: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji w klastrze
description: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji aprowizowanych w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: be7464cab4f9a1014fa129314c332bc41b2f499c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212946"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Wyświetlanie listy aplikacji uruchomionych w klastrze usługi Service Fabric

Ten przykładowy skrypt umożliwia nawiązanie połączenia z klastrem usługi Service Fabric i zwrócenie listy wszystkich aprowizowanych aplikacji.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
