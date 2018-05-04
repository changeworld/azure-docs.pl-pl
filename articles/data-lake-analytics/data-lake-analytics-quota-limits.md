---
title: Limity przydziału usługi Azure Data Lake Analytics
description: Dowiedz się, jak dostosować i zwiększyć limity przydziału na kontach usługi Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: c6c39fb0810a7ea8b6facec1ca80da25d2253329
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limity przydziału usługi Azure Data Lake Analytics

Dowiedz się, jak dostosować i zwiększyć limity przydziału na kontach usługi Azure Data Lake Analytics (ADLA). Znajomość tych limitów mogą ułatwić zrozumienie zachowania zadania z języka U-SQL. Wszystkie limity przydziału są miękkie, więc maksymalna limity można zwiększyć przez skontaktowaniu się z pomocą techniczną platformy Azure.

## <a name="azure-subscriptions-limits"></a>Limity subskrypcji platformy Azure

**Maksymalna liczba ADLA konta dla subskrypcji dla regionu:** 5

Jeśli próbujesz utworzyć konto ADLA szóstego, wystąpi błąd "Osiągnięto maksymalną liczbę kont usługi Data Lake Analytics może (5) w regionie, w obszarze Nazwa subskrypcji". 

Jeśli chcesz wykracza poza ten limit, możesz spróbować te opcje:
* Wybierz inny region, jeśli jest to odpowiednie
* Skontaktuj się z pomocą techniczną platformy Azure przez [otwarcie biletu pomocy technicznej](#increase-maximum-quota-limits) Aby zażądać zwiększenia limitu przydziału.

## <a name="adla-account-limits"></a>Limity konta ADLA

**Maksymalna liczba jednostek Analytics (AUs) dla konta:** 250

Jest to maksymalna liczba AUs, które można uruchomić jednocześnie w ramach Twojego konta. Jeśli całkowita liczba uruchamianie AUs we wszystkich zadań przekracza ten limit, automatycznie są kolejkowane zadania nowsza. Na przykład:

* Jeśli masz tylko jedno zadanie uruchomione z 250 AUs, podczas przesyłania drugiej zadania go będzie czekać w kolejce zadań dopiero po zakończeniu pierwszego zadania.
* Jeśli masz pięć zadania uruchomione i każdego używa 50 AUs, podczas przesyłania szóstego zadanie, które wymaga 20 AUs oczekuje w kolejce zadań, dopóki istnieją 20 AUs dostępne.

**Maksymalna liczba jednoczesnych zadań U-SQL dla danego konta:** 20

Jest to maksymalna liczba zadań, które można uruchomić jednocześnie w ramach Twojego konta. Powyżej tej wartości automatycznie są kolejkowane zadania nowsza.

## <a name="adjust-adla-quota-limits-per-account"></a>Dostosuj ADLA limity przydziału dla konta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz istniejące konto ADLA.
3. Kliknij pozycję **Właściwości**.
4. Dostosuj **równoległości** i **równoczesnych zadań** w zależności od potrzeb.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Zwiększ limit przydziału maksymalnej

1. W portalu Azure, otwórz żądanie pomocy technicznej.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Wybierz typ problemu **przydziału**.
3. Wybierz użytkownika **subskrypcji** (Upewnij się, nie jest "wersję próbną").
4. Wybierz typ przydziału **usługi Data Lake Analytics**.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stronie problem opisano żądanego Zwiększ limit z **szczegóły** z czego potrzebujesz tej dodatkowej pojemności.

    ![Strona portalu usługi Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Sprawdź informacje kontaktowe i Utwórz żądanie obsługi.

Firma Microsoft sprawdzenia żądania przez i próbuje jak najszybciej dostosowania potrzeb biznesowych.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
