---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67183789"
---
Aby ukończyć ten samouczek, musisz spełniać następujące wymagania:

**Interfejs wiersza polecenia platformy Azure**: Na komputerze lokalnym musi być zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

**Docker**: W tym samouczku założono podstawową wiedzę na temat najważniejszych pojęć platformy Docker, takich jak kontenery, obrazy kontenerów i podstawowe `docker` polecenia. Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie][docker-get-started].

**Aparat platformy Docker**: Do ukończenia tego samouczka potrzebny jest aparat platformy Docker zainstalowany lokalnie. Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS][docker-mac], [Windows][docker-windows]i [Linux][docker-linux].

> [!IMPORTANT]
> Ponieważ usługa Azure Cloud Shell nie zawiera demona platformy Docker, w celu ukończenia tego samouczka *musisz* zainstalować na swoim *komputerze lokalnym* wiersz polecenia platformy Azure i aparat platformy Docker. W tym samouczku nie możesz korzystać z usługi Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
