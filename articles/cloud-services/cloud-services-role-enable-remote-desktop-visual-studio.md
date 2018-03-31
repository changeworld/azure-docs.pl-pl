---
title: Włączanie połączeń usług pulpitu zdalnego dla roli usług w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować aplikację usługi chmury Azure, aby umożliwić połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: ebe536cc5838e3e6f0a2c15950ec766c1fd44bfe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Włącz połączenia pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu z poziomu roli uruchomionej w systemie Azure. Podłączanie pulpitu zdalnego umożliwia rozwiązywanie problemów i diagnozowanie problemów z aplikacją, gdy jest on uruchomiony.

Kreatora publikowania, który zawiera program Visual Studio dla usług w chmurze zawiera opcję można włączyć pulpitu zdalnego w procesie publikowania, przy użyciu podanych poświadczeń. Użycie tej opcji jest odpowiednia, korzystając z programu Visual Studio 2017 wersji 15,4 i wcześniejszych.

Z programu Visual Studio 2017 wersji 15.5 i nowszych jednak zalecane jest włączenie pulpitu zdalnego za pomocą Kreatora publikowania, chyba że praca odbywa się tylko jako jeden Deweloper uniknąć. W danej sytuacji, w którym projekt może zostać otwarte przez innych zamiast tego włączyć pulpitu zdalnego za pośrednictwem portalu Azure, za pomocą programu PowerShell lub z wersji definicji w przepływie pracy ciągłego wdrażania. To zalecenie wynika z zmiana jak Visual Studio komunikuje się przy użyciu pulpitu zdalnego usługi w chmurze maszyny Wirtualnej, opisane w tym artykule.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurowanie pulpitu zdalnego za pomocą programu Visual Studio 2017 wersji 15,4 lub starszej

Podczas korzystania z programu Visual Studio 2017 wersji 15,4 lub starszy, można użyć **Włącz pulpit zdalny dla wszystkich ról** opcji Kreatora publikowania. Nadal można użyć kreatora z programu Visual Studio 2017 wersji 15.5 i nowszych, ale nie należy użyć opcji pulpitu zdalnego.

1. W programie Visual Studio, należy uruchomić Kreatora publikowania prawym przyciskiem myszy projekt usługi chmury w Eksploratorze rozwiązań i wybierając pozycję **publikowania**.

2. Zaloguj się do subskrypcji platformy Azure, w razie potrzeby, a następnie wybierz **dalej**.

3. Na **ustawienia** wybierz pozycję **Włącz pulpit zdalny dla wszystkich ról**, a następnie wybierz pozycję **ustawień...**  łącze, aby otworzyć **konfigurację usług pulpitu zdalnego** okno dialogowe.

4. W dolnej części okna dialogowego, wybierz **więcej opcji**. To polecenie wyświetla listy rozwijanej, w którym można utworzyć lub wybrać certyfikat, dzięki czemu można zaszyfrować informacji poświadczeń przy nawiązywaniu połączeń za pośrednictwem pulpitu zdalnego.

   > [!Note]
   > Certyfikaty, które są potrzebne dla połączeń usług pulpitu zdalnego są inne niż certyfikaty, które są używane dla innych operacji platformy Azure. Certyfikat dostępu zdalnego musi mieć klucz prywatny.

5. Wybierz certyfikat z listy lub  **&lt;Utwórz... &gt;**. W przypadku tworzenia nowego certyfikatu, Podaj przyjazną nazwę dla nowego certyfikatu po wyświetleniu monitu i wybierz **OK**. W polu listy rozwijanej pojawi się nowy certyfikat.

6. Podaj nazwę użytkownika i hasło. Nie można użyć istniejącego konta. Nie należy używać jako nazwa użytkownika dla nowego konta "Administrator".

7. Wybierz datę na konto, które wygasa i po połączeń pulpitu zdalnego, które będą blokowane.

8. Po podane wszystkie wymagane informacje, wybierz **OK**. Visual Studio dodaje ustawienia pulpitu zdalnego do projektu `.cscfg` i `.csdef` pliki, w tym hasła, które są szyfrowane przy użyciu wybranego certyfikatu.

9. Wykonania wszelkie pozostałe czynności przy użyciu **dalej** przycisk, a następnie wybierz **publikowania** gdy wszystko jest gotowe do publikowania usługi w chmurze. Jeśli nie chcesz opublikować, wybierz **anulować** i odpowiedź **tak** po wyświetleniu monitu, aby zapisać zmiany. Usługi w chmurze można opublikować później przy użyciu tych ustawień.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurowanie pulpitu zdalnego w przypadku korzystania z programu Visual Studio 2017 wersji 15.5 i nowsze

Z programu Visual Studio 2017 wersji 15.5 lub nowszym służy nadal Kreatora publikowania projektu usługi w chmurze. Można również użyć **Włącz pulpit zdalny dla wszystkich ról** opcję podczas pracy tylko jako pojedynczy developer.

