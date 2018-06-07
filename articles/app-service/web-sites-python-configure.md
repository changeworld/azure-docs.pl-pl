---
title: Konfigurowanie języka Python z aplikacjami sieci Web usługi aplikacji Azure
description: W tym samouczku opisano opcje dotyczące tworzenia i konfigurowania podstawowego serwera sieci Web zgodnych aplikacji Python interfejs bramy (WSGI) w aplikacji sieci Web usługi aplikacji Azure.
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: ''
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: 32d9cd6c42387b67881877a1165dfcbcaef405ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598644"
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurowanie języka Python z aplikacjami sieci Web usługi aplikacji Azure
W tym samouczku opisano opcje dotyczące tworzenia i konfigurowania podstawowej aplikacji Python zgodnych sieci Web serwera bramy interfejsu (WSGI) w [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Opisuje dodatkowe funkcje Git, takiego jak wdrożenie środowiska wirtualnego, a instalacja pakietu przy użyciu pliku requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django lub Flask?
W portalu Azure Marketplace zawiera szablony dla struktur Bottle, Django i Flask. Jeśli tworzysz pierwszej aplikacji sieci web w usłudze Azure App Service, możesz ją utworzyć szybko z portalu Azure:

* [Aplikacja sieci Web z Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Aplikacja sieci Web przy użyciu platformy Django](https://portal.azure.com/#create/PTVS.Django)
* [Aplikacja sieci Web z platformy Flask, w systemie Linux](https://portal.azure.com/#create/PTVS.FlaskLinux)

## <a name="web-app-creation-on-azure-portal"></a>Tworzenie aplikacji sieci Web w portalu Azure
Ten samouczek zakłada istniejącą subskrypcję platformy Azure i dostęp do portalu Azure.

Jeśli nie masz istniejącej aplikacji sieci web, możesz utworzyć jedną z [portalu Azure](https://portal.azure.com). W lewym górnym rogu kliknij **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

## <a name="git-publishing"></a>Publikowanie w usłudze Git
Skonfiguruj publikowanie w systemie Git dla nowo utworzonej aplikacji sieci Web zgodnie z poniższymi instrukcjami w artykule [Local Git Deployment to Azure App Service](app-service-deploy-local-git.md) (Lokalne wdrażanie przy użyciu systemu Git w usłudze Azure App Service). W tym samouczku używana Git do tworzenia, zarządzania i publikowanie aplikacji sieci web języka Python w usłudze Azure App Service.

Po skonfigurowaniu publikowania w usłudze Git repozytorium Git jest utworzona i skojarzona z aplikacji sieci web. Adres URL repozytorium jest wyświetlany i może służyć do dystrybuowania danych ze środowiska projektowego lokalnych do chmury. Do publikowania aplikacji za pomocą narzędzia Git, upewnij się, że zainstalowano klienta Git i skorzystać z instrukcji podanych do dystrybuowania zawartości aplikacji sieci web w usłudze Azure App Service.

## <a name="application-overview"></a>Omówienie aplikacji
W kolejnych sekcjach są tworzone następujące pliki. Powinny one umieszczone w folderze głównym repozytorium Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Program obsługi WSGI
WSGI jest standardem Python opisanego przez [3333 program ten](http://www.python.org/dev/peps/pep-3333/) Definiowanie interfejsu między serwerem sieci web i Python. Zapewnia interfejs standardowych do pisania różnych aplikacji sieci web i struktur za pomocą języka Python. Obecnie popularnych struktur sieci web języka Python posłużyć WSGI. Umożliwia aplikacji sieci Web usługi aplikacji Azure obsługę tych platform; Ponadto użytkownicy zaawansowani można nawet tworzyć własne tak długo, jak niestandardowy program obsługi jest zgodna z wytycznymi specyfikacji WSGI.

Oto przykład `app.py` definiuje niestandardowego programu obsługi:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Można uruchomić tej aplikacji lokalnie z `python app.py`, a następnie wyszukaj `http://localhost:5555` w przeglądarce sieci web.

## <a name="virtual-environment"></a>Środowisko wirtualne
Chociaż poprzedniego przykładową aplikację nie wymaga żadnych pakiety zewnętrzne, jest prawdopodobne, że aplikacja wymaga niektórych.

Aby ułatwić zarządzanie zależności pakietów zewnętrznych, wdrożenie Azure Git obsługuje tworzenie środowisk wirtualnych.

Wykrycie Azure pliku requirements.txt w folderze głównym repozytorium, automatycznie tworzy środowiska wirtualnego o nazwie `env`. Jest to wykonywane tylko przy pierwszym wdrożeniu lub podczas każdego wdrożenia po Python wybranego środowiska wykonawczego został zmieniony.

Prawdopodobnie chcesz utworzyć środowisko wirtualne lokalnie dla rozwoju, ale nie dołączaj do repozytorium Git.

## <a name="package-management"></a>Zarządzanie pakietami
Pakiety wymienione w pliku requirements.txt są instalowane automatycznie w środowisku wirtualnym przy użyciu narzędzia pip. Dzieje się to przy każdym wdrożeniu, ale pip pomija instalację, jeśli pakiet jest już zainstalowana.

Przykład `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Wersja języka Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Przykład `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Należy utworzyć plik web.config, aby określić, jak serwer powinien obsługiwać żądań.

Jeśli plik pliku web.x.y.config w repozytorium, w którym x.y zgodny wybrane środowisko uruchomieniowe języka Python, Azure automatycznie kopiuje odpowiedniego pliku jako pliku web.config.

W poniższych przykładach plik web.config jest zależne od środowiska wirtualnego skryptu serwera proxy, który jest opisany w następnej sekcji.  Pracują z programem obsługi WSGI używane w tym przykładzie `app.py` powyżej.

Przykład `web.config` dla języka Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Przykład `web.config` dla języka Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Pliki statyczne są obsługiwane przez serwer sieci web bezpośrednio, bez przechodzenia przez kod języka Python, aby poprawić wydajność.

W powyższych przykładach lokalizacji plików statycznych na dysku powinien być zgodny lokalizacji w adresie URL. Oznacza to, że żądanie `http://pythonapp.azurewebsites.net/static/site.css` posłuży pliku na dysku w `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` Określa się tam WSGI program obsługi. W powyższych przykładach ma `app.wsgi_app` ponieważ program obsługi ma funkcji o nazwie `wsgi_app` w `app.py` w folderze głównym.

`PYTHONPATH` można dostosować, ale po zainstalowaniu wszystkich zależności w środowisku wirtualnym, określając je w pliku requirements.txt, nie należy go zmienić.

## <a name="virtual-environment-proxy"></a>Serwer Proxy środowiska wirtualnego
Następujący skrypt służy do pobierania programu obsługi WSGI, aktywować wirtualnego środowiska i dziennik błędów. Jest ona być rodzajowa i używane bez modyfikacji.

Zawartość `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Dostosowywanie wdrożenia usługi Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Rozwiązywanie problemów — instalowanie pakietów
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Rozwiązywanie problemów — środowisko wirtualne
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](/develop/python/).

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację sieci Web o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 
