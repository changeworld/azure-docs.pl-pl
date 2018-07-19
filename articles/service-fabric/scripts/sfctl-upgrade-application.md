---
title: Przykładowy skrypt interfejsu wiersza polecenia usługi Service Fabric — aktualizowanie aplikacji w klastrze
description: Przykładowy skrypt interfejsu wiersza polecenia usługi Service Fabric — aktualizowanie aplikacji za pomocą nowej wersji. W tym przykładzie wdrożona aplikacja zostanie również uaktualniona o nowe elementy.
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
ms.date: 12/06/2017
ms.author: twhitney
ms.custom: ''
ms.openlocfilehash: bd0941cc6e67a759dd5fcb2e8912f8fcd6e9b18c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071168"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Dodawanie certyfikatu aplikacji do klastra usługi Service Fabric

Ten przykładowy skrypt przekazuje nową wersję istniejącej aplikacji, a następnie uaktualnia wdrożoną aplikację o nowe elementy.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
