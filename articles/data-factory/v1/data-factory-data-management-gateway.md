---
title: Brama zarządzania danymi dla fabryki danych | Dokumentacja firmy Microsoft
description: Skonfiguruj bramę danych do przenoszenia danych między firmą i chmurą. Użyj bramy zarządzania danymi w fabryce danych Azure, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: c1db81594f44f805cf50523b449af62d76099a08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="data-management-gateway"></a>Brama zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [hosta samodzielnego środowiska uruchomieniowego integracji w wersji 2](../create-self-hosted-integration-runtime.md). 

> [!NOTE]
> Brama zarządzania danymi ma teraz zostały rebranded jako Self-hosted integracji w czasie wykonywania.  

Brama zarządzania danymi jest agent klienta, które muszą być zainstalowane w środowisku lokalnym można skopiować dane między chmury i lokalnych magazynów danych. Dane lokalne magazyny obsługiwane przez fabrykę danych są wymienione w [obsługiwanych źródeł danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) sekcji.

W tym artykule uzupełnia wskazówki w [przenoszenie danych między lokalnymi i w chmurze magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W tym przewodnikiem utworzysz potok, który używa bramy do przenoszenia danych z lokalną bazą danych programu SQL Server do obiektów blob platformy Azure. Ten artykuł zawiera szczegółowe informacje szczegółowe na temat bramy zarządzania danymi. 

Możesz skalować w poziomie bramy zarządzania danymi można skojarzyć wielu lokalnymi maszynami z bramą. Możesz skalować w górę zwiększając liczbę zadań przepływu danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępny do logicznego bramy z jednego węzła. Zobacz [skalowanie brama zarządzania danymi w fabryce danych Azure](data-factory-data-management-gateway-high-availability-scalability.md) artykułu, aby uzyskać szczegółowe informacje.

> [!NOTE]
> Obecnie brama obsługuje tylko działania kopiowania i działania procedury składowanej w fabryce danych. Nie jest możliwe użycie bramy z działań niestandardowych dostęp do lokalnych źródeł danych.      

## <a name="overview"></a>Przegląd
### <a name="capabilities-of-data-management-gateway"></a>Funkcje bramy zarządzania danymi
Brama zarządzania danymi oferuje następujące możliwości:

* Model lokalnych źródeł danych i źródeł danych w ramach tej samej fabryki danych w chmurze i przenoszenia danych.
* Ma jednego okienka szkła monitorowania i zarządzania wgląd w stan bramy ze strony fabryki danych.
* Zarządzanie bezpieczny dostęp do lokalnych źródeł danych.
  * Brak zmian wymagane do firmowej zapory. Brama podejmuje tylko połączeń wychodzących oparte na protokole HTTP można otworzyć internet.
  * Szyfrowania poświadczeń dostępu do sieci lokalnych magazynów danych przy użyciu certyfikatu.
* Przenoszenie danych wydajnie — dane są przesyłane równolegle, sieci odporność na sporadyczne problemy z automatycznego Logika ponawiania próby w.

### <a name="command-flow-and-data-flow"></a>Polecenie przepływu i przepływu danych
Gdy działanie kopiowania umożliwia kopiowanie danych między lokalnymi i w chmurze, działanie używa bramy na przesyłanie danych z lokalnego źródła danych do chmury i na odwrót.

Poniżej przedstawiono przepływ danych wysokiego poziomu i podsumowanie kroków kopii danych bramy: ![przepływ danych przy użyciu bramy](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Deweloper danych tworzy bramy dla fabryki danych Azure za pomocą [portalu Azure](https://portal.azure.com) lub [polecenia Cmdlet programu PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. Projektant danych tworzy połączonej usługi magazynu danych lokalnych za pośrednictwem bramy. W ramach konfigurowania połączonej usługi developer danych używa aplikacji ustawienie poświadczeń, aby określić typy uwierzytelniania i poświadczenia.  Okno dialogowe Ustawienia poświadczeń aplikacji komunikuje się z magazynem danych do testowania połączenia i bramę, aby zapisać poświadczenia.
3. Brama szyfruje poświadczenia z certyfikatem skojarzone z tą bramą (dostarczonych przez dewelopera danych), przed zapisaniem poświadczeń w chmurze.
4. Usługi fabryka danych komunikuje się z bramą do planowania i zarządzanie zadań za pośrednictwem kanału kontroli, który używa kolejki magistrali usług Azure udostępnionych. Gdy zadanie działania kopiowania musi zostać rozpoczęte, fabryki danych kolejkuje żądanie wraz z informacjami o poświadczenia. Brama dotyczącego zadania po sondowania kolejki.
5. Brama odszyfrowuje poświadczenia z tego samego certyfikatu, a następnie łączy się z lokalnym magazynem danych typu odpowiedniego uwierzytelnienia i poświadczeń o.
6. Brama kopiuje dane z magazynu lokalnego do magazynu w chmurze, albo na odwrót w zależności od sposobu skonfigurowania działanie kopiowania w potoku danych. W tym kroku bramy komunikuje się bezpośrednio z usług magazynu w chmurze, takich jak magazyn obiektów Blob Azure za pośrednictwem bezpiecznego kanału (HTTPS).

### <a name="considerations-for-using-gateway"></a>Zagadnienia dotyczące korzystania z bramy
* Pojedyncze wystąpienie bramy zarządzania danymi może służyć do wielu źródeł danych lokalnych. Jednak **pojedyncze wystąpienie bramy jest związany z tylko jedną fabryki danych Azure** i nie mogą być udostępniane innym fabryki danych.
* Może mieć **tylko jedno wystąpienie bramy zarządzania danymi** zainstalowane na jednym komputerze. Załóżmy, masz dwie fabryki danych wymagających dostęp do lokalnych źródeł danych, musisz zainstalować bramy na dwóch komputerach lokalnych. Innymi słowy brama jest związany z fabryki danych
* **Bramy musi być na tym samym komputerze jako źródło danych**. Jednak bramy bliżej się ze źródłem danych o skraca czas bramy do nawiązania połączenia ze źródłem danych. Zaleca się zainstalować bramę na komputerze, na którym jest inny niż ten, który obsługuje lokalnego źródła danych. W przypadku bramy i źródła danych na różnych komputerach, brama nie konkurują o zasoby ze źródłem danych.
* Może mieć **wielu bram na różnych komputerach nawiązywania połączenia z tym samym źródle danych lokalnych**. Na przykład może mieć dwóch bram obsługująca dwóch fabryk danych, ale to samo źródło danych lokalnych jest zarejestrowana w usłudze fabryki danych.
* Jeśli masz już bramy zainstalowanej na programu obsługi komputera **usługi Power BI** scenariuszu instalowanie **oddzielnej bramy dla fabryki danych Azure** na innym komputerze.
* Można używać bramy, nawet wtedy, gdy używasz **ExpressRoute**.
* Traktuj źródła danych jako źródło danych lokalnych (który znajduje się za zaporą) nawet jeśli używasz **ExpressRoute**. Użyj bramy do ustanowienia połączenia między usługą i źródła danych.
* Należy **używania bramy** nawet, jeśli magazyn danych jest w chmurze na **maszyny Wirtualnej Azure IaaS**.

## <a name="installation"></a>Instalacja
### <a name="prerequisites"></a>Wymagania wstępne
* Obsługiwane **systemu operacyjnego** wersje to Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalacja brama zarządzania danymi na kontrolerze domeny nie jest obecnie obsługiwana.
* .NET framework 4.5.1 lub nowszy jest wymagany. Jeśli instalujesz bramę na komputerze z systemem Windows 7, zainstaluj program .NET Framework 4.5 lub nowszej. Zobacz [wymagania systemowe programu .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) szczegółowe informacje.
* Zalecanym **konfiguracji** bramy maszyna jest co najmniej 2 GHz 4 rdzenie, 8 GB pamięci RAM i dysk 80 GB.
* Jeśli komputer hosta przejdzie w stan hibernacji, brama nie odpowiada na żądania danych. W związku z tym, skonfigurować odpowiednie **planu zasilania** na komputerze przed zainstalowaniem bramy. Jeśli komputer jest skonfigurowany do hibernacji, instalacja bramy wyświetli komunikat.
* Musi być administratorem na komputerze, aby zainstalować i skonfigurować bramę zarządzania danymi pomyślnie. Można dodać użytkowników do **brama zarządzania danymi użytkowników** lokalnej grupy systemu Windows. Członkowie tej grupy będą mogli korzystać **Menedżera konfiguracji bramy zarządzania danymi** narzędzia do skonfigurowania bramy.

Jak uruchomień działania kopiowania wystąpi na określonej częstotliwości, użycie zasobów (procesora CPU, pamięci) na maszynie również zgodny ze wzorcem tego samego szczytu i czas bezczynności. Wykorzystanie zasobów zależy również od silnie ilości danych jest przenoszony. W przypadku wielu kopii zadania w toku, zostanie wyświetlony użycia zasobów, do góry w godzinach szczytu.

### <a name="installation-options"></a>Opcje instalacji
Brama zarządzania danymi można zainstalować w następujący sposób:

* Pobierając pakiet Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  Plik MSI można również uaktualnić istniejącą bramę zarządzania danymi do najnowszej wersji przy użyciu wszystkich ustawień zachowane.
* Klikając **pobieranie i instalowanie bramy danych** łącze w PODRĘCZNIKU instalacji lub **Zainstaluj bezpośrednio na tym komputerze** w obszarze Ustawienia EXPRESS. Zobacz [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje na temat używania instalacji ekspresowej. Krok wykonywany ręcznie umożliwia przejście do Centrum pobierania.  Instrukcje dotyczące pobierania i instalowania bramy z Centrum pobierania znajdują się w następnej sekcji.

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji:
1. Skonfiguruj planu zasilania na komputerze hosta dla bramy, dzięki czemu komputer nie hibernacji. Jeśli komputer hosta przejdzie w stan hibernacji, brama nie odpowiada na żądania danych.
2. Utwórz kopię zapasową certyfikatu skojarzonego z bramą.

### <a name="install-the-gateway-from-download-center"></a>Zainstaluj bramę w Centrum pobierania
1. Przejdź do [stronę pobierania brama zarządzania danymi firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kliknij przycisk **Pobierz**, wybierz odpowiednią wersję (**32-bitowych** vs. **64-bitowych**) i kliknij przycisk **dalej**.
3. Uruchom **MSI** bezpośrednio lub zapisać go na dysku twardym i uruchom.
4. Na **powitalnej** wybierz pozycję **języka** kliknij **dalej**.
5. **Zaakceptuj** umowę licencyjną użytkownika oprogramowania i kliknij przycisk **dalej**.
6. Wybierz **folderu** zainstalować bramę, a następnie kliknij przycisk **dalej**.
7. Na **gotowe do zainstalowania** kliknij przycisk **zainstalować**.
8. Kliknij przycisk **Zakończ** do ukończenia instalacji.
9. Pobierz klucz z portalu Azure. Zobacz następną sekcję, aby uzyskać instrukcje krok po kroku.
10. Na **bramy rejestru** strony **Menedżera konfiguracji bramy zarządzania danymi** na komputerze, wykonaj następujące czynności:
    1. Wklej klucz w tekście.
    2. Opcjonalnie kliknij **klucz bramy Pokaż** tekst klucza.
    3. Kliknij przycisk **zarejestrować**.

### <a name="register-gateway-using-key"></a>Rejestrowanie bramy przy użyciu klucza
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Jeśli jeszcze nie utworzono logicznej bramy w portalu
Aby utworzyć bramę w portalu i uzyskiwanie klucza z **Konfiguruj** strony, wykonaj kroki z wskazówki w [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Jeśli brama logiczne zostały już utworzone w portalu
1. W portalu Azure, przejdź do **fabryki danych** , a następnie kliknij przycisk **połączonych usług** kafelka.

    ![Strona fabryki danych](media/data-factory-data-management-gateway/data-factory-blade.png)
2. W **połączonych usług** wybierz logicznym **bramy** utworzone w portalu.

    ![logiczne bramy](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. W **bramy danych** kliknij przycisk **pobieranie i instalowanie bramy danych**.

    ![Pobierz łącze w portalu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. W **Konfiguruj** kliknij przycisk **ponowne tworzenie klucza**. Kliknij przycisk Tak w komunikacie ostrzegawczym po odczytaniu jej starannie.

    ![Ponowne tworzenie klucza](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kliknij przycisk Kopiuj obok klucza. Klucz jest kopiowany do Schowka.

    ![Kopiowanie klucza](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony systemowe na pasku zadań / powiadomienia
Na poniższej ilustracji przedstawiono niektóre ikony na pasku zadań, które są wyświetlane.

![ikony systemowe na pasku zadań](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Jeśli Przenieś kursor nad komunikat ikona/powiadomień na pasku zadań systemu, zobaczysz szczegółowe informacje o stanie operacji bramy/aktualizacji w oknie podręcznym.

### <a name="ports-and-firewall"></a>Porty i zapory
Istnieją dwa zapory, należy wziąć pod uwagę: **firmowej zapory** uruchomionego na routerze centralnej organizacji, i **zapory systemu Windows** skonfigurowany jako demon na komputerze lokalnym, w którym zainstalowano bramę.  

![zapory](./media/data-factory-data-management-gateway/firewalls2.png)

Na poziomie firmowa Zapora należy skonfigurować następujące domeny i portów wychodzących:

| Nazwy domen | Porty | Opis |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.core.windows.net |443 |Używany do kopiowania przejściowa przy użyciu obiektów Blob platformy Azure (jeśli jest skonfigurowane)|
| *.frontend.clouddatahub.net |443 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.servicebus.windows.net |9350-9354, 5671 |Przekaźnik magistrali usługi opcjonalne za pośrednictwem protokołu TCP używanego przez kreatora kopiowania |


Na poziomie zapory systemu Windows te porty wyjściowe zwykle są włączone. Nie można skonfigurować w domenach i porty odpowiednio na maszynie bramy.

> [!NOTE]
> 1. Na podstawie Twojego źródła / wychwytywanie, może zajść potrzeba dodatkowe domeny listy dozwolonych adresów IP i portów wychodzących w sieci firmowej/Zapora systemu Windows.
> 2. Dla niektórych baz danych w chmurze (na przykład: [bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [usługi Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), itd.), może być konieczne dozwolonych adres IP komputera bramy na ich konfiguracji zapory.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiowanie danych z magazynu danych źródła do ujścia magazynu danych
Upewnij się, że reguły zapory są włączone prawidłowo w firmowej zapory, Zapora systemu Windows na komputerze bramy i magazyn danych sam. Włączenie tych zasad umożliwia bramy do łączenia się zarówno źródła i sink pomyślnie. Włącz reguły dla każdego magazynu danych, który jest używany w operacji kopiowania.

Na przykład do kopiowania z **z lokalnym magazynem danych do zbiornika bazy danych SQL Azure lub ujścia magazynu danych SQL Azure**, wykonaj następujące czynności:

* Zezwalaj na wychodzące **TCP** komunikację za pośrednictwem portu **1433** dla zapory systemu Windows i firmowej zapory.
* Skonfiguruj ustawienia zapory serwera Azure SQL, aby dodać adres IP komputera bramy do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na wychodzący port 1433, brama nie bezpośredni dostęp Azure SQL. W takim przypadku można użyć [przemieszczane kopiowania](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) do bazy danych SQL Azure / SQL Azure DW. W tym scenariuszu do przenoszenia danych są tylko wymagane HTTPS (port 443).
>
>


### <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli firmowej środowisko sieciowe używa serwera proxy w celu uzyskania dostępu do Internetu, należy skonfigurować bramę zarządzania danymi do użycia odpowiednie ustawienia serwera proxy. Podczas fazy początkowe rejestracyjny można ustawić serwera proxy.

![Zestaw proxy podczas rejestracji](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brama używa serwera proxy do nawiązania połączenia usługi w chmurze. Kliknij przycisk **zmiany** łącze podczas instalacji początkowej. Zostanie wyświetlony **ustawienie serwera proxy** okna dialogowego.

![Zestaw serwera proxy przy użyciu Menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxySettings.png)

Dostępne są trzy opcje konfiguracji:

* **Nie używaj serwera proxy**: bramy nie jawnie używać dowolnego serwera proxy do łączenia z usług w chmurze.
* **Użyj serwera proxy systemu**: Brama używa serwera proxy, ustawienie skonfigurowane w diahost.exe.config i diawp.exe.config.  Jeśli żadnego serwera proxy jest skonfigurowany w diahost.exe.config i diawp.exe.config, bramy łączy się z usługą w chmurze bezpośrednio, bez przechodzenia przez serwer proxy.
* **Użyć niestandardowego serwera proxy**: Konfiguruj ustawienia do użycia dla bramy, zamiast konfiguracje diahost.exe.config i diawp.exe.config proxy HTTP.  Wymagane są adres i Port.  Nazwa użytkownika i hasło są opcjonalne, w zależności od ustawienia uwierzytelniania serwer proxy.  Wszystkie ustawienia są zaszyfrowane za pomocą certyfikat poświadczeń bramy i przechowywane lokalnie na komputerze hosta bramy.

Usługa hosta bramy zarządzania danymi do automatycznego uruchomienia po zapisaniu zaktualizowanych ustawień serwera proxy.

Po brama została pomyślnie zarejestrowana, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, należy użyć Menedżera konfiguracji bramy zarządzania danymi.

1. Uruchom **Menedżera konfiguracji bramy zarządzania danymi**.
2. Przejdź do karty **Ustawienia**.
3. Kliknij przycisk **zmiany** łącze w **serwer HTTP Proxy** sekcji, aby uruchomić **ustawiony serwer Proxy HTTP** okna dialogowego.  
4. Po kliknięciu **dalej** przycisku, zobacz okno dialogowe ostrzeżenia pytania o uprawnienia zapisać ustawienie serwera proxy i ponownie uruchom usługę hosta bramy.

Można wyświetlać i aktualizować serwer proxy HTTP za pomocą narzędzia Configuration Manager.

![Zestaw serwera proxy przy użyciu Menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Jeśli skonfigurowano serwer proxy z uwierzytelnianiem NTLM usługa hosta bramy jest uruchamiana na koncie domeny. Jeśli zmienisz hasło dla konta domeny później, pamiętaj, aby zaktualizować ustawienia konfiguracji dla usługi i odpowiednio uruchom ją ponownie. Ze względu na to wymaganie zaleca się, że używasz konta domeny dedykowanego serwera proxy, który nie wymaga często zaktualizuj hasło dostępu do.
>
>

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy
W przypadku wybrania **użycia serwera proxy systemu** ustawienie serwera proxy HTTP, brama używa ustawienie diahost.exe.config i diawp.exe.config serwera proxy.  Jeśli w diahost.exe.config i diawp.exe.config określono żadnego serwera proxy, bramy łączy się z usługą w chmurze bezpośrednio, bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizacji pliku diahost.exe.config.  

1. W Eksploratorze plików wykonaj kopię bezpieczne C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared\diahost.exe.config Aby utworzyć kopię zapasową oryginalnego pliku.
2. Uruchamianie Notepad.exe uruchomioną jako administrator, a następnie otwórz plik tekstowy "C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared\diahost.exe.config. Domyślny znacznik można znaleźć system.net zgodnie z poniższym kodem:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Następnie można dodać szczegóły serwera proxy, jak pokazano w poniższym przykładzie:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Dodatkowe właściwości są dozwolone wewnątrz tagu serwera proxy do określenia wymagane ustawienia, takie jak scriptLocation. Zapoznaj się [serwera proxy elementu (ustawienia sieciowe)](https://msdn.microsoft.com/library/sa91de1e.aspx) na składni.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Zapisz plik konfiguracji do oryginalnej lokalizacji, a następnie ponownie uruchom usługę hosta bramy zarządzania danymi, które przejmuje zmiany. Aby ponownie uruchomić usługę: użyj apletu usługi w Panelu sterowania lub z **Menedżera konfiguracji bramy zarządzania danymi** > kliknij **Zatrzymaj usługę** przycisk, a następnie kliknij przycisk **Uruchom usługę**. Jeśli usługa nie zostanie uruchomiona, istnieje prawdopodobieństwo, że dodano niepoprawną składnię tagu XML w pliku konfiguracji aplikacji, który był edytowany.

> [!IMPORTANT]
> Pamiętaj zaktualizować **zarówno** diahost.exe.config i diawp.exe.config.  


Oprócz tych punktów należy również upewnij się, że jest dozwolonych w firmie Microsoft Azure. Lista prawidłowych adresów IP firmy Microsoft Azure można pobrać z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Symptomów zapory i serwera proxy problemów związanych z serwerami
Jeśli wystąpią błędy podobne do następujących pól, prawdopodobnie z powodu nieprawidłowej konfiguracji zapory lub serwera proxy serwera, która blokuje bramy połączenie z fabryki danych do samodzielnego uwierzytelnienia. Zapoznaj się z poprzedniej sekcji, aby upewnić się, zapory i serwera proxy są poprawnie skonfigurowane.

1. Podczas próby zarejestrowania bramy, zostanie wyświetlony następujący błąd: "nie można zarejestrować klucz bramy. Zanim spróbujesz ponownie zarejestrować klucz bramy, upewnij się, że brama zarządzania danymi jest w stanie połączenia i usługa hosta bramy zarządzania danymi została uruchomiona."
2. Po otwarciu Menedżera konfiguracji zostanie wyświetlony stan jako "Rozłączono" lub "Łączenie." Podczas przeglądania dzienników zdarzeń systemu Windows, w obszarze "Podglądu zdarzeń" > "I usługi Dzienniki aplikacji" > "Brama zarządzania danymi", można wyświetlić komunikaty o błędach, takich jak następujący błąd: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otwórz port 8050 na potrzeby szyfrowania poświadczeń
**Ustawienie poświadczeń** aplikacja korzysta z portu wejściowego **8050** do przekazywania poświadczeń do bramy, podczas konfigurowania usługi lokalnej połączone w portalu Azure. Podczas instalacji bramy domyślnie instalacja bramy powoduje otwarcie go na komputerze bramy.

Jeśli używasz zapory innych firm, można ręcznie Otwórz port 8050. Jeśli napotkasz problem zapory podczas instalacji bramy, możesz spróbować za pomocą następującego polecenia do instalacji bramy bez konfiguracji zapory.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Jeśli wybierzesz nie otworzyć port 8050 na komputerze bramy, użyj mechanizmów innych niż z użyciem **ustawienie poświadczeń** aplikacji w celu skonfigurowania poświadczeń magazynu danych. Na przykład można użyć [AzureRmDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/mt603802.aspx) polecenia cmdlet programu PowerShell. Zobacz [ustawienie poświadczeń i zabezpieczeń](#set-credentials-and-securityy) sekcję na temat sposobu poświadczeń magazynu danych można ustawić.

## <a name="update"></a>Aktualizacja
Domyślna brama zarządzania danymi jest automatycznie aktualizowany, gdy dostępna jest nowsza wersja bramy. Brama nie jest aktualizowana, aż wszystkie zaplanowane zadania są wykonywane. Kolejne zadania są przetwarzane przez bramę, aż do zakończenia operacji aktualizacji. W przypadku niepowodzenia aktualizacji bramy jest przywracana starej wersji.

Zostanie wyświetlony podczas zaplanowanej aktualizacji w następujących miejscach:

* Strona właściwości bramy w portalu Azure.
* Strona główna programu Menedżera bramy zarządzania danymi konfiguracji
* Komunikat powiadomienia na pasku zadań systemu.

Na karcie Strona główna z danych Menedżera konfiguracji bramy zarządzania Wyświetla harmonogram aktualizacji i czas ostatniego bramy zostało zainstalowane zaktualizowane.

![Aktualizacje harmonogramu](media/data-factory-data-management-gateway/UpdateSection.png)

Można zainstalować aktualizację od razu lub zaczekaj na bramy, które mają być aktualizowane automatycznie w zaplanowanym terminie. Na przykład na poniższej ilustracji przedstawiono komunikatu powiadomienia wyświetlane w Menedżerze konfiguracji bramy oraz przycisk Aktualizuj, które można kliknąć, aby natychmiast zainstalować.

![Aktualizacja w DMG programu Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Na pasku zadań powiadomienie będzie wyglądać jak pokazano na poniższej ilustracji:

![Komunikat na pasku zadań systemu](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Możesz wyświetlić stan operacji aktualizacji (ręczne lub automatyczne) na pasku zadań. Po następnym uruchomieniu Menedżera konfiguracji bramy został wyświetlony komunikat na pasku powiadomień o zaktualizowaniu bramy wraz z linkiem do [co to jest nowy temat](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Aby włączyć/wyłączyć funkcję automatycznej aktualizacji
Użytkownik może Włącz/Wyłącz funkcję automatycznej aktualizacji, wykonując następujące czynności:

[Dla jednego węzła bramy]
1. Uruchom program Windows PowerShell na komputerze bramy.
2. Przejdź do folderu C:\Program Files\Microsoft integracji Runtime\3.0\PowerShellScript\.
3. Uruchom następujące polecenie, aby włączyć automatyczną aktualizację funkcji OFF (wyłączone).   

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off
    ```
4. Aby włączyć ją ponownie:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on  
    ```
[Wielowęzłowego wysoką dostępność i skalowalność bramy (wersja zapoznawcza)](data-factory-data-management-gateway-high-availability-scalability.md)
1. Uruchom program Windows PowerShell na komputerze bramy.
2. Przejdź do folderu C:\Program Files\Microsoft integracji Runtime\3.0\PowerShellScript\.
3. Uruchom następujące polecenie, aby włączyć automatyczną aktualizację funkcji OFF (wyłączone).   

    Bramy o wysokiej dostępności funkcji (wersja zapoznawcza) wymagany jest dodatkowy param AuthKey.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Aby włączyć ją ponownie:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po zainstalowaniu bramy, można uruchomić Menedżera konfiguracji bramy zarządzania danymi w jednym z następujących sposobów:

1. W **wyszukiwania** wpisz **brama zarządzania danymi** można uzyskać dostępu do tego narzędzia.
2. Uruchom plik wykonywalny **ConfigManager.exe** w folderze: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared**

### <a name="home-page"></a>Strona główna
Strona główna umożliwia wykonywanie następujących czynności:

* Wyświetl stan bramy (podłączone do usługi w chmurze itp.).
* **Zarejestruj** przy użyciu klucza z portalu.
* **Zatrzymaj** i uruchomić **usługi hosta bramy zarządzania danymi** na komputerze bramy.
* **Zaplanuj aktualizacje** o określonej godzinie dni.
* Wyświetlić datę bramy **ostatniej aktualizacji**.

### <a name="settings-page"></a>Strona Ustawienia
Strona Ustawienia umożliwia wykonywanie następujących czynności:

* Wyświetlanie, zmienianie i eksportowanie **certyfikatu** używany przez bramę. Ten certyfikat jest używany do szyfrowania poświadczeń źródła danych.
* Zmień **HTTPS port** dla punktu końcowego. Brama otwiera port ustawienie poświadczeń źródła danych.
* **Stan** punktu końcowego
* Widok **certyfikat SSL** jest używany do komunikacji SSL między portalu i bramy można ustawić poświadczenia dla źródeł danych.  

### <a name="remote-access-from-intranet"></a>Dostęp zdalny z sieci intranet  
Ta funkcja zostanie włączona w przyszłości. W kolejnych aktualizacji (v3.4 lub nowszym) firma Microsoft będzie można włączyć / wyłączyć połączenia zdalnego, obecnie wykonywanej przy użyciu portu 8050 (patrz sekcja powyżej) podczas używania aplikacji programu PowerShell lub Menedżera poświadczeń do szyfrowania poświadczeń. 

### <a name="diagnostics-page"></a>Strony diagnostyki
Strona Diagnostyka umożliwia wykonywanie następujących czynności:

* Włącz pełne **rejestrowania**, sprawdź dzienniki w Podglądzie zdarzeń i wysyłania dzienników do firmy Microsoft, jeśli wystąpił błąd.
* **Połączenie testowe** ze źródłem danych.  

### <a name="help-page"></a>Strona pomocy
Strona pomocy zawiera następujące informacje:  

* Krótki opis bramy
* Numer wersji
* Łącza do pomocy online zasady zachowania poufności informacji i umowy licencyjnej.  

## <a name="monitor-gateway-in-the-portal"></a>Monitor bramy w portalu
W portalu Azure można wyświetlić migawki czasie niemal rzeczywistym wykorzystania zasobów (procesora CPU, pamięci, network(in/out) itp.) na maszynie bramy.  

1. W portalu Azure, przejdź do strony głównej fabrykę danych, a następnie kliknij przycisk **połączone usługi** kafelka. 

    ![Strona główna fabryki danych](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Wybierz **bramy** w **połączone usługi** strony.

    ![Strona połączone usługi](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. W **bramy** strony widać pamięci i Procesora CPU bramy.

    ![Wykorzystanie Procesora i pamięci bramy](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Włącz **Zaawansowane ustawienia** aby zobaczyć więcej szczegółów, takich jak użycie sieci.
    
    ![Zaawansowane monitorowanie bramy](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Poniższa tabela zawiera opisy kolumn w **węzłów bramy** listy:  

Właściwość monitorowania | Opis
:------------------ | :---------- 
Name (Nazwa) | Nazwa bramy logiczne i skojarzone z tą bramą węzłów. Węzeł jest na lokalnej maszynie systemu Windows, które zainstalowano na nim bramę. Uzyskać informacji o mających więcej niż jeden węzeł (maksymalnie czterech węzłów) w jednej bramy logiczne, zobacz [brama zarządzania danymi - wysokiej dostępności i skalowalności](data-factory-data-management-gateway-high-availability-scalability.md).    
Stan | Stan bramy logicznych i węzłów bramy. Przykład: Online/Offline/Limited/itp. Informacje o tych stanów znajdują się w temacie [stanu bramy](#gateway-status) sekcji. 
Wersja | Wyświetlana jest wersja bramy logicznych i każdy węzeł bramy. Wersja bramy logicznej jest określana na podstawie wersji Większość węzłów w grupie. Jeśli ma prawidłowo węzłów z różnych wersji w konfiguracji bramy logiczne, tylko węzły z tym samym numerem wersji funkcji logicznej bramy. Inne osoby włączony jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku automatycznej aktualizacji nie powiedzie się). 
Dostępna pamięć | Dostępna pamięć na węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Użycie procesora CPU | Użycie procesora CPU przez węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Sieć (We/Wy) | Użycie sieci węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Równoczesnych zadań (systemem / Limit) | Liczba zadań lub zadania uruchomione w każdym węźle. Ta wartość jest blisko migawka w czasie rzeczywistym. Limit oznacza maksymalny współbieżnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Można zwiększyć limit skalowanie w górę wykonywania zadań jednoczesnych w zaawansowanych scenariuszach, w których procesora CPU i pamięci/sieci jest używane w obszarze, ale limit czasu są działania. Ta funkcja jest również dostępna przy użyciu bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalność i dostępność).  
Rola | Istnieją dwa typy ról wielowęzłowego bramy — dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich wszystkich służy do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który służy do pobierania zadania/zadań z usługi w chmurze i wysyłania ich do węzłów innego procesu roboczego (w tym sam).

Na tej stronie zobacz temat niektórych ustawień, które będą bardziej zrozumiałe, gdy istnieją co najmniej dwa węzły (skalowanie w poziomie scenariusz) w bramie. Zobacz [brama zarządzania danymi - wysokiej dostępności i skalowalności](data-factory-data-management-gateway-high-availability-scalability.md) szczegółowe informacje na temat konfigurowania bramy wieloma węzłami.

### <a name="gateway-status"></a>Stan bramy
W poniższej tabeli przedstawiono możliwe stany **węzeł bramy**: 

Stan  | Komentarz/scenariuszy
:------- | :------------------
Online | Węzeł jest podłączony do usługi fabryki danych.
Offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowane automatycznie.
Ograniczone | Z powodu problemu z łącznością. Może być z powodu problemu 8050 port HTTP, problem dotyczący łączności magistrali usługi lub problemu z synchronizacją poświadczeń. 
Nieaktywna | Węzeł znajduje się w różnych konfiguracji innych węzłów większość konfiguracji.<br/><br/> Węzeł może być nieaktywne, gdy nie można połączyć do innych węzłów. 


W poniższej tabeli przedstawiono możliwe stany **logicznej bramy**. Stan bramy zależy od stany węzłów bramy. 

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowana dla tej bramy logiczne
Online | Węzły bramy są w trybie online
Offline | Nie węzła w stan online.
Ograniczone | Nie wszystkie węzły w tej bramy są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzeł może być wyłączony! <br/><br/>Może wynikać z problemu z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy. 

## <a name="scale-up-gateway"></a>Skalowanie w górę bramy
Można skonfigurować liczbę **zadania przepływu danych równoczesnych** który można uruchomić w węźle na skalowanie w górę możliwość przenoszenia danych między lokalnymi i w chmurze magazynów danych. 

Gdy ilość dostępnej pamięci i Procesora nie są używane również, ale pojemność bezczynności wynosi 0, należy skalowanie w górę zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Można również skalować podczas działania są limit czasu, ponieważ brama jest przeciążona. W ustawieniach zaawansowanych węzeł bramy może zwiększyć maksymalną pojemność dla węzła. 
  

## <a name="troubleshooting-gateway-issues"></a>Rozwiązywanie problemów z bramy
Zobacz [problemów bramy](data-factory-troubleshoot-gateway-issues.md) artykuł, aby informacje/porady dotyczące rozwiązywania problemów z przy użyciu bramy zarządzania danymi.  

## <a name="move-gateway-from-one-machine-to-another"></a>Przenieść bramy z jednego komputera na inny
Ta sekcja zawiera kroki przenoszenie klienta bramy z jednego komputera na inny komputer.

1. W portalu, przejdź do **strony głównej fabryki danych**i kliknij przycisk **połączonych usług** kafelka.

    ![Łącze bram danych](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Wybierz bramy w **BRAM danych** sekcji **połączonych usług** strony.

    ![Połączonej strony usług z wybranej bramy](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. W **bramy danych** kliknij przycisk **pobieranie i instalowanie bramy danych**.

    ![Pobierz łącze bramy](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. W **Konfiguruj** kliknij przycisk **pobieranie i instalowanie bramy danych**i postępuj zgodnie z instrukcjami, aby zainstalować bramę danych na tym komputerze.

    ![Konfigurowanie strony](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Zachowaj **Menedżera konfiguracji bramy zarządzania danymi firmy Microsoft** otworzyć.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. W **Konfiguruj** w portalu kliknij pozycję **ponowne tworzenie klucza** na pasku poleceń, a następnie kliknij **tak** dla komunikat ostrzegawczy. Kliknij przycisk **przycisku Kopiuj** obok tekstu klucza, który kopiuje do Schowka klucz. Bramy na maszynie starego awarii jak najszybciej ponownie utwórz klucz.  

    ![Ponowne tworzenie klucza](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Wklej **klucza** w polu tekstowym w **Zarejestruj bramę** strony **Menedżera konfiguracji bramy zarządzania danymi** na tym komputerze. (opcjonalnie) Kliknij przycisk **klucz bramy Pokaż** pole wyboru, aby wyświetlić tekst klucza.

    ![Kopiowanie klucza i rejestrowanie](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknij przycisk **zarejestrować** do rejestrowania bramy z usługą w chmurze.
9. Na **ustawienia** , kliknij pozycję **zmiany** aby wybrać sam certyfikat, który był używany z starego bramy, wprowadź **hasło**i kliknij przycisk **Zakończ**.

   ![Określ certyfikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Można wyeksportować certyfikat z starego bramy, wykonując następujące kroki: Uruchom Menedżera konfiguracji bramy zarządzania danymi na maszynie stare, przełącz się do **certyfikatu** , kliknij pozycję **wyeksportować** przycisk i postępuj zgodnie z instrukcjami.
10. Po pomyślnej rejestracji bramy, powinna zostać wyświetlona **rejestracji** ustawioną **zarejestrowanej** i **stan** ustawioną **uruchomiono** na stronie głównej Menedżera konfiguracji bramy.

## <a name="encrypting-credentials"></a>Szyfrowania poświadczeń
Aby zaszyfrować poświadczenia Edytor fabryki danych, wykonaj następujące czynności:

1. Uruchamianie przeglądarki sieci web na **maszyna bramy**, przejdź do [portalu Azure](http://portal.azure.com). Wyszukaj fabrykę danych, jeśli to konieczne, otwórz fabryki danych w **FABRYKI danych** a następnie kliknij przycisk **tworzenie i wdrażanie** można uruchomić Edytor fabryki danych.   
2. Kliknij istniejący **połączona usługa** w widoku drzewa, aby wyświetlić jego definicji JSON lub tworzenie połączonej usługi, która wymaga brama zarządzania danymi (na przykład: SQL Server lub Oracle).
3. W edytorze JSON dla **gatewayName** właściwości, wprowadź nazwę bramy.
4. Wprowadź nazwę serwera dla **źródła danych** właściwości w **connectionString**.
5. Wprowadź nazwę bazy danych dla **wykazu początkowego** właściwości w **connectionString**.    
6. Kliknij przycisk **Szyfruj** przycisk na pasku poleceń, uruchamianą kliknięcie — po **Menedżera poświadczeń** aplikacji. Powinny pojawić się **ustawienie poświadczeń** okno dialogowe.

    ![Ustawienie poświadczeń w oknie dialogowym](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. W **ustawienie poświadczeń** okna dialogowego wykonaj następujące czynności:
   1. Wybierz **uwierzytelniania** , które mają usługi fabryka danych na potrzeby połączenia z bazą danych.
   2. Wprowadź nazwę użytkownika, który ma dostęp do bazy danych dla **USERNAME** ustawienie.
   3. Wprowadź hasło dla użytkownika na potrzeby **hasło** ustawienie.  
   4. Kliknij przycisk **OK** do szyfrowania poświadczeń i zamknąć okno dialogowe.
8. Powinny pojawić się **encryptedCredential** właściwości w **connectionString** teraz.

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
Jeśli uzyskujesz dostęp do portalu z komputera, który różni się od maszyna bramy, należy się upewnić, że aplikacji Menedżer poświadczeń może połączyć się z komputerem bramy. Jeśli aplikacja nie może połączyć się z maszyną bramy, nie zezwala można ustawić poświadczenia dla źródła danych i przetestować połączenie ze źródłem danych.  

Jeśli używasz **ustawienie poświadczeń** aplikacji portalu szyfruje poświadczenia z certyfikatu podanego w **certyfikatu** karty **Menedżera konfiguracji bramy** na komputerze bramy.

Jeśli szukasz oparty na interfejsach API podejście do szyfrowania poświadczeń, można użyć [AzureRmDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/mt603802.aspx) polecenia cmdlet programu PowerShell do szyfrowania poświadczeń. Polecenie cmdlet używa certyfikatu, że brama jest skonfigurowana na potrzeby szyfrowania poświadczeń. Dodaj zaszyfrowane poświadczenia do **EncryptedCredential** elementu **connectionString** w formacie JSON. Użyj formatu JSON z [AzureRmDataFactoryLinkedService nowy](https://msdn.microsoft.com/library/mt603647.aspx) polecenia cmdlet lub Edytor fabryki danych.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Jest jednym z podejść więcej do ustawiania poświadczeń przy użyciu Edytor fabryki danych. Tworzenie usługi SQL Server połączone za pomocą edytora, a następnie wprowadź poświadczenia w postaci zwykłego tekstu, poświadczenia są szyfrowane przy użyciu certyfikatu, który jest właścicielem usługi fabryka danych. Nie używa certyfikatu że brama jest skonfigurowana do użycia. Takie podejście może być nieco szybsze w niektórych przypadkach, jest mniej bezpieczne. Dlatego zaleca się, należy wykonać takie podejście tylko na potrzeby tworzenia/testowania.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
W tej sekcji opisano sposób tworzenia i zarejestruj bramę przy użyciu poleceń cmdlet programu Azure PowerShell.

1. Uruchom **programu Azure PowerShell** w trybie administratora.
2. Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie i wprowadzając poświadczenia platformy Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```
3. Użyj **AzureRmDataFactoryGateway nowy** polecenia cmdlet, aby utworzyć bramę logicznych w następujący sposób:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Przykład polecenia i dane wyjściowe**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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

1. W programie Azure PowerShell, przejdź do folderu: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\PowerShellScript\**. Uruchom **RegisterGateway.ps1** skojarzone z zmiennej lokalnej **$Key** jak pokazano w poniższym poleceniu. Ten skrypt rejestruje agenta klienta zainstalowanej na komputerze z bramą logicznego utworzonych wcześniej.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Za pomocą parametru IsRegisterOnRemoteMachine można zarejestrować bramę na komputerze zdalnym. Przykład:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Można użyć **Get-AzureRmDataFactoryGateway** polecenia cmdlet, aby uzyskać listę bram w fabryce danych. Gdy **stan** pokazuje **online**, oznacza to, brama jest gotowa do użycia.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Można usunąć bramy przy użyciu **AzureRmDataFactoryGateway Usuń** opis polecenia cmdlet i aktualizacji przy użyciu bramy **AzureRmDataFactoryGateway zestaw** polecenia cmdlet. Składnię i inne szczegółowe informacje o tych poleceniach cmdlet zobacz odwołanie do polecenia Cmdlet fabryki danych.  

### <a name="list-gateways-using-powershell"></a>Lista bram przy użyciu programu PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Usuń bramę przy użyciu programu PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Kolejne kroki
* Zobacz [przenoszenie danych między lokalnymi i w chmurze magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W tym przewodnikiem utworzysz potok, który używa bramy do przenoszenia danych z lokalną bazą danych programu SQL Server do obiektów blob platformy Azure.  
