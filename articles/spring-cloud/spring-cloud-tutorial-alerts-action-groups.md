---
title: 'Samouczek: Monitorowanie zasobów usługi Azure Spring Cloud przy użyciu alertów i grup akcji | Dokumenty firmy Microsoft'
description: Dowiedz się, jak korzystać z alertów Spring Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77920080"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Samouczek: Monitorowanie zasobów wiosennej chmury przy użyciu alertów i grup akcji

Alerty usługi Azure Spring Cloud obsługują zasoby monitorowania na podstawie warunków, takich jak dostępny magazyn, szybkość żądań lub użycie danych. Alert wysyła powiadomienie, gdy stawki lub warunki spełniają określone specyfikacje.

Istnieją dwa kroki, aby skonfigurować potok alertów: 
1. Skonfiguruj grupę akcji z działaniami, które należy podjąć po wyzwoleniu alertu, takimi jak poczta e-mail, SMS, Element runbook lub element webhook. Grupy akcji mogą być ponownie używane między różnymi alertami.
2. Konfigurowanie reguł alertów. Reguły wiążą wzorce metryk z grupami akcji na podstawie zasobu docelowego, metryki, warunku, agregacji czasu itp.

## <a name="prerequisites"></a>Wymagania wstępne
Oprócz wymagań platformy Azure Spring ten samouczek zależy od następujących zasobów.

* Wdrożone wystąpienie usługi Azure Spring Cloud.  Postępuj zgodnie z naszym [przewodnikiem Szybki start,](spring-cloud-quickstart-launch-app-cli.md) aby rozpocząć.

* Zasób platformy Azure do monitorowania. W tym przykładzie monitoruje wystąpienie Spring Cloud.
 
Poniższe procedury inicjują zarówno **grupę akcji,** jak i **alert,** zaczynając od opcji **Alerty** w lewym okienku nawigacji wystąpienia Spring Cloud. (Procedura może również rozpocząć się od **monitora przeglądu** strony witryny Azure portal.) 

Przejdź z grupy zasobów do wystąpienia usługi Spring Cloud. Wybierz **pozycję Alerty** w lewym okienku, a następnie wybierz pozycję **Zarządzaj akcjami:**

![Strona grupy zasobów portalu zrzutu ekranu](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Konfigurowanie grupy akcji

Aby rozpocząć procedurę inicjowania nowej **grupy akcji,** wybierz **pozycję + Dodaj grupę akcji**.

![Zrzut ekranu portalu Dodaj grupę akcji](media/alerts-action-groups/action-1.png)

Na stronie **Dodaj grupę akcji:**

 1. Określ **nazwę grupy akcji** i **krótką nazwę**.

 1. Określ **subskrypcję** i **grupę zasobów**.

 1. Określ **nazwę akcji**.

 1. Wybierz **typ akcji**.  Spowoduje to otwarcie innego okienka po prawej stronie, aby zdefiniować akcję, która zostanie podjęta przy aktywacji.

 1. Zdefiniuj akcję za pomocą opcji w prawym okienku.  W tym przypadku użyto powiadomienia e-mail.

 1. Kliknij **przycisk OK** w prawym okienku akcji.

 1. Kliknij **przycisk OK** w oknie dialogowym Dodawanie grupy **akcji.** 

  ![Zrzut ekranu Portal zdefiniować akcję](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Konfigurowanie alertu 

W poprzednich krokach utworzono **grupę akcji,** która używa poczty e-mail. Można również użyć powiadomienia telefonicznego, zestawów webhook, funkcji platformy Azure itp.  

Aby skonfigurować **alert,** przejdź wstecz do strony Alerty i kliknij pozycję **Zarządzaj regułami** **alertów** .

  ![Alert definiowania alertu przez portal zrzutów ekranu](media/alerts-action-groups/alerts-2.png)

1. Wybierz **zasób** dla alertu.

1. Kliknij **przycisk + Nowa reguła alertu**.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-3.png)

1. Na stronie **Reguła Tworzenia** określ **zasób**.

1. Ustawienie **WARUNEK** udostępnia wiele opcji monitorowania zasobów **spring cloud.**  Kliknij **przycisk Dodaj,** aby otworzyć **okienko logiki konfiguruj sygnał.**

1. Wybierz warunek. W tym przykładzie użyto **procentu użycia procesora cpu systemu**.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-3-1.png)

1. Przewiń w dół **okienko logiki skonfiguruj sygnał,** aby ustawić **wartość próg** do monitorowania.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-3-2.png)

1. Kliknij przycisk **Gotowe**.

Aby uzyskać szczegółowe informacje na temat warunków dostępnych do monitorowania, zobacz [Opcje metryk portalu użytkownika](spring-cloud-concept-metrics.md#user-metrics-options).

 W obszarze **AKCJE**kliknij pozycję **Wybierz grupę akcji**. Z okienka **AKCJE** wybierz wcześniej zdefiniowaną **grupę akcji**.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-3-3.png) 

1. Przewiń w dół i w obszarze **SZCZEGÓŁY ALERTu**nazwij regułę alertu.

1. Ustaw **ważność**.

1. Kliknij **pozycję Utwórz regułę alertu**.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-3-4.png)

Sprawdź, czy nowa reguła alertu jest włączona.

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-4.png)

Regułę można również utworzyć na stronie **Metryki:**

   ![Zrzut ekranu Portal nowa reguła alertów](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Następne kroki
* [Opcje metryk portalu użytkownika](spring-cloud-concept-metrics.md#user-metrics-options)
* [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Zachowanie alertu programu SMS w grupach akcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Samouczek: Korzystanie z śledzenia rozproszonego w chmurze Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
