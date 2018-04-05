---
title: Tworzenie środowiska uruchomieniowego integracji siebie w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowiska uruchomieniowego integracji siebie w fabryce danych Azure, dzięki czemu fabryki danych można uzyskać dostępu do magazyny danych w sieci prywatnej.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b18fa2879056600cfc9c6a85afc599e2695af711
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Jak utworzyć i skonfigurować Self-hosted integracji w czasie wykonywania
Integracja środowiska uruchomieniowego (IR) jest używany przez fabryki danych Azure zapewnienie możliwości integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej. Aby uzyskać więcej informacji o IR, zobacz [Omówienie środowiska uruchomieniowego integracji](concepts-integration-runtime.md).

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [Data Factory version 1 documentation (Dokumentacja usługi Data Factory w wersji 1)](v1/data-factory-introduction.md).

Hostowanie Samoobsługowe integracji środowiska uruchomieniowego jest możliwe uruchamianie działania kopiowania między danych w chmurze magazynów i magazynu danych w sieci prywatnej i wywoływanie transformacji działania względem obliczeniowe zasobów w lokalnej lub sieci wirtualnej platformy Azure. Zainstaluj potrzeb związanych z integracją siebie środowiska uruchomieniowego na maszynie lokalnej lub maszyny wirtualnej w sieci prywatnej.  

Tym dokumencie przedstawiono sposób tworzenia i konfigurowania Self-hosted IR.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Ogólne kroki, aby zainstalować IR hostowanie Samoobsługowe
1.  Tworzenie środowiska uruchomieniowego integracji siebie. Oto przykład środowiska PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Pobierz i zainstaluj hosta samodzielnego środowiska uruchomieniowego integracji (na komputerze lokalnym).
3.  Pobierz klucz uwierzytelniania i zarejestruj środowiska uruchomieniowego integracji hostowania samoobsługowego przy użyciu klucza. Oto przykład środowiska PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Polecenie przepływu i przepływu danych
Podczas przenoszenia danych między lokalnymi i w chmurze, działanie wykorzystuje runtime siebie integracji na przesyłanie danych z lokalnego źródła danych do chmury i na odwrót.

Poniżej przedstawiono przepływ danych wysokiego poziomu podsumowania kroki kopii IR hostowania samoobsługowego:

