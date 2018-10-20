---
title: Samouczek — tworzenie potoku CI/CD na platformie Azure dzięki usługom DevOps platformy Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć potoku usługom DevOps platformy Azure na potrzeby ciągłej integracji i dostarczania, który służy do wdrażania aplikacji sieci web usług IIS na maszynie Wirtualnej Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
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
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4b4d514ec8bfd78b303a7f51c2a4072507da5be9
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471462"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>Samouczek: Tworzenie potoku ciągłej integracji za pomocą usługi DevOps platformy Azure i usług IIS
Aby zautomatyzować tworzenie, testowanie i fazy wdrożenia, projektowania aplikacji, można użyć ciągłej integracji i potoku wdrażania (CI/CD). W tym samouczku utworzysz potok ciągłej integracji/ciągłego wdrażania za pomocą usługi DevOps platformy Azure i Windows maszyny wirtualnej (VM) na platformie Azure, w którym działa program IIS. Omawiane kwestie:

> [!div class="checklist"]
> * Publikowanie aplikacji sieci web ASP.NET do projektu usługom DevOps platformy Azure
> * Tworzenie potoku kompilacji, który jest wyzwalany przez kod zatwierdzenia
> * Instalowanie i konfigurowanie usług IIS na maszynie wirtualnej na platformie Azure
> * Dodaj wystąpienie usług IIS do grupy wdrożenia w usługom DevOps platformy Azure
> * Tworzenie potoku wydania w celu publikowania nowej sieci web wdrażanie pakietów usług IIS
> * Testowanie potoku ciągłej integracji/ciągłego wdrażania

Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-project-in-azure-devops-services"></a>Tworzenie projektu w usługom DevOps platformy Azure
Usługom DevOps platformy Azure pozwala na potrzeby projektowania i łatwa współpraca bez konserwowania lokalnego rozwiązania do zarządzania kodem. Usługi Azure DevOps zapewnia testowanie kodu w chmurze, kompilacja i usługi application insights. Możesz wybrać repozytorium kontroli wersji i środowisko IDE, która najlepiej pasuje do tworzenia kodu. Na potrzeby tego samouczka można użyć bezpłatnego organizacji do tworzenia podstawowej aplikacji sieci web ASP.NET i potoku ciągłej integracji/ciągłego wdrażania. Jeśli nie masz jeszcze organizacji usługom DevOps platformy Azure, [utworzyć](http://go.microsoft.com/fwlink/?LinkId=307137).

Do zarządzania procesem zatwierdzania kodu, potoki, potoki kompilacji i wydań, w następujący sposób Utwórz projekt w usługom DevOps platformy Azure:

1. Otwórz pulpit nawigacyjny usługom DevOps platformy Azure w przeglądarce sieci web i wybierz polecenie **nowy projekt**.
2. Wprowadź *myWebApp* dla **Nazwa projektu**. Pozostaw wszystkie pozostałe wartości domyślne, aby użyć *Git* kontroli wersji i *Agile* elementów roboczych procesu.
3. Wybierz opcję, aby **udostępniać** *członków zespołu*, a następnie wybierz **Utwórz**.
5. Po utworzeniu projektu, wybierz opcję, aby **zainicjować za pomocą pliku README lub gitignore**, następnie **zainicjować**.
6. Wewnątrz Twój nowy projekt, wybierz **pulpity nawigacyjne** u góry, następnie wybierz pozycję **Otwórz w programie Visual Studio**.


## <a name="create-aspnet-web-application"></a>Tworzenie aplikacji sieci web platformy ASP.NET
W poprzednim kroku utworzono projekt w usługom DevOps platformy Azure. Ostatnim krokiem zostanie otwarty nowy projekt w programie Visual Studio. Zarządzanie zatwierdzenia kodu w **Team Explorer** okna. Utwórz lokalną kopię nowy projekt, a następnie tworzenie aplikacji internetowej ASP.NET na podstawie szablonu w następujący sposób:

1. Wybierz **klonowania** do tworzenia lokalnego repozytorium usługi git projektu usługom DevOps platformy Azure.
    
    ![Sklonuj repozytorium z projektu usługom DevOps platformy Azure](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. W obszarze **rozwiązania**, wybierz opcję **New**.

    ![Utwórz rozwiązanie aplikacji sieci web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Wybierz **Web** szablonów, a następnie wybierz **aplikacji sieci Web ASP.NET** szablonu.
    1. Wprowadź nazwę aplikacji, taką jak *myWebApp*i usuń zaznaczenie pola dla **Utwórz katalog rozwiązania**.
    2. Jeśli ta opcja jest dostępna, usuń zaznaczenie pola wyboru do **Dodaj usługę Application Insights do projektu**. Usługi Application Insights wymaga autoryzować aplikację sieci web za pomocą usługi Azure Application Insights. W celu uproszczenia w tym samouczku, Pomiń ten proces.
    3. Kliknij przycisk **OK**.
4. Wybierz **MVC** z listy szablonów.
    1. Wybierz **Zmień uwierzytelnianie**, wybierz **bez uwierzytelniania**, a następnie wybierz **OK**.
    2. Wybierz **OK** do utworzenia rozwiązania.
5. W **Team Explorer** oknie Wybierz **zmiany**.

    ![Zatwierdź zmiany lokalne repozytorium Git repozytoriów platformy Azure](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. W polu tekstowym zatwierdzenia wprowadzać wiadomości, takie jak *początkowe zatwierdzenie*. Wybierz **zatwierdzić wszystkie i synchronizacji** z menu rozwijanego.


## <a name="create-build-pipeline"></a>Tworzenie potoku kompilacji
W usługach Azure DevOps Services umożliwia potoku kompilacji opisują, jak aplikacja powinny zostać skompilowane. W tym samouczku utworzymy podstawowy potok, który przyjmuje naszego kodu źródłowego, kompiluje rozwiązanie, a następnie tworzy sieci web wdrażanie pakietów, których używamy do uruchomienia aplikacji sieci web na serwerze IIS.

1. W projekcie usługom DevOps platformy Azure wybierz **kompilowanie i wydawanie** u góry, następnie wybierz pozycję **kompilacje**.
3. Wybierz **+ Nowa definicja**.
4. Wybierz **platformy ASP.NET (wersja ZAPOZNAWCZA)** szablonu, a następnie wybierz **Zastosuj**.
5. Pozostaw wszystkie domyślne wartości zadania. W obszarze **źródła**, upewnij się, że *myWebApp* repozytorium i *wzorca* gałęzi są zaznaczone.

    ![Tworzenie potoku kompilacji w projekcie usługom DevOps platformy Azure](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na **wyzwalaczy** kartę, przesuń suwak **Włącz ten wyzwalacz** do *włączone*.
7. Zapisz potoku kompilacji i kolejki nową kompilację, wybierając **Zapisz k & olejką** , następnie **Zapisz k & olejką** ponownie. Pozostaw wartości domyślne i wybierz pozycję **kolejki**.

Obejrzyj gdy kompilacja jest zaplanowana na hostowany agent następnie zaczyna kompilacji. Dane wyjściowe są podobne do poniższego przykładu:

![Pomyślnej kompilacji projektu usługom DevOps platformy Azure](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby zapewnić platformę do uruchomienia aplikacji sieci web platformy ASP.NET, należy maszynę wirtualną Windows, w którym działa program IIS. Usługi Azure DevOps używa agenta do interakcji z wystąpieniem usług IIS, jak zatwierdzenie kodu i kompilacje.

Tworzenie maszyny Wirtualnej 2016 serwera systemu Windows za pomocą [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* w *wschodnie stany USA* lokalizacji. Grupa zasobów *myResourceGroupAzureDevOpsServices* i pomocnicze zasoby sieci są również tworzone. Aby zezwolić na ruch w sieci web, TCP port *80* jest otwarty do maszyny Wirtualnej. Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które ma być używany jako poświadczenia logowania dla maszyny Wirtualnej:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
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

Aby nawiązać połączenie z maszyną Wirtualną, Uzyskaj publiczny adres IP z [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) w następujący sposób:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Tworzenie sesji usług pulpitu zdalnego z maszyną wirtualną:

```cmd
mstsc /v:<publicIpAddress>
```

Na maszynie Wirtualnej, należy otworzyć **administratora programu PowerShell** wiersza polecenia. Zainstaluj usługi IIS i wymagane funkcje platformy .NET:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Utwórz grupę wdrożenia

Gary'ego vaynerchucka pakiet narzędzia web deploy na serwerze usług IIS, zdefiniuj grupę wdrożenia w usługom DevOps platformy Azure. Ta grupa umożliwia określenie, serwerów, które są celem nowe kompilacje, jak Zatwierdź kod w usłudze usługom DevOps platformy Azure i wykonywane są kompilacje.

1. W usługach infrastruktury DevOps platformy Azure, wybierz **kompilowanie i wydawanie** , a następnie wybierz **grup wdrożeń**.
2. Wybierz **Dodaj wdrożenie grupy**.
3. Wprowadź nazwę grupy, taką jak *Mój_iis*, a następnie wybierz **Utwórz**.
4. W **rejestrowania maszyn** sekcji, upewnij się, *Windows* jest zaznaczone, a następnie zaznacz pole, aby **do uwierzytelniania można używać osobistego tokenu dostępu w skrypcie**.
5. Wybierz **Skopiuj skrypt do Schowka**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Dodawanie maszyny Wirtualnej usług IIS do grupy wdrożenia
Z utworzoną grupę wdrożenia należy dodać każde wystąpienie usługi IIS do grupy. Usługom DevOps platformy Azure generuje skrypt, który pobierze i skonfiguruje agenta na maszynie Wirtualnej, która odbiera nową sieć web wdrażania pakietów, a następnie stosuje je do usług IIS.

1. Ponownie **administratora programu PowerShell** sesji na maszynie Wirtualnej, Wklej i uruchom skrypt skopiowane z usługom DevOps platformy Azure.
2. Gdy zostanie wyświetlony monit, aby skonfigurować znaczniki dla agenta, wybierz pozycję *Y* i wprowadź *web*.
3. Po wyświetleniu monitu dla konta użytkownika, naciśnij klawisz *zwracają* aby zaakceptować wartości domyślne.
4. Poczekaj na zakończone z komunikatem o skryptu *vstsagent.account.computername usługa została uruchomiona pomyślnie*.
5. W **grup wdrożeń** strony **kompilowanie i wydawanie** menu Otwórz *Mój_iis* grupie wdrożenia. Na **maszyn** kartę, upewnij się, że Twoja maszyna wirtualna ma na liście.

    ![Maszyna wirtualna pomyślnie dodane do grupy wdrożenia usługom DevOps platformy Azure](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>Tworzenie potoku tworzenia wersji
Aby opublikować kompilacje, musisz utworzyć potok tworzenia wersji w usługom DevOps platformy Azure. Ten potok jest wyzwalana automatycznie przez pomyślnej kompilacji aplikacji. Wybierz grupę wdrożenia, aby wypchnąć sieci web wdrożenia pakietu i zdefiniować odpowiednie ustawienia usług IIS.

1. Wybierz **kompilowanie i wydawanie**, a następnie wybierz **kompilacje**. Wybierz potok kompilacji, utworzony w poprzednim kroku.
2. W obszarze **niedawno ukończone**, wybierz najnowszych kompilacji, a następnie wybierz **wersji**.
3. Wybierz **tak** do utworzenia potoku tworzenia wersji.
4. Wybierz **pusty** szablonu, następnie wybierz pozycję **dalej**.
5. Upewnij się, potok kompilacji projektu i źródła są wypełniane przy użyciu projektu.
6. Wybierz **ciągłe wdrażanie** pole wyboru, a następnie wybierz **Utwórz**.
7. Zaznacz pole listy rozwijanej obok **+ Dodaj zadania** i wybierz polecenie *Dodaj fazę grupy wdrożenia*.
    
    ![Dodaj zadanie do potoku w usłudze Azure DevOps Services wydania](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Wybierz **Dodaj** obok **Deploy(Preview) aplikacji sieci Web usług IIS**, a następnie wybierz **Zamknij**.
9. Wybierz **Uruchom na grupie wdrożenia** zadania nadrzędnego.
    1. Aby uzyskać **grupy wdrożenia**, wybierz grupę wdrożenia, został utworzony wcześniej, takich jak *Mój_iis*.
    2. W **Machine tagi** wybierz opcję **Dodaj** i wybierz polecenie *web* tagu.
    
    ![Wersja potoku wdrażania grupy zadań dla usług IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Wybierz **wdrażanie: Wdrażanie aplikacji sieci Web w usług IIS** zadanie, aby skonfigurować ustawienia wystąpienia usług IIS w następujący sposób:
    1. Aby uzyskać **nazwa witryny sieci Web**, wprowadź *domyślna witryna sieci Web*.
    2. Pozostaw inne ustawienia domyślne.
12. Wybierz **Zapisz**, a następnie wybierz **OK** dwa razy.


## <a name="create-a-release-and-publish"></a>Tworzenie wydania i publikowanie
Może teraz wypychać sieci web wdrożenia pakietu jako nową wersję. Ten krok, który komunikuje się z agenta na każdym wystąpieniu, która jest częścią grupy wdrożenia, wypycha sieci web wdrożenia pakietu, a następnie konfiguruje usługi IIS do uruchamiania aplikacji sieci web zaktualizowane.

1. W potoku wydania, należy wybrać **+ wydanie**, następnie wybierz **tworzenie wydania**.
2. Sprawdź czy najnowsza kompilacja jest zaznaczony na liście rozwijanej wraz z **automatycznego wdrożenia: po utworzeniu wersji**. Wybierz pozycję **Utwórz**.
3. Małe zostanie wyświetlony transparent w górnej części potok wydania, takie jak *wersji "Release-1" utworzył*. Wybierz link wydania.
4. Otwórz **dzienniki** kartę, aby obserwować postęp wydania.
    
    ![Udane wydanie usługom DevOps platformy Azure, jak i sieci web należy wdrożyć pakiet wypychania](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Po utworzeniu wydania, otwórz przeglądarkę internetową i wpisz publiczny adres OPS maszyny wirtualnej. Aplikacja sieci web platformy ASP.NET jest uruchomiona.

    ![Uruchomione na maszynie Wirtualnej usług IIS aplikacji internetowej platformy ASP.NET](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testowanie całego potoku ciągłej integracji/ciągłego wdrażania
W aplikacji sieci web uruchomionych w usługach IIS teraz spróbuj cały potok ciągłej integracji/ciągłego wdrażania. Po wprowadzeniu zmiany w Visual Studio i zatwierdzania wyzwolenia kodu, kompilacji, następnie wyzwalające wydania zaktualizowanych witrynę sieci web w usługach IIS należy wdrożyć pakiet:

1. W programie Visual Studio, otwórz **Eksploratora rozwiązań** okna.
2. Przejdź do, a następnie otwórz *myWebApp | Widoki | Strona główna | Index.cshtml*
3. Edytuj wiersz 6 w celu odczytu:

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. Zapisz plik.
5. Otwórz **Team Explorer** wybierz *myWebApp* projektu, a następnie wybierz **zmiany**.
6. Wprowadź wiadomość dotyczącą zatwierdzenia, taką jak *potoku CI/CD testowania*, następnie wybierz **Zatwierdź wszystko i synchronizacja** z menu rozwijanego.
7. W obszarze roboczym usługom DevOps platformy Azure nowa kompilacja jest wyzwalana od zatwierdzania kodu. 
    - Wybierz **kompilowanie i wydawanie**, a następnie wybierz **kompilacje**. 
    - Wybierz swój potok kompilacji, a następnie wybierz **kolejce & uruchomiony** kompilacji, aby obejrzeć w trakcie trwania kompilacji.
9. Gdy kompilacja zakończy się pomyślnie, zostanie wywołany nowej wersji.
    - Wybierz **kompilowanie i wydawanie**, następnie **wersji** aby zobaczyć wdrożenia pakietu wypchnięty do maszyny Wirtualnej usług IIS sieci web. 
    - Wybierz **Odśwież** ikonę, aby zaktualizować stan. Gdy *środowisk* kolumna zawiera zielony znacznik wyboru, wydanie pomyślnym wdrożeniu w usługach IIS.
11. Aby wyświetlić zmiany zastosowane, należy odświeżyć witryny sieci Web usług IIS w przeglądarce.

    ![Uruchamianie na maszynie Wirtualnej usług IIS z potoku ciągłej integracji/ciągłego wdrażania aplikacji sieci web ASP.NET](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzeniu aplikacji internetowej ASP.NET w usługach infrastruktury DevOps platformy Azure i skonfigurowaniu kompilacji i potoki wydań, aby wdrożyć nową sieć web wdrażanie pakietów usług IIS przy każdym zatwierdzeniu kodu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Publikowanie aplikacji sieci web ASP.NET do projektu usługom DevOps platformy Azure
> * Tworzenie potoku kompilacji, który jest wyzwalany przez kod zatwierdzenia
> * Instalowanie i konfigurowanie usług IIS na maszynie wirtualnej na platformie Azure
> * Dodaj wystąpienie usług IIS do grupy wdrożenia w usługom DevOps platformy Azure
> * Tworzenie potoku wydania w celu publikowania nowej sieci web wdrażanie pakietów usług IIS
> * Testowanie potoku ciągłej integracji/ciągłego wdrażania

Przejdź do następnego samouczka, aby dowiedzieć się, jak zainstalować SQL&#92;IIS&#92;stos technologii .NET przeznaczony na dwóch maszyn wirtualnych Windows.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET stack](tutorial-iis-sql.md)
