---
title: Zarządzanie zasadami konta usługi Azure Data Lake Analytics
description: Dowiedz się, jak używać zasad konta do kontrolowania użycia konta Usługi Data Lake Analytics, takiego jak maksymalna liczba analiz i maksymalna liczba zadań.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966439"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu zasad konta

Zasady konta ułatwiają kontrolowanie sposobu wykorzystania zasobów konta usługi Azure Data Lake Analytics. Te zasady umożliwiają kontrolowanie kosztów korzystania z usługi Azure Data Lake Analytics. Na przykład za pomocą tych zasad można zapobiec nieoczekiwane skoki kosztów, ograniczając liczbę jednostek AUs konto może jednocześnie używać.

## <a name="account-level-policies"></a>Zasady na poziomie konta

Te zasady dotyczą wszystkich zadań na koncie Usługi Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maksymalna liczba procesorów analizy na koncie usługi Data Lake Analytics
Zasady sterują całkowitą liczbą jednostek analitycznych (AU), z których może korzystać twoje konto Usługi Data Lake Analytics. Domyślnie wartość jest ustawiona na 250. Na przykład jeśli ta wartość jest ustawiona na 250 AUs, można mieć jedno zadanie uruchomione z 250 AUs przypisane do niego lub 10 zadań uruchomionych z 25 AUs każdy. Dodatkowe zadania, które są przesyłane są umieszczane w kolejce, dopóki uruchomione zadania nie zostaną zakończone. Po zakończeniu uruchamiania zadań au są zwalniane dla zadań w kolejce do uruchomienia.

Aby zmienić liczbę analiz użytkownika konta Usługi Data Lake Analytics:

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Limity i zasady**.
3. W obszarze **Maksymalna liczba AUs**przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym. 
4. Kliknij przycisk **Zapisz**.

> [!NOTE]
> Jeśli potrzebujesz więcej niż domyślne (250) aus, w portalu, kliknij **Pomoc + Wsparcie,** aby przesłać żądanie pomocy technicznej. Liczba au dostępnych na koncie Data Lake Analytics może zostać zwiększona.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maksymalna liczba zadań, które można uruchamiać jednocześnie
Ta zasada ogranicza liczbę zadań można uruchomić jednocześnie. Domyślnie ta wartość jest ustawiona na 20. Jeśli usługa Data Lake Analytics ma dostępne au, nowe zadania są zaplanowane do natychmiastowego uruchomienia, dopóki całkowita liczba uruchomionych zadań osiągnie wartość tej zasady. Po osiągnięciu maksymalnej liczby zadań, które można uruchomić jednocześnie, kolejne zadania są umieszczane w kolejce w kolejności priorytetu, aż do jednego lub więcej uruchomionych zadań zakończyć (w zależności od dostępnych aus).

Aby zmienić liczbę zadań, które można uruchomić jednocześnie:

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Limity i zasady**.
3. W obszarze **Maksymalna liczba uruchomionych zadań**przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym. 
4. Kliknij przycisk **Zapisz**.

> [!NOTE]
> Jeśli musisz uruchomić więcej niż domyślną (20) liczbę zadań, w portalu kliknij przycisk **Pomoc+Obsługa,** aby przesłać żądanie pomocy technicznej. Liczba zadań, które można uruchomić jednocześnie na koncie Usługi Data Lake Analytics, może zostać zwiększona.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak długo przechowywać metadane i zasoby zadania 
Gdy użytkownicy uruchamiają zadania U-SQL, usługa Data Lake Analytics przechowuje wszystkie powiązane pliki. Pliki te obejmują skrypt U-SQL, pliki DLL, do których odwołuje się skrypt U-SQL, skompilowane zasoby i statystyki. Pliki znajdują się w folderze /system/ domyślnego konta usługi Azure Data Lake Storage. Ta zasada określa, jak długo te zasoby są przechowywane, zanim zostaną automatycznie usunięte (wartość domyślna to 30 dni). Można użyć tych plików do debugowania i do dostrajania wydajności zadań, które zostaną ponownie uruchom w przyszłości.

Aby zmienić czas przechowywania metadanych i zasobów zadania:

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Limity i zasady**.
3. W obszarze **Dni zachować kwerendy o zadaniach przesuń**suwak, aby zaznaczyć wartość, lub wprowadź wartość w polu tekstowym.  
4. Kliknij przycisk **Zapisz**.

## <a name="job-level-policies"></a>Zasady dotyczące poziomu zadań

Zasady na poziomie zadania umożliwiają kontrolowanie maksymalnych jednostek AU i maksymalnego priorytetu, jaki poszczególni użytkownicy (lub członkowie określonych grup zabezpieczeń) mogą ustawiać na zadaniach, które przesyłają. Ta zasada umożliwia kontrolowanie kosztów ponoszonych przez użytkowników. Pozwala również kontrolować wpływ zaplanowanych zadań na zadania produkcyjne o wysokim priorytecie, które są uruchomione na tym samym koncie usługi Data Lake Analytics.

Usługa Data Lake Analytics ma dwie zasady, które można ustawić na poziomie zadania:

* **Limit UA na zadanie:** Użytkownicy mogą przesyłać tylko zadania, które mają do tej liczby AUs. Domyślnie ten limit jest taki sam jak maksymalny limit UA dla konta.
* **Priorytet:** Użytkownicy mogą przesyłać tylko zadania o priorytecie niższym lub równym tej wartości. Wyższa liczba wskazuje niższy priorytet. Domyślnie ten limit jest ustawiony na 1, co jest najwyższym możliwym priorytetem.

Na każdym koncie jest ustawiona domyślna zasada. Domyślna zasada ma zastosowanie do wszystkich użytkowników konta. Można utworzyć dodatkowe zasady dla określonych użytkowników i grup. 

> [!NOTE]
> Zasady na poziomie konta i zasady na poziomie zadania mają zastosowanie jednocześnie.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Dodawanie zasad dla określonego użytkownika lub grupy

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Limity i zasady**.
3. W obszarze **Limity przesyłania zadań**kliknij przycisk **Dodaj zasady.** Następnie wybierz lub wprowadź następujące ustawienia:
    1. **Nazwa zasady obliczeniowej:** Wprowadź nazwę zasad, aby przypomnieć o celu zasad.
    2. **Wybierz użytkownika lub grupę**: Wybierz użytkownika lub grupę, do dla które stosuje się te zasady.
    3. **Ustawianie limitu AU zadania:** Ustawianie limitu UA, który ma zastosowanie do wybranego użytkownika lub grupy.
    4. **Ustaw limit priorytetu:** Ustaw limit priorytetu, który ma zastosowanie do wybranego użytkownika lub grupy.

4. Kliknij przycisk **OK**.

5. Nowa zasada jest wyświetlana w tabeli **Domyślne** zasady w obszarze **Limity przesyłania zadań**. 

### <a name="delete-or-edit-an-existing-policy"></a>Usuwanie lub edytowanie istniejącej zasady

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Limity i zasady**.
3. W obszarze **Limity przesyłania zadań**znajdź zasady, które chcesz edytować.
4.  Aby wyświetlić opcje **Usuwanie** i **edycja,** w prawej `...`kolumnie tabeli kliknij pozycję .

## <a name="additional-resources-for-job-policies"></a>Dodatkowe zasoby dla zasad zatrudnienia
* [Wpis w blogu przegląd zasad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Wpis w blogu dotyczące zasad dotyczących poziomu konta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Wpis w blogu dotyczące zasad dotyczących poziomu zadania](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu usługi Azure PowerShell](data-lake-analytics-manage-use-powershell.md)