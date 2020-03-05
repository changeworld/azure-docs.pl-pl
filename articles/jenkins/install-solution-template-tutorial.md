---
title: Samouczek — Tworzenie serwera Jenkins na platformie Azure
description: W tym samouczku zainstalujesz Jenkins na maszynie wirtualnej z systemem Linux platformy Azure z szablonu rozwiązania Jenkins i utworzysz przykładową aplikację w języku Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274900"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Samouczek: Tworzenie serwera Jenkins na maszynie wirtualnej z systemem Linux na platformie Azure 

W tym samouczku pokazano, jak zainstalować [Jenkins](https://jenkins.io) na maszynie wirtualnej Ubuntu Linux za pomocą narzędzi i wtyczek skonfigurowanych do pracy z platformą Azure. Po zakończeniu uzyskasz działający na platformie Azure serwer Jenkins umożliwiający skompilowanie przykładowej aplikacji Java z usługi [GitHub](https://github.com).

> [!div class="checklist"]
> * Instalowanie i Konfigurowanie serwera Jenkins na platformie Azure
> * Dostęp do konsoli usługi Jenkins przy użyciu tunelu SSH
> * Tworzenie projektu Freestyle
> * Kompilowanie kodu i pakowanie przykładowej aplikacji

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]