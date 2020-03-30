---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334852"
---
**Ilość i przechowywanie danych** 

| Warstwa | Limit na dzień | Przechowywanie danych | Komentarz |
|:---|:---|:---|:---|
| Bieżąca warstwa cenowa na GB<br>(wprowadzony w kwietniu 2018 r.) | Bez ograniczeń | 30 - 730 dni | Przechowywanie danych po 31 dniach jest dostępne za dodatkową opłatą. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsze warstwy bezpłatne<br>(wprowadzona w kwietniu 2016 r.) | 500 MB | 7 dni | Gdy obszar roboczy osiągnie limit 500 MB dziennie, przesyłanie danych zatrzymuje się i wznawia na początku następnego dnia. Dzień jest oparty na czasie UTC. Należy zauważyć, że dane zebrane przez usługę Azure Security Center nie są uwzględniane w tym limicie 500 MB dziennie i będą nadal zbierane powyżej tego limitu.  |
| Starsza warstwa autonomiczna na GB<br>(wprowadzona w kwietniu 2016 r.) | Bez ograniczeń | 30 do 730 dni | Przechowywanie danych po 31 dniach jest dostępne za dodatkową opłatą. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsza wersja na węzeł (OMS)<br>(wprowadzona w kwietniu 2016 r.) | Bez ograniczeń | 30 do 730 dni | Przechowywanie danych po 31 dniach jest dostępne za dodatkową opłatą. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsza warstwa standardowa | Bez ograniczeń | 30 dni  | Nie można regulować retencji |
| Starsza warstwa Premium | Bez ograniczeń | 365 dni  | Nie można regulować retencji |

**Liczba obszarów roboczych na subskrypcję.**

| Warstwa cenowa    | Limit obszaru roboczego | Komentarze
|:---|:---|:---|
| Warstwa Bezpłatna  | 10 | Tego limitu nie można zwiększyć. |
| Wszystkie pozostałe poziomy | Bez ograniczeń | Jesteś ograniczony przez liczbę zasobów w grupie zasobów i liczbę grup zasobów na subskrypcję. |

**Portal Azure**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwracanych przez kwerendę dziennika | 10 000 | Zmniejsz wyniki przy użyciu zakresu kwerendy, zakresu czasu i filtrów w kwerendzie. |


**Interfejs API modułu zbierającego dane**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalny rozmiar pojedynczego słupka | 30 MB | Podziel większe woluminy na wiele wpisów. |
| Maksymalny rozmiar wartości pól  | 32 KB | Pola dłuższe niż 32 KB są obcinane. |

**Interfejs API wyszukiwania**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwróconych w jednej kwerendzie | 500 000 | |
| Maksymalny rozmiar zwracanych danych | 64 000 000 bajtów (~61 MiB)| |
| Maksymalny czas pracy kwerendy | 10 minut | Zobacz [limity czasu, aby](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) uzyskać szczegółowe informacje.  |
| Maksymalna stawka żądania | 200 żądań na 30 sekund na adres IP użytkownika lub klienta AAD | Szczegółowe informacje można znaleźć w [zakresie limitów szybkości.](https://dev.loganalytics.io/documentation/Using-the-API/Limits) |

**Ogólne limity obszaru roboczego**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba kolumn w tabeli         | 500 | |
| Maksymalna liczba znaków dla nazwy kolumny | 500 | |
| Eksport danych | Obecnie niedostępne | Użyj funkcji platformy Azure lub aplikacji logiki do agregowania i eksportowania danych. | 

**Szybkość pozyskiwania danych**


Usługa Azure Monitor to usługa danych na dużą skalę, która obsługuje tysiące klientów wysyłających terabajty danych każdego miesiąca w rosnącym tempie. Domyślny limit szybkości pozyskiwania woluminów dla danych wysyłanych z zasobów platformy Azure przy użyciu [ustawień diagnostycznych](../articles/azure-monitor/platform/diagnostic-settings.md) wynosi około **6 GB/min** na obszar roboczy. Jest to wartość przybliżona, ponieważ rzeczywisty rozmiar może się różnić między typami danych w zależności od długości dziennika i jego współczynnika kompresji. Ten limit nie dotyczy danych wysyłanych z agentów lub [interfejsu API modułu zbierającego dane](../articles/azure-monitor/platform/data-collector-api.md).

Jeśli wysyłasz dane z wyższą szybkością do jednego obszaru roboczego, niektóre dane są odrzucane, a zdarzenie jest wysyłane do tabeli *Operacja* w obszarze roboczym co 6 godzin, podczas gdy próg jest nadal przekraczany. Jeśli wolumin pozyskiwania nadal przekracza limit szybkości lub oczekujesz, że osiągniesz go w najbliższym czasie, możesz poprosić o zwiększenie obszaru roboczego, otwierając żądanie pomocy technicznej.
 
Aby otrzymywać powiadomienia o takim zdarzeniu w obszarze roboczym, należy utworzyć [regułę alertu dziennika](../articles/azure-monitor/platform/alerts-log.md) przy użyciu następującej kwerendy z logiką alertu na podstawie liczby wyników na tarce niż zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>W zależności od tego, jak długo korzystasz z usługi Log Analytics, możesz mieć dostęp do starszych warstw cenowych. Dowiedz się więcej o [starszych warstwach cenowych usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 