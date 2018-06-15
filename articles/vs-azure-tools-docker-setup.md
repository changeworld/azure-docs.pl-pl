---
title: Konfigurowanie hosta Docker z VirtualBox | Dokumentacja firmy Microsoft
description: Instrukcje krok po kroku, aby skonfigurować domyślne wystąpienie Docker przy użyciu rozwiązania Docker maszyny i VirtualBox
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 11e238fa901a164df1dfd896e38df828601e650b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190402"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurowanie hosta Docker z VirtualBox
## <a name="overview"></a>Przegląd
W tym artykule przedstawiono Konfigurowanie domyślnego wystąpienia Docker przy użyciu rozwiązania Docker maszyny i VirtualBox. Jeśli używasz [Docker dla systemu Windows w wersji beta](http://beta.docker.com/), ta konfiguracja nie jest konieczne.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące narzędzia muszą być zainstalowane.

* [Przybornik docker](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfigurowanie klienta platformy Docker przy użyciu programu Windows PowerShell
Aby skonfigurować klienta Docker, po prostu otwórz program Windows PowerShell i wykonaj następujące czynności:

1. Utwórz wystąpienie domyślne hostów docker.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Sprawdź, czy domyślne wystąpienie jest skonfigurowana i uruchomiona. (Powinna zostać wyświetlona wystąpienia o nazwie "default" systemem.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![dane wyjściowe ls maszyny docker][0]
3. Ustaw domyślny jako bieżącego hosta i skonfiguruj powłoki.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Wyświetlanie aktywnych kontenery Docker. Lista powinna być pusta.
   
    ```PowerShell
    docker ps
    ```
   
    ![dane wyjściowe ps docker][1]

> [!NOTE]
> Każdym uruchomieniu komputerze deweloperskim, będzie konieczne ponowne uruchomienie hosta lokalnego docker.
> Aby to zrobić, należy wydać następujące polecenie w wierszu polecenia: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
