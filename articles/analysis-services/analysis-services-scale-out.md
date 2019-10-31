---
title: Azure Analysis Services skalowanie w poziomie | Microsoft Docs
description: Replikowanie serwerów Azure Analysis Services przy użyciu skalowania w poziomie
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: af1a0db397510014301a58aea7238b695a6c0740
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146446"
---
# <a name="azure-analysis-services-scale-out"></a>Skalowanie w poziomie usług Azure Analysis Services

W przypadku skalowania w poziomie zapytania klientów mogą być dystrybuowane między wieloma *replikami zapytań* w *puli zapytań*, co skraca czasy odpowiedzi w przypadku dużych obciążeń zapytań. Można również oddzielić przetwarzanie od puli zapytań, dzięki czemu nie mają negatywnego wpływ na zapytania klientów. Skalowanie w poziomie można skonfigurować w Azure Portal lub przy użyciu interfejsu API REST Analysis Services.

Skalowanie w poziomie jest dostępne dla serwerów w warstwie cenowej standardowa. Każda replika kwerendy jest rozliczana według stawki co serwer. Wszystkie repliki zapytań są tworzone w tym samym regionie, w którym znajduje się serwer. Liczba replik zapytań, które można skonfigurować, jest ograniczona przez region, w którym znajduje się serwer. Aby dowiedzieć się więcej, zobacz [dostępność według regionów](analysis-services-overview.md#availability-by-region). Skalowanie w poziomie nie zwiększa ilości dostępnej pamięci dla serwera. Aby zwiększyć ilość pamięci, należy uaktualnić plan. 

## <a name="why-scale-out"></a>Dlaczego warto skalować w poziomie?

W typowym wdrożeniu serwera jeden serwer służy zarówno jako serwer przetwarzania, jak i serwer zapytań. Jeśli liczba zapytań klientów względem modeli na serwerze przekracza jednostki przetwarzania zapytań (QPU) dla planu serwera lub przetwarzanie modelu odbywa się w tym samym czasie co duże obciążenia zapytań, wydajność może ulec zmniejszeniu. 

Dzięki skalowaniu w poziomie można utworzyć pulę zapytań zawierającą do siedmiu dodatkowych zasobów repliki zapytań (osiem łącznie łącznie z serwerem *podstawowym* ). Liczbę replik w puli zapytań można skalować tak, aby spełniały wymagania QPU w krytycznych porach, a serwer przetwarzania od puli zapytań w dowolnym momencie. 

Bez względu na liczbę replik zapytań używanych w puli zapytań przetwarzanie obciążeń nie jest dystrybuowane między replikami zapytań. Serwer podstawowy służy jako serwer przetwarzania. Repliki zapytań oferują tylko zapytania względem baz danych modelu zsynchronizowanych między serwerem podstawowym a każdą repliką w puli zapytań. 

Skalowanie w górę może potrwać do 5 minut, aby nowe repliki zapytań były przyrostowo dodawane do puli zapytań. Gdy wszystkie nowe repliki zapytań są uruchomione, nowe połączenia klientów są zrównoważone dla zasobów w puli zapytań. Istniejące połączenia klientów nie są zmieniane z zasobu, z którym są obecnie połączone. Podczas skalowania w programie wszystkie istniejące połączenia klientów z pulą zapytań, które są usuwane z puli zapytań, są przerywane. Klienci mogą ponownie połączyć się z innym zasobem puli zapytań.

## <a name="how-it-works"></a>Zasady działania

Podczas konfigurowania skalowania w poziomie po raz pierwszy bazy danych modelu na serwerze podstawowym są *automatycznie* synchronizowane z nowymi replikami w nowej puli zapytań. Automatyczna synchronizacja odbywa się tylko raz. Podczas automatycznej synchronizacji pliki danych serwera podstawowego (szyfrowane przy użyciu magazynu obiektów BLOB) są kopiowane do drugiej lokalizacji, również szyfrowane w usłudze BLOB Storage. Repliki w puli zapytań są następnie *odwodnione* danymi z drugiego zestawu plików. 

Gdy automatyczna synchronizacja jest przeprowadzana tylko w przypadku skalowania serwera po raz pierwszy, można także przeprowadzić synchronizację ręczną. Synchronizacja gwarantuje, że dane w replikach w puli zapytań są zgodne z serwerem podstawowym. Podczas przetwarzania (odświeżania) modeli na serwerze podstawowym należy wykonać synchronizację *po* zakończeniu operacji przetwarzania. Ta synchronizacja kopiuje zaktualizowane dane z plików serwera podstawowego w usłudze BLOB Storage do drugiego zestawu plików. Repliki w puli zapytań są następnie oznaczane przy użyciu zaktualizowanych danych z drugiego zestawu plików w usłudze BLOB Storage. 

Podczas kolejnej operacji skalowania w poziomie, na przykład zwiększając liczbę replik w puli zapytań z dwóch do pięciu, nowe repliki są przechowywane przy użyciu danych z drugiego zestawu plików w usłudze BLOB Storage. Brak synchronizacji. Jeśli przeprowadzono synchronizację po skalowaniu w poziomie, nowe repliki w puli zapytań byłyby dwukrotnie odwodnione — nadmiarowe odwodnienie. Podczas wykonywania kolejnej operacji skalowania w poziomie należy pamiętać o następujących kwestiach:

* Wykonaj synchronizację *przed operacją skalowania w poziomie* , aby uniknąć nadmiarowego uzupełniania dodanych replik. Współbieżne synchronizowanie i operacje skalowania w poziomie uruchomione w tym samym czasie są niedozwolone.

* Podczas automatyzowania operacji przetwarzania *i* skalowania w poziomie ważne jest, aby najpierw przetworzyć dane na serwerze podstawowym, a następnie przeprowadzić synchronizację, a następnie wykonać operację skalowania w poziomie. Ta sekwencja gwarantuje minimalny wpływ na QPU i zasoby pamięci.

* Synchronizacja jest dozwolona nawet wtedy, gdy w puli zapytań nie ma żadnych replik. W przypadku skalowania z zera do co najmniej jednej repliki z nowymi danymi z operacji przetwarzania na serwerze podstawowym Wykonaj synchronizację najpierw bez replik w puli zapytań, a następnie Skaluj w poziomie. Synchronizacja przed skalowaniem nie pozwala na uniknięcie nadmiarowego uzupełniania nowo dodanych replik.

* W przypadku usuwania bazy danych modelu z serwera podstawowego nie są automatycznie usuwane z replik w puli zapytań. Należy wykonać operację synchronizacji za pomocą polecenia programu PowerShell [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) , które usuwa pliki/s dla tej bazy danych z udostępnionej lokalizacji magazynu obiektów BLOB repliki, a następnie usuwa bazę danych modelu w replikach w puli zapytań. Aby określić, czy istnieje baza danych modelu w replikach w puli zapytań, ale nie na serwerze podstawowym, upewnij się, że ustawienie **oddziel serwer przetwarzania od tworzenia zapytań do puli** ma **wartość tak**. Następnie użyj programu SSMS do nawiązania połączenia z serwerem podstawowym przy użyciu kwalifikatora `:rw`, aby sprawdzić, czy baza danych istnieje. Następnie połącz się z replikami w puli zapytań, łącząc bez kwalifikatora `:rw`, aby sprawdzić, czy ta sama baza danych również istnieje. Jeśli baza danych istnieje w replikach w puli zapytań, ale nie na serwerze podstawowym, uruchom operację synchronizacji.   

* W przypadku zmiany nazwy bazy danych na serwerze podstawowym należy wykonać dodatkowy krok, aby upewnić się, że baza danych jest prawidłowo synchronizowana z dowolnymi replikami. Po zmianie nazwy Wykonaj synchronizację przy użyciu polecenia [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) określającego parametr `-Database` o starej nazwie bazy danych. Ta Synchronizacja powoduje usunięcie bazy danych i plików ze starą nazwą z dowolnych replik. Następnie wykonaj kolejną synchronizację określającą parametr `-Database` z nową nazwą bazy danych. Druga synchronizacja kopiuje nowo nazwaną bazę danych do drugiego zestawu plików i odwodnione wszystkie repliki. Tych synchronizacji nie można wykonać za pomocą polecenia Synchronizuj model w portalu.

### <a name="separate-processing-from-query-pool"></a>Oddzielne przetwarzanie od puli zapytań

W celu uzyskania maksymalnej wydajności operacji przetwarzania i wykonywania zapytań można wybrać opcję oddzielenia serwera przetwarzania od puli zapytań. Po oddzieleniu nowe połączenia klientów są przypisywane do replik zapytań tylko w puli zapytań. Jeśli operacje przetwarzania są wykonywane tylko przez krótki czas, można wybrać opcję oddzielenia serwera przetwarzania od puli zapytań tylko przez czas potrzebny do wykonania operacji przetwarzania i synchronizacji, a następnie dołączyć ją z powrotem do puli zapytań. W przypadku oddzielenia serwera przetwarzania od puli zapytań lub dodania go z powrotem do puli zapytań ukończenie operacji może potrwać do 5 minut.

## <a name="monitor-qpu-usage"></a>Monitorowanie użycia QPU

Aby ustalić, czy skalowanie w poziomie serwera jest konieczne, Monitoruj serwer w Azure Portal przy użyciu metryk. Jeśli QPU regularnie maxes, oznacza to, że liczba zapytań względem Twoich modeli przekracza limit QPU dla Twojego planu. Metryka długości kolejki zadań puli zapytań zwiększa się także wtedy, gdy liczba zapytań w kolejce puli wątków zapytań przekracza dostępne QPU. 

Kolejną dobrą metryką dla oglądania jest średnia QPU przez ServerResourceType. Ta Metryka porównuje średnią QPU dla serwera podstawowego z serwerem puli zapytań. 

![Metryki skalowania zapytania w poziomie](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Aby skonfigurować QPU przez ServerResourceType
1. Na wykresie liniowym metryki kliknij pozycję **Dodaj metrykę**. 
2. W **obszarze zasób**wybierz swój serwer, a następnie w **obszarze Przestrzeń nazw**wybierz pozycję **Analysis Services metryki standardowe**, a następnie w polu **Metryka**wybierz pozycję **QPU**, a następnie w obszarze **agregacja**wybierz pozycję **średnia**. 
3. Kliknij przycisk **Zastosuj podział**. 
4. W polu **wartości**wybierz pozycję **ServerResourceType**.  

Aby dowiedzieć się więcej, zobacz [monitorowanie metryk serwera](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurowanie zwiększania skali w poziomie

### <a name="in-azure-portal"></a>W Azure Portal

1. W portalu kliknij pozycję **skalowanie w poziomie**. Użyj suwaka, aby wybrać liczbę serwerów repliki zapytań. Liczba wybranych replik jest poza istniejącym serwerem.  

2. W **oddzielnym serwerze przetwarzania od puli zapytań**wybierz pozycję tak, aby wykluczyć serwer przetwarzania z serwerów zapytań. [Połączenia](#connections) klienckie korzystające z domyślnych parametrów połączenia (bez `:rw`) są przekierowywane do replik w puli zapytań. 

   ![Suwak skalowania w poziomie](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij przycisk **Zapisz** , aby zainicjować obsługę administracyjną nowych serwerów repliki zapytań. 

Podczas pierwszego konfigurowania skalowania w poziomie dla serwera modele na serwerze podstawowym są automatycznie synchronizowane z replikami w puli zapytań. Automatyczna synchronizacja odbywa się tylko raz, podczas pierwszej konfiguracji skalowania w poziomie do co najmniej jednej repliki. Kolejne zmiany liczby replik na tym samym serwerze *nie spowodują wyzwolenia innej automatycznej synchronizacji*. Automatyczna synchronizacja nie zostanie wykonana ponownie, nawet jeśli ustawisz serwer na wartość zero replik, a następnie ponownie skalujesz do dowolnej liczby replik. 

## <a name="synchronize"></a>Zsynchronizuj 

Operacje synchronizacji należy wykonać ręcznie lub przy użyciu interfejsu API REST.

### <a name="in-azure-portal"></a>W Azure Portal

W obszarze **przegląd** > model > **zsynchronizuj model**.

![Suwak skalowania w poziomie](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST

Użyj operacji **synchronizacji** .

#### <a name="synchronize-a-model"></a>Synchronizowanie modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Pobierz stan synchronizacji  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Kody stanu powrotu:


|Kod  |Opis  |
|---------|---------|
|-1     |  Nieprawidłowe       |
|0     | Replikacji        |
|1     |  Ponownego wypełniania       |
|2     |   Zakończone       |
|3     |   Niepowodzenie      |
|4     |    Finalizowanie     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem korzystania z programu PowerShell należy [zainstalować lub zaktualizować najnowszy moduł Azure PowerShell](/powershell/azure/install-az-ps). 

Aby uruchomić synchronizację, należy użyć [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Aby ustawić liczbę replik zapytań, użyj [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ opcjonalny parametr `-ReadonlyReplicaCount`.

Aby oddzielić serwer przetwarzania od puli zapytań, użyj polecenie [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ opcjonalny parametr `-DefaultConnectionMode`, który ma być używany `Readonly`.

Aby dowiedzieć się więcej, zobacz [Używanie jednostki usługi z modułem AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Połączenia

Na stronie Przegląd serwera istnieją dwie nazwy serwerów. Jeśli nie skonfigurowano jeszcze skalowania w poziomie serwera, obie nazwy serwerów działają tak samo. Po skonfigurowaniu skalowania w poziomie dla serwera należy określić odpowiednią nazwę serwera w zależności od typu połączenia. 

W przypadku połączeń klienckich użytkowników końcowych, takich jak Power BI Desktop, Excel i aplikacje niestandardowe, użyj **nazwy serwera**. 

W przypadku programów SSMS, Visual Studio i parametrów połączenia w programie PowerShell, aplikacji funkcji platformy Azure i biblioteki AMO Użyj **nazwy serwera zarządzania**. Nazwa serwera zarządzania zawiera specjalny kwalifikator `:rw` (do odczytu i zapisu). Wszystkie operacje przetwarzania są wykonywane na podstawowym serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skalowanie w górę, skalowanie w dół a skalowanie w poziomie

Warstwę cenową można zmienić na serwerze z wieloma replikami. Ta sama warstwa cenowa ma zastosowanie do wszystkich replik. W wyniku operacji skalowania wszystkie repliki są najpierw wyłączane, a następnie wszystkie repliki zostaną wyświetlone w nowej warstwie cenowej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Problem:** Użytkownicy **nie mogą znaleźć serwera "\<Name wystąpienia serwera >" w trybie połączenia "ReadOnly".**

**Rozwiązanie:** Po wybraniu opcji **oddziel serwer przetwarzania od puli zapytań** , połączenia klienta przy użyciu domyślnych parametrów połączenia (bez `:rw`) są przekierowywane do replik puli zapytań. Jeśli repliki w puli zapytań nie są jeszcze w trybie online, ponieważ synchronizacja nie została jeszcze zakończona, przekierowane połączenia klientów mogą zakończyć się niepowodzeniem. Aby uniknąć nieudanych połączeń, w puli zapytań muszą znajdować się co najmniej dwa serwery podczas przeprowadzania synchronizacji. Każdy serwer jest synchronizowany pojedynczo, podczas gdy inne pozostają w trybie online. Jeśli zdecydujesz, aby nie mieć serwera przetwarzania w puli zapytań podczas przetwarzania, możesz usunąć go z puli do przetworzenia, a następnie dodać z powrotem do puli po zakończeniu przetwarzania, ale przed synchronizacją. Do monitorowania stanu synchronizacji służą metryki pamięci i QPU.



## <a name="related-information"></a>Informacje pokrewne

[Monitoruj metryki serwera](analysis-services-monitor.md)   
[Zarządzaj Azure Analysis Services](analysis-services-manage.md) 
