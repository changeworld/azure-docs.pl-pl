---
title: Funkcjonalność systemu operacyjnego w usłudze App Service — platformy Azure
description: Dowiedz się więcej o funkcji systemu operacyjnego, dostępnych dla aplikacji internetowych, zapleczy aplikacji mobilnych i aplikacji API apps w usłudze Azure App Service
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
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e5ab6651503766844b2aeef1849bffff9cf4d7bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835513"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkcjonalność systemu operacyjnego w usłudze Azure App Service
W tym artykule opisano typowych funkcji systemu operacyjnego linii bazowej, która jest dostępna dla wszystkich aplikacji Windows działających na [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Ta funkcja obejmuje plików, sieci i dostępu do rejestru i dzienniki diagnostyczne i zdarzenia. 

> [!NOTE] 
> [Aplikacje systemu Linux](containers/app-service-linux-intro.md) w usłudze App Service, uruchom w ich własnych kontenerów. Brak dostępu do systemu operacyjnego hosta jest dozwolone, masz dostęp do kontenera na poziomie administratora. Podobnie, aby uzyskać [aplikacji działających w kontenerach Windows](app-service-web-get-started-windows-container.md), masz dostęp administracyjny do kontenera, ale brak dostępu do systemu operacyjnego hosta. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Warstw planu usługi App Service
Usługa App Service uruchamia aplikacje klienta w środowisku hostingu z wieloma dzierżawami. Aplikacje wdrożone w **bezpłatna** i **Shared** warstwy Uruchom w procesach roboczych na udostępnionych maszynach wirtualnych, gdy aplikacje wdrożone w **standardowa** i **— wersja Premium**  warstwy uruchamiania na maszynach wirtualnych w wersji dedykowanej specjalnie dla aplikacji skojarzonych z jednego klienta.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ponieważ usługi App Service umożliwia bezproblemowe skalowanie między różnych warstw, konfiguracji zabezpieczeń, wymuszane dla aplikacji usługi App Service pozostaje taki sam. Daje to gwarancję, że aplikacje nie nagle zachowywać się inaczej, kończy się niepowodzeniem w nieoczekiwany sposób, gdy plan usługi App Service zmienia się z jednej warstwy do innej.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Struktur programowania
Warstwy cenowe usługi App Service kontroli ilości zasobów obliczeniowych (procesora CPU, dysku magazynu, pamięci i wychodzący ruch sieciowy) dostępne dla aplikacji. Jednak szeroki zakres funkcji framework, które są dostępne dla aplikacji pozostaje taki sam, niezależnie od skalowania warstwy.

Usługa App Service obsługuje szereg struktur programowania, w tym ASP.NET, klasyczne ASP, node.js, PHP i Python — wszystkie z nich są uruchamiane jako rozszerzenia w ramach usług IIS. Aplikacje usługi App Service w celu uproszczenia i normalizacji konfiguracji zabezpieczeń, jest zazwyczaj uruchamiane różnych struktur programowania przy użyciu ustawień domyślnych. Jedno z podejść do konfigurowania aplikacji mogło być dostosować obszar powierzchni interfejsu API i funkcje dla każdej struktury poszczególnych rozwoju. Usługi App Service zamiast tego przyjmuje bardziej ogólnym podejściem, należy włączyć wspólne podstawową funkcjonalność systemu operacyjnego, niezależnie od tego, w ramach rozwoju aplikacji.

W poniższych sekcjach opisano ogólne rodzaje funkcjonalność systemu operacyjnego są dostępne dla aplikacji usługi App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dostęp do plików
Istnieją różne dyski w ramach usługi App Service, w tym dyskach lokalnych i dyskach sieciowych.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Dyski lokalne
Zasadniczo App Service to w usłudze działającej na szczycie infrastruktury Azure PaaS (platforma jako usługa). W rezultacie dyski lokalne, dołączonych "" do maszyny wirtualnej są takie same typy dysku dostępne dla dowolnej roli procesu roboczego, działające na platformie Azure. Obejmuje to:

- Dysk systemu operacyjnego (dysku D:\)
- Dysk aplikacji, który zawiera pliki cspkg pakiet Azure używany wyłącznie przez usługę App Service (i dostępnych dla klientów)
- "User" dysku (dysk C:\), którego rozmiar różni się w zależności od rozmiaru maszyny Wirtualnej. 

Należy monitorować wykorzystaniu dysku, w miarę wzrostu aplikacji. W przypadku osiągnięcia limitu przydziału dysku może mieć niepożądane skutki do aplikacji. Na przykład: 

- Aplikacja może zgłaszać błąd wskazujący za mało miejsca na dysku.
- Podczas przeglądania do konsoli Kudu, mogą pojawić się błędy na dysku.
- Wdrażanie z usługi VSTS lub Visual Studio może się nie powieść `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`.
- Aplikacja może to spowodować obniżenie wydajności.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Dyskach sieciowych (zwane również udziały UNC)
Jednym z unikatowe aspekty usługi App Service, która umożliwia proste wdrażanie aplikacji i konserwacji jest, że cała zawartość użytkownika jest przechowywany na zbiór udziały UNC. Ten model mapuje dobrze wspólny wzorzec przechowywania zawartości, które korzystają z lokalnych hostingu środowiska, w których wiele serwerów z równoważeniem obciążenia. 

W ramach usługi App Service istnieje szereg udziały UNC, utworzone w każdym centrum danych. Procent zawartość użytkownika dla wszystkich klientów w każdym centrum danych jest przydzielona do każdego udziału UNC. Ponadto zawartość dla subskrypcji pojedynczego klienta zawsze znajduje się na tym samym UNC pliku udostępniać. 

Ze względu na sposób Azure działają usługi odpowiedzialny za hostingu udziału UNC określonej maszyny wirtualnej zmieni się wraz z upływem czasu. Ma żadnej gwarancji, że udziały UNC zostanie zainstalowany przez różnych maszyn wirtualnych zgodnie z ich wprowadzenia w górę i w dół w trakcie normalnego przebiegu operacji na platformie Azure. Z tego powodu aplikacje nigdy nie należy upewnić ustaloną założenia, że informacje o maszynie w ścieżce UNC pliku jest trwały na wraz z upływem czasu. Zamiast tego należy używać wygodne *symulowane* ścieżka bezwzględna **D:\home\site** udostępniająca usługi App Service. Ta ścieżka bezwzględna symulowane zapewnia przenośnych, i — użytkownik — niezależnie od aplikacji metoda odwoływania się do własnych aplikacji. Za pomocą **D:\home\site**, jeden mogą przesyłać pliki udostępnione z aplikacji bez konieczności konfigurowania nowej ścieżki bezwzględnej dla każdego transferu.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy plików udzielono dostępu do aplikacji
Subskrypcja każdy klient ma strukturę katalogów zarezerwowane w określonym udziale UNC w centrum danych. Klient może mieć wiele aplikacji tworzone w obrębie centrum danych specyficznych dla wszystkich katalogów należącymi do subskrypcji pojedynczego klienta są tworzone na tym samym UNC udostępniania. Udział może obejmować katalogi, takich jak te dotyczące zawartości, błędów i dzienniki diagnostyczne i wcześniejszych wersjach aplikację utworzoną za pomocą kontroli źródła. Zgodnie z oczekiwaniami, katalogów aplikacji klienta są dostępne do odczytu i zapisu w czasie wykonywania przez kod aplikacji w aplikacji.

Na dyski lokalne dołączone do maszyny wirtualnej, która uruchamia aplikację usługa App Service rezerwuje fragmentów miejsca na dysku C:\ jako tymczasowego magazynu lokalnego specyficzny dla aplikacji. Mimo że aplikacja ma pełny odczyt/zapis dostęp do jego własnej tymczasowego magazynu lokalnego, że magazyn tak naprawdę nie jest przeznaczona do użycia bezpośrednio przez kod aplikacji. Przeciwnie celem jest zapewnienie przechowywania plików tymczasowych dla usług IIS i sieci web struktury aplikacji. Usługa App Service również ogranicza tymczasowego magazynu lokalnego, dostępne dla poszczególnych aplikacji, aby uniemożliwić korzystanie z nadmiernej ilości pamięci masowej z pliku lokalnego poszczególnych aplikacji.

Jak usługa App Service używa tymczasowy magazyn lokalny dwa przykłady są katalogu na pliki tymczasowe platformy ASP.NET i katalogu dla usług IIS skompresowane pliki. Systemu kompilacji platformy ASP.NET używa katalogu "Temporary ASP.NET Files" jako lokalizację pamięci podręcznej tymczasowe kompilacji. Usługi IIS używają katalogu "Usług IIS skompresowanych plików tymczasowych" do przechowywania danych wyjściowych skompresowanych odpowiedzi. Oba typy plików użycia (a także inne) są mapowane ponownie w usłudze App Service do lokalnego magazynu tymczasowego dla aplikacji. To ponowne mapowanie gwarantuje, że funkcje jest kontynuowane zgodnie z oczekiwaniami.

Każda aplikacja w usłudze App Service działa jako tożsamość procesu losowy unikatowy procesu roboczego z niskim poziomem uprawnień, o nazwie "tożsamość puli aplikacji", opisane dalej w tym miejscu: [ https://www.iis.net/learn/manage/configuring-security/application-pool-identities ](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Kod aplikacji używa tej tożsamości dla podstawowego dostępu tylko do odczytu na dysku systemu operacyjnego (dysku D:\). Oznacza to, kod aplikacji, można wyświetlić listę typowych struktur katalogów i przeczytaj wspólne pliki na dysku systemu operacyjnego. Chociaż to może się wydawać nieco szerokiego poziom dostępu, tym samym katalogi i pliki są dostępne podczas aprowizowania roli proces roboczy na platformie Azure usługa hostowana i odczytywać zawartość dysku. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Dostęp do plików w wielu wystąpieniach
Katalog macierzysty zawiera zawartość aplikacji, a kod aplikacji może zapisywać do niego. Jeśli aplikacja działa w wielu wystąpieniach, katalog macierzysty jest udostępniona wszystkim wystąpieniom, dzięki czemu wszystkie wystąpienia widoczne w tym samym katalogu. Tak na przykład, jeśli aplikacja zapisuje przekazywanych plików do katalogu macierzystego, te pliki są natychmiast dostępne dla wszystkich wystąpień. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Dostęp do sieci
Kod aplikacji może być TCP/IP i protokołów opartych na protokole UDP nawiązywać połączenia sieciowego ruchu wychodzącego z Internet dostępne punkty końcowe, z użyciem usług zewnętrznych. Aplikacje mogą używać tych tych samych protokołów łączenie się z usługami w ramach platformy Azure&#151;na przykład, ustanawiając połączenia HTTPS do usługi SQL Database.

Istnieje również ograniczone możliwości dla aplikacji ustanowić jedno połączenie lokalnego sprzężenia zwrotnego i aplikacji nasłuchiwać tego gniazda lokalnego sprzężenia zwrotnego. Ta funkcja istnieje głównie w celu przygotowania aplikacji, które nasłuchują na sockets lokalnego sprzężenie zwrotne jako część ich funkcje. Każda aplikacja widzi połączenia sprzężenia zwrotnego "private". Aplikacja "A" nie może nasłuchiwać na gniazdo lokalnego sprzężenie zwrotne ustanowione przez aplikację "B".

Nazwane potoki są również obsługiwane w mechanizmie komunikacji międzyprocesowej (IPC) między różne procesy, które wspólnie uruchamiania aplikacji. Na przykład moduł FastCGI usług IIS opiera się na nazwanych potoków do zapewnienia koordynacji procesów poszczególnych, systemem strony PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Wykonywanie kodu, procesów i pamięci
Jak wspomniano wcześniej, aplikacje są uruchamiane wewnątrz niskim poziomem uprawnień procesów przy użyciu tożsamości puli aplikacji losowych. Kod aplikacji ma dostęp do obszaru pamięci skojarzone z procesu roboczego, a także wszystkie podrzędne procesy, które może być zduplikowana przez procesy CGI lub innych aplikacji. Jednak jedna aplikacja nie może uzyskać dostępu pamięci lub danych innej aplikacji, nawet jeśli nie znajduje się na tej samej maszyny wirtualnej.

Aplikacje mogą być uruchamiane skrypty lub stron napisane przy użyciu struktur projektowania sieci web obsługiwane. Usługa App Service nie Skonfiguruj ustawienia framework sieci web do bardziej ograniczony tryby. Na przykład aplikacji ASP.NET uruchomionej w usłudze App Service, uruchom w zaufaniu "pełnej" w przeciwieństwie do bardziej ograniczony tryb zaufania. Platform sieci Web, w tym ASP klasycznego i ASP.NET, można wywołać w procesie komponentów COM (ale nie poza składników COM, proces), takich jak ADO (ActiveX Data Objects), która jest zarejestrowana domyślnie w systemie operacyjnym Windows.

Aplikacje można zduplikować i uruchomić dowolny kod. Jest dozwolony dla aplikacji wykonywanie takich czynności jak duplikowanie powłoki poleceń lub uruchom skrypt programu PowerShell. Mimo że dowolnego kodu i procesów można zduplikowany z aplikacji, programów wykonywalnych i skryptów są jednak nadal ograniczony do uprawnień przyznanych nadrzędnej puli aplikacji. Na przykład aplikację można zduplikować plik wykonywalny, który sprawia, że wywołanie HTTP ruchu wychodzącego, ale ta sama pliku wykonywalnego nie ma możliwości usunięcia powiązania adres IP maszyny wirtualnej na podstawie jego karty sieciowej. Wywołania sieciowe dotyczące połączeń wychodzących jest dozwolony na kod z niskim poziomem uprawnień, ale podjęto próbę ponownego konfigurowania ustawień sieciowych na maszynie wirtualnej wymaga uprawnień administracyjnych.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Dzienniki diagnostyczne i zdarzenia
Informacje w dzienniku jest inny zestaw danych, które niektóre aplikacje próbują uzyskać dostęp. Typy informacji dziennika, które są dostępne dla kodu uruchamianego w środowisku usługi App Service obejmuje diagnostyki i rejestrowania informacji generowanych przez aplikację, która również jest łatwo dostępny do aplikacji. 

Na przykład W3C HTTP dzienniki generowane przez aktywnych aplikacji dostępnych w katalogu dziennika w lokalizacji w udziale sieciowym, utworzony dla aplikacji, lub będą dostępne w magazynie obiektów blob, jeśli klient został skonfigurowany do rejestrowania W3C do magazynu. Tę druga opcję umożliwia dużych ilości dzienniki, aby zebrać bez ryzyka przekroczenia limitów magazynu plików skojarzonych z udziału sieciowego.

W podobny względzie informacji diagnostycznych w czasie rzeczywistym z aplikacji platformy .NET mogą również być rejestrowane za pomocą śledzenia .NET i infrastruktury diagnostyki z opcjami, aby zapisać informacje o śledzeniu do udziału sieciowego przez aplikację, lub też do lokalizacji magazynu obiektów blob.

Obszary diagnostyki rejestrowania i śledzenia, które nie są dostępne dla aplikacji są zdarzenia Windows ETW i dzienniki zdarzeń w usłudze common Windows (na przykład systemu, aplikacji i zabezpieczeń dzienniki zdarzeń). Ponieważ informacje ze śledzenia zdarzeń systemu Windows może potencjalnie być widoczne dla komputera (z prawej strony listy ACL), są blokowane odczytu i zapisu do zdarzenia ETW. Deweloperzy zauważyć, że wywołania interfejsu API do odczytu i zapisu ETW, zdarzeń i dzienników zdarzeń w usłudze common Windows wydaje się działać, ale wynika to z usługi App Service jest "faking" wywołania, tak aby pojawiały się sukcesem. W rzeczywistości kod aplikacji nie ma dostępu do tych danych zdarzenia.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Dostęp do rejestru
Aplikacje mają dostęp tylko do odczytu do większości (choć nie wszystkie) rejestru maszyny wirtualnej są uruchomione. W praktyce oznacza to, że klucze rejestru, które umożliwiają dostęp tylko do odczytu do lokalnej grupy użytkowników są dostępne dla aplikacji. Jeden obszar rejestru, który nie jest obecnie obsługiwane dla odczytu lub zapisu jest HKEY\_bieżącego\_gałęzi użytkownika.

Dostęp do zapisu w rejestrze jest zablokowany, łącznie z dostępem do żadnych kluczy rejestru na użytkownika. Z perspektywy aplikacji dostęp do zapisu w rejestrze powinno nigdy nie można polegać w środowisku platformy Azure, ponieważ aplikacje można (i należy) migrowani na różnych maszynach wirtualnych. Tylko trwałego magazynu zapisywalny, który może być zależna od aplikacji jest struktury zawartości katalogu aplikacji przechowywanych w udziałach UNC usługi aplikacji. 

## <a name="remote-desktop-access"></a>Dostęp do pulpitu zdalnego

Usługa App Service nie zapewnia dostęp do pulpitu zdalnego do wystąpień maszyn wirtualnych.

## <a name="more-information"></a>Więcej informacji

[Usługa Azure App Service piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) — najbardziej aktualne informacje o środowisku wykonawczym usługi App Service. Na tej stronie są obsługiwane bezpośrednio przez zespół programistyczny usługi App Service.

