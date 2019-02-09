---
title: Przygotuj komputer-host usługi Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przygotowania komputera hosta usługi Azure Stack Development Kit (ASDK) ASDK instalacji.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 36012c023025b8304dfaf9cc63997f600ef6cbe8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962727"
---
# <a name="prepare-the-asdk-host-computer"></a>Przygotuj komputer-host ASDK
Przed zainstalowaniem ASDK na komputerze-hoście, ASDK host muszą być przygotowane do instalacji. Gdy na komputerze deweloperskim zestaw hostów zostały przygotowane, uruchomi się z dysku twardego maszyny wirtualnej CloudBuilder.vhdx, aby rozpocząć wdrażanie ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Przygotowanie komputera hosta development kit
Przed zainstalowaniem ASDK na komputerze-hoście, należy przygotować środowisko komputera hosta ASDK.
1. Zaloguj się jako Administrator lokalny na komputerze hosta development kit.
2. Upewnij się, że plik CloudBuilder.vhdx został przeniesiony do katalogu głównego dysku C:\ (C:\CloudBuilder.vhdx).
3. Uruchom następujący skrypt, aby pobrać plik Instalatora programu rozwoju kit (asdk installer.ps1) z [repozytorium narzędzia usługi Azure Stack GitHub](https://github.com/Azure/AzureStack-Tools) do **C:\AzureStack_Installer** folderu na użytkownika Development kit hoście:

   > [!IMPORTANT]
   > Pamiętaj pobrać plik asdk installer.ps1 każdym ASDK. Częste zmiany zostały wprowadzone w tym skrypcie i najbardziej aktualnej wersji należy używać w przypadku każdego wdrożenia ASDK. Starsze wersje skrypt może nie działać w najnowszej wersji.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Z poziomu konsoli programu PowerShell z podwyższonym poziomem uprawnień uruchom **C:\AzureStack_Installer\asdk-installer.ps1** skrypt, a następnie kliknij przycisk **przygotowanie środowiska**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Na **vhdx wybierz Cloudbuilder** strony Instalatora, Znajdź i wybierz **cloudbuilder.vhdx** pliku, który został pobrany i w [poprzednich kroków](asdk-download.md). Na tej stronie możesz również opcjonalnie włączyć **Dodaj sterowniki** pole wyboru, jeśli chcesz dodać sterowniki do komputera hosta development kit. Kliknij przycisk **Dalej**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Na **opcjonalne ustawienia** Podaj administratora lokalnego konta informacje o komputerze deweloperskim zestaw hosta, a następnie kliknij przycisk **dalej**.<br><br>Jeśli nie zostaną podane poświadczenia administratora lokalnego, w tym kroku, należy bezpośrednio lub KVM dostęp do hosta po ponownym uruchomieniu komputera w ramach konfigurowania deweloperski.

   ![](media/asdk-prepare-host/3.PNG)

    Można również podać wartości następujące opcjonalne ustawienia:
    - **Nazwa_komputera**: Ta opcja określa nazwę hosta development kit. Nazwa musi spełniać wymagania w pełni kwalifikowaną nazwę domeny i musi być co najwyżej 15 znaków lub mniej znaków. Wartość domyślna to losową nazwę komputera generowane przez Windows.
    - **Konfiguracji statycznych adresów IP**: Ustawia wdrożenia do użycia statycznego adresu IP. W przeciwnym razie gdy Instalator jest wykonywany ponowny rozruch cloudbuilder.vhdx, interfejsy sieciowe są skonfigurowane za pomocą usługi DHCP. Jeśli zdecydujesz się użyć konfiguracji statycznych adresów IP, dodatkowe opcje są wyświetlane, gdy należy się również:
      - Wybierz kartę sieciową. Upewnij się, możesz nawiązać połączenie karty przed kliknięciem przycisku **dalej**.
      - Upewnij się, że wyświetlana **adresu IP**, **bramy**, i **DNS** wartości są poprawne, a następnie kliknij przycisk **dalej**.
13. Kliknij przycisk **dalej** można uruchomić proces przygotowywania.
14. Podczas przygotowywania wskazuje **Ukończono**, kliknij przycisk **dalej**.

    ![](media/asdk-prepare-host/4.PNG)

15. Kliknij przycisk **ponowny rozruch teraz** do rozruchu komputera hosta development kit w cloudbuilder.vhdx i [kontynuować proces wdrażania](asdk-install.md).

    ![](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Kolejne kroki
[Zainstaluj ASDK](asdk-install.md)
