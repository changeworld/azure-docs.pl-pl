---
title: Łączenie danych Symantec ICDx z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane usługi Symantec ICDx z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588097"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Podłączanie urządzenia Symantec ICDx 



Łącznik Symantec ICDx umożliwia łatwe łączenie wszystkich dzienników rozwiązań zabezpieczających firmy Symantec z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badań. Zapewnia to lepszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między usługą Symantec ICDx i azure sentinel korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchomiona usługa Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurowanie i podłączanie programu Symantec ICDx 

Firma Symantec ICDx może integrować i eksportować dzienniki bezpośrednio do usługi Azure Sentinel.

1. Otwórz konsolę zarządzania ICDx, aby dodać usługi przesyłania dalej usługi Microsoft Azure Sentinel (Log Analytics).
2. Na pasku nawigacyjnym ICDx kliknij pozycję **Konfiguracja**. 
3. U góry ekranu **Konfiguracja** kliknij pozycję **Przesyłanie dalej**.
4. W obszarze **Usługi przesyłania dalej**obok usługi Microsoft Azure Sentinel (Log Analytics) kliknij pozycję **Dodaj**. 
4. W oknie **Microsoft Azure Sentinel (Log Analytics)** kliknij pozycję **Pokaż zaawansowane**. 
5. W górnej części okna rozszerzonego do usługi Microsoft Azure Sentinel (Log Analytics) wykonaj następujące czynności:
    -   **Nazwa**: Wpisz nazwę usługi przesyłania dalej, która ma nie więcej niż 30 znaków. Wybierz unikatową, znaczącą nazwę. Ta nazwa pojawia się na liście usług przesyłania dalej na ekranie **Konfiguracja** i na pulpitach nawigacyjnych na ekranie **pulpitu nawigacyjnego.** Na przykład: Microsoft Azure Log Analytics Wschód. To pole jest wymagane.
    -   **Opis**: Wpisz opis usługi przesyłania dalej. Ten opis pojawia się również na liście usług przesyłania dalej na ekranie **Konfiguracja.** Dołącz szczegóły, takie jak typ zdarzenia jest przekazywany i grupa, która musi sprawdzić dane.
    -   **Typ uruchomienia:** Wybierz metodę uruchamiania dla konfiguracji usługi przesyłania dalej. Opcje są ręczne i automatyczne.<br>Wartość domyślna to Automatyczne. 
6. W obszarze **Zdarzenia**wykonaj następujące czynności: 
    - **Źródło**: Wybierz jedno lub więcej archiwów, z których chcesz przesyłać dalej zdarzenia. Można wybrać aktywne archiwa kolekcjonerskie (w tym archiwum wspólne), oddzielone archiwa kolekcjonerskie (czyli archiwa dla usuniętych kolekcjonerów), archiwa odbiorników ICDx lub archiwum systemowe. <br>Wartość domyślna to Wspólne archiwum.
      > [!NOTE]
      > Archiwa odbiorników ICDx są wymienione oddzielnie, według nazwy. 
 
    - **Filtr**: Dodaj filtr określający podzbiór zdarzeń do przekazania. Wykonaj jedną z następujących czynności:
        - Aby wybrać warunek filtru, kliknij pozycję Typ, Atrybut, Operator i Wartość. 
        - W polu Filtr przejrzyj stan filtru. Można go edytować bezpośrednio w polu lub usunąć w razie potrzeby.
        - Kliknij przycisk I lub LUB, aby dodać go do stanu filtru.
        - Można również kliknąć przycisk Zapisane kwerendy, aby zastosować zapisaną kwerendę.
    - **Uwzględnione atrybuty:** Wpisz listę atrybutów rozdzielanych przecinkami, które mają być uwzględnione w danych przesyłanych dalej. Uwzględnione atrybuty mają pierwszeństwo przed wykluczonymi atrybutami.
    - **Wykluczone atrybuty:** Wpisz listę atrybutów rozdzielanych przecinkami, które mają zostać wykluczone z przesyłanych dalej danych.
    - **Rozmiar partii:** Wybierz liczbę zdarzeń do wysłania na partię. Opcje to 10, 50, 100, 500 i 1000.<br>Wartość domyślna to 100. 
    - **Limit szybkości:** Wybierz szybkość, z jaką zdarzenia są przekazywane, wyrażone jako zdarzenia na sekundę. Twoje opcje to Unlimited, 500, 1000, 5000, 10000. <br> Wartość domyślna to 5000. 
7. W obszarze **Usługa Azure Destination**wykonaj następujące czynności: 
    - **Identyfikator obszaru roboczego:** Wklej identyfikator obszaru roboczego od dołu. To pole jest wymagane.
    - **Klucz podstawowy:** Wklej klucz podstawowy od dołu. To pole jest wymagane.
    - **Nazwa dziennika niestandardowego:** Wpisz nazwę dziennika niestandardowego w obszarze roboczym usługi Microsoft Azure portal Log Analytics, do którego zamierzasz przesyłać dalej zdarzenia. Wartość domyślna to SymantecICDx. To pole jest wymagane.
8. Kliknij *przycisk Zapisz,* aby zakończyć konfigurację usługi przesyłania dalej. 
9. Aby uruchomić przesyłanie dalej, w obszarze **Opcje**kliknij pozycję **Więcej,** a następnie **przycisk Uruchom**.
10. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zdarzeń ICDx firmy Symantec, wyszukaj **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Symantec ICDx z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


