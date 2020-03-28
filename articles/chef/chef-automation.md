---
title: Szybki start — konfigurowanie maszyny wirtualnej systemu Windows na platformie Azure przy użyciu programu Chef
description: W tym przewodniku Szybki start dowiesz się, jak wdrożyć i skonfigurować maszynę wirtualną systemu Windows na platformie Azure za pomocą programu Chef
keywords: szef kuchni, lazur, devops, maszyna wirtualna
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77590069"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Szybki start — konfigurowanie maszyny wirtualnej systemu Windows na platformie Azure przy użyciu programu Chef

Program Chef umożliwia dostarczanie automatyzacji i konfiguracji żądanego stanu.

Dzięki najnowszej wersji interfejsu API w chmurze program Chef zapewnia bezproblemową integrację z platformą Azure, zapewniając możliwość aprowizowania i wdrażania stanów konfiguracji za pomocą jednego polecenia.

W tym artykule skonfigurowano środowisko szefa kuchni, aby aprowizować maszyny wirtualne platformy Azure i przechodzić przez tworzenie zasad lub książki kucharskiej, a następnie wdrażając tę książkę kucharską na maszynie wirtualnej platformy Azure.

## <a name="chef-basics"></a>Podstawy szefa kuchni

Zanim zaczniesz od tego artykułu, [zapoznaj się z podstawowymi pojęciami programu Chef](https://www.chef.io/chef).

Na poniższym diagramie przedstawiono architekturę wysokiego poziomu chef.

![Architektura szefa kuchni](media/chef-automation/chef-architecure.png)

Szef kuchni posiada trzy główne elementy architektoniczne: 
- Chef Server — punkt zarządzania i istnieją dwie opcje dla chef server: hostowane rozwiązanie lub rozwiązanie lokalne.
- Klient szefa kuchni (węzeł) — agent, który znajduje się na serwerach, którymi zarządzasz.
- Chef Workstation — nazwa zarówno stacji roboczej administratora (w której są tworzyć zasady i uruchamiać polecenia zarządzania), jak i pakietu oprogramowania narzędzi chefa.

Ogólnie rzecz biorąc, **stacja robocza** jest widoczna jako lokalizacja, w której uruchamiane są polecenia, a **stacja robocza szefa kuchni** dla pakietu oprogramowania.

Na przykład należy pobrać polecenie noża jako część **stacji roboczej szefa kuchni,** ale uruchamiasz polecenia noża ze **stacji roboczej** w celu zarządzania infrastrukturą.

Szef kuchni używa również koncepcji *książek kucharskich* i *przepisów kulinarnych.* Te warunki są zasady, które są zdefiniowane i stosowane do serwerów, odpowiednio.

## <a name="preparing-your-workstation"></a>Przygotowanie stacji roboczej

Najpierw przygotuj stację roboczą, tworząc katalog do przechowywania plików konfiguracyjnych i książek kucharskich chefa.

Utwórz katalog o nazwie `C:\Chef`.

Pobierz i zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na stacji roboczej.

## <a name="configure-azure-service-principal"></a>Konfigurowanie jednostki usługi platformy Azure

Będziemy używać jednostki usługi, aby pomóc nam tworzyć zasoby platformy Azure z naszej stacji roboczej szefa kuchni.  Aby utworzyć odpowiednią jednostkę usługi z wymaganymi uprawnieniami, uruchom następujące polecenia w programie PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Zanotuj identyfikator subskrypcji, identyfikator dzierżawy, identyfikator klienta i klucz tajny klienta (hasło ustawione wcześniej w tym samouczku), ponieważ będą potrzebne te wartości. 

## <a name="setup-chef-server"></a>Serwer kucharza instalatora

W tym przewodniku założono, że zarejestrujesz się w Hosted Chef.

Jeśli nie korzystasz jeszcze z serwera szefa kuchni, możesz:

* Zarejestruj się w [Hosted Chef](https://manage.chef.io/signup), który jest najszybszym sposobem, aby rozpocząć pracę z Chef.
* Zainstaluj samodzielny serwer Chef Server na komputerze z systemem Linux, postępując zgodnie z [instrukcjami instalacji](https://docs.chef.io/install_server.html) z [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Tworzenie konta hostowanego szefa kuchni

Zarejestruj się na konto Hosted Chef [tutaj](https://manage.chef.io/signup).

Podczas procesu rejestracji zostaniesz poproszony o utworzenie nowej organizacji.

![Tworzenie okna organizacji](media/chef-automation/create-organization.png)

Po utworzeniu organizacji pobierz zestaw startowy.

![Konfigurowanie szefa kuchni](media/chef-automation/configure-chef.png)

> [!NOTE]
> Jeśli pojawi się monit z ostrzeżeniem, że klucze zostaną zresetowane, jest w porządku, aby kontynuować, ponieważ nie mamy jeszcze skonfigurowanej istniejącej infrastruktury.
>

Ten plik zip zestawu startowego zawiera pliki `.chef` konfiguracji organizacji i klucz użytkownika w katalogu.

Musi `organization-validator.pem` być pobrany oddzielnie, ponieważ jest to klucz prywatny i klucze prywatne nie powinny być przechowywane na serwerze chef. Z [programu Chef Manage](https://manage.chef.io/)przejdź do sekcji Administracja i wybierz "Resetuj klucz sprawdzania poprawności", który zawiera plik do pobrania oddzielnie. Zapisz plik w c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurowanie stacji roboczej szefa kuchni

Wyodrębnić zawartość `chef-starter.zip` do `c:\chef`.

Skopiuj wszystkie pliki pod `chef-starter\chef-repo\.chef` katalog. `c:\chef`

Skopiuj `organization-validator.pem` plik do `c:\chef`, `c:\Downloads`jeśli jest zapisany w pliku .

Katalog powinien teraz wyglądać mniej więcej w poniższym przykładzie.

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

Teraz powinieneś mieć pięć plików i cztery katalogi (w tym pusty katalog chef-repo) w katalogu głównym c:\chef.

### <a name="edit-kniferb"></a>Edycja noża.rb

Pliki PEM zawierają organizacyjne i administracyjne klucze prywatne do komunikacji, a plik knife.rb zawiera konfigurację noża. Będziemy musieli edytować plik knife.rb.

Otwórz plik knife.rb w wybranym edytorze. Niezrealisowany plik powinien wyglądać mniej więcej tak:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Dodaj następujące informacje do strony knife.rb, zastępując symbole zastępcze informacjami:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Linie te zapewnią, że Knife odwołuje się `c:\chef\cookbooks`do katalogu książek kucharskich w obszarze .

Plik `knife.rb` powinien teraz wyglądać podobnie do następującego przykładu:

![Przykład pliku noża](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Instalowanie stacji roboczej szefa kuchni

Następnie [pobierz i zainstaluj stację roboczą Chef](https://downloads.chef.io/chef-workstation/).

Zainstaluj stację roboczą programu Chef w lokalizacji domyślnej.

Na pulpicie zobaczysz program CW PowerShell. To narzędzie służy do interakcji z produktami chef. CW PowerShell udostępnia nowe polecenia, `chef-run` takie jak polecenia chef `chef`cli (takie jak ). Zobacz zainstalowaną wersję chef workstation i `chef -v`narzędzia Chef z . Wersję stacji roboczej można również sprawdzić, wybierając pozycję **Informacje o stacji roboczej szefa kuchni** w aplikacji Chef Workstation.

`chef --version`powinien zwrócić coś takiego:

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
> Kolejność ścieżki jest ważna! Jeśli ścieżki opscode nie są w odpowiedniej kolejności, problemy będą powodować.
>

Przed kontynuowaniem uruchom ponownie stację roboczą.

### <a name="install-knife-azure"></a>Instalowanie usługi Knife Azure

W tym samouczku przyjęto założenie, że używasz usługi Azure Resource Manager do interakcji z maszyną wirtualną.

Zainstaluj rozszerzenie Knife Azure, które zawiera wtyczkę platformy Azure.

Uruchom następujące polecenie.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument `–-pre` zapewnia, że otrzymujesz najnowszą wersję RC wtyczki Platformy Azure, która zapewnia dostęp do najnowszego zestawu interfejsów API.
>
>

Jest prawdopodobne, że wiele zależności zostanie również zainstalowany w tym samym czasie.

![Wyjście z instalacji noża-azure](./media/chef-automation/install-knife-azure.png)

Aby upewnić się, że wszystko jest poprawnie skonfigurowane, uruchom następujące polecenie.

    knife azurerm server list

Jeśli wszystko jest poprawnie skonfigurowane, zostanie wyświetlona lista dostępnych obrazów platformy Azure przewijania.

Gratulacje. Twoja stacja robocza jest skonfigurowana!

## <a name="creating-a-cookbook"></a>Tworzenie książki kucharskiej

Książka kucharska jest używana przez szefa kuchni do definiowania zestawu poleceń, które mają być uruchamiane na zarządzanym kliencie. Tworzenie książki kucharskiej jest proste, `chef generate cookbook` wystarczy użyć polecenia, aby wygenerować szablon książki kucharskiej. Ta książka kucharska jest dla serwera sieci web, który automatycznie wdraża usługi IIS.

W `C:\Chef directory`obszarze , uruchom następujące polecenie.

    chef generate cookbook webserver

To polecenie generuje zestaw plików w katalogu C:\Chef\cookbooks\webserver. Następnie zdefiniuj zestaw poleceń dla klienta chef do uruchomienia na zarządzanej maszynie wirtualnej.

Polecenia są przechowywane w pliku default.rb. W tym pliku zdefiniuj zestaw poleceń, które instalują usługi IIS, `wwwroot` uruchamiają usługi IIS i kopiują plik szablonu do folderu.

Zmodyfikuj plik C:\chef\cookbooks\webserver\recipes\default.rb i dodaj następujące wiersze.

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

W tym kroku wygenerujesz plik szablonu `default.html` do użycia jako strona.

Uruchom następujące polecenie, aby wygenerować szablon:

    chef generate template webserver Default.htm

Przejdź do `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` pliku. Edytuj plik, dodając prosty kod HTML *Hello World,* a następnie zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Prześlij książkę kucharską na serwer chefa

W tym kroku należy utworzyć kopię książki kucharskiej utworzonej na komputerze lokalnym i przesłać ją do serwera hostowanego przez szefa kuchni. Po przesłaniu książka kucharska pojawi się na karcie **Zasady.**

    knife cookbook upload webserver

![Wyniki instalacji książki kucharskiej na chef server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej za pomocą platformy Knife Azure

Wdrażanie maszyny wirtualnej `Webserver` platformy Azure `knife` i stosowanie książki kucharskiej za pomocą polecenia.

Polecenie `knife` zainstaluje również usługę sieci Web usług IIS i domyślną stronę sieci Web.

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

Przykład `knife` polecenia tworzy *Standard_DS2_v2* maszynę wirtualną z zainstalowanym systemem Windows Server 2016 w regionie Zachodnie stany USA. Zmodyfikuj te wartości zgodnie z potrzebami aplikacji.

Po uruchomieniu polecenia przejdź do witryny Azure portal, aby wyświetlić komputer rozpocząć aprowizowania.

![Maszyna wirtualna jest aprowizowana](./media/chef-automation/virtual-machine-being-provisioned.png)

Wiersz polecenia pojawi się dalej.

![Wyjście noża podczas tworzenia maszyny wirtualnej](./media/chef-automation/knife-output-when-creating-vm.png)

Po zakończeniu wdrażania wyświetlany jest publiczny adres IP nowej maszyny wirtualnej. Wklej tę wartość do przeglądarki internetowej, aby wyświetlić nową witrynę sieci Web. Po wdrożeniu maszyny wirtualnej otworzyliśmy port 80, dzięki czemu powinien być dostępny zewnętrznie.   

![Testowanie maszyny wirtualnej](./media/chef-automation/testing-the-virtual-machine.png)

W tym przykładzie użyto kreatywnego kodu HTML.

Można również wyświetlić stan węzła [Chef Manage](https://manage.chef.io/). 

![Wyświetlanie stanu węzła](./media/chef-automation/viewing-node-status.png)

Nie zapomnij również, że możesz połączyć się za pośrednictwem sesji RDP z witryny Azure portal za pośrednictwem portu 3389.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Program Chef na platformie Azure](/azure/chef/)