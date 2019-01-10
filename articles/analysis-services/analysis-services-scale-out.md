---
title: Usługa Azure Analysis Services skalowalnego w poziomie | Dokumentacja firmy Microsoft
description: Replikacja serwerów usług Azure Analysis Services za pomocą skalowalnego w poziomie
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 775de554f39df8359c3852a2d7fa876fd12199d2
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190845"
---
# <a name="azure-analysis-services-scale-out"></a>Usługa Azure Analysis Services skalowalnego w poziomie

Za pomocą skalowalnego w poziomie, zapytań klienta mogą być dystrybuowane między wieloma *replikami zapytania* w puli zapytania, zmniejszając czasy odpowiedzi podczas obciążeń związanych z zapytaniami wysokiej. Można też oddzielić przetwarzania od puli zapytania, zapewnienie, że zapytań klienta nie dotkną operacji przetwarzania. Skalowalny w poziomie można skonfigurować w witrynie Azure portal lub za pomocą interfejsu API REST usługi Analysis Services.

## <a name="how-it-works"></a>Jak to działa

W przypadku wdrożenia serwera typowe jeden serwer służy jako zarówno serwera przetwarzania i serwera zapytania. Jeśli liczba zapytań klienta względem modele na serwerze przekracza zapytania przetwarzania jednostek (jednostek QPU) dla planu danych na serwerze lub modelu przetwarzanie odbywa się w tym samym czasie jako obciążeń związanych z zapytaniami o wysokiej, może obniżyć wydajność. 

Za pomocą skalowalnego w poziomie można utworzyć pulę zapytania przy użyciu maksymalnie siedem dodatkowe zasoby repliki (łącznie ośmioma, wliczając serwer). Możesz skalować liczbę replik zapytań do spełnienia wymagań jednostek QPU przez czas krytycznych i w dowolnym momencie możesz oddzielić serwer przetwarzania od puli zapytania. Wszystkie repliki zapytania są tworzone w tym samym regionie co serwer.

Niezależnie od liczby replik zapytań, znajdującym się w puli zapytania obciążeń przetwarzania nie są dystrybuowane między replikami zapytań. Pojedynczy serwer służy jako serwer przetwarzania. Repliki zapytania służyć tylko zapytania względem modeli synchronizowane między każdej repliki zapytania w puli zapytania. 

Podczas skalowania w poziomie, nowe replikami zapytania są dodawane do puli zapytania przyrostowo. Może upłynąć do pięciu minut, zanim nowe zasoby repliki zapytania mają zostać uwzględnione w puli zapytania. Gdy wszystkie nowe repliki zapytania są włączone i uruchomione, nowe połączenia klientów jest równoważone między wszystkich zasobów w puli zapytania. Istniejące połączenia klienta nie są zmieniane z zasobu, które są aktualnie połączeni.  Podczas skalowania w, wszelkie istniejące połączenia klienta do kwerendy puli zasobów, który jest usuwany z puli zapytania są kończone. Ich ponowne łączenie się pozostały zasób puli zapytania po zakończeniu skalowanie w operacji, który może zająć maksymalnie pięć minut.

Podczas przetwarzania modeli, po ukończeniu operacji przetwarzania, należy wykonać synchronizację między serwerem przetwarzania a replikami zapytania. Automatyzacja operacji przetwarzania, należy skonfigurować operacji synchronizacji po pomyślnym zakończeniu operacji przetwarzania. Można przeprowadzić synchronizację ręcznie w portalu lub przy użyciu programu PowerShell lub interfejsu API REST. 

### <a name="separate-processing-from-query-pool"></a>Oddziel przetwarzania od puli zapytania

Aby uzyskać maksymalną wydajność przetwarzania i operacje zapytań można oddzielić serwer przetwarzania od puli zapytania. Gdy oddzielone, istniejących i nowych połączeń klientów przypisanych do repliki zapytania w puli zapytania tylko. Operacje przetwarzania zajmował jedynie krótkim czasie, można oddzielić serwer przetwarzania od puli zapytania tylko ilości czasu potrzebnego do wykonywania operacji przetwarzania i synchronizacji, a następnie dołącz ją z powrotem do puli zapytania. 

> [!NOTE]
> Skalowalny w poziomie jest dostępna dla serwerów warstwy cenowej standardowa. Każdej repliki zapytania jest rozliczana przy użyciu stawki stosowanej jako serwer.

> [!NOTE]
> Skalowalny w poziomie nie zwiększa ilość dostępnej pamięci na serwerze. Aby zwiększyć ilość pamięci, musisz uaktualnić swój plan.

## <a name="region-limits"></a>Limity regionu

