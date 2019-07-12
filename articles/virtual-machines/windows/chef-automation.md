---
title: Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu Chef | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Chef do wdrażania automatycznego maszyny wirtualnej i konfiguracji w systemie Microsoft Azure
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
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719278"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatyzowanie wdrażania maszyny wirtualnej platformy Azure przy użyciu narzędzia Chef

Chef to doskonałe narzędzie do dostarczania automatyzacji i żądanego stanu konfiguracji.

Najnowsza wersja interfejsu API w chmurze Chef zapewnia bezproblemową integrację z platformą Azure, dzięki czemu możesz aprowizować i wdrażać stanami konfiguracji za pomocą jednego polecenia.

Ten artykuł służy do konfigurowania środowiska Chef do udostępniania maszyn wirtualnych platformy Azure i poznać dokładnie proces tworzenia zasad lub "Cookbook", a następnie wdrożenie ten podręcznik na maszynie wirtualnej platformy Azure.

## <a name="chef-basics"></a>Podstawy programu chef
Przed przystąpieniem do wykonywania [zapoznaj się z podstawowymi informacjami dotyczącymi Chef](https://www.chef.io/chef).

Poniższy diagram przedstawia architekturę wysokiego poziomu Chef.

![][2]

Program chef ma trzy główne składniki architektury: Serwer programu chef, Chef klienta (node) i stacji roboczej programu Chef.

Serwer programu Chef punkt zarządzania i dostępne są dwie opcje dla serwera programu Chef: hostowanego rozwiązania lub rozwiązaniem w firmie.

Klient programu Chef (node) jest agent, który znajduje się na serwerach, którymi zarządzasz.

Program Chef stacji roboczej, która jest nazwą zarówno przez administratora stacji roboczej, gdzie tworzenie zasad i wykonywania poleceń zarządzania i pakiet oprogramowania, narzędzi programu Chef.

Ogólnie rzecz biorąc, zobaczysz _stację roboczą_ jako lokalizacja, w którym należy wykonać czynności i _stacji roboczej Chef_ pakietu oprogramowania.
Na przykład, konieczne będzie pobranie polecenia nóż jako część _stacji roboczej programu Chef_, ale polecenia nóż z _stację roboczą_ zarządzania infrastrukturą.

Program chef korzysta z koncepcji "Podręczniki" i "Przepisów", które skutecznie są zasady, możemy zdefiniować i zastosować do serwerów.

## <a name="preparing-your-workstation"></a>Przygotowanie stacji roboczej

Po pierwsze przeznaczonego do przygotowania stację roboczą, tworząc katalog do przechowywania plików konfiguracyjnych programu Chef i podręczniki.

Utwórz katalog o nazwie C:\Chef.

Pobierz i zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wersji do stacji roboczej.

## <a name="configure-azure-service-principal"></a>Konfigurowanie jednostki usługi platformy Azure

W najprostszej warunków i jednostki usługi platformy Azure jest konto usługi.   Firma Microsoft będzie używać nazwy głównej usługi Aby pomóc nam tworzenie zasobów platformy Azure ze stacji roboczej naszych Chef.  Aby utworzyć odpowiednie jednostki usługi z wymaganymi uprawnieniami, należy uruchomić następujące polecenia w programie PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Prosimy o poświęcenie Zanotuj identyfikator subskrypcji, identyfikator dzierżawy, identyfikatora klienta i klucz tajny klienta (hasła, gdy ustawiasz powyżej), będzie on potrzebny później. 

## <a name="setup-chef-server"></a>Konfigurowanie serwera programu Chef

W tym przewodniku założono, że będzie zarejestrowaniu hostowany program Chef.

Jeśli jeszcze nie używasz serwera Chef, możesz to zrobić:

* Należy zasubskrybować [hostowany program Chef](https://manage.chef.io/signup), który jest najszybszym sposobem na rozpoczęcie pracy przy użyciu programu Chef.
* Instalowanie autonomiczny serwer programu Chef na maszynie z opartą na systemie linux, zgodnie z [instrukcje dotyczące instalacji](https://docs.chef.io/install_server.html) z [dokumentacja programu Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Tworzenie konta hostowany program Chef

Załóż konto hostowane Chef [tutaj](https://manage.chef.io/signup).

W procesie rejestracji użytkownik jest proszony o Utwórz nową organizację.

![][3]

Po utworzeniu Twojej organizacji, należy pobrać starter kit.

![][4]

> [!NOTE]
> Jeśli zostanie wyświetlony monit, ostrzeżenie, klucze zostaną zresetowane, jest OK kontynuować, gdy będą dostępne nie istniejącej infrastruktury, jak jeszcze skonfigurowane.
>

Ten plik zip zestawu starter zawiera pliki konfiguracji w organizacji i klucz użytkownika w `.chef` katalogu.

`organization-validator.pem` Należy go najpierw pobrać oddzielnie, ponieważ jest klucz prywatny i kluczy prywatnych nie powinien być przechowywany na serwerze programu Chef. Z [Zarządzanie Chef](https://manage.chef.io/), przejdź do sekcji Administracja i wybierz pozycję "Resetuj sprawdzania poprawności klucza", który zawiera plik do pobrania osobno. Zapisz plik c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurowanie stacji roboczej programu Chef

Wyodrębnij zawartość starter.zip chef, aby c:\chef.

Skopiuj wszystkie pliki w ramach programu chef starter\chef repozytorium\.chef z katalogiem c:\chef.

Kopiuj `organization-validator.pem` plik c:\chef, jeśli zostanie zapisany w c:\Downloads

Katalogu powinien teraz wyglądać jak w poniższym przykładzie.

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

Teraz masz pięć plików i katalogów cztery (w tym katalogu puste repozytorium firmy chef) w katalogu głównym c:\chef.

### <a name="edit-kniferb"></a>Edytuj knife.rb

Pliki PEM zawierają organizacji i administracyjnych kluczy prywatnych do komunikacji i plik knife.rb zawiera konfigurację Nóż. Konieczne będzie edytowanie pliku knife.rb.

Otwórz plik knife.rb w wybranym edytorze. Niezmienione plik powinien wyglądać podobnie jak:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Dodaj następujące informacje do swojej knife.rb:

validation_client_name "moduł sprawdzania myorg"

validation_key           "#{current_dir}/myorg.pem"

nóż [: azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"

nóż [: azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Linie te zapewniają, że nóż odwołuje się do katalogu podręczniki, w obszarze c:\chef\cookbooks i używa wzorów jednostki usługi platformy Azure, który został utworzony podczas operacji na platformie Azure.

Plik knife.rb powinien teraz wyglądać podobnie do poniższego przykładu:

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

## <a name="install-chef-workstation"></a>Zainstaluj program Chef stacji roboczej

Następnie [Pobierz i zainstaluj](https://downloads.chef.io/chef-workstation/) stacji roboczej programu Chef.
Zainstaluj Chef stacji roboczej domyślnej lokalizacji. Ta instalacja może potrwać kilka minut.

Na komputerze stacjonarnym zobaczysz "efektywna PowerShell", który jest załadowany za pomocą narzędzia, które będą potrzebne do interakcji z produktów Chef środowiska. PowerShell efektywna udostępnia nowe polecenia ad-hoc, takich jak `chef-run` również jako tradycyjne CLI Chef, polecenia takie jak `chef`. Zobacz stacji roboczej programu Chef i narzędzia Chef z zainstalowaną wersją `chef -v`. Możesz również sprawdzić wersji stacji roboczej, wybierając pozycję "Dotyczące programu Chef stacji roboczej" z aplikacji stacji roboczej programu Chef.

`chef --version` powinny zostać zwrócone mniej więcej tak:

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
> Ważna jest kolejność ścieżki! Jeśli Twoje ścieżki opscode nie znajdują się w odpowiedniej kolejności będziesz mieć problemy.
>

Uruchom ponownie stację roboczą, przed kontynuowaniem.

### <a name="install-knife-azure"></a>Zainstaluj nóż platformy Azure

Ten samouczek zakłada, że używasz usługi Azure Resource Manager do interakcji z maszyną wirtualną.

Zainstaluj rozszerzenie Azure Nóż. Dzięki temu nóż za pomocą wtyczki"Azure".

Uruchom następujące polecenie.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument — wstępnie gwarantuje, że otrzymujesz najnowszej wersji RC nóż wtyczka platformy Azure, która zapewnia dostęp do najnowszych zestaw interfejsów API.
>
>

Istnieje prawdopodobieństwo, że liczba zależności, także zostaną zainstalowane w tym samym czasie.

![][8]

Aby upewnić się, że wszystko jest poprawnie skonfigurowane, uruchom następujące polecenie.

    knife azurerm server list

Jeśli wszystko jest poprawnie skonfigurowane, zobaczysz listę przewijanie dostępne obrazy platformy Azure.

Gratulacje. Konfigurowanie stacji roboczej!

## <a name="creating-a-cookbook"></a>Podręcznik opisujący tworzenie

Podręcznik opisujący jest używany przez program Chef zdefiniować zestaw poleceń, które chcesz wykonać na komputerze klienckim zarządzanych. Podręcznik opisujący tworzenie jest proste, wystarczy użyć **chef Generowanie cookbook** polecenie, aby wygenerować szablonu Cookbook. Ten podręcznik jest dla serwera sieci web, która automatycznie wdraża usług IIS.

W katalogu C:\Chef uruchom następujące polecenie.

    chef generate cookbook webserver

To polecenie generuje zestaw plików w katalogu C:\Chef\cookbooks\webserver. Następnie zdefiniuj zestaw poleceń do wykonania w zarządzanej maszyny wirtualnej przez klienta programu Chef.

Polecenia są przechowywane w default.rb pliku. W tym pliku należy zdefiniować zestaw poleceń, który instaluje usługi IIS, uruchomienie usług IIS i kopiuje plik szablonu do folderu wwwroot.

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

Gdy skończysz, Zapisz plik.

## <a name="creating-a-template"></a>Tworzenie szablonu
W tym kroku zostanie wygenerowany plik szablonu do używana jako strona default.html.

Uruchom następujące polecenie, aby wygenerować szablonu:

    chef generate template webserver Default.htm

Przejdź do `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` pliku. Edytuj plik, dodając kilka prostych kodów "Hello World" HTML, a następnie zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Przekaż Cookbook na serwer programu Chef
W tym kroku możesz utworzyć kopię Cookbook, które zostały utworzone na komputerze lokalnym i przekaż go do serwera hostowanej Chef. Po przekazaniu plików Cookbook pojawia się w obszarze **zasad** kartę.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej z systemem Azure nóż
Wdrażanie maszyny wirtualnej platformy Azure i stosowanie Cookbook "Serwer sieci Web", który instaluje usługi IIS sieci web usługi i domyślne strony sieci web.

Aby to zrobić, należy użyć **Tworzenie serwera usługi azurerm nóż** polecenia.

Przykład polecenia pojawia się obok.

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


W powyższym przykładzie utworzy Standard_DS2_v2 maszyny wirtualnej z systemem Windows Server 2016 zainstalowany w regionie zachodnie stany USA. Zastąp określonego zmiennych i uruchom.

> [!NOTE]
> Za pośrednictwem wiersza polecenia I jestem również Automatyzowanie reguł filtra punktu końcowego sieci przy użyciu parametru punkty końcowe — tcp. Czy mogę zostało otwarte porty 80 i 3389, aby zapewnić dostęp do stron sieci web i sesję RDP.
>
>

Po uruchomieniu polecenia, przejdź do witryny Azure portal, aby zobaczyć maszynie rozpocząć aprowizację.

![][15]

Pojawia się obok wiersza polecenia.

![][16]

Po zakończeniu wdrożenia publiczny adres IP nowej maszyny wirtualnej pojawi się po zakończeniu wdrożenia, możesz to skopiować i wkleić go do przeglądarki sieci web i wyświetlić witrynę sieci Web, która została wdrożona. Wdrożyliśmy maszyny wirtualnej możemy otworzyć port 80, dlatego powinien być dostępny zewnętrznie.   

![][11]

W tym przykładzie użyto creative kodu HTML.

Można również wyświetlić stan węzła [Zarządzanie Chef](https://manage.chef.io/). 

![][17]

Pamiętaj, że możesz również łączyć się za pośrednictwem sesji protokołu RDP w witrynie Azure portal za pośrednictwem portu 3389.

Dziękuję! Go i Rozpocznij infrastruktury jako kodu pracy na platformie Azure już dzisiaj!

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
