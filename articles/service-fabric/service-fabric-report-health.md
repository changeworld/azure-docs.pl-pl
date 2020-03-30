---
title: Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług
description: W tym artykule opisano sposób wysyłania niestandardowych raportów kondycji do jednostek kondycji sieci szkieletowej usługi Azure. Zawiera zalecenia dotyczące projektowania i wdrażania raportów o kondycji wysokiej jakości.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451584"
---
# <a name="add-custom-service-fabric-health-reports"></a>Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług
Usługa Azure Service Fabric wprowadza [model kondycji](service-fabric-health-introduction.md) przeznaczony do flagi w złej kondycji klastra i warunki aplikacji dla określonych jednostek. Model kondycji używa **reporterów kondycji** (składniki systemu i watchdogs). Celem jest łatwa i szybka diagnoza i naprawa. Autorzy usług muszą myśleć z góry o zdrowiu. Każdy warunek, który może mieć wpływ na zdrowie, powinien być zgłaszany, zwłaszcza jeśli może pomóc flagi problemów w pobliżu katalogu głównego. Informacje o kondycji można zaoszczędzić czas i wysiłek na debugowanie i badania. Przydatność jest szczególnie jasne, gdy usługa jest uruchomiona na dużą skalę w chmurze (prywatne lub azure).

Reporterzy Service Fabric monitorują zidentyfikowane warunki zainteresowania. Informują o tych warunkach na podstawie ich lokalnego poglądu. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkich reporterów, aby ustalić, czy jednostki są globalnie zdrowe. Model ma być bogaty, elastyczny i łatwy w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Fałszywe alarmy, które błędnie pokazują niezdrowe problemy mogą negatywnie wpłynąć na uaktualnienia lub inne usługi korzystające z danych o kondycji. Przykładami takich usług są usługi naprawcze i mechanizmy alarmowe. W związku z tym niektóre myśli jest potrzebne do dostarczania raportów, które przechwytywania warunki zainteresowania w najlepszy możliwy sposób.

Aby zaprojektować i wdrożyć raportowanie kondycji, watchdogs i składniki systemu muszą:

* Zdefiniuj warunek, który ich interesuje, sposób, w jaki jest monitorowany, oraz wpływ na funkcjonalność klastra lub aplikacji. Na podstawie tych informacji zdecyduj o właściwości raportu kondycji i stanie kondycji.
* Określ [jednostkę,](service-fabric-health-introduction.md#health-entities-and-hierarchy) której dotyczy raport.
* Określ, gdzie jest wykonywane raportowanie, z poziomu usługi lub z wewnętrznego lub zewnętrznego watchdog.
* Zdefiniuj źródło używane do identyfikowania reportera.
* Wybierz strategię raportowania okresowo lub na przejściach. Zalecany sposób jest okresowo, ponieważ wymaga prostszego kodu i jest mniej podatne na błędy.
* Określ, jak długo raport dla niezdrowych warunków powinien pozostać w magazynie kondycji i jak powinien zostać wyczyszczony. Korzystając z tych informacji, zdecyduj o czasie raportu, aby zachować żywo i usuwać po wygaśnięciu.

Jak wspomniano, raportowanie można wykonać z:

* Monitorowana replika usługi sieci szkieletowej usług.
* Wewnętrzne watchdogs wdrożone jako usługa sieci szkieletowej usług (na przykład usługa bezstanowa sieci szkieletowej usług, która monitoruje warunki i raporty problemów). Watchdogs można wdrożyć wszystkie węzły lub mogą być affinitized do monitorowanej usługi.
* Wewnętrzne watchdogs, które są uruchamiane w węzłach sieci szkieletowej usług, ale *nie* są implementowane jako usługi sieci szkieletowej usług.
* Zewnętrzne watchdogs, które sondują zasób *spoza* klastra sieci szkieletowej usług (na przykład usługi monitorowania, takich jak Gomez).

> [!NOTE]
> Po wyjęciu z pudełka klaster jest wypełniany raportami kondycji wysyłanymi przez składniki systemu. Dowiedz się więcej na [temat Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Raporty użytkowników muszą być wysyłane na [jednostki kondycji,](service-fabric-health-introduction.md#health-entities-and-hierarchy) które zostały już utworzone przez system.
> 
> 

Gdy projekt raportowania kondycji jest jasny, raporty o kondycji mogą być łatwo wysyłane. [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) służy do raportowania kondycji, jeśli klaster nie jest [bezpieczny](service-fabric-cluster-security.md) lub jeśli klient sieci szkieletowej ma uprawnienia administratora. Raportowanie można wykonać za pośrednictwem interfejsu API przy użyciu [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), za pośrednictwem programu PowerShell lub za pośrednictwem REST. Raporty wsadowe pokręteł konfiguracji w celu zwiększenia wydajności.

> [!NOTE]
> Kondycja raportu jest synchroniczne i reprezentuje tylko prace sprawdzania poprawności po stronie klienta. Fakt, że raport jest akceptowany przez `Partition` klienta `CodePackageActivationContext` kondycji lub lub obiektów nie oznacza, że jest stosowany w magazynie. Jest wysyłany asynchronicznie i ewentualnie partią z innymi raportami. Przetwarzanie na serwerze może nadal zakończyć się niepowodzeniem: numer sekwencyjny może być przestarzały, jednostka, na której raport musi zostać zastosowany, została usunięta itp.
> 
> 

## <a name="health-client"></a>Klient kondycji
Raporty kondycji są wysyłane do menedżera kondycji za pośrednictwem klienta kondycji, który mieszka wewnątrz klienta sieci szkieletowej. Menedżer kondycji zapisuje raporty w magazynie zdrowia. Klienta kondycji można skonfigurować z następującymi ustawieniami:

* **HealthReportSendInterval**: Opóźnienie między czasem, w której raport jest dodawany do klienta, a czasem, w której jest wysyłany do menedżera kondycji. Służy do wsadowania raportów w jednej wiadomości, zamiast wysyłać jedną wiadomość dla każdego raportu. Wsadowanie zwiększa wydajność. Domyślnie: 30 sekund.
* **HealthReportRetrySendInterval:** Interwał, w którym klient kondycji ponownie przekazuje skumulowane raporty kondycji do menedżera kondycji. Domyślnie: 30 sekund, minimum: 1 sekunda.
* **HealthOperationTimeout:** Limit czasu dla wiadomości raportu wysłanej do menedżera kondycji. Jeśli usiłuje przesunięty upływ, klient kondycji ponawia go, dopóki menedżer kondycji potwierdzi, że raport został przetworzony. Domyślnie: dwie minuty.

> [!NOTE]
> Gdy raporty są wsadowe, klient sieci szkieletowej musi być utrzymywany przy życiu dla co najmniej HealthReportSendInterval, aby upewnić się, że są wysyłane. Jeśli wiadomość zostanie utracona lub menedżer kondycji nie może zastosować ich z powodu błędów przejściowych, klient sieci szkieletowej musi być utrzymywany przy życiu dłużej, aby dać mu szansę na ponowienie próby.
> 
> 

Buforowanie na kliencie bierze pod uwagę unikatowość raportów. Na przykład jeśli określonego złego reportera zgłasza 100 raportów na sekundę na tej samej właściwości tej samej jednostki, raporty są zastępowane ostatnią wersją. Co najwyżej jeden taki raport istnieje w kolejce klienta. Jeśli przetwarzanie wsadowe jest skonfigurowane, liczba raportów wysłanych do menedżera kondycji jest tylko jeden na interwał wysyłania. Ten raport jest ostatnim dodanym raportem, który odzwierciedla najbardziej aktualny stan jednostki.
Określ parametry `FabricClient` konfiguracji podczas tworzenia przez [przekazywanie FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) z żądanymi wartościami dla wpisów związanych ze zdrowiem.

Poniższy przykład tworzy klienta sieci szkieletowej i określa, że raporty powinny być wysyłane po ich dodaniu. Na limity czasu i błędy, które mogą być ponowione, ponownych prób się co 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Zalecamy zachowanie domyślnych ustawień klienta `HealthReportSendInterval` sieci szkieletowej, które są ustawione na 30 sekund. To ustawienie zapewnia optymalną wydajność dzięki dosadowaniu. W przypadku raportów krytycznych, które muszą `HealthReportSendOptions` być `true` wysyłane tak szybko, jak to możliwe, należy użyć z natychmiastowe w [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Natychmiastowe raporty pomijają interwał przetwarzania wsadowego. Używaj tej flagi ostrożnie; chcemy skorzystać z przetwarzania wsadowego klienta kondycji, gdy tylko jest to możliwe. Natychmiastowe wysyłanie jest również przydatne, gdy klient sieci szkieletowej jest zamykany (na przykład proces określił nieprawidłowy stan i musi zostać zamknięty, aby zapobiec skutkom ubocznym). Zapewnia najlepsze przesłanie zgromadzonych raportów. Po dodaniu jednego raportu z immediate flagą klient kondycji partii wszystkie skumulowane raporty od ostatniego wysłania.

Te same parametry można określić, gdy połączenie z klastrem jest tworzone za pośrednictwem programu PowerShell. Poniższy przykład rozpoczyna połączenie z klastrem lokalnym:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Podobnie jak w przypadku interfejsu `-Immediate` API, raporty mogą być wysyłane `HealthReportSendInterval` za pomocą przełącznika, które mają być wysyłane natychmiast, niezależnie od wartości.

W przypadku rest raporty są wysyłane do bramy sieci szkieletowej usług, która ma klienta sieci szkieletowej wewnętrznej. Domyślnie ten klient jest skonfigurowany do wysyłania raportów wsadowych co 30 sekund. Interwał wsadowy można zmienić `HttpGatewayHealthReportSendInterval` `HttpGateway`przy włączonym ustawieniu konfiguracji klastra . Jak wspomniano, lepszym rozwiązaniem jest wysyłanie `Immediate` raportów z prawdą. 

> [!NOTE]
> Aby upewnić się, że nieautoryzowane usługi nie mogą zgłaszać kondycji encji w klastrze, skonfiguruj serwer tak, aby akceptował żądania tylko od zabezpieczonych klientów. Używane `FabricClient` do raportowania musi mieć zabezpieczenia włączone, aby móc komunikować się z klastrem (na przykład za pomocą protokołu Kerberos lub uwierzytelniania certyfikatów). Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Raport z usług o niskich uprawnieniach
Jeśli usługi sieci szkieletowej usług nie mają dostępu administratora do klastra, `Partition` `CodePackageActivationContext`można raportować kondycję jednostek z bieżącego kontekstu za pośrednictwem programu .

* W przypadku usług bezstanowych należy użyć [usługi IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) do raportowania bieżącego wystąpienia usługi.
* W przypadku usług stanowych należy użyć [usługi IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) do raportowania bieżącej repliki.
* Użyj [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) do raportowania bieżącej jednostki partycji.
* Użyj [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) do raportowania bieżącej aplikacji.
* Użyj [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) do raportowania bieżącej aplikacji wdrożonej w bieżącym węźle.
* Użyj [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) do raportowania na pakiet usługi dla aplikacji wdrożonych w bieżącym węźle.

> [!NOTE]
> Wewnętrznie `Partition` i `CodePackageActivationContext` przytrzymaj klienta kondycji skonfigurowany z ustawieniami domyślnymi. Jak wyjaśniono dla [klienta zdrowia](service-fabric-report-health.md#health-client), raporty są wsadowe i wysyłane na czasomierzu. Obiekty powinny być utrzymywane przy życiu, aby mieć możliwość wysłania raportu.
> 
> 

Podczas wysyłania raportów `Partition` `CodePackageActivationContext` za pośrednictwem interfejsów API kondycji można określić. `HealthReportSendOptions` Jeśli masz krytyczne raporty, które muszą być `HealthReportSendOptions` wysyłane `true`tak szybko, jak to możliwe, użyj z natychmiastowym . Natychmiastowe raporty pominąć interwał przetwarzania wsadowego klienta kondycji wewnętrznej. Jak wspomniano wcześniej, należy używać tej flagi z ostrożnością; chcemy skorzystać z przetwarzania wsadowego klienta kondycji, gdy tylko jest to możliwe.

## <a name="design-health-reporting"></a>Projektowanie raportów dotyczących kondycji
Pierwszym krokiem w generowaniu raportów wysokiej jakości jest określenie warunków, które mogą mieć wpływ na kondycję usługi. Każdy warunek, który może pomóc flagi problemów w usłudze lub klastra, gdy się uruchamia - lub nawet lepiej, zanim zdarzy się problem - może potencjalnie zaoszczędzić miliardy dolarów. Korzyści obejmują mniej czasu przestoju, mniej godzin nocnych spędzonych na badaniu i naprawianiu problemów oraz większe zadowolenie klientów.

Po zidentyfikowaniu warunków, autorzy watchdog trzeba znaleźć najlepszy sposób, aby monitorować je pod kątem równowagi między obciążeniem i użyteczności. Rozważmy na przykład usługę, która wykonuje złożone obliczenia, które używają niektórych plików tymczasowych w udziale. Watchdog może monitorować udział, aby upewnić się, że jest dostępna wystarczająca ilość miejsca. Może nasłuchiwają powiadomień o zmianach w pliku lub katalogu. Może zgłosić ostrzeżenie, jeśli zostanie osiągnięty próg początkowy, i zgłosić błąd, jeśli udział jest pełny. W przypadku ostrzeżenia system naprawczy może rozpocząć czyszczenie starszych plików w udziale. W razie błędu system naprawy może przenieść replikę usługi do innego węzła. Należy zauważyć, jak stany stanu są opisane w kategoriach kondycji: stan stanu, który można uznać za zdrowy (ok) lub w złej kondycji (ostrzeżenie lub błąd).

Po ustawieniu szczegółów monitorowania, moduł zapisujący watchdog musi dowiedzieć się, jak zaimplementować watchdog. Jeśli warunki można określić z poziomu usługi, watchdog może być częścią monitorowanej usługi. Na przykład kod usługi można sprawdzić użycie udziału, a następnie raport za każdym razem, gdy próbuje napisać plik. Zaletą tego podejścia jest to, że raportowanie jest proste. Należy zwrócić uwagę, aby zapobiec błędom watchdog wpływu na funkcjonalność usługi.

Raportowanie z poziomu monitorowanej usługi nie zawsze jest opcją. Watchdog w ramach usługi może nie być w stanie wykryć warunki. Może nie mieć logiki lub danych do ustalenia. Obciążenie związane z monitorowaniem warunków może być wysokie. Warunki również nie mogą być specyficzne dla usługi, ale zamiast tego wpływają na interakcje między usługami. Inną opcją jest mieć watchdogs w klastrze jako oddzielne procesy. Watchdogs monitorować warunki i raport, bez wpływu na główne usługi w jakikolwiek sposób. Na przykład te watchdogs mogą być implementowane jako usługi bezstanowe w tej samej aplikacji, wdrożony we wszystkich węzłach lub w tych samych węzłach co usługa.

Czasami watchdog uruchomiony w klastrze nie jest opcją albo. Jeśli monitorowany warunek jest dostępność lub funkcjonalność usługi, jak użytkownicy widzą go, najlepiej mieć watchdogs w tym samym miejscu co klienci użytkownika. Tam mogą przetestować operacje w taki sam sposób, w jaki użytkownicy je nazywają. Na przykład można mieć watchdog, który mieszka poza klastrem, problemy z żądaniami do usługi i sprawdza opóźnienie i poprawność wyniku. (Na przykład w przypadku usługi kalkulatora 2+2 zwraca 4 w rozsądnym czasie?)

Po sfinalizowaniu szczegółów watchdog, należy zdecydować się na identyfikator źródła, który jednoznacznie identyfikuje go. Jeśli wiele watchdogs tego samego typu znajdują się w klastrze, muszą raportować na różnych jednostek lub, jeśli raportują na tej samej jednostki, użyj innego identyfikatora źródła lub właściwości. W ten sposób ich raporty mogą współistnieć. Właściwość raportu kondycji należy uchwycić monitorowany warunek. (W powyższym przykładzie właściwością może być **ShareSize**.) Jeśli wiele raportów stosuje się do tego samego warunku, właściwość powinna zawierać pewne dynamiczne informacje, które umożliwiają współistnienie raportów. Jeśli na przykład należy monitorować wiele udziałów, nazwa właściwości może być **nazwą ShareSize-sharename**.

> [!NOTE]
> *Nie* należy używać magazynu kondycji do przechowywania informacji o stanie. Tylko informacje dotyczące zdrowia powinny być zgłaszane jako zdrowie, ponieważ informacje te mają wpływ na ocenę kondycji jednostki. Sklep zdrowia nie został zaprojektowany jako sklep ogólnego przeznaczenia. Używa logiki oceny kondycji do agregowania wszystkich danych do stanu kondycji. Wysyłanie informacji niezwiązanych ze zdrowiem (takich jak stan raportowania o stanie kondycji OK) nie ma wpływu na stan kondycji zagregowane, ale może negatywnie wpłynąć na wydajność magazynu kondycji.
> 
> 

Kolejnym punktem decyzji jest podmiot, który ma podlegać sprawozdaniu. W większości przypadków warunek wyraźnie identyfikuje jednostkę. Wybierz jednostkę z możliwie najlepszą ziarnistością. Jeśli warunek wpływa na wszystkie repliki w partycji, raport na partycji, a nie na usługę. Istnieją przypadki rogu, gdzie więcej myśli jest potrzebne, choć. Jeśli warunek wpływa na jednostkę, takich jak replika, ale pragnienie jest mieć warunek oflagowane na więcej niż czas trwania okresu repliki, a następnie powinny być zgłaszane na partycji. W przeciwnym razie po usunięciu repliki magazyn kondycji czyści wszystkie swoje raporty. Autorzy programu Watchdog muszą myśleć o okresach istnienia jednostki i raporcie. Musi być jasne, kiedy raport powinien zostać wyczyszczony ze sklepu (na przykład, gdy błąd zgłoszony w jednostce nie ma już zastosowania).

Spójrzmy na przykład, który łączy punkty, które opisałem. Należy wziąć pod uwagę aplikację sieci szkieletowej usług składającą się z głównej usługi trwałej stanowej i pomocniczych usług bezstanowych wdrożonych we wszystkich węzłach (jeden pomocniczy typ usługi dla każdego typu zadania). Wzorzec ma kolejkę przetwarzania, która zawiera polecenia do wykonania przez pomocnicze. Pomocnicze żądania wykonać przychodzące żądania i wysłać z powrotem sygnały potwierdzenia. Jednym z warunków, które mogą być monitorowane jest długość kolejki przetwarzania głównego. Jeśli długość kolejki głównej osiągnie próg, zostanie zgłoszone ostrzeżenie. Ostrzeżenie wskazuje, że pomocnicze nie mogą obsłużyć obciążenia. Jeśli kolejka osiągnie maksymalną długość i polecenia zostaną usunięte, zgłaszany jest błąd, ponieważ usługa nie może odzyskać. Raporty mogą znajdować się na właściwości **QueueStatus**. Watchdog mieszka wewnątrz usługi i jest wysyłany okresowo na głównej repliki podstawowej. Czas życia wynosi dwie minuty i jest wysyłany okresowo co 30 sekund. Jeśli podstawowy ustępuje, raport jest automatycznie czyszczony ze sklepu. Jeśli replika usługi jest w górę, ale jest zakleszczony lub ma inne problemy, raport wygasa w magazynie kondycji. W takim przypadku jednostka jest oceniana w przypadku błędu.

Innym warunkiem, który można monitorować, jest czas wykonania zadania. Wzorzec rozdziela zadania do pomocniczych na podstawie typu zadania. W zależności od projektu wzorzec może sondować pomocnicze elementy stanu zadania. Może również czekać na pomocnicze do wysyłania sygnałów potwierdzenia, gdy są one wykonywane. W drugim przypadku należy zwrócić uwagę na wykrycie sytuacji, w których osoby pomocnicze umierają lub wiadomości są tracone. Jedną z opcji jest wysłanie żądania ping do tego samego pomocniczego, który odsyła jego stan. Jeśli nie zostanie odebrany żaden stan, wzorzec uzna go za błąd i zmienia harmonogram zadania. To zachowanie zakłada, że zadania są idempotentne.

Monitorowany stan można przetłumaczyć jako ostrzeżenie, jeśli zadanie nie zostanie wykonane w określonym czasie (**t1**, na przykład 10 minut). Jeśli zadanie nie zostanie ukończone w czasie **(t2**, na przykład 20 minut), monitorowany warunek można przetłumaczyć jako błąd. To raportowanie można wykonać na wiele sposobów:

* Główna replika podstawowa raportuje się okresowo. W kolejce można mieć jedną właściwość dla wszystkich oczekujących zadań. Jeśli co najmniej jedno zadanie trwa dłużej, stan raportu na właściwość **PendingTasks** jest ostrzeżenie lub błąd, odpowiednio. Jeśli nie ma żadnych oczekujących zadań lub wszystkie zadania rozpoczęte wykonywanie, stan raportu jest OK. Zadania są trwałe. Jeśli podstawowy ustępuje, nowo promowane podstawowe można kontynuować raport poprawnie.
* Inny proces watchdog (w chmurze lub zewnętrzne) sprawdza zadania (z zewnątrz, na podstawie pożądanego wyniku zadania), aby sprawdzić, czy są one zakończone. Jeśli nie są one zgodne z progami, raport jest wysyłany w usłudze głównej. Raport jest również wysyłany dla każdego zadania, które zawiera identyfikator zadania, na przykład **PendingTask + taskId**. Raporty powinny być wysyłane tylko w niezdrowych stanach. Ustaw czas na kilka minut i oznacz raporty do usunięcia po ich wygaśnięciu, aby zapewnić oczyszczanie.
* Pomocniczy, który wykonuje raporty zadania, gdy trwa dłużej niż oczekiwano, aby go uruchomić. Raportuje wystąpienie usługi na właściwość **PendingTasks**. Raport wskazuje wystąpienie usługi, które ma problemy, ale nie przechwytuje sytuacji, w której wystąpienie umiera. Raporty są następnie czyszczone. Może on akcesji raport na temat usługi dodatkowej. Jeśli pomocniczy kończy zadanie, wystąpienie pomocnicze czyści raport z magazynu. Raport nie przechwytuje sytuacji, w której komunikat potwierdzenia zostanie utracony, a zadanie nie zostanie zakończone z punktu widzenia wzorca.

Jednak raportowanie odbywa się w przypadkach opisanych powyżej, raporty są przechwytywane w kondycji aplikacji, gdy kondycja jest oceniana.

## <a name="report-periodically-vs-on-transition"></a>Okresowo raportuje a w okresie przejściowym
Za pomocą modelu raportowania kondycji watchdogs można wysyłać raporty okresowo lub na przejściach. Zalecany sposób raportowania watchdog jest okresowo, ponieważ kod jest znacznie prostsze i mniej podatne na błędy. Watchdogs musi dążyć do tak proste, jak to możliwe, aby uniknąć błędów, które wyzwalają niepoprawne raporty. Niepoprawne raporty *w złej kondycji* wpływają na oceny kondycji i scenariusze oparte na kondycji, w tym uaktualnienia. Niepoprawne raporty *dobrej kondycji* ukrywają problemy w klastrze, co nie jest pożądane.

W przypadku raportowania okresowego watchdog można zaimplementować za pomocą czasomierza. W wywołaniu zwrotnym czasomierza watchdog może sprawdzić stan i wysłać raport na podstawie bieżącego stanu. Nie ma potrzeby, aby zobaczyć, który raport został wysłany wcześniej lub dokonać optymalizacji pod względem wiadomości. Klient kondycji ma logikę przetwarzania wsadowego, aby pomóc w wydajności. Gdy klient kondycji jest utrzymywany przy życiu, ponawia wewnętrzne procesy, dopóki raport nie zostanie potwierdzony przez magazyn kondycji lub watchdog wygeneruje nowszy raport z tą samą jednostką, właściwością i źródłem.

Raportowanie przejść wymaga starannej obsługi stanu. Watchdog monitoruje niektóre warunki i raportuje tylko wtedy, gdy warunki się zmieniają. Plusem tego podejścia jest to, że potrzeba mniej sprawozdań. Wadą jest to, że logika watchdog jest złożona. Watchdog musi zachować warunki lub raporty, tak aby można je było sprawdzić w celu określenia zmian stanu. W razie pracy awaryjnej należy zwrócić uwagę na dodane raporty, ale nie zostały jeszcze wysłane do magazynu zdrowia. Numer sekwencyjny musi być stale wzrastający. Jeśli nie, raporty są odrzucane jako przestarzałe. W rzadkich przypadkach, gdy występuje utrata danych, synchronizacja może być konieczna między stanem reportera a stanem magazynu kondycji.

Sprawozdawczość na temat przejść ma sens `Partition` w `CodePackageActivationContext`przypadku sprawozdawczości usług na temat siebie, za pośrednictwem lub . Po usunięciu obiektu lokalnego (repliki lub wdrożonego pakietu usługi / wdrożonej aplikacji) wszystkie jego raporty są również usuwane. To automatyczne oczyszczanie rozluźnia potrzebę synchronizacji między reporterem a magazynem zdrowia. Jeśli raport jest dla partycji nadrzędnej lub aplikacji nadrzędnej, należy zwrócić uwagę na pracy awaryjnej, aby uniknąć starych raportów w magazynie kondycji. Logika musi zostać dodana, aby zachować prawidłowy stan i wyczyścić raport z magazynu, gdy nie jest już potrzebne.

## <a name="implement-health-reporting"></a>Wdrażanie raportowania kondycji
Gdy szczegóły jednostki i raportu są jasne, wysyłanie raportów kondycji można wykonać za pośrednictwem interfejsu API, programu PowerShell lub REST.

### <a name="api"></a>interfejs API
Aby raportować za pośrednictwem interfejsu API, należy utworzyć raport kondycji specyficzne dla typu jednostki, które mają być raportem. Przekaż raport klientowi kondycji. Alternatywnie należy utworzyć informacje o kondycji i `Partition` przekazać `CodePackageActivationContext` je do poprawnych metod raportowania lub do raportu na bieżących jednostek.

W poniższym przykładzie przedstawiono okresowe raportowanie z watchdog w klastrze. Watchdog sprawdza, czy zasób zewnętrzny można uzyskać dostęp z wewnątrz węzła. Zasób jest potrzebny przez manifest usługi w aplikacji. Jeśli zasób jest niedostępny, inne usługi w aplikacji nadal mogą działać poprawnie. W związku z tym raport jest wysyłany na wdrożonej jednostki pakietu usługi co 30 sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Wyślij raporty kondycji za pomocą **send-ServiceFabric*EntityType*HealthReport**.

W poniższym przykładzie przedstawiono okresowe raportowanie wartości procesora CPU w węźle. Raporty powinny być wysyłane co 30 sekund, a mają czas na życie dwóch minut. Jeśli wygasną, reporter ma problemy, więc węzeł jest oceniany pod kątem błędu. Gdy procesor CPU jest powyżej progu, raport ma stan kondycji ostrzeżenia. Gdy procesor CPU pozostaje powyżej progu przez więcej niż skonfigurowany czas, jest zgłaszany jako błąd. W przeciwnym razie reporter wysyła stan kondycji OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Poniższy przykład zgłasza ostrzeżenie przejściowe na replice. Najpierw pobiera identyfikator partycji, a następnie identyfikator repliki dla usługi, która jest zainteresowana. Następnie wysyła raport z **programu PowershellWatcher** na właściwości **ResourceDependency**. Raport jest interesujący tylko przez dwie minuty i jest automatycznie usuwany ze sklepu.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Wysyłaj raporty kondycji przy użyciu REST z żądaniami POST, które przechodzą do żądanej jednostki i mają w treści opis raportu kondycji. Na przykład zobacz, jak wysyłać [raporty kondycji klastra](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) REST lub [raporty kondycji usługi](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Obsługiwane są wszystkie jednostki.

## <a name="next-steps"></a>Następne kroki
Na podstawie danych o kondycji moduły zapisu usług i administratorzy klastra/aplikacji mogą myśleć o sposobach wykorzystania informacji. Na przykład mogą skonfigurować alerty na podstawie stanu zdrowia, aby złapać poważne problemy, zanim wywołują przerwy w dostawie prądu. Administratorzy mogą również skonfigurować systemy naprawcze, aby automatycznie rozwiązywać problemy.

[Wprowadzenie do monitorowania kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

[Wyświetlanie raportów kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnienie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

