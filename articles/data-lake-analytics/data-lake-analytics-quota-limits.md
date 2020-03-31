---
title: Dostosowywanie przydziałów i limitów w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak dostosować i zwiększyć przydziały i limity na kontach usługi Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644719"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Dostosowywanie przydziałów i limitów w usłudze Azure Data Lake Analytics

Dowiedz się, jak dostosować i zwiększyć przydział i limity na kontach usługi Azure Data Lake Analytics (ADLA). Znajomość tych limitów pomoże Ci zrozumieć zachowanie zadania U-SQL. Wszystkie limity przydziałów są miękkie, dzięki czemu można zwiększyć maksymalne limity, kontaktując się z pomocą techniczną platformy Azure.

## <a name="azure-subscriptions-limits"></a>Limity subskrypcji platformy Azure

**Maksymalna liczba kont ADLA na subskrypcję w regionie:** 5

Jeśli spróbujesz utworzyć szóste konto ADLA, pojawi się komunikat o błędzie "Osiągnąłeś maksymalną dozwoloną liczbę kont Usługi Data Lake Analytics (5) w regionie pod nazwą subskrypcji".

Jeśli chcesz przekroczyć ten limit, możesz wypróbować następujące opcje:
* wybierz inny region, jeśli jest to odpowiednie
* skontaktuj się z pomocą techniczną platformy [Azure, otwierając bilet pomocy technicznej,](#increase-maximum-quota-limits) aby zażądać zwiększenia przydziału.

## <a name="default-adla-account-limits"></a>Domyślne limity kont usługi ADLA

**Maksymalna liczba jednostek analitycznych (AU) na konto:** 250, domyślnie 32

Jest to maksymalna liczba aus, które można uruchomić jednocześnie na koncie. Jeśli całkowita liczba uruchomionych aus we wszystkich zadaniach przekracza ten limit, nowsze zadania są automatycznie umieszczane w kolejce. Przykład:

* Jeśli masz tylko jedno zadanie uruchomione z 32 aus, po przesłaniu drugiego zadania będzie czekać w kolejce zadań, aż pierwsze zadanie zostanie zakończone.
* Jeśli masz już cztery zadania uruchomione i każdy używa 8 AUs, po przesłaniu piątego zadania, które wymaga 8 AUs czeka w kolejce zadań, aż istnieje 8 AUs dostępne.

    ![Limity usługi Azure Data Lake Analytics i strona przydziału](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maksymalna liczba jednostek analitycznych (AU) na zadanie:** 250, domyślnie 32

Jest to maksymalna liczba jednostek AU, którą każde zadanie może być przypisane na twoim koncie. Zadania, które są przypisane więcej niż ten limit zostaną odrzucone, chyba że osoba przesyłającą ma wpływ na zasady obliczeń (limit przesyłania zadania), który daje im więcej jednostek AU na zadanie. Górna granica tej wartości to limit UA dla konta.

**Maksymalna liczba równoczesnych zadań U-SQL na konto:** 20

Jest to maksymalna liczba zadań, które można uruchomić jednocześnie na koncie. Powyżej tej wartości nowsze zadania są automatycznie umieszczane w kolejce.

## <a name="adjust-adla-account-limits"></a>Dostosowywanie limitów kont usługi ADLA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz istniejące konto ADLA.
3. Kliknij pozycję **Właściwości**.
4. Dostosuj wartości **maksymalnej liczby au,** **maksymalną liczbę uruchomionych zadań**i **limity przesyłania zadań** do własnych potrzeb.

## <a name="increase-maximum-quota-limits"></a>Zwiększenie maksymalnych limitów przydziałów

Więcej informacji na temat limitów platformy Azure można znaleźć w [dokumentacji limitów specyficznych dla usługi platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)

1. Otwórz żądanie pomocy technicznej w witrynie Azure portal.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Wybierz typ problemu **Przydział**.
3. Wybierz **subskrypcję** (upewnij się, że nie jest to subskrypcja "próbna").
4. Wybierz typ przydziału **Data Lake Analytics**.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stronie problemu wyjaśnij żądany limit zwiększenia, **podaj szczegóły,** dlaczego potrzebujesz tej dodatkowej pojemności.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Zweryfikuj informacje kontaktowe i utwórz żądanie pomocy technicznej.

Firma Microsoft sprawdza twoje żądanie i stara się jak najszybciej zaspokoić potrzeby biznesowe.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
