---
title: 'Samouczek: czyszczenie klastra autonomicznego usługi Service Fabric — Azure Service Fabric | Microsoft Docs'
description: W tym samouczku dowiesz się, jak wyczyścić klastra autonomicznego
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
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274092"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Samouczek: Czyszczenie klastra autonomicznego

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków Tworzenie klastra autonomicznego w serwisie AWS lub na platformie Azure i zainstalować aplikację do niego.

Niniejszy samouczek jest czwartą częścią serii. Ta część samouczka dowiesz się, jak wyczyścić AWS lub zasobów platformy Azure, które zostały utworzone do obsługi klastra usługi Service Fabric.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie klastra usługi Service Fabric
> * Czyszczenie usługi AWS lub zasobów platformy Azure

## <a name="clean-up-service-fabric-cluster"></a>Czyszczenie klastra usługi Service Fabric

1. Połącz protokół RDP maszyny Wirtualnej, która umożliwia zainstalowanej usługi Service Fabric.
2. Otwórz program PowerShell.
3. Zmień katalog na wyodrębniony folder z drugiej części samouczka.
4. Uruchom następujące polecenie, aby usunąć klaster usługi Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Wprowadź `Y` po wyświetleniu monitu, jeśli pomyślnie danych wyjściowych będzie wyglądać podobnie do poniższego, przy użyciu własnych adresów IP podstawione w:

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

1. Zaloguj się do konta usługi AWS.
2. Przejdź do konsoli EC2.
3. Wybierz trzy węzły utworzone w pierwszej części samouczka.
4. Kliknij pozycję **akcje** > **wystąpienie przechodzi w stan** > **zakończyć**.

## <a name="clean-up-azure-resources"></a>Oczyszczanie zasobów platformy Azure

1. Zaloguj się do Portalu Azure.
2. Przejdź do **maszyn wirtualnych** sekcji.
3. Zaznacz pola wyboru, aby uzyskać trzy węzły, które zostały utworzone w części pierwszej części samouczka.
4. Kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W czwartej części samouczka przedstawiono proces czyszczenia zasobów utworzonych w poprzednich częściach.

> [!div class="checklist"]
> * Czyszczenie zasobów

> [!div class="nextstepaction"]
> [Powrót do początku](service-fabric-tutorial-standalone-create-infrastructure.md)