Liczba replik zapytań, które można skonfigurować są ograniczone według regionu, w której znajduje się serwer. Aby dowiedzieć się więcej, zobacz [Dostępność wg regionu](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Użycie jednostek QPU monitora

 Aby ustalić, czy skalowalnego w poziomie serwera jest niezbędne, serwer w witrynie Azure portal należy monitorować za pomocą metryk. Jeśli Twoje QPU regularnie wydłużyć, oznacza to, że liczba zapytań dotyczących modeli przekracza limit jednostek QPU dla planu. Metryka długość kolejki zadania puli zapytania zwiększa także dostępne QPU przekroczenia liczby zapytań w kolejce puli wątków zapytań. Aby dowiedzieć się więcej, zobacz [monitorowanie metryk serwera](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Skonfiguruj skalowalny w poziomie

### <a name="in-azure-portal"></a>W witrynie Azure portal

1. W portalu, kliknij przycisk **skalowalnego w poziomie**. Wybierz liczbę serwerów replik zapytań za pomocą suwaka. Liczba replik, które wybierzesz jest oprócz istniejącego serwera.

2. W **oddziel serwer przetwarzania od puli zapytań**, wybierz opcję Tak, aby wykluczyć serwer przetwarzania z serwerów z zapytania. Połączenia klienckie za pomocą domyślne parametry połączenia (bez: rw) są przekierowywane do replik w puli zapytania. 

   ![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij przycisk **Zapisz** do aprowizowania nowych serwerów repliki zapytania. 

Modele tabelaryczne na podstawowym serwerze są synchronizowane z serwerem funkcji replica. Po ukończeniu synchronizacji rozpocznie się puli zapytania, dystrybucja przychodzące zapytania na serwery repliki. 

## <a name="synchronization"></a>Synchronizacja 

Podczas aprowizacji nowej repliki zapytania usług Azure Analysis Services automatycznie replikuje swoje modele we wszystkich replik. Można również wykonać ręczną synchronizację za pomocą portalu lub interfejsu API REST. W przypadku przetwarzania modeli, należy wykonać synchronizację, dlatego aktualizacje są synchronizowane między repliki zapytania.

### <a name="in-azure-portal"></a>W witrynie Azure portal

W **Przegląd** > model > **modelu Synchronize**.

![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST

Użyj **synchronizacji** operacji.

#### <a name="synchronize-a-model"></a>Synchronizowanie modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Pobierz stan synchronizacji  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

Przed rozpoczęciem korzystania z programu PowerShell, [Instalowanie lub aktualizowanie najnowszy moduł AzureRM](https://github.com/Azure/azure-powershell/releases). 

Aby ustawić liczba replik zapytań, należy użyć [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Określ opcjonalne `-ReadonlyReplicaCount` parametru.

Aby uruchomić synchronizacji, użyj [AzureAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Połączenia

Na stronie przeglądu serwera istnieją dwie nazwy serwera. Jeśli nie skonfigurowano jeszcze skalowalnego w poziomie serwera, obie nazwy serwera działać tak samo. Po skonfigurowaniu skalowalnego w poziomie serwera, należy określić nazwę odpowiedniego serwera, w zależności od typu połączenia. 

Dla połączeń klienckich użytkowników końcowych, takich jak Power BI Desktop, Excel i aplikacje niestandardowe, użyj **nazwy serwera**. 

SSMS, SSDT i parametry połączenia w programie PowerShell, użyj aplikacji funkcji platformy Azure i AMO, **nazwa serwera zarządzania**. Nazwa serwera zarządzania obejmuje specjalny `:rw` kwalifikator (odczyt zapis). Wszystkie operacje przetwarzania są wykonywane na serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Problem:** Użytkownicy otrzymują błąd **nie można odnaleźć serwera "\<nazwa serwera >" wystąpienie w trybie połączenia "ReadOnly".**

**Rozwiązanie:** Podczas wybierania **oddziel serwer przetwarzania od puli zapytań** opcji połączeń klienta za pomocą domyślne parametry połączenia (bez: rw) są przekierowywane do repliki puli zapytania. Jeśli replik w puli zapytania nie zostały jeszcze w trybie online ponieważ synchronizacji nie został jeszcze zostały zakończone, połączeń przekierowanego klienckich może zakończyć się niepowodzeniem. Aby zapobiec połączenia zakończone niepowodzeniem, należy zrezygnować z oddziel serwer przetwarzania od puli zapytań, dopiero po zakończeniu operacji skalowania w poziomie i synchronizacji. Metryki pamięci i QPU służy do monitorowania stanu synchronizacji.

## <a name="related-information"></a>Informacje pokrewne

[Monitorowanie metryk serwera](analysis-services-monitor.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md) 

