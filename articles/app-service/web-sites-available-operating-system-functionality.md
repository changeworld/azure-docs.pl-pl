---
title: Funkcje systemu operacyjnego w usłudze Azure App Service
description: Dowiedz się więcej o funkcji systemu operacyjnego, dostępnych dla aplikacji sieci web, zapleczy aplikacji mobilnych i aplikacji API apps w usłudze Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: 00b5f9c78000fbb9bf86e8c1d8b06e3645795a12
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850158"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkcje systemu operacyjnego w usłudze Azure App Service
W tym artykule opisano typowe funkcje systemu operacyjnego linii bazowej, która jest dostępna dla wszystkich aplikacji uruchomionych na [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ta funkcja zawiera plik, sieci i dostępu do rejestru i dzienników diagnostyki i zdarzeń. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Warstwy planu usług aplikacji
App Service uruchamia aplikacje klienta w środowisku macierzystym wielodostępnej. Aplikacje wdrożone w **wolne** i **Shared** warstw uruchomić w procesach roboczych na udostępnionych maszynach wirtualnych, gdy aplikacje wdrożone w **standardowe** i **Premium** warstw, uruchom na przeznaczony specjalnie dla aplikacji skojarzonych z jednym maszyn wirtualnych.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ponieważ usługi App Service obsługuje bezproblemowe skalowanie między różnych warstw, konfiguracja zabezpieczeń wymuszane dla aplikacji usługi aplikacji jest taka sama. Daje to pewność, że aplikacje nie nagle zachowywać się inaczej, niepowodzeniem w nieoczekiwany sposób, jeśli plan usługi aplikacji zmienia się z jedną warstwę do innego.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Projektowanie struktury
Warstw cenowych usługi aplikacji kontroli ilości zasobów obliczeniowych (Procesora, Magazyn danych na dysku, pamięci i wyjście sieci) dostępne dla aplikacji. Jednak szerokość framework funkcji dostępnych dla aplikacji jest taka sama niezależnie od skalowania warstwy.

Usługi aplikacji obsługuje wiele platform, projektowanie, na przykład ASP.NET, klasyczne ASP, node.js, PHP i Python - wszystkich jest uruchomiony jako rozszerzenia w usługach IIS. Usługi aplikacji — aplikacje w celu uproszczenia i normalizacji konfiguracji zabezpieczeń, są zazwyczaj uruchamiane różne środowiska deweloperskie z ustawień domyślnych. Aby dostosować powierzchni interfejsu API i funkcje dla każdego platforma programistyczna poszczególnych mogło być jeden ze sposobów konfigurowania aplikacji. Usługi aplikacji ma zamiast tego podejścia bardziej ogólnym przez włączenie linii bazowej typowych funkcji systemu operacyjnego, niezależnie od tego, platforma programistyczna aplikacji.

W poniższych sekcjach podsumowano ogólne rodzaje funkcji systemu operacyjnego dostępnych dla aplikacji usługi App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dostęp do plików
Istnieją różne dyski w usługi aplikacji, w tym dyski lokalne i dyskach sieciowych.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Dyski lokalne
Zasadniczo usługi aplikacji — usługa jest uruchomiona na szczycie infrastruktury Azure PaaS (platforma jako usługa). W związku z tym dyski lokalne, dołączonych "" do maszyny wirtualnej są takie same typy dysku dostępne dla każdej roli procesu roboczego działające na platformie Azure. Obejmuje to:

- Dysk systemu operacyjnego (dysku D:\)
- Dysk aplikacji, który zawiera pliki cspkg pakietu Azure używany wyłącznie przez usługę App Service (i niedostępne do klientów)
- Dysku "użytkownika" (dysku C:\), którego rozmiar może być różna w zależności od rozmiaru maszyny wirtualnej. 

Należy monitorować Twoje wykorzystanie dysku wraz z rozwojem aplikacji. Gdy zostanie osiągnięty przydział dysku, może mieć niepożądane skutki do aplikacji.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Dyski sieciowe (lub udziały UNC)
Jednym z unikatowe aspekty usługi App Service ułatwia wdrażanie aplikacji i konserwacji prostego jest że cała zawartość użytkownika są przechowywane na zbiór udziały UNC. Ten model mapuje dobrze na wspólnej strukturze zawartości Magazyn używany przez lokalną hostingu środowisk, w których wiele serwerów z równoważeniem obciążenia. 

W ramach usługi aplikacji istnieje szereg udziały UNC utworzone w każdej centrum danych. Procent zawartości użytkownika dla wszystkich klientów w każdym centrum danych jest przydzielony do każdego udziału UNC. Ponadto wszystkich plików zawartości subskrypcji jednego odbiorcy zawsze jest umieszczana na tym samym UNC udziału. 

Ze względu na sposób Azure działają usługi odpowiedzialny za hosting udziału UNC określonej maszyny wirtualnej zmieni się wraz z upływem czasu. Jest gwarancji, że udziały UNC zostanie zainstalowany przez różnych maszyn wirtualnych zgodnie z ich wprowadzenia w górę i w dół w trakcie normalnych operacji platformy Azure. Z tego powodu aplikacje powinny nie należy wprowadzać ustalony założenia, że informacje o maszynie w ścieżkę pliku UNC jest trwały na wraz z upływem czasu. Zamiast tego należy użyć wygodne *symulowane* ścieżkę bezwzględną **D:\home\site** udostępniający usługi aplikacji. Ta ścieżka bezwzględna symulowane udostępnia metodę przenośnym, funkcja aplikacji i użytkownika odwoływania się do własnych aplikacji. Za pomocą **D:\home\site**, co można transfer udostępnionych plików aplikację aplikacji bez konieczności konfigurowania nową ścieżkę bezwzględną do każdego transferu.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy plików udzielono dostępu do aplikacji
Subskrypcja każdego klienta ma struktury katalogów zarezerwowane w udziale UNC określone w obrębie centrum danych. Klient może mieć wielu aplikacjom tworzone w obrębie centrum danych specyficznych dla wszystkich katalogów należącymi do subskrypcji jednego odbiorcy są tworzone w tym samym UNC udziału. Udział może obejmować katalogi, takich jak zawartość, błąd dzienników diagnostycznych i wcześniejszych wersjach aplikacji utworzonych przez kontroli źródła. Zgodnie z oczekiwaniami, katalogów aplikacji klienta są dostępne do odczytu i zapisu w czasie wykonywania przez kod aplikacji w aplikacji.

Na dyskach lokalnych dołączony do maszyny wirtualnej, która uruchamia aplikację usługa aplikacji rezerwuje fragmentu miejsca na dysku C:\ do tymczasowego przechowywania lokalnego specyficzny dla aplikacji. Mimo że aplikacja ma dostęp Pełna odczytu/zapisu do jego własnej tymczasowego magazynu lokalnego, magazynu naprawdę nie jest przeznaczona do użycia bezpośrednio w kodzie aplikacji. Zamiast celem jest zapewnienie przechowywania plików tymczasowych dla usług IIS i sieci web struktury aplikacji. Usługi aplikacji również ogranicza tymczasowego magazynu lokalnego dostępne dla każdej aplikacji, aby zapobiec zajęciu nadmiernej ilości pamięci masowej pliku lokalnego poszczególnych aplikacji.

Dwa przykłady używaniu tymczasowy magazyn lokalny usługi aplikacji to katalog dla plików tymczasowych ASP.NET i katalogu dla usług IIS skompresowane pliki. System kompilacji platformy ASP.NET używa katalogu "Temporary ASP.NET Files" jako lokalizację pamięci podręcznej tymczasowego kompilacji. Usługi IIS używają "IIS skompresowany katalogu plików tymczasowych" do przechowywania danych wyjściowych skompresowanych odpowiedzi. Oba typy plików użycia (a także innych) są mapowane ponownie w usłudze App Service do lokalnego magazynu tymczasowego dla aplikacji. To ponowne mapowanie gwarantuje, że funkcja będzie nadal występować, zgodnie z oczekiwaniami.

Każdej aplikacji w usłudze App Service jest uruchamiany jako tożsamość procesu losowy unikatowy roboczego niskich uprawnieniach o nazwie "tożsamość puli aplikacji", dodatkowe opisane tutaj: [ http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Kod aplikacji będą używać tej tożsamości dla podstawowego dostępu tylko do odczytu na dysku systemu operacyjnego (dysku D:\). Oznacza to, kod aplikacji, można wyświetlić listę wspólnej struktury katalogu i odczytu wspólne pliki na dysku systemu operacyjnego. Mimo że to wydaje się nieco szerokie poziom dostępu, tym samym katalogów i plików są dostępne podczas obsługi administracyjnej roli proces roboczy na platformie Azure usługi hostowanej i odczytywania zawartości dysku. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Dostęp do plików w wielu wystąpieniach
Katalog macierzysty zawiera zawartość aplikacji, a kod aplikacji może zapisywać do niego. Jeśli aplikacja jest uruchamiana na wiele wystąpień, katalog macierzysty jest współdzielona przez wszystkie wystąpienia, aby wszystkie wystąpienia Zobacz ten sam katalog. Tak, na przykład jeśli aplikacja zapisuje pliki przekazane do katalogu macierzystego, te pliki są natychmiast dostępne dla wszystkich wystąpień. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Dostęp do sieci
Kod aplikacji może być TCP/IP i protokołów opartych na protokole UDP nawiązywać połączenia wychodzącego do Internet dostępny punktów końcowych, które udostępniają usług zewnętrznych. Aplikacje można użyć tych tych samych protokołów łączenie się z usługami w systemie Azure&#151;na przykład, ustanawiając połączenia HTTPS z bazą danych SQL.

Istnieje również ograniczone możliwości aplikacje do ustanowienia jednego połączenia lokalnego sprzężenia zwrotnego i aplikacji nasłuchiwać tego gniazda lokalnego sprzężenia zwrotnego. Ta funkcja istnieje przede wszystkim, aby włączyć aplikacje, które nasłuchują na sockets lokalnego sprzężenia zwrotnego w ramach ich funkcji. Każda aplikacja czyta połączenia "prywatny" sprzężenia zwrotnego. Aplikacja "A" nie może nasłuchiwać z gniazdem lokalnego sprzężenia zwrotnego ustanowionych przez aplikację "B".

Nazwane potoki również są obsługiwane jako mechanizm komunikacji międzyprocesowej (IPC) między różnych procesów, które zbiorczo uruchomienia aplikacji. Na przykład moduł FastCGI usług IIS korzysta z nazwanych potoków do koordynowania poszczególnych procesów, które uruchamiane strony PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Wykonywanie kodu, procesów i pamięci
Jak wspomniano wcześniej, aplikacje będą działać wewnątrz procesów roboczych niskim poziomem uprawnień przy użyciu tożsamości puli aplikacji losowych. Kod aplikacji ma dostęp do obszaru pamięci skojarzone z procesu roboczego, a także wszystkie podrzędne procesy, które mogą być zduplikowany przez procesy CGI lub innych aplikacji. Jednak jednej aplikacji nie może uzyskać dostępu pamięci lub danych z innej aplikacji, nawet jeśli nie znajduje się na tej samej maszyny wirtualnej.

Aplikacje mogą być uruchamiane skrypty lub stron zapisanych z środowiska deweloperskie obsługiwanych w sieci web. Usługi aplikacji nie Skonfiguruj ustawienia sieci web framework bardziej ograniczony tryb. Na przykład aplikacji ASP.NET uruchomionych w usłudze aplikacji uruchom zaufania "pełnej" zamiast bardziej ograniczony w trybie zaufania. Struktur sieci Web, w tym ASP klasycznego i ASP.NET, można wywołać składniki modelu COM w trakcie (ale nie poza składniki COM), takich jak ADO (ActiveX Data Objects), które są zarejestrowane domyślnie w systemie operacyjnym Windows.

Aplikacje można zduplikować i uruchomić dowolny kod. Jest dozwolony dla aplikacji można wykonywać następujące czynności duplikowanie powłoki poleceń lub uruchomić skrypt programu PowerShell. Jednak nawet jeśli dowolny kod i procesy można zduplikowany z aplikacji, programów wykonywalnych i skryptów są nadal ograniczone do uprawnienia przyznane do puli aplikacji nadrzędnej. Na przykład aplikację można zduplikować plik wykonywalny, który wykonuje wychodzące wywołanie HTTP, ale ten sam pliku wykonywalnego nie ma możliwości usunięcia powiązania adres IP maszyny wirtualnej na podstawie jego karty sieciowej. Wywołania wychodzącego jest dozwolone kodu niskim poziomem uprawnień, ale podjęto próbę ponownego konfigurowania ustawień sieci na maszynie wirtualnej wymaga uprawnień administracyjnych.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Dzienników diagnostyki i zdarzeń
Informacje w dzienniku jest inny zestaw danych, że niektóre aplikacje próbują uzyskać dostęp. Typy informacji dziennika do kodu uruchamianego w środowisku usługi aplikacji obejmuje diagnostycznych oraz rejestrowania informacji generowanych przez aplikację, która również jest łatwo dostępny do aplikacji. 

Na przykład W3C HTTP dzienniki generowane przez aplikację active są dostępne, albo w katalogu dziennika w lokalizacji udziału sieciowego utworzony dla aplikacji, lub dostępne w magazynie obiektów blob, jeśli klient nie został skonfigurowany do magazynu Usługa rejestrowania W3C. Tę druga opcję umożliwia dużych ilości dzienniki, aby zebrać bez ryzyka przekraczanie limitów magazynowania pliku skojarzonego z udziałem sieciowym.

W podobny szyjnej, informacje diagnostyczne w czasie rzeczywistym z aplikacji .NET mogą również być rejestrowane przy użyciu infrastruktury diagnostyki i śledzenie .NET z opcjami, aby zapisać informacje o śledzeniu do udziału sieciowego w aplikacji, lub do lokalizacji magazynu obiektów blob.

Obszary diagnostyki rejestrowania i śledzenia, które nie są dostępne dla aplikacji są zdarzenia ETW systemu Windows i typowych dzienniki zdarzeń systemu Windows (na przykład System, aplikacji i zabezpieczenia dzienników zdarzeń). Ponieważ informacje o śledzeniu ETW potencjalnie może być widoczny dla komputera (z prawej strony listy ACL), Odczyt i zapis do zdarzenia ETW są zablokowane. Deweloperzy zauważyć, że wywołania interfejsu API do odczytywania i zapisywania ETW zdarzeń i dzienniki zdarzeń systemu Windows wspólnej pojawiają się do pracy, ale wynika to z usługi aplikacji jest "faking" wywołania, tak aby wyglądały powiodło się. W rzeczywistości kod aplikacji nie ma dostępu do tych danych zdarzenia.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Dostęp do rejestru
Aplikacje mają dostęp tylko do odczytu do znacznie (choć nie wszystkie) z rejestru maszyny wirtualnej jest uruchomiona. W praktyce oznacza to, że klucze rejestru, które umożliwiają dostęp tylko do odczytu do lokalnej grupy użytkowników są dostępne dla aplikacji. Jeden obszar rejestru, który nie jest obecnie obsługiwane dla odczytu lub zapisu jest HKEY\_bieżącego\_gałęzi użytkownika.

Dostęp do zapisu w rejestrze jest zablokowany, łącznie z dostępem do dowolnych kluczy rejestru dla poszczególnych użytkowników. Z perspektywy aplikacji dostęp do zapisu w rejestrze powinno nigdy nie być stosowane w środowisku platformy Azure od aplikacji można (i czy) Pobierz migracji w różnych maszyn wirtualnych. Tylko trwałego magazynu zapisywalny, który może być zależny od aplikacji jest struktura katalogów zawartości dla aplikacji przechowywanych w udziałach UNC usługi aplikacji. 

## <a name="more-information"></a>Więcej informacji

[Azure Web App piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -najbardziej aktualne informacje dotyczące wykonywania środowiska usługi aplikacji. Ta strona jest obsługiwana bezpośrednio przez zespół deweloperów usługi aplikacji.

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację internetową o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
> 
> 


