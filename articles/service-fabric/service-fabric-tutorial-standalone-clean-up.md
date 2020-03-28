---
title: Oczyszczanie autonomicznego klastra
description: W tym samouczku dowiesz się, jak oczyścić zasoby usługi AWS lub platformy Azure w autonomicznym klastrze sieci szkieletowej usług.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639024"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: czyszczenie klastra autonomicznego

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz autonomiczny klaster hostowany na platformie AWS lub Azure i zainstaluj w nim aplikację.

Niniejszy samouczek jest czwartą częścią serii. W tej części samouczka pokazano, jak oczyścić zasoby usługi AWS lub platformy Azure utworzone w celu hostowania klastra sieci szkieletowej usług.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie klastra usługi Service Fabric
> * Oczyszczanie zasobów usługi AWS lub platformy Azure

## <a name="clean-up-service-fabric-cluster"></a>Czyszczenie klastra usługi Service Fabric

1. RDP do maszyny Wirtualnej, która została zainstalowana w sieci szkieletowej usług.
2. Otwórz program PowerShell.
3. Zmień katalog na wyodrębniony folder z drugiej części samouczka.
4. Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Wprowadź `Y` po wyświetleniu monitu, jeśli to się powiedzie, dane wyjściowe będą wyglądać następująco, z własnymi adresami IP podstawionymi w:

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

1. Zaloguj się na swoje konto AWS.
2. Przejdź do konsoli EC2.
3. Wybierz trzy węzły utworzone w pierwszej części samouczka.
4. Kliknij stan**wystąpienia** >  **akcji** > **Zakończ**.

## <a name="clean-up-azure-resources"></a>Oczyszczanie zasobów platformy Azure

1. Zaloguj się do Portalu Azure.
2. Przejdź do sekcji **Maszyny wirtualne.**
3. Zaznacz pola wyboru dla trzech węzłów utworzonych w części trzeciej samouczka.
4. Kliknij **przycisk Usuń**.

## <a name="next-steps"></a>Następne kroki

W czwartej części samouczka przedstawiono proces czyszczenia zasobów utworzonych w poprzednich częściach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)
