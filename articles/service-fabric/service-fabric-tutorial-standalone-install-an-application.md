---
title: 'Samouczek: instalowanie aplikacji w autonomicznym klastrze usługi Service Fabric — Azure Service Fabric | Microsoft Docs'
description: W tym samouczku dowiesz się, jak zainstalować aplikację do klastra usługi Service Fabric autonomicznego.
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
ms.openlocfilehash: 58daa53dba8f18c3f73253008fe687ba8b0d0839
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274077"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Samouczek: Wdrażanie aplikacji na klastra autonomicznego usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków Tworzenie klastra autonomicznego hostowanych na platformie AWS i wdrożyć aplikację do niego.

Ten samouczek jest trzecią częścią serii.  Klastry autonomiczne usługi Service Fabric oferuje opcję, aby wybrać własne środowisko, a następnie utworzyć klaster jako część naszego podejścia "Dowolny system operacyjny, każdej chmury" przy użyciu usługi Service Fabric. W tym samouczku pokazano, jak utworzyć infrastrukturę usług AWS niezbędną do obsługi takiego klastra autonomicznego.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* [Instalowanie programu Visual Studio 2019](https://www.visualstudio.com/) i zainstaluj **programowanie na platformie Azure** i **ASP.NET i tworzenie aplikacji internetowych** obciążeń.
* [Zainstaluj zestaw SDK usługi Service Fabric.](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Po pobraniu aplikacji można ją wdrożyć w klastrze bezpośrednio z programu Visual Studio.

1. Otwórz program Visual Studio.

2. Wybierz pozycję **Plik** > **Otwórz**.

3. Przejdź do folderu, do którego sklonowano repozytorium git, i wybierz plik Voting.sln.

4. Kliknij prawym przyciskiem myszy projekt aplikacji `Voting` w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

5. Wybierz listę rozwijaną **Punkt końcowy połączenia** i wprowadź publiczną nazwę DNS jednego z węzłów w klastrze.  Na przykład `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Należy pamiętać, że na platformie Azure, w pełni kwalifikowaną nazwę domeny (FQDN) nie znajduje się automatycznie, ale może łatwo [można ustawić na stronie Omówienie maszyny Wirtualnej.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia; ta aplikacja jest wdrożona na porcie 8080 — na przykład ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Kolejne kroki

W części trzeciej tej serii przedstawiono sposób wdrażania aplikacji w klastrze:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

Przejdź do części czwartej serii, aby wyczyścić klaster.

> [!div class="nextstepaction"]
> [Czyszczenie zasobów](service-fabric-tutorial-standalone-clean-up.md)