---
title: Dostosuj przydziały i limity w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak dostosować i zwiększyć przydziały i limity konta usługi Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
editor: jasonwhowell
manager: kfile
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: e493c45ef49d370bbf7d007e039b4102aaa3cdf0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623982"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Dostosuj przydziały i limity w usłudze Azure Data Lake Analytics

Dowiedz się, jak dostosować i zwiększyć przydziały i limity konta usługi Azure Data Lake Analytics (ADLA). Znajomość tych limitów mogą ułatwić zrozumienie zachowania zadania z języka U-SQL. Wszystkie limity przydziału są miękkie, więc maksymalna limity można zwiększyć przez skontaktowaniu się z pomocą techniczną platformy Azure.

## <a name="azure-subscriptions-limits"></a>Limity subskrypcji platformy Azure

**Maksymalna liczba ADLA konta dla subskrypcji dla regionu:** 5

Jeśli próbujesz utworzyć konto ADLA szóstego, wystąpi błąd "Osiągnięto maksymalną liczbę kont usługi Data Lake Analytics może (5) w regionie, w obszarze Nazwa subskrypcji". 

Jeśli chcesz wykracza poza ten limit, możesz spróbować te opcje:
* Wybierz inny region, jeśli jest to odpowiednie
* Skontaktuj się z pomocą techniczną platformy Azure przez [otwarcie biletu pomocy technicznej](#increase-maximum-quota-limits) Aby zażądać zwiększenia limitu przydziału.

## <a name="default-adla-account-limits"></a>Domyślne limity konta ADLA

**Maksymalna liczba jednostek Analytics (AUs) dla konta:** 32

Jest to maksymalna liczba AUs, które można uruchomić jednocześnie w ramach Twojego konta. Jeśli całkowita liczba uruchamianie AUs we wszystkich zadań przekracza ten limit, automatycznie są kolejkowane zadania nowsza. Na przykład:

* Jeśli masz tylko jedno zadanie uruchomione z 32 AUs, podczas przesyłania drugiej zadania go będzie czekać w kolejce zadań dopiero po zakończeniu pierwszego zadania.
* Jeśli masz już cztery zadania uruchomione i każdego używa 8 AUs, podczas przesyłania piątej zadanie, które wymaga 8 AUs oczekuje w kolejce zadań, dopóki istnieją 8 AUs dostępne.

**Maksymalna liczba jednostek Analytics (AUs) na zadanie:** 32

To jest domyślna maksymalna liczba AUs, które można przypisać każdego poszczególnych zadań w ramach Twojego konta. Zadania, które są przypisane przekracza ten limit zostaną odrzucone, chyba że obiekt przesyłający zależy od zasad obliczeń (limit przesyłania zadania), co umożliwia im więcej AUs na zadanie. Górna granica ta wartość jest limit Australia dla konta.

**Maksymalna liczba jednoczesnych zadań U-SQL dla danego konta:** 20

Jest to maksymalna liczba zadań, które można uruchomić jednocześnie w ramach Twojego konta. Powyżej tej wartości automatycznie są kolejkowane zadania nowsza.

## <a name="adjust-adla-account-limits"></a>Dostosuj limity konta ADLA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz istniejące konto ADLA.
3. Kliknij pozycję **Właściwości**.
4. Dostosuj wartości dla **maksymalna AUs**, **maksymalna liczba uruchomionych zadań**, i **zadania przesyłania limity** w zależności od potrzeb.

## <a name="increase-maximum-quota-limits"></a>Zwiększ limit przydziału maksymalnej

Więcej informacji na temat limitów Azure można znaleźć w [specyficzne dla usługi Azure ogranicza dokumentacji](../azure-subscription-service-limits.md#data-lake-analytics-limits).

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