Podczas pracy w ramach zespołu, zamiast tego należy włączyć pulpitu zdalnego usługi w chmurze platformy Azure przy użyciu [portalu Azure](cloud-services-role-enable-remote-desktop-new-portal.md) lub [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

To zalecenie wynika z zmiana jak Visual Studio 2017 wersji 15.5 i nowszych komunikuje się z usługą w chmurze maszyny Wirtualnej. Podczas włączania usług pulpitu zdalnego za pomocą Kreatora publikowania, wcześniejszych wersji programu Visual Studio komunikować się z maszyną Wirtualną za pośrednictwem co została wywołana z "RDP dodatku." Visual Studio 2017 wersji 15.5 i nowszych komunikuje się zamiast przy użyciu "rozszerzenia protokołu RDP" jest bardziej bezpieczne i bardziej elastyczne. Ta zmiana również wyrównana z tego portalu Azure i metod programu PowerShell, można włączyć pulpitu zdalnego również użyć rozszerzenia protokołu RDP.

Gdy program Visual Studio komunikuje się z rozszerzeniem RDP, przesyłać hasła w postaci zwykłego tekstu przy użyciu protokołu SSL. Jednak pliki konfiguracji projektu przechowywać tylko zaszyfrowane hasło, które mogły być odszyfrowane w postaci zwykłego tekstu tylko przy użyciu lokalnego certyfikatu, która pierwotnie była używana do ich zaszyfrowania.

W przypadku wdrożenia projektu usługi w chmurze z tego samego komputera programowanie każdorazowo, lokalnego certyfikatu jest dostępna. W takim przypadku można nadal używać **Włącz pulpit zdalny dla wszystkich ról** opcji Kreatora publikowania.

Jeśli użytkownik lub innymi deweloperami chcesz wdrożyć projekt usługi w chmurze z różnych komputerów, następnie te komputery nie będą jednak certyfikat wymagany do odszyfrowania hasło. W związku z tym pojawić następujący komunikat o błędzie:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Można zmienić hasła za każdym razem, gdy wdrożyć usługę w chmurze, ale ta akcja staje się niewygodne dla każdego, kto chce za pomocą pulpitu zdalnego.

Jeśli udostępniasz projektu z zespołem, następnie, najlepiej jest wyczyść pole wyboru w Kreatorze publikowania i zamiast tego włączyć pulpitu zdalnego za pomocą [portalu Azure](cloud-services-role-enable-remote-desktop-new-portal.md) lub za pomocą [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Wdrażanie z serwera kompilacji programu Visual Studio 2017 wersji 15.5 i nowsze

Można wdrożyć projektu usługi w chmurze z serwera kompilacji (na przykład z programu Visual Studio Team Services), na których program Visual Studio 2017 wersji 15.5 lub nowszej jest zainstalowany w agenta kompilacji. Z tego rozmieszczenia wdrożenia odbywa się na tym samym komputerze, na którym znajduje się certyfikat szyfrowania.

Aby korzystać z rozszerzeniem RDP z Visual Studio Team Services, podaj następujące informacje w definicji kompilacji:

1. Obejmują `/p:ForceRDPExtensionOverPlugin=true` argumentach MSBuild, aby się upewnić, że wdrożenie działa z rozszerzeniem RDP, a nie wtyczki RDP. Na przykład:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po wykonaniu kroków z kompilacji Dodaj **wdrażania usługi w chmurze Azure** kroku i ustawienia swoich właściwości.

1. Po wykonaniu kroku wdrożenia, należy dodać **programu Azure Powershell** krok, ustaw jej **Nazwa wyświetlana** właściwości "Azure wdrażania: Włącz protokół RDP rozszerzenia" (lub inną nazwę odpowiedniego) i wybierz odpowiednią Azure Subskrypcja.

1. Ustaw **typ skryptu** "Równo" i wklej poniższy kod do **wbudowanego skryptu** pola. (Można również utworzyć `.ps1` pliku w projekcie za pomocą tego skryptu, ustaw **typ skryptu** "Ścieżka pliku skryptu", jak i zestawu **ścieżka skryptu** wskaż plik.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Połącz do roli Azure przy użyciu pulpitu zdalnego

Po opublikowaniu usługi w chmurze na platformie Azure oraz włączono pulpitu zdalnego, umożliwia Visual Studio w Eksploratorze serwera zaloguj się do usługi w chmurze maszyny Wirtualnej:

1. W Eksploratorze serwera rozwiń **Azure** węzeł, a następnie rozwiń węzeł usługi w chmurze i jedną z jej ról, aby wyświetlić listę wystąpień.

2. Kliknij prawym przyciskiem myszy węzeł wystąpienia, a następnie wybierz **połączyć za pomocą pulpitu zdalnego**.

3. Wprowadź nazwę użytkownika i hasło, które wcześniej utworzony. Obecnie użytkownik jest zalogowany do sesji zdalnej.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)