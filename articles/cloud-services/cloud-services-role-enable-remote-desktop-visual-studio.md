---
title: Włączanie połączeń usług pulpitu zdalnego dla roli w usługach Azure Cloud Services
description: Jak skonfigurować aplikację usługi chmury platformy Azure, aby zezwolić na połączenia pulpitu zdalnego
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 924719a8371f4d41cb9ead09252d8f3d3424326a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717757"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Włączanie funkcji Podłączanie pulpitu zdalnego dla roli w usługach Azure Cloud Services przy użyciu programu Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny zapewnia dostęp do pulpitu roli działających na platformie Azure. Podłączanie pulpitu zdalnego umożliwia rozwiązywanie problemów i diagnozowanie problemów z aplikacją, gdy jest on uruchomiony.

Kreator publikowania, który program Visual Studio oferuje dla usług w chmurze zawiera opcję można włączyć pulpitu zdalnego w procesie publikowania za pomocą podanych poświadczeń. Przy użyciu tej opcji jest odpowiednia w przypadku korzystania z programu Visual Studio 2017 w wersji 15.4 lub starszym.

Za pomocą programu Visual Studio 2017 w wersji 15.5 lub nowszej jednak zalecane jest włączenie pulpitu zdalnego za pomocą Kreatora publikacji, chyba że pracujesz tylko w postaci jednego dewelopera uniknąć. Dla każdej sytuacji, w którym projekt może zostać otwarty przez innych programistów zamiast tego włączyć Pulpit zdalny za pośrednictwem witryny Azure portal, za pomocą programu PowerShell lub z potoku wersji w przepływie pracy ciągłego wdrażania. To zalecenie wynika z zmianę jak Visual Studio komunikuje się przy użyciu pulpitu zdalnego w usłudze w chmurze maszyny Wirtualnej, opisany w tym artykule.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurowanie pulpitu zdalnego za pomocą programu Visual Studio 2017 w wersji 15.4 lub starszym

Korzystając z programu Visual Studio 2017 w wersji 15.4 lub starszym, można użyć **Włącz pulpit zdalny dla wszystkich ról** opcja w Kreatorze publikacji. Nadal można użyć kreatora za pomocą programu Visual Studio 2017 w wersji 15.5 lub nowszej, ale nie należy używać opcji usług pulpitu zdalnego.

1. W programie Visual Studio, należy uruchomić Kreatora publikacji, kliknij prawym przyciskiem myszy projekt usługi w chmurze w Eksploratorze rozwiązań i wybierając pozycję **Publikuj**.

2. Zaloguj się do subskrypcji platformy Azure, w razie potrzeby, a następnie wybierz pozycję **dalej**.

3. Na **ustawienia** wybierz opcję **Włącz pulpit zdalny dla wszystkich ról**, a następnie wybierz **ustawień...**  link umożliwiający otworzenie **konfiguracji pulpitu zdalnego** okno dialogowe.

4. W dolnej części okna dialogowego wybierz **więcej opcji**. To polecenie wyświetla listy rozwijanej, w którym można utworzyć lub wybrać certyfikat, tak aby informacje o poświadczeniach można szyfrować podczas nawiązywania połączenia za pośrednictwem pulpitu zdalnego.

   > [!Note]
   > Certyfikaty, które są potrzebne dla połączeń usług pulpitu zdalnego różnią się od certyfikaty używane dla innych operacji na platformie Azure. Certyfikat dostępu zdalnego musi mieć klucz prywatny.

5. Wybierz certyfikat z listy lub wybierz  **&lt;Utwórz... &gt;** . W przypadku tworzenia nowego certyfikatu, Podaj przyjazną nazwę dla nowego certyfikatu, po wyświetleniu monitu, a następnie wybierz **OK**. W polu listy rozwijanej pojawi się nowy certyfikat.

6. Podaj nazwę użytkownika i hasło. Nie można użyć istniejącego konta. Nie należy używać jako nazwy użytkownika dla nowego konta "Administrator".

7. Wybierz datę na konto, które wygasa, a po połączeń pulpitu zdalnego, które będą blokowane.

8. Po podano wszystkie wymagane informacje, wybierz **OK**. Program Visual Studio dodaje ustawienia pulpitu zdalnego do swojego projektu `.cscfg` i `.csdef` pliki, w tym hasła, które są szyfrowane przy użyciu wybranego certyfikatu.

9. Wykonaj wszystkie pozostałe kroki przy użyciu **dalej** przycisk, a następnie wybierz **Publikuj** kiedy wszystko będzie gotowe do opublikowania swojej usługi w chmurze. Jeśli nie jesteś gotowy do publikowania, wybierz **anulować** i odpowiedź **tak** po wyświetleniu monitu, aby zapisać zmiany. Usługi w chmurze można opublikować później, przy użyciu tych ustawień.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurowanie pulpitu zdalnego, korzystając z programu Visual Studio 2017 w wersji 15.5 lub nowszej