![Ogólne omówienie](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Projektant danych tworzy środowiska uruchomieniowego integracji siebie w ramach fabryki danych Azure przy użyciu polecenia cmdlet programu PowerShell. Azure portal nie obsługuje obecnie tej funkcji.
2. Projektant danych tworzy połączonej usługi magazynu danych lokalnych za pośrednictwem wystąpienia środowiska wykonawczego siebie integracji, które powinny być używane do nawiązania połączenia magazynów danych. W ramach konfigurowania połączonej usługi, danych deweloperów używa aplikacji Menedżer poświadczeń (obecnie nieobsługiwane) do ustawiania typy uwierzytelniania i poświadczeń. Okno dialogowe aplikacji Menedżera poświadczeń komunikuje się z magazynem danych, aby przetestować połączenie i środowiska uruchomieniowego integracji siebie na zapisywanie poświadczeń.
4.  Hostowanie Samoobsługowe integrację środowiska uruchomieniowego węzła szyfruje poświadczenia przy użyciu systemu Windows ochrony danych aplikacji programowania interfejsu (DPAPI) i zapisze go lokalnie. Jeśli ustawiono wiele węzłów wysokiej dostępności, poświadczenia dodatkowe są synchronizowane w innych węzłach. Wszystkie węzły są szyfrowane za pomocą DPAPI i są przechowywane lokalnie. Synchronizacja poświadczeń jest niewidoczny dla dewelopera danych i jest obsługiwany przez siebie IR.    
5.  Usługi fabryka danych komunikuje się ze środowiskiem uruchomieniowym integracji siebie do planowania i zarządzanie zadań za pomocą **kanał kontrolny** używającą kolejki magistrali usług Azure udostępnionych. Gdy zadanie działania musi zostać uruchomione, fabryki danych kolejkuje żądanie wraz z żadnych informacji dotyczących poświadczeń (w przypadku poświadczenia nie są już przechowywane na siebie integrację środowiska uruchomieniowego). Hostowanie Samoobsługowe integrację środowiska uruchomieniowego dotyczącego zadania po sondowania kolejki.
6.  Hostowanie Samoobsługowe integrację środowiska uruchomieniowego kopiuje dane z magazynu lokalnego do magazynu w chmurze, albo na odwrót w zależności od sposobu skonfigurowania działanie kopiowania w potoku danych. W tym kroku środowiska uruchomieniowego integracji siebie komunikuje się bezpośrednio z usług magazynu w chmurze, takich jak magazyn obiektów Blob Azure za pośrednictwem bezpiecznego kanału (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Zagadnienia dotyczące korzystania z własnym hostowanej IR.

- Runtime pojedynczego siebie integracji może służyć dla wielu lokalnych źródeł danych. Jednak **pojedynczego siebie integracji środowiska uruchomieniowego jest związany z tylko jedną fabryki danych Azure** i nie mogą być udostępniane innym fabryki danych.
- Może mieć **tylko jedno wystąpienie środowiska uruchomieniowego integracji siebie** zainstalowane na jednym komputerze. Załóżmy ma dwa fabryki danych, które wymagają dostęp do lokalnych źródeł danych, musisz zainstalować siebie integrację środowiska uruchomieniowego na dwóch lokalnych komputerach. Innymi słowy runtime integracji hostowania samoobsługowego jest związany z fabryki danych
- **Siebie integrację środowiska uruchomieniowego musi być na tym samym komputerze jako źródło danych**. Jednak konieczności samodzielnego hostingu środowiska uruchomieniowego integracji bliżej ze źródłem danych, skraca czas siebie integrację środowiska uruchomieniowego do nawiązania połączenia ze źródłem danych. Zaleca się zainstalowanie środowiska uruchomieniowego integracji siebie na komputerze, na którym jest inna niż wersja tego źródła danych lokalnych hostów. W przypadku hostowania samoobsługowego integrację środowiska uruchomieniowego i źródła danych na różnych komputerach, środowiska uruchomieniowego integracji hostowanie Samoobsługowe nie konkurować o zasoby z źródła danych.
- Może mieć **wiele runtimes siebie integracji na różnych komputerach nawiązywania połączenia z tym samym źródle danych lokalnych**. Na przykład może mieć dwóch siebie integracji środowiska uruchomieniowego obsługująca dwóch fabryk danych, ale to samo źródło danych lokalnych jest zarejestrowana w usłudze fabryki danych.
- Jeśli masz już bramy zainstalowanej na programu obsługi komputera **usługi Power BI** scenariuszu zainstalować **środowiska wykonawczego oddzielne integracji hostowanie Samoobsługowe dla fabryki danych Azure** na innym komputerze.
- Hostowanie Samoobsługowe integrację środowiska uruchomieniowego, należy użyć do obsługi integracji danych w ramach sieci wirtualnej platformy Azure.
- Traktuj źródła danych jako źródło danych lokalnych (który znajduje się za zaporą) nawet jeśli używasz **ExpressRoute**. Użyj środowiska uruchomieniowego integracji hostowania samoobsługowego nawiązać połączenie między usługą i źródła danych.
- Należy użyć środowiska uruchomieniowego integracji siebie, nawet jeśli magazyn danych jest w chmurze na **maszyny wirtualne Azure IaaS**.
- Zadania mogą nie działać w środowisku uruchomieniowym Self-hosted integracji zainstalowane w systemie Windows Server, na których zgodne ze standardem FIPS jest włączone szyfrowanie. Aby obejść ten problem, należy wyłączyć szyfrowanie zgodne ze standardem FIPS na serwerze. Umożliwia wyłączenie szyfrowania zgodne ze standardem FIPS, należy zmienić następującą wartość rejestru z zakresu od 1 (włączone), 0 (wyłączone): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane **systemu operacyjnego** są wersje systemu Windows 7 z dodatkiem SP1, Windows 8.1, Windows 10, systemu Windows Server 2008 R2 z dodatkiem SP1, systemu Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Instalacja środowiska uruchomieniowego integracji siebie na **kontroler domeny nie jest obsługiwany**.
- **.NET framework 4.6.1 lub nowszej** jest wymagana. Jeśli instalujesz siebie integrację środowiska uruchomieniowego na komputerze z systemem Windows 7, zainstaluj program .NET Framework 4.6.1 lub nowszej. Zobacz [wymagania systemowe programu .NET Framework](/dotnet/framework/get-started/system-requirements) szczegółowe informacje.
- Zalecanym **konfiguracji** środowiska uruchomieniowego integracji siebie maszyna jest co najmniej 2 GHz 4 rdzenie, 8 GB pamięci RAM i dysk 80 GB.
- Jeśli komputer hosta przejdzie w stan hibernacji, środowiska uruchomieniowego integracji hostowanie Samoobsługowe nie odpowiada na żądania danych. W związku z tym skonfigurować plan zasilania odpowiednie na komputerze przed instalacją środowiska uruchomieniowego integracji siebie. Jeśli komputer jest skonfigurowany do hibernacji, instalacji środowiska uruchomieniowego integracji siebie wyświetli komunikat.
- Musi być administratorem na tym komputerze do instalowania i konfigurowania środowiska uruchomieniowego integracji siebie pomyślnie.
- Jak uruchomień działania kopiowania wystąpi na określonej częstotliwości, użycie zasobów (procesora CPU, pamięci) na maszynie również zgodny ze wzorcem tego samego szczytu i czas bezczynności. Wykorzystanie zasobów zależy również od silnie ilości danych jest przenoszony. W przypadku wielu kopii zadania w toku, zostanie wyświetlony użycia zasobów, do góry w godzinach szczytu.

## <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji
Hostowanie Samoobsługowe integrację środowiska uruchomieniowego mogą być instalowane przez pobieranie pakietu Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobacz [przenoszenie danych między lokalnymi i w chmurze artykułu](tutorial-hybrid-copy-powershell.md) instrukcje krok po kroku.

- Skonfiguruj planu zasilania na komputerze hosta dla siebie integrację środowiska uruchomieniowego, dzięki czemu komputer nie hibernacji. Jeśli komputer hosta przejdzie w stan hibernacji, środowiska uruchomieniowego integracji siebie włącza w trybie offline.
- Utwórz kopię zapasową poświadczeń skojarzonych z własnym hostowanej integracji środowiska uruchomieniowego regularnie.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Zainstaluj i zarejestruj IR Self-hosted z Centrum pobierania

1. Przejdź do [stronę pobierania środowiska uruchomieniowego integracji Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kliknij przycisk **Pobierz**, wybierz odpowiednią wersję (**32-bitowych** vs. **64-bitowych**) i kliknij przycisk **dalej**.
3. Uruchom **MSI** bezpośrednio lub zapisać go na dysku twardym i uruchom.
4. Na **powitalnej** wybierz pozycję **języka** kliknij **dalej**.
5. **Zaakceptuj** umowę licencyjną użytkownika oprogramowania i kliknij przycisk **dalej**.
6. Wybierz **folderu** zainstaluj środowisko uruchomieniowe integracji siebie, a następnie kliknij przycisk **dalej**.
7. Na **gotowe do zainstalowania** kliknij przycisk **zainstalować**.
8. Kliknij przycisk **Zakończ** do ukończenia instalacji.
9. Pobierz klucz uwierzytelniania przy użyciu programu Azure PowerShell. Przykład programu PowerShell do pobierania klucza uwierzytelniania:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na **zarejestrować integracji Runtime (hosta samodzielnego)** strony programu Microsoft integrację środowiska uruchomieniowego programu Configuration Manager na komputerze, wykonaj następujące czynności:
    1. Wklej **klucz uwierzytelniania** w obszarze tekstu.
    2. Opcjonalnie kliknij **klucz uwierzytelniania Pokaż** tekst klucza.
    3. Kliknij przycisk **zarejestrować**.


## <a name="high-availability-and-scalability"></a>Wysoką dostępność i skalowalność
Środowisko uruchomieniowe Self-hosted integracji może być skojarzony z wieloma komputerami lokalnymi. Te komputery są nazywane węzłami. Może mieć maksymalnie czterech węzłów skojarzony ze środowiskiem uruchomieniowym Self-hosted integracji. Zalety mające wiele węzłów (lokalnymi maszynami z bramą zainstalowany) dla bramy logiczne to:
1. Wyższej dostępności środowiska uruchomieniowego integracji Self-hosted, że nie jest już pojedynczy punkt awarii w danych Big Data rozwiązania lub w chmurze danych integracją z fabryką danych Azure, zapewniając ciągłość z maksymalnie 4 węzły.
2. Zwiększona wydajność i przepływność podczas przenoszenia danych między lokalnymi i w chmurze magazynów danych. Uzyskać więcej informacji na temat [porównania wydajności](copy-activity-performance.md).

Można skojarzyć wielu węzłów po prostu instalując oprogramowanie środowiska uruchomieniowego integracji Self-hosted od [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) i rejestrując ją przy użyciu jednej z kluczy uwierzytelniania uzyskane z AzureRmDataFactoryV2IntegrationRuntimeKey nowe polecenia cmdlet, zgodnie z opisem w [samouczka](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Nie trzeba utworzyć nowy środowiska uruchomieniowego integracji Self-hosted kojarzenia każdego węzła. Można zainstalować środowiska uruchomieniowego integracji siebie na innym komputerze i zarejestrowanie go za pomocą tego samego klucza uwierzytelniania. 

> [!NOTE]
> Przed dodaniem innego węzła dla **wysokiej dostępności i skalowalności**, sprawdź, czy **"Dostępu zdalnego do intranetu"** jest opcja **włączone** w węźle 1 (Microsoft Menedżer konfiguracji środowiska uruchomieniowego integracji -> Ustawienia -> zdalny dostęp do intranetu). 

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów dla protokołu TLS/SSL
Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, który służy do zabezpieczania komunikacji między integrację środowiska uruchomieniowego węzłów:

- Certyfikat musi być publicznie zaufany X509 certyfikatu w wersji 3. Firma Microsoft zaleca, aby używać certyfikatów wystawionych przez publiczny (niezależny) urząd certyfikacji (CA).
- Każdy węzeł środowiska uruchomieniowego integracji musi ufać temu certyfikatowi.
- Symbol wieloznaczny certyfikaty są obsługiwane. Jeśli nazwa FQDN to **node1.domain.contoso.com**, można użyć ***. domain.contoso.com** jako nazwa podmiotu certyfikatu.
- Certyfikaty sieci SAN nie jest zalecane, ponieważ będą używane tylko ostatni element alternatywnej nazwy podmiotu, a wszystkie pozostałe zostaną pominięte ze względu na bieżące ograniczenia. Na przykład mieć certyfikat SAN którego SAN są **node1.domain.contoso.com** i **node2.domain.contoso.com**, tego certyfikatu można używać tylko na komputerze, którego nazwa FQDN to **node2.domain.contoso.com**.
- Obsługuje wszystkie rozmiar klucza obsługiwana przez system Windows Server 2012 R2 dla certyfikatów SSL.
- Certyfikat przy użyciu CNG klucze nie są obsługiwane. Doesrted DoesDoes nie obsługuje certyfikatów, które używają kluczy CNG.

## <a name="system-tray-icons-notifications"></a>Ikony systemowe na pasku zadań / powiadomienia
Przesunięcie kursora za pośrednictwem wiadomości ikonę/powiadomień na pasku zadań systemu można znaleźć szczegółowe informacje o stanie środowiska uruchomieniowego integracji siebie.

![System powiadomień na pasku zadań](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty i zapory
Istnieją dwa zapory, należy wziąć pod uwagę: **firmowej zapory** uruchomionego na routerze centralnej organizacji, i **zapory systemu Windows** skonfigurowany jako demon na komputerze lokalnym gdzie hostowanie Samoobsługowe integrację środowiska uruchomieniowego jest zainstalowany.

![Zapora](media\create-self-hosted-integration-runtime\firewall.png)

W **firmowej zapory** poziomu, wymagają konfigurowania portów wychodzących i następujących domen:

Nazwy domen | Porty | Opis
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Używany do komunikacji z zapleczem usługi przenoszenia danych
*.core.windows.net | 443 | Używany do kopiowania przejściowa przy użyciu obiektów Blob platformy Azure (jeśli jest skonfigurowane)
*.frontend.clouddatahub.net | 443 | Używany do komunikacji z zapleczem usługi przenoszenia danych

W **zapory systemu Windows** poziomie (komputer), te porty wyjściowe zwykle są włączone. Nie można skonfigurować w domenach i porty odpowiednio na hosta samodzielnego integrację środowiska uruchomieniowego maszyny.

> [!NOTE]
> Na podstawie Twojego źródła / wychwytywanie, może zajść potrzeba dodatkowe domeny listy dozwolonych adresów IP i portów wychodzących w sieci firmowej/Zapora systemu Windows.
>
> Dla niektórych baz danych w chmurze (na przykład: baza danych SQL Azure, usługa Azure Data Lake, itp.), może być konieczne dozwolonych adres IP komputera środowiska uruchomieniowego integracji siebie na ich konfiguracji zapory.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiowanie danych z źródła do ujścia
Sprawdź, czy reguły zapory są włączone prawidłowo w firmowej zapory, Zapora systemu Windows na komputerze środowiska uruchomieniowego integracji hostowania samoobsługowego i magazyn danych sam. Włączenie tych zasad umożliwia hostowanie Samoobsługowe integracji środowiska uruchomieniowego do łączenia się zarówno źródła i sink pomyślnie. Włącz reguły dla każdego magazynu danych, który jest używany w operacji kopiowania.

Na przykład do kopiowania z **magazynu danych lokalnych do zbiornika bazy danych SQL Azure lub ujścia magazynu danych SQL Azure**, wykonaj następujące czynności:

- Zezwalaj na wychodzące **TCP** komunikację za pośrednictwem portu **1433** dla zapory systemu Windows i firmowej zapory.
- Skonfiguruj ustawienia zapory programu Azure SQL, aby dodać adres IP maszyny środowiska uruchomieniowego integracji siebie do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na wychodzący port 1433, hostowanie Samoobsługowe integrację środowiska uruchomieniowego nie bezpośredni dostęp Azure SQL. W takim przypadku można użyć [przemieszczane kopiowania](copy-activity-performance.md) do bazy danych SQL Azure / SQL Azure DW. W tym scenariuszu do przenoszenia danych są tylko wymagane HTTPS (port 443).


## <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli w środowisku firmowym sieci korzysta z serwera proxy do uzyskania dostępu do Internetu, należy skonfigurować środowiska uruchomieniowego integracji siebie do użycia odpowiednie ustawienia serwera proxy. Podczas fazy początkowe rejestracyjny można ustawić serwera proxy.

![Określ serwer proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Hostowanie Samoobsługowe integrację środowiska uruchomieniowego używa serwera proxy do nawiązania połączenia usługi w chmurze. Kliknij łącze Zmień podczas instalacji początkowej. Zostanie wyświetlona okno dialogowe Ustawienia serwera proxy.

![Zestaw serwera proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Dostępne są trzy opcje konfiguracji:

- **Nie używaj serwera proxy**: hostowanie Samoobsługowe integrację środowiska uruchomieniowego nie jawnie używać dowolnego serwera proxy do nawiązania połączenia usługi w chmurze.
- **Użyj serwera proxy systemu**: hosta samodzielnego używa środowiska uruchomieniowego integracji w diahost.exe.config i diawp.exe.config skonfigurowano ustawienie serwera proxy. Jeśli żadnego serwera proxy jest skonfigurowany w diahost.exe.config i diawp.exe.config, hostowanie Samoobsługowe integrację środowiska uruchomieniowego łączy się z usługą w chmurze bezpośrednio, bez przechodzenia przez serwer proxy.
- **Użyć niestandardowego serwera proxy**: Konfiguruj ustawienia do użycia na potrzeby hostowania samoobsługowego integrację środowiska uruchomieniowego, zamiast konfiguracje diahost.exe.config i diawp.exe.config proxy HTTP. Wymagane są adres i Port. Nazwa użytkownika i hasło są opcjonalne, w zależności od ustawienia uwierzytelniania serwer proxy. Wszystkie ustawienia są szyfrowane z interfejsu DPAPI systemu Windows na siebie integrację środowiska uruchomieniowego i przechowywane lokalnie na komputerze.

Środowisko uruchomieniowe integracji usługi hosta do automatycznego uruchomienia po zapisaniu zaktualizowanych ustawień serwera proxy.

Po środowiska uruchomieniowego integracji hostowania samoobsługowego został pomyślnie zarejestrowany, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj Menedżera konfiguracji środowiska uruchomieniowego integracji.

1.  Uruchom **Menedżera konfiguracji środowiska uruchomieniowego integracji Microsoft**.
2.  Przejdź do karty **Ustawienia**.
3.  Kliknij przycisk **zmiany** łącze w **serwer HTTP Proxy** sekcji, aby uruchomić **ustawiony serwer Proxy HTTP** okna dialogowego.
4.  Po kliknięciu **dalej** przycisku, zobacz okno dialogowe ostrzeżenia pytania o uprawnienia zapisać ustawienie serwera proxy i ponownie uruchom usługę hosta środowiska uruchomieniowego integracji.

Można wyświetlać i aktualizować serwer proxy HTTP za pomocą narzędzia Configuration Manager.

![Widok serwera proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Jeśli skonfigurowano serwer proxy z uwierzytelnianiem NTLM środowiska uruchomieniowego integracji hosta usługa działa na koncie domeny. Jeśli zmienisz hasło dla konta domeny później, pamiętaj, aby zaktualizować ustawienia konfiguracji dla usługi i odpowiednio uruchom ją ponownie. Ze względu na to wymaganie zaleca się, że używasz konta domeny dedykowanego serwera proxy, który nie wymaga często zaktualizuj hasło dostępu do.

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy

W przypadku wybrania **użycia serwera proxy systemu** ustawienie serwera proxy HTTP, hostowanie Samoobsługowe integrację środowiska uruchomieniowego używa ustawienie diahost.exe.config i diawp.exe.config serwera proxy. Jeśli w diahost.exe.config i diawp.exe.config określono żadnego serwera proxy, hostowanie Samoobsługowe integrację środowiska uruchomieniowego łączy do usługi w chmurze bezpośrednio, bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizacji pliku diahost.exe.config.

1. W Eksploratorze plików wykonaj kopię bezpieczne C:\Program Files\Microsoft integracji Runtime\3.0\Shared\diahost.exe.config Aby utworzyć kopię zapasową oryginalnego pliku.
2. Uruchamianie Notepad.exe uruchomioną jako administrator, a następnie otwórz plik tekstowy "C:\Program Files\Microsoft integracji Runtime\3.0\Shared\diahost.exe.config. Domyślny znacznik można znaleźć system.net zgodnie z poniższym kodem:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Następnie można dodać szczegóły serwera proxy, jak pokazano w poniższym przykładzie:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu serwera proxy do określenia wymagane ustawienia, takie jak scriptLocation. Zobacz [serwera proxy elementu (ustawienia sieciowe)](https://msdn.microsoft.com/library/sa91de1e.aspx) składni.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracji do oryginalnej lokalizacji, a następnie uruchom ponownie usługę Self-hosted integrację środowiska uruchomieniowego hosta, który przejmuje zmiany. Aby ponownie uruchomić usługę: użyj apletu usługi w Panelu sterowania lub z **integrację środowiska uruchomieniowego Configuration Manager** > kliknij **Zatrzymaj usługę** przycisk, a następnie kliknij przycisk **Start Usługa**. Jeśli usługa nie zostanie uruchomiona, istnieje prawdopodobieństwo, że dodano niepoprawną składnię tagu XML w pliku konfiguracji aplikacji, który był edytowany.

> [!IMPORTANT]
> Pamiętaj zaktualizować zarówno diahost.exe.config i diawp.exe.config.

Oprócz tych punktów należy również upewnij się, że jest dozwolonych w firmie Microsoft Azure. Lista prawidłowych adresów IP firmy Microsoft Azure można pobrać z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Symptomów zapory i serwera proxy problemów związanych z serwerami
Jeśli wystąpią błędy podobne do następujących pól, prawdopodobnie z powodu nieprawidłowej konfiguracji zapory lub serwera proxy serwera, która blokuje środowiska uruchomieniowego integracji siebie połączenie z fabryki danych do samodzielnego uwierzytelnienia. Zapoznaj się z poprzedniej sekcji, aby upewnić się, zapory i serwera proxy są poprawnie skonfigurowane.

1.  Podczas próby zarejestrowania środowiska uruchomieniowego integracji hostowania samoobsługowego, zostanie wyświetlony następujący błąd: "nie można zarejestrować tego węzła środowiska uruchomieniowego integracji! Potwierdzić, że klucz uwierzytelniania jest prawidłowy i hosta Usługa integracji jest uruchomiona na tym komputerze. "
2.  Po otwarciu Menedżera konfiguracji środowiska uruchomieniowego integracji, zobacz stan jako "**Rozłączono**"lub"**łączenie**". Podczas przeglądania dzienników zdarzeń systemu Windows, w obszarze "Podglądu zdarzeń" > "I usługi Dzienniki aplikacji" > "Runtime integracji Microsoft", można wyświetlić komunikaty o błędach, takich jak następujący błąd:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Włączenie dostępu zdalnego z sieci Intranet  
W przypadku, jeśli używasz **PowerShell** lub **aplikacji Menedżera poświadczeń** do szyfrowania poświadczeń dostępu z innego komputera (w sieci) innych niż środowiska uruchomieniowego integracji siebie zainstalowanym, następnie będzie wymagać **"Zdalnego dostępu z intranetu"** Aby włączyć opcję. Po uruchomieniu **PowerShell** lub **aplikacji Menedżera poświadczeń** do szyfrowania poświadczeń na tym samym komputerze, na którym instalowany jest hostowanie Samoobsługowe integrację środowiska uruchomieniowego, następnie **"dostępu zdalnego z sieci Intranet "** może nie być włączone.

Zdalny dostęp z intranetu powinien być **włączone** przed dodaniem innego węzła dla **wysokiej dostępności i skalowalności**.  

Podczas integracji hostowania samoobsługowego środowiska uruchomieniowego instalacji (i jego nowszych wersjach 3.3.xxxx.x v), domyślnie wyłącza instalacji środowiska uruchomieniowego integracji siebie **"Zdalnego dostępu z intranetu"** na komputerze środowiska uruchomieniowego integracji siebie.

Jeśli używasz zapory innych firm, można ręcznie otworzyć port 8060 (lub użytkownik skonfigurowany). Jeśli napotkasz problem zapory podczas instalacji środowiska uruchomieniowego integracji hostowania samoobsługowego możesz spróbować zainstalować środowisko uruchomieniowe siebie integracji bez konfigurowania zapory przy użyciu następującego polecenia.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Menedżer poświadczeń aplikacji** nie jest jeszcze dostępna w celu szyfrowania poświadczeń w ADFv2. Firma Microsoft będzie później dodać tę obsługę.  

Jeśli wybierzesz nie otworzyć port 8060 na komputerze środowiska uruchomieniowego integracji hostowania samoobsługowego, używa mechanizmów innych niż z użyciem ** ustawienie poświadczeń ** aplikacji w celu skonfigurowania poświadczeń magazynu danych. Na przykład można użyć polecenia cmdlet New-AzureRmDataFactoryV2LinkedServiceEncryptCredential środowiska PowerShell. Zobacz sekcję ustawienie poświadczeń i zabezpieczeń w sposób poświadczeń magazynu danych można ustawić.


## <a name="next-steps"></a>Kolejne kroki
Następujące samouczki krok po kroku: [samouczek: kopiowanie danych lokalnych do chmury](tutorial-hybrid-copy-powershell.md).
