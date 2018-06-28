---
title: Skalowalne usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Replikacja serwerów usług Azure Analysis Services z skalowalnego w poziomie
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5d1d55a1cf29d6dc3574099cd468c42ccfc72f5b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597131"
---
# <a name="azure-analysis-services-scale-out"></a>Skalowalne usług Azure Analysis Services

Z skalowalnego w poziomie, zapytań klienta mogą być dystrybuowane między wieloma *zapytania replik* w puli zapytania, zmniejszyć czasy odpowiedzi w ciągu zapytania wysokiej obciążeń. Można też oddzielić przetwarzania z puli zapytania, zapewnienie, że zapytań klienta nie wpływały niekorzystnie na przetworzenie operacji. Skalowalny w poziomie można skonfigurować w portalu Azure lub za pomocą interfejsu API REST usług analizy.

## <a name="how-it-works"></a>Jak to działa

We wdrożeniu serwera typowe jeden serwer służy jako serwer przetwarzania i serwer kwerend. Jeśli liczba zapytań klienta dotyczących modeli na serwerze przekracza zapytania przetwarzania jednostki (QPU) dla serwera planu, lub modelu przetwarzanie odbywa się w tym samym czasie jako wysoki zapytania obciążeń, może obniżyć wydajność. 

Z skalowalnego w poziomie można utworzyć pulę zapytania z replikami dodatkowe zapytania do siedmiu (łącznie osiem, łącznie z serwera). Liczba replik zapytania do spełnienia wymagań QPU w czasie krytyczne można skalować i w dowolnym momencie można oddzielić serwer przetwarzania z puli zapytania. Wszystkie repliki zapytania są tworzone w tym samym regionie co serwer.

Niezależnie od liczby replik zapytania, znajdującym się w puli zapytania obciążenia przetwarzaniem danych nie są dystrybuowane między replikami zapytania. Pojedynczy serwer służy jako serwer przetwarzania. Zapytanie replik służyć tylko kwerend dotyczących modeli synchronizowane między każdej replice w puli zapytania. 

Po zakończeniu operacji przetwarzania odbywa się synchronizacja między serwerem przetwarzania i serwery repliki zapytania. Automatyzacja operacji przetwarzania, należy skonfigurować synchronizację, po pomyślnym ukończeniu operacji przetwarzania. Można wykonać synchronizację ręcznie w portalu lub przy użyciu programu PowerShell lub interfejsu API REST.

> [!NOTE]
> Skalowalny w poziomie jest dostępna dla serwerów w standardowej warstwie cenowej. Jest on rozliczany każdej repliki zapytania w tym samym poziomie jak serwer.

> [!NOTE]
> Skalowalny w poziomie nie zwiększyć ilość dostępnej pamięci serwera. Aby zwiększyć ilość pamięci, należy uaktualnić swój plan.

## <a name="region-limits"></a>Limity regionu

Liczba replik zapytania, które można skonfigurować są ograniczone przez serwer znajduje się w regionie. Stosuje się następujące ograniczenia:

|Region  |Maksymalna liczba replik  |
|---------|---------|
|Wschodnie stany USA 2    |    7     |
|Środkowo-zachodnie stany USA     |    7     |
|Europa Zachodnia     |    7     |
|Zachodnie stany USA     |     7    |
|Środkowe stany USA     |     3    |
|Azja Południowo-Wschodnia    |     3    |
|Innych regionów  |   1    |



## <a name="monitor-qpu-usage"></a>Monitorowanie użycia QPU

 Aby ustalić, czy skalowalnego w poziomie serwera jest niezbędne, serwer w portalu Azure należy monitorować za pomocą metryki. Jeśli Twoje QPU regularnie maxes wychodzących, oznacza to, że liczba zapytań dotyczących modeli przekracza limit QPU dla planu. Metryka długość kolejki zadania puli zapytania zwiększa także liczby zapytań w kolejki puli wątków zapytania przekracza dostępne QPU. Aby dowiedzieć się więcej, zobacz [monitorowanie metryk serwera](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Skonfiguruj skalowalny w poziomie

### <a name="in-azure-portal"></a>W portalu Azure

1. W portalu kliknij **skalowalnego w poziomie**. Wybierz liczbę serwerów repliki zapytania za pomocą suwaka. Liczba replik wybrany jest oprócz istniejącego serwera.

2. W **oddzielić serwer przetwarzania z puli podczas badania**, wybierz opcję Tak, aby wykluczyć z serwerów zapytania serwera przetwarzania.

   ![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij przycisk **zapisać** do obsługi administracyjnej nowych serwerów repliki zapytania. 

Modele tabelaryczne na podstawowym serwerze są synchronizowane z serwerów repliki. Po ukończeniu synchronizacji rozpocznie się puli zapytania dystrybucja zapytania przychodzące między serwery replik. 


## <a name="synchronization"></a>Synchronizacja 

Podczas obsługi administracyjnej nowych replik kwerendy usług Azure Analysis Services automatycznie replikuje modeli we wszystkich replik. Można również wykonać ręczną synchronizację za pomocą portalu lub interfejsu API REST. Podczas przetwarzania modeli, należy wykonać synchronizację, więc aktualizacje są synchronizowane między repliki zapytania.

### <a name="in-azure-portal"></a>W portalu Azure

W **omówienie** > modelu > **modelu Synchronizuj**.

![Suwak skalowalnego w poziomie](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST
Użyj **synchronizacji** operacji.

#### <a name="synchronize-a-model"></a>Synchronizacji modelu   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Pobierz stan synchronizacji  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Przed rozpoczęciem korzystania z programu PowerShell, [Zainstaluj lub zaktualizuj moduł najnowsze AzureRM](https://github.com/Azure/azure-powershell/releases). 

Aby ustawić liczbę replik zapytanie, należy użyć [AzureRmAnalysisServicesServer zestawu](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Określ opcjonalny `-ReadonlyReplicaCount` parametru.

Aby uruchomić synchronizację, należy użyć [AzureAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>Połączenia

Na stronie Przegląd serwera istnieją dwie nazwy serwera. Jeśli jeszcze nie skonfigurowano skalowalnego w poziomie serwera, obie nazwy serwera działa w ten sam. Po skonfigurowaniu skalowalnego w poziomie serwera, należy określić nazwę serwera odpowiedniej w zależności od typu połączenia. 

Dla połączeń klienckich przez użytkownika końcowego, takich jak Power BI Desktop, Excel i niestandardowych aplikacji, użyj **nazwy serwera**. 

SSMS, narzędzi SSDT i parametry połączenia w programie PowerShell, użyj funkcji platformy Azure, aplikacji i obiektach AMO, **nazwy serwera zarządzania**. Nazwa serwera zarządzania obejmuje specjalnego `:rw` kwalifikator (odczytu i zapisu). Wszystkie operacje przetwarzania odbywa się na serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informacje pokrewne

[Metryki serwera monitora](analysis-services-monitor.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md) 

