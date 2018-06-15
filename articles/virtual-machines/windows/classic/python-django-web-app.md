---
title: Aplikacja sieci web Django na maszynie Wirtualnej Azure z systemem Windows Server | Dokumentacja firmy Microsoft
description: Dowiedz się, jak hostowanie witryny sieci Web na podstawie Django na platformie Azure przy użyciu systemu Windows Server 2012 R2 Datacenter Maszynę wirtualną z klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2017
ms.locfileid: "24057022"
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Aplikacja sieci web Django Hello World na maszynie Wirtualnej systemu Windows Server

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i klasycznym modelu wdrażania](../../../resource-manager-deployment-model.md). W tym artykule opisano klasycznym modelu wdrażania. Zaleca się, że większości nowych wdrożeń Użyj modelu Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym samouczku przedstawiono sposób hosta witryny sieci Web na podstawie Django w systemie Windows Server w maszynach wirtualnych platformy Azure. W samouczku przyjęto założenie, nie już pewne doświadczenie w systemie Azure. Po zakończeniu samouczka może mieć aplikacji opartej na Django w górę i w chmurze.

Instrukcje:

* Skonfiguruj maszynę wirtualną platformy Azure do hosta Django. Mimo że w tym samouczku wyjaśniono, jak to zrobić w **systemu Windows Server**, można wykonać takie same dla maszyny Wirtualnej systemu Linux hostowanych w systemie Azure.
* Utwórz nową aplikację Django w systemie Windows.

Samouczek przedstawia sposób tworzenia podstawowej aplikacji sieci web Hello World. Aplikacja znajduje się w maszynie wirtualnej platformy Azure.

Poniższy zrzut ekranu przedstawia gotową aplikację:

![Oknie przeglądarki zostanie wyświetlona strona world hello na platformie Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Tworzenie i konfigurowanie maszyny wirtualnej platformy Azure na hoście Django

1. Aby utworzyć maszynę wirtualną platformy Azure z rozkładem systemu Windows Server 2012 R2 Datacenter, zobacz [Utwórz maszynę wirtualną z systemem Windows w portalu Azure](tutorial.md).
2. Ustaw Azure, aby kierować port 80 ruch z sieci web do portu 80 w maszynie wirtualnej:
   
   1. W portalu Azure przejdź do pulpitu nawigacyjnego, a następnie wybierz nowo utworzony maszyny wirtualnej.
   2. Kliknij kolejno opcje **Punkty końcowe** i **Dodaj**.

     ![Dodawanie punktu końcowego](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Na **dodać punkt końcowy** strony, dla **nazwa**, wprowadź **HTTP**. Ustaw portów TCP publiczne i prywatne **80**.

     ![Wprowadź nazwę i Ustaw porty publiczny i prywatny](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Kliknij przycisk **OK**.
     
3. Na pulpicie nawigacyjnym wybierz maszyny Wirtualnej. Aby zdalnie Zaloguj się do maszyny wirtualnej platformy Azure nowo utworzony za pomocą protokołu RDP (Remote Desktop), kliknij przycisk **Connect**.  

> [!IMPORTANT] 
> Poniższe instrukcje założono, że użytkownik zalogowany do maszyny wirtualnej prawidłowo. One założono, że są wydawania polecenia na maszynie wirtualnej, a nie na komputerze lokalnym.

## <a id="setup"></a>Instalacji języka Python, Django i WFastCGI
> [!NOTE]
> Aby pobrać za pomocą programu Internet Explorer, może być konieczne skonfigurowanie programu Internet Explorer **Konfiguracja zwiększonych zabezpieczeń** ustawienia. Aby to zrobić, kliknij przycisk **Start** > **narzędzia administracyjne** > **Menedżera serwera** > **serweralokalnego**. Kliknij przycisk **Konfiguracja zwiększonych zabezpieczeń**, a następnie wybierz **poza**.

1. Zainstaluj najnowsze wersje Python 2.7 lub 3.4 Python z [python.org][python.org].
2. Instalowanie przy użyciu narzędzia pip pakiety wfastcgi i django.
   
    Dla języka Python 2.7 użyj następującego polecenia:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Dla języka Python 3.4 Użyj następującego polecenia:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Instalowanie usług IIS przy użyciu FastCGI
* Zainstaluj usługi Internet Information Services (IIS) z obsługą FastCGI. Może to potrwać kilka minut do wykonania.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Utwórz nową aplikację Django
1. W C:\inetpub\wwwroot Aby utworzyć nowy projekt Django, wprowadź następujące polecenie:
   
   Dla języka Python 2.7 użyj następującego polecenia:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Dla języka Python 3.4 Użyj następującego polecenia:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Wynik polecenia New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. `django-admin` Polecenia wygenerowanie podstawowej struktury witryn skonstruowanych na podstawie Django:
   
   * `helloworld\manage.py`pomaga rozpocząć hosting i Zatrzymaj hosting witryny sieci Web na podstawie Django.
   * `helloworld\helloworld\settings.py`zawiera ustawienia Django dla aplikacji.
   * `helloworld\helloworld\urls.py`nie ma kodu mapowania między każdego adresu URL i jego widoku.
3. W katalogu C:\inetpub\wwwroot\helloworld\helloworld Utwórz nowy plik o nazwie views.py. Ten plik zawiera widok, który renderuje stronę "hello world". W edytorze kodu wprowadź następujące polecenia:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Zastąp zawartość pliku urls.py za pomocą następujących poleceń:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Konfigurowanie usług IIS
1. W pliku applicationhost.config globalne Odblokuj sekcję programów obsługi.  Dzięki temu pliku web.config, aby użyć obsługi języka Python. Dodaj to polecenie:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Aktywacja WFastCGI. Spowoduje to dodanie aplikacji do pliku applicationhost.config globalny, który odwołuje się do pliku wykonywalnego interpretera Python i skrypt wfastcgi.py.
   
    W języku Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    W języku Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. W C:\inetpub\wwwroot\helloworld Utwórz plik web.config. Wartość `scriptProcessor` atrybutu powinna być zgodna dane wyjściowe z poprzedniego kroku. Aby uzyskać więcej informacji o ustawieniu wfastcgi, zobacz [pypi wfastcgi][wfastcgi].
   
   W języku Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   W języku Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Należy zaktualizować lokalizację usługi IIS domyślnej witryny sieci Web wskazują folder projektu Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Ładowanie strony sieci Web w przeglądarce.

![Oknie przeglądarki zostanie wyświetlona strona world hello na platformie Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Zamknij maszynę wirtualną z platformy Azure
Po zakończeniu korzystania z tego samouczka, firma Microsoft zaleca zamknięcie lub Usuń maszynę Wirtualną platformy Azure utworzoną w ramach tego samouczka. Spowoduje to zwolnienie zasobów dla innych samouczków, a można uniknąć naliczania opłat użycia platformy Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
