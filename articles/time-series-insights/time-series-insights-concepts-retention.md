---
title: Omówienie przechowywania danych w środowisku usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano dwa ustawienia, które kontrolują przechowywanie danych w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e3336df30873b40d2b8a464d1f866b524f76776d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236993"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Omówienie przechowywania danych w usłudze Azure Time Series Insights

W tym artykule opisano dwa ustawienia, które mają wpływ na przechowywanie danych w środowisku usługi Azure Time Series Insights.

## <a name="video"></a>Połączenia wideo

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Poniższy klip wideo zawiera podsumowanie przechowywanie danych usługi Time Series Insights oraz sposobu planowania dla niego.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Każdy z środowiska Azure Time Series ma ustawienie sterujące **czas przechowywania danych**. Wartość rozciąga się od 1 do 400 dni. Dane zostaną usunięte, na podstawie pojemności magazynu w środowisku lub okres przechowywania, osiągnięta jako pierwsza.

Ponadto w środowisku Azure Time Series zawierającym **zachowanie przekroczony limit magazynu** ustawienie. Ona kontroluje ruch przychodzący i przeczyścić zachowanie, gdy zostanie osiągnięta maksymalna wydajność środowiska. Istnieją dwa zachowania do wyboru podczas konfigurowania go:

- **Usuwanie starych danych** (ustawienie domyślne)  
- **Wstrzymaj transferu danych przychodzących**

> [!NOTE]
> Domyślnie podczas tworzenia nowego środowiska, przechowywania jest skonfigurowany do **wyczyścić stare dane**. To ustawienie może być przełączane w razie potrzeby po przy użyciu witryny Azure portal w czasie jego tworzenia **Konfiguruj** strony środowiska usługi Time Series Insights.

Informacje na temat przechowywania zachowania przełączania można przejrzeć [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).

Porównaj zachowania w zakresie przechowywania danych:

## <a name="purge-old-data"></a>Usuwanie starych danych

- To zachowanie jest zachowaniem domyślnym dla środowiska usługi Time Series Insights.  
- To zachowanie jest preferowane w przypadku, gdy użytkownicy chcą zobaczyć zawsze ich *najbardziej aktualne dane* w swoim środowisku usługi Time Series Insights.
- To zachowanie *Przeczyszcza* danych środowiska po osiągnięciu limitu (czas przechowywania, rozmiar lub count, osiągnięta jako pierwsza). Przechowywanie jest domyślnie do 30 dni.
- Najstarszy pozyskiwanych danych są przeczyszczane pierwszy (podejście FIFO).

### <a name="example-one"></a>Przykład jednego

Należy wziąć pod uwagę przykładowe środowisko z zachowaniem przechowywania **nadal transferu danych przychodzących i czyszczenie starych danych**:

**Czas przechowywania danych** jest ustawiona na 400 dni. **Pojemność** jest ustawiona na jednostki S1, co zawiera 30 GB łączna pojemność.   Załóżmy, że dla ruchu przychodzącego dane są gromadzone do 500 MB dziennie średnio. To środowisko można zachować tylko 60 dni ważności danych, biorąc pod uwagę współczynnik danych przychodzących, ponieważ osiągnięto maksymalną pojemność w ciągu 60 dni. Dane przychodzące są gromadzone jako: 500 MB każdy dzień x 60 dni = 30 GB.

W dniu 61st środowiska pokazuje zawierać najświeższe dane, ale Przeczyszcza najstarsze dane starsze niż 60 dni. Przeczyszczanie sprawia, że miejsce dla nowych danych przesyłania strumieniowego, tak aby nowe dane mogą w dalszym ciągu być eksplorowana. Jeśli użytkownik zamierza przechowywać dane dłużej, można zwiększyć rozmiar środowiska, dodając usługę o kolejne jednostki lub wypchnąć mniejszej ilości danych.  

### <a name="example-two"></a>Przykład 2

Należy wziąć pod uwagę środowisko skonfigurowane również zachowanie przechowywania **nadal transferu danych przychodzących i czyszczenie starych danych**. W tym przykładzie **czas przechowywania danych** jest ustawiona na niższą wartość 180 dni. **Pojemność** jest ustawiona na jednostki S1, co zawiera 30 GB łączna pojemność. Aby można było przechowywać dane przez pełne 180 dni, dzienny ruch przychodzący nie może przekraczać 0.166 GB (166 MB) na dzień.  

