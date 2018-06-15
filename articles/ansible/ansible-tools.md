---
title: Narzędzia dla platformy Azure przy użyciu Ansible
description: Zainstalować i używać poszczególnych narzędzi dla Ansible z platformy Azure
ms.service: ansible
keywords: ansible, azure, metodyki devops, narzędzia kodzie vs, kod programu visual studio, rozszerzenia
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212050"
---
# <a name="tools-for-using-ansible-with-azure"></a>Narzędzia dla platformy Azure przy użyciu Ansible

Następujące narzędzia sprawiają, że praca z Ansible i Azure łatwiejsze i bardziej wydajnych.

## <a name="visual-studio-code-extension-for-ansible"></a>Rozszerzenia programu Visual Studio Code Ansible

[Rozszerzenia Visual Studio Code Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) zapewnia kilka funkcji przy użyciu Ansible z kodem Visual Studio:

- Wpisz automatycznego uzupełniania dyrektywy Ansible, moduły i wtyczki z dokumentacji Ansible jako użytkownik.
- Kod wstawki wyświetlania po kliknięciu przycisku &lt;Ctrl >&lt;miejsca > podczas tworzenia Twojej playbooks Ansible.
- Wyróżnianie składni Wyświetla kod podręcznikowym Ansible w różnych kolorów i czcionek, zgodnie z składni yaml programu.
- Ansible playbooks można uruchomić z okna programu Visual Studio Code Terminal.
    - (Tylko system Windows) Ansible można uruchomić z kontenera Docker.
    - (Linux i macOS) Ansible można uruchomić z kontenera Docker lub lokalnej instalacji Ansible. 
- Ansible playbooks można uruchomić z powłoki chmury Azure.