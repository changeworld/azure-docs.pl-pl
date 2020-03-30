---
title: Opis metryk dla usługi Azure Spring Cloud
description: Dowiedz się, jak przeglądać metryki w usłudze Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256759"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Opis metryk dla usługi Azure Spring Cloud

Eksplorator metryk platformy Azure jest składnikiem portalu Microsoft Azure, który umożliwia drukowanie wykresów, wizualnie korelowanie trendów i badanie skoków i spadków metryk. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. 

W usłudze Azure Spring Cloud istnieją dwa punkty widoku dla metryk.
* Wykresy na każdej stronie przeglądu aplikacji
* Strona typowe metryki

 ![Wykresy metryki](media/metrics/metrics-1.png)

Wykresy w **aplikacji Omówienie** zapewniają szybkie sprawdzanie stanu dla każdej aplikacji. Wspólna strona **Metryki** zawiera wszystkie metryki dostępne w celach informacyjnych. Możesz tworzyć własne wykresy na wspólnej stronie metryk i przypinać je na pulpicie nawigacyjnym.

## <a name="application-overview-page"></a>Strona przeglądu aplikacji
Wybierz aplikację w **obszarze Zarządzanie aplikacjami,** aby znaleźć wykresy na stronie przeglądu.  

 ![Zarządzanie metrykami aplikacji](media/metrics/metrics-2.png)

Każda aplikacja **przegląd aplikacji** strona przedstawia wykres metryki, który umożliwia szybkie sprawdzanie stanu aplikacji.  

 ![Omówienie metryk aplikacji](media/metrics/metrics-3.png)

Usługa Azure Spring Cloud udostępnia te pięć wykresów z metrykami, które są aktualizowane co minutę:

* **Błędy serwera Http:** Liczba błędów dla żądań HTTP do aplikacji
* **Dane w**: Bajty odebrane przez aplikację
* **Wyjście danych:** bajty wysyłane przez aplikację
* **Żądania:** Żądania odebrane przez aplikację
* **Średni czas reakcji:** średni czas reakcji z aplikacji

Dla wykresu można wybrać zakres czasu od jednej godziny do siedmiu dni.

## <a name="common-metrics-page"></a>Strona typowe metryki

**Metryki** w lewym okienku nawigacji łączy się ze wspólną stronę metryki.

Najpierw wybierz dane do wyświetlenia:

![Wybierz widok metryki](media/metrics/metrics-4.png)

