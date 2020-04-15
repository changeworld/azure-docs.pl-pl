---
title: Opis przechowywania danych w danym środowisku — usługa Azure Time Series Insight | Dokumenty firmy Microsoft
description: W tym artykule opisano dwa ustawienia, które kontrolują przechowywanie danych w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380686"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Opis przechowywania danych w usłudze Azure Time Series Insights

W tym artykule opisano dwa ustawienia podstawowe, które wpływają na przechowywanie danych w środowisku usługi Azure Time Series Insights.

## <a name="video"></a>Film wideo

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Poniższy film zawiera podsumowanie przechowywania danych usługi Time Series Insights i sposobu planowania.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Każde ze środowisk usługi Azure Time Series Insights ma ustawienie, które kontroluje **czas przechowywania danych.** Wartość obejmuje od 1 do 400 dni. Dane są usuwane na podstawie pojemności magazynu środowiska lub czasu przechowywania, w zależności od tego, co nastąpi wcześniej.

Ponadto środowisko usługi Azure Time Series Insights ma **limit magazynu przekroczone** ustawienie zachowania. Kontroluje ruch przychodzący i przeczyścić zachowanie po osiągnięciu maksymalnej pojemności środowiska. Istnieją dwa zachowania do wyboru podczas konfigurowania:

- **Czyszczenie starych danych** (domyślnie)  
- **Wstrzymanie przychywania się**

> [!NOTE]
> Domyślnie podczas tworzenia nowego środowiska przechowywanie jest skonfigurowane do **przeczyszczania starych danych**. To ustawienie można przełączać w razie potrzeby po czasie tworzenia za pomocą witryny Azure portal, na stronie **Konfigurowanie** środowiska usługi Time Series Insights środowiska.
> * Aby uzyskać informacje dotyczące konfigurowania zasad przechowywania, zobacz [Konfigurowanie przechowywania w aplikacji Time Series Insights](time-series-insights-how-to-configure-retention.md).

Obie zasady przechowywania danych są opisane bardziej szczegółowo poniżej.

## <a name="purge-old-data"></a>Czyszczenie starych danych

- **Przeczyść stare dane** jest ustawieniem domyślnym dla środowisk usługi Azure Time Series Insights.  
- **Przeczyść stare dane** jest preferowane, gdy użytkownicy chcą zawsze mieć swoje *najnowsze dane* w środowisku usługi Time Series Insights.
- Ustawienie **Przeczyść stare dane** *czyści* dane po osiągnięciu limitów środowiska (czas przechowywania, rozmiar lub liczba, w zależności od tego, co nastąpi wcześniej). Przechowywanie jest domyślnie ustawione na 30 dni.
- Najstarsze połkniętych danych jest usuwany pierwszy ("First In First Out" podejście).

### <a name="example-one"></a>Przykład jeden

Rozważmy przykładowe środowisko z zachowaniem przechowywania **Kontynuuj ruch przychodzący i czyścij stare dane:**

**Czas przechowywania danych** jest ustawiony na 400 dni. **Pojemność** jest ustawiona na jednostkę S1, która zawiera 30 GB całkowitej pojemności. Załóżmy, że dane przychodzące gromadzą się średnio do 500 MB każdego dnia. To środowisko może zachować tylko 60 dni wartości danych, biorąc pod uwagę szybkość danych przychodzących, ponieważ maksymalna pojemność jest osiągana po 60 dniach. Dane przychodzące gromadzą się jako: 500 MB każdego dnia x 60 dni = 30 GB.

W 61 dniu środowisko pokazuje najświeższe dane, ale czyści najstarsze dane, starsze niż 60 dni. Przeczyszczanie sprawia, że miejsce na nowe przesyłanie strumieniowe danych w, dzięki czemu nowe dane mogą być nadal badane. Jeśli użytkownik chce zachować dane dłużej, mogą zwiększyć rozmiar środowiska, dodając dodatkowe jednostki lub wypychać mniej danych.  

### <a name="example-two"></a>Przykład dwa

Należy wziąć pod uwagę środowisko skonfigurowane zachowanie przechowywania **Kontynuuj ruch przychodzący i przeczyść stare dane**. W tym przykładzie **czas przechowywania danych** jest ustawiony na niższą wartość 180 dni. **Pojemność** jest ustawiona na jednostkę S1, która zawiera 30 GB całkowitej pojemności. Aby przechowywać dane przez pełne 180 dni, dzienny ruch przychodzący nie może przekraczać 0,166 GB (166 MB) dziennie.  

