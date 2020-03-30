---
title: Konfigurowanie laboratorium skryptów powłoki systemu Linux za pomocą usług Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium do nauczania skryptów powłoki na Linuksie.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443554"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Skonfiguruj laboratorium do nauczania skryptów powłoki na Linuksie
W tym artykule pokazano, jak skonfigurować laboratorium do nauczania skryptów powłoki w systemie Linux. Skrypty są użyteczną częścią administracji systemowej, która pozwala administratorom uniknąć powtarzających się zadań. W tym przykładowym scenariuszu klasa obejmuje tradycyjnych skryptów bash i skryptów rozszerzonych. Ulepszone skrypty to skrypty łączące polecenia bash i Ruby. Takie podejście pozwala Ruby przekazać dane wokół i bash polecenia do interakcji z powłoki. 

Uczniowie biorący te klasy skryptów otrzymują maszynę wirtualną Linuksa, aby nauczyć się podstaw linuksa, a także zapoznać się ze skryptami powłoki bash. Maszyna wirtualna Systemu Linux jest wyposażona w dostęp do pulpitu zdalnego z zainstalowanym [edytorami](https://help.gnome.org/users/gedit/stable/) tekstu gedit i [Visual Studio Code.](https://code.visualstudio.com/)

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services lub użyć istniejącego konta laboratorium. Zobacz następujący samouczek tworzenia nowego konta laboratorium: [Samouczek do konfigurowania konta laboratorium](tutorial-setup-lab-account.md).

Po utworzeniu konta laboratorium włącz następujące ustawienia na koncie laboratorium: 

| Ustawienie konta laboratorium | Instrukcje |
| ----------- | ------------ |  
| Obrazy z Marketplace | Włącz obraz [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) do użytku na koncie laboratoryjnym. Aby uzyskać więcej informacji, zobacz [Określanie obrazów marketplace dostępnych dla twórców laboratoriów.](specify-marketplace-images.md) | 

Wykonaj [ten samouczek,](tutorial-setup-classroom-lab.md) aby utworzyć nowe laboratorium i zastosować następujące ustawienia:

| Ustawienia laboratorium | Wartość/instrukcja | 
| ------------ | ------------------ |
| Rozmiar maszyny wirtualnej | Small  |
| Obraz maszyny Wirtualnej | [Serwer Ubuntu 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Włączanie połączenia pulpitu zdalnego | Włącz. <p>Włączenie tego ustawienia umożliwi nauczycielom i uczniom łączenie się z maszynami wirtualnymi za pomocą pulpitu zdalnego (RDP). Aby uzyskać więcej informacji, zobacz [Włączanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux w laboratorium w usłudze Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Instalowanie pulpitu i xrdp
Obraz [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) nie ma domyślnie zainstalowanego serwera pulpitu zdalnego. Postępuj zgodnie z instrukcjami w [instalacji i konfigurowania pulpitu zdalnego, aby połączyć się z maszyną wirtualną z systemem Linux w artykule platformy Azure,](../../virtual-machines/linux/use-remote-desktop.md) aby zainstalować pakiety, które są potrzebne na komputerze szablonu do łączenia się za pośrednictwem protokołu pulpitu zdalnego.

## <a name="install-ruby"></a>Instalowanie języka Ruby
Ruby jest dynamicznym językiem open source, który można łączyć ze skryptami bash. W tej sekcji `apt-get` pokazano, jak zainstalować najnowszą wersję [ruby](https://www.ruby-lang.org/).

1. Zainstaluj aktualizacje, uruchamiając następujące polecenia:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Zainstaluj [Ruby](https://www.ruby-lang.org/).  Ruby jest dynamicznym językiem open source, który można łączyć ze skryptami bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalowanie narzędzi programistycznych
W tej sekcji pokazano, jak zainstalować kilka edytorów tekstu. Gedit jest domyślnym edytorem tekstu dla środowiska pulpitu gnome. Został zaprojektowany jako edytor tekstu ogólnego przeznaczenia. Visual Studio Code to edytor tekstu, który zawiera obsługę debugowania i integracji kontroli źródła.

> [!NOTE]
> Dostępnych jest kilka różnych edytorów tekstu. Visual Studio Code i gedit to tylko dwa przykłady.

1. Zainstaluj [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).  Kod programu Visual Studio można zainstalować przy użyciu sklepu Snap Store.  Aby uzyskać alternatywne opcje instalacji, zobacz [Alternatywne pliki do pobrania programu Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Szablon jest teraz aktualizowany i zawiera zarówno język programowania, jak i narzędzia programistyczne potrzebne do ukończenia laboratorium. Obraz szablonu można teraz opublikować w laboratorium. Wybierz przycisk **Publikuj** na stronie szablonu, aby opublikować szablon w laboratorium.  

## <a name="cost"></a>Koszty 
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć następującego przykładu:
 
Dla klasy 25 uczniów z 20 godzin zaplanowanego czasu zajęć i 10 godzin przydziału na pracę domową lub zadania, cena za laboratorium będzie: 

25 studentów * (20 + 10) godzin * 20 jednostek laboratoryjnych * 0.01 USD za godzinę = 150 USD

Aby uzyskać więcej informacji na temat cen można znaleźć w następującym dokumencie: [Cennik usług Azure Lab .](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Podsumowanie
W tym artykule posuwał się przez kroki, aby utworzyć laboratorium dla klas skryptów. Podczas gdy ten artykuł koncentruje się na konfigurowaniu narzędzi skryptowych Ruby na komputerze z systemem Linux, ta sama konfiguracja może być używana dla innych klas skryptów, takich jak Python w systemie Linux.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne dla konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail linki do rejestracji do studentów](how-to-configure-student-usage.md#send-invitations-to-users). 





