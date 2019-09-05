---
title: Metryki Azure Monitor dla Application Gateway
description: Dowiedz się, jak używać metryk do monitorowania wydajności bramy aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309845"
---
# <a name="metrics-for-application-gateway"></a>Metryki dla Application Gateway

Application Gateway publikuje punkty danych o nazwie Metrics, aby [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) wydajności Application Gateway i wystąpień zaplecza. Te metryki to wartości liczbowe w uporządkowanym zestawie danych szeregów czasowych, które opisują kilka aspektów bramy aplikacji w danym momencie. Jeśli istnieją żądania przepływające przez Application Gateway, miary i wysyłają metryki w 60 sekund. Jeśli nie ma żadnych żądań przepływających przez Application Gateway lub Brak danych dla metryki, Metryka nie zostanie zgłoszona. Aby uzyskać więcej informacji, zobacz [Azure monitor metryki](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metryki obsługiwane przez jednostkę SKU Application Gateway v2

### <a name="timing-metrics"></a>Metryki chronometrażu

Dostępne są następujące metryki związane z chronometrażem żądania i odpowiedzi. Analizując te metryki, można określić, czy spowolnienie w aplikacji, w związku z siecią WAN, Application Gateway, sieci między Application Gatewayem i zapleczem, czy wydajność aplikacji.

- **Czas RTT klienta**

  Średni czas błądzenia między klientami a Application Gateway. Ta Metryka wskazuje, jak długo zajmuje się nawiązaniem połączeń i zwróceniem potwierdzeń.

- **Łączny czas bramy aplikacji**

  Średni czas przetwarzania żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako średnia interwału od momentu, kiedy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania Application Gateway, czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć i czas odpowiedzi serwera wewnętrznej bazy danych.

- **Czas połączenia z zapleczem**

  Czas nawiązywania połączenia z serwerem zaplecza. 

- **Czas odpowiedzi na pierwszy bajt zaplecza**

  Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania pierwszego bajtu nagłówka odpowiedzi, który zbliża czas przetwarzania serwera wewnętrznej bazy danych

- **Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych**

  Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi

### <a name="application-gateway-metrics"></a>Metryki Application Gateway

W przypadku Application Gateway dostępne są następujące metryki:

- **Bajty odebrane**

   Liczba bajtów odebranych przez Application Gateway od klientów

- **Bajty wysłane**

   Liczba bajtów wysłanych przez Application Gateway do klientów

- **Protokół TLS klienta**

   Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, które ustanowiły połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, należy filtrować według protokołu TLS wymiaru.

- **Bieżące jednostki wydajności**

   Liczba zużytych jednostek pojemności. Jednostki wydajności mierzą koszt oparty na zużyciu, który jest naliczany wraz ze stałym kosztem. Istnieją trzy uwarunkowania jednostek wydajności — jednostka obliczeniowa, trwałe połączenia i przepływność. Każda jednostka wydajności składa się z maksymalnie: 1 jednostka obliczeniowa lub 2500 połączeń trwałych lub przepływność 2,22-MB/s.

- **Bieżące jednostki obliczeniowe**

   Liczba zużytych pojemności procesora. Czynniki wpływające na jednostkę obliczeniową to połączenia TLS/s, obliczenia ponownego zapisu adresów URL i przetwarzanie reguł WAF. 

- **Bieżące połączenia**

   Liczba bieżących połączeń ustanowionych z Application Gateway

- **Nieudane żądania**

   Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.


- **Stan odpowiedzi**

   Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

   Liczba bajtów na sekundę obsłużonych przez Application Gateway

- **Łączna liczba żądań**

   Liczba pomyślnych żądań obsłużonych przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.

- **Reguły dopasowane do zapory aplikacji sieci Web**

- **Reguły wyzwalane przez zaporę aplikacji sieci Web**

### <a name="backend-metrics"></a>Metryki zaplecza

W przypadku Application Gateway dostępne są następujące metryki:

- **Stan odpowiedzi zaplecza**

  Liczba kodów stanu odpowiedzi HTTP zwracanych przez sekwencje końcowe. Nie obejmuje to żadnych kodów odpowiedzi wygenerowanych przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Liczba hostów w dobrej kondycji**

  Liczba punktów końcowych, które są określone w dobrej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby pokazać hosty zdrowe/złej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba punktów końcowych, które są określone w złej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby pokazać hosty w złej kondycji w określonej puli zaplecza.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metryki obsługiwane przez jednostkę SKU Application Gateway v1

### <a name="application-gateway-metrics"></a>Metryki Application Gateway

W przypadku Application Gateway dostępne są następujące metryki:

- **Bieżące połączenia**

  Liczba bieżących połączeń ustanowionych z Application Gateway

- **Nieudane żądania**

  Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.

- **Stan odpowiedzi**

  Stan odpowiedzi HTTP zwrócony przez Application Gateway. Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**

  Liczba bajtów na sekundę obsłużonych przez Application Gateway

- **Łączna liczba żądań**

  Liczba pomyślnych żądań obsłużonych przez Application Gateway. Liczbę żądań można w dalszej odfiltrować, aby pokazać liczbę dla każdej lub określonej puli zaplecza — kombinację ustawień http.

- **Reguły dopasowane do zapory aplikacji sieci Web**

- **Reguły wyzwalane przez zaporę aplikacji sieci Web**

### <a name="backend-metrics"></a>Metryki zaplecza

W przypadku Application Gateway dostępne są następujące metryki:

- **Liczba hostów w dobrej kondycji**

  Liczba punktów końcowych, które są określone w dobrej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby pokazać hosty zdrowe/złej kondycji w określonej puli zaplecza.

- **Liczba hostów w złej kondycji**

  Liczba punktów końcowych, które są określone w złej kondycji przez sondę kondycji. Można filtrować według jednej puli zaplecza, aby pokazać hosty w złej kondycji w określonej puli zaplecza.

## <a name="metrics-visualization"></a>Wizualizacja metryk

Przejdź do bramy aplikacji, w obszarze **monitorowanie** wybierz pozycję **metryki**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji przedstawiono przykład z trzema metrykami wyświetlonymi w ciągu ostatnich 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Widok metryki")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aby wyświetlić bieżącą listę metryk, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Reguły alertów dla metryk

Reguły alertów można uruchomić na podstawie metryk dla zasobu. Na przykład alert może wywołać element webhook lub wysłać wiadomość e-mail do administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub w określonym przedziale czasu.

Poniższy przykład przeprowadzi Cię przez proces tworzenia reguły alertu, która wysyła wiadomość e-mail do administratora po naruszeniu przez przepływność wartości progowej:

1. Wybierz pozycję **Dodaj alert metryki** , aby otworzyć stronę **Dodawanie reguły** . Możesz również uzyskać dostęp do tej strony ze strony metryki.

   ![Przycisk "Dodaj alert dotyczący metryki"][6]

2. Na stronie **Dodawanie reguły** Wypełnij sekcje nazwa, warunek i powiadomienie, a następnie wybierz **przycisk OK**.

   * W selektorze **warunku** wybierz jedną z czterech wartości: **Większe**niż, **większe niż lub równe**, **mniejsze**niż lub **mniejsze niż lub równe**.

   * W selektorze **okresu** Wybierz okres z pięciu minut do 6 godzin.

   * W przypadku wybrania opcji **właściciele, współautorzy i czytelnicy poczty**e-mail wiadomość e-mail może być dynamiczna na podstawie użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie można podać listę użytkowników z rozdzieloną przecinkami w polu **dodatkowe adresy e-mail administratora** .

   ![Dodaj stronę reguły][7]

W przypadku naruszenia progu wiadomości e-mail podobnej do tej na poniższej ilustracji przychodzą:

![Adres e-mail pod kątem naruszenia progu][8]

Po utworzeniu alertu dotyczącego metryki pojawia się lista alertów. Zawiera przegląd wszystkich reguł alertów.

![Lista alertów i reguł][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, zobacz [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Aby dowiedzieć się więcej o elementach webhook i sposobach ich użycia z alertami, odwiedź stronę [Konfigurowanie elementu webhook w ramach alertu dotyczącego metryki platformy Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure przy użyciu wpisu w blogu Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i więcej wpisów w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
