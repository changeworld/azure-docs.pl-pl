---
title: Usługi Azure Analysis Services — skalowanie w poziomie| Dokumenty firmy Microsoft
description: Replikuj serwery usług Azure Analysis Services ze skalowaniem w poziomie. Kwerendy klienta mogą być następnie dystrybuowane między replikami wielu zapytań w puli zapytań skalowalnym w poziomie.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247989"
---
# <a name="azure-analysis-services-scale-out"></a>Skalowanie w poziomie usług Azure Analysis Services

Dzięki skalowaniu w poziomie kwerendy klienta mogą być dystrybuowane między *replikami* wielu zapytań w *puli zapytań,* skracając czas odpowiedzi podczas dużych obciążeń zapytań. Można również oddzielić przetwarzanie od puli zapytań, zapewniając, że operacje przetwarzania nie mają negatywnego wpływu na zapytania klientów. Skalowanie w poziomie można skonfigurować w witrynie Azure portal lub przy użyciu interfejsu API REST usług Analysis Services.

Skalowanie w poziomie jest dostępne dla serwerów w warstwie cenowej Standardowa. Każda replika kwerendy jest rozliczana z taką samą szybkością jak serwer. Wszystkie repliki zapytań są tworzone w tym samym regionie co serwer. Liczba replik zapytań, które można skonfigurować, jest ograniczona przez region, w którego znajduje się serwer. Aby dowiedzieć się więcej, zobacz [Dostępność według regionu](analysis-services-overview.md#availability-by-region). Skalowanie w poziomie nie zwiększa ilości dostępnej pamięci dla serwera. Aby zwiększyć ilość pamięci, należy uaktualnić plan. 

## <a name="why-scale-out"></a>Po co skalować w poziomie?

W typowym wdrożeniu serwera jeden serwer służy zarówno jako serwer przetwarzania, jak i serwer zapytań. Jeśli liczba zapytań klientów dotyczących modeli na serwerze przekracza jednostki przetwarzania zapytań (QPU) dla planu serwera lub przetwarzanie modelu występuje w tym samym czasie, co wysokie obciążenia zapytań, wydajność może się zmniejszyć. 

Za pomocą skalowania w poziomie można utworzyć pulę zapytań z maksymalnie siedmioma dodatkowymi zasobami repliki zapytań (łącznie osiem, w tym serwer *podstawowy).* Można skalować liczbę replik w puli zapytań, aby spełnić wymagania QPU w krytycznych momentach i można oddzielić serwer przetwarzania od puli zapytań w dowolnym momencie. 

Niezależnie od liczby replik zapytań w puli zapytań obciążenia przetwarzania nie są rozdzielane między repliki zapytań. Serwer podstawowy służy jako serwer przetwarzania. Repliki kwerend obsługują tylko zapytania względem baz danych modelu zsynchronizowanych między serwerem podstawowym a każdą repliką w puli zapytań. 

Podczas skalowania w poziomie, może upłynąć do pięciu minut dla replik nowych kwerend, które mają być stopniowo dodawane do puli zapytań. Gdy wszystkie nowe repliki zapytań są uruchomione, nowe połączenia klientów są równoważenia obciążenia między zasobami w puli zapytań. Istniejące połączenia klientów nie są zmieniane z zasobu, z który są obecnie połączone. Podczas skalowania w, wszystkie istniejące połączenia klienta do zasobu puli kwerend, który jest usuwany z puli zapytań są przerywane. Klienci mogą ponownie połączyć się z pozostałym zasobem puli zapytań.

## <a name="how-it-works"></a>Jak to działa

Podczas konfigurowania skalowania w poziomie po raz pierwszy, bazy danych modelu na serwerze podstawowym są *automatycznie* synchronizowane z nowymi replikami w nowej puli zapytań. Automatyczna synchronizacja odbywa się tylko raz. Podczas automatycznej synchronizacji pliki danych serwera podstawowego (zaszyfrowane w magazynie obiektów blob) są kopiowane do drugiej lokalizacji, również szyfrowane w magazynie obiektów blob. Repliki w puli zapytań są następnie *nawodnione* danymi z drugiego zestawu plików. 

Podczas automatycznej synchronizacji jest wykonywana tylko podczas skalowania w poziomie serwera po raz pierwszy, można również wykonać synchronizację ręczną. Synchronizacja zapewnia dane dotyczące replik w puli zapytań zgodne z pulą zapytań serwera podstawowego. Podczas przetwarzania (odświeżania) modeli na serwerze podstawowym należy przeprowadzić synchronizację *po* zakończeniu operacji przetwarzania. Ta synchronizacja kopiuje zaktualizowane dane z plików serwera podstawowego w magazynie obiektów blob do drugiego zestawu plików. Repliki w puli zapytań są następnie nawodnione zaktualizowanymi danymi z drugiego zestawu plików w magazynie obiektów blob. 

Podczas wykonywania kolejnej operacji skalowania w poziomie, na przykład zwiększenie liczby replik w puli zapytań z dwóch do pięciu, nowe repliki są nawodnione danymi z drugiego zestawu plików w magazynie obiektów blob. Nie ma synchronizacji. Jeśli następnie wykonać synchronizację po skalowaniu w poziomie, nowe repliki w puli kwerend będzie nawodniony dwa razy - nadmiarowe nawodnienie. Podczas wykonywania kolejnej operacji skalowania w poziomie, należy pamiętać:

* Wykonaj synchronizację *przed operacją skalowania w poziomie,* aby uniknąć nadmiarowego nawodnienia dodanych replik. Równoczesne synchronizacji i skalowania w poziomie operacji uruchomionych w tym samym czasie nie są dozwolone.

* Podczas automatyzacji operacji przetwarzania *i* skalowania w poziomie, ważne jest, aby najpierw przetwarzać dane na serwerze podstawowym, a następnie wykonać synchronizację, a następnie wykonać operację skalowania w poziomie. Ta sekwencja zapewnia minimalny wpływ na QPU i zasoby pamięci.

* Synchronizacja jest dozwolona nawet wtedy, gdy w puli kwerend nie ma żadnych replik. Jeśli skalowanie w poziomie od zera do jednej lub więcej replik z nowymi danymi z operacji przetwarzania na serwerze podstawowym, należy najpierw wykonać synchronizację bez replik w puli zapytań, a następnie skalować w poziomie. Synchronizacja przed skalowaniem w poziomie pozwala uniknąć nadmiarowego nawodnienia nowo dodanych replik.

* Podczas usuwania bazy danych modelu z serwera podstawowego, nie jest automatycznie usuwany z replik w puli zapytań. Operację synchronizacji należy wykonać za pomocą polecenia [Sync-AzAnalysisInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell, które usuwa pliki dla tej bazy danych z lokalizacji magazynu udostępnionego obiektu blob repliki, a następnie usuwa bazę danych modelu w replikach w puli zapytań. Aby ustalić, czy baza danych modelu istnieje w replikach w puli kwerend, ale nie na serwerze podstawowym, upewnij się, że ustawienie **Oddziel serwer przetwarzania od puli kwerend** jest **tak**. Następnie użyj SSMS, aby połączyć `:rw` się z serwerem podstawowym przy użyciu kwalifikatora, aby sprawdzić, czy baza danych istnieje. Następnie połącz się z replikami w `:rw` puli zapytań, łącząc się bez kwalifikatora, aby sprawdzić, czy ta sama baza danych również istnieje. Jeśli baza danych istnieje w replikach w puli zapytań, ale nie na serwerze podstawowym, uruchom operację synchronizacji.   

* Podczas zmiany nazwy bazy danych na serwerze podstawowym, istnieje dodatkowy krok, aby upewnić się, że baza danych jest poprawnie zsynchronizowany do wszystkich replik. Po zmianie nazwy należy przeprowadzić synchronizację za pomocą polecenia [Sync-AzAnalysisServiceInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) określającego `-Database` parametr o starej nazwie bazy danych. Ta synchronizacja usuwa bazę danych i pliki o starej nazwie z wszystkich replik. Następnie wykonaj kolejną synchronizację określającą `-Database` parametr o nowej nazwie bazy danych. Druga synchronizacja kopiuje nowo nazwaną bazę danych do drugiego zestawu plików i nawilża wszystkie repliki. Synchronizacji tych nie można wykonać za pomocą polecenia Synchronizuj model w portalu.

### <a name="synchronization-mode"></a>Tryb synchronizacji

Domyślnie repliki kwerend są ponownie nawodnione w całości, a nie przyrostowo. Nawodnienie odbywa się etapami. Są one odłączone i dołączone dwa naraz (przy założeniu, że istnieją co najmniej trzy repliki), aby upewnić się, że co najmniej jedna replika jest przechowywana w trybie online dla zapytań w danym momencie. W niektórych przypadkach klienci mogą być konieczne ponowne połączenie z jedną z replik online, podczas gdy ten proces ma miejsce. Za pomocą (w wersji zapoznawczej) **ReplicaSyncMode** ustawienie, można teraz określić synchronizacji repliki kwerendy odbywa się równolegle. Synchronizacja równoległa zapewnia następujące korzyści: 

- Znaczne skrócenie czasu synchronizacji. 
- Dane w replikach są bardziej prawdopodobne, aby być spójne podczas procesu synchronizacji. 
- Ponieważ bazy danych są przechowywane w trybie online na wszystkich replikach w całym procesie synchronizacji, klienci nie muszą ponownie łączyć. 
- Pamięć podręczna w pamięci jest aktualizowana przyrostowo tylko ze zmienionymi danymi, które mogą być szybsze niż pełne ponowne nawodnienie modelu. 

#### <a name="setting-replicasyncmode"></a>Ustawianie trybu replicasyncmode

Użyj SSMS, aby ustawić replicasyncMode we właściwościach zaawansowanych. Możliwe wartości są następujące: 

- `1`(domyślnie): Pełne nawodnienie bazy danych replik w etapach (przyrostowe). 
- `2`: Zoptymalizowana synchronizacja równolegle. 

![RelicaSyncMode ustawienie](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Podczas **ustawiania ReplicaSyncMode=2**, w zależności od tego, jaka część pamięci podręcznej musi zostać zaktualizowana, repliki kwerend mogą zużywać dodatkową pamięć. Aby baza danych była dostępna w trybie online i dostępne dla kwerend, w zależności od tego, jaka ilość danych została zmieniona, operacja może wymagać *do podwojenia pamięci* w replice, ponieważ zarówno stare, jak i nowe segmenty są przechowywane w pamięci jednocześnie. Węzły repliki mają taką samą alokację pamięci jak węzeł podstawowy i zwykle istnieje dodatkowa pamięć w węźle podstawowym dla operacji odświeżania, więc może być mało prawdopodobne, że repliki zabraknie pamięci. Ponadto typowy scenariusz jest, że baza danych jest stopniowo aktualizowana w węźle podstawowym i dlatego wymaganie podwójnej pamięci powinno być rzadkością. Jeśli operacja synchronizacji napotka błąd braku pamięci, ponowi próbę przy użyciu domyślnej techniki (dołącz/odłącz dwa naraz). 

### <a name="separate-processing-from-query-pool"></a>Oddzielenie przetwarzania od puli zapytań

Aby uzyskać maksymalną wydajność zarówno dla przetwarzania, jak i operacji kwerend, można oddzielić serwer przetwarzania od puli zapytań. Po rozdzieleniu nowe połączenia klientów są przypisywane do replik zapytań tylko w puli zapytań. Jeśli operacje przetwarzania zajmują tylko krótki czas, można oddzielić serwer przetwarzania od puli zapytań tylko przez czas potrzebny do wykonania operacji przetwarzania i synchronizacji, a następnie dołączyć go z powrotem do puli zapytań. Podczas oddzielania serwera przetwarzania z puli zapytań lub dodanie go z powrotem do puli zapytań może potrwać do pięciu minut, aby operacja została ukończona.

## <a name="monitor-qpu-usage"></a>Monitorowanie użycia QPU

Aby ustalić, czy skalowanie w poziomie dla serwera jest konieczne, [należy monitorować serwer](analysis-services-monitor.md) w witrynie Azure portal przy użyciu metryki. Jeśli Twój QPU regularnie się maksymaci, oznacza to, że liczba zapytań dotyczących modeli przekracza limit QPU dla Twojego planu. Metryka długości kolejki zadań puli zapytań zwiększa się również, gdy liczba zapytań w kolejce puli wątków kwerend przekracza dostępne QPU. 

Innym dobrym metryką do obejrzenia jest średnia QPU przez ServerResourceType. Ta metryka porównuje średnią wartość QPU dla serwera podstawowego z pulą zapytań. 

![Metryki skalowania kwerendy w poziomie](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Aby skonfigurować QPU by ServerResourceType**

1. Na wykresie liniowym Metryki kliknij pozycję **Dodaj metrykę**. 
2. W **obszarze RESOURCE**wybierz serwer, a następnie w obszarze nazw **METRYK**wybierz **standardowe metryki Analysis Services**, następnie w **METRYKU**, wybierz **QPU**, a następnie w **agregacji**wybierz pozycję **Avg**. 
3. Kliknij **pozycję Zastosuj dzielenie**. 
4. W **polu WARTOŚCI**wybierz pozycję **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Szczegółowe rejestrowanie diagnostyczne

Użyj dzienników usługi Azure Monitor, aby uzyskać bardziej szczegółową diagnostykę skalowane w poziomie zasobów serwera. Za pomocą dzienników można użyć zapytań usługi Log Analytics, aby wyrwać QPU i pamięć według serwera i repliki. Aby dowiedzieć się więcej, zobacz przykładowe kwerendy w [rejestrowaniu diagnostyki usług Analysis Services](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurowanie zwiększania skali w poziomie

### <a name="in-azure-portal"></a>W witrynie Azure portal

1. W portalu kliknij pozycję **Skaluj w poziomie**. Użyj suwaka, aby wybrać liczbę serwerów replik zapytań. Liczba wybranych replik jest dodatkiem do istniejącego serwera.  

2. W **obszarze Oddziel serwer przetwarzania od puli kwerend**wybierz opcję tak, aby wykluczyć serwer przetwarzania z serwerów zapytań. [Połączenia](#connections) klienta przy użyciu domyślnego `:rw`ciągu połączenia (bez) są przekierowywane do replik w puli zapytań. 

   ![Suwak skalowanie w poziomie](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij **przycisk Zapisz,** aby aprowizować nowe serwery replik kwerend. 

Podczas konfigurowania skalowa w poziomie dla serwera po raz pierwszy modele na serwerze podstawowym są automatycznie synchronizowane z replikami w puli zapytań. Automatyczna synchronizacja występuje tylko raz, podczas pierwszego konfigurowania skalowania w poziomie do jednej lub więcej replik. Kolejne zmiany liczby replik na tym samym serwerze *nie wyzwolą kolejnej automatycznej synchronizacji*. Automatyczna synchronizacja nie nastąpi ponownie, nawet jeśli serwer zostanie ustawiony na zero replik, a następnie ponownie skalowany w poziomie do dowolnej liczby replik. 

## <a name="synchronize"></a>Synchronizowanie 

Operacje synchronizacji muszą być wykonywane ręcznie lub przy użyciu interfejsu API REST.

### <a name="in-azure-portal"></a>W witrynie Azure portal

W **przeglądzie** > modelu > **modelu synchronizuj**.

![Suwak skalowanie w poziomie](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST

Użyj operacji **synchronizacji.**

#### <a name="synchronize-a-model"></a>Synchronizowanie modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Uzyskaj stan synchronizacji  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Kody stanu zwrotu:


|Code  |Opis  |
|---------|---------|
|-1     |  Nieprawidłowy       |
|0     | Replikowanie        |
|1     |  Nawadnianie       |
|2     |   Zakończone       |
|3     |   Niepowodzenie      |
|4     |    Finalizacji     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed użyciem programu PowerShell [należy zainstalować lub zaktualizować najnowszy moduł programu Azure PowerShell](/powershell/azure/install-az-ps). 

Aby uruchomić synchronizację, użyj [funkcji Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Aby ustawić liczbę replik zapytań, należy użyć [programu Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ `-ReadonlyReplicaCount` parametr opcjonalny.

Aby oddzielić serwer przetwarzania od puli zapytań, należy użyć [funkcji Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ `-DefaultConnectionMode` opcjonalny `Readonly`parametr do użycia .

Aby dowiedzieć się więcej, zobacz [Korzystanie z jednostki usługi z modułem Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Połączenia

Na stronie Przegląd serwera znajdują się dwie nazwy serwerów. Jeśli nie skonfigurowano jeszcze skalowania w poziomie dla serwera, obie nazwy serwerów działają tak samo. Po skonfigurowaniu skalowania w poziomie dla serwera należy określić odpowiednią nazwę serwera w zależności od typu połączenia. 

W przypadku połączeń klientów użytkowników końcowych, takich jak power bi desktop, Excel i aplikacje niestandardowe, należy użyć **nazwy serwera**. 

W przypadku usług SSMS, visual studio i ciągów połączeń w programach PowerShell, aplikacjach funkcji platformy Azure i AMO należy użyć **nazwy serwera zarządzania**. Nazwa serwera zarządzania `:rw` zawiera specjalny kwalifikator (odczyt i zapis). Wszystkie operacje przetwarzania występują na (podstawowym) serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skalowanie w górę, skalowanie w dół a skalowanie w poziomie

Można zmienić warstwę cenową na serwerze z wieloma replikami. Ta sama warstwa cenowa ma zastosowanie do wszystkich replik. Operacja skalowania najpierw obniży wszystkie repliki jednocześnie, a następnie wywoła wszystkie repliki w nowej warstwie cenowej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Problem:** Użytkownicy otrzymują błąd **Nie można znaleźć serwera '\<Nazwa serwera>' wystąpienie w trybie połączenia "ReadOnly".**

**Rozwiązanie:** Podczas **wybierania opcji Oddziel serwer przetwarzania od** puli kwerend połączenia klienta przy `:rw`użyciu domyślnego ciągu połączenia (bez) są przekierowywane do replik puli zapytań. Jeśli repliki w puli zapytań nie są jeszcze w trybie online, ponieważ synchronizacja nie została jeszcze zakończona, przekierowane połączenia klientów mogą zakończyć się niepowodzeniem. Aby zapobiec nie powiodło się połączeń, musi istnieć co najmniej dwa serwery w puli zapytań podczas wykonywania synchronizacji. Każdy serwer jest synchronizowany indywidualnie, podczas gdy inne pozostają w trybie online. Jeśli zdecydujesz się nie mieć serwer przetwarzania w puli zapytań podczas przetwarzania, można usunąć go z puli do przetwarzania, a następnie dodać go z powrotem do puli po zakończeniu przetwarzania, ale przed synchronizacją. Użyj metryk pamięci i QPU, aby monitorować stan synchronizacji.



## <a name="related-information"></a>Informacje pokrewne

[Monitorowanie metryk serwera](analysis-services-monitor.md)   
[Zarządzanie usługami analizy platformy Azure](analysis-services-manage.md) 
