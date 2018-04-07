---
title: Wdrożenie maszyny wirtualnej platformy Azure z Chef | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Chef wdrażania automatycznego maszyn wirtualnych i konfiguracji w systemie Microsoft Azure
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
ms.openlocfilehash: 36293c41219a1b42d75850fa66d3c631637bb855
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatyzowanie wdrażania maszyny wirtualnej platformy Azure przy użyciu narzędzia Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef to doskonałe narzędzie do dostarczania automatyzacji i żądanego stanu konfiguracji.

Przy użyciu najnowszych danych w chmurze interfejsu api zlecenia, Chef zapewnia bezproblemową integrację z platformy Azure, co daje możliwość obsługi administracyjnej i wdrażania stanami konfiguracji, za pomocą jednego polecenia.

W tym artykule służy do konfigurowania środowiska Chef ich obsługa administracyjna maszyn wirtualnych platformy Azure i przeprowadzenie tworząc zasady lub "CookBook", a następnie wdrażania tego cookbook na maszynie wirtualnej platformy Azure.

Zacznijmy!

## <a name="chef-basics"></a>Podstawy chef
Przed rozpoczęciem [Przejrzyj podstawowe pojęcia Chef](http://www.chef.io/chef). 

Poniższy diagram przedstawia architekturę Chef wysokiego poziomu.

![][2]

Chef ma trzy główne składniki architektury: Chef, Chef klienta (węzeł), Chef stacji roboczej i serwerze.

Serwer Chef punkt zarządzania i dostępne są dwie opcje serwera Chef: hostowanej rozwiązania lub rozwiązaniem w firmie. Użyjemy hostowanego rozwiązania.

Klient Chef (węzeł) jest agent, który znajduje się na serwerach, którym zarządzasz.

Stacja robocza Chef jest administracyjnej stacji roboczej, którym możemy Tworzenie zasad i wykonywać polecenia zarządzania. Możemy uruchomić **nóż** polecenie z Chef stacji roboczej do zarządzania infrastrukturą.

Istnieje również pojęcie "Cookbooks" i "Przepisami". Efektywne są zasady możemy definiowanie i stosowane do serwerów.

## <a name="preparing-the-workstation"></a>Przygotowanie stacji roboczej
Po pierwsze umożliwia przygotowywanie stacji roboczej. Używam standardowe stacji roboczej systemu Windows. Należy utworzyć katalog do przechowywania plików konfiguracji i cookbooks.

Najpierw utwórz katalog o nazwie C:\chef.

Następnie utwórz drugi katalog o nazwie c:\chef\cookbooks.

Teraz musisz pobrać pliku ustawień platformy Azure, Chef może komunikować się z subskrypcją platformy Azure.

Pobierz Twoje ustawienia za pomocą usługi PowerShell Azure publikowania [Get AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) polecenia. 

Zapisz plik ustawień publikowania w C:\chef.

## <a name="creating-a-managed-chef-account"></a>Tworzenie zarządzanego konta Chef
Załóż konto hostowanej Chef [tutaj](https://manage.chef.io/signup).

Podczas procesu rejestracji użytkownik jest proszony o Utwórz nową organizację.

![][3]

Po utworzeniu organizacji, Pobierz program starter kit.

![][4]

> [!NOTE]
> Jeśli zostanie wyświetlony monit, ostrzeżenie, że spowoduje zresetowanie kluczy, to przycisk ok, aby kontynuować, gdy będą dostępne nie istniejącej infrastruktury jeszcze skonfigurowany.
> 
> 

Ten plik starter kit zip zawiera organizacji plików konfiguracji i kluczy.

## <a name="configuring-the-chef-workstation"></a>Konfigurowanie stacji roboczej Chef
Wyodrębnij zawartość starter.zip chef, aby C:\chef.

Kopiowanie wszystkich plików w katalogu chef-starter\chef repozytorium\.chef do katalogu c:\chef.

Katalogu powinien teraz wyglądać jak w następującym przykładzie.

![][5]

Teraz powinien mieć cztery pliki, w tym Azure publikowania plików w folderze głównym c:\chef.

Pliki PEM zawierają organizacji i administratora klucze prywatne do komunikacji, podczas plik knife.rb zawiera konfigurację Nóż. Musimy Przeprowadź edycję pliku knife.rb.

Otwórz plik w edytorze wyboru i modyfikowania "cookbook_path" przez usunięcie /... / ze ścieżki tak wygląda na to, jak pokazano w następnym.

    cookbook_path  ["#{current_dir}/cookbooks"]

Również Dodaj następujący wiersz w czasie wykonywania odbicia nazwę platformy Azure pliku ustawień publikowania.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Plik knife.rb powinna wyglądać podobnie do poniższego przykładu.

![][6]

Te wiersze zapewnia, że nóż odwołuje się do katalogu cookbooks w c:\chef\cookbooks i używa również naszych pliku ustawień publikowania platformy Azure podczas operacji platformy Azure.

## <a name="installing-the-chef-development-kit"></a>Instalowanie Chef Development Kit
Następny [Pobierz i zainstaluj](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit), aby skonfigurować stację roboczą Chef.

![][7]

Zainstaluj w domyślnej lokalizacji c:\opscode. Ta instalacja potrwa około 10 minut.

Upewnij się, że zmiennej PATH zawiera wpisy dla C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Jeśli nie są dostępne, upewnij się, że możesz dodać tych ścieżek!

*NALEŻY PAMIĘTAĆ, ŻE WAŻNA JEST KOLEJNOŚĆ ŚCIEŻKI!* Jeśli Twoje opscode ścieżek nie są w odpowiedniej kolejności masz problemy.

Przed kontynuowaniem uruchom ponownie stację roboczą.

Następnie zostanie zainstalowany rozszerzenia nóż Azure. Zapewnia to nóż z wtyczki"Azure".

Uruchom następujące polecenie.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument — wstępnie gwarantuje, że wyświetlany jest najnowsza wersja RC nóż wtyczki Azure, która zapewnia dostęp do najnowszych zestaw interfejsów API.
> 
> 

Istnieje prawdopodobieństwo, że liczba zależności zostanie również zainstalowany w tym samym czasie.

![][8]

Aby upewnić się, czy wszystko jest poprawnie skonfigurowana, uruchom następujące polecenie.

    knife azure image list

Jeśli wszystko jest poprawnie skonfigurowane, zobaczysz listę dostępnych obrazów Azure przewiń.

Gratulacje. Konfigurowanie stacji roboczej!

## <a name="creating-a-cookbook"></a>Tworzenie Cookbook
Cookbook jest używany przez Chef, aby określić zestaw poleceń, które chcesz wykonać na zarządzanym kliencie. Tworzenie Cookbook jest proste i używamy **chef Generowanie cookbook** polecenie, aby wygenerować szablonu dotyczące systemów. I będzie wywoływany serwer sieci web Cookbook jako chcę zasadę, która powoduje automatyczne wdrożenie usług IIS.

W obszarze katalogu C:\Chef uruchom następujące polecenie.

    chef generate cookbook webserver

Spowoduje to wygenerowanie zestawu plików w katalogu C:\Chef\cookbooks\webserver. Teraz musisz zdefiniować zestaw poleceń, chcielibyśmy klienta Chef do wykonania na zarządzanej maszynie wirtualnej.

Polecenia są przechowywane w pliku default.rb. W tym pliku I będzie można definiujący zestaw poleceń, który instaluje usługi IIS, uruchamiania usług IIS i kopiuje plik szablonu do folderu wwwroot.

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

Gdy wszystko będzie gotowe, Zapisz plik.

## <a name="creating-a-template"></a>Tworzenie szablonu
Jak wspomniano wcześniej, należy wygenerować pliku szablonu, który będzie używany jako strony default.html.

Uruchom następujące polecenie, aby wygenerować szablonu.

    chef generate template webserver Default.htm

Teraz przejdź do pliku C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edytuj plik przez dodanie prostego kodu "Hello World" HTML, a następnie zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Przesłanie Cookbook serwer Chef
W tym kroku wiemy zabierać Cookbook, który został utworzony na komputerze lokalnym i przekazać go do serwera hostowanej Chef. Po przekazaniu plików Cookbook pojawią się w folderze **zasad** kartę.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej z platformy Azure, nóż
Firma Microsoft będzie teraz wdrożyć maszyny wirtualnej platformy Azure i stosować Cookbook "Serwer sieci Web", co spowoduje zainstalowanie usług IIS sieci web service i domyślnej strony sieci web.

Aby to zrobić, użyj **utworzenie przez serwer nóż azure** polecenia.

Czy na przykład polecenia pojawi się dalej.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametry są oczywiste. Zastąp określonego zmiennych, a następnie uruchom.

> [!NOTE]
> Za pomocą wiersza polecenia I używam również Automatyzowanie reguł filtra punktu końcowego sieci przy użyciu parametru punkty końcowe tcp —. I zostały otwarte porty 80 i 3389 w celu zapewnienia dostępu do Moja strona sieci web i sesji protokołu RDP.
> 
> 

Po uruchomieniu polecenia przejdź do portalu Azure i zostanie wyświetlony ten komputer należy rozpocząć.

![][13]

Następnie zostanie wyświetlona w wierszu polecenia.

![][10]

Po zakończeniu wdrażania, firma Microsoft należy połączyć z usługą sieci web za pośrednictwem portu 80, jak firma Microsoft ma otwarty port podczas przydzielania możemy maszyny wirtualnej przy użyciu polecenia nóż Azure. Ta maszyna wirtualna jest dostępna tylko maszyny wirtualnej w usłudze w chmurze, połączę go z adresem url usługi chmury.

![][11]

Jak widać, otrzymano creative z mojego kodu HTML.

Pamiętaj, że firma Microsoft mogą łączyć za pomocą sesji protokołu RDP z portalu Azure za pośrednictwem portu 3389.

Mam nadzieję, że było to pomocne! Przejdź i uruchom infrastruktury jako przebieg kodu z platformą Azure już dziś!

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
