---
title: 'Samouczek: czyszczenie klastra autonomicznego usługi Service Fabric — Azure Service Fabric | Microsoft Docs'
description: W tym samouczku dowiesz się, jak oczyścić klaster autonomiczny
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
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385160"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: Czyszczenie klastra autonomicznego

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz klaster autonomiczny hostowany na AWS lub na platformie Azure i zainstaluje do niego aplikację.

Niniejszy samouczek jest czwartą częścią serii. W tej części samouczka pokazano, jak wyczyścić AWS lub zasoby platformy Azure, które zostały utworzone w celu hostowania klastra Service Fabric.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie klastra usługi Service Fabric
> * Czyszczenie zasobów AWS lub platformy Azure

## <a name="clean-up-service-fabric-cluster"></a>Czyszczenie klastra usługi Service Fabric

1. Połącz protokół RDP z maszyną wirtualną, która została użyta do zainstalowania Service Fabric.
2. Otwórz program PowerShell.
3. Zmień katalog na wyodrębniony folder z drugiej części samouczka.
4. Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Wprowadź `Y` po wyświetleniu monitu, jeśli dane wyjściowe będą wyglądały jak poniżej, z własnymi adresami IP zastępują:

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

1. Zaloguj się do konta AWS.
2. Przejdź do konsoli EC2.
3. Wybierz trzy węzły utworzone w pierwszej części samouczka.
4. Kliknij pozycję **Akcje** > **stan** > wystąpienia**Przerwij**.

## <a name="clean-up-azure-resources"></a>Czyszczenie zasobów platformy Azure

1. Zaloguj się do Portalu Azure.
2. Przejdź do sekcji **Virtual Machines** .
3. Zaznacz pola wyboru dla trzech węzłów utworzonych w pierwszej części samouczka.
4. Kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W czwartej części samouczka przedstawiono proces czyszczenia zasobów utworzonych w poprzednich częściach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)
