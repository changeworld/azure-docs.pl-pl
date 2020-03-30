---
title: Funkcjonalność systemu operacyjnego
description: Dowiedz się więcej o funkcjach systemu operacyjnego w usłudze Azure App Service w systemie Windows. Dowiedz się, jakie typy dostępu do plików, sieci i rejestru uzyskuje aplikacja.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671625"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkcjonalność systemu operacyjnego w usłudze Azure App Service
W tym artykule opisano typowe podstawowe funkcje systemu operacyjnego, które są dostępne dla wszystkich aplikacji systemu Windows uruchomionych w [usłudze Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Ta funkcja obejmuje dostęp do plików, sieci i rejestru oraz dzienniki diagnostyczne i zdarzenia. 

> [!NOTE] 
> [Aplikacje systemu Linux](containers/app-service-linux-intro.md) w usłudze App Service działają we własnych kontenerach. Żaden dostęp do systemu operacyjnego hosta nie jest dozwolony, masz dostęp administratora do kontenera. Podobnie w przypadku [aplikacji działających w kontenerach systemu Windows](app-service-web-get-started-windows-container.md)masz dostęp administracyjny do kontenera, ale nie masz dostępu do systemu operacyjnego hosta. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Warstwy planu usługi app service
Usługa App Service uruchamia aplikacje klientów w środowisku hostingu z wieloma dzierżawcami. Aplikacje wdrożone w warstwach **Bezpłatne** i **Udostępnione** działają w procesach roboczych na udostępnionych maszynach wirtualnych, podczas gdy aplikacje wdrożone w warstwach **Standard** i **Premium** działają na maszynach wirtualnych dedykowanych specjalnie dla aplikacji skojarzonych z jednym klientem.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ponieważ usługa App Service obsługuje bezproblemowe skalowanie między różnymi warstwami, konfiguracja zabezpieczeń wymuszana dla aplikacji usługi App Service pozostaje taka sama. Dzięki temu aplikacje nie zachowują się nagle inaczej, co kończy się niepowodzeniem w nieoczekiwany sposób, gdy plan usługi App Service przełącza się z jednej warstwy do drugiej.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Ramy rozwoju
Warstwy cenowe usługi App Service kontrolują ilość zasobów obliczeniowych (procesora CPU, pamięci masowej, pamięci i wyjścia sieciowego) dostępnych dla aplikacji. Jednak szerokość funkcji struktury dostępnych dla aplikacji pozostaje taka sama, niezależnie od warstw skalowania.

Usługa App Service obsługuje różne struktury programistyczne, w tym ASP.NET, klasyczny ASP, node.js, PHP i Python — wszystkie z nich działają jako rozszerzenia w usługach IIS. Aby uprościć i znormalizować konfigurację zabezpieczeń, aplikacje usługi App Service zazwyczaj uruchamiają różne struktury programistyczne z ustawieniami domyślnymi. Jednym z podejść do konfigurowania aplikacji może być dostosowanie obszaru powierzchni interfejsu API i funkcji dla każdej struktury programowania. Usługa App Service zamiast tego przyjmuje bardziej ogólne podejście, włączając wspólną linię bazową funkcji systemu operacyjnego, niezależnie od struktury rozwoju aplikacji.

W poniższych sekcjach podsumowano ogólne rodzaje funkcji systemu operacyjnego dostępne dla aplikacji usługi App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dostęp do plików
W usłudze App Service istnieją różne dyski, w tym dyski lokalne i dyski sieciowe.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Dyski lokalne
W swej istocie usługa App Service jest usługą działającą na infrastrukturze Usługi Azure PaaS (platforma jako usługa). W rezultacie dyski lokalne, które są "dołączone" do maszyny wirtualnej są te same typy dysków dostępne dla dowolnej roli procesu roboczego uruchomionej na platformie Azure. Obejmuje to:

- Dysk systemu operacyjnego (D:\ dysku)
- Dysk aplikacji zawierający pliki cspkg pakietu Azure używane wyłącznie przez usługę App Service (i niedostępne dla klientów)
- Dysk "użytkownika" (C:\ dysk), którego rozmiar różni się w zależności od rozmiaru maszyny Wirtualnej. 

Ważne jest, aby monitorować wykorzystanie dysku w miarę rozwoju aplikacji. Jeśli przydział dysku zostanie osiągnięty, może mieć niekorzystne skutki dla aplikacji. Przykład: 

- Aplikacja może rzucać błąd wskazujący za mało miejsca na dysku.
- Podczas przeglądania konsoli Kudu mogą wystąpić błędy dysku.
- Wdrożenie z usługi Azure DevOps `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`lub Visual Studio może zakończyć się niepowodzeniem w programie .
- Twoja aplikacja może mieć powolną wydajność.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Dyski sieciowe (aka akcji UNC)
Jednym z unikatowych aspektów usługi App Service, która sprawia, że wdrażanie aplikacji i konserwacja są proste, jest to, że cała zawartość użytkownika jest przechowywana w zestawie udziałów UNC. Ten model dobrze mapuje do wspólnego wzorca magazynu zawartości używanego przez lokalne środowiska hostingu sieci Web, które mają wiele serwerów z równoważenia obciążenia. 

W usłudze App Service w każdym centrum danych utworzono wiele udziałów UNC. Procent zawartości użytkownika dla wszystkich klientów w każdym centrum danych jest przydzielany do każdego udziału UNC. Ponadto cała zawartość pliku dla subskrypcji jednego klienta jest zawsze umieszczana w tej samej udziale UNC. 

Ze względu na sposób działania usług platformy Azure określona maszyna wirtualna odpowiedzialna za hosting udziału UNC zmieni się wraz z czasem. Jest gwarantowana, że udziały UNC będą montowane przez różne maszyny wirtualne, ponieważ są one wychowane i w dół podczas normalnego przebiegu operacji platformy Azure. Z tego powodu aplikacje nigdy nie powinny zakładać, że informacje o komputerze w ścieżce pliku UNC pozostaną stabilne w czasie. Zamiast tego należy użyć *wygodnej* ścieżki bezwzględnej **faux D:\home\site,** którą zapewnia usługa App Service. Ta sztuczna ścieżka absolutna zapewnia przenośną metodę niezależną od aplikacji i użytkownika do odwoływania się do własnej aplikacji. Za pomocą **D:\home\site**można przesyłać udostępnione pliki z aplikacji do aplikacji bez konieczności konfigurowania nowej ścieżki bezwzględnej dla każdego transferu.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy dostępu do plików przyznane aplikacji
Subskrypcja każdego klienta ma strukturę zastrzeżonego katalogu na określonym udziale UNC w centrum danych. Klient może mieć wiele aplikacji utworzonych w określonym centrum danych, więc wszystkie katalogi należące do jednej subskrypcji klienta są tworzone w tym samym udziale UNC. Udział może zawierać katalogi, takie jak te dla zawartości, dzienniki błędów i diagnostyki oraz wcześniejsze wersje aplikacji utworzone przez formant źródła. Zgodnie z oczekiwaniami katalogi aplikacji klienta są dostępne do odczytu i zapisu w czasie wykonywania przez kod aplikacji aplikacji.

Na dyskach lokalnych podłączonych do maszyny wirtualnej, na których działa aplikacja, usługa App Service rezerwuje fragment miejsca na C:\ do tymczasowego magazynu lokalnego dla aplikacji. Mimo że aplikacja ma pełny dostęp do odczytu/zapisu do własnego tymczasowego magazynu lokalnego, że magazyn naprawdę nie jest przeznaczony do użycia bezpośrednio przez kod aplikacji. Celem jest raczej zapewnienie tymczasowego magazynu plików dla usług IIS i struktur aplikacji sieci web. Usługa App Service ogranicza również ilość tymczasowego magazynu lokalnego dostępnego dla każdej aplikacji, aby zapobiec zużywaniu przez poszczególne aplikacje nadmiernej ilości lokalnego magazynu plików.

Dwa przykłady użycia tymczasowego magazynu lokalnego przez usługę App Service to katalog plików ASP.NET tymczasowych i katalog skompresowanych plików usług IIS. System kompilacji ASP.NET używa katalogu "Tymczasowe pliki ASP.NET" jako tymczasowej lokalizacji pamięci podręcznej kompilacji. Usługi IIS używają katalogu "Tymczasowe skompresowane pliki IIS" do przechowywania skompresowanych danych wyjściowych odpowiedzi. Oba te typy użycia plików (jak również inne) są mapowane w usłudze App Service do tymczasowego magazynu lokalnego dla aplikacji. To ponowne mapowanie zapewnia, że funkcjonalność jest kontynuowana zgodnie z oczekiwaniami.

Każda aplikacja w usłudze App Service działa jako losowa unikatowa tożsamość procesu procesu [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)pracownika o niskich uprawnieniach o nazwie "tożsamość puli aplikacji", opisana dalej tutaj: . Kod aplikacji używa tej tożsamości do podstawowego dostępu tylko do odczytu do dysku systemu operacyjnego (D:\ dysku). Oznacza to, że kod aplikacji może wyświetlić listę typowych struktur katalogów i odczytać typowe pliki na dysku systemu operacyjnego. Chociaż może się to wydawać, że jest to dość szeroki poziom dostępu, te same katalogi i pliki są dostępne podczas inicjowania obsługi administracyjnej roli procesu roboczego w usłudze hosta platformy Azure i odczytywania zawartości dysku. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Dostęp do plików w wielu wystąpieniach
Katalog macierzysty zawiera zawartość aplikacji, a kod aplikacji może do niej zapisywać. Jeśli aplikacja działa w wielu wystąpieniach, katalog macierzysty jest współużytkowane przez wszystkie wystąpienia, dzięki czemu wszystkie wystąpienia widzą ten sam katalog. Na przykład jeśli aplikacja zapisuje przekazane pliki do katalogu macierzystego, pliki te są natychmiast dostępne dla wszystkich wystąpień. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Dostęp do sieci
Kod aplikacji może używać protokołów opartych na protokołach TCP/IP i UDP do nawiązywać wychodzące połączenia sieciowe z punktami końcowymi dostępnymi w Internecie, które ujawniają usługi zewnętrzne. Aplikacje mogą używać tych samych protokołów do łączenia się z usługami na platformie Azure, na przykład przez ustanowienie połączeń HTTPS z bazą danych SQL.

Istnieje również ograniczona możliwość dla aplikacji do ustanowienia jednego lokalnego połączenia sprzężenia zwrotnego i mieć nasłuchiwać aplikacji na tym lokalnym gnieździe sprzężenia zwrotnego. Ta funkcja istnieje przede wszystkim w celu włączenia aplikacji, które nasłuchują na lokalnych gniazdach sprzężenia zwrotnego w ramach ich funkcji. Każda aplikacja widzi "prywatne" połączenie sprzężenia zwrotnego. Aplikacja "A" nie może nasłuchiwać lokalnego gniazda sprzężenia zwrotnego ustanowionego przez aplikację "B".

Nazwane potoki są również obsługiwane jako mechanizm komunikacji między procesami (IPC) między różnymi procesami, które zbiorowo uruchamiają aplikację. Na przykład moduł IIS FastCGI opiera się na nazwanych potokach do koordynowania poszczególnych procesów uruchamianych stron PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Wykonanie kodu, procesy i pamięć
Jak wspomniano wcześniej, aplikacje działają wewnątrz procesów roboczych o niskich uprawnieniach przy użyciu tożsamości puli aplikacji losowych. Kod aplikacji ma dostęp do miejsca w pamięci skojarzonego z procesem roboczym, a także do wszystkich procesów podrzędnych, które mogą być zduplikowane przez procesy CGI lub inne aplikacje. Jednak jedna aplikacja nie może uzyskać dostępu do pamięci lub danych innej aplikacji, nawet jeśli znajduje się na tej samej maszynie wirtualnej.

Aplikacje mogą uruchamiać skrypty lub strony napisane za pomocą obsługiwanych struktur programowania sieci Web. Usługa App Service nie konfiguruje żadnych ustawień struktury sieci web do bardziej ograniczonych trybów. Na przykład ASP.NET aplikacje uruchomione w usłudze App Service działają w "pełnym" zaufaniu, w przeciwieństwie do trybu bardziej ograniczonego zaufania. Struktury sieci Web, w tym zarówno klasyczne ASP, jak i ASP.NET, mogą wywoływać składniki COM w trakcie procesu (ale nie poza procesem składniki COM), takie jak ADO (ActiveX Data Objects), które są domyślnie zarejestrowane w systemie operacyjnym Windows.

Aplikacje mogą odradzać i uruchamiać dowolny kod. Jest dopuszczalne dla aplikacji do takich rzeczy, takich jak spawn powłoki polecenia lub uruchomić skrypt programu PowerShell. Jednak mimo że dowolny kod i procesy mogą być zduplikowane z aplikacji, programy wykonywalne i skrypty są nadal ograniczone do uprawnień przyznanych nadrzędnej puli aplikacji. Na przykład aplikacja może zrodzić plik wykonywalny, który wywołuje wychodzące WYWOŁANIE HTTP, ale ten sam plik wykonywalny nie może próbować odłączyć adresu IP maszyny wirtualnej z karty sieciowej. Wykonywanie wychodzącego wywołania sieciowego jest dozwolone dla kodu o niskich uprawnieniach, ale próba ponownego skonfigurowania ustawień sieciowych na maszynie wirtualnej wymaga uprawnień administracyjnych.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Dzienniki diagnostyczne i zdarzenia
Informacje dziennika to kolejny zestaw danych, do których niektóre aplikacje próbują uzyskać dostęp. Typy informacji dziennika dostępnych do kodu uruchomionego w usłudze App Service obejmują informacje diagnostyczne i dziennika generowane przez aplikację, która jest również łatwo dostępna dla aplikacji. 

Na przykład dzienniki HTTP W3C generowane przez aktywną aplikację są dostępne w katalogu dziennika w lokalizacji udziału sieciowego utworzonej dla aplikacji lub dostępne w magazynie obiektów blob, jeśli klient skonfigurował rejestrowanie W3C do magazynu. Ta ostatnia opcja umożliwia zbieranie dużych ilości dzienników bez ryzyka przekroczenia limitów przechowywania plików skojarzonych z udziałem sieciowym.

W podobnym duchu informacje diagnostyczne w czasie rzeczywistym z aplikacji .NET mogą być również rejestrowane przy użyciu infrastruktury śledzenia i diagnostyki .NET, z opcjami zapisywania informacji śledzenia do udziału sieciowego aplikacji lub alternatywnie do lokalizacji magazynu obiektów blob.

Obszary rejestrowania i śledzenia diagnostyki, które nie są dostępne dla aplikacji, to zdarzenia ETW systemu Windows i typowe dzienniki zdarzeń systemu Windows (na przykład dzienniki zdarzeń system, aplikacja i zabezpieczenia). Ponieważ informacje śledzenia ETW mogą być potencjalnie widoczne w całej maszynie (z odpowiednimi listami ACL), dostęp do odczytu i zapisu do zdarzeń ETW jest zablokowany. Deweloperzy mogą zauważyć, że wywołania interfejsu API do odczytu i zapisu zdarzeń ETW i wspólnych dzienników zdarzeń systemu Windows wydają się działać, ale to dlatego, że usługa app service jest "udawanie" wywołań, tak aby wydają się pomyślnie. W rzeczywistości kod aplikacji nie ma dostępu do danych tego zdarzenia.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Dostęp do rejestru
Aplikacje mają dostęp tylko do odczytu do wielu (choć nie wszystkie) rejestru maszyny wirtualnej, na których są uruchomione. W praktyce oznacza to, że klucze rejestru, które umożliwiają dostęp tylko do odczytu do lokalnej grupy Użytkownicy są dostępne dla aplikacji. Jednym z obszarów rejestru, który obecnie nie jest obsługiwany dla dostępu\_\_do odczytu lub zapisu, jest gałąź HKEY CURRENT USER.

Dostęp do zapisu do rejestru jest zablokowany, w tym dostęp do dowolnych kluczy rejestru dla każdego użytkownika. Z punktu widzenia aplikacji dostęp do zapisu do rejestru nigdy nie powinien polegać w środowisku platformy Azure, ponieważ aplikacje mogą (i robią) migrować przez różne maszyny wirtualne. Jedynym trwałym zapisywalnym magazynem, od których może polegać aplikacja, jest struktura katalogów zawartości dla aplikacji przechowywana w udziałach UNC usługi App Service. 

## <a name="remote-desktop-access"></a>Dostęp do pulpitu zdalnego

Usługa App Service nie zapewnia dostępu pulpitu zdalnego do wystąpień maszyn wirtualnych.

## <a name="more-information"></a>Więcej informacji

[Piaskownica usługi Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) — najbardziej aktualne informacje o środowisku wykonywania usługi App Service. Ta strona jest obsługiwana bezpośrednio przez zespół deweloperów usługi App Service.