Szczegółowe informacje o opcji wszystkich metryk można znaleźć w [sekcji](#user-metrics-options) poniżej.

Następnie wybierz typ agregacji dla każdej metryki:

![Agregacja metryczna](media/metrics/metrics-5.png)

Typ agregacji wskazuje sposób agregowania punktów metryk na wykresie według czasu. Co minutę jest jeden punkt metryki nieprzetworzonej, a typ wstępnej agregacji w ciągu kilku minut jest wstępnie zdefiniowany przez typ metryki.
* Suma: Sumuj wszystkie wartości jako dane wyjściowe docelowe.
* Średnia: Użyj średniej wartości w okresie jako docelowej produkcji wyjściowej.
* Max/Min: Użyj wartości Max/Min w okresie jako wyjścia docelowego.

Zakres czasu, aby pokazać może być również modyfikowany. Zakres czasu można wybrać z ostatnich 30 minut do ostatnich 30 dni lub niestandardowy zakres czasu.

![Modyfikacja metryczna](media/metrics/metrics-6.png)

Widok domyślny zawiera wszystkie metryki aplikacji usługi Azure Spring Cloud razem. Metryki jednej aplikacji lub wystąpienia można filtrować na ekranie.  Kliknij **pozycję Dodaj filtr**, ustaw właściwość na **Aplikację**i wybierz aplikację docelową, którą chcesz monitorować, w polu tekstowym **Wartości.** 

Można użyć dwóch rodzajów filtrów (właściwości):
* Aplikacja: filtrowanie według nazwy aplikacji
* Wystąpienie: filtrowanie według wystąpienia aplikacji

![Filtry metryczne](media/metrics/metrics-7.png)

Można również użyć opcji **Zastosuj podział,** która spowoduje narysowanie wielu linii dla jednej aplikacji:

![Podział metryczny](media/metrics/metrics-8.png)

>[!TIP]
> Możesz tworzyć własne wykresy na stronie metryki i przypinać je do **pulpitu nawigacyjnego**. Zacznij od nadania nazwy wykresowi.  Następnie wybierz **pozycję Przypnij do pulpitu nawigacyjnego w prawym górnym rogu**. Teraz możesz sprawdzić aplikację na **pulpicie nawigacyjnym**portalu .

## <a name="user-metrics-options"></a>Opcje metryk użytkownika

W poniższych tabelach przedstawiono dostępne dane i szczegóły.

### <a name="error"></a>Błąd
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika sprężyny | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Błąd globalny Tomcat | tomcat.global.error | Liczba | Liczba wystąpi ą błędy przetworzonych żądań |

### <a name="performance"></a>Wydajność
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika sprężyny | Jednostka | Szczegóły |
>|----|----|----|------------|
>|Procent użycia procesora CPU systemu | system.cpu.usage | Wartość procentowa | Ostatnie użycie procesora dla całego systemu. Ta wartość jest podwójną w interwale [0.0,1.0]. Wartość 0,0 oznacza, że wszystkie procesory były bezczynne w ostatnim okresie obserwowanym okresie czasu, podczas gdy wartość 1,0 oznacza, że wszystkie procesory aktywnie działały w 100% czasu w ostatnim okresie obserwowanym.|
>| Procent użycia procesora CPU aplikacji | Procent użycia procesora CPU aplikacji | Wartość procentowa | Ostatnie użycie procesora CPU dla procesu java virtual machine. Ta wartość jest podwójną w interwale [0.0,1.0]. Wartość 0,0 oznacza, że żaden z procesorów nie uruchamiał wątków z procesu JVM w ostatnim okresie obserwowanego okresu, podczas gdy wartość 1,0 oznacza, że wszystkie procesory aktywnie uruchamiały wątki z JVM 100% czasu w ostatnim okresie obserwowanym. Wątki z JVM obejmują wątki aplikacji, a także wątki wewnętrzne JVM.|
>| Przypisana pamięć aplikacji | jvm.memory.committed | Bajty | Reprezentuje ilość pamięci, która jest gwarantowana do użycia przez JVM. JVM może zwolnić pamięć do systemu i popełnione może być mniejsza niż init. zawsze będzie większa lub równa wykorzystanym. |
>| Używana pamięć aplikacji | jvm.memory.used | Bajty | Reprezentuje ilość pamięci aktualnie używanej w bajtach. |
>| Maksymalna ilość pamięci aplikacji | jvm.memory.max | Bajty | Reprezentuje maksymalną ilość pamięci, która może służyć do zarządzania pamięcią. Ilość używanej i zatwierdzonej pamięci będzie zawsze mniejsza lub równa max, jeśli zdefiniowano maks. Alokacja pamięci może zakończyć się niepowodzeniem, jeśli próbuje zwiększyć używanej pamięci, tak aby została zatwierdzona >, nawet jeśli jest używana <= max, nadal będzie prawdziwa (na przykład, gdy w systemie brakuje pamięci wirtualnej). |
>| Maksymalny dostępny rozmiar danych starej generacji | jvm.gc.max.data.size | Bajty | Szczytowe użycie pamięci w puli pamięci starej generacji od momentu uruchomienia maszyny wirtualnej Java. |
>| Rozmiar danych starej generacji | jvm.gc.live.data.size | Bajty | Rozmiar starej generacji puli pamięci po pełnej GC. |
>| Promowanie rozmiaru danych starej generacji | jvm.gc.memory.promoted | Bajty | Liczba dodatnich wzrostów wielkości puli pamięci starej generacji przed GC do po GC. |
>| Promuj rozmiar danych młodego pokolenia | jvm.gc.memory.allocated jvm.gc.memory.allocated jvm.gc.memory.allocated jv | Bajty | Zwiększa się do zwiększenia wielkości puli pamięci młodego pokolenia po jednym GC do przed następnym. |
>| Liczba wstrzymania GC | jvm.gc.pause (całkowita liczba) | Liczba | Całkowita liczba GC po rozpoczęciu tego JMV, w tym Young i Old GC. |
>| Całkowity czas pauzy GC | jvm.gc.pause (całkowity czas) | Milisekund | Całkowity czas pochłonięty przez GC po rozpoczęciu tego JMV, w tym Young i Old GC. |

### <a name="request"></a>Żądanie
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika sprężyny | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Tomcat całkowita wysłana bajty | tomcat.global.sent | Bajty | Ilość wysłanych danych serwera www Tomcat |
>| Tomcat Całkowita liczba odebranych bajtów | tomcat.global.received | Bajty | Ilość danych odebranych przez serwer www Tomcat |
>| Tomcat żądanie całkowity czas | tomcat.global.request (całkowity czas) | Milisekund | Całkowity czas przetwarzania żądań serwera sieci Web firmy Tomcat |
>| Całkowita liczba żądań Tomcat | tomcat.global.request (całkowita liczba) | Liczba | Całkowita liczba żądań przetworzonych serwera sieci Web Tomcat |
>| Tomcat Wniosek Maksymalny czas | tomcat.global.request.max | Milisekund | Maksymalny czas przetwarzania żądania przez serwer www Tomcat |

### <a name="session"></a>Sesja
>[!div class="mx-tdCol2BreakAll"]
>| Nazwa | Nazwa metryki siłownika sprężyny | Jednostka | Szczegóły |
>|----|----|----|------------|
>| Tomcat Session Max Aktywna liczba | tomcat.sessions.active.mak | Liczba | Maksymalna liczba sesji, które były aktywne w tym samym czasie |
>| Tomcat Sesja Max Alive Czas | tomcat.sessions.alive.max | Milisekund | Najdłuższy czas (w sekundach), że wygasła sesja była żywa |
>| Liczba utworów sesji Tomcat | tomcat.sessions.created | Liczba | Liczba utworzonych sesji |
>| Liczba wygasłych sesji Tomcat | tomcat.sessions.expired | Liczba | Liczba sesji, które wygasły |
>| Liczba odrzuconych sesji Tomcat | tomcat.sessions.odrzucony | Liczba | Liczba sesji, które nie zostały utworzone, ponieważ osiągnięto maksymalną liczbę aktywnych sesji. |

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie do Eksploratora metryk platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analizowanie dzienników i danych za pomocą ustawień diagnostycznych](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Następne kroki
* [Samouczek: Monitorowanie zasobów wiosennej chmury przy użyciu alertów i grup akcji](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Przydziały i plany usług dla usługi Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

