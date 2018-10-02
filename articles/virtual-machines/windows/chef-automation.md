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
ms.openlocfilehash: de89756a3f9ef1139e855da16c0343a9919b56cb
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585378"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatyzowanie wdrażania maszyny wirtualnej platformy Azure przy użyciu narzędzia Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef to doskonałe narzędzie do dostarczania automatyzacji i żądanego stanu konfiguracji.

Najnowsza wersja interfejsu API w chmurze Chef zapewnia bezproblemową integrację z platformą Azure, dzięki czemu możesz aprowizować i wdrażać stanami konfiguracji za pomocą jednego polecenia.

Ten artykuł służy do konfigurowania środowiska Chef do udostępniania maszyn wirtualnych platformy Azure i poznać dokładnie proces tworzenia zasad lub "CookBook", a następnie wdrożenie ten podręcznik na maszynie wirtualnej platformy Azure.

Zacznijmy!

## <a name="chef-basics"></a>Podstawy programu chef
Przed przystąpieniem do wykonywania [zapoznaj się z podstawowymi informacjami dotyczącymi Chef](http://www.chef.io/chef). 

Poniższy diagram przedstawia architekturę wysokiego poziomu Chef.

![][2]

Program chef ma trzy główne składniki architektury: serwer programu Chef, Chef, klient (node) i stacji roboczej programu Chef.

Serwer programu Chef punkt zarządzania i dostępne są dwie opcje dla serwera programu Chef: hostowanego rozwiązania lub rozwiązaniem w firmie. Firma Microsoft będzie używać rozwiązania hostowanego w tym samouczku.

Klient programu Chef (node) jest agent, który znajduje się na serwerach, którymi zarządzasz.

Stacja robocza Chef jest administracyjnej stacji roboczej, którym możemy tworzyć zasady i wykonywania poleceń zarządzania. Możemy uruchomić **nóż** polecenie z poziomu stacji roboczej programu Chef do zarządzania infrastrukturą.

Istnieje również pojęcie "Podręczniki" i "Przepisów". Są to efektywne zasady, możemy zdefiniować i zastosować do serwerów.

## <a name="preparing-the-workstation"></a>Przygotowanie stacji roboczej
Po pierwsze służy do przygotowania stacji roboczej. Używam standardowa stacji roboczej Windows. Należy utworzyć katalog do przechowywania plików konfiguracji i podręczniki.

Najpierw utworzyć katalog o nazwie C:\chef.

Następnie utwórz drugi katalog o nazwie c:\chef\cookbooks.

Teraz musisz pobrać plik ustawienia usługi Azure, więc Chef może komunikować się z subskrypcją platformy Azure.

Pobierz swoje publikowanie ustawień za pomocą usługi PowerShell Azure [Get AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) polecenia. 

Zapisz plik ustawień publikowania w C:\chef.

## <a name="creating-a-managed-chef-account"></a>Tworzenie zarządzanego konta programu Chef
Hostowane konto programu Chef [tutaj](https://manage.chef.io/signup).

Podczas procesu rejestracji użytkownik jest proszony o Utwórz nową organizację.

![][3]

Po utworzeniu Twojej organizacji, należy pobrać starter kit.

![][4]

> [!NOTE]
> Jeśli zostanie wyświetlony monit, ostrzeżenie, klucze zostaną zresetowane, jest OK kontynuować, gdy będą dostępne nie istniejącej infrastruktury, jak jeszcze skonfigurowane.
> 
> 

Ten plik zip zestaw startowy zawiera organizacji, pliki konfiguracji i kluczy.

## <a name="configuring-the-chef-workstation"></a>Konfigurowanie stacji roboczej programu Chef
Wyodrębnij zawartość starter.zip chef, aby C:\chef.

Skopiuj wszystkie pliki w ramach programu chef starter\chef repozytorium\.chef z katalogiem c:\chef.

Katalogu powinien teraz wyglądać jak w poniższym przykładzie.

![][5]

Teraz masz cztery pliki w folderze głównym c:\chef w tym plików publikowania platformy Azure.

Pliki PEM zawierają organizacji i prywatne klucze administratora w celu komunikacji, gdy plik knife.rb zawiera konfigurację Nóż. Konieczne będzie edytowanie pliku knife.rb.

Otwórz plik w wybranym edytorze i zmodyfikować "cookbook_path" poprzez usunięcie /... / ze ścieżki, dzięki czemu będzie ono wyświetlane jako:

    cookbook_path  ["#{current_dir}/cookbooks"]

Dodaj również następującą pliku ustawień publikowania wiersza odzwierciedlający Nazwa subskrypcji platformy Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Plik knife.rb powinna teraz wyglądać podobnie do poniższego przykładu.

![][6]

Linie te zapewniają, że nóż odwołuje się do katalogu podręczniki, w obszarze c:\chef\cookbooks i używa wzorów naszego pliku ustawień publikowania platformy Azure podczas operacji na platformie Azure.

## <a name="installing-the-chef-development-kit"></a>Instalowanie programu Chef Development Kit
Następnie [Pobierz i zainstaluj](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit), aby skonfigurować stację roboczą Chef.

![][7]

Zainstalować w domyślnej lokalizacji c:\opscode. Ta instalacja potrwa około 10 minut.

Upewnij się, że w zmiennej PATH zawiera wpisy dla C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Jeśli nie istnieje, upewnij się, że możesz dodać te ścieżki!

> [!NOTE]
> Ważna jest kolejność ścieżki! Jeśli Twoje ścieżki opscode nie znajdują się w odpowiedniej kolejności będziesz mieć problemy. 
> 

Uruchom ponownie stację roboczą, przed kontynuowaniem.

Następnie firma Microsoft zainstaluje rozszerzenia nóż platformy Azure. Dzięki temu nóż za pomocą wtyczki"Azure".

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
Podręcznik opisujący jest używany przez program Chef zdefiniować zestaw poleceń, które chcesz wykonać na komputerze klienckim zarządzanych. Podręcznik opisujący tworzenie jest proste, a następnie używamy **chef Generowanie cookbook** polecenie, aby wygenerować szablonu Cookbook. Czy mogę będzie wywoływać Mój serwer sieci web Cookbook jako chciałbym zasadę automatycznego wdrażania usług IIS.

W katalogu C:\Chef uruchom następujące polecenie.

    chef generate cookbook webserver

Spowoduje to wygenerowanie zestawu plików w katalogu C:\Chef\cookbooks\webserver. Teraz musisz zdefiniować zestaw poleceń, prosimy o poświęcenie klienta programu Chef do wykonania w zarządzanej maszyny wirtualnej.

Polecenia są przechowywane w default.rb pliku. W tym pliku I będzie można definiujący zestaw poleceń, który instaluje usługi IIS, uruchomienie usług IIS i kopiuje plik szablonu do folderu wwwroot.

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
Jak wspomniano wcześniej, należy wygenerować plik szablonu, które będzie używane jako strony default.html.

Uruchom następujące polecenie, aby wygenerować szablonu.

    chef generate template webserver Default.htm

Teraz przejdź do pliku C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edytuj plik, dodając kilka prostych kodów "Hello World" HTML, a następnie zapisz plik.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Przekaż Cookbook na serwer programu Chef
W tym kroku jesteśmy zabierać Cookbook, który został utworzony na komputerze lokalnym i przekazać go do serwera hostowanej Chef. Po przekazaniu plików Cookbook pojawi się w obszarze **zasad** kartę.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Wdrażanie maszyny wirtualnej z systemem Azure nóż
Firma Microsoft będzie teraz wdrożyć maszynę wirtualną platformy Azure oraz stosować Cookbook "Serwer sieci Web", co spowoduje zainstalowanie usług IIS sieci web service i domyślnej strony sieci web.

Aby to zrobić, należy użyć **server nóż azure utworzyć** polecenia.

Czy na przykład polecenia pojawia się obok.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametry są oczywiste. Zastąp określonego zmiennych i uruchom.

> [!NOTE]
> Za pośrednictwem wiersza polecenia I jestem również Automatyzowanie reguł filtra punktu końcowego sieci przy użyciu parametru punkty końcowe — tcp. Czy mogę zostało otwarte porty 80 i 3389, aby zapewnić dostęp do Mój strony sieci web i sesję RDP.
> 
> 

Po uruchomieniu polecenia, przejdź do witryny Azure portal i zostanie wyświetlony maszynie rozpocząć aprowizację.

![][13]

Pojawia się obok wiersza polecenia.

![][10]

Po zakończeniu wdrażania możemy należy połączyć się z usługą sieci web za pośrednictwem portu 80 jako otwarto port gdy firma Microsoft aprowizowane maszyny wirtualnej za pomocą polecenia nóż Azure. Ta maszyna wirtualna jest tylko maszynę wirtualną w mojej usłudze w chmurze, I będziesz go połączyć z adres url usługi w chmurze.

![][11]

Jak widać, mam creative mój kod HTML.

Pamiętaj, że możemy także połączyć za pośrednictwem sesji protokołu RDP w witrynie Azure portal za pośrednictwem portu 3389.

Mam nadzieję, że było to pomocne! Go i Rozpocznij infrastruktury jako kodu pracy na platformie Azure już dzisiaj!

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
