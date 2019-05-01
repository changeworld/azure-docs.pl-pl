---
title: Usługa Azure Analysis Services skalowalnego w poziomie | Dokumentacja firmy Microsoft
description: Replikacja serwerów usług Azure Analysis Services za pomocą skalowalnego w poziomie
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 42cdf230379665c596761f9846e52454a3d99680
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939668"
---
# <a name="azure-analysis-services-scale-out"></a>Usługa Azure Analysis Services skalowalnego w poziomie

Za pomocą skalowalnego w poziomie, zapytań klienta mogą być dystrybuowane między wieloma *replikami zapytania* w *zapytania puli*, zmniejszając czasy odpowiedzi podczas obciążeń związanych z zapytaniami wysoka. Można też oddzielić przetwarzania od puli zapytania, zapewnienie, że zapytań klienta nie dotkną operacji przetwarzania. Skalowalny w poziomie można skonfigurować w witrynie Azure portal lub za pomocą interfejsu API REST usługi Analysis Services.

Skalowalny w poziomie jest dostępna dla serwerów warstwy cenowej standardowa. Każdej repliki zapytania jest rozliczana przy użyciu stawki stosowanej jako serwer. Wszystkie repliki zapytania są tworzone w tym samym regionie co serwer. Liczba replik zapytań, które można skonfigurować są ograniczone według regionu, w której znajduje się serwer. Aby dowiedzieć się więcej, zobacz [Dostępność wg regionu](analysis-services-overview.md#availability-by-region). Skalowalny w poziomie nie zwiększa ilość dostępnej pamięci na serwerze. Aby zwiększyć ilość pamięci, musisz uaktualnić swój plan. 

## <a name="why-scale-out"></a>Dlaczego skalowalnego w poziomie?

W przypadku wdrożenia serwera typowe jeden serwer służy jako zarówno serwera przetwarzania i serwera zapytania. Jeśli liczba zapytań klienta względem modele na serwerze przekracza zapytania przetwarzania jednostek (jednostek QPU) dla planu danych na serwerze lub modelu przetwarzanie odbywa się w tym samym czasie jako obciążeń związanych z zapytaniami o wysokiej, może obniżyć wydajność. 

Za pomocą skalowalnego w poziomie, można utworzyć pulę zapytania z nawet siedmioma zasobami repliki dodatkowe zapytania (osiem całkowita, włącznie z Twojej *głównej* serwera). Możesz skalować liczbę replik w puli zapytania do spełnienia wymagań jednostek QPU przez czas krytycznych i w dowolnym momencie możesz oddzielić serwer przetwarzania od puli zapytania. 

Niezależnie od liczby replik zapytań, znajdującym się w puli zapytania obciążeń przetwarzania nie są dystrybuowane między replikami zapytań. Serwer podstawowy służy jako serwer przetwarzania. Repliki zapytania służyć tylko zapytania względem bazy danych modelu, między serwerem podstawowym i każdej repliki w puli zapytania. 

Podczas skalowania w poziomie, może upłynąć do pięciu minut, zanim nowe replikami zapytania przyrostowo zostać dodane do puli zapytania. Gdy wszystkie nowe repliki zapytania są włączone i uruchomione, nowe połączenia klientów jest równoważone między zasobami w puli zapytania. Istniejące połączenia klienta nie są zmieniane z zasobu, które są aktualnie połączeni. Podczas skalowania w, wszelkie istniejące połączenia klienta do kwerendy puli zasobów, który jest usuwany z puli zapytania są kończone. Klientów można połączyć pozostały zasób puli zapytania.

## <a name="how-it-works"></a>Jak to działa

Podczas konfigurowania skalowalnego w poziomie po raz pierwszy, baz danych w modelu na podstawowym serwerze są *automatycznie* synchronizowane z nowej repliki w nowej puli zapytania. Automatyczna synchronizacja występuje tylko jeden raz. Podczas automatycznej synchronizacji plików danych serwera podstawowego (szyfrowane, gdy w magazynie obiektów blob) są kopiowane w innej lokalizacji, również są szyfrowane w stanie spoczynku w usłudze blob storage. Replik w puli zapytania są następnie *uwodniony* przy użyciu danych z drugiego zestawu plików. 

Gdy automatyczna synchronizacja jest wykonywana tylko wtedy, gdy użytkownik skalowalnego w poziomie serwera po raz pierwszy, można również wykonać ręczną synchronizację. Synchronizowanie gwarantuje, że dane w replikach w puli zapytania pasuje do podstawowego serwera. Podczas przetwarzania modeli (odświeżanie) na serwerze podstawowym, należy wykonać synchronizację *po* są wykonywane operacje przetwarzania. Wykonanie synchronizacji kopiuje zaktualizowane dane z podstawowego serwera plików w usłudze blob storage do drugiego zestawu plików. Replik w puli zapytania są następnie uwodniony przy użyciu zaktualizowanych danych z drugiego zestawu plików w magazynie obiektów blob. 

Podczas wykonywania kolejnych operacji skalowania w poziomie, na przykład liczba replik w puli zapytania od dwóch do pięciu, zwiększenie nowej repliki są uwodniony przy użyciu danych z drugiego zestawu plików w magazynie obiektów blob. Brak synchronizacji nie istnieje. Gdyby wykonaniu synchronizacji po skalowanie w poziomie, nowych replik w puli zapytania będą uwodniony dwukrotnie — nadmiarowe wypełniania zasobami. Podczas wykonywania kolejnych operacji skalowania w poziomie, ważne jest pamiętać:

* Wykonaj synchronizację *przed wykonaniem operacji skalowania w poziomie* w celu uniknięcia nadmiarowe wypełniania dodano replik. Wykonywanej współbieżnie zsynchronizowanej i operacji skalowania w poziomie, uruchomione w tym samym czasie nie są dozwolone.

* Podczas przetwarzania zarówno automatyzacji *i* operacji skalowania w poziomie, ważne jest, aby najpierw przetwarzania danych na serwerze podstawowym, a następnie przeprowadzić synchronizację, a następnie wykonaj operację skalowania w poziomie. Ta sekwencja gwarantuje minimalny wpływ na jednostek QPU i zasobów pamięci.

* Synchronizacja jest dozwolone, nawet wtedy, gdy istnieją nie replik w puli zapytania. Jeśli jest skalowana na zewnątrz od zera do co najmniej jedną replikę za pomocą nowych danych z operacji przetwarzania na serwerze podstawowym, najpierw wykonać synchronizację z nie replik w puli zapytania, a następnie skalowalnego w poziomie. Synchronizowanie przed skalowanie pozwala uniknąć wypełniania nadmiarowych replik nowo dodane.

* Podczas usuwania bazy danych modelu z serwera podstawowego, go nie automatycznie usuwane z replik w puli zapytania. Operacja synchronizacji należy wykonać przy użyciu [AzAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) polecenia programu PowerShell, która usuwa plik/s dla tej bazy danych z lokalizacji magazynu udostępnionego obiektu blob repliki, a następnie usuwa modelu Baza danych w replikach w puli zapytania. Aby ustalić, czy baza danych modelu istnieje w replikach w puli zapytania, ale nie na serwerze podstawowym, upewnij się, **oddziel serwer przetwarzania od puli zapytań** jest ustawienie **tak**. Następnie łączyć się przy użyciu podstawowego serwera za pomocą programu SSMS `:rw` kwalifikator, aby sprawdzić, czy baza danych istnieje. Następnie połącz się z replik w puli zapytania, nawiązując połączenie bez `:rw` kwalifikator, aby sprawdzić, czy tej samej bazy danych, również istnieje. Jeśli baza danych istnieje w replikach w puli zapytania, ale nie na serwerze podstawowym, należy uruchomić operację synchronizacji.   

* Podczas zmiany nazwy bazy danych na serwerze podstawowym, Brak dodatkowych czynności niezbędne do zapewnienia, że baza danych jest poprawnie synchronizowane wszystkie repliki. Po zmianie nazwy, należy wykonać synchronizacji przy użyciu [AzAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) polecenie, określając `-Database` parametru przy użyciu starej nazwy bazy danych. Wykonanie synchronizacji usuwa z wszystkie repliki bazy danych i plików przy użyciu starej nazwy. Następnie wykonać, określając inną synchronizacji `-Database` parametru z nową nazwą bazy danych. Drugi synchronizacji kopiuje do drugiego zestawu plików nowo nazwanym bazy danych i hydrates wszystkie repliki. Nie można wykonać te synchronizacje poleceniem Synchronize modelu w portalu.

### <a name="separate-processing-from-query-pool"></a>Oddziel przetwarzania od puli zapytania

Aby uzyskać maksymalną wydajność przetwarzania i operacje zapytań można oddzielić serwer przetwarzania od puli zapytania. Gdy oddzielone, istniejących i nowych połączeń klientów przypisanych do repliki zapytania w puli zapytania tylko. Operacje przetwarzania zajmował jedynie krótkim czasie, można oddzielić serwer przetwarzania od puli zapytania tylko ilości czasu potrzebnego do wykonywania operacji przetwarzania i synchronizacji, a następnie dołącz ją z powrotem do puli zapytania. 

## <a name="monitor-qpu-usage"></a>Użycie jednostek QPU monitora

Aby ustalić, czy skalowalnego w poziomie serwera jest niezbędne, serwer w witrynie Azure portal należy monitorować za pomocą metryk. Jeśli Twoje QPU regularnie wydłużyć, oznacza to, że liczba zapytań dotyczących modeli przekracza limit jednostek QPU dla planu. Metryka długość kolejki zadania puli zapytania zwiększa także dostępne QPU przekroczenia liczby zapytań w kolejce puli wątków zapytań. 

Inny dobrą metryką, aby obejrzeć to średni QPU przez ServerResourceType. Ta metryka porównuje QPU średnia dla serwera podstawowego, korzystając z niego puli zapytania. 

![Zapytania skalowania w poziomie metryki](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Aby skonfigurować jednostek QPU przez ServerResourceType
1. Na wykresie liniowym metryki kliknij **Dodaj metrykę**. 
2. W **zasobów**, wybierz serwer, a następnie w **METRYKI przestrzeni nazw**, wybierz opcję **standardowych metryk usług Analysis Services**, a następnie w obszarze **METRYKI**, Wybierz **QPU**, a następnie w polu **agregacji**, wybierz opcję **Avg**. 
3. Kliknij przycisk **zastosować podział**. 
4. W **wartości**, wybierz opcję **ServerResourceType**.  

Aby dowiedzieć się więcej, zobacz [monitorowanie metryk serwera](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurowanie zwiększania skali w poziomie

### <a name="in-azure-portal"></a>W witrynie Azure portal

1. W portalu, kliknij przycisk **skalowalnego w poziomie**. Wybierz liczbę serwerów replik zapytań za pomocą suwaka. Liczba replik, które wybierzesz jest oprócz istniejącego serwera.

2. W **oddziel serwer przetwarzania od puli zapytań**, wybierz opcję Tak, aby wykluczyć serwer przetwarzania z serwerów z zapytania. Klient [połączeń](#connections) przy użyciu domyślne parametry połączenia (bez `:rw`) są przekierowywane do replik w puli zapytania. 

   ![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij przycisk **Zapisz** do aprowizowania nowych serwerów repliki zapytania. 

Podczas konfigurowania skalowalnego w poziomie serwera po raz pierwszy, modele na podstawowym serwerze są automatycznie synchronizowane z replik w puli zapytania. Automatyczna synchronizacja występuje tylko wtedy, gdy podczas pierwszej konfiguracji skalowania w poziomie do co najmniej jedną replikę. Kolejne zmiany liczby replik na tym samym serwerze *nie spowodują uruchomienia innego automatyczną synchronizację*. Automatyczna synchronizacja zostanie przeprowadzona ponownie nawet wtedy, gdy serwer na zero repliki i następnie ponownie skalowalnego w poziomie do dowolnej liczby replik. 

## <a name="synchronize"></a>Synchronizuj 

Operacje synchronizacji należy wykonać ręcznie lub za pomocą interfejsu API REST.

### <a name="in-azure-portal"></a>W witrynie Azure portal

W **Przegląd** > model > **modelu Synchronize**.

![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST

Użyj **synchronizacji** operacji.

#### <a name="synchronize-a-model"></a>Synchronizowanie modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Pobierz stan synchronizacji  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Zwróć kodów stanu:


|Kod  |Opis  |
|---------|---------|
|-1     |  Nieprawidłowy       |
|0     | Replikowanie        |
|1     |  Ponownego wypełniania       |
|2     |   Zakończone       |
|3     |   Niepowodzenie      |
|4     |    Kończenie     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem korzystania z programu PowerShell, [Instalowanie lub aktualizowanie najnowszy moduł Azure PowerShell](/powershell/azure/install-az-ps). 

Aby uruchomić synchronizacji, użyj [AzAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Aby ustawić liczba replik zapytań, należy użyć [AzAnalysisServicesServer zestaw](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ opcjonalne `-ReadonlyReplicaCount` parametru.

Aby oddzielić serwer przetwarzania od puli zapytania, należy użyć [AzAnalysisServicesServer zestaw](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Określ opcjonalne `-DefaultConnectionMode` parametr do użycia `Readonly`.

Aby dowiedzieć się więcej, zobacz [przy użyciu nazwy głównej usługi przy użyciu modułu Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Połączenia

Na stronie przeglądu serwera istnieją dwie nazwy serwera. Jeśli nie skonfigurowano jeszcze skalowalnego w poziomie serwera, obie nazwy serwera działać tak samo. Po skonfigurowaniu skalowalnego w poziomie serwera, należy określić nazwę odpowiedniego serwera, w zależności od typu połączenia. 

Dla połączeń klienckich użytkowników końcowych, takich jak Power BI Desktop, Excel i aplikacje niestandardowe, użyj **nazwy serwera**. 

SSMS, SSDT i parametry połączenia w programie PowerShell, użyj aplikacji funkcji platformy Azure i AMO, **nazwa serwera zarządzania**. Nazwa serwera zarządzania obejmuje specjalny `:rw` kwalifikator (odczyt zapis). Wszystkie operacje przetwarzania są wykonywane na serwerze zarządzania (podstawowy).

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Problem:** Użytkownicy otrzymują błąd **nie można odnaleźć serwera "\<nazwa serwera >" wystąpienie w trybie połączenia "ReadOnly".**

**Rozwiązanie:** Podczas wybierania **oddziel serwer przetwarzania od puli zapytań** opcji połączeń klienta za pomocą domyślne parametry połączenia (bez `:rw`) są przekierowywane do repliki puli zapytania. Jeśli replik w puli zapytania nie zostały jeszcze w trybie online ponieważ synchronizacji nie został jeszcze zostały zakończone, połączeń przekierowanego klienckich może zakończyć się niepowodzeniem. Aby zapobiec połączenia zakończone niepowodzeniem, musi istnieć co najmniej dwa serwery w puli zapytania podczas przeprowadzania synchronizacji. Każdy serwer jest synchronizowany indywidualnie, podczas gdy inne pozostać w trybie online. Jeśli zdecydujesz się w puli zapytań podczas przetwarzania nie istnieje serwer przetwarzania, można usunąć go z puli do przetwarzania, a następnie dodaj go do puli po przetwarzanie zostało ukończone, ale przed synchronizacją. Metryki użycia pamięci i QPU, aby monitorować stan synchronizacji.



## <a name="related-information"></a>Informacje pokrewne

[Monitorowanie metryk serwera](analysis-services-monitor.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md) 
