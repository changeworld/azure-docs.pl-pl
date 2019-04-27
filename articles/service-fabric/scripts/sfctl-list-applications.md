---
title: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji w klastrze
description: Przykładowy skrypt wiersza polecenia usługi Service Fabric — wyświetlanie listy aplikacji aprowizowanych w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/13/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: ''
ms.openlocfilehash: 8fd83190f3cf92ef0f88ff0fb2a807e03199a5c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621934"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Wyświetlanie listy aplikacji uruchomionych w klastrze usługi Service Fabric

Ten przykładowy skrypt umożliwia nawiązanie połączenia z klastrem usługi Service Fabric i zwrócenie listy wszystkich aprowizowanych aplikacji.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Retrieve all applications from the cluster
sfctl application list
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
<!--Update_Description: update meta properties -->