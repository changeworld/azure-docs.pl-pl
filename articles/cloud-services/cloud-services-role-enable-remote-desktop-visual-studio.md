---
title: Za pomocą programu Visual Studio włącz pulpit zdalny dla roli (usługi w chmurze Azure)
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure, aby umożliwić połączenia pulpitu zdalnego
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294383"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Włączanie połączenia pulpitu zdalnego dla roli w usługach w chmurze platformy Azure przy użyciu programu Visual Studio

> [!div class="op_single_selector"]
> * [Portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli uruchomionej na platformie Azure. Za pomocą połączenia pulpitu zdalnego można rozwiązywać i diagnozować problemy z aplikacją, gdy jest uruchomiona.

Kreator publikowania, który program Visual Studio udostępnia dla usług w chmurze, zawiera opcję włączania pulpitu zdalnego podczas procesu publikowania przy użyciu poświadczeń, które podasz. Użycie tej opcji jest odpowiednie podczas korzystania z programu Visual Studio 2017 w wersji 15.4 i wcześniejszych.

W programie Visual Studio 2017 w wersji 15.5 lub nowszej zaleca się jednak unikanie włączania pulpitu zdalnego za pośrednictwem kreatora publikowania, chyba że pracujesz tylko jako pojedynczy deweloper. W każdej sytuacji, w której projekt może być otwarty przez innych deweloperów, zamiast tego włączyć pulpit zdalny za pośrednictwem witryny Azure portal, za pośrednictwem programu PowerShell lub z potoku wydania w ciągłym przepływie pracy wdrażania. To zalecenie jest spowodowane zmianą sposobu komunikowania się programu Visual Studio z pulpitem zdalnym na maszynie wirtualnej usługi w chmurze, jak wyjaśniono w tym artykule.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurowanie pulpitu zdalnego za pomocą programu Visual Studio 2017 w wersji 15.4 lub wcześniejszej

Korzystając z programu Visual Studio 2017 w wersji 15.4 lub wcześniejszej, można użyć opcji **Włącz pulpit zdalny dla wszystkich ról** w kreatorze publikowania. Nadal można używać kreatora w programie Visual Studio 2017 w wersji 15.5 lub nowszej, ale nie należy używać opcji Pulpit zdalny.

1. W programie Visual Studio uruchom kreatora publikowania, klikając prawym przyciskiem myszy projekt usługi w chmurze w Eksploratorze rozwiązań i wybierając pozycję **Publikuj**.

2. W razie potrzeby zaloguj się do subskrypcji platformy Azure i wybierz pozycję **Dalej**.

3. Na stronie **Ustawienia** wybierz pozycję **Włącz pulpit zdalny dla wszystkich ról**, a następnie wybierz łącze **Ustawienia...,** aby otworzyć okno dialogowe **Konfiguracja pulpitu zdalnego.**

4. U dołu okna dialogowego wybierz pozycję **Więcej opcji**. To polecenie wyświetla listę rozwijaną, na której można utworzyć lub wybrać certyfikat, dzięki czemu można szyfrować informacje o poświadczeniach podczas łączenia się za pośrednictwem pulpitu zdalnego.

   > [!Note]
   > Certyfikaty potrzebne do połączenia pulpitu zdalnego różnią się od certyfikatów używanych dla innych operacji platformy Azure. Certyfikat dostępu zdalnego musi mieć klucz prywatny.

5. Wybierz certyfikat z listy lub wybierz pozycję ** &lt;Utwórz... &gt;**. W przypadku tworzenia nowego certyfikatu podaj przyjazną nazwę nowego certyfikatu po wyświetleniu monitu i wybierz przycisk **OK**. Nowy certyfikat pojawi się w polu listy rozwijanej.

6. Podaj nazwę użytkownika i hasło. Nie można użyć istniejącego konta. Nie używaj "Administratora" jako nazwy użytkownika nowego konta.

7. Wybierz datę wygaśnięcia konta i zablokowanie połączeń pulpitu zdalnego.

8. Po podasz wszystkie wymagane informacje wybierz przycisk **OK**. Program Visual Studio dodaje ustawienia pulpitu `.cscfg` `.csdef` zdalnego do projektu i plików, w tym hasło zaszyfrowane przy użyciu wybranego certyfikatu.

9. Wykonaj wszystkie pozostałe kroki za pomocą przycisku **Dalej,** a następnie wybierz pozycję **Publikuj,** gdy będziesz gotowy do opublikowania usługi w chmurze. Jeśli nie chcesz publikować, wybierz **pozycję Anuluj** i **odpowiedz tak** po wyświetleniu monitu o zapisanie zmian. Usługę w chmurze można opublikować później za pomocą tych ustawień.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurowanie pulpitu zdalnego podczas korzystania z programu Visual Studio 2017 w wersji 15.5 lub nowszej

W programie Visual Studio 2017 w wersji 15.5 lub nowszej nadal można używać kreatora publikowania w projekcie usługi w chmurze. Możesz również użyć opcji **Włącz pulpit zdalny dla wszystkich ról,** jeśli pracujesz tylko jako pojedynczy programista.

Jeśli pracujesz jako część zespołu, należy zamiast tego włączyć pulpit zdalny w usłudze w chmurze platformy Azure przy użyciu [witryny Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub [programu PowerShell.](cloud-services-role-enable-remote-desktop-powershell.md)

To zalecenie jest spowodowane zmianą sposobu, w jaki visual studio 2017 w wersji 15.5 i później komunikuje się z maszyną wirtualną usługi w chmurze. Podczas włączania pulpitu zdalnego za pośrednictwem kreatora publikowania, wcześniejsze wersje programu Visual Studio komunikują się z maszyną wirtualną za pośrednictwem tak zwanej "wtyczki RDP". Visual Studio 2017 w wersji 15.5 i nowszych komunikuje się zamiast tego przy użyciu "rozszerzenia RDP", który jest bezpieczniejsze i bardziej elastyczne. Ta zmiana jest również zgodna z faktem, że azure portal i powershell metody, aby włączyć pulpit zdalny również używać rozszerzenia RDP.

Gdy program Visual Studio komunikuje się z rozszerzeniem RDP, przesyła hasło zwykłego tekstu za pomocą protokołu TLS. Jednak pliki konfiguracyjne projektu przechowują tylko zaszyfrowane hasło, które można odszyfrować na zwykły tekst tylko przy użyciu lokalnego certyfikatu, który został pierwotnie użyty do zaszyfrowania.

Jeśli projekt usługi w chmurze jest wdrażany za każdym razem z tego samego komputera dewelopera, ten certyfikat lokalny jest dostępny. W takim przypadku nadal można użyć opcji **Włącz pulpit zdalny dla wszystkich ról** w kreatorze publikowania.

Jeśli jednak ty lub inni deweloperzy chcecie wdrożyć projekt usługi w chmurze z różnych komputerów, te inne komputery nie będą miały certyfikatu niezbędnego do odszyfrowania hasła. W rezultacie zostanie wyświetlony następujący komunikat o błędzie:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Hasło można zmienić za każdym razem, gdy wdrażasz usługę w chmurze, ale ta akcja staje się niewygodna dla wszystkich osób, które muszą korzystać z pulpitu zdalnego.

Jeśli udostępniasz projekt zespołowi, najlepiej wyczyścić tę opcję w kreatorze publikowania i zamiast tego włączyć pulpit zdalny bezpośrednio za pośrednictwem [witryny Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub za pomocą programu [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Wdrażanie z serwera kompilacji w programie Visual Studio 2017 w wersji 15.5 i nowszej

Można wdrożyć projekt usługi w chmurze z serwera kompilacji (na przykład z usługami Azure DevOps), na którym visual studio 2017 w wersji 15.5 lub nowszej jest zainstalowany w agencie kompilacji. W tym układzie wdrożenie odbywa się z tego samego komputera, na którym jest dostępny certyfikat szyfrowania.

Aby użyć rozszerzenia RDP z usług Azure DevOps Services, dołącz następujące szczegóły w potoku kompilacji:

1. Uwzględnij `/p:ForceRDPExtensionOverPlugin=true` w argumentach MSBuild, aby upewnić się, że wdrożenie działa z rozszerzeniem RDP, a nie wtyczką RDP. Przykład:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po krokach kompilacji dodaj krok **wdrażania usługi Azure Cloud Service** i ustaw jego właściwości.

1. Po kroku wdrażania dodaj krok **programu Azure Powershell,** ustaw jego właściwość **nazwy display** na "Wdrożenie platformy Azure: Włącz rozszerzenie RDP" (lub inną odpowiednią nazwę) i wybierz odpowiednią subskrypcję platformy Azure.

1. Ustaw **typ skryptu** na "Wbudowany" i wklej poniższy kod do pola **Skrypt wbudowany.** (Można również utworzyć `.ps1` plik w projekcie za pomocą tego skryptu, ustawić **typ skryptu** na "Ścieżka pliku skryptu" i ustawić **ścieżkę skryptu,** aby wskazywała plik).

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Łączenie się z rolą platformy Azure przy użyciu pulpitu zdalnego

Po opublikowaniu usługi w chmurze na platformie Azure i włączeniu pulpitu zdalnego można użyć Eksploratora programu Visual Studio Server do zalogowania się do maszyny Wirtualnej usługi w chmurze:

1. W Eksploratorze serwera rozwiń węzeł **platformy Azure,** a następnie rozwiń węzeł dla usługi w chmurze i jedną z jego ról, aby wyświetlić listę wystąpień.

2. Kliknij prawym przyciskiem myszy węzeł wystąpienia i wybierz polecenie **Połącz za pomocą pulpitu zdalnego**.

3. Wprowadź nazwę użytkownika i hasło utworzone wcześniej. Jesteś teraz zalogowany do sesji zdalnej.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)
