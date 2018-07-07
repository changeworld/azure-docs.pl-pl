---
title: Hostowanie na architekturze Ruby on Rails witryny sieci Web na maszynie Wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie i Hostuj Ruby on Rails witryny sieci Web opartej na platformie Azure za pomocą maszyny wirtualnej systemu Linux.
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
ms.openlocfilehash: 6ea1d249b7f9aec3a45923b162a97ce7f83d0d31
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901157"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Aplikacja sieci Web Ruby on Rails na maszynie wirtualnej platformy Azure
Ten samouczek pokazuje, jak hostować Ruby on Rails witryny sieci Web na platformie Azure za pomocą maszyny wirtualnej systemu Linux.  

W tym samouczku została zweryfikowana za pomocą Ubuntu Server 14.04 LTS. Jeśli używasz różnych dystrybucji systemu Linux, użytkownik może się okazać zmodyfikowanie kroki, aby zainstalować platformy Rails.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Tworzenie maszyny Wirtualnej platformy Azure
Rozpocznij od utworzenia maszyny Wirtualnej platformy Azure przy użyciu obrazu systemu Linux.

Aby utworzyć maszynę Wirtualną, można użyć witryny Azure portal lub interfejsu wiersza polecenia platformy Azure (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
2. Kliknij przycisk **Utwórz zasób**, następnie w polu wyszukiwania wpisz "Ubuntu Server 14.04". Kliknij pozycję zwróconego przez wyszukiwanie. W przypadku modelu wdrażania wybierz **klasycznego**, następnie kliknij przycisk "Utwórz".
3. W bloku podstawowe należy podać wartości dla wymaganych pól: Nazwa (dla maszyn wirtualnych), nazwę użytkownika, typ uwierzytelniania i odpowiednie poświadczenia subskrypcji platformy Azure, grupę zasobów i lokalizacji.

   ![Utwórz nowy obraz systemu Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Po aprowizacji maszyny Wirtualnej kliknij nazwę maszyny Wirtualnej i kliknij przycisk **punktów końcowych** w **ustawienia** kategorii. Punkt końcowy SSH, na liście Znajdź **autonomiczny**.

   ![Domyślny punkt końcowy](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Postępuj zgodnie z instrukcjami w [tworzenie maszyny wirtualnej Linux uruchomiony][vm-instructions].

Po aprowizacji maszyny Wirtualnej można uzyskać punkt końcowy SSH, uruchamiając następujące polecenie:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Zainstaluj oprogramowanie Ruby on Rails
1. Łączenie z maszyną wirtualną za pomocą protokołu SSH.
2. W sesji SSH Użyj następujących poleceń do zainstalowania języka Ruby na maszynie Wirtualnej:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Instalacja może zająć kilka minut. Po ukończeniu, użyj następującego polecenia, aby sprawdzić, czy zainstalowano język Ruby:

        ruby -v

3. Aby zainstalować platformy Rails, użyj następującego polecenia:

        sudo gem install rails --no-rdoc --no-ri -V

    Użycie flagi rdoc nie i — wystąpienia zarezerwowanego nie, aby pominąć instalację w dokumentacji, które jest szybsze.
    To polecenie będzie prawdopodobnie długo trwać do wykonania, więc Dodawanie -V spowoduje wyświetlenie informacji o postępie instalacji.

## <a name="create-and-run-an-app"></a>Tworzenie i uruchamianie aplikacji
Wylogowując za pośrednictwem protokołu SSH, uruchom następujące polecenia:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[Nowe](http://guides.rubyonrails.org/command_line.html#rails-new) polecenie tworzy nową aplikację platformy Rails. [Serwera](http://guides.rubyonrails.org/command_line.html#rails-server) polecenie uruchamia serwer sieci web WEBrick, które dołączono szyny. (Do użycia w środowisku produkcyjnym prawdopodobnie chcesz użyć innego serwera, takie jak Unicorn lub pasażera.)

Powinny pojawić się dane wyjściowe podobne do następujących:

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Dodawanie punktu końcowego
1. Przejdź do witryny Azure portal] [https://portal.azure.com] i wybierz maszynę Wirtualną.

2. Wybierz **punktów końcowych** w **ustawienia** wzdłuż lewej krawędzi strony.

3. Kliknij przycisk **Dodaj** w górnej części strony.

4. W **Dodaj punkt końcowy** okna dialogowego strony, wprowadź następujące informacje:

   * **Nazwa**: HTTP
   * **Protokół**: TCP
   * **Port publiczny**: 80
   * **Port prywatny**: 3000
   * **Zmiennoprzecinkowe adres PI**: wyłączone
   * **Listy kontroli dostępu — kolejność**: 1001 lub inną wartość, która ustawia priorytet tej reguły dostępu.
   * **Listy kontroli dostępu — nazwa**: allowHTTP
   * **Listy kontroli dostępu — Akcja**: zezwolenie na
   * **Listy kontroli dostępu — podsieci zdalnej**: 1.0.0.0/16

     Ten punkt końcowy ma publiczny port 80, który będzie kierować ruch do portu prywatnego 3000, której nasłuchuje serwer platformy Rails. Reguły listy kontroli dostępu zezwala na publicznych ruch na porcie 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Kliknij przycisk OK, aby zapisać punktu końcowego.

6. Komunikat powinien pojawić się informacją, że **zapisywanie punktu końcowego maszyny wirtualnej**. Gdy ta wiadomość zniknie, punkt końcowy jest aktywny. Teraz może przetestować aplikację, przechodząc na nazwę DNS maszyny wirtualnej. Witryna sieci Web powinny wyglądać podobnie do następującego:

    ![Domyślna strona platformy rails][default-rails-cloud]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku jak większość czynności ręcznie. W środowisku produkcyjnym czy napisać własną aplikację na komputerze deweloperskim i wdrożyć go na maszynie Wirtualnej platformy Azure. Ponadto większość środowisk produkcyjnych hostowanie aplikacji platformy Rails, w połączeniu z innym procesem serwera, takich jak Apache i NginX, która obsługuje żądania, routing do wielu wystąpień aplikacji platformy Rails i obsługuje zasoby statyczne. Aby uzyskać więcej informacji, zobacz http://guides.rubyonrails.org/routing.html.

Aby dowiedzieć się więcej na temat platformy Ruby on Rails, odwiedź stronę [Ruby on Rails przewodniki][rails-guides].

Aby korzystać z usług platformy Azure z aplikacji języka Ruby, zobacz:

* [Store danych niestrukturalnych przy użyciu obiektów blob][blobs]
* [Pary klucz/wartość Store przy użyciu tabel][tables]
* [Obsługiwać zawartości wymagającej wysokiej przepustowości za pomocą Content Delivery Network][cdn-howto]

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
