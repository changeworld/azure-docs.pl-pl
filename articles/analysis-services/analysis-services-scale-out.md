---
title: Usługa Azure Analysis Services skalowalnego w poziomie | Dokumentacja firmy Microsoft
description: Replikacja serwerów usług Azure Analysis Services za pomocą skalowalnego w poziomie
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4cb7b165311f57fadd63770646907ddfc0378844
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445024"
---
# <a name="azure-analysis-services-scale-out"></a>Usługa Azure Analysis Services skalowalnego w poziomie

Za pomocą skalowalnego w poziomie, zapytań klienta mogą być dystrybuowane między wieloma *replikami zapytania* w puli zapytania, zmniejszając czasy odpowiedzi podczas obciążeń związanych z zapytaniami wysokiej. Można też oddzielić przetwarzania od puli zapytania, zapewnienie, że zapytań klienta nie dotkną operacji przetwarzania. Skalowalny w poziomie można skonfigurować w witrynie Azure portal lub za pomocą interfejsu API REST usługi Analysis Services.

## <a name="how-it-works"></a>Jak to działa

W przypadku wdrożenia serwera typowe jeden serwer służy jako zarówno serwera przetwarzania i serwera zapytania. Jeśli liczba zapytań klienta względem modele na serwerze przekracza zapytania przetwarzania jednostek (jednostek QPU) dla planu danych na serwerze lub modelu przetwarzanie odbywa się w tym samym czasie jako obciążeń związanych z zapytaniami o wysokiej, może obniżyć wydajność. 

Za pomocą skalowalnego w poziomie można utworzyć pulę zapytania z nawet siedmioma dodatkowymi replikami zapytania (łącznie ośmioma, wliczając serwer). Możesz skalować liczbę replik zapytań do spełnienia wymagań jednostek QPU przez czas krytycznych i w dowolnym momencie możesz oddzielić serwer przetwarzania od puli zapytania. Wszystkie repliki zapytania są tworzone w tym samym regionie co serwer.

Niezależnie od liczby replik zapytań, znajdującym się w puli zapytania obciążeń przetwarzania nie są dystrybuowane między replikami zapytań. Pojedynczy serwer służy jako serwer przetwarzania. Repliki zapytania służyć tylko zapytania względem modeli synchronizowane między każdej replice w puli zapytania. 

Po zakończeniu operacji przetwarzania odbywa się synchronizacja między serwerem przetwarzania a serwerami repliki zapytania. Automatyzacja operacji przetwarzania, należy skonfigurować operacji synchronizacji po pomyślnym zakończeniu operacji przetwarzania. Można przeprowadzić synchronizację ręcznie w portalu lub przy użyciu programu PowerShell lub interfejsu API REST.

> [!NOTE]
> Skalowalny w poziomie jest dostępna dla serwerów warstwy cenowej standardowa. Każdej repliki zapytania jest rozliczana przy użyciu stawki stosowanej jako serwer.

> [!NOTE]
> Skalowalny w poziomie nie zwiększa ilość dostępnej pamięci na serwerze. Aby zwiększyć ilość pamięci, musisz uaktualnić swój plan.

## <a name="region-limits"></a>Limity regionu

Liczba replik zapytań, które można skonfigurować są ograniczone według regionu, w której znajduje się serwer. Poniższe limity mają zastosowanie:

|Region  |Maksymalna liczba replik  |
|---------|---------|
|Wschodnie stany USA 2    |    7     |
|Środkowo-zachodnie stany USA     |    7     |
|Europa Zachodnia     |    7     |
|Zachodnie stany USA     |     7    |
|Środkowe stany USA     |     3    |
|Azja Południowo-Wschodnia    |     3    |
|Inne regiony  |   1    |



## <a name="monitor-qpu-usage"></a>Użycie jednostek QPU monitora

 Aby ustalić, czy skalowalnego w poziomie serwera jest niezbędne, serwer w witrynie Azure portal należy monitorować za pomocą metryk. Jeśli Twoje QPU regularnie wydłużyć, oznacza to, że liczba zapytań dotyczących modeli przekracza limit jednostek QPU dla planu. Metryka długość kolejki zadania puli zapytania zwiększa także dostępne QPU przekroczenia liczby zapytań w kolejce puli wątków zapytań. Aby dowiedzieć się więcej, zobacz [monitorowanie metryk serwera](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Skonfiguruj skalowalny w poziomie

### <a name="in-azure-portal"></a>W witrynie Azure portal

1. W portalu, kliknij przycisk **skalowalnego w poziomie**. Wybierz liczbę serwerów replik zapytań za pomocą suwaka. Liczba replik, które wybierzesz jest oprócz istniejącego serwera.

2. W **oddziel serwer przetwarzania od puli zapytań**, wybierz opcję Tak, aby wykluczyć serwer przetwarzania z serwerów z zapytania.

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
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Przed rozpoczęciem korzystania z programu PowerShell, [Instalowanie lub aktualizowanie najnowszy moduł AzureRM](https://github.com/Azure/azure-powershell/releases). 

Aby ustawić liczba replik zapytań, należy użyć [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Określ opcjonalne `-ReadonlyReplicaCount` parametru.

Aby uruchomić synchronizacji, użyj [AzureAnalysisServicesInstance synchronizacji](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>Połączenia

Na stronie przeglądu serwera istnieją dwie nazwy serwera. Jeśli nie skonfigurowano jeszcze skalowalnego w poziomie serwera, obie nazwy serwera działać tak samo. Po skonfigurowaniu skalowalnego w poziomie serwera, należy określić nazwę odpowiedniego serwera, w zależności od typu połączenia. 

Dla połączeń klienckich użytkowników końcowych, takich jak Power BI Desktop, Excel i aplikacje niestandardowe, użyj **nazwy serwera**. 

SSMS, SSDT i parametry połączenia w programie PowerShell, użyj aplikacji funkcji platformy Azure i AMO, **nazwa serwera zarządzania**. Nazwa serwera zarządzania obejmuje specjalny `:rw` kwalifikator (odczyt zapis). Wszystkie operacje przetwarzania są wykonywane na serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informacje pokrewne

[Monitorowanie metryk serwera](analysis-services-monitor.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md) 

