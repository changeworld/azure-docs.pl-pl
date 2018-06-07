---
title: Wdrażanie kontenera ASP.NET Docker do rejestru kontenera platformy Azure (ACR) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Visual Studio Tools for Docker do wdrażania aplikacji sieci web platformy ASP.NET Core rejestru kontenera
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 4442c1d763f4ed21a5efeedbe957727254e2a0b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658475"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Wdrażanie kontenera ASP.NET rejestru kontenera za pomocą programu Visual Studio
## <a name="overview"></a>Przegląd
Docker jest aparatem lekkie kontenera, podobne w pewnym sensie do maszyny wirtualnej, która służy do obsługi aplikacji i usług.
Ten samouczek przedstawia przy użyciu programu Visual Studio do publikowania aplikacji konteneryzowanych [rejestru kontenera Azure](https://azure.microsoft.com/en-us/services/container-registry).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs).

## <a name="prerequisites"></a>Wymagania wstępne
W celu ukończenia tego samouczka:

* Zainstaluj najnowszą wersję pakietu [programu Visual Studio 2017](https://azure.microsoft.com/en-us/downloads/) z obciążeniem "Programowanie ASP.NET i sieć web"
* Zainstaluj [Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Tworzenie aplikacji internetowej ASP.NET Core
Poniższe kroki pomocne przy tworzeniu Podstawowa aplikacja platformy ASP.NET Core, który będzie używany w tym samouczku.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publikowanie z kontenera do rejestru kontenera platformy Azure
1. Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz polecenie **publikowania**.
2. W oknie dialogowym Publikowanie docelowy, wybierz **rejestru kontenera** kartę.
3. Wybierz **nowego rejestru kontenera Azure** i kliknij przycisk **publikowania**.
4. Wypełnij odpowiednie wartości w **tworzenie nowych rejestru kontenera Azure**.

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prefiks DNS** | Nazwa unikatowa w skali globalnej | Nazwa, która identyfikuje kontener rejestru. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której chcesz utworzyć rejestr kontenera. Wybierz **nowy** Aby utworzyć nową grupę zasobów.|
    | **[JEDNOSTKA SKU](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus)** | Standardowa (Standard) | Warstwy usług rejestru kontenera  |
    | **Lokalizacja w rejestrze** | Bliską lokalizację | Wybierz lokalizację, w [region](https://azure.microsoft.com/regions/) pobliżu lub w jego pobliżu innych usług używających rejestru kontenera. |
    ![Okno dialogowe rejestru kontenera Azure tworzenie programu Visual Studio][0]
5. Kliknij przycisk **Utwórz**

Możesz teraz pociągnąć kontenera z rejestru dla każdego hosta może uruchamiać obrazy usługi Docker, na przykład [wystąpień kontenera Azure](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
