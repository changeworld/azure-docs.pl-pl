---
title: Dostosuj limity przydziału i ograniczenia w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak dostosować i zwiększyć limity przydziału i ograniczenia w ramach kont usługi Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d3601fd8c32c70cf828cd08fada71258ec8fa5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812680"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Dostosuj limity przydziału i ograniczenia w usłudze Azure Data Lake Analytics

Dowiedz się, jak dostosować i zwiększyć przydziały i limity, w ramach kont usługi Azure Data Lake Analytics (ADLA). Te limity, wiedząc, mogą pomóc Ci zrozumieć swoje zachowanie zadania U-SQL. Wszystkie limity przydziału są elastyczne, więc maksymalne limity można zwiększyć, kontaktując się z pomocą techniczną platformy Azure.

## <a name="azure-subscriptions-limits"></a>Limity subskrypcji platformy Azure

**Maksymalna liczba kont ADLA na subskrypcję na region:**  5

Jeśli próbujesz utworzyć konto ADLA szóstego, zostanie wyświetlony błąd "Osiągnięto maksymalną liczbę kont usługi Data Lake Analytics może (5) w regionie na podstawie nazwy subskrypcji". 

Jeśli chcesz wyjść poza ten limit, możesz spróbować tych opcji:
* Wybierz inny region, jeśli jest to odpowiednie
* Skontaktuj się z działem pomocy technicznej platformy Azure przez [otwierania biletu pomocy technicznej](#increase-maximum-quota-limits) można zażądać zwiększenia limitu przydziału.

## <a name="default-adla-account-limits"></a>Domyślne limity konta ADLA

**Maksymalna liczba jednostek analizy (Australia) dla danego konta:** 32

Jest to maksymalna liczba jednostek analizy, które można uruchomić jednocześnie w ramach Twojego konta. Jeśli łączna liczba uruchomionych jednostek alokacji na wszystkie zadania przekracza ten limit, nowsze są kolejkowane automatycznie. Na przykład:

* Jeśli masz tylko jedno zadanie z 32 jednostkach analizy, po przesłaniu sekundy zadania go będzie oczekiwać w kolejce zadań, dopiero po zakończeniu pierwszego zadania.
* Jeśli masz już cztery zadania uruchomione i jest używana w każdym 8 jednostek alokacji, gdy prześlesz piąty zadanie, które wymaga 8 oczekuje w kolejce zadań do momentu 8 jednostek analizy dostępnych jednostek analizy.

**Maksymalna liczba jednostek analizy (Australia) poszczególne zadania:** 32

Jest to domyślny maksymalną liczbę jednostek analizy, które można przypisać każdej poszczególnych zadań na Twoim koncie. Zadania, które są przypisane ponad ten limit zostanie odrzucone, chyba że osoby przesyłającej dotyczy przy użyciu zasad obliczeń (limit przesyłania zadania), co umożliwia im większej liczby jednostek analizy na zadanie. Górna granica ta wartość jest limit jednostek Alokacji dla konta.

**Maksymalna liczba równoczesnych zadań U-SQL na konto:** 20

Jest to maksymalna liczba zadań, które można uruchomić jednocześnie w ramach Twojego konta. Powyżej tej wartości nowszych są kolejkowane automatycznie.

## <a name="adjust-adla-account-limits"></a>Dostosuj limity konta ADLA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz istniejące konto ADLA.
3. Kliknij pozycję **Właściwości**.
4. Dopasuj wartości **maksymalną liczbę równoległości**, **maksymalna liczba uruchomionych zadań**, i **limity przesyłania zadań** do własnych potrzeb.

## <a name="increase-maximum-quota-limits"></a>Zwiększenie limitów maksymalny limit przydziału

Więcej informacji dotyczących ograniczeń platformy Azure można znaleźć w [dokumentację limitów platformy Azure specyficzne dla usługi](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Otwórz żądanie pomocy technicznej w witrynie Azure portal.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Wybierz typ problemu **przydziału**.
3. Wybierz swoje **subskrypcji** (Upewnij się, nie jest "próbne" subskrypcji).
4. Wybierz typ limitu przydziału **usługi Data Lake Analytics**.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stronie problem wyjaśnić żądane zwiększenie limitu z **szczegóły** z czego tej dodatkowej pojemności.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Sprawdź swoje informacje kontaktowe, a następnie utwórz żądanie obsługi.

Firma Microsoft monitoruje Twoje żądanie i próbuje obsłużyć możliwie jak Twoje potrzeby biznesowe.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
