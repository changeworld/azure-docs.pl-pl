---
title: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji w klastrze
description: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji aprowizowanych w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 60cb5aaf93521a3e2f7c922949d112ad96a00180
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805641"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Wyświetlanie listy aplikacji uruchomionych w klastrze usługi Service Fabric

Ten przykładowy skrypt umożliwia nawiązanie połączenia z klastrem usługi Service Fabric i zwrócenie listy wszystkich aprowizowanych aplikacji.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