Za każdym razem, gdy dzienny współczynnik transferu danych przychodzących w tym środowisku przekracza 0,166 GB dziennie, dane nie mogą być przechowywane przez 180 dni, ponieważ niektóre dane są czyszczone. Należy wziąć pod uwagę to samo środowisko podczas zajętości czasu. Załóżmy, że współczynnik transferu ruchu przychodzącego środowiska może wzrosnąć do średnio 0.189 GB dziennie. W tym napiętym przedziale czasowym przechowywane są około 158 dni danych (30GB/0.189 = 158.73 dni przechowywania). Ten czas jest mniejsza niż żądany przedział czasu przechowywania danych.

## <a name="pause-ingress"></a>Wstrzymanie przychywania się

- Ustawienie **Wstrzymaj ruch przychodzący** ma na celu zapewnienie, że dane nie zostaną usunięte, jeśli limity rozmiaru i liczby zostaną osiągnięte przed okresem przechowywania.  
- **Wstrzymaj ruch przychodzący** zapewnia użytkownikom dodatkowy czas na zwiększenie pojemności środowiska przed przeczyszczeniem danych z powodu naruszenia okresu przechowywania.
- Pomaga chronić przed utratą danych, ale może stworzyć możliwość utraty najnowszych danych, jeśli ruch przychodzący jest wstrzymany poza okres przechowywania źródła zdarzeń.
- Jednak po osiągnięciu maksymalnej pojemności środowiska środowisko wstrzymuje transfer danych, dopóki nie wystąpią następujące dodatkowe akcje:

   - Zwiększasz maksymalną pojemność środowiska, aby dodać więcej jednostek skalowania, zgodnie z opisem w [jak skalować środowisko Usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Okres przechowywania danych jest osiągnięty, a dane są czyszczane, co obniża poniżej maksymalnej pojemności środowiska.

### <a name="example-three"></a>Przykład trzeci

Należy wziąć pod uwagę środowisko z zachowaniem przechowywania skonfigurowanym do **wstrzymywania transferu danych przychodzących**. W tym przykładzie **okres przechowywania danych** jest skonfigurowany do 60 dni. **Pojemność** jest ustawiona na trzy (3) jednostki S1. Załóżmy, że to środowisko ma przychodzących danych 2 GB każdego dnia. W tym środowisku ruch przychodzący jest wstrzymany po osiągnięciu maksymalnej pojemności.

W tym czasie środowisko pokazuje ten sam zestaw danych, dopóki ruch przychodzący zostanie wznowiony lub dopóki nie zostanie **włączona opcja continue ingress** (która przeczyściłaby starsze dane, aby zrobić miejsce dla nowych danych).

Po wznowieniu transferu przychodzącego:

- Przepływy danych w kolejności odebranej przez źródło zdarzeń
- Zdarzenia są indeksowane na podstawie ich sygnatury czasowej, chyba że zostały przekroczone zasady przechowywania w źródle zdarzeń. Aby uzyskać więcej informacji na temat konfiguracji przechowywania źródła zdarzeń, [Często zadawane pytania dotyczące centrów zdarzeń](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Należy ustawić alerty, aby zapewnić powiadomienie, aby uniknąć wstrzymania transferu ruchu przychodzącego. Utrata danych jest możliwe, ponieważ domyślne przechowywanie jest 1 dzień dla źródeł zdarzeń platformy Azure. W związku z tym po wstrzymaniu transferu danych przychodzących prawdopodobnie utracisz najnowsze dane, chyba że zostanie podjęta dodatkowa akcja. Należy zwiększyć pojemność lub przełączyć zachowanie na **Przeczyszczanie starych danych,** aby uniknąć możliwości utraty danych.

W centrum zdarzeń, których dotyczy problem, należy rozważyć dostosowanie **właściwości Przechowywanie wiadomości,** aby zminimalizować utratę danych po wystąpieniu wstrzymania transferu danych przychodzących w usłudze Time Series Insights.

[![Przechowywanie komunikatów w centrum zdarzeń.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Jeśli w źródle zdarzeń nie są`timeStampPropertyName`skonfigurowane żadne właściwości ( ), usługa Time Series Insights domyślnie sygnatura czasowa przybycia do Centrum zdarzeń jest skonfigurowana jako oś X. Jeśli `timeStampPropertyName` jest skonfigurowany jako coś innego, środowisko `timeStampPropertyName` szuka skonfigurowane w pakiecie danych, gdy zdarzenia są analizowane.

Przeczytaj [jak skalować środowisko usługi Time Series Insights,](time-series-insights-how-to-scale-your-environment.md) aby skalować środowisko, aby pomieścić dodatkową pojemność lub zwiększyć długość przechowywania.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat konfigurowania lub zmieniania ustawień przechowywania danych, zapoznaj [się z przeglądem Konfigurowanie przechowywania w aplikacji Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej o [ograniczaniu opóźnień w usłudze Azure Time Series Insights.](time-series-insights-environment-mitigate-latency.md)
