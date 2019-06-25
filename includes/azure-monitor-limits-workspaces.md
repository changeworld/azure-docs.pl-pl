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
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305314"
---
**Kolekcja ilości i przechowywania danych** 

| Warstwa | Ograniczenia na dzień | Przechowywanie danych | Komentarz |
|:---|:---|:---|:---|
| Warstwa cenowa bieżącego na GB<br>(zostanie wprowadzony kwietnia 2018 r.) | Bez ograniczeń | 30 - 730 dni | Przechowywanie danych dłużej niż 31 dni jest dostępna dla dodatkowych opłat. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsze bezpłatnymi warstwami<br>(zostanie wprowadzony kwietnia 2016) | 500 MB | 7 dni | Obszar roboczy osiągnie 500 MB dziennie, limit na, pozyskiwania danych zatrzyma się i zostanie wznowiona na początku następnego dnia. Dzień jest oparty na czasie UTC. Należy pamiętać, że dane zebrane przez usługę Azure Security Center nie są uwzględnione w tym 500 MB dziennie, limit na i będą w dalszym ciągu być zbierane powyżej tego limitu.  |
| Starsze warstwy autonomicznego na GB<br>(zostanie wprowadzony kwietnia 2016) | Bez ograniczeń | 30 do 730 dni | Przechowywanie danych dłużej niż 31 dni jest dostępna dla dodatkowych opłat. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsza wersja na węzeł (OMS)<br>(zostanie wprowadzony kwietnia 2016) | Bez ograniczeń | 30 do 730 dni | Przechowywanie danych dłużej niż 31 dni jest dostępna dla dodatkowych opłat. Dowiedz się więcej o cenach usługi Azure Monitor. |
| Starsze w warstwie standardowa | Bez ograniczeń | 30 dni  | Nie można go zmienić przechowywania |
| Starsze warstwy Premium | Bez ograniczeń | 365 dni  | Nie można go zmienić przechowywania |

**Liczba obszarów roboczych na subskrypcję.**

| Warstwa cenowa    | Limit obszaru roboczego | Komentarze
|:---|:---|:---|
| Warstwa bezpłatna  | 10 | Nie można zwiększyć ten limit. |
| Innej warstwy | Bez ograniczeń | Ograniczeniem liczby zasobów w grupie zasobów i liczba grup zasobów na subskrypcję. |

**Azure Portal**

| Category | Limits | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwróconych przez zapytanie dziennika | 10 000 | Ogranicz wyniki za pomocą zakres kwerendy, zakres czasu i filtrów w zapytaniu. |


**Interfejs API modułu zbierającego dane**

| Category | Limits | Komentarze |
|:---|:---|:---|
| Maksymalny rozmiar pojedynczego wpisu | 30 MB | Dziel większe woluminy na wiele wpisów. |
| Maksymalny rozmiar wartości pól  | 32 KB | Pola dłuższe niż 32 KB są obcinane. |

**Interfejs API wyszukiwania**

| Category | Limits | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwracanych dla danych niezagregowanych | 5,000 | |
| Maksymalna liczba rekordów dla danych zagregowanych | 500,000 | Zagregowane dane to wyszukiwanie obejmujące `summarize` polecenia. |
| Maksymalny rozmiar danych zwróconych | 64,000,000 bajtów (~ 61 MiB)| |
| Zapytanie maksymalny czas działania | 10 minut | Zobacz [przekroczeń limitu czasu](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) Aby uzyskać szczegółowe informacje.  |
| Maksymalna liczba żądań | 200 żądań na 30 sekund dla adresu IP klienta lub użytkownika usługi AAD | Zobacz [limity szybkości](https://dev.loganalytics.io/documentation/Using-the-API/Limits) Aby uzyskać szczegółowe informacje. |

**Limity ogólne obszaru roboczego**

| Category | Limits | Komentarze |
|:---|:---|:---|
| Maksymalna liczba kolumn w tabeli         | 500 | |
| Maksymalna liczba znaków dla nazwy kolumny | 500 | |
| Regiony z wydajnością | Środkowo-zachodnie stany USA | Obecnie nie można utworzyć nowy obszar roboczy w tym regionie, ponieważ znajduje się na limit pojemności tymczasowych. Ten limit jest planowana skierowanych do końca września maja 2019 r. |
| Eksportowanie danych | nie jest obecnie dostępna | Użyj funkcji platformy Azure lub aplikację logiki Umożliwia agregowanie danych i eksportowanie. | 

>[!NOTE]
>W zależności od tego, jak długo używasz usługi Log Analytics mogą mieć dostęp do starszych warstw cenowych. Dowiedz się więcej o [starszej wersji usługi Log Analytics warstw cenowych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 