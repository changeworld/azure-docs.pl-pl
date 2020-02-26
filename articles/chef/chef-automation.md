---
title: Szybki Start — Konfigurowanie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Chef
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć i skonfigurować maszynę wirtualną z systemem Windows na platformie Azure przy użyciu programu Chef
keywords: Chef, Azure, DevOps, maszyna wirtualna
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590069"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Szybki Start — Konfigurowanie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu Chef

Chef umożliwia dostarczanie konfiguracji automatyzacji i żądanych stanów.

Najnowsza wersja interfejsu API chmury Chef zapewnia bezproblemową integrację z platformą Azure, umożliwiając obsługę i wdrażanie Stanów konfiguracji za pomocą jednego polecenia.

W tym artykule opisano Konfigurowanie środowiska Chef na potrzeby aprowizacji maszyn wirtualnych platformy Azure i instrukcje tworzenia zasad lub Cookbook, a następnie wdrażania tego Cookbook na maszynie wirtualnej platformy Azure.

## <a name="chef-basics"></a>Chef — podstawy

Przed rozpoczęciem pracy z tym artykułem [zapoznaj się z podstawowymi pojęciami](https://www.chef.io/chef)dotyczącymi Chef.

Na poniższym diagramie przedstawiono architekturę Chef wysokiego poziomu.

![Architektura Chef](media/chef-automation/chef-architecure.png)

Chef ma trzy główne składniki architektoniczne: 
- Serwer Chef — punkt zarządzania i dostępne są dwie opcje serwera Chef: rozwiązanie hostowane lub rozwiązanie lokalne.
- Klient Chef (węzeł) — Agent, który znajduje się na serwerach, którymi zarządzasz.
- Stacja robocza Chef — nazwa stacji roboczej administratora (tworzenia zasad i uruchamiania poleceń zarządzania) oraz pakietu oprogramowania narzędzi Chef.

Ogólnie rzecz biorąc, **stacja robocza** jest wyświetlana jako lokalizacja, w której uruchamiasz polecenia i **Chef stacji roboczej** dla pakietu oprogramowania.

Przykładowo można pobrać polecenie nóż jako część **stacji roboczej Chef**, ale można uruchomić polecenia nóż z **stacji roboczej** , aby zarządzać infrastrukturą.

Chef używa również koncepcji *Podręczniki* i *przepisów*. Te warunki są zasadami, które są zdefiniowane i stosowane odpowiednio do serwerów.

## <a name="preparing-your-workstation"></a>Przygotowywanie stacji roboczej

Najpierw Utwórz katalog do przechowywania plików konfiguracji Chef i podręczniki.

Utwórz katalog o nazwie `C:\Chef`.

Pobierz i zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na stacji roboczej.

## <a name="configure-azure-service-principal"></a>Konfigurowanie jednostki usługi platformy Azure

Będziemy używać nazwy głównej usługi, aby pomóc nam w tworzeniu zasobów platformy Azure z naszej stacji roboczej Chef.  Aby utworzyć odpowiednią jednostkę usługi z wymaganymi uprawnieniami, uruchom następujące polecenia w programie PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Zwróć uwagę na to, jak te wartości będą potrzebne w ramach identyfikatora subskrypcji, TenantID, ClientID i tajnego klienta (hasło ustawione wcześniej w tym samouczku). 

## <a name="setup-chef-server"></a>Skonfiguruj serwer Chef

W tym przewodniku przyjęto założenie, że rejestrujesz się w usłudze hostowanej Chef.

Jeśli nie korzystasz jeszcze z serwera Chef, możesz:

* Zarejestruj się w usłudze [hostowanej Chef](https://manage.chef.io/signup), która jest najszybszym sposobem na rozpoczęcie pracy z usługą Chef.
* Zainstaluj autonomiczny serwer Chef na komputerze z systemem Linux, postępując zgodnie z [instrukcjami dotyczącymi instalacji](https://docs.chef.io/install_server.html) zawartymi w dokumentacji [Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Tworzenie konta hostowanej Chef

Utwórz konto hostowanej usługi Chef [tutaj](https://manage.chef.io/signup).

Podczas procesu rejestracji zostanie wyświetlony monit o utworzenie nowej organizacji.

![Utwórz okno organizacji](media/chef-automation/create-organization.png)

Po utworzeniu organizacji Pobierz zestaw startowy.

![Konfigurowanie Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Jeśli zostanie wyświetlony monit z ostrzeżeniem, że Twoje klucze zostaną zresetowane, nie będzie to możliwe, ponieważ nie skonfigurowano jeszcze istniejącej infrastruktury.
>

Ten plik zip zestawu startowego zawiera pliki konfiguracji organizacji i klucz użytkownika w katalogu `.chef`.

`organization-validator.pem` należy pobrać oddzielnie, ponieważ jest kluczem prywatnym, a klucze prywatne nie powinny być przechowywane na serwerze Chef. W obszarze [Zarządzanie Chef](https://manage.chef.io/)przejdź do sekcji Administracja, a następnie wybierz pozycję "Resetuj klucz weryfikacji", która udostępnia plik do pobrania osobno. Zapisz plik w c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurowanie stacji roboczej Chef

Wyodrębnij zawartość `chef-starter.zip`, aby `c:\chef`.

Skopiuj wszystkie pliki w obszarze `chef-starter\chef-repo\.chef` do katalogu `c:\chef`.

Skopiuj plik `organization-validator.pem` do `c:\chef`, jeśli zostanie on zapisany w `c:\Downloads`.

Katalog powinien teraz wyglądać podobnie do poniższego przykładu.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Teraz należy mieć pięć plików i cztery katalogi (w tym pusty katalog Chef-repozytorium) w katalogu głównym c:\chef.

### <a name="edit-kniferb"></a>Edytowanie nóż. RB

Pliki PEM zawierają Twoją organizację i administracyjne klucze prywatne do komunikacji, a plik nóż. RB zawiera konfigurację programu nóż. Będziemy musieli edytować plik nóż. RB.

Otwórz plik nóż. RB w wybranym edytorze. Niezmienionego pliku powinien wyglądać następująco:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Dodaj następujące informacje do obiektu nóż. RB, zastępując symbole zastępcze informacjami:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Te linie zapewniają, że nóż odwołuje się do katalogu Podręczniki w obszarze `c:\chef\cookbooks`.

Plik `knife.rb` powinien teraz wyglądać podobnie do poniższego przykładu:

![Przykład pliku nóż](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Zainstaluj stację roboczą Chef

Następnie [Pobierz i zainstaluj stację roboczą Chef](https://downloads.chef.io/chef-workstation/).

Zainstaluj stację roboczą Chef w domyślnej lokalizacji.

Na pulpicie zobaczysz wartość CW PowerShell. To narzędzie jest używane do współpracy z produktami Chef. W przypadku programu PowerShell dostępne są nowe polecenia, takie jak `chef-run` i poleceń interfejsu wiersza polecenia Chef (na przykład `chef`). Zapoznaj się z zainstalowaną wersją stacji roboczej Chef i narzędziami Chef z `chef -v`. Możesz również sprawdzić wersję stacji roboczej, wybierając pozycję **Informacje o stacji roboczej Chef** z aplikacji stacji roboczej Chef.

`chef --version` powinien zwrócić podobny element:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Kolejność ścieżki jest ważna. Jeśli ścieżki Opscode nie są w poprawnej kolejności, pojawią się problemy.
>

Przed kontynuowaniem Uruchom ponownie stację roboczą.

### <a name="install-knife-azure"></a>Zainstaluj program nóż Azure

W tym samouczku założono, że używasz Azure Resource Manager do współpracy z maszyną wirtualną.

Zainstaluj rozszerzenie nóż platformy Azure, które obejmuje wtyczkę platformy Azure.

Uruchom następujące polecenie.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument `–-pre` zapewnia otrzymywanie najnowszej wersji RC wtyczki "nóż platformy Azure", która zapewnia dostęp do najnowszego zestawu interfejsów API.
>
>

Prawdopodobnie zostanie również zainstalowana wiele zależności w tym samym czasie.

![Dane wyjściowe instalacji nóż — Azure](./media/chef-automation/install-knife-azure.png)

Aby upewnić się, że wszystko jest prawidłowo skonfigurowane, uruchom następujące polecenie.

    knife azurerm server list

Jeśli wszystko jest prawidłowo skonfigurowane, zostanie wyświetlona lista dostępnych obrazów platformy Azure.

Gratulacje. Stacja robocza została skonfigurowana!

## <a name="creating-a-cookbook"></a>Tworzenie Cookbook

Cookbook jest używany przez Chef do definiowania zestawu poleceń, które mają być uruchamiane na zarządzanym kliencie. Tworzenie Cookbook jest proste, po prostu Użyj polecenia `chef generate cookbook`, aby wygenerować szablon Cookbook. Ten Cookbook dotyczy serwera sieci Web, który automatycznie wdraża usługi IIS.

W `C:\Chef directory`Uruchom następujące polecenie.

    chef generate cookbook webserver

To polecenie generuje zestaw plików w katalogu C:\Chef\cookbooks\webserver. Następnie zdefiniuj zestaw poleceń dla klienta Chef do uruchamiania na zarządzanej maszynie wirtualnej.

Polecenia są przechowywane w pliku default. RB. W tym pliku Zdefiniuj zestaw poleceń, które instalują usługi IIS, uruchamia usługi IIS i kopiuje plik szablonu do folderu `wwwroot`.

Zmodyfikuj plik C:\chef\cookbooks\webserver\recipes\default.RB i Dodaj następujące wiersze.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Zapisz plik po zakończeniu.

## <a name="creating-a-template"></a>Tworzenie szablonu

W tym kroku wygenerujesz plik szablonu do użycia jako strona `default.html`.

Uruchom następujące polecenie, aby wygenerować szablon:

    chef generate template webserver Default.htm

Przejdź do pliku `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Edytuj plik, dodając prosty *Hello World* kod HTML, a następnie Zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Przekaż Cookbook do serwera Chef

W tym kroku utworzysz kopię Cookbook utworzoną na komputerze lokalnym i przekażesz ją do hostowanego serwera Chef. Po przekazaniu Cookbook pojawia się na karcie **zasady** .

    knife cookbook upload webserver

![Wyniki instalacji Cookbook na serwerze Chef](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej przy użyciu programu nóż platformy Azure

Wdróż maszynę wirtualną platformy Azure i Zastosuj `Webserver` Cookbook przy użyciu polecenia `knife`.

`knife` polecenie również zainstaluje usługę sieci Web IIS i domyślną stronę sieci Web.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

Przykład polecenia `knife` tworzy *Standard_DS2_v2* maszynę wirtualną z systemem Windows Server 2016 zainstalowanym w regionie zachodnie stany USA. Należy zmodyfikować te wartości na potrzeby aplikacji.

Po uruchomieniu polecenia przejdź do Azure Portal, aby zobaczyć, że komputer rozpoczął udostępnianie.

![Maszyna wirtualna jest obsługiwana](./media/chef-automation/virtual-machine-being-provisioned.png)

Zostanie wyświetlony wiersz polecenia dalej.

![Zanóż dane wyjściowe podczas tworzenia maszyny wirtualnej](./media/chef-automation/knife-output-when-creating-vm.png)

Po zakończeniu wdrażania zostanie wyświetlony publiczny adres IP nowej maszyny wirtualnej. Wklej tę wartość do przeglądarki sieci Web, aby wyświetlić nową witrynę sieci Web. Po wdrożeniu maszyny wirtualnej została otwarta wartość port 80, dlatego powinna być dostępna zewnętrznie.   

![Testowanie maszyny wirtualnej](./media/chef-automation/testing-the-virtual-machine.png)

Ten przykład używa Creative kod HTML.

Możesz również wyświetlić stan węzła [Chef zarządzanie](https://manage.chef.io/). 

![Wyświetlanie stanu węzła](./media/chef-automation/viewing-node-status.png)

Nie zapomnij, że można także nawiązać połączenie za pośrednictwem sesji RDP z Azure Portal za pośrednictwem portu 3389.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Chef na platformie Azure](/azure/chef/)