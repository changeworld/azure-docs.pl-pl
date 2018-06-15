---
title: Dodawanie niestandardowych raportów kondycji sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Opisuje sposób wysyłania raportów o kondycji niestandardowych do jednostki kondycji sieci szkieletowej usług Azure. Zawiera zalecenia dotyczące projektowania i wdrażania raportów o kondycji jakości.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 3eccb6ba18e6689c3726c8d930279b8a85ab1c92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212531"
---
# <a name="add-custom-service-fabric-health-reports"></a>Dodawanie niestandardowych raportów kondycji usługi Service Fabric
Sieć szkieletowa usług Azure wprowadza [model kondycji](service-fabric-health-introduction.md) umożliwiającą Flaga zła klastra i warunki aplikacji na konkretnych obiektów. Model kondycji używa **Raporty kondycji** (składników systemu i watchdogs). Celem jest łatwe i szybkie diagnozowanie i naprawy. Moduły zapisujące usługi należy wziąć pod uwagę góry o kondycji. Wszelkie warunek, który może mieć wpływ na kondycji należy podać, zwłaszcza, jeśli może pomóc problemów flagi bliski katalogu głównego. Informacje o kondycji można oszczędzić czas i wysiłek na analizie i debugowania. Przydatność jest jasne, szczególnie, gdy usługa jest uruchomiona na dużą skalę w chmurze (prywatny czy Azure).

Monitor Raporty sieci szkieletowej usług określone warunki zainteresowań. Zgłaszają przy użyciu tych warunków na podstawie ich lokalnego widoku. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkie raporty w celu ustalenia, czy jednostki są globalnie dobrej kondycji. Model jest przeznaczony do sformatowanego, elastyczne i łatwe w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Fałszywych alarmów, przedstawiających błędnie zła problemów może niekorzystnie wpłynąć na uaktualnienia lub innych usług używających danych kondycji. Przykładami takich usług są usługi repair i mechanizmów alertów. W związku z tym rozwagą jest potrzebna do zapewnienia raportów, które przechwytywania warunki odsetek w najlepszy możliwy sposób.

Do projektowania i implementacji raportowania kondycji watchdogs i składników systemu musi:

* Zdefiniuj warunek, z którymi są zainteresowani, jak jest monitorowany i wpływ funkcję klastra lub aplikacji. Na podstawie tych informacji, wybierz właściwość raportu kondycji i stan kondycji.
* Określić [jednostki](service-fabric-health-introduction.md#health-entities-and-hierarchy) stosowanym do raportu.
* Określić, gdzie raportowania gotowe, z usługi lub z programu alarmowego wewnętrznych lub zewnętrznych.
* Zdefiniuj źródło używany do identyfikowania osoby zgłaszającej.
* Wybierz strategię raportowania, okresowo lub na przejścia. Zalecaną metodą jest okresowo, ponieważ wymaga prostsze kodu i jest mniej podatne na błędy.
* Określ, jak długo raportu zła warunki powinny pozostać w magazynie kondycji i jak powinny zostać wyczyszczone. Korzystając z tych informacji, decyduje o czas wygaśnięcia raportu i zachowanie Usuń na wygaśnięcia.

Jak wspomniano, raportowanie może odbywać się od:

* Monitorowane repliki usługi sieć szkieletowa usług.
* Wewnętrzny watchdogs wdrożony jako usługa sieci szkieletowej usług (na przykład sieci szkieletowej usług bezstanowych Usługa monitoruje warunki i generuje raporty). Watchdogs mogą być wdrażane wszystkie węzły lub mogą być koligowane z monitorowanej usługi.
* Wewnętrzny watchdogs, uruchom na węzłach sieci szkieletowej usług, które są *nie* zaimplementowane jako usługi sieci szkieletowej usług.
* Watchdogs zewnętrznych, które sondowania zasobu z *poza* klastra sieci szkieletowej usług (na przykład monitorowania usługi, takiej jak Gomez).

> [!NOTE]
> Fabrycznej klaster został wypełniony raportów o kondycji wysyłane przez składniki systemu. Dowiedz się więcej o [przy użyciu kondycji systemu raportów do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Raporty użytkownika musi zostać wysłany [jednostki kondycji](service-fabric-health-introduction.md#health-entities-and-hierarchy) które już zostały utworzone przez system.
> 
> 

Raz kondycji raportowania, że projekt jest wyczyszczone, raportów o kondycji mogą być wysyłane łatwe. Można użyć [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) do raportu kondycji, jeśli klaster nie jest [bezpiecznego](service-fabric-cluster-security.md) lub jeśli klienta programu fabric ma uprawnienia administratora. Raportowanie może odbywać się za pośrednictwem interfejsu API przez przy użyciu [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), za pomocą programu PowerShell lub za pośrednictwem REST. Konfiguracja pokrętła partii raportów, aby uzyskać lepszą wydajność.

> [!NOTE]
> Raport kondycji jest synchroniczne, i reprezentuje tylko pracy weryfikacji po stronie klienta. Fakt, że raport jest akceptowane przez klienta kondycji lub `Partition` lub `CodePackageActivationContext` obiektów nie oznacza, że jest ono stosowane w magazynie. Jest wysyłane asynchronicznie, być może umieścić w zadaniu wsadowym razem z innymi raportami. Przetwarzanie na serwerze nadal może zakończyć się niepowodzeniem: numer sekwencji może być stałe, jednostki, na którym należy zastosować raport został usunięty, itp.
> 
> 

## <a name="health-client"></a>Kondycji klienta
Raporty kondycji są wysyłane do magazynu kondycji za pomocą klienta kondycji, które znajdują się wewnątrz klienta sieci szkieletowej. Kondycji klienta można skonfigurować następujące ustawienia:

* **HealthReportSendInterval**: opóźnienie między czas raportu jest dodawany do klienta i są wysyłane do magazynu kondycji. Używane do raportów usługi partia zadań w pojedynczym komunikacie zamiast wysyłania jeden komunikat dla każdego raportu. Przetwarzanie wsadowe poprawia wydajność. Wartość domyślna: 30 sekund.
* **HealthReportRetrySendInterval**: interwał, jaką kondycji klienta wysyła ponownie kondycji wszystkich raportów w magazynie kondycji. Wartość domyślna: 30 sekund.
* **HealthOperationTimeout**: limit czasu dla raportu wiadomość wysłana do magazynu kondycji. Jeśli upłynie limit czasu wiadomości, kondycji klient ponawia próbę go do momentu magazynu kondycji potwierdza, że raport został przetworzony. Wartość domyślna: dwie minuty.

> [!NOTE]
> Jeśli raporty są przetwarzane wsadowo, klient sieci szkieletowej musi utrzymywać ich aktywność dla co najmniej HealthReportSendInterval, aby upewnić się, że są one wysyłane. Jeśli komunikat zostanie utracony lub magazynu kondycji nie może zastosować je z powodu błędów przejściowych, klienta programu fabric musi być aktywne już aby nadać mu w taki sposób, aby ponowić próbę.
> 
> 

Buforowanie na kliencie przyjmuje unikatowości raporty pod uwagę. Na przykład określonego zły osoby zgłaszającej zgłoszenie 100 raportów na sekundę na tę samą właściwość o tej samej jednostki, raporty są zastępowane ostatniej wersji. Istnieje co najwyżej jeden taki raport w kolejce klienta. Jeśli skonfigurowano przetwarzanie wsadowe liczbę raportów wysyłane do magazynu kondycji jest tylko jeden interwał wysyłania. Ten raport jest ostatni raport dodany odzwierciedla najbardziej bieżącego stanu jednostki.
Określ parametry konfiguracji po `FabricClient` jest tworzony przez przekazanie [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) z odpowiednie wartości dla wpisy dotyczące kondycji.

Poniższy przykład tworzy klienta sieci szkieletowej i określa, czy raporty mają być wysyłane w przypadku, gdy są one dodawane. Limity czasu i błędy, które można ponowić ponownych prób nastąpić co 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Zaleca się pozostawienie ustawienia, które Ustaw domyślnego klienta sieci szkieletowej `HealthReportSendInterval` 30 sekund. To ustawienie zapewnia optymalną wydajność ze względu na przetwarzanie wsadowe. Krytyczne raportów, które muszą zostać przesłane tak szybko, jak to możliwe, należy użyć `HealthReportSendOptions` z Immediate `true` w [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) interfejsu API. Raporty natychmiastowego obejścia interwał przetwarzanie wsadowe. Użyj tej flagi ostrożność; chcemy skorzystać klient kondycji przetwarzanie wsadowe, jeśli to możliwe. Wyślij natychmiastowego jest również przydatny podczas zamykania klienta sieci szkieletowej (na przykład proces wykrył nieprawidłowy stan i konieczność jej zamknięcia zapobiegające efekty uboczne). Zapewnia send optymalnych wszystkich raportów. Po dodaniu jeden raport z flagą natychmiastowego kondycji klienta partii skumulowany raporty od czasu ostatniego wysłania.

Takie same parametry można określić podczas tworzenia połączenia z klastrem za pomocą programu PowerShell. Poniższy przykład uruchamia połączenie z lokalnym klastrem:

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

Podobnie do interfejsu API, raporty można wysyłać przy użyciu `-Immediate` przełącznika wysłanych od razu, niezależnie od `HealthReportSendInterval` wartość.

Dla pozostałych raporty są wysyłane do bramy sieci szkieletowej usług klienta wewnętrznej sieci szkieletowej. Domyślnie ten klient jest skonfigurowany do wysyłania raportów umieścić w zadaniu wsadowym co 30 sekund. Ustawienia konfiguracji klastra można zmienić interwał partii `HttpGatewayHealthReportSendInterval` na `HttpGateway`. Jak wspomniano, lepszym rozwiązaniem jest na wysyłanie raportów o `Immediate` wartość true. 

> [!NOTE]
> Aby upewnić się, że nieautoryzowanego usługi nie można utworzyć raportu kondycji dla obiektów w klastrze, należy skonfigurować serwer do akceptowania żądań tylko od klientów zabezpieczonych. `FabricClient` Używane na potrzeby raportowania musi mieć z włączonymi zabezpieczeniami mógł komunikować się z klastrem (na przykład z uwierzytelnianiem Kerberos lub certyfikatów). Przeczytaj więcej na temat [klastra zabezpieczeń](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Raport z niskim poziomem uprawnień Services
Jeśli nie ma dostępu administratora do klastra usługi sieć szkieletowa usług, mogą raportować kondycję na jednostkach z bieżącego kontekstu za pomocą `Partition` lub `CodePackageActivationContext`.

* W przypadku usług bezstanowych użyj [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) raport ma dotyczyć bieżącego wystąpienia usługi.
* Usługi stanowej, użyj [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) raport ma dotyczyć bieżącej repliki.
* Użyj [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) raport ma dotyczyć bieżącego obiektu partycji.
* Użyj [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) raport ma dotyczyć bieżącej aplikacji.
* Użyj [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) raport ma dotyczyć bieżącej aplikacji wdrożonych w bieżącym węźle.
* Użyj [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) raport ma dotyczyć pakietu usług dla aplikacji wdrożonych w bieżącym węźle.

> [!NOTE]
> Wewnętrznie `Partition` i `CodePackageActivationContext` przechowywania kondycji klient skonfigurowany przy użyciu ustawień domyślnych. Zgodnie z objaśnieniem dla [kondycji klienta](service-fabric-report-health.md#health-client), raporty są umieszczane w partii i wysyłane przez czasomierz. Obiekty powinny życiu mieć możliwość wysyłania raportu.
> 
> 

Można określić `HealthReportSendOptions` podczas wysyłania raportów za pośrednictwem `Partition` i `CodePackageActivationContext` kondycji interfejsów API. Jeśli masz krytyczne raportów, które muszą zostać przesłane tak szybko, jak to możliwe, użyj `HealthReportSendOptions` z Immediate `true`. Raporty natychmiastowego obejścia przetwarzanie wsadowe Interwał wewnętrznego kondycji klienta. Jak wspomniano wcześniej, użyj tej flagi ostrożność; chcemy skorzystać klient kondycji przetwarzanie wsadowe, jeśli to możliwe.

## <a name="design-health-reporting"></a>Projektowanie raportowania kondycji
Pierwszym krokiem podczas generowania raportów wysokiej jakości identyfikuje warunki, które mogą mieć wpływ na kondycję usługi. Wszelkie warunek, który może pomóc flagi problemów w usłudze lub klastrze podczas jej uruchamiania--lub lepszy, zanim się stanie, problem — może potencjalnie zapisać miliardów dolarów. Zalety mniej czas przestoju, mniejszą liczbę godzin nocy poświęcony na wykrywanie i naprawianie problemów i wyższe zadowolenia klienta.

Po zidentyfikowaniu warunki autorzy programu alarmowego muszą ustalić przyczynę najlepszy sposób, aby monitorować ich równowagi między koszty i użyteczność. Rozważmy na przykład usługi, który wykonuje obliczenia złożonych, które używają niektóre pliki tymczasowe w udziale. Programu alarmowego można monitorować udziału, aby upewnić się, że jest dostępna wystarczająca ilość miejsca. Można go nasłuchiwania powiadomień o zmianach pliku lub katalogu. Można go raportować ostrzeżenie, jeśli próg góry, a zgłaszać błąd, gdy udział jest pełna. Na ostrzeżenie, można uruchomić system naprawy, czyszczenie starszych plików w udziale. W przypadku wystąpienia błędu systemu naprawy można przenieść repliki usługi do innego węzła. Należy pamiętać, opisu stanów warunku pod względem kondycji: stan warunek, który jest uznawana za dobrej kondycji (ok) lub złej kondycji (ostrzeżenia lub błędu).

Po ustawieniu monitorowania szczegółów, Edytor programu alarmowego należy dowiedzieć się, jak do zaimplementowania programu alarmowego. Jeśli warunki można ustalić na podstawie w ramach usługi, programu alarmowego może być częścią samej usługi monitorowane. Na przykład kodu usługi można Sprawdź użycie udziału, a następnie składają raporty za każdym razem, gdy próbuje zapisać plik. Zaletą tej metody jest raportowania proste. Należy uważać, aby zapobiec usterki programu alarmowego wpływu na funkcjonalność usługi.

Raportowanie w programie monitorowanej usługi nie zawsze jest opcją. Nie można wykryć warunki strażnika w ramach usługi. Może nie dysponować logiki lub określenie danych. Obciążenie monitorowania warunków może być wysokie. Warunki również nie mogą być specyficzne dla usługi, ale zamiast tego wpływa na interakcje między usługami. Inną opcją jest watchdogs w klastrze jako osobne procesy. Watchdogs monitorować warunki i raportów, bez wywierania wpływu na usług w dowolny sposób. Na przykład watchdogs te można można zaimplementować jako usługi bezstanowej w tej samej aplikacji wdrożone na wszystkich węzłach lub tych samych węzłów jako usługa.

Czasami programu alarmowego uruchamianych w klastrze nie ma możliwości użycia albo. Jeśli stan monitorowanych się dostępności lub funkcji usługi użytkownicy widzą go, jest najlepszym rozwiązaniem jest watchdogs w tym samym miejscu jako klienci użytkownika. One testowania czynności w taki sam sposób jak użytkownicy połączeń telefonicznych z nimi. Na przykład można mieć programu alarmowego, znajduje się poza klastrem, wysyła żądania do usługi i sprawdza opóźnienia i poprawność wyniku. (Dla usługi Kalkulator, na przykład 2 + 2 zwróci 4 w rozsądnym czasie?)

Po sfinalizowany szczegóły dotyczące programu alarmowego, należy podjąć decyzję dotyczącą na identyfikator źródła, który unikatowo identyfikuje go. Jeśli wiele watchdogs tego samego typu mieszkają w klastrze, muszą one zgłosić na różnych obiektów, lub jeśli zgłaszają na tej samej jednostki, użyj Identyfikatora innego źródła lub właściwości. W ten sposób ich raporty mogą współistnieć. Właściwość raport o kondycji należy przechwytywać monitorowanych warunku. (Na przykład powyżej, właściwość może być **ShareSize**.) Jeśli wiele raportów dotyczą tego samego warunku, właściwość powinien zawierać niektóre informacje dynamicznych, która umożliwia współistnienie raportów. Na przykład, jeśli wiele udziałów muszą być monitorowane, nazwa właściwości może być **ShareSize sharename**.

> [!NOTE]
> Czy *nie* korzystać z magazynu kondycji, aby zachować informacje o stanie. Należy podać tylko informacje dotyczące kondycji jako kondycji, jak te informacje ma wpływ na ocenę kondycji jednostki. Magazynu kondycji nie został zaprojektowany jako magazynu ogólnego przeznaczenia. Używa logiki oceny kondycji agregacji wszystkie dane do stanu kondycji. Wysyłanie informacji o kondycji (takich jak raportowanie stanu ze stanem kondycji OK) niezwiązanych ze sobą bez wpływu na stan kondycji zagregowane, ale może negatywnie wpłynąć na wydajność magazynu kondycji.
> 
> 

Następny punkt decyzji znajduje się na jakiej encji do raportu. Większość czasu warunek wyraźnie idetifies jednostki. Wybierz obiekt z najlepsze możliwe stopnia szczegółowości. Jeśli warunek ma wpływ na wszystkie repliki na partycji, zgłoś na partycji, nie w usłudze. Brak sytuacjach wyjątkowych, gdy potrzebne jest więcej myśl, mimo że. Jeśli warunek ma wpływ na jednostkę, takich jak repliki, ale desire ma mieć warunek oflagowane więcej niż czas trwania życia repliki, a następnie należy podać na partycji. W przeciwnym razie po usunięciu repliki magazynu kondycji czyści jego raporty. Autorzy programu alarmowego muszą myśleć o okresy istnienia jednostki i raportu. Musi być jasne podczas raportu należy oczyścić z magazynu (na przykład błędu zgłoszonego z jednostką już ma zastosowanie).

Oto przykład umieszcza razem punktów, które I opisem. Należy rozważyć aplikacji usługi sieć szkieletowa składa się z głównym usługi stanowej trwałe i dodatkowej usługi bezstanowej wdrożone na wszystkich węzłach (jeden typ dodatkowej usługi dla poszczególnych typów zadań). Wzorzec ma kolejki przetwarzania, która zawiera polecenia ma być wykonane przez pomocnicze bazy danych. Pomocnicze bazy danych wykonaj żądań przychodzących i wysyłanie sygnałów wstecz potwierdzenia. Jeden warunek, który może być monitorowany jest długość kolejki przetwarzania wzorca. Jeśli długość kolejki głównej osiągnie próg, ostrzeżenie jest raportowana. Ostrzeżenie wskazuje, że pomocnicze bazy danych nie może obsłużyć obciążenia. Jeśli kolejka osiągnie maksymalną długość polecenia są usuwane, jest zgłaszany błąd, ponieważ usługa nie można odzyskać. Raporty mogą być we właściwości **QueueStatus**. Programu alarmowego znajdują się wewnątrz usługi, a jest okresowo przesyłane na głównym repliki podstawowej. Czas wygaśnięcia wynosi dwie minuty, a wysłaniem okresowo co 30 sekund. Jeśli podstawowy ulegnie awarii, raportu jest automatycznie czyszczone z magazynu. Jeśli replika usługa działa, ale jest on zakleszczone lub inne problemy raportu wygaśnie w magazynie kondycji. W takim przypadku jednostki jest oceniane w błąd.

Inny warunek, który może być monitorowany jest czas wykonania zadania. Wzorzec dystrybuuje zadania pomocnicze bazy danych na podstawie typu zadań. W zależności od projektu wzorzec można sondować pomocnicze bazy danych dla stanu zadania. Można również poczekać, aż pomocnicze bazy danych do wysyłania potwierdzeń wstecz sygnałów, gdy są one wykonywane. W drugim przypadku należy uważać, aby wykryć sytuacje, w którym struktury pomocnicze bazy danych lub komunikaty zostaną utracone. Jedną z opcji jest wzorca wysłać żądanie ping do tej samej dodatkowej, której odsyła jego stan. Jeśli brak stan nie zostanie odebrana, wzorzec uzna awarii i zmienia harmonogram zadania. To zachowanie przyjęto założenie, że zadania są idempotentności.

Stan monitorowanych można przetłumaczyć jako ostrzeżenie, jeśli zadanie nie zostanie zrobione w określonym czasie (**t1**, na przykład 10 minut). Jeśli zadanie nie zostało ukończone w czasie (**t2**, na przykład 20 minut), monitorowanych warunku można przetłumaczyć jako błąd. Ta raportowania może odbywać się na wiele sposobów:

* Główny repliki podstawowej raporty okresowo od siebie samego. Może mieć jedną właściwość dla wszystkich zadań oczekujących w kolejce. Jeśli co najmniej jedno zadanie trwa dłużej, stan raportu we właściwości **PendingTasks** jest ostrzeżenia lub błędu, zależnie od potrzeb. Jeśli nie ma żadnych oczekujących zadań lub wszystkich zadań rozpoczął wykonywanie sekwencji, stan raportu jest OK. Zadania są trwałe. Podstawowy ulegnie awarii, mogą nadal raport prawidłowo nowo utworzonego podstawowej.
* Inny proces programu alarmowego (w chmurze lub zewnętrznego) sprawdza zadań (z zewnątrz, na podstawie wyniku żądaną zadania) aby zobaczyć, czy zostały zakończone. Jeśli nie przestrzegania progów, raport jest wysyłany na głównym usługi. Raport zostanie także wysłana na każdego zadania, które obejmuje identyfikatora zadania, takie jak **PendingTask + taskId**. Raporty mają być wysyłane tylko w stan złej kondycji. Ustaw czas na żywo na kilka minut i oznacz raportów została usunięta po wygasną zapewnienie oczyszczania.
* Pomocniczy, który wykonuje zadania raportów, gdy trwa dłużej niż oczekiwano go uruchomić. Raporty dla wystąpienia usługi o właściwości **PendingTasks**. Raport określa wystąpienie usługi, które występują problemy, ale nie przechwytuje sytuacji, gdy wystąpienie zaniku. Raporty są następnie wyczyścić. Można go raport dotyczący dodatkowej usługi. Pomocniczy wykona zadania, dodatkowej wystąpienia czyści raportu z magazynu. Raport nie przechwytuje sytuacji, gdy komunikat potwierdzenia zostaną utracone i zadanie nie zostało zakończone z punktu widzenia głównego.

Jednak raportowania odbywa się w przypadkach opisanych powyżej, raporty są przechwytywane kondycji aplikacji podczas oceny kondycji.

## <a name="report-periodically-vs-on-transition"></a>Raport okresowo a na przejście
Przy użyciu modelu raportowania kondycji watchdogs raporty można wysyłać okresowo lub przejścia. Zalecaną metodą raportowania programu alarmowego jest okresowo, ponieważ kod jest znacznie prostsza i mniej podatna na błędy. Watchdogs starań musi być tak proste, jak to możliwe uniknąć błędów, które mogą powodować nieprawidłowe raporty. Niepoprawna *zła* wpływ na raporty oceny kondycji i scenariusze, w oparciu o kondycji, w tym uaktualnienia. Niepoprawna *dobrej kondycji* raporty ukrycie problemów w klastrze, który nie jest wymagana.

Okresowe raportowania programu alarmowego może być zaimplementowany z czasomierzem. Wywołanie zwrotne czasomierza programu alarmowego można sprawdzić stan i wysłać raport na podstawie bieżącego stanu. Nie istnieje potrzeba Zobacz, który raport został wysłany wcześniej, czy wydawać żadnych optymalizacji pod względem obsługi wiadomości. Kondycji klienta jest przetwarzanie wsadowe logiki, aby pomóc w wydajności. Gdy klient kondycji jest aktywne, ponowną wewnętrznie aż do raportu zostało potwierdzone przez magazynu kondycji lub programu alarmowego generuje nowszej raport o tej samej jednostki, właściwości i źródła.

Raporty dotyczące przejścia wymaga obsługi dokładne stanu. Programu alarmowego monitoruje niektóre warunki i raporty, tylko po zmianie warunków. Odwróć tego podejścia jest potrzebne są mniej raportów. Wadą interfejsu jest złożona logika programu alarmowego. Programu alarmowego muszą zachować warunków lub raporty, dzięki czemu mogą być kontrolowane ustalenie zmiany stanu. W tryb failover należy uważać z raportami dodane, ale jeszcze nie są wysyłane do magazynu kondycji. Numer sekwencyjny musi być coraz większe. Jeśli nie, raporty są odrzucane jako przestarzały. W rzadkich przypadkach, gdy poniesienia utraty danych synchronizacja może być wymagana między stan osoby zgłaszającej i stanu magazynu kondycji.

Raporty dotyczące przejścia sens dla usług raportowania, za pomocą `Partition` lub `CodePackageActivationContext`. Jeśli lokalny obiekt (repliki lub wdrożony pakiet usługi / wdrożonych aplikacji) jest usunięty, jego raporty zostaną również usunięte. To automatyczne oczyszczanie zwalnia potrzebę synchronizacja osoby zgłaszającej i magazynu kondycji. Jeśli raport dotyczy partycji nadrzędnej lub aplikacji nadrzędnej, szczególną uwagę musi być przełączona w tryb failover w celu uniknięcia starych raportów w magazynie kondycji. Logika musi zostać dodany do utrzymania poprawnego stanu i wyczyść raportu z magazynu, jeśli nie jest już potrzebne.

## <a name="implement-health-reporting"></a>Implementowanie raportowania kondycji
Po zatwierdzeniu treść jednostki i raportu Wyczyść, wysyłania raportów o kondycji jest możliwe za pośrednictwem interfejsu API środowiska PowerShell i REST.

### <a name="api"></a>Interfejs API
Aby zgłosić za pośrednictwem interfejsu API, musisz utworzyć specyficzne dla typu jednostki, interesujące raport ma dotyczyć raport o kondycji. Nadaj raportu do kondycji klienta. Można również utworzyć informacje o kondycji i przekaż go do Popraw metod raportowania na `Partition` lub `CodePackageActivationContext` raport ma dotyczyć bieżącego jednostek.

W poniższym przykładzie przedstawiono okresowych raportów z strażnika w klastrze. Programu alarmowego sprawdza, czy zasób zewnętrzny można uzyskać z w węźle. Zasób jest wymagane przez manifest usługi aplikacji. Jeśli zasób jest niedostępny, innych usług w aplikacji mogą nadal działać prawidłowo. W związku z tym raport jest wysyłany w jednostce pakietu wdrożonej usługi co 30 sekund.

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
Wysyłanie raportów o kondycji z **ServiceFabric wysyłania*EntityType*HealthReport**.

W poniższym przykładzie przedstawiono okresowych raportów na wartościach Procesora w węźle. Raporty mają być wysyłane co 30 sekund i mają czas wygaśnięcia wynosi dwie minuty. Jeśli wygasną, osoby zgłaszającej występują problemy, więc węzeł jest oceniane w błąd. Gdy Procesora jest powyżej wartości progowej, raport ma stan kondycji ostrzeżenia. Jeśli Procesor pozostaje powyżej progu przez ponad skonfigurowanym czasie, będzie zgłaszane jako błąd. W przeciwnym razie reportera, który wysyła kondycję OK.

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

Poniższy przykład zgłosi ostrzeżenie przejściowy na replice. Identyfikator partycji, a następnie identyfikator repliki najpierw pobiera dla usługi, który jest zainteresowana. Następnie wysyła raport z **PowershellWatcher** we właściwości **ResourceDependency**. Raport ma znaczenie tylko dwóch minut, a zostanie automatycznie usunięte z magazynu.

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
Wysyłanie raportów o kondycji za pomocą usługi REST z żądania POST, przejdź do żądanego jednostki, które w treści opis raportu kondycji. Zobacz na przykład, jak wysyłać REST [klastra raportów o kondycji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) lub [raportów o kondycji usługi](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Obsługiwane są wszystkie jednostki.

## <a name="next-steps"></a>Kolejne kroki
Na podstawie kondycji danych, moduły zapisujące usługi i Administratorzy klastra/aplikacji można traktować sposobów, aby korzystać z informacji. Na przykład one można skonfigurować alerty na podstawie stanu kondycji do wychwytywania poważne problemy, zanim one powodować awarie. Administratorzy mogą również skonfigurować systemy naprawy Aby automatycznie rozwiązać problemy.

[Wprowadzenie do kondycji sieci szkieletowej usług monitorowania](service-fabric-health-introduction.md)

[Wyświetl raporty dotyczące kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

[Jak zgłosić i Sprawdź kondycję usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Użyj systemowych raportów kondycji do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorowanie i diagnozowania usług lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

