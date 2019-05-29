---
title: 'Samouczek: czyszczenie klastra autonomicznego usługi Service Fabric — Azure Service Fabric | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak wyczyścić klaster autonomiczny
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 67334a0e8ae3e6dcca86830cd088e6e446331aee
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306714"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: Czyszczenie klastra autonomicznego

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. Ta seria samouczków przedstawia proces tworzenia klastra autonomicznego hostowanego w usłudze AWS i instalowania w nim aplikacji.

Niniejszy samouczek jest czwartą częścią serii. W tej części samouczka dowiesz się, jak wyczyścić zasoby usługi AWS utworzone w celu hostowania klastra usługi Service Fabric.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie klastra usługi Service Fabric
> * Czyszczenie zasobów usługi AWS

## <a name="clean-up-service-fabric-cluster"></a>Czyszczenie klastra usługi Service Fabric

* Połącz się za pomocą protokołu RDP z wystąpieniem usługi EC2 użytym do zainstalowania usługi Service Fabric.
* Otwórz program PowerShell.
* Zmień katalog na wyodrębniony folder z drugiej części samouczka.
* Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* Po wyświetleniu monitu naciśnij `Y`. Jeśli działanie się powiedzie, zobaczysz następujące dane wyjściowe, z Twoimi adresami IP:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Czyszczenie zasobów usługi AWS

* Zaloguj się do konta usługi AWS
* Przejdź do konsoli EC2.
* Wybierz trzy węzły utworzone w pierwszej części samouczka.
* Kliknij kolejno pozycje **Actions** (Działania)  >  **Instance State** (Stan wystąpienia)  >  **Terminate** (Zakończ).

## <a name="next-steps"></a>Kolejne kroki

W czwartej części samouczka przedstawiono proces czyszczenia zasobów utworzonych w poprzednich częściach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)