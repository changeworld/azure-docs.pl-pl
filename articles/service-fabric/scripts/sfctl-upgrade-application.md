---
title: Przykładowy skrypt interfejsu wiersza polecenia usługi Service Fabric — aktualizowanie aplikacji w klastrze
description: Przykładowy skrypt interfejsu wiersza polecenia usługi Service Fabric — aktualizowanie aplikacji za pomocą nowej wersji. W tym przykładzie wdrożona aplikacja zostanie również uaktualniona o nowe elementy.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: e925f4f7b4a3da2d298413feccc94ea742e47f6b
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034711"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Dodawanie certyfikatu aplikacji do klastra usługi Service Fabric

Ten przykładowy skrypt przekazuje nową wersję istniejącej aplikacji, a następnie uaktualnia wdrożoną aplikację o nowe elementy.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
