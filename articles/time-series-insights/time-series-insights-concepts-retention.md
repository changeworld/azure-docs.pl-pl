---
title: Zrozumienie przechowywanie danych w środowisku Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano dwa ustawienia, które kontrolują przechowywanie danych w środowisku Azure czas serii Insights.
ms.service: time-series-insights
services: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: ad0cbf38a1a32632869c490c5a46796eaa3884f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652454"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Zrozumienie przechowywanie danych w czasie serii Insights
W tym artykule opisano dwa ustawienia, które mają wpływ na przechowywanie danych w środowisku Insights serii czasu (TSI).

Każde środowisko TSI ma ustawienie, która kontroluje **czas przechowywania danych**. Wartość jest liczony od 1 do 400. Dane zostaną usunięte, oparte na środowisku pojemności lub przechowywania okresem magazynu (1-400), zależnie od zostanie osiągnięty jako pierwszy.

Każde środowisko TSI ma dodatkowe ustawienie **zachowanie przekroczony limit magazynu**. To ustawienie określa przychodzące i przeczyszczania zachowanie po osiągnięciu maksymalnej pojemności środowisku. Istnieją dwa zachowania do wyboru:
- **Przeczyść stare dane** (ustawienie domyślne)  
- **Transfer danych przychodzących Wstrzymaj**

> [!NOTE]
> Domyślnie podczas tworzenia nowego środowiska, przechowywania jest skonfigurowany do **przeczyścić stare dane**. To ustawienie można przełączać w razie potrzeby po przy użyciu portalu Azure, w czasie tworzenia **Konfiguruj** strony TSI środowiska.

Aby uzyskać informacje dotyczące przechowywania zachowania przełączania, przejrzyj [konfigurowania przechowywania w czasie serii Insights](time-series-insights-how-to-configure-retention.md).

Porównania zachowania przechowywania danych:

## <a name="purge-old-data"></a>Przeczyść starych danych
- To zachowanie jest domyślne zachowanie dla środowisk TSI i dowody tych samych środowisk TSI zachowanie wystawił, ponieważ została uruchomiona w publicznej wersji zapoznawczej.  
- To zachowanie jest preferowana, kiedy użytkownik chce zawsze widzieć ich *najnowszych danych* w swoim środowisku TSI. 
- To zachowanie *Przeczyszcza* danych środowiska po osiągnięciu limitu (czas przechowywania, rozmiaru lub liczby zależności zostanie osiągnięty jako pierwszy). Przechowywania jest domyślnie 30 dni. 
- Najstarsze dane pozyskiwane są przeczyszczane pierwszy (metoda FIFO).

### <a name="example-1"></a>Przykład 1:
Rozważ przykład środowisko z zachowaniem przechowywania **kontynuować transfer danych przychodzących i usuwania starych danych**: W tym przykładzie **czas przechowywania danych** wynosi 400 dni. **Pojemność** ustawiono S1 jednostki, która zawiera 30 GB całkowitej pojemności.   Załóżmy, że przychodzących danych akumuluje do 500 MB każdego dnia średnia. To środowisko tylko można zachować 60 dni ważności danych podana liczba przychodzących danych, ponieważ osiągnięto maksymalną pojemność w ciągu 60 dni. Akumuluje przychodzących danych jako: 500 MB każdy dzień x 60 dni = 30 GB. 

W tym przykładzie dnia 61st środowisko zawiera najnowsza dane, ale Przeczyszcza najstarsze dane starsze niż 60 dni. Przeczyszczanie powoduje, że miejsce dla nowych danych przesyłania strumieniowego, dzięki czemu nowe dane mogą w dalszym ciągu eksplorowania. 

Jeśli użytkownik zamierza przechowywać dane dłużej, rozmiar środowiska można zwiększyć przez dodanie dodatkowych jednostek lub wypchnąć mniejszej ilości danych.  

### <a name="example-2"></a>Przykład 2:
Należy wziąć pod uwagę środowisko również skonfigurowane zachowanie przechowywania **kontynuować transfer danych przychodzących i usuwania starych danych**. W tym przykładzie **czas przechowywania danych** ma wartość niższą wartość 180 dni. **Pojemność** ustawiono S1 jednostki, która zawiera 30 GB całkowitej pojemności. Aby przechowywać dane na pełne okres 180 dni, codzienne wejściowych nie może przekraczać 0.166 GB (166 MB) na dzień.  