Zawsze, gdy to środowisko dzienny transfer przychodzący przekroczy 0.166 GB dziennie, dane nie mogą być przechowywane przez 180 dni, ponieważ pobiera przeczyścić dane. Należy wziąć pod uwagę tego samego środowiska zajęty horyzoncie czasowym. Załóżmy, że szybkość transferu danych przychodzących środowiska może wzrosnąć do średniej 0.189 GB dziennie. W tym okresie zajęty 158 dni danych zostaną zachowane (30GB/0.189 = 158,73 dni przechowywania). Ten czas jest mniejszy niż przedział czasu przechowywania żądanych danych.

## <a name="pause-ingress"></a>Wstrzymaj transferu danych przychodzących

- **Wstrzymać ruch przychodzący** ustawienie zaprojektowano w celu zapewnienia dane nie są przeczyszczane, jeśli osiągnięciu limitów rozmiaru i liczby przed ich okresu przechowywania.  
- **Wstrzymaj ruch przychodzący** zapewnia dodatkowy czas dla użytkowników zwiększyć jej pojemność, ich środowiska, zanim dane są przeczyszczane ze względu na naruszenie okres przechowywania danych
- Zapewnia ochronę przed utratą danych, ale można utworzyć szansy sprzedaży związane z utratą najnowszych danych, jeśli ruch przychodzący jest wstrzymana, po upływie okresu przechowywania źródła zdarzeń.
- Jednak po osiągnięciu maksymalnej pojemności środowiska środowiska wstrzymuje transferu danych przychodzących, dopóki nie są wykonywane następujące akcje dodatkowe:

   - Zwiększ maksymalną pojemność środowiska, aby dodać kolejne jednostki skalowania zgodnie z opisem w [sposób skalowania środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Okres przechowywania danych, a dane są przeczyszczane dzięki środowisku poniżej maksymalnej pojemności.

### <a name="example-three"></a>Przykład 3

Należy wziąć pod uwagę środowisko, z zachowaniem przechowywania skonfigurowane do **wstrzymać ruch przychodzący**. W tym przykładzie **okres przechowywania danych** jest skonfigurowany do 60 dni. **Pojemność** jest ustawiona na trzy (3) jednostek S1. Załóżmy, że w tym środowisku obowiązują transferu danych przychodzących, 2GB danych dziennie. W tym środowisku ruch przychodzący jest wstrzymany, po osiągnięciu maksymalnej pojemności.

W tym czasie środowiska pokazuje tego samego zestawu danych do transferu danych przychodzących wznawia lub do chwili **nadal ruch przychodzący** jest włączona (który będzie przeczyścić starszych danych, aby zwolnić miejsce dla nowych danych).

Po wznowieniu działania transferu danych przychodzących:

- Dane przepływają w kolejności, w której zostało przesłane przez źródło zdarzenia
- Zdarzenia są indeksowane w oparciu o ich sygnatury czasowej, chyba że przekroczono zasady przechowywania w źródle zdarzenia. Aby uzyskać więcej informacji na temat konfiguracji przechowywania źródła zdarzeń [Event Hubs często zadawane pytania](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Należy ustawić alerty, aby powiadomić w celu uniknięcia ruch przychodzący jest wstrzymane. Możliwa jest utrata danych, ponieważ domyślny okres przechowywania będzie 1 dzień dla źródeł zdarzeń platformy Azure. W związku z tym gdy ruch przychodzący jest wstrzymany, prawdopodobnie zostaną utracone najnowsze dane, chyba że zostaną podjęte dodatkowe działania. Należy zwiększyć pojemność, lub zachowanie, aby przełączyć **wyczyścić stare dane** w celu uniknięcia potencjalnych utraty danych.

W których to dotyczy usługi Event Hubs, należy wziąć pod uwagę dostosowywanie **okres przechowywania wiadomości** właściwość, aby zminimalizować ryzyko utraty danych, gdy wystąpi Wstrzymaj transferu danych przychodzących usługi Time Series Insights.

[![Przechowywanie komunikatów Centrum zdarzeń.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Jeśli skonfigurowano żadnych właściwości źródła zdarzeń (`timeStampPropertyName`), usługi Time Series Insights, wartość domyślna to sygnatura czasowa odebrania w Centrum zdarzeń jako osi x. Jeśli `timeStampPropertyName` jest skonfigurowany jako coś innego, środowisko szuka skonfigurowanych `timeStampPropertyName` w pakiecie danych, gdy zdarzenia są analizowane.

Jeśli chcesz skalować środowiska do uwzględnienia dodatkowej pojemności lub zwiększyć okres przechowywania, zobacz [sposób skalowania środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md) Aby uzyskać więcej informacji.  

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o konfigurowaniu lub zmiana ustawień przechowywania danych, przejrzyj [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).
