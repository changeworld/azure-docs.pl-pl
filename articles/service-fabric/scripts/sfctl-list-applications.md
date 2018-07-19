---
title: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji w klastrze
description: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji aprowizowanych w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: twhitney
ms.custom: ''
ms.openlocfilehash: bea88460ecaa093a0719d627e3608fd1d530727b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070202"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Wyświetlanie listy aplikacji uruchomionych w klastrze usługi Service Fabric

Ten przykładowy skrypt umożliwia nawiązanie połączenia z klastrem usługi Service Fabric i zwrócenie listy wszystkich aprowizowanych aplikacji.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
