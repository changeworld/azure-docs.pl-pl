---
title: Wdrażanie maszyny wirtualnej platformy Azure za pomocą usługi Chef
description: Dowiedz się, jak używać Chef do automatycznego wdrażania i konfigurowania maszyn wirtualnych na Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 58642cdbf164523390d5e4925290b43f6c05549b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039544"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatyzowanie wdrażania maszyny wirtualnej platformy Azure przy użyciu narzędzia Chef

Chef to doskonałe narzędzie do dostarczania konfiguracji automatyzacji i żądanych stanów.

Najnowsza wersja interfejsu API chmury Chef zapewnia bezproblemową integrację z platformą Azure, umożliwiając obsługę i wdrażanie Stanów konfiguracji za pomocą jednego polecenia.

W tym artykule opisano Konfigurowanie środowiska Chef na potrzeby aprowizacji maszyn wirtualnych platformy Azure i instrukcje tworzenia zasad lub "Cookbook", a następnie wdrażania tego Cookbook na maszynie wirtualnej platformy Azure.

## <a name="chef-basics"></a>Chef — podstawy
Przed rozpoczęciem [zapoznaj się z podstawowymi pojęciami](https://www.chef.io/chef)dotyczącymi Chef.

Na poniższym diagramie przedstawiono architekturę Chef wysokiego poziomu.

![][2]

Chef ma trzy główne składniki architektoniczne: Chef Server, Chef Client (Node) i Chef Workstation.

Serwer Chef jest punktem zarządzania i dostępne są dwie opcje serwera Chef: rozwiązanie hostowane lub rozwiązanie lokalne.

Klient Chef (węzeł) to Agent, który znajduje się na serwerach, którymi zarządzasz.

Stacja robocza Chef, która jest nazwą zarówno administracyjnej stacji roboczej, w której tworzysz zasady, jak i poleceniem wykonywania poleceń zarządzania oraz pakiet oprogramowania narzędzi Chef.

Ogólnie rzecz biorąc, _stacja robocza_ będzie widoczna jako lokalizacja, w której wykonujesz akcje i _Chef stacji roboczej_ dla pakietu oprogramowania.
Przykładowo pobierzesz polecenie nóż jako część _stacji roboczej Chef_, ale uruchamiasz polecenia nóż z _stacji roboczej_ , aby zarządzać infrastrukturą.

Chef używa również koncepcji "Podręczniki" i "przepisów", które są efektywnie zdefiniowanymi zasadami, które są stosowane do serwerów.

## <a name="preparing-your-workstation"></a>Przygotowywanie stacji roboczej

Najpierw Utwórz katalog do przechowywania plików konfiguracji Chef i podręczniki.

Utwórz katalog o nazwie C:\Chef.

Pobierz i zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na stacji roboczej.

## <a name="configure-azure-service-principal"></a>Konfigurowanie jednostki usługi platformy Azure

W najprostszej terminologii, a nazwa główna usługi platformy Azure to konto usługi.   Będziemy używać nazwy głównej usługi, aby pomóc nam w tworzeniu zasobów platformy Azure z naszej stacji roboczej Chef.  Aby utworzyć odpowiednią jednostkę usługi z wymaganymi uprawnieniami, należy uruchomić następujące polecenia w programie PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Zanotuj swój identyfikator subskrypcji, TenantID, ClientID i klucz tajny klienta (hasło ustawione powyżej). będzie on potrzebny później. 

## <a name="setup-chef-server"></a>Skonfiguruj serwer Chef

W tym przewodniku przyjęto założenie, że zarejestrujesz się w usłudze hostowanej Chef.

Jeśli nie korzystasz jeszcze z serwera Chef, możesz:

* Zarejestruj się w usłudze [hostowanej Chef](https://manage.chef.io/signup), która jest najszybszym sposobem na rozpoczęcie pracy z usługą Chef.
* Zainstaluj autonomiczny serwer Chef na komputerze z systemem Linux, postępując zgodnie z [instrukcjami dotyczącymi instalacji](https://docs.chef.io/install_server.html) zawartymi w dokumentacji [Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Tworzenie konta hostowanej Chef

Utwórz konto hostowanej usługi Chef [tutaj](https://manage.chef.io/signup).

Podczas procesu rejestracji zostanie wyświetlony monit o utworzenie nowej organizacji.

![][3]

Po utworzeniu organizacji Pobierz zestaw startowy.

![][4]

> [!NOTE]
> Jeśli zostanie wyświetlony monit z ostrzeżeniem, że Twoje klucze zostaną zresetowane, nie będzie to możliwe, ponieważ nie skonfigurowano jeszcze istniejącej infrastruktury.
>

Ten plik zip zestawu startowego zawiera pliki konfiguracji organizacji i klucz użytkownika w katalogu `.chef`.

`organization-validator.pem` należy pobrać oddzielnie, ponieważ jest kluczem prywatnym, a klucze prywatne nie powinny być przechowywane na serwerze Chef. W obszarze [Zarządzanie Chef](https://manage.chef.io/)przejdź do sekcji Administracja i wybierz pozycję "Resetuj klucz weryfikacji", która udostępnia plik do pobrania osobno. Zapisz plik w c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurowanie stacji roboczej Chef

Wyodrębnij zawartość pliku Chef-Starter. zip do c:\chef.

Skopiuj wszystkie pliki w obszarze Chef-starter\chef-repo\.Chef do katalogu c:\chef.

Skopiuj plik `organization-validator.pem` do c:\chef, jeśli jest on zapisany w c:\Downloads

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

Dodaj następujące informacje do Twojego nóż. RB:

validation_client_name "MyOrg-Walidator"

validation_key "# {current_dir}/MyOrg.pem"

Nóż [: azure_tenant_id] = "0000000-1111-AAAA-bbbb-222222222222"

Nóż [: azure_subscription_id] = "11111111-BBBBB-CCCC-1111-222222222222"

Nóż [: azure_client_id] = "11111111-BBBBB-CCCC-1111-2222222222222"

Nóż [: azure_client_secret] = "#1234p $wdchef 19"


Te linie zapewniają, że nóż odwołuje się do katalogu Podręczniki w obszarze c:\chef\cookbooks, a także używa jednostki usługi platformy Azure utworzonej podczas operacji platformy Azure.

Plik nóż. RB powinien teraz wyglądać podobnie do poniższego przykładu:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

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

Następnie [Pobierz i zainstaluj](https://downloads.chef.io/chef-workstation/) stację roboczą Chef.
Zainstaluj stację roboczą Chef w domyślnej lokalizacji. Ta instalacja może potrwać kilka minut.

Na pulpicie zobaczysz "CW PowerShell", czyli środowisko załadowane za pomocą narzędzia potrzebnego do współdziałania z produktami Chef. W przypadku programu PowerShell dostępne są nowe polecenia ad hoc, takie jak `chef-run`, a także tradycyjne polecenia interfejsu CLI Chef, takie jak `chef`. Zapoznaj się z zainstalowaną wersją stacji roboczej Chef i narzędziami Chef z `chef -v`. Możesz również sprawdzić wersję stacji roboczej, wybierając pozycję "informacje o Chef stacji roboczej" z aplikacji stacji roboczej Chef.

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
> Kolejność ścieżki jest ważna. Jeśli ścieżki Opscode nie znajdują się w poprawnej kolejności, wystąpią problemy.
>

Przed kontynuowaniem Uruchom ponownie stację roboczą.

### <a name="install-knife-azure"></a>Zainstaluj program nóż Azure

W tym samouczku założono, że używasz Azure Resource Manager do współpracy z maszyną wirtualną.

Zainstaluj rozszerzenie nóż platformy Azure. Zapewnia to nóż przy użyciu "wtyczki platformy Azure".

Uruchom następujące polecenie.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument – pre zapewnia otrzymywanie najnowszej wersji RC wtyczki programu nóż platformy Azure, która zapewnia dostęp do najnowszego zestawu interfejsów API.
>
>

Prawdopodobnie zostanie również zainstalowana wiele zależności w tym samym czasie.

![][8]

Aby upewnić się, że wszystko jest prawidłowo skonfigurowane, uruchom następujące polecenie.

    knife azurerm server list

Jeśli wszystko jest prawidłowo skonfigurowane, zostanie wyświetlona lista dostępnych obrazów platformy Azure.

Gratulacje. Stacja robocza została skonfigurowana!

## <a name="creating-a-cookbook"></a>Tworzenie Cookbook

Cookbook jest używany przez Chef do definiowania zestawu poleceń, które mają zostać wykonane na zarządzanym kliencie. Tworzenie Cookbook jest proste, po prostu Użyj polecenia **Chef Generate Cookbook** , aby wygenerować szablon Cookbook. Ten Cookbook dotyczy serwera sieci Web, który automatycznie wdraża usługi IIS.

W katalogu C:\Chef Uruchom następujące polecenie.

    chef generate cookbook webserver

To polecenie generuje zestaw plików w katalogu C:\Chef\cookbooks\webserver. Następnie zdefiniuj zestaw poleceń dla klienta Chef do wykonania na zarządzanej maszynie wirtualnej.

Polecenia są przechowywane w pliku default. RB. W tym pliku Zdefiniuj zestaw poleceń, które instalują usługi IIS, uruchamia usługi IIS i kopiuje plik szablonu do folderu wwwroot.

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
W tym kroku zostanie wygenerowany plik szablonu, który będzie używany jako domyślna strona HTML.

Uruchom następujące polecenie, aby wygenerować szablon:

    chef generate template webserver Default.htm

Przejdź do pliku `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Edytuj plik, dodając prosty kod HTML "Hello world", a następnie Zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Przekaż Cookbook do serwera Chef
W tym kroku utworzysz kopię Cookbook utworzoną na komputerze lokalnym i przekażesz ją do hostowanego serwera Chef. Po przekazaniu Cookbook pojawia się na karcie **zasady** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej przy użyciu programu nóż platformy Azure
Wdróż maszynę wirtualną platformy Azure i Zastosuj Cookbook "WebServer", który zainstaluje usługę sieci Web usług IIS i domyślną stronę sieci Web.

Aby to zrobić, użyj polecenia **nóż azurerm Server Create** .

Przykład polecenia pojawia się dalej.

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


W powyższym przykładzie zostanie utworzona maszyna wirtualna Standard_DS2_v2 z systemem Windows Server 2016 zainstalowanym w regionie zachodnie stany USA. Zastąp określone zmienne i uruchom je.

> [!NOTE]
> Za pomocą wiersza polecenia mam również zautomatyzować reguły filtru sieci punktu końcowego przy użyciu parametru – TCP-Endpoints. Otwarto porty 80 i 3389 w celu zapewnienia dostępu do strony sieci Web i sesji RDP.
>
>

Po uruchomieniu polecenia przejdź do Azure Portal, aby zobaczyć, że komputer rozpoczął udostępnianie.

![][15]

Zostanie wyświetlony wiersz polecenia dalej.

![][16]

Po zakończeniu wdrażania, publiczny adres IP nowej maszyny wirtualnej będzie wyświetlany po zakończeniu wdrażania, można skopiować ten plik i wkleić go do przeglądarki internetowej i wyświetlić wdrożoną witrynę sieci Web. Po wdrożeniu maszyny wirtualnej, która otworzyła port 80, dlatego powinna być dostępna zewnętrznie.   

![][11]

Ten przykład używa Creative kod HTML.

Możesz również wyświetlić stan węzła [Chef zarządzanie](https://manage.chef.io/). 

![][17]

Nie zapomnij, że można także nawiązać połączenie za pośrednictwem sesji RDP z Azure Portal za pośrednictwem portu 3389.

Dziękujemy! Zacznij pracę z infrastrukturą jako z platformą Azure już dzisiaj!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
