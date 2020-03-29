---
title: Konfigurowanie alertów — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: W tym artykule opisano sposób konfigurowania alertów metryk usługi Azure Database dla usługi PostgreSQL — hiperskala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063145"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Użyj witryny Azure Portal, aby skonfigurować alerty dotyczące metryk dla usługi Azure Database dla postgreSQL — skala hiperskala (Citus)

W tym artykule pokazano, jak skonfigurować usługę Azure Database dla alertów PostgreSQL przy użyciu witryny Azure portal. Możesz otrzymać alert na podstawie [monitorowania metryk](concepts-hyperscale-monitoring.md) dla usług platformy Azure.

Firma Asfigurujemy alert do wyzwalania, gdy wartość określonej metryki przekroczy próg. Alert wyzwala, gdy warunek jest po raz pierwszy spełniony, a następnie wyzwala ją.

Alert można skonfigurować tak, aby uruchamiał następujące czynności:
* Wysyłaj powiadomienia e-mail do administratora usługi i współadministratorów.
* Wyślij wiadomość e-mail na dodatkowe określone wiadomości e-mail.
* Zadzwoń do elementu webhook.

Za pomocą:
* [Portal Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertów na metryce z witryny Azure portal
1. W [witrynie Azure portal](https://portal.azure.com/)wybierz serwer Usługi Azure Database dla postgreSQL, który chcesz monitorować.

2. W sekcji **Monitorowanie** paska bocznego wybierz **pozycję Alerty,** jak pokazano na rysunku:

   ![Wybierz reguły alertów](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Wybierz **pozycję Nowa reguła alertu** (+ ikona).

4. Zostanie otwarta strona **Reguła tworzenia,** jak pokazano poniżej. Wypełnij wymagane informacje:

   ![Dodaj formularz alertu metryki](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. W sekcji **Warunek** wybierz pozycję **Dodaj**.

6. Wybierz metrykę z listy sygnałów, które mają być alertowane. W tym przykładzie wybierz "Procent magazynu".
   
   ![Wybierz metrykę](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Skonfiguruj logikę alertu:

    * **Operator** (np. "Większa niż")
    * **Wartość progowa** (np. 85 proc.)
    * **Stopień szczegółowości agregacji** musi zostać spełniony przed wyzwoleniem alertu (np. "W ciągu ostatnich 30 minut")
    * i **częstotliwość oceny** (np. "1 minuta")
   
   Po zakończeniu wybierz **pozycję Gotowe.**

   ![Wybierz metrykę](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. W sekcji **Grupy akcji** wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę, która będzie otrzymywać powiadomienia w alertie.

9. Wypełnij formularz "Dodaj grupę akcji" nazwą, krótką nazwą, subskrypcją i grupą zasobów.

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Skonfiguruj typ akcji **e-mail/SMS/Push/Voice.**
    
    Wybierz opcję "Rola usługi Azure Resource Manager poczty e-mail", aby wysyłać powiadomienia do właścicieli subskrypcji, współautorów i czytelników.
   
    Po zakończeniu wybierz **przycisk OK.**

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Określ nazwę reguły alertu, opis i ważność.

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Wybierz **pozycję Utwórz regułę alertu,** aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwala, jak opisano wcześniej.

### <a name="managing-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu możesz go wybrać i wykonać następujące czynności:

* Wyświetl wykres przedstawiający próg metryki i rzeczywiste wartości z poprzedniego dnia istotne dla tego alertu.
* **Edytuj** lub **usuń** regułę alertu.
* **Wyłącz** lub **włącz** alert, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.

## <a name="suggested-alerts"></a>Sugerowane alerty

### <a name="disk-space"></a>Miejsce na dysku

Monitorowanie i alerty są ważne dla każdej grupy serwerów produkcyjnych w hiperskali (Citus). Podstawowa baza danych PostgreSQL wymaga wolnego miejsca na dysku, aby działać poprawnie. Jeśli dysk zostanie zapełniony, węzeł serwera bazy danych przejdzie w tryb offline i odmówi uruchomienia, dopóki nie będzie dostępne miejsce. W tym momencie wymaga żądania pomocy technicznej firmy Microsoft, aby naprawić sytuację.

Zaleca się ustawienie alertów miejsca na dysku w każdym węźle w każdej grupie serwerów, nawet w przypadku użycia nieprodukcyjnego. Alerty użycia miejsca na dysku zapewniają ostrzeżenie z wyprzedzeniem potrzebne do interwencji i utrzymania węzłów w dobrej kondycji. Aby uzyskać najlepsze wyniki, wypróbuj serię alertów przy użyciu 75%, 85% i 95%. Wartości procentowe do wyboru zależą od szybkości pozyskiwania danych, ponieważ szybkie pobłyszenie danych szybciej zapełnia dysk.

Gdy dysk zbliża się do limitu miejsca, wypróbuj te techniki, aby uzyskać więcej wolnego miejsca:

* Przejrzyj zasady przechowywania danych. Jeśli to możliwe, przenieś starsze dane do chłodni.
* Należy rozważyć [dodanie węzłów](howto-hyperscale-scaling.md#add-worker-nodes) do grupy serwerów i równoważenie fragmentów. Ponowne równoważenie dystrybuuje dane na większej liczbie komputerów.
* Należy rozważyć [zwiększenie zdolności produkcyjnych](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) węzłów procesu roboczego. Każdy pracownik może mieć maksymalnie 2 TiB pamięci masowej. Jednak dodanie węzłów należy podjąć przed zmiana rozmiaru węzłów, ponieważ dodawanie węzłów kończy się szybciej.

### <a name="cpu-usage"></a>Użycie procesora

Monitorowanie użycia procesora CPU jest przydatne do ustanowienia linii bazowej dla wydajności. Na przykład można zauważyć, że użycie procesora CPU wynosi zwykle około 40-60%. Jeśli użycie procesora CPU nagle zaczyna oscylować wokół 95%, można rozpoznać anomalię. Użycie procesora MOŻE odzwierciedlać wzrost organiczny, ale może również ujawnić bezpańskie zapytanie. Podczas tworzenia alertu procesora CPU, ustawić ziarnistość długiej agregacji, aby złapać długotrwałe wzrosty i zignorować chwilowe skoki.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się [z omówieniem kolekcji metryk,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) aby upewnić się, że usługa jest dostępna i responsywna.
