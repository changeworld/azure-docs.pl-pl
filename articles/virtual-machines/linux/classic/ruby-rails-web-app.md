---
title: Host Ruby w witrynie sieci Web szyny na maszynie Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie i udostępniać Ruby na podstawie szyny witryny sieci Web na platformie Azure przy użyciu maszyny wirtualnej systemu Linux.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fa19f3dc7dded712102d4ba9b66dd4df1bfd20dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397601"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Aplikacja sieci Web Ruby on Rails na maszynie wirtualnej platformy Azure
Ten samouczek pokazuje, jak udostępniać Ruby na szyny witryny sieci Web na platformie Azure przy użyciu maszyny wirtualnej systemu Linux.  

W tym samouczku została zweryfikowana przy użyciu Ubuntu Server 14.04 LTS. Jeśli używasz innej dystrybucji systemu Linux, może być konieczne zmodyfikowanie kroki, aby zainstalować szyny.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Tworzenie maszyny Wirtualnej platformy Azure
Rozpocznij od utworzenia maszyny Wirtualnej platformy Azure z obrazem systemu Linux.

Aby utworzyć maszynę Wirtualną, używając portalu Azure lub interfejsu wiersza polecenia platformy Azure (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Kliknij przycisk **Utwórz zasób**, następnie w polu wyszukiwania wpisz "Ubuntu Server 14.04". Kliknij wpis zwrócony przez wyszukiwanie. Wybierz model wdrażania **klasycznego**, następnie kliknij przycisk "Utwórz".
3. Podaj wartości dla pól wymaganych w bloku podstawowe służące: Nazwa (VM), nazwę użytkownika, typ uwierzytelniania i odpowiednie poświadczenia subskrypcji platformy Azure, lokalizacji i grupy zasobów.

   ![Utwórz nowy obraz Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Po zainicjowaniu obsługi maszyny Wirtualnej, kliknij nazwę maszyny Wirtualnej i kliknij przycisk **punkty końcowe** w **ustawienia** kategorii. Znaleźć punkt końcowy SSH, kategorii **autonomiczny**.

   ![Domyślny punkt końcowy](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Postępuj zgodnie z instrukcjami [tworzenia maszyny wirtualnej systemem Linux][vm-instructions].

Po zainicjowaniu obsługi maszyny Wirtualnej, można uzyskać punkt końcowy SSH, uruchamiając następujące polecenie:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Zainstaluj na szyny Ruby
1. Używanie protokołu SSH, aby nawiązać połączenie z maszyną Wirtualną.
2. W sesji SSH Aby zainstalować Ruby na maszynie Wirtualnej należy użyć następujących poleceń:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Instalacja może zająć kilka minut. Po ukończeniu, użyj następującego polecenia, aby sprawdzić, czy zainstalowano Ruby:

        ruby -v

3. Aby zainstalować szyny, użyj następującego polecenia:

        sudo gem install rails --no-rdoc --no-ri -V

    Użyj nie rdoc i--nie ri flagi pomijania instalowanie dokumentacji, co jest szybsze.
    To polecenie będzie prawdopodobnie zająć dużo czasu do wykonania, więc Dodawanie -V zostaną wyświetlone informacje o postępie instalacji.

## <a name="create-and-run-an-app"></a>Tworzenie i uruchamianie aplikacji
Jest nadal zalogowany za pośrednictwem protokołu SSH, uruchom następujące polecenia:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[Nowe](http://guides.rubyonrails.org/command_line.html#rails-new) polecenie tworzy nową aplikację szyny. [Serwera](http://guides.rubyonrails.org/command_line.html#rails-server) polecenie uruchamia serwer sieci web WEBrick dołączony do szyny. (Do użytku produkcyjnego będzie prawdopodobnie chcesz użyć innego serwera, takie jak Unicorn lub pasażerów.)

Powinny pojawić się dane wyjściowe podobne do następujących:

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Dodawanie punktu końcowego
1. Przejdź do [Azure portal] [https://portal.azure.com] i wybierz maszyny Wirtualnej.

2. Wybierz **punkty końcowe** w **ustawienia** wzdłuż lewej krawędzi strony.

3. Kliknij przycisk **dodać** w górnej części strony.

4. W **dodać punkt końcowy** okna dialogowego wprowadź następujące informacje:

   * **Nazwa**: HTTP
   * **Protokół**: TCP
   * **Port publiczny**: 80
   * **Port prywatny**: 3000
   * **Pływającego adresu PI**: wyłączone
   * **Listy kontroli dostępu — kolejność**: 1001 lub inną wartość, która ustawia priorytet tej reguły dostępu.
   * **Listy kontroli dostępu — nazwa**: allowHTTP
   * **Listy kontroli dostępu — Akcja**: Zezwalaj
   * **Listy kontroli dostępu - podsieci zdalnej**: 1.0.0.0/16

     Ten punkt końcowy ma publiczny port 80, które będzie kierować ruch do port prywatny 3000, gdzie serwer szyny nasłuchuje. Reguły listy kontroli dostępu zezwala na publiczny ruch na porcie 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Kliknij przycisk OK, aby zapisać punktu końcowego.

6. Komunikat powinien zostać wyświetlony stwierdzający **zapisywanie punktu końcowego maszyny wirtualnej**. Gdy ten komunikat nie zniknie, punkt końcowy jest aktywna. Może teraz przetestować aplikację, przechodząc do nazwę DNS maszyny wirtualnej. Witryna sieci Web powinna wyglądać podobnie do następującego:

    ![Domyślna strona szyny][default-rails-cloud]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku jak większość czynności ręcznie. W środowisku produkcyjnym może napisać aplikację na komputerze deweloperskim i wdrożyć go na maszynie Wirtualnej platformy Azure. Ponadto większość środowisk produkcyjnych hostowania aplikacji szyny w połączeniu z innym procesem serwera, takich jak Apache lub NginX, która obsługuje żądania routingu do wielu wystąpień aplikacji szyny i obsługująca zasoby statyczne. Aby uzyskać więcej informacji zobacz http://rubyonrails.org/deploy/.

Aby dowiedzieć się więcej na temat Ruby na szyny, odwiedź stronę [dopisków fonetycznych w przewodnikach szyny][rails-guides].

Aby korzystać z aplikacji dopisków fonetycznych usług Azure, zobacz:

* [Przechowywania danych niestrukturalnych przy użyciu obiektów blob][blobs]
* [Pary klucz wartość magazynu przy użyciu tabel][tables]
* [Udostępniać zawartość dużej przepustowości w sieci dostarczania zawartości][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
