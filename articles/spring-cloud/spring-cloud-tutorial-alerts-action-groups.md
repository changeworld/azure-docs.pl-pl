---
title: 'Samouczek: monitorowanie zasobów chmurowych platformy Azure przy użyciu alertów i grup akcji | Microsoft Docs'
description: Dowiedz się, jak korzystać z alertów w chmurze wiosennej.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694005"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Samouczek: monitorowanie źródeł wiosennych w chmurze przy użyciu alertów i grup akcji

Alerty chmurowe platformy Azure obsługują monitorowanie zasobów na podstawie takich warunków, jak dostępne magazyny, częstotliwość żądań lub użycie danych. Alert wysyła powiadomienie, gdy stawki lub warunki spełniają zdefiniowane specyfikacje.

Aby skonfigurować potok alertów, należy wykonać dwie czynności: 
1. Skonfiguruj grupę akcji z akcjami, które mają być podejmowane po wyzwoleniu alertu, na przykład wiadomości e-mail, wiadomości SMS, elementu Runbook lub elementu webhook. Grupy akcji mogą być ponownie używane między różnymi alertami.
2. Skonfiguruj reguły alertów. Reguły wiążą wzorce metryk z grupami akcji w oparciu o zasób docelowy, metrykę, warunek, agregację czasu itp.

## <a name="prerequisites"></a>Wymagania wstępne
Oprócz wymagań wiosennych platformy Azure ten samouczek zależy od następujących zasobów.

* Wdrożone wystąpienie chmury Azure wiosennej.  Skorzystaj z naszego [przewodnika Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby rozpocząć pracę.

* Zasób platformy Azure do monitorowania, na przykład baza danych zaimplementowana w tym artykule: [jak używać sprężyny danych interfejs API Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
Poniższe procedury inicjują **grupę akcji** i **alert** , rozpoczynając od opcji **alertów** w okienku nawigacji po lewej stronie wystąpienia chmury wiosnowej. (Procedurę można również uruchomić na stronie **Przegląd monitora** w Azure Portal). 

Przejdź do grupy zasobów do wystąpienia chmury wiosennej. W lewym okienku wybierz pozycję **alerty** , a następnie wybierz pozycję **Zarządzaj akcjami**:

![Strona grupy zasobów portalu zrzutu ekranu](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Konfigurowanie grupy akcji

Aby rozpocząć procedurę inicjowania nowej **grupy akcji**, wybierz pozycję **+ Dodaj grupę akcji**.

![Dodaj grupę akcji do portalu zrzutu ekranu](media/alerts-action-groups/action-1.png)

Na stronie **Dodaj grupę akcji** :

 1. Określ **nazwę grupy akcji** i **krótką nazwę**.

 1. Określ **subskrypcję** i **grupę zasobów**.

 1. Określ **nazwę akcji**.

 1. Wybierz **Typ akcji**.  Spowoduje to otwarcie kolejnego okienka po prawej stronie, aby zdefiniować akcję, która zostanie podjęta po aktywacji.

 1. Zdefiniuj akcję przy użyciu opcji w okienku po prawej stronie.  W tym przypadku jest stosowane powiadomienie e-mail.

 1. Kliknij przycisk **OK** w prawym okienku akcji.

 1. Kliknij przycisk **OK** w oknie dialogowym **Dodaj grupę akcji** . 

  ![Akcja definiowania portalu zrzutu ekranu](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Konfigurowanie alertu 

W poprzednich krokach utworzono **grupę akcji** , która używa poczty e-mail. Możesz również użyć powiadomień na telefon, elementów webhook, usługi Azure Functions itd.  

Aby skonfigurować **alert**, przejdź z powrotem do strony **alerty** , a następnie kliknij pozycję **Zarządzaj regułami alertów**.

  ![Portal zrzutu ekranu — Definiowanie alertu](media/alerts-action-groups/alerts-2.png)

1. Wybierz **zasób** dla alertu.

1. Kliknij pozycję **+ Nowa reguła alertu**.

  ![Nowa reguła alertu portalu zrzutu ekranu](media/alerts-action-groups/alerts-3.png)

1. Na stronie **Tworzenie reguły** Określ **zasób**, **warunek**i **Akcje**.  W okienku **Akcje** wybierz wcześniej zdefiniowaną **grupę akcji**.

1. W obszarze **szczegóły alertu**Nadaj nazwę regule alertu.

1. Kliknij przycisk **Utwórz regułę alertu**.

  ![Nowa reguła alertu portalu zrzutu ekranu](media/alerts-action-groups/alerts-4.png)

Sprawdź, czy Nowa reguła alertu jest włączona.

  ![Nowa reguła alertu portalu zrzutu ekranu](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Następne kroki
* [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Zachowanie alertów SMS w grupach akcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Samouczek: używanie rozproszonego śledzenia w chmurze Azure wiosennej](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