Za pomocą programu Visual Studio 2017 w wersji 15.5 lub nowszej możesz nadal używać Kreatora publikowania projektu usługi w chmurze. Można również użyć **Włącz pulpit zdalny dla wszystkich ról** opcję, jeśli pracujesz tylko w postaci jednego dewelopera.

Jeśli pracujesz w ramach zespołu, zamiast tego należy włączyć pulpitu zdalnego w usłudze w chmurze platformy Azure przy użyciu [witryny Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

To zalecenie wynika z zmianę jak Visual Studio 2017 w wersji 15.5 lub nowszej komunikuje się z usługą w chmurze maszyny Wirtualnej. Podczas włączania usług pulpitu zdalnego za pomocą Kreatora publikacji, starszych wersji programu Visual Studio komunikować się z maszyną Wirtualną za pośrednictwem co wywołał "RDP dodatku plug-in." Visual Studio 2017 w wersji 15.5 lub nowszej, który komunikuje się zamiast tego użycie "rozszerzenia RDP" to bezpieczniejsze i bardziej elastycznym. Ta zmiana jest również wyrównuje z faktu, witryny Azure portal i metod programu PowerShell, aby włączyć Pulpit zdalny również użyć rozszerzenia RDP.

Gdy program Visual Studio komunikuje się z rozszerzeniem RDP, przesyłać hasła w postaci zwykłego tekstu za pośrednictwem protokołu SSL. Jednak pliki konfiguracji projektu przechowywać tylko zaszyfrowane hasło, które można odszyfrować na zwykły tekst tylko przy użyciu lokalnego certyfikatu, który pierwotnie został użyty do zaszyfrowania.

W przypadku wdrożenia projektu usługi w chmurze z tym samym komputerze deweloperskim każdorazowo lokalnego certyfikatu jest dostępna. W takim przypadku można nadal używać **Włącz pulpit zdalny dla wszystkich ról** opcja w Kreatorze publikacji.

Jeśli użytkownik lub innym deweloperom Wdróż projekt usługi w chmurze z różnych komputerów, następnie te komputery nie będą jednak certyfikat wymagany do odszyfrowania hasło. W rezultacie zobaczysz następujący komunikat o błędzie:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Można zmienić hasła, za każdym razem, gdy wdrożona usługa w chmurze, ale staje się tej akcji nie można użyć dla każdego, kto chce za pomocą pulpitu zdalnego.

Jeśli udostępniasz aplikację z zespołem, następnie najlepiej wyczyść pole wyboru w Kreatorze publikacji i zamiast tego włączyć za pomocą usług pulpitu zdalnego [witryny Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub za pomocą [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Wdrażanie z serwera kompilacji za pomocą programu Visual Studio 2017 w wersji 15.5 lub nowszej

Można wdrożyć projektu usługi w chmurze z serwera kompilacji (na przykład dzięki usługom DevOps platformy Azure), na których Visual Studio 2017 w wersji 15.5 lub nowszej jest zainstalowany w agencie kompilacji. Za pomocą to rozmieszczenie wdrożenie to następuje na tym samym komputerze, na którym jest dostępny certyfikat szyfrowania.

Aby użyć rozszerzenia protokołu RDP z usługom DevOps platformy Azure, podaj następujące informacje w potoku kompilacji:

1. Obejmują `/p:ForceRDPExtensionOverPlugin=true` argumentach programu MSBuild, aby upewnić się, czy wdrożenie działa z rozszerzeniem RDP, a nie wtyczki protokołu RDP. Na przykład:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po kroków kompilacji dodać **wdrażania usługi w chmurze Azure** krok i ustaw jego właściwości.

1. Po wykonaniu kroku wdrożenia, należy dodać **programu Azure Powershell** krok, ustaw jego **nazwę wyświetlaną** właściwość "wdrażania na platformie Azure: Włączanie rozszerzenia RDP"(lub inną odpowiednią nazwę) i wybierz odpowiednią subskrypcję platformy Azure.

1. Ustaw **typ skryptu** do "Inline" i Wklej kod poniżej do **wbudowany skrypt** pola. (Można również utworzyć `.ps1` pliku w projekcie za pomocą tego skryptu, ustaw **typ skryptu** "Ścieżka pliku skryptu" i zestaw **ścieżka skryptu** wskaż plik.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Łączenie z roli usługi platformy Azure za pomocą usług pulpitu zdalnego

Po Opublikuj usługę w chmurze na platformie Azure i włączono pulpitu zdalnego, można użyć Eksploratora serwera w usłudze Visual Studio do zalogowania się do usługi w chmurze maszyny Wirtualnej:

1. W oknie Eksploratora serwera rozwiń **Azure** węzła, a następnie rozwiń węzeł usługi w chmurze i jedną z jej ról, aby wyświetlić listę wystąpień.

2. Kliknij prawym przyciskiem myszy węzeł wystąpienia, a następnie wybierz pozycję **połączyć za pomocą pulpitu zdalnego**.

3. Wprowadź nazwę użytkownika i hasło, który został utworzony wcześniej. Teraz użytkownik jest zalogowany do sesji zdalnej.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)
