---
title: Wykrywanie problemów z urządzeniami w zdalnym rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób automatycznego wykrywania problemów z urządzeniami oparte na wartościach progowych w zdalnym rozwiązanie monitorowania przy użyciu reguł i akcje.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 461490a312cbcfda50f4b2e9db39c40250d716fd
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Wykrywanie problemów przy użyciu reguły progu

Ten samouczek pokazuje możliwości aparatu reguł w zdalnym rozwiązanie monitorowania. Aby dodać tych funkcji, samouczku scenariusza aplikacji Contoso IoT.

Firma Contoso ma regułę, która generuje alert krytyczny, gdy wykorzystanie zgłoszone przez **Chłodnica** urządzenia przekracza 250 PSI. Jako operator chcesz zidentyfikować **Chłodnica** urządzeń, które mogą mieć problemy czujników, wyszukując początkowej wartości szczytowe wykorzystania. Aby zidentyfikować te urządzenia, należy utworzyć regułę, aby generować ostrzeżenia, gdy wykorzystanie przekracza 150 PSI.

Możesz również została zwracali alert krytyczny musi być wyzwalane, gdy średnia wilgotność **Chłodnica** urządzenia w ciągu ostatnich 5 minut jest większa niż 80% i temperatura **Chłodnica** urządzenia w ostatnich 5 minut jest większa niż 75 f stopni.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Wyświetlanie reguł w rozwiązaniu
> * Utwórz nową regułę
> * Utwórz nową regułę z wieloma warunkami
> * Edytuj istniejącą regułę
> * Usuwanie reguły

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, należy wdrożone wystąpienie zdalnego rozwiązanie monitorowania w ramach subskrypcji platformy Azure.

Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrożyć zdalnego monitorowania akcelerator rozwiązań](iot-accelerators-remote-monitoring-deploy.md) samouczka.

## <a name="view-the-rules-in-your-solution"></a>Wyświetlanie reguł w rozwiązaniu

**Reguły** w rozwiązaniu zostanie wyświetlona lista wszystkich istniejących zasad:

![Strona reguł i akcji](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

Aby wyświetlić tylko reguły, które dotyczą **Chłodnica** urządzeń, zastosuj filtr:

![Lista reguł filtrowania](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

Możesz wyświetlić więcej informacji na temat regułę i go edytować, po wybraniu na liście:

![Wyświetlanie szczegółów reguły](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

Aby wyłączyć, włączanie lub usuwanie co najmniej jednej reguły, wybierz wiele reguł na liście:

![Wybierz wiele reguł](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Utwórz nową regułę

Aby dodać nową regułę, która generuje ostrzeżenie podczas wykorzystania w **Chłodnica** urządzenia przekracza 150 PSI, wybierz **nową regułę**:

![Utwórz regułę](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Aby utworzyć regułę, należy użyć następujących wartości:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Nazwa reguły        | Ostrzeżenie Chłodnica                       |
| Opis      | Wykorzystanie Chłodnica przekracza 150 PSI |
| Grupy urządzeń     | **Chłodniach** grupy urządzeń             |
| Obliczenia      | Natychmiastowe                               |
| Pole warunek 1| pressure                              |
| Operator warunkowy 1 | Więcej niż                      |
| Wartość warunku 1    | 150                               |
| Poziom Serverity  | Ostrzeżenie                               |

Aby zapisać nową regułę, wybierz **Zastosuj**.

Można wyświetlić, gdy reguła jest wyzwalana na **reguły** strony lub na **pulpitu nawigacyjnego** strony.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Utwórz nową regułę z wieloma warunkami

Aby utworzyć nową regułę z wieloma warunkami, które generuje krytyczny alert średnia wilgotność **Chłodnica** urządzenia w ciągu ostatnich 5 minut jest większa niż 80% i temperatura **Chłodnica** urządzenia w ciągu ostatnich 5 minut jest większa niż 75 f stopni, wybierz **nową regułę**:

![Tworzenie reguły iloczyn](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Aby utworzyć regułę, należy użyć następujących wartości:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Nazwa reguły        | Chłodnica wilgoć i temp krytyczne    |
| Opis      | Wilgotności i temperatury są krytyczne |
| Grupy urządzeń     | **Chłodniach** grupy urządzeń             |
| Obliczenia      | Średnia                               |
| Okres czasu      | 5                                     |
| Pole warunek 1| wilgotność                              |
| Operator warunkowy 1 | Więcej niż                      |
| Wartość warunku 1    | 80                               |
| Poziom Serverity  | Krytyczne                              |

Aby dodać drugi warunek, kliknij pozycję "+ Dodaj warunek".

![Utwórz warunek 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Użyj następujących wartości na nowy warunek:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Pole warunku 2| Temperatury                           |
| Operator warunkowy 2 | Więcej niż                      |
| Wartość warunku 2    | 75                                |

Aby zapisać nową regułę, wybierz **Zastosuj**.

Można wyświetlić, gdy reguła jest wyzwalana na **reguły** strony lub na **pulpitu nawigacyjnego** strony.

## <a name="edit-an-existing-rule"></a>Edytuj istniejącą regułę

Aby wprowadzić zmianę do istniejącej reguły, wybierz je w listy zasad.

![Edytuj regułę](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono należy jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Wyświetlanie reguł w rozwiązaniu
> * Utwórz nową regułę
> * Edytuj istniejącą regułę
> * Usuwanie reguły

Teraz, kiedy znasz sposobu wykrywania problemów przy użyciu reguł oparte na wartościach progowych, Sugerowane następne kroki są Aby dowiedzieć się, jak:

* [Konfigurowanie urządzeń oraz zarządzanie nimi](./../iot-suite/iot-suite-remote-monitoring-manage.md).
* [Rozwiązywanie problemów i Korygowanie problemów z urządzeniami](./../iot-suite/iot-suite-remote-monitoring-maintain.md).
* [Testowanie rozwiązania z urządzeniami symulowane](../iot-suite/iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->