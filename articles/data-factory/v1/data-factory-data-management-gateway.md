---
title: Brama zarządzania danymi w usłudze Data Factory | Dokumentacja firmy Microsoft
description: Konfigurowanie bramy danych do przenoszenia danych między systemem lokalnym i w chmurze. Użyj bramy zarządzania danymi w usłudze Azure Data Factory, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 63b9cc26b927f78598422575646c876d90954bed
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143298"
---
# <a name="data-management-gateway"></a>Brama zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [może być samodzielnie hostowane środowisko IR w](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Brama zarządzania danymi ma zostały teraz przemianowane jako środowiskiem Integration Runtime.

Brama zarządzania danymi to agent klienta, który należy zainstalować w środowisku w środowisku lokalnym, aby skopiować dane między magazynami danych w chmurze i lokalnych. Dane lokalne magazyny obsługiwane przez usługę Data Factory są wymienione w [obsługiwanych źródeł danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) sekcji.

Ten artykuł stanowi uzupełnienie instrukcje przedstawione w [przenoszenie danych między lokalizacją lokalną i chmurą magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W instruktażu utworzysz potok, który używa bramy, aby przenieść dane z lokalnej bazy danych programu SQL Server do obiektu blob platformy Azure. Ten artykuł zawiera szczegółowe informacje szczegółowe na temat bramy zarządzania danymi.

Brama zarządzania danymi można skalować poprzez skojarzenie wielu maszyn w środowisku lokalnym za pomocą bramy. Możesz skalować w górę, zwiększając liczbę zadań przenoszenia danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępna dla logicznej bramy z jednym węzłem. Zobacz [bramy zarządzania danymi skalowanie w usłudze Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artykuł, aby uzyskać szczegółowe informacje.

> [!NOTE]
> Obecnie brama obsługuje tylko działanie kopiowania i działanie procedury składowanej w usłudze Data Factory. Nie jest możliwe użycie bramy z działań niestandardowych dostęp do lokalnych źródeł danych.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
### <a name="capabilities-of-data-management-gateway"></a>Funkcje bramy zarządzania danymi
Brama zarządzania danymi zapewnia następujące możliwości:

* Model lokalnych źródeł danych i źródeł danych w ramach tej samej fabryki danych w chmurze i przenoszenia danych.
* Ma jedną taflę szkła do monitorowania i zarządzania za pomocą wgląd w stan bramy ze strony usługi Data Factory.
* Bezpieczne zarządzanie dostępem do lokalnych źródeł danych.
  * Brak zmian wymagane do firmowej zapory. Brama wykonuje tylko wychodzące połączenia HTTP do otwartej sieci internet.
  * Szyfrowanie poświadczeń dla swoich lokalnych magazynów danych za pomocą certyfikatu.
* Efektywne przenoszenie danych — dane są przesyłane w sposób równoległy, problemy z siecią odporne na tymczasowy z funkcją automatycznego logika ponowień.

### <a name="command-flow-and-data-flow"></a>Polecenie przepływu i przepływu danych
Gdy używasz działania kopiowania do skopiowania danych między systemem lokalnym i w chmurze, działanie korzysta z bramy na przesyłanie danych z lokalnego źródła danych do chmury i na odwrót.

Poniżej przedstawiono przepływ danych wysokiego poziomu dla i podsumowanie kroków związanym z kopiowaniem za pomocą bramy danych: ![Przepływ danych przy użyciu bramy](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Dane i deweloperów tworzy bramę dla usługi Azure Data Factory przy użyciu [witryny Azure portal](https://portal.azure.com) lub [polecenia Cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. Dane i deweloperów tworzy połączonej usługi magazynu danych w środowisku lokalnym za pośrednictwem bramy. W ramach konfigurowania połączoną usługę dane i deweloperów używa aplikacji Ustawianie poświadczeń w celu określenia typów uwierzytelniania i poświadczeń. Okno dialogowe Ustawianie poświadczeń w aplikacji komunikuje się z magazynem danych, aby przetestować połączenie i bramy w celu zapisania poświadczeń.
3. Brama szyfruje poświadczenia za pomocą certyfikatu skojarzonego z tą bramą (dostarczonych przez dane i deweloperów danych), przed zapisaniem poświadczeń w chmurze.
4. Usługa Data Factory komunikuje się z bramą, planowania i zarządzania zadań za pośrednictwem kanału kontroli, który używa kolejki magistrali udostępnionych usług platformy Azure. Gdy zadanie działanie kopiowania musi zostać rozpoczęte, Data Factory kolejki żądania wraz z informacjami o poświadczenia. Brama dotyczącego zadania po sondowania kolejki.
5. Brama odszyfrowuje poświadczenia za pomocą tego samego certyfikatu, a następnie łączy z lokalnym magazynem danych o typie odpowiedniego uwierzytelnienia i poświadczenia.
6. Brama kopiuje dane z lokalnego magazynu do magazynu w chmurze lub na odwrót w zależności od sposobu skonfigurowania działania kopiowania w potoku danych. W tym kroku brama komunikuje się bezpośrednio z usług magazynu w chmurze, takich jak Azure Blob Storage za pośrednictwem bezpiecznego kanału (HTTPS).

### <a name="considerations-for-using-gateway"></a>Zagadnienia dotyczące korzystania z bramy
* Pojedyncze wystąpienie bramy zarządzania danymi może służyć do wielu źródeł danych w środowisku lokalnym. Jednak **pojedyncze wystąpienie bramy jest powiązana z tylko jedną usługę Azure data factory** i nie mogą być udostępniane innym fabryki danych.
* Może mieć **tylko jedno wystąpienie bramy zarządzania danymi** zainstalowane na jednym komputerze. Załóżmy, że masz dwa fabryki danych, które chcą korzystać z lokalnych źródeł danych, musisz zainstalować bramy na dwóch komputerach w środowisku lokalnym. Innymi słowy brama jest powiązany z fabryki danych z konkretnych
* **Bramy nie musi znajdować się na tym samym komputerze co źródło danych**. Jednak masz bramy bliżej źródła danych skraca czas dla bramy do połączenia ze źródłem danych. Zaleca się zainstalowanie bramy na komputerze, który jest inny niż ten, który jest hostem lokalnym źródłem danych. W przypadku bramy i źródła danych na różnych maszynach, brama nie konkurują o zasoby ze źródłem danych.
* Może mieć **wiele bram na różnych maszynach, na nawiązywanie połączeń z tego samego źródła danych w środowisku lokalnym**. Na przykład może mieć dwóch bram obsługująca dwóch fabryki danych, ale tego samego źródła danych lokalnych jest zarejestrowana przy użyciu fabryk danych.
* Jeśli masz już bramę zainstalowanym obsługująca Twojego komputera **usługi Power BI** scenariuszu instalowanie **oddzielnej bramy usługi Azure Data Factory** na innym komputerze.
* Brama musi być używana, nawet wtedy, gdy używasz **ExpressRoute**.
* Traktuj źródła danych jako źródła danych w środowisku lokalnym, (która znajduje się za zaporą) nawet wtedy, kiedy należy używać **ExpressRoute**. Brama umożliwia nawiązanie łączności między usługą i źródła danych.
* Należy najpierw **korzystanie z bramy** nawet, jeśli magazyn danych znajduje się w chmurze na **maszyny Wirtualnej IaaS platformy Azure**.

## <a name="installation"></a>Instalacja
### <a name="prerequisites"></a>Wymagania wstępne
* Obsługiwane **systemu operacyjnego** wersje to Windows 7, systemu Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalacja bramy zarządzania danymi na kontrolerze domeny nie jest obecnie obsługiwane.
* .NET framework 4.5.1 lub nowszej jest wymagana. Jeśli instalujesz bramę na komputerze z Windows 7, należy zainstalować .NET Framework 4.5 lub nowszej. Zobacz [.NET Framework System Requirements](https://msdn.microsoft.com/library/8z6watww.aspx) Aby uzyskać szczegółowe informacje.
* Zalecanym **konfiguracji** bramy maszyna jest co najmniej 2 GHz, 4 rdzenie, 8 GB pamięci RAM i 80 GB dysku.
* Jeśli komputer hosta przechodzi w stan hibernacji, brama nie odpowiada na żądania danych. W związku z tym, skonfiguruj odpowiednie **planu zasilania** na komputerze przed zainstalowaniem bramy. Jeśli komputer jest skonfigurowany do hibernacji, instalacja bramy wyświetli komunikat.
* Musi być administratorem na komputerze, aby zainstalować i skonfigurować bramę zarządzania danymi pomyślnie. Aby dodać kolejnych użytkowników do **bramy zarządzania danymi użytkowników** grupy lokalnej Windows. Członkowie tej grupy będą mogli korzystać **Menedżera konfiguracji bramy zarządzania danych** narzędzie w celu skonfigurowania bramy.

Jako uruchomienia działania kopiowania i tak się stanie w określonej częstotliwości użycia zasobów (procesor CPU, pamięci) na maszynie również zgodna z szczytowe i czasach bezczynności tego samego wzorca. Wykorzystanie zasobów zależy również od intensywnie ilości danych przenoszonych. W przypadku wielu zadań kopiowania w toku, zobaczysz użycia zasobów wzrosną godzinami szczytu.

### <a name="installation-options"></a>Opcje instalacji
Brama zarządzania danymi, można zainstalować w następujący sposób:

* Przez pobranie pakietu Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Można również pliku MSI do uaktualnienia istniejącej bramy zarządzania danymi do najnowszej wersji przy użyciu wszystkich ustawień zachowane.
* Klikając **pobieranie i instalowanie bramy danych** link w obszarze instalacja RĘCZNA lub **zainstalować bezpośrednio na tym komputerze** w ramach instalacji EKSPRESOWEJ. Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku na temat korzystania z instalacji ekspresowej. Czynność ręczna umożliwia przejście do Centrum pobierania. Instrukcje dotyczące pobierania i instalowania bramy z Centrum pobierania znajdują się w następnej sekcji.

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji:
1. Na maszynie hosta dla bramy należy skonfigurować plan zasilania, tak aby maszyny bez hibernacji. Jeśli komputer hosta przechodzi w stan hibernacji, brama nie odpowiada na żądania danych.
2. Utwórz kopię zapasową certyfikatu skojarzonego z tą bramą.

### <a name="install-the-gateway-from-download-center"></a>Instalowanie bramy z Centrum pobierania
1. Przejdź do [stronę pobierania bramy zarządzania danymi firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kliknij przycisk **Pobierz**, wybierz opcję **64-bitowych** wersji (32-bitowych nie jest obsługiwana) i kliknij przycisk **dalej**.
3. Uruchom **MSI** bezpośrednio lub zapisać go w celu dysku twardego i uruchom.
4. Na **powitalnej** wybierz opcję **języka** kliknij **dalej**.
5. **Zaakceptuj** Umowa licencyjna użytkownika oprogramowania i kliknij przycisk **dalej**.
6. Wybierz **folderu** zainstalować bramę, a następnie kliknij przycisk **dalej**.
7. Na **gotowe do zainstalowania** kliknij **zainstalować**.
8. Kliknij przycisk **Zakończ** do ukończenia instalacji.
9. Pobieranie klucza w witrynie Azure portal. Zobacz następną sekcję, aby uzyskać instrukcje krok po kroku.
10. Na **bramy rejestru** strony **Menedżera konfiguracji bramy zarządzania danych** uruchomiony na Twoim komputerze, wykonaj następujące czynności:
    1. Wklej klucz w tekście.
    2. Opcjonalnie kliknij **klucz bramy Show** Aby wyświetlić tekst klucza.
    3. Kliknij przycisk **zarejestrować**.

### <a name="register-gateway-using-key"></a>Rejestrowanie bramy za pomocą klucza
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Jeśli nie utworzono jeszcze logicznej bramy w portalu
Aby utworzyć bramę w portalu i uzyskać klucz z **Konfiguruj** strony, wykonaj kroki z przewodnika w [przenoszenie danych między lokalizacją lokalną i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Jeśli utworzono już logicznej bramy w portalu
1. W witrynie Azure portal, przejdź do **usługi Data Factory** strony, a następnie kliknij przycisk **połączonych usług** kafelka.

    ![Strona fabryki danych](media/data-factory-data-management-gateway/data-factory-blade.png)
2. W **połączonych usług** wybierz logicznej **bramy** utworzone w portalu.

    ![logicznej bramy](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. W **bramy Data Gateway** kliknij **pobieranie i instalowanie bramy danych**.

    ![Pobierz link w portalu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. W **Konfiguruj** kliknij **ponowne tworzenie klucza**. Kliknij przycisk Tak w komunikacie ostrzegawczym zapoznanie się z jej ostrożnie.

    ![Utwórz ponownie klucz](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kliknij przycisk kopiowania obok klucza. Klucz jest kopiowany do Schowka.

    ![Kopiowanie klucza](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony na pasku zadań systemu / powiadomienia
Na poniższej ilustracji przedstawiono niektóre ikony na pasku zadań, które są wyświetlane.

![ikony na pasku zadań systemu](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Jeśli przesuniesz kursor nad komunikat powiadomienia ikonę na pasku zadań systemu możesz zobaczyć szczegółowe informacje o stanie operacji bramy/aktualizacji w oknie podręcznym.

### <a name="ports-and-firewall"></a>Porty i zapory
Istnieją dwa zapór, które należy wziąć pod uwagę: **zaporą firmową** uruchomionego na routerze centralnej w organizacji, i **zapory Windows** skonfigurowany jako demon na maszynie lokalnej, gdy brama jest zainstalowane.

![zapory](./media/data-factory-data-management-gateway/firewalls2.png)

Na poziomie firmowa Zapora należy skonfigurować następujące domeny i portów wychodzących:

| Nazwy domen | Porty | Opis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Używany do komunikacji z zapleczem usługi Data Movement Service |
| *.core.windows.net |443 |Używane z kopiowaniem przejściowa przy użyciu obiektów Blob platformy Azure (jeśli jest skonfigurowane)|
| *.frontend.clouddatahub.net |443 |Używany do komunikacji z zapleczem usługi Data Movement Service |
| *.servicebus.windows.net |9350-9354, 5671 |Opcjonalne usługi Service bus relay za pośrednictwem protokołu TCP używany przez kreatora kopiowania |

Na poziomie zapory Windows te porty wychodzące zwykle są włączone. Nie można skonfigurować w domenach i porty odpowiednio na maszynie bramy.

> [!NOTE]
> 1. Na podstawie Twojego źródła / ujścia, może zajść potrzeba dodatkowe domeny listy dozwolonych adresów i portów wychodzących w zaporze firmowej/Windows.
> 2. Dla niektórych baz danych w chmurze (na przykład: [Usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [usługi Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), itp.), może być konieczne do listy dozwolonych adresu IP maszyny bramy na ich konfigurację zapory.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiowanie danych z magazynu danych źródłowych do magazynu danych ujścia
Upewnij się, że reguły zapory są prawidłowo włączona zapory firmowej zapory Windows na maszynie bramy i sam magazyn danych. Włączenie tych zasad umożliwia bramy nawiązać połączenie z obu źródła i ujścia pomyślnie. Włącz reguły dla każdego magazynu danych, który jest używany w operacji kopiowania.

Na przykład, aby kopiowanie danych z **lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse**, wykonaj następujące czynności:

* Zezwalaj na wychodzące **TCP** komunikację na porcie **1433** dla zapory Windows i zaporą firmową.
* Konfigurowanie ustawień zapory serwera Azure SQL, aby dodać adres IP na maszynie bramy do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na wychodząca przez port 1433, brama nie może uzyskać dostępu usługi Azure SQL bezpośrednio. W takim przypadku można użyć [kopiowania etapowego](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) do usługi Azure SQL Database / magazyn danych SQL Azure. W tym scenariuszu HTTPS (port 443) będzie wymagać tylko w przypadku przenoszenia danych.
>
>

### <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli w środowisku firmowym sieci korzysta z serwera proxy, dostęp do Internetu, należy skonfigurować bramę zarządzania danymi, aby użyć odpowiednie ustawienia serwera proxy. W fazie początkowej rejestracji, można ustawić serwera proxy.

![Ustaw serwer proxy, podczas rejestracji](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brama używa serwera proxy, aby nawiązać połączenie z usługą w chmurze. Kliknij przycisk **zmiany** łącze podczas początkowej konfiguracji. Zostanie wyświetlony **ustawienia serwera proxy** okna dialogowego.

![Zestaw serwera proxy przy użyciu Menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxySettings.png)

Dostępne są trzy opcje konfiguracji:

* **Nie używaj serwera proxy**: Bramy nie jawnie za pomocą dowolnego serwera proxy połączyć z usług w chmurze.
* **Użyj serwera proxy systemu**: Brama używa ustawienia serwera proxy skonfigurowanego w diahost.exe.config i diawp.exe.config. Jeśli żaden serwer proxy jest skonfigurowany w diahost.exe.config i diawp.exe.config, brama łączy się z usługą w chmurze bezpośrednio bez konieczności zwracania się za pośrednictwem serwera proxy.
* **Użyj niestandardowego serwera proxy**: Konfiguruj ustawienia do użycia dla bramy, zamiast korzystać z konfiguracji w diahost.exe.config i diawp.exe.config proxy HTTP. Wymagane są adres i Port. Nazwa użytkownika i hasło są opcjonalne, w zależności od ustawienia uwierzytelniania na serwerze proxy. Wszystkie ustawienia są szyfrowane za pomocą certyfikatu poświadczeń bramy i przechowywane lokalnie na komputerze hosta bramy.

Brama zarządzania danymi usługi hosta powoduje automatyczne ponowne uruchomienie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po brama została pomyślnie zarejestrowana, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, należy użyć Menedżera konfiguracji bramy zarządzania danych.

1. Uruchom **Menedżera konfiguracji bramy zarządzania danych**.
2. Przejdź do karty **Ustawienia**.
3. Kliknij przycisk **zmiany** łącze w **serwer HTTP Proxy** sekcji, aby uruchomić **Ustaw serwer Proxy HTTP** okna dialogowego.
4. Po kliknięciu **dalej** przycisku, zobaczysz okno dialogowe ostrzeżenia z prośbą o Twojej zgody, aby zapisać ustawienia serwera proxy i uruchom ponownie usługę bramy hosta.

Można wyświetlić i zaktualizować serwer proxy HTTP za pomocą narzędzia Configuration Manager.

![Zestaw serwera proxy przy użyciu Menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Jeśli skonfigurowano serwer proxy przy użyciu metody NTLM usługi Host bramy działa w ramach konta domeny. Jeśli zmienisz hasło do konta domeny w dalszej części, pamiętaj, aby zaktualizować ustawienia konfiguracji dla usługi, a następnie uruchom go ponownie w związku z tym. Ze względu na to wymaganie zaleca się, że używasz konta domeny dedykowanego dostępu do serwera proxy, który nie wymaga częstej aktualizacji hasła.
>
>

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy
Jeśli wybierzesz **Użyj systemowego serwera proxy** ustawienia serwera proxy HTTP, brama używa ustawienia diahost.exe.config i diawp.exe.config serwera proxy. Jeśli żaden serwer proxy jest określona w diahost.exe.config i diawp.exe.config, brama łączy się z usługą w chmurze bezpośrednio bez konieczności zwracania się za pośrednictwem serwera proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config.

1. W Eksploratorze plików kliknij kopię bezpieczne *C:\\\\Program Files\\brama zarządzania danymi firmy Microsoft\\2.0\\Shared\\diahost.exe.config* do Utwórz kopię zapasową oryginalnego pliku.
2. Uruchom Notepad.exe uruchomione jako administrator, a następnie otwórz plik tekstowy *C:\\\\Program Files\\brama zarządzania danymi firmy Microsoft\\2.0\\Shared\\ diahost.exe.config*. Możesz znaleźć tag domyślny dla przestrzeni nazw system.net, jak pokazano w poniższym kodzie:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Następnie można dodać szczegółów serwera proxy, jak pokazano w poniższym przykładzie:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu serwera proxy do określania wymaganych ustawień, takich jak scriptLocation. Zapoznaj się [serwera proxy — Element (ustawienia sieci)](https://msdn.microsoft.com/library/sa91de1e.aspx) składni.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracji do oryginalnej lokalizacji, a następnie ponownie uruchom usługę hosta bramy zarządzania danych, która uwzględnia zmiany. Aby ponownie uruchomić usługę: użyj apletu usługi w Panelu sterowania lub z **Menedżera konfiguracji bramy zarządzania danych** > kliknij przycisk **Zatrzymaj usługę** przycisk, a następnie kliknij przycisk **Start Usługa**. Jeśli usługa nie zostanie uruchomiona, istnieje prawdopodobieństwo, że dodano niepoprawną składnię tagu XML w pliku konfiguracji aplikacji, który był edytowany.

> [!IMPORTANT]
> Nie zapomnij zaktualizować **zarówno** diahost.exe.config i diawp.exe.config.

Oprócz tych punktów, należy również upewnić, że Microsoft Azure znajduje się w dozwolonych w firmie. Lista prawidłowych adresów IP platformy Microsoft Azure można pobrać z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Możliwe objawy zapory i serwera proxy problemów związanych z serwerem
Jeśli wystąpią błędy podobne do poniższych, prawdopodobnie z powodu nieprawidłowej konfiguracji zapory lub serwera proxy, który blokuje bramy połączenie z usługą Data Factory do samodzielnego uwierzytelnienia. Można znaleźć w poprzedniej sekcji, aby upewnić się, zapory i serwera proxy są prawidłowo skonfigurowane.

1. Gdy użytkownik próbuje zarejestrować bramę, pojawi się następujący błąd: "Nie można zarejestrować klucz bramy. Zanim spróbujesz ponownie zarejestrować klucz bramy, upewnij się, że brama zarządzania danymi jest w stanie połączono i uruchomiono usługę hosta bramy zarządzania danymi."
2. Po otwarciu programu Configuration Manager, zostanie wyświetlony stan jako "Rozłączono" lub "Łączenie". Podczas przeglądania dzienników zdarzeń Windows, w obszarze "Podgląd zdarzeń" > "Aplikacji i usług dzienniki" > "Brama zarządzania danymi", zostanie wyświetlony błąd komunikaty, takie jak następujący błąd: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otwórz port 8050 na potrzeby szyfrowania poświadczeń
**Ustawianie poświadczeń** aplikacja korzysta z portu wejściowego **8050** do przekazywania poświadczeń do bramy, po skonfigurowaniu lokalnej połączonej usługi w witrynie Azure portal. Podczas instalacji bramy domyślnie instalacja bramy otwiera go na maszynie bramy.

Jeśli używasz zapory innych firm, możesz ręcznie Otwórz port 8050. Jeśli napotkasz problem z zaporą podczas instalacji bramy możesz spróbować zainstalować bramę, bez konieczności konfigurowania zapory przy użyciu następującego polecenia.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Jeśli wybierzesz nie otworzyć port 8050 na maszynie bramy, użyj mechanizmów, innych niż z użyciem **Ustawianie poświadczeń** aplikację, aby skonfigurować poświadczenia magazynu danych. Na przykład można użyć [New AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) polecenia cmdlet programu PowerShell. Zobacz sekcję Ustawianie poświadczeń i zabezpieczeń na sposób przechowywania poświadczeń w danych można ustawić.

## <a name="update"></a>Aktualizacja
Domyślnie brama zarządzania danymi jest automatycznie aktualizowany, gdy dostępna jest nowsza wersja bramy. Brama nie jest aktualizowana, aż wszystkie zaplanowane zadania są wykonywane. Kolejne zadania nie są przetwarzane przez bramę, aż do zakończenia operacji aktualizacji. W przypadku niepowodzenia aktualizacji bramy zostanie wycofana do starej wersji.

Zostanie wyświetlony podczas zaplanowanej aktualizacji w następujących miejscach:

* Strona właściwości bramy w witrynie Azure portal.
* Strona główna programu Data Management Gateway Configuration Manager
* Komunikat powiadomienia na pasku zadań systemu.

Na karcie Narzędzia główne programu Data Management Gateway Configuration Manager Wyświetla harmonogram aktualizacji i czas ostatniego brama została zainstalowana/zaktualizowane.

![Aktualizacje harmonogramu](media/data-factory-data-management-gateway/UpdateSection.png)

Można następnie od razu zainstalować aktualizację lub poczekaj na bramie i automatycznie aktualizowane w zaplanowanym czasie. Na przykład na poniższej ilustracji przedstawiono komunikatu powiadomienia wyświetlane w Menedżerze konfiguracji bramy oraz przycisk Aktualizuj, który można kliknąć, aby natychmiast go zainstalować.

![Aktualizacja programu DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Komunikat powiadomienia w zasobniku systemowym powinien wyglądać jak pokazano na poniższej ilustracji:

![Komunikat na pasku zadań systemu](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Zostanie wyświetlony stan operacji aktualizacji (ręcznych lub automatycznych) na pasku zadań. Podczas uruchamiania Menedżera konfiguracji bramy następnym razem zostanie wyświetlony pasek powiadomień, czy brama została zaktualizowana wraz z linkiem do [co to jest nowy temat](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Aby włączyć/wyłączyć funkcję automatycznej aktualizacji
Można wyłączanie/włączanie funkcji Aktualizacje automatyczne, wykonując następujące czynności:

[Na potrzeby jednego węzła bramy]
1. Uruchom program Windows PowerShell na maszynie bramy.
2. Przełącz się do *C:\\\\Program Files\\produktu Microsoft Integration Runtime\\3.0\\PowerShellScript\\*  folderu.
3. Uruchom następujące polecenie, aby włączyć Aktualizacje automatyczne są wyposażone w OFF (wyłączone).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Aby włączyć ją ponownie:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Bramy o wysokiej dostępności i skalowalności wielowęzłowego](data-factory-data-management-gateway-high-availability-scalability.md)
1. Uruchom program Windows PowerShell na maszynie bramy.
2. Przełącz się do *C:\\\\Program Files\\produktu Microsoft Integration Runtime\\3.0\\PowerShellScript\\*  folderu.
3. Uruchom następujące polecenie, aby włączyć Aktualizacje automatyczne są wyposażone w OFF (wyłączone).

    Dla bramy przy użyciu funkcji wysokiej dostępności wymagany jest dodatkowy param AuthKey.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Aby włączyć ją ponownie:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po zainstalowaniu bramy, można uruchomić Menedżera konfiguracji bramy zarządzania danych w jednym z następujących sposobów:

1. W **wyszukiwania** okna, typ **bramy zarządzania danymi** na dostęp do tego narzędzia.
2. Uruchom plik wykonywalny *ConfigManager.exe* w folderze: *C:\\\\plików programu\\bramy zarządzania danymi firmy Microsoft\\2.0\\udostępnione*.

### <a name="home-page"></a>Strona główna
Strona główna umożliwia wykonywanie następujących czynności:

* Wyświetl stan bramy (połączony z usługą w chmurze itd.).
* **Zarejestruj** przy użyciu klucza z poziomu portalu.
* **Zatrzymaj** i Rozpocznij **usługi danych zarządzania bramy hosta** na maszynie bramy.
* **Zaplanuj aktualizacje** o określonej godzinie w dni.
* Wyświetl datę bramy **Ostatnia aktualizacja:**.

### <a name="settings-page"></a>Strona Ustawienia
Na stronie ustawień umożliwia wykonywanie następujących czynności:

* Wyświetlanie, zmiana i wyeksportować **certyfikatu** używany przez bramę. Ten certyfikat jest używany do szyfrowania poświadczeń źródła danych.
* Zmiana **portu HTTPS** dla punktu końcowego. Brama otwiera port do ustawiania poświadczeń źródła danych.
* **Stan** punktu końcowego
* Widok **certyfikat SSL** używany do komunikacji SSL między portalem i bramy można ustawić poświadczenia dla źródeł danych.

### <a name="remote-access-from-intranet"></a>Dostęp zdalny z intranetu
Ta funkcja zostanie włączona w przyszłości. W przyszłych aktualizacjach (wersji 3.4 lub nowszej) firma Microsoft powiadomi Cię Włącz / Wyłącz połączenia zdalnego, obecnie wykonywanej przy użyciu portu 8050 (zobacz sekcję powyżej), podczas korzystania z aplikacji programu PowerShell lub Menedżera poświadczeń do szyfrowania poświadczeń.

### <a name="diagnostics-page"></a>Strona diagnostyki
Strona Diagnostyka umożliwia wykonywanie następujących czynności:

* Włącz pełne **rejestrowania**, wyświetlić dzienniki w Podglądzie zdarzeń i wysłać dzienniki do firmy Microsoft, jeśli wystąpił błąd.
* **Testuj połączenie** ze źródłem danych.

### <a name="help-page"></a>Strona pomocy
Strona pomocy zawiera następujące informacje:

* Krótki opis bramy
* Numer wersji
* Zawiera łącza do pomocy online zasady zachowania poufności i umowę licencyjną.

## <a name="monitor-gateway-in-the-portal"></a>Monitor bramy w portalu
W witrynie Azure portal można wyświetlić migawki niemal w czasie rzeczywistym wykorzystania zasobów (procesor CPU, pamięci, network(in/out) itp.) na maszynie bramy.

1. W witrynie Azure portal, przejdź do strony głównej fabryki danych, a następnie kliknij przycisk **połączonych usług** kafelka.

    ![Strona główna fabryki danych](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Wybierz **bramy** w **połączonych usług** strony.

    ![Strona połączone usługi](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. W **bramy** stronie widać, pamięci i Procesora CPU bramy.

    ![Użycie Procesora i pamięci bramy](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Włącz **Zaawansowane ustawienia** aby zobaczyć więcej szczegółów, takich jak użycie sieci.
    
    ![Zaawansowane monitorowanie bramy](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

W poniższej tabeli przedstawiono opisy kolumn w **węzły bramy** listy:

Właściwość monitorowania | Opis
:------------------ | :----------
Name (Nazwa) | Nazwa logicznej bramy i węzłów skojarzone z tą bramą. Węzeł to maszyna Windows w środowisku lokalnym, która ma bramę na nim zainstalowany. Aby uzyskać informacji na temat mających więcej niż jeden węzeł (maksymalnie cztery węzły) w jednej logicznej bramy, zobacz [bramy zarządzania danymi — wysokiej dostępności i skalowalności](data-factory-data-management-gateway-high-availability-scalability.md).
Stan | Stan logicznej bramy i węzłów bramy. Przykład: Online/Offline/Limited/itd. Aby uzyskać informacji na temat tych stanów, zobacz [stan bramy](#gateway-status) sekcji.
Wersja | Wyświetla wersję logicznej bramy i każdy węzeł bramy. Wersja logicznej bramy jest określany na podstawie wersji Większość węzłów w grupie. W przypadku węzłów z różnymi wersjami w Instalatorze logicznej bramy tylko węzły numerem wersji funkcji logicznej bramy prawidłowo. Innym jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku, gdy aktualizacje automatyczne nie powiedzie się).
Dostępna pamięć | Dostępna pamięć na węzeł bramy. Ta wartość jest niemal w czasie rzeczywistym migawki.
Użycie procesora CPU | Użycie procesora CPU węzeł bramy. Ta wartość jest niemal w czasie rzeczywistym migawki.
Sieć (We/Wy) | Wykorzystanie sieci węzła bramy. Ta wartość jest niemal w czasie rzeczywistym migawki.
Równoczesne zadania (uruchomione / Limit) | Numer zadania lub podzadania uruchomione w każdym węźle. Ta wartość jest niemal w czasie rzeczywistym migawki. Limit oznacza maksymalna liczba równoczesnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Możesz zwiększyć limit, aby skalować w górę do wykonywania zadań jednoczesnych w zaawansowanych scenariuszach, gdzie Procesora/pamięci/IZ niewykorzystywanych, ale działania przekraczają limit. Ta funkcja jest również dostępna za pośrednictwem bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalności i dostępności).
Rola | Istnieją dwa typy ról w bramie wieloma węzłami - dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich można wszystkie służyć do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do pobierania zadania/zadań z usług cloud services i wysyłać je do innego procesu roboczego węzłów (w tym sam).

Na tej stronie zobaczysz niektóre ustawienia, które będą bardziej zrozumiałe, gdy istnieją co najmniej dwa węzły (skalowanie w poziomie scenariusza) w bramie. Zobacz [bramy zarządzania danymi — wysokiej dostępności i skalowalności](data-factory-data-management-gateway-high-availability-scalability.md) szczegółowe informacje na temat konfigurowania bramy wieloma węzłami.

### <a name="gateway-status"></a>Stan bramy
W poniższej tabeli przedstawiono możliwe stany **węzeł bramy**:

Stan  | Komentarze/scenariusze
:------- | :------------------
Online | Węzeł jest połączony do usługi Data Factory.
Offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowane automatycznie.
Ograniczone | Ze względu na problem z łącznością. Może być spowodowane problemem 8050 portu HTTP, problem łączności z usługą Service bus lub problemu z synchronizacją poświadczeń.
Nieaktywna | Węzeł znajduje się w konfiguracji różni się od konfiguracji innych większości węzłów.<br/><br/> Węzeł może być nieaktywne, gdy nie można nawiązać z innych węzłów.

W poniższej tabeli przedstawiono możliwe stany **logicznej bramy**. Stan bramy zależy od stany węzłów bramy.

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie został jeszcze zarejestrowany na tym logicznej bramy
Online | Węzły bramy są w trybie online
Offline | Nie węzła w stan online.
Ograniczone | Nie wszystkie węzły w tę bramę są w dobrej kondycji. Ten stan jest ostrzeżenie, że może to być jednego z węzłów! <br/><br/>Może wynikać z problemu z synchronizacją poświadczeń na węzeł dyspozytora/procesu roboczego.

## <a name="scale-up-gateway"></a>Skalowanie w górę bramy
Można skonfigurować liczbę **zadania przepływu danych współbieżnych** uruchomioną w węźle, aby skalować w górę możliwości przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych.

Gdy ilość dostępnej pamięci i procesora CPU nie mogą być wykorzystane dobrze, ale bezczynną dyspozycyjność wynosi 0, należy skalowanie w górę, zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Możesz również skalować w górę, gdy działania przekraczają limit, ponieważ brama jest przeciążona. W ustawieniach zaawansowanych węzeł bramy można zwiększyć maksymalną pojemność dla węzła.

## <a name="troubleshooting-gateway-issues"></a>Rozwiązywania problemów z bramą
Zobacz [rozwiązywania problemów z bramą](data-factory-troubleshoot-gateway-issues.md) artykułu, aby uzyskać informacje o/porady dotyczące rozwiązywania problemów z używaniem bramy zarządzania danymi.

## <a name="move-gateway-from-one-machine-to-another"></a>Przenieść bramę z jednego komputera na inny
Ta sekcja zawiera kroki klienta ruchomej bramy z jednego komputera na inny komputer.

1. W portalu, przejdź do **strona główna fabryki danych**i kliknij przycisk **połączonych usług** kafelka.

    ![Data Gateways Link](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Wybierz bramę **bram DATA GATEWAYS** części **połączonych usług** strony.

    ![Połączonej strony usług przy użyciu wybranej bramy](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. W **bramy Data gateway** kliknij **pobieranie i instalowanie bramy danych**.

    ![Brama link pobierania](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. W **Konfiguruj** kliknij **pobieranie i instalowanie bramy danych**i postępuj zgodnie z instrukcjami, aby zainstalować bramę danych na maszynie.

    ![Konfigurowanie strony](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Zachowaj **Menedżera konfiguracji bramy zarządzania danych Microsoft** Otwórz.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. W **Konfiguruj** strony w portalu, kliknij przycisk **ponowne tworzenie klucza** na pasku poleceń, a następnie kliknij **tak** dla komunikat ostrzegawczy. Kliknij przycisk **przycisku kopiowania** obok tekst klucza, który kopiuje klucza do Schowka. Bramy na komputerze w starej zatrzymuje działają, ponieważ wkrótce ponownie utworzyć klucz.

    ![Utwórz ponownie klucz](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Wklej **klucz** w polu tekstowym w **zarejestrować bramę** strony **Menedżera konfiguracji bramy zarządzania danych** na swojej maszynie. (opcjonalnie) Kliknij przycisk **klucz bramy Show** pole wyboru, aby wyświetlić tekst klucza.

    ![Kopiuj klucz i Zarejestruj się](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknij przycisk **zarejestrować** do rejestrowania bramy z usługą w chmurze.
9. Na **ustawienia** kliknij pozycję **zmiany** aby wybrać ten sam certyfikat, który był używany z stara brama, wprowadź **hasło**i kliknij przycisk **Zakończ**.

   ![Określ certyfikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Możesz wyeksportować certyfikat z stara brama, wykonując następujące kroki: Uruchom Menedżera konfiguracji bramy zarządzania dane starej maszyny, przełącz się do **certyfikatu** kliknij pozycję **wyeksportować** przycisk, a następnie postępuj zgodnie z instrukcjami.
10. Po pomyślnej rejestracji bramy, powinien zostać wyświetlony **rejestracji** równa **zarejestrowanej** i **stan** równa **uruchomiono** na stronie głównej Menedżera konfiguracji bramy.

## <a name="encrypting-credentials"></a>Szyfrowania poświadczeń
Aby zaszyfrować poświadczenia w edytorze fabryki danych, wykonaj następujące czynności:

1. Uruchom przeglądarkę sieci web na **maszyny bramy**, przejdź do [witryny Azure portal](https://portal.azure.com). Wyszukiwanie fabryki danych, jeśli to konieczne, otwórz fabryki danych w **usługi DATA FACTORY** strony, a następnie kliknij przycisk **tworzenie i wdrażanie** można uruchomić edytora fabryki danych.
2. Kliknij istniejący **połączoną usługę** w widoku drzewa, aby wyświetlić jego definicja JSON lub tworzenie połączonej usługi, która wymaga bramy zarządzania danymi (na przykład: SQL Server lub Oracle).
3. W edytorze kodu JSON dla **gatewayName** właściwość, wprowadź nazwę bramy.
4. Wprowadź nazwę serwera dla **źródła danych** właściwość **connectionString**.
5. Wprowadź nazwę bazy danych dla **Initial Catalog** właściwość **connectionString**.
6. Kliknij przycisk **Szyfruj** przycisk na pasku poleceń, który uruchamia kliknięcie — po **Menedżera poświadczeń** aplikacji. Powinien zostać wyświetlony **Ustawianie poświadczeń** okno dialogowe.

    ![Ustawienie poświadczeń w oknie dialogowym](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. W **Ustawianie poświadczeń** okna dialogowego pole, wykonaj następujące czynności:
   1. Wybierz **uwierzytelniania** czy chcesz, aby usługa Data Factory do nawiązywania połączenia z bazą danych.
   2. Wprowadź nazwę użytkownika, który ma dostęp do bazy danych dla **USERNAME** ustawienie.
   3. Wprowadź hasło dla użytkownika **hasło** ustawienie.
   4. Kliknij przycisk **OK** do szyfrowania poświadczeń i zamknąć okno dialogowe.
8. Powinien zostać wyświetlony **encryptedCredential** właściwość **connectionString** teraz.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Jeśli uzyskujesz dostęp do portalu z komputera, który różni się od maszynie bramy, upewnij się, że aplikacji Menedżer poświadczeń może połączyć się na maszynie bramy. Jeśli aplikacja nie może połączyć się na maszynie bramy, go nie pozwala ustawić poświadczenia dla źródła danych i do testowania połączenia ze źródłem danych.

Kiedy używasz **Ustawianie poświadczeń w** aplikacji portalu szyfruje poświadczenia za pomocą certyfikatu podanego w **certyfikatu** karcie **Menedżera konfiguracji bramy**  na maszynie bramy.

Jeśli szukasz podejście oparte na interfejsie API w celu szyfrowania poświadczeń można użyć [New AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) polecenia cmdlet programu PowerShell, aby zaszyfrować poświadczenia. Polecenie cmdlet używa certyfikatu że brama jest skonfigurowana na potrzeby szyfrowania poświadczeń. Dodaj zaszyfrowanych poświadczeń w celu **EncryptedCredential** elementu **connectionString** w formacie JSON. Użyj formatu JSON przy użyciu [New AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) polecenia cmdlet lub w edytorze fabryki danych.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Istnieje jedno z podejść więcej do ustawiania poświadczeń przy użyciu edytora fabryki danych. Tworzenie usługi połączonej programu SQL Server za pomocą edytora, a następnie wprowadź poświadczenia w postaci zwykłego tekstu, poświadczenia są szyfrowane przy użyciu certyfikatu, który jest właścicielem usługi Data Factory. Nie używa certyfikatu jaki brama jest skonfigurowana do używania. Chociaż takie podejście może być nieznacznie szybsze w niektórych przypadkach, jest mniej bezpieczna opcja. Dlatego zaleca się, należy wykonać takie podejście, tylko do celów projektowania i testowania.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
W tej sekcji opisano, jak utworzyć i zarejestrować bramę przy użyciu poleceń cmdlet programu Azure PowerShell.

1. Uruchom **programu Azure PowerShell** w trybie administratora.
2. Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie i wprowadź swoje poświadczenia platformy Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Użyj **New AzDataFactoryGateway** polecenie cmdlet do tworzenia logicznej bramy w następujący sposób:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Przykładowe polecenie z danymi wyjściowymi**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. W programie Azure PowerShell przejdź do folderu: *C:\\\\plików programu\\produktu Microsoft Integration Runtime\\3.0\\PowerShellScript\\*. Uruchom *RegisterGateway.ps1* skojarzone ze zmienną lokalnej **$Key** jak pokazano w poniższym poleceniu. Ten skrypt rejestruje agenta klienta, zainstalowane na komputerze z bramą logiczne, utworzonej wcześniej.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Za pomocą parametru IsRegisterOnRemoteMachine można zarejestrować bramę na komputerze zdalnym. Przykład:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Możesz użyć **Get AzDataFactoryGateway** polecenia cmdlet, aby uzyskać listę bram w fabryce danych. Gdy **stan** pokazuje **online**, oznacza to, brama jest gotowa do użycia.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Możesz usunąć za pomocą bramy **AzDataFactoryGateway Usuń** polecenia cmdlet i zaktualizuj opis bramy przy użyciu **AzDataFactoryGateway zestaw** polecenia cmdlet. Informacje o składni i inne szczegóły dotyczące tych poleceń cmdlet Zobacz Data Factory Cmdlet Reference.  

### <a name="list-gateways-using-powershell"></a>Lista bram przy użyciu programu PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Usuwanie bramy za pomocą programu PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W instruktażu utworzysz potok, który używa bramy, aby przenieść dane z lokalnej bazy danych programu SQL Server do obiektu blob platformy Azure.
