---
title: Samouczek — utworzyć potok CI/CD w platformie Azure za pomocą usługi Team Services | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się jak utworzyć potok Visual Studio Team Services dla ciągłej integracji i dostarczania, która wdraża aplikację sieci web do usług IIS na maszynie Wirtualnej systemu Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d017f2453bbd757c16e2df034f5879f24ffe42f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192224"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Samouczek: Tworzenie potoku ciągłej integracji z Visual Studio Team Services i IIS
Aby zautomatyzować kompilacji, testowe i wdrożenia etapy tworzenia aplikacji, można użyć ciągłej integracji i wdrażania (CI/CD) potoku. W tym samouczku utworzysz potok CI/CD przy użyciu programu Visual Studio Team Services i Windows maszyny wirtualnej (VM) na platformie Azure z uruchomionymi usługami IIS. Omawiane kwestie:

> [!div class="checklist"]
> * Publikowanie aplikacji sieci web ASP.NET do projektu usługi Team Services
> * Tworzenie definicji kompilacji, która jest wyzwalany przez kod zatwierdzenia
> * Instalowanie i konfigurowanie usług IIS na maszynie wirtualnej na platformie Azure
> * Dodaj wystąpienia usług IIS do grupy wdrożenia w Team Services
> * Utworzyć wersję definicji, aby opublikować nową sieć web wdrażania pakietów do usług IIS
> * Testowanie potoku CI/CD

Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Tworzenie projektu na Team Services
Visual Studio Team Services umożliwia łatwe współpracy i rozwoju bez zachowania to rozwiązanie do zarządzania lokalnymi kodu. Usługi Team Services zapewnia testowania kodu w chmurze, kompilacji i usługi application insights. Można wybrać repozytorium kontroli wersji i IDE, która najlepiej pasuje do programowania kodu. W tym samouczku bezpłatne konto służy do tworzenia podstawowej aplikacji sieci web ASP.NET i CI/CD potoku. Jeśli nie masz już konto usługi Team Services [utworzyć](http://go.microsoft.com/fwlink/?LinkId=307137).

Zarządzanie procesem zatwierdzania kodu, definicje kompilacji i wydania definicje, należy utworzyć projektu w Team Services w następujący sposób:

1. Otwórz pulpit nawigacyjny usługi Team Services w przeglądarce sieci web i wybierz polecenie **nowy projekt**.
2. Wprowadź *myWebApp* dla **Nazwa projektu**. Pozostaw innych wartości domyślnych do użycia *Git* kontroli wersji i *Agile* elementów roboczych procesu.
3. Wybierz opcję, aby **udostępniać** *członków zespołu*, a następnie wybierz pozycję **Utwórz**.
5. Po utworzeniu projektu, należy wybrać opcję **zainicjować README lub gitignore**, następnie **zainicjować**.
6. Wewnątrz nowego projektu, wybierz **pulpity nawigacyjne** u góry, następnie wybierz **Otwórz w programie Visual Studio**.


## <a name="create-aspnet-web-application"></a>Tworzenie aplikacji sieci web ASP.NET
W poprzednim kroku utworzono projekt w Team Services. Ostatnim krokiem zostanie otwarty nowy projekt w programie Visual Studio. Zarządzanie zatwierdzenia kodu w **Team Explorer** okna. Utwórz lokalną kopię nowego projektu, a następnie utworzyć aplikację sieci web platformy ASP.NET na podstawie szablonu w następujący sposób:

1. Wybierz **klonowania** można utworzyć repozytorium git lokalnego projektu Team Services.
    
    ![Klonowanie repozytorium z projektu Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. W obszarze **rozwiązań**, wybierz pozycję **nowy**.

    ![Utwórz rozwiązanie aplikacji sieci web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Wybierz **Web** szablonów, a następnie wybierz pozycję **aplikacji sieci Web ASP.NET** szablonu.
    1. Wprowadź nazwę aplikacji, takich jak *myWebApp*i usuń zaznaczenie pola **Utwórz katalog rozwiązania**.
    2. Jeśli opcja jest dostępna, usuń zaznaczenie pola do **Dodaj usługę Application Insights do projektu**. Usługi Application Insights wymaga autoryzacji aplikacji sieci web z usługą Azure Application Insights. Aby zachować proste, w tym samouczku, Pomiń ten proces.
    3. Kliknij przycisk **OK**.
4. Wybierz **MVC** z listy szablonów.
    1. Wybierz **Zmień uwierzytelnianie**, wybierz **bez uwierzytelniania**, a następnie wybierz pozycję **OK**.
    2. Wybierz **OK** do utworzenia rozwiązania.
5. W **Team Explorer** okna, wybierz **zmiany**.

    ![Zatwierdź zmiany lokalne repozytorium git programu Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. W polu tekstowym zatwierdzania wpisz wiadomość, takich jak *zatwierdzania początkowego*. Wybierz **zatwierdzić wszystkie i synchronizacji** z menu rozwijanego.


## <a name="create-build-definition"></a>Tworzenie definicji kompilacji
W usłudze Team Services umożliwia definicji kompilacji przedstawiają sposób aplikacji powinny zostać skompilowane. W tym samouczku utworzymy podstawową definicję, który przyjmuje naszego kodu źródłowego kompilacje rozwiązania, a następnie tworzy sieci web wdrażanie pakietu, który możemy użyć do uruchomienia aplikacji sieci web na serwerze IIS.

1. W projekcie usługi Team Services, wybierz **kompilacji i wydania** u góry, następnie wybierz **kompilacje**.
3. Wybierz **+ nową definicję**.
4. Wybierz **ASP.NET (wersja ZAPOZNAWCZA)** szablonu, a następnie wybierz **Zastosuj**.
5. Pozostaw wszystkie domyślne wartości zadania. W obszarze **Pobierz źródła**, upewnij się, że *myWebApp* repozytorium i *wzorca* są zaznaczone oddziału.

    ![Utwórz definicję kompilacji w projekcie usługi Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na **wyzwalaczy** karcie, przesuń suwak **włączyć tego wyzwalacza** do *włączone*.
7. Zapisz definicję kompilacji i kolejki nowej kompilacji, wybierając **Zapisz & kolejka** , następnie **Zapisz & kolejka** ponownie. Pozostaw wartości domyślne, a następnie wybierz **kolejki**.

Obejrzyj gdy kompilacja jest zaplanowane na agencie hostowanej, następnie zaczyna kompilacji. Dane wyjściowe są podobne do poniższego przykładu:

![Pomyślnie kompilacji projektu Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby zapewnić platformę do uruchomienia aplikacji sieci web ASP.NET, należy maszyny wirtualnej systemu Windows z uruchomionymi usługami IIS. Usługi Team Services używa agenta do interakcji z wystąpieniem usług IIS Zatwierdź kodu i kompilacji są wyzwalane.

Utworzyć Maszynę systemu Windows Server 2016 z [nowe AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* w *wschodnie stany USA* lokalizacji. Grupa zasobów *myResourceGroupVSTS* i dodatkowe zasoby sieciowe również są tworzone. Aby zezwolić na ruch sieci web, TCP port *80* jest otwarty do maszyny Wirtualnej. Po wyświetleniu monitu podaj nazwę użytkownika i hasło do użycia jako poświadczenia logowania dla maszyny Wirtualnej:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupVSTS" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Aby nawiązać połączenie z maszyną Wirtualną, należy uzyskać publiczny adres IP z [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) w następujący sposób:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Tworzenie sesji usług pulpitu zdalnego do maszyny Wirtualnej:

```cmd
mstsc /v:<publicIpAddress>
```

Na Maszynie wirtualnej, należy otworzyć **administratora programu PowerShell** wiersza polecenia. Zainstaluj usługi IIS i wymagane funkcje platformy .NET:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Utwórz grupę wdrożenia
Aby Przekładanie sieci web wdrożenia pakietu na serwerze usług IIS, zdefiniuj grupę wdrożenia w Team Services. Ta grupa umożliwia określenie, serwerów, które są docelowy nowe kompilacje zatwierdzisz kod do usługi Team Services i zakończeniu kompilacji.

1. W usłudze Team Services, wybierz **kompilacji i wydania** , a następnie wybierz **grupy wdrożenia**.
2. Wybierz **Dodaj wdrożenie grupy**.
3. Wprowadź nazwę grupy, taką jak *Mój_iis*, a następnie wybierz pozycję **Utwórz**.
4. W **rejestrowania maszyn** upewnij się, *Windows* jest zaznaczone, a następnie zaznacz pole w celu **do uwierzytelniania należy używać osobisty token dostępu w skrypcie**.
5. Wybierz **Skopiuj skrypt do Schowka**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Dodaj IIS maszyny Wirtualnej do grupy wdrożenia
Z utworzoną grupę wdrożenia każdego wystąpienia usług IIS należy dodać do grupy. Usługi Team Services generuje skrypt, który pobiera i skonfiguruje agenta na maszynie Wirtualnej, który odbiera nowej sieci web wdrażania pakietów, a następnie stosuje je do usług IIS.

1. W **administratora programu PowerShell** sesji na maszynie Wirtualnej, Wklej i uruchom skrypt skopiowany z usługi Team Services.
2. Po wyświetleniu monitu, aby skonfigurować tagi dla agenta, wybierz *Y* , a następnie wprowadź *web*.
3. Po wyświetleniu monitu dla konta użytkownika, naciśnij klawisz *zwracać* zaakceptować wartości domyślne.
4. Poczekaj na zakończenie z następującym komunikatem skryptu *vstsagent.account.computername usługa została uruchomiona pomyślnie*.
5. W **grupy wdrożenia** strony **kompilacji i wydania** menu, otwórz *Mój_iis* grupę wdrożenia. Na **maszyny** karcie, sprawdź, czy maszyna wirtualna jest wyświetlane.

    ![Maszyna wirtualna pomyślnie dodane do wdrożenia usługi Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Utwórz definicję zlecenia
Aby opublikować kompilacji, należy utworzyć definicji wersji w Team Services. Ta definicja jest automatycznie wyzwalane przez to pomyślnego utworzenia kompilacji aplikacji. Wybierz grupę wdrożenia sieci web wypychanej wdrożyć pakiet i zdefiniuj odpowiednie ustawienia usług IIS.

1. Wybierz **kompilacji i wydania**, a następnie wybierz pozycję **kompilacje**. Wybierz definicję kompilacji, utworzony w poprzednim kroku.
2. W obszarze **niedawno ukończoną**, wybierz ostatniej kompilacji, a następnie wybierz **wersji**.
3. Wybierz **tak** do tworzenia definicji wersji.
4. Wybierz **pusty** szablonu, następnie wybierz **dalej**.
5. Sprawdź definicję kompilacji projektu i źródła są wypełniane przy użyciu projektu.
6. Wybierz **ciągłe wdrażanie** pole wyboru, a następnie wybierz **Utwórz**.
7. Zaznacz pole listy rozwijanej obok pola **+ Dodaj zadania** i wybierz polecenie *dodać faza wdrożenia grupy*.
    
    ![Dodaj zadanie do wersji definicji w Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Wybierz **Dodaj** obok **Deploy(Preview) aplikacji sieci Web usług IIS**, a następnie wybierz pozycję **Zamknij**.
9. Wybierz **Uruchom na grupę wdrożenia** zadaniem nadrzędnym.
    1. Dla **grupę wdrożenia**, wybierz grupę wdrożenia został utworzony wcześniej, takich jak *Mój_iis*.
    2. W **komputera tagi** wybierz opcję **Dodaj** i wybierz polecenie *web* tagu.
    
    ![Zwolnij definicji zadania wdrażania w grupie dla usług IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Wybierz **Wdróż: Wdrażanie aplikacji sieci Web usług IIS** zadanie, aby skonfigurować ustawienia wystąpienia usług IIS w następujący sposób:
    1. Aby uzyskać **nazwa witryny sieci Web**, wprowadź *domyślna witryna sieci Web*.
    2. Pozostaw wszystkie inne ustawienia domyślne.
12. Wybierz **zapisać**, a następnie wybierz pozycję **OK** dwa razy.


## <a name="create-a-release-and-publish"></a>Tworzenie zlecenia i publikowanie
Teraz możesz wypchnąć sieci web wdrażanie pakietu jako nową wersję. Ten krok, który komunikuje się z agentem na każde wystąpienie, która jest częścią grupy wdrożenia, wypchnięcia sieci web wdrożenia pakietu, a następnie konfiguruje usługi IIS do uruchomienia aplikacji sieci web zaktualizowane.

1. W definicji wersji, wybierz **+ wersji**, a następnie wybierz **Tworzenie wersji**.
2. Sprawdź, czy ostatniej kompilacji jest zaznaczony na liście rozwijanej wraz z **automatycznego wdrożenia: po utworzeniu wersji**. Wybierz pozycję **Utwórz**.
3. Transparent małych pojawia się w górnej części definicję zlecenia, takie jak *wersji "Wersji 1" utworzył*. Wybierz łącze wersji.
4. Otwórz **dzienniki** kartę, aby obserwowanie wersji.
    
    ![Pomyślne wersji Team Services i sieci web wdrażanie pakietu wypychania](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Po zakończeniu wersji Otwórz przeglądarkę sieci web, a następnie wprowadź publiczny adres OPS maszyny Wirtualnej. Aplikacja sieci web programu ASP.NET jest uruchomiona.

    ![Uruchomiona na maszynie Wirtualnej IIS aplikacji sieci web ASP.NET](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Test całej potoku CI/CD
Z aplikacji sieci web uruchomionej na serwerze IIS teraz spróbuj całego procesu CI/CD. Po wprowadzeniu zmiany w Visual Studio i zatwierdzania wyzwolenia kompilacji kodu, następnie wyzwalające zlecenia zaktualizowane witrynę sieci web do usług IIS należy wdrożyć pakiet:

1. W programie Visual Studio Otwórz **Eksploratora rozwiązań** okna.
2. Przejdź do i Otwórz *myWebApp | Widoki | Strona główna | Index.cshtml*
3. Edytuj wiersz 6 do odczytu:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Zapisz plik.
5. Otwórz **Team Explorer** wybierz *myWebApp* projektu, a następnie wybierz pozycję **zmiany**.
6. Wpisz wiadomość, zatwierdzania, takich jak *potoku testowania CI/CD*, a następnie wybierz **wszystkie zatwierdzenia i synchronizacji** z menu rozwijanego.
7. W obszarze roboczym Team Services nowej kompilacji zostanie wywołany z zatwierdzenia kodu. 
    - Wybierz **kompilacji i wydania**, a następnie wybierz pozycję **kompilacje**. 
    - Wybierz definicję kompilacji, a następnie wybierz **w kolejce & uruchomiona** kompilacji, aby obejrzeć w trakcie kompilacji.
9. Gdy kompilacja zakończy się pomyślnie, zostanie wywołany nowej wersji.
    - Wybierz **kompilacji i wydania**, następnie **wersje** wyświetlić wdrażania pakietu do maszyny Wirtualnej IIS sieci web. 
    - Wybierz **Odśwież** ikonę, aby zaktualizować stan. Gdy *środowisk* kolumna zawiera zielony znacznik wyboru, pomyślnym wdrożeniu wersji usług IIS.
11. Aby zobaczyć zmiany zastosowane, należy odświeżyć witryny usług IIS w przeglądarce sieci Web.

    ![Uruchamianie na IIS maszyny Wirtualnej na podstawie elementu konfiguracji/CD potoku aplikacji sieci web ASP.NET](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzeniu aplikacji sieci web ASP.NET w usłudze Team Services i skonfigurowaniu kompilacji i definicji wersji, aby wdrożyć nową sieć web wdrażania pakietów do usług IIS na każdym zatwierdzeniu kodu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Publikowanie aplikacji sieci web ASP.NET do projektu usługi Team Services
> * Tworzenie definicji kompilacji, która jest wyzwalany przez kod zatwierdzenia
> * Instalowanie i konfigurowanie usług IIS na maszynie wirtualnej na platformie Azure
> * Dodaj wystąpienia usług IIS do grupy wdrożenia w Team Services
> * Utworzyć wersję definicji, aby opublikować nową sieć web wdrażania pakietów do usług IIS
> * Testowanie potoku CI/CD

ADVANCE do samouczka dalej, aby dowiedzieć się, jak zainstalować program SQL&#92;IIS&#92;stos .NET w parze składającej się z maszynami wirtualnymi systemu Windows.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET stack](tutorial-iis-sql.md)