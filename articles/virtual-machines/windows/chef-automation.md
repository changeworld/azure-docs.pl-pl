---
title: Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu Chef | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Chef do wdrażania automatycznego maszyny wirtualnej i konfiguracji w systemie Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 9cb7172fb529d8f0cd8650db7c06a78176ef342d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729559"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatyzowanie wdrażania maszyny wirtualnej platformy Azure przy użyciu narzędzia Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

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

Utwórz katalog o nazwie C:\chef.

Pobieranie programu Azure PowerShell [ustawień publikowania](https://docs.microsoft.com/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information).

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

`organization-validator.pem` Należy go najpierw pobrać oddzielnie, ponieważ jest klucz prywatny i kluczy prywatnych nie powinien być przechowywany na serwerze programu Chef. Z [Zarządzanie Chef](https://manage.chef.io/) i wybierz pozycję "Resetuj sprawdzania poprawności klucza", który zawiera plik do pobrania osobno. Zapisz plik c:\chef.

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

Dodaj również następującą pliku ustawień publikowania wiersza odzwierciedlający Nazwa subskrypcji platformy Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Modyfikowanie "cookbook_path" poprzez usunięcie /... / ze ścieżki, dzięki czemu będzie ono wyświetlane jako:

    cookbook_path  ["#{current_dir}/cookbooks"]

Linie te zapewniają, że nóż odwołuje się do katalogu podręczniki, w obszarze c:\chef\cookbooks i używa wzorów naszego pliku ustawień publikowania platformy Azure podczas operacji na platformie Azure.

Plik knife.rb powinien teraz wyglądać podobnie do poniższego przykładu:

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Zainstaluj program Chef stacji roboczej

Następnie [Pobierz i zainstaluj](https://downloads.chef.io/chef-workstation/) stacji roboczej programu Chef.
Zainstaluj Chef stacji roboczej domyślnej lokalizacji. Ta instalacja może potrwać kilka minut.

Na komputerze stacjonarnym zobaczysz "efektywna PowerShell", który jest załadowany za pomocą narzędzia, które będą potrzebne do interakcji z produktów Chef środowiska. PowerShell efektywna udostępnia nowe polecenia ad-hoc, takich jak `chef-run` również jako tradycyjne CLI Chef, polecenia takie jak `chef`. Zobacz stacji roboczej programu Chef i narzędzia Chef z zainstalowaną wersją `chef -v`. Możesz również sprawdzić wersji stacji roboczej, wybierając pozycję "Dotyczące programu Chef stacji roboczej" z aplikacji stacji roboczej programu Chef.

`chef --version` powinny zostać zwrócone mniej więcej tak:

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
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

    knife azure image list

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

Aby to zrobić, należy użyć **server nóż azure utworzyć** polecenia.

Przykład polecenia pojawia się obok.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametry są oczywiste. Zastąp określonego zmiennych i uruchom.

> [!NOTE]
> Za pośrednictwem wiersza polecenia I jestem również Automatyzowanie reguł filtra punktu końcowego sieci przy użyciu parametru punkty końcowe — tcp. Czy mogę zostało otwarte porty 80 i 3389, aby zapewnić dostęp do Mój strony sieci web i sesję RDP.
>
>

Po uruchomieniu polecenia, przejdź do witryny Azure portal, aby zobaczyć maszynie rozpocząć aprowizację.

![][13]

Pojawia się obok wiersza polecenia.

![][10]

Po zakończeniu wdrożenia należy nawiązać połączenie z usługą sieci web za pośrednictwem portu 80, ponieważ port jest otwarty podczas aprowizacji maszyny wirtualnej za pomocą polecenia nóż platformy Azure. Ponieważ ta maszyna wirtualna jest tylko maszynę wirtualną w tej usłudze w chmurze, połączyć się z nim adres url usługi w chmurze.

![][11]

W tym przykładzie użyto creative kodu HTML.

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


<!--Link references-->
