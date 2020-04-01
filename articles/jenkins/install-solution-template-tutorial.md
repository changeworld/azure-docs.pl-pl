---
title: Samouczek — tworzenie serwera usługi Jenkins na platformie Azure
description: W tym samouczku należy zainstalować usługę Jenkins na maszynie wirtualnej systemu Azure Linux z szablonu rozwiązania usługi Jenkins i utworzyć przykładową aplikację Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274900"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Samouczek: Tworzenie serwera usługi Jenkins na maszynie wirtualnej systemu Azure linux 

W tym samouczku pokazano, jak zainstalować [usługę Jenkins](https://jenkins.io) na maszynie wirtualnej systemu Linux Ubuntu za pomocą narzędzi i wtyczek skonfigurowanych do pracy z platformą Azure. Po zakończeniu uzyskasz działający na platformie Azure serwer Jenkins umożliwiający skompilowanie przykładowej aplikacji Java z usługi [GitHub](https://github.com).

> [!div class="checklist"]
> * Instalowanie i konfigurowanie serwera usługi Jenkins na platformie Azure
> * Uzyskiwanie dostępu do konsoli usługi Jenkins przy użyciu tunelu SSH
> * Tworzenie projektu Freestyle
> * Skompiluj kod i zapakuj przykładową aplikację

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]