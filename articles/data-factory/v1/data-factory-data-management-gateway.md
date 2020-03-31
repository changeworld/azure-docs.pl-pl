---
title: Brama zarządzania danymi dla fabryki danych
description: Skonfiguruj bramę danych, aby przenosić dane między środowiskiem lokalnym a chmurą. Użyj bramy zarządzania danymi w usłudze Azure Data Factory, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1340c205477b256e3d96ff7ccacb64e575725c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065400"
---
# <a name="data-management-gateway"></a>Brama zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [środowisko uruchomieniowe integracji hostowanego samodzielnie w pliku](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Brama zarządzania danymi została zmieniona na własny hostowany środowisko uruchomieniowe integracji.

Brama zarządzania danymi jest agentem klienta, który należy zainstalować w środowisku lokalnym, aby skopiować dane między chmurą i lokalnymi magazynami danych. Lokalne magazyny danych obsługiwane przez fabrykę danych są wymienione w sekcji [Obsługiwane źródła danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Ten artykuł uzupełnia przewodnik w [przenieś danych między lokalnymi i chmurowymi magazynami danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W instruktażu utworzysz potok, który używa bramy do przenoszenia danych z lokalnej bazy danych programu SQL Server do obiektu blob platformy Azure. Ten artykuł zawiera szczegółowe szczegółowe informacje na temat bramy zarządzania danymi.

Bramę zarządzania danymi można skalować w poziomie, kojarząc z bramą wiele komputerów lokalnych. Można skalować w górę, zwiększając liczbę zadań przenoszenia danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępna dla bramy logicznej z jednym węzłem. Zobacz [skalowanie bramy zarządzania danymi w usłudze Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artykuł, aby uzyskać szczegółowe informacje.

> [!NOTE]
> Obecnie brama obsługuje tylko działanie kopiowania i działanie procedury składowanej w fabryce danych. Nie jest możliwe użycie bramy z działania niestandardowego w celu uzyskania dostępu do lokalnych źródeł danych.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
### <a name="capabilities-of-data-management-gateway"></a>Możliwości bramy zarządzania danymi
Brama zarządzania danymi oferuje następujące możliwości:

* Modelowanie lokalnych źródeł danych i źródeł danych w chmurze w tej samej fabryce danych i przenoszenie danych.
* Mieć pojedynczą szybę do monitorowania i zarządzania z wglądem w stan bramy ze strony Fabryka danych.
* Bezpieczne zarządzanie dostępem do lokalnych źródeł danych.
  * Nie są wymagane żadne zmiany w zaporze firmowej. Brama tylko sprawia, że wychodzące połączenia oparte na PROW, aby otworzyć Internet.
  * Szyfruj poświadczenia dla lokalnych magazynów danych za pomocą certyfikatu.
* Efektywne przenoszenie danych — dane są przesyłane równolegle, odporne na sporadyczne problemy z siecią za pomocą logiki automatycznego ponawiania.

### <a name="command-flow-and-data-flow"></a>Przepływ poleceń i przepływ danych
Podczas kopiowania danych między środowiskiem lokalnym a chmurą użycie działania jest używane do przesyłania danych z lokalnego źródła danych do chmury i odwrotnie.

Oto przepływ danych wysokiego poziomu i podsumowanie kroków kopiowania z bramą danych: ![Przepływ danych przy użyciu bramy](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Deweloper danych tworzy bramę dla usługi Azure Data Factory przy użyciu [portalu Azure lub](https://portal.azure.com) polecenia [cmdlet programu PowerShell.](https://docs.microsoft.com/powershell/module/az.datafactory/)
2. Deweloper danych tworzy usługę połączona dla lokalnego magazynu danych, określając bramę. W ramach konfigurowania połączonej usługi deweloper danych używa aplikacji Ustawianie poświadczeń do określania typów uwierzytelniania i poświadczeń. Okno dialogowe Aplikacja Ustawianie poświadczeń komunikuje się z magazynem danych w celu przetestowania połączenia i bramy w celu zapisania poświadczeń.
3. Brama szyfruje poświadczenia za pomocą certyfikatu skojarzonego z bramą (dostarczoną przez dewelopera danych), przed zapisaniem poświadczeń w chmurze.
4. Usługa Data Factory komunikuje się z bramą do planowania & zarządzania zadaniami za pośrednictwem kanału sterowania, który używa udostępnionej kolejki magistrali usługi Azure. Gdy zadanie działania kopiowania musi zostać rozpoczęte, usługa Data Factory kolejkuje żądanie wraz z informacjami o poświadczeniach. Brama rozpoczyna zadanie po sondowaniu kolejki.
5. Brama odszyfrowuje poświadczenia przy użyciu tego samego certyfikatu, a następnie łączy się z lokalnym magazynem danych z odpowiednim typem uwierzytelniania i poświadczeniami.
6. Brama kopiuje dane z magazynu lokalnego do magazynu w chmurze lub odwrotnie w zależności od sposobu konfigurowania działania kopiowania w potoku danych. W tym kroku brama komunikuje się bezpośrednio z usługami magazynu w chmurze, takimi jak usługa Azure Blob Storage za pośrednictwem bezpiecznego kanału (HTTPS).

### <a name="considerations-for-using-gateway"></a>Zagadnienia dotyczące korzystania z bramy
* Pojedyncze wystąpienie bramy zarządzania danymi może służyć dla wielu lokalnych źródeł danych. Jednak **wystąpienie pojedynczej bramy jest powiązane tylko z jedną fabryką danych platformy Azure** i nie można jej udostępnić innej fabryce danych.
* Na jednym komputerze może być **zainstalowana tylko jedno wystąpienie bramy zarządzania danymi.** Załóżmy, że masz dwie fabryki danych, które muszą uzyskać dostęp do lokalnych źródeł danych, musisz zainstalować bramy na dwóch komputerach lokalnych. Innymi słowy, brama jest powiązana z określoną fabryką danych
* **Brama nie musi znajdować się na tym samym komputerze co źródło danych.** Jednak posiadanie bramy bliżej źródła danych skraca czas na połączenie się bramy ze źródłem danych. Zaleca się zainstalowanie bramy na komputerze, który różni się od tego, który obsługuje lokalne źródło danych. Gdy brama i źródło danych znajdują się na różnych komputerach, brama nie konkuruje o zasoby ze źródłem danych.
* Na różnych komputerach może być **dostępnych wiele bram łączących się z tym samym lokalnym źródłem danych.** Na przykład może mieć dwie bramy obsługujące dwie fabryki danych, ale to samo lokalne źródło danych jest zarejestrowane w obu fabrykach danych.
* Jeśli masz już zainstalowaną bramę na komputerze obsługującą scenariusz **usługi Power BI,** zainstaluj **oddzielną bramę dla usługi Azure Data Factory** na innym komputerze.
* Brama musi być używana nawet podczas korzystania z usługi **ExpressRoute**.
* Traktuj źródło danych jako lokalne źródło danych (które znajduje się za zaporą), nawet jeśli używasz **usługi ExpressRoute.** Użyj bramy do ustanowienia łączności między usługą a źródłem danych.
* Bramy należy **używać,** nawet jeśli magazyn danych znajduje się w chmurze na **maszynie wirtualnej usługi Azure IaaS.**

## <a name="installation"></a>Instalacja
### <a name="prerequisites"></a>Wymagania wstępne
* Obsługiwane wersje **systemu operacyjnego** to Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalacja bramy zarządzania danymi na kontrolerze domeny nie jest obecnie obsługiwana.
* .NET Framework 4.5.1 lub powyżej jest wymagane. Jeśli instalujesz bramę na komputerze z systemem Windows 7, zainstaluj program .NET Framework 4.5 lub nowszą. Szczegółowe informacje można znaleźć w [wymaganiach systemowych programu .NET Framework.](https://msdn.microsoft.com/library/8z6watww.aspx)
* Zalecana **konfiguracja** komputera bramy to co najmniej 2 GHz, 4 rdzenie, 8 GB pamięci RAM i dysk 80 GB.
* Jeśli komputer-host hibernuje, brama nie odpowiada na żądania danych. W związku z tym przed zainstalowaniem bramy należy skonfigurować odpowiedni **plan zasilania** na komputerze. Jeśli urządzenie jest skonfigurowane do hibernacji, instalacja bramy monituje o komunikat.
* Aby pomyślnie zainstalować i skonfigurować bramę zarządzania danymi, musisz być administratorem na komputerze. Do **bramy zarządzania danymi użytkownicy** lokalnej grupy systemu Windows można dodawać dodatkowych użytkowników. Członkowie tej grupy mogą konfigurować bramę za pomocą narzędzia **Menedżer konfiguracji bramy zarządzania danymi.**

Ponieważ działanie kopiowania przebiega na określonej częstotliwości, użycie zasobów (PROCESOR, pamięć) na komputerze również podąża za tym samym wzorcem z czasów szczytowych i bezczynnych. Wykorzystanie zasobów zależy również w dużej mierze od ilości przesyłanych danych. Gdy wiele zadań kopiowania są w toku, widać użycie zasobów iść w górę w godzinach szczytu.

### <a name="installation-options"></a>Opcje instalacji
Bramę zarządzania danymi można zainstalować w następujący sposób:

* Pobierając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Msi może również służyć do uaktualniania istniejącej bramy zarządzania danymi do najnowszej wersji, przy zachowaniu wszystkich ustawień.
* Klikając pozycję **Pobierz i zainstaluj łącze bramy danych** w obszarze RĘCZNA KONFIGURACJA lub Zainstaluj bezpośrednio na tym komputerze **w** obszarze EXPRESS SETUP. Aby uzyskać instrukcje krok po kroku dotyczące korzystania z konfiguracji ekspresowej, zobacz [Przenoszenie danych między środowiskiem lokalnym a chmurą.](data-factory-move-data-between-onprem-and-cloud.md) Krok ręczny prowadzi do centrum pobierania. Instrukcje dotyczące pobierania i instalowania bramy z centrum pobierania znajdują się w następnej sekcji.

### <a name="installation-best-practices"></a>Najważniejsze wskazówki dotyczące instalacji:
1. Skonfiguruj plan zasilania na komputerze-hoście bramy, aby maszyna nie hibernacji. Jeśli komputer-host hibernuje, brama nie odpowiada na żądania danych.
2. Łącze zapasowe certyfikatu skojarzonego z bramą.

### <a name="install-the-gateway-from-download-center"></a>Instalowanie bramy z centrum pobierania
1. Przejdź do [strony pobierania bramy zarządzania danymi firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kliknij **przycisk Pobierz**, wybierz wersję **64-bitową** (32-bitowa nie jest już obsługiwana) i kliknij przycisk **Dalej**.
3. Uruchom **msi** bezpośrednio lub zapisać go na dysku twardym i uruchomić.
4. Na stronie **Powitanie** wybierz **język,** kliknij przycisk **Dalej**.
5. **Zaakceptuj** umowę licencyjną użytkownika końcowego i kliknij przycisk **Dalej**.
6. Wybierz **folder,** aby zainstalować bramę, a następnie kliknij przycisk **Dalej**.
7. Na stronie **Gotowe do zainstalowania** kliknij pozycję **Zainstaluj**.
8. Kliknij **przycisk Zakończ,** aby zakończyć instalację.
9. Pobierz klucz z witryny Azure portal. Zapoznaj się z następną sekcją, aby uzyskać instrukcje krok po kroku.
10. Na stronie **Rejestrowanie bramy** **programu Menedżer konfiguracji bramy zarządzania danymi** uruchomiony na komputerze wykonaj następujące czynności:
    1. Wklej klucz w tekście.
    2. Opcjonalnie kliknij pozycję **Pokaż klucz bramy,** aby wyświetlić tekst klucza.
    3. Kliknij **pozycję Zarejestruj**.

### <a name="register-gateway-using-key"></a>Rejestrowanie bramy przy użyciu klucza
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Jeśli brama logiczna nie została jeszcze utworzona w portalu
Aby utworzyć bramę w portalu i uzyskać klucz ze strony **Konfigurowanie,** wykonaj kroki z przewodnika w artykule [Przenoszenie danych między środowiskiem lokalnym a chmurą.](data-factory-move-data-between-onprem-and-cloud.md)

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Jeśli brama logiczna została już utworzona w portalu
1. W witrynie Azure portal przejdź do strony **Fabryka danych** i kliknij kafelek **Usługi połączone.**

    ![Strona Fabryka danych](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na stronie **Usługi połączone** wybierz **bramę** logiczną utworzoną w portalu.

    ![brama logiczna](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na stronie **Brama danych** kliknij pozycję **Pobierz i zainstaluj bramę danych**.

    ![Link do pobrania w portalu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na stronie **Konfigurowanie** kliknij pozycję **Odtwórz klucz**. Kliknij przycisk Tak na komunikat ostrzegawczy po dokładnym przeczytaniu.

    ![Odtworzenie klucza](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kliknij przycisk Kopiuj obok klawisza. Klucz zostanie skopiowany do schowka.

    ![Kopiowanie klucza](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony/powiadomienia w zasobniku systemowym
Na poniższej ilustracji przedstawiono niektóre widoczne ikony zasobnika.

![ikony zasobnika systemowego](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Jeśli przesuniesz kursor nad ikoną/komunikatem powiadomienia zasobnika systemowego, w oknie podręcznym zobaczysz szczegółowe informacje o stanie operacji bramy/aktualizacji.

### <a name="ports-and-firewall"></a>Porty i zapory
Istnieją dwie zapory, które należy wziąć pod uwagę: **zapora firmowa działająca** na centralnym routerze organizacji i **zapora systemu Windows** skonfigurowana jako demon na komputerze lokalnym, na którym jest zainstalowana brama.

![Zapory](./media/data-factory-data-management-gateway/firewalls2.png)

Na poziomie zapory firmowej należy skonfigurować następujące domeny i porty wychodzące:

| Nazwy domen | Porty | Opis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.core.windows.net |443 |Używane do kopiowania etapowego przy użyciu obiektu blob platformy Azure (jeśli jest skonfigurowane)|
| *.frontend.clouddatahub.net |443 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.servicebus.windows.net |9350-9354, 5671 |Opcjonalny przekaźnik magistrali usług za pomocą protokołu TCP używany przez Kreatora kopiowania |

Na poziomie zapory systemu Windows te porty wychodzące są zwykle włączone. Jeśli nie, można odpowiednio skonfigurować domeny i porty na komputerze bramy.

> [!NOTE]
> 1. Na podstawie źródła / pochłaniacze, może być konieczne do białej listy dodatkowych domen i portów wychodzących w firmie / zapory systemu Windows.
> 2. W przypadku niektórych baz danych w chmurze (na przykład: [Usługa Azure SQL Database,](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings) [usługa Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)itp.) może być konieczne wpisywać adres IP maszyny bramy na ich konfigurację zapory.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiowanie danych ze źródłowego magazynu danych do magazynu danych ujścia
Upewnij się, że reguły zapory są prawidłowo włączone na zaporze firmowej, zaporze systemu Windows na komputerze bramy i samym magazynie danych. Włączenie tych reguł umożliwia bramie łączenie się ze źródłem i sink pomyślnie. Włącz reguły dla każdego magazynu danych, który jest zaangażowany w operację kopiowania.

Na przykład, aby skopiować z **lokalnego magazynu danych do ujścia bazy danych SQL azure lub ujścia usługi Azure SQL Data Warehouse,** wykonaj następujące kroki:

* Zezwalaj na wychodzącą komunikację **TCP** na porcie **1433** zarówno dla zapory systemu Windows, jak i zapory firmowej.
* Skonfiguruj ustawienia zapory serwera SQL platformy Azure, aby dodać adres IP komputera bramy do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli zapora nie zezwala na port wychodzący 1433, brama nie może uzyskać bezpośredniego dostępu do usługi Azure SQL. W takim przypadku można użyć [kopii etapowej](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) do bazy danych SQL Azure Database/ SQL Azure DW. W tym scenariuszu można wymagać tylko HTTPS (port 443) dla przenoszenia danych.
>
>

### <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli w środowisku sieci firmowej używany jest serwer proxy do uzyskiwania dostępu do Internetu, skonfiguruj bramę zarządzania danymi tak, aby używała odpowiednich ustawień serwera proxy. Serwer proxy można ustawić podczas początkowej fazy rejestracji.

![Ustawianie serwera proxy podczas rejestracji](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway używa serwera proxy do łączenia się z usługą w chmurze. Kliknij pozycję **Zmień** łącze podczas wstępnej konfiguracji. Zostanie wyświetlone okno dialogowe **ustawień serwera proxy.**

![Ustawianie serwera proxy przy użyciu menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxySettings.png)

Dostępne są trzy opcje konfiguracji:

* **Nie należy używać serwera proxy:** Brama jawnie nie używa żadnego serwera proxy do łączenia się z usługami w chmurze.
* **Użyj serwera proxy systemu:** Brama używa ustawienia serwera proxy skonfigurowanego w pliku diahost.exe.config i diawp.exe.config. Jeśli serwer proxy nie jest skonfigurowany w pliku diahost.exe.config i diawp.exe.config, brama łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy.
* **Użyj niestandardowego serwera proxy:** Skonfiguruj ustawienie serwera proxy HTTP do użycia dla bramy, zamiast używać konfiguracji w diahost.exe.config i diawp.exe.config. Adres i port są wymagane. Nazwa użytkownika i hasło są opcjonalne w zależności od ustawienia uwierzytelniania serwera proxy. Wszystkie ustawienia są szyfrowane przy użyciu certyfikatu poświadczeń bramy i przechowywane lokalnie na komputerze hosta bramy.

Usługa hosta bramy zarządzania danymi uruchamia się automatycznie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po pomyślnym zarejestrowaniu bramy, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj programu Data Management Gateway Configuration Manager.

1. Uruchom **program Data Management Gateway Configuration Manager**.
2. Przełącz się do karty **Ustawienia.**
3. Kliknij **pozycję Zmień** łącze w sekcji Serwer proxy **HTTP,** aby uruchomić okno dialogowe **Ustaw serwer proxy HTTP.**
4. Po kliknięciu przycisku **Dalej** zostanie wyświetlone okno dialogowe z ostrzeżeniem z prośbą o pozwolenie na zapisanie ustawienia serwera proxy i ponowne uruchomienie usługi hosta bramy.

Serwer proxy HTTP można wyświetlać i aktualizować za pomocą narzędzia Programu Menedżer konfiguracji.

![Ustawianie serwera proxy przy użyciu menedżera konfiguracji](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Jeśli skonfigurujesz serwer proxy z uwierzytelnianiem NTLM, usługa hosta bramy działa na koncie domeny. Jeśli później zmienisz hasło do konta domeny, pamiętaj, aby zaktualizować ustawienia konfiguracji usługi i odpowiednio ją ponownie uruchomić. Ze względu na to wymaganie zalecamy użycie dedykowanego konta domeny, aby uzyskać dostęp do serwera proxy, który nie wymaga częstej aktualizacji hasła.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurowanie ustawień serwera proxy
Jeśli wybierzesz opcję Użyj ustawienia **serwera proxy systemu** dla serwera proxy HTTP, brama użyje ustawienia serwera proxy w pliku diahost.exe.config i diawp.exe.config. Jeśli w pliku proxy nie określono serwera proxy w pliku diahost.exe.config i diawp.exe.config, brama łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config.

1. W Eksploratorze plików należy wykonać bezpieczną kopię *programu\\\\C: Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config,* aby wykonać kopię zapasową oryginalnego pliku.
2. Uruchom program Notepad.exe jako administrator i otwórz plik tekstowy *\\\\C: Program\\\\Files\\Microsoft Data Management Gateway\\2.0 Shared diahost.exe.config*. Znajdziesz domyślny tag dla system.net, jak pokazano w poniższym kodzie:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Następnie można dodać szczegóły serwera proxy, jak pokazano w poniższym przykładzie:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu serwera proxy, aby określić wymagane ustawienia, takie jak scriptLocation. Jeśli nie majdą składni, zapoznaj się z [elementem serwera proxy (Ustawienia sieci).](https://msdn.microsoft.com/library/sa91de1e.aspx)

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracyjny w oryginalnej lokalizacji, a następnie uruchom ponownie usługę Host bramy zarządzania danymi, która pobiera zmiany. Aby ponownie uruchomić usługę: użyj apletu usług z panelu sterowania lub z **menedżera konfiguracji bramy zarządzania danymi** > kliknij przycisk **Zatrzymaj usługę,** a następnie kliknij **przycisk Uruchom usługę**. Jeśli usługa nie zostanie uruchomiony, jest prawdopodobne, że niepoprawna składnia tagu XML została dodana do pliku konfiguracji aplikacji, który był edytowany.

> [!IMPORTANT]
> Nie zapomnij zaktualizować **zarówno** diahost.exe.config i diawp.exe.config.

Oprócz tych punktów należy również upewnić się, że platforma Microsoft Azure znajduje się na białej liście firmy. Listę prawidłowych adresów IP platformy Microsoft Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Możliwe symptomy problemów związanych z zaporą i serwerem proxy
Jeśli wystąpią błędy podobne do następujących, jest prawdopodobne, ze względu na nieprawidłową konfigurację zapory lub serwera proxy, który blokuje bramy przed łączeniem się z fabryką danych, aby się uwierzytelnić. Zapoznaj się z poprzednią sekcją, aby upewnić się, że zapora i serwer proxy są poprawnie skonfigurowane.

1. Podczas próby zarejestrowania bramy pojawia się następujący błąd: "Nie można zarejestrować klucza bramy. Przed ponowną próbą zarejestrowania klucza bramy upewnij się, że brama zarządzania danymi jest w stanie połączonym, a usługa hosta bramy zarządzania danymi jest uruchomiona."
2. Po otwarciu programu Menedżer konfiguracji jest widoczny stan "Rozłączony" lub "Łączenie". Podczas przeglądania dzienników zdarzeń systemu Windows w obszarze "Podgląd zdarzeń" > "Dzienniki aplikacji i usług" > "Brama zarządzania danymi" wyświetlane są komunikaty o błędach, takie jak następujący błąd:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otwarty port 8050 do szyfrowania poświadczeń
Aplikacja **Ustawianie poświadczeń** używa portu przychodzącego **8050** do przekazywania poświadczeń do bramy podczas konfigurowania lokalnej usługi połączonej w witrynie Azure portal. Podczas konfigurowania bramy domyślnie instalacja bramy otwiera ją na komputerze bramy.

Jeśli używasz zapory innej firmy, możesz ręcznie otworzyć port 8050. Jeśli podczas konfigurowania bramy napotkasz problem z zaporą, możesz spróbować zainstalować bramę przy użyciu następującego polecenia bez konfigurowania zapory.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Jeśli nie chcesz otwierać portu 8050 na komputerze bramy, użyj mechanizmów innych niż przy użyciu aplikacji **Ustawianie poświadczeń** do konfigurowania poświadczeń magazynu danych. Na przykład można użyć polecenia cmdlet [programu PowerShell New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell. Zobacz ustawianie poświadczeń i zabezpieczeń sekcji, jak można ustawić poświadczenia magazynu danych.

## <a name="update"></a>Aktualizacja
Domyślnie brama zarządzania danymi jest automatycznie aktualizowana, gdy dostępna jest nowsza wersja bramy. Brama nie jest aktualizowana, dopóki nie zostaną wykonane wszystkie zaplanowane zadania. Bramy nie są przetwarzane żadne dalsze zadania, dopóki nie zostanie ukończona operacja aktualizacji. Jeśli aktualizacja nie powiedzie się, brama zostanie przywrócąta do starej wersji.

Zaplanowany czas aktualizacji jest widoczny w następujących miejscach:

* Strona właściwości bramy w witrynie Azure portal.
* Strona główna menedżera konfiguracji bramy zarządzania danymi
* Komunikat powiadomienia o zasobniku systemowym.

Na karcie Narzędzia główne programu Menedżer konfiguracji bramy zarządzania danymi jest wyświetlany harmonogram aktualizacji oraz ostatni czas instalowania/aktualizowania bramy.

![Aktualizacje harmonogramu](media/data-factory-data-management-gateway/UpdateSection.png)

Aktualizację można zainstalować od razu lub poczekać, aż brama zostanie automatycznie zaktualizowana o zaplanowanej godzinie. Na przykład na poniższej ilustracji przedstawiono komunikat powiadomienia wyświetlany w menedżerze konfiguracji bramy wraz z przyciskiem Aktualizuj, który można kliknąć, aby zainstalować go natychmiast.

![Aktualizacja w menedżerze konfiguracji DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Komunikat powiadomienia w zasobniku systemowym będzie wyglądał tak, jak pokazano na poniższej ilustracji:

![Komunikat zasobnika systemowego](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Stan operacji aktualizacji (ręcznej lub automatycznej) jest widoczny w zasobniku systemowym. Podczas następnego uruchamiania programu Gateway Configuration Manager na pasku powiadomień zostanie wyświetlony komunikat informujący o zaktualizowaniu bramy wraz z łączem do [nowego tematu](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Aby wyłączyć/włączyć funkcję automatycznej aktualizacji
Funkcję automatycznej aktualizacji można wyłączyć/włączyć, wykonując następujące czynności:

[Dla bramy z pojedynczym węzłem]
1. Uruchom program Windows PowerShell na komputerze bramy.
2. Przełącz się do folderu *C:\\\\\\Program Files Microsoft Integration Runtime\\3.0\\PowerShellScript.\\ *
3. Uruchom następujące polecenie, aby wyłączyć funkcję automatycznej aktualizacji (wyłączyć).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Aby włączyć go z powrotem:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Dla bramy o wysokiej dostępności i skalowalnej dla wielu węzłów](data-factory-data-management-gateway-high-availability-scalability.md)
1. Uruchom program Windows PowerShell na komputerze bramy.
2. Przełącz się do folderu *C:\\\\\\Program Files Microsoft Integration Runtime\\3.0\\PowerShellScript.\\ *
3. Uruchom następujące polecenie, aby wyłączyć funkcję automatycznej aktualizacji (wyłączyć).

    W przypadku bramy z funkcją wysokiej dostępności wymagany jest dodatkowy param AuthKey.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Aby włączyć go z powrotem:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po zainstalowaniu bramy można uruchomić program Menedżer konfiguracji bramy zarządzania danymi w jeden z następujących sposobów:

1. W oknie **Wyszukiwanie** wpisz **Bramę zarządzania danymi,** aby uzyskać dostęp do tego narzędzia.
2. Uruchom plik wykonywalny *ConfigManager.exe* w folderze: *\\\\C: Pliki programów\\Brama\\zarządzania danymi firmy Microsoft 2.0\\Udostępnione*.

### <a name="home-page"></a>Strona główna
Strona główna umożliwia wykonać następujące czynności:

* Stan widoku bramy (połączonej z usługą w chmurze itp.).
* **Zarejestruj się** przy użyciu klucza z portalu.
* **Zatrzymaj** i uruchom **usługę Host bramy zarządzania danymi** na komputerze bramy.
* **Zaplanuj aktualizacje** o określonej godzinie.
* Wyświetlanie daty ostatniej **aktualizacji**bramy .

### <a name="settings-page"></a>Strona Ustawienia
Strona Ustawienia umożliwia wykonać następujące czynności:

* Wyświetlanie, zmiana i **eksportowanie certyfikatu** używanego przez bramę. Ten certyfikat jest używany do szyfrowania poświadczeń źródła danych.
* Zmień **port HTTPS** dla punktu końcowego. Brama otwiera port do ustawiania poświadczeń źródła danych.
* **Stan** punktu końcowego
* Wyświetl **certyfikat SSL** jest używany do komunikacji TLS/SSL między portalem a bramą w celu ustawiania poświadczeń dla źródeł danych.

### <a name="remote-access-from-intranet"></a>Dostęp zdalny z intranetu
Ta funkcja zostanie włączona w przyszłości. W nadchodzących aktualizacjach (wersja 3.4 lub nowsza) pozwolimy Ci włączyć/ wyłączyć wszelką łączność zdalną, która obecnie ma miejsce przy użyciu portu 8050 (patrz sekcja powyżej) podczas korzystania z aplikacji PowerShell lub Credential Manager do szyfrowania poświadczeń.

### <a name="diagnostics-page"></a>Strona diagnostyki
Diagnostyka strona umożliwia wykonać następujące czynności:

* Włącz **pełne rejestrowanie,** wyświetlanie dzienników w podglądzie zdarzeń i wysyłanie dzienników do firmy Microsoft w przypadku awarii.
* **Przetestuj połączenie** ze źródłem danych.

### <a name="help-page"></a>Strona pomocy
Na stronie Pomocy są wyświetlane następujące informacje:

* Krótki opis bramy
* Numer wersji
* Łącza do pomocy online, oświadczenia o ochronie prywatności i umowy licencyjnej.

## <a name="monitor-gateway-in-the-portal"></a>Monitorowanie bramy w portalu
W witrynie Azure portal można wyświetlić migawkę w czasie zbliżonym do rzeczywistego wykorzystania zasobów (procesora CPU, pamięci, sieci(in/out) itp.) na komputerze bramy.

1. W witrynie Azure portal przejdź do strony głównej fabryki danych i kliknij kafelek **Połączonych usług.**

    ![Strona główna fabryki danych](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Wybierz **bramę** na stronie **Usługi połączone.**

    ![Strona usług połączonych](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na stronie **Bramy** można zobaczyć użycie pamięci i procesora CPU bramy.

    ![Wykorzystanie procesora i pamięci bramy](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Włącz **ustawienia zaawansowane,** aby wyświetlić więcej szczegółów, takich jak użycie sieci.
    
    ![Zaawansowane monitorowanie bramy](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Poniższa tabela zawiera opisy kolumn na liście **Węzły bramy:**

Właściwość monitorowania | Opis
:------------------ | :----------
Nazwa | Nazwa bramy logicznej i węzłów skojarzonych z bramą. Węzeł jest lokalnym komputerem z systemem Windows, na który znajduje się brama. Aby uzyskać informacje na temat posiadania więcej niż jednego węzła (do czterech węzłów) w jednej bramie logicznej, zobacz [Brama zarządzania danymi — wysoka dostępność i skalowalność](data-factory-data-management-gateway-high-availability-scalability.md).
Stan | Stan bramy logicznej i węzłów bramy. Przykład: Online / Offline / Limited / itp. Aby uzyskać informacje o tych stanach, zobacz [sekcję stanu bramy.](#gateway-status)
Wersja | Pokazuje wersję bramy logicznej i każdego węzła bramy. Wersja bramy logicznej jest określana na podstawie wersji większości węzłów w grupie. Jeśli w konfiguracji bramy logicznej znajdują się węzły z różnymi wersjami, poprawnie działają tylko węzły o tym samym numerze wersji co brama logiczna. Inne są w trybie ograniczonym i muszą być ręcznie aktualizowane (tylko w przypadku, gdy automatyczna aktualizacja nie powiedzie się).
Dostępna pamięć | Dostępna pamięć w węźle bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego.
Wykorzystanie procesora | Wykorzystanie procesora CPU węzła bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego.
Sieć (wsuwanie/wyjęcie) | Wykorzystanie sieci węzła bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego.
Równoczesne zadania (uruchamianie/ limit) | Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. Limit oznacza maksymalną liczbę równoczesnych zadań dla każdego węzła. Ta wartość jest definiowana na podstawie rozmiaru maszyny. Można zwiększyć limit skalowania w górę równoczesnych zadań w zaawansowanych scenariuszach, gdzie procesor CPU/memory/network jest niedostateczone, ale działania są limit czasu. Ta funkcja jest również dostępna z bramą z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona).
Rola | Istnieją dwa typy ról w bramie wielowęzłowej — dyspozytor i pracownik. Wszystkie węzły są pracownikami, co oznacza, że wszystkie mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego (w tym do siebie).

Na tej stronie zostaną wyświetlona niektóre ustawienia, które mają większy sens, gdy w bramie znajdują się dwa lub więcej węzłów (scenariusz skalowania w poziomie). Zobacz [Brama zarządzania danymi — wysoka dostępność i skalowalność,](data-factory-data-management-gateway-high-availability-scalability.md) aby uzyskać szczegółowe informacje na temat konfigurowania bramy wielowęzłowej.

### <a name="gateway-status"></a>Stan bramy
Poniższa tabela zawiera możliwe stany **węzła bramy:**

Stan  | Komentarze/scenariusze
:------- | :------------------
Online | Węzeł połączony z usługą Data Factory.
W trybie offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest automatycznie aktualizowany.
Ograniczone | Ze względu na problem z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością magistrali usług lub problemem synchronizacji poświadczeń.
Nieaktywne | Węzeł jest w konfiguracji różni się od konfiguracji innych węzłów większościowych.<br/><br/> Węzeł może być nieaktywny, gdy nie może połączyć się z innymi węzłami.

Poniższa tabela zawiera możliwe stany **bramy logicznej**. Stan bramy zależy od stanów węzłów bramy.

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany w tej bramie logicznej
Online | Węzły bramy są w trybie online
W trybie offline | Brak węzła w stanie online.
Ograniczone | Nie wszystkie węzły w tej bramie są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzły mogą być w dół! <br/><br/>Może to być spowodowane problemem synchronizacji poświadczeń w węźle dyspozytor/pracownik.

## <a name="scale-up-gateway"></a>Skalowanie bramy w górę
Można skonfigurować liczbę **równoczesnych zadań przenoszenia danych,** które można uruchomić w węźle, aby zwiększyć możliwości przenoszenia danych między magazynami danych w chmurze i w chmurze.

Gdy dostępna pamięć i procesor CPU nie są dobrze wykorzystywane, ale pojemność bezczynności wynosi 0, należy zwiększyć skalę, zwiększając liczbę równoczesnych zadań, które można uruchomić w węźle. Można również skalować w górę, gdy działania są limit czasu, ponieważ brama jest przeciążony. W zaawansowanych ustawieniach węzła bramy można zwiększyć maksymalną pojemność węzła.

## <a name="troubleshooting-gateway-issues"></a>Rozwiązywanie problemów z bramą
Zobacz [Rozwiązywanie problemów z bramą](data-factory-troubleshoot-gateway-issues.md) artykuł informacji/wskazówek dotyczących rozwiązywania problemów z korzystaniem z bramy zarządzania danymi.

## <a name="move-gateway-from-one-machine-to-another"></a>Przenoszenie bramy z jednego komputera na drugą
Ta sekcja zawiera kroki dotyczące przenoszenia klienta bramy z jednego komputera na inny komputer.

1. W portalu przejdź do **strony głównej usługi Data Factory**i kliknij kafelek Usługi **połączone.**

    ![Łącze bram danych](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Wybierz bramę w sekcji **BRAMY DANYCH** na stronie **Usługi połączone.**

    ![Strona Usługi połączone z wybraną bramą](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na stronie **Brama danych** kliknij pozycję **Pobierz i zainstaluj bramę danych**.

    ![Pobierz łącze bramy](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na stronie **Konfigurowanie** kliknij pozycję **Pobierz i zainstaluj bramę danych**i postępuj zgodnie z instrukcjami, aby zainstalować bramę danych na komputerze.

    ![Konfigurowanie strony](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Zachowaj otwarty **program Menedżer konfiguracji bramy zarządzania danymi firmy Microsoft.**

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na stronie **Konfigurowanie** w portalu kliknij pozycję **Odtwórz klucz** na pasku poleceń, a następnie kliknij przycisk **Tak,** aby uzyskać komunikat ostrzegawczy. Kliknij **przycisk kopiuj** obok tekstu kluczowego, który kopiuje klucz do schowka. Brama na starym komputerze przestaje działać, gdy tylko odtworzysz klucz.

    ![Odtworzenie klucza](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Wklej **klucz** do pola tekstowego na stronie **Rejestrowanie bramy** **programu Data Management Gateway Configuration Manager** na komputerze. (opcjonalnie) Kliknij pole wyboru **Pokaż klucz bramy,** aby wyświetlić tekst klucza.

    ![Kopiowanie klucza i rejestracja](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknij **przycisk Zarejestruj** się, aby zarejestrować bramę w usłudze w chmurze.
9. Na karcie **Ustawienia** kliknij pozycję **Zmień,** aby wybrać ten sam certyfikat, który był używany ze starą bramą, wprowadź **hasło**i kliknij przycisk **Zakończ**.

   ![Określ certyfikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Certyfikat ze starej bramy można wyeksportować, wykonując następujące czynności: uruchom program Data Management Gateway Configuration Manager na starym komputerze, przełącz się na kartę **Certyfikat,** kliknij przycisk **Eksportuj** i postępuj zgodnie z instrukcjami.
10. Po pomyślnej rejestracji bramy na stronie głównej menedżera konfiguracji bramy powinien zostać wyświetlony zestaw **Rejestracji** na **Zarejestrowany** i **Stan** na Uruchomiono pozycję **Uruchomiono.**

## <a name="encrypting-credentials"></a>Szyfrowanie poświadczeń
Aby zaszyfrować poświadczenia w Edytorze fabryki danych, wykonaj następujące czynności:

1. Uruchom przeglądarkę internetową na **komputerze bramy,** przejdź do [witryny Azure portal](https://portal.azure.com). Wyszukaj fabrykę danych w razie potrzeby, otwórz fabrykę danych na stronie **DATA FACTORY,** a następnie kliknij przycisk **Autor & Deploy,** aby uruchomić Edytor fabryki danych.
2. Kliknij istniejącą **usługę połączony** w widoku drzewa, aby wyświetlić jej definicję JSON lub utworzyć połączona usługa, która wymaga bramy zarządzania danymi (na przykład: SQL Server lub Oracle).
3. W edytorze JSON dla właściwości **gatewayName** wprowadź nazwę bramy.
4. Wprowadź nazwę serwera właściwości **Źródło danych** w **pliku connectionString**.
5. Wprowadź nazwę bazy danych właściwości **Wykaz początkowy** w **pliku connectionString**.
6. Kliknij przycisk **Zaszyfruj** na pasku poleceń, który uruchamia aplikację **Menedżera poświadczeń kliknięć.** Powinno zostać wyświetlone okno dialogowe **Ustawianie poświadczeń.**

    ![Okno dialogowe Ustawianie poświadczeń](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. W oknie dialogowym **Ustawianie poświadczeń** wykonaj następujące czynności:
   1. Wybierz **uwierzytelnianie,** którego usługa Data Factory ma używać do łączenia się z bazą danych.
   2. Wprowadź nazwę użytkownika, który ma dostęp do bazy danych dla ustawienia **NAZWA UŻYTKOWNIKA.**
   3. Wprowadź hasło dla użytkownika dla ustawienia **HASŁO.**
   4. Kliknij **przycisk OK,** aby zaszyfrować poświadczenia i zamknąć okno dialogowe.
8. Powinien zostać wyświetlony **encryptedCredential** właściwości w **connectionString** teraz.

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
   Jeśli dostęp do portalu z komputera, który różni się od komputera bramy, należy upewnić się, że aplikacja Menedżera poświadczeń można połączyć się z komputerem bramy. Jeśli aplikacja nie może dotrzeć do komputera bramy, nie pozwala na ustawienie poświadczeń dla źródła danych i przetestowanie połączenia ze źródłem danych.

Korzystając z aplikacji **Ustawianie poświadczeń,** portal szyfruje poświadczenia za pomocą certyfikatu określonego na karcie **Certyfikat** **menedżera konfiguracji bramy** na komputerze bramy.

Jeśli szukasz podejścia opartego na interfejsie API do szyfrowania poświadczeń, możesz użyć polecenia cmdlet Programu PowerShell [new-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell do szyfrowania poświadczeń. Polecenie cmdlet używa certyfikatu, który brama jest skonfigurowana do szyfrowania poświadczeń. Możesz dodać zaszyfrowane poświadczenia do **elementu EncryptedCredential** **connectionString** w JSON. JSON jest używany z poleceniem cmdlet [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) lub w Edytorze fabryki danych.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Istnieje jeszcze jedno podejście do ustawiania poświadczeń przy użyciu Edytora fabryki danych. Jeśli utworzysz usługę połączone programu SQL Server przy użyciu edytora i wprowadzisz poświadczenia w postaci zwykłego tekstu, poświadczenia są szyfrowane przy użyciu certyfikatu, który jest właścicielem usługi Data Factory. NIE używa certyfikatu, który brama jest skonfigurowana do użycia. Chociaż takie podejście może być nieco szybsze w niektórych przypadkach, jest mniej bezpieczne. W związku z tym zaleca się, aby postępować zgodnie z tym podejściem tylko do celów rozwoju/testowania.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
W tej sekcji opisano sposób tworzenia i rejestrowania bramy przy użyciu poleceń cmdlet programu Azure PowerShell.

1. Uruchom **program Azure PowerShell** w trybie administratora.
2. Zaloguj się do konta platformy Azure, uruchamiając następujące polecenie i wprowadzając poświadczenia platformy Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Polecenie cmdlet **New-AzDataFactoryGateway** służy do tworzenia bramy logicznej w następujący sposób:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Przykładowe polecenie i wyjście:**

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

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

1. W programie Azure PowerShell przełącz się do folderu: *C:\\\\\\Pliki programów\\\\Microsoft Integration\\Runtime 3.0 PowerShellScript*. Uruchom *RegisterGateway.ps1* skojarzony ze zmienną lokalną **$Key,** jak pokazano w poniższym poleceniu. Ten skrypt rejestruje agenta klienta zainstalowanego na komputerze z bramą logiczną utworzysz wcześniej.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Bramę można zarejestrować na komputerze zdalnym przy użyciu parametru IsRegisterOnRemoteMachine. Przykład:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Polecenia cmdlet **Get-AzDataFactoryGateway** można użyć, aby uzyskać listę bram w fabryce danych. Gdy **stan** jest wyświetlany **w trybie online,** oznacza to, że brama jest gotowa do użycia.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Bramę można usunąć za pomocą polecenia cmdlet **Remove-AzDataFactoryGateway** i zaktualizować ją za pomocą poleceń poleceń cmdlet **Set-AzDataFactoryGateway.** Aby uzyskać informacje na temat składni i innych szczegółów dotyczących tych poleceń cmdlet, zobacz Odwołanie do polecenia polecenia cmdlet fabryki danych.  

### <a name="list-gateways-using-powershell"></a>Lista bram przy użyciu programu PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Usuwanie bramy przy użyciu programu PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Następne kroki
* Zobacz [Przenoszenie danych między lokalnymi i chmurowymi magazynami danych](data-factory-move-data-between-onprem-and-cloud.md) artykułu. W instruktażu utworzysz potok, który używa bramy do przenoszenia danych z lokalnej bazy danych programu SQL Server do obiektu blob platformy Azure.