Zawsze, gdy to środowisko dziennych stawek wejściowych przekracza 0.166 GB na dzień, nie można przechowywać danych na okres 180 dni, ponieważ niektóre dane pobiera wyczyszczone. Należy wziąć pod uwagę tego samego środowiska w czasie zajęty. Załóżmy, że szybkość transferu w środowisku może zwiększyć średni 0.189 GB na dzień. W tym czasie zajęty, zostaną zachowane 158 dni danych (30GB/0.189 = 158.73 dni przechowywania). Ten czas jest mniejsza niż przedział czasu przechowywania żądanych danych.

## <a name="pause-ingress"></a>Transfer danych przychodzących Wstrzymaj
- To zachowanie jest przeznaczona do upewnij się, że dane nie są przeczyszczane, jeśli osiągnięto limity rozmiaru i liczby przed ich okresu przechowywania.  
- Takie zachowanie odznacza się dodatkowy czas do zwiększenia pojemności środowiska przed dane są przeczyszczane z powodu naruszenia okresu przechowywania dla użytkowników
- To zachowanie chroni przed utratą danych, ale tworzy możliwość utraty najnowszych danych wejściowych jest wstrzymana, po upływie okresu przechowywania źródła zdarzeń.
- Jednak po osiągnięciu maksymalnej pojemności środowiska środowiska wstrzymuje danych wejściowych, dopóki wykonywane są dodatkowe akcje: 
   - Można zwiększyć maksymalną pojemność w środowisku. Aby uzyskać więcej informacji, zobacz [sposób skalowania środowiska czasu serii Insights](time-series-insights-how-to-scale-your-environment.md) można dodać więcej jednostek skalowania.
   - Okres przechowywania danych, a dane są przeczyszczane, w związku z tym Przywracanie środowiska poniżej pojemności maksymalnej.

### <a name="example-3"></a>Przykład 3:
Należy wziąć pod uwagę środowisko z zachowaniem przechowywania skonfigurowany tak, aby **wstrzymać wejściowych**. W tym przykładzie **okres przechowywania danych** jest skonfigurowany do 60 dni. **Pojemność** ma ustawioną wartość 3 jednostki S1. Załóżmy, że w tym środowisku ma wejściowych danych 2GB każdego dnia. W tym środowisku transfer danych przychodzących jest wstrzymana, po osiągnięciu maksymalnej pojemności. W tym czasie środowisko zawiera tego samego zestawu danych wejściowych wznawia lub do momentu "continue wejściowych" są włączone (co spowoduje przeczyścić starszych danych, aby zwolnić miejsce dla nowych danych). 

Po wznowieniu działania wejściowych:
- Dane przepływają w kolejności, które zostało przesłane przez źródło zdarzenia
- Zdarzenia są indeksowane zależności od ich znaczników czasu, chyba że przekroczono zasady przechowywania źródła zdarzenia. Aby uzyskać więcej informacji na temat konfiguracji przechowywania źródła zdarzeń [centra zdarzeń — często zadawane pytania](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Należy ustawić alerty, aby powiadomienia w celu uniknięcia transfer danych przychodzących jest wstrzymana. Możliwa jest utrata danych, ponieważ przechowywania domyślna to 1 dzień dla źródeł zdarzeń platformy Azure. W związku z tym gdy transfer danych przychodzących jest wstrzymana, prawdopodobnie utracisz najnowszych danych, chyba że zostaną podjęte dodatkowe działania. Należy zwiększyć pojemność lub przełącznika zachowania do **przeczyścić stare dane** Aby uniknąć potencjalnych utraty danych.

W wpływ na usługi Event Hubs, rozważ dostosowanie **przechowywania wiadomości** właściwość, aby zminimalizować ryzyko utraty danych po Wstrzymaj ruch przychodzący w czasie serii Insights.

![Przechowywanie wiadomości Centrum zdarzeń.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Jeśli właściwości nie są konfigurowane w źródle zdarzeń (timeStampPropertyName), TSI domyślnie sygnatura czasowa przyjęcia w Centrum zdarzeń jako osi x. Jeśli timeStampPropertyName jest skonfigurowany jako inny, środowiska szuka skonfigurowanego timeStampPropertyName w pakiecie danych zdarzenia są analizowania. 

Aby skalować środowiska, aby zmieścił się w jej większą pojemność lub zwiększ długość przechowywania, zobacz [sposób skalowania środowiska czasu serii Insights](time-series-insights-how-to-scale-your-environment.md) Aby uzyskać więcej informacji.  

## <a name="next-steps"></a>Kolejne kroki
Informacje dotyczące przechowywania zachowanie przełączania [konfigurowania przechowywania w czasie serii Insights](time-series-insights-how-to-configure-retention.md).
