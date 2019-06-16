---
title: Zarządzanie zasadami usługi Azure Data Lake Analytics
description: Dowiedz się, jak używać zasad do kontrolowania użycia konta usługi Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 64095f6706bb978cd33b8fe7833fe4e65fc3b0f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813432"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Zarządzanie przy użyciu zasad usługi Azure Data Lake Analytics

Za pomocą zasad konta, możesz kontrolować sposób zasoby konta usługi Azure Data Lake Analytics są używane. Te zasady umożliwiają kontrolowanie kosztów za pomocą usługi Azure Data Lake Analytics. Na przykład za pomocą tych zasad można uniemożliwić nieoczekiwane koszty wzrostów poprzez ograniczenie liczby jednostek analizy, konta mogą jednocześnie używać.

## <a name="account-level-policies"></a>Zasady na poziomie konta

Te zasady mają zastosowanie do wszystkich zadań w ramach konta usługi Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maksymalna liczba jednostek analizy na koncie usługi Data Lake Analytics
Zasady regulują całkowita liczba jednostek analizy (Australia) można używać konta usługi Data Lake Analytics. Domyślnie wartość jest równa 250. Na przykład, jeśli ta wartość jest równa 250 jednostek analizy, może mieć jedno zadanie z 250 jednostek analizy przypisanych do niej lub 10 zadań uruchomiony z 25 jednostek alokacji każdego. Dodatkowe zadania, które są przesyłane są umieszczane w kolejce do momentu zakończenia są wykonywane zadania. Po zakończeniu uruchomionych zadań, jednostek alokacji są zwalniane dla tych zadań w kolejce do uruchomienia.

Aby zmienić liczbę jednostek alokacji dla konta usługi Data Lake Analytics:

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **maksymalną liczbę równoległości**, przesuń suwak, aby wybrać wartość lub wprowadź wartość w polu tekstowym. 
4. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Jeśli potrzebujesz więcej niż domyślny (250) jednostkach analizy, w portalu kliknij pozycję **Pomoc i obsługę techniczną** można przesłać żądanie pomocy technicznej. Można zwiększyć liczbę jednostek analizy dostępnych w ramach konta usługi Data Lake Analytics.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maksymalna liczba zadań, które można uruchomić jednocześnie
Zasady regulują, jak wiele zadań można uruchamiać w tym samym czasie. Domyślnie ta wartość jest równa 20. Jeśli usługi Data Lake Analytics ma dostępne jednostki alokacji, nowe zadania są planowane do uruchomienia natychmiast, dopóki jest to łączna liczba uruchomionych zadań osiągnie wartość niniejszych zasad. W przypadku osiągnięcia maksymalną liczbę zadań, które można uruchomić jednocześnie kolejne zadania są umieszczane w kolejce w kolejności priorytetu do momentu ukończenia co najmniej jedno zadanie uruchomione w (w zależności od dostępności Australia).

Aby zmienić liczbę zadań, które można uruchomić jednocześnie:

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **maksymalnej liczby z uruchamiania zadań**, przesuń suwak, aby wybrać wartość lub wprowadź wartość w polu tekstowym. 
4. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Jeśli musisz uruchomić więcej niż domyślny (20) liczbę zadań, w portalu, kliknij przycisk **Pomoc i obsługę techniczną** można przesłać żądanie pomocy technicznej. Można zwiększyć liczbę zadań, które można uruchomić równocześnie na Twoim koncie usługi Data Lake Analytics.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak długo do metadanych zadania keep i zasobów 
Po wykonaniu zadań U-SQL usługi Data Lake Analytics zachowuje wszystkie powiązane pliki. Powiązane pliki obejmują skrypt U-SQL, pliki DLL, do którego odwołuje się skrypt U-SQL, skompilowany zasoby i statystyki. Pliki znajdują się w folderze /system/ domyślnego konta usługi Azure Data Lake Storage. Ta zasada kontroluje, jak długo są przechowywane te zasoby, zanim zostaną automatycznie usunięte (wartość domyślna to 30 dni). Te pliki można użyć do debugowania i dostrajania wydajności zadań, które będzie można ponownie uruchomić w przyszłości.

Aby zmienić czas przechowywania metadanych zadania i zasoby:

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **dni, aby zachować zadanie odpytuje**, przesuń suwak, aby wybrać wartość lub wprowadź wartość w polu tekstowym.  
4. Kliknij pozycję **Zapisz**.

## <a name="job-level-policies"></a>Zasady na poziomie zadania

Za pomocą zasad na poziomie zadania można kontrolować, maksymalna liczba jednostek alokacji i maksymalny priorytet poszczególnych użytkowników (lub członkom określonych grup zabezpieczeń), które można ustawić dla zadań, które wysyłają. Ta zasada umożliwia kontrolowanie kosztów poniesionych przez użytkowników. Umożliwia także kontroli wpływ, jaki zaplanowane zadania może mieć na zadania o wysokim priorytecie w środowisku produkcyjnym, które są uruchomione na tym samym koncie usługi Data Lake Analytics.

Usługa Data Lake Analytics ma dwie zasady, które można ustawić na poziomie zadania:

* **Limit jednostek Alokacji na zadanie**: Użytkownicy mogą przesyłać tylko zadania, które trzeba tej liczby jednostek analizy. Domyślnie ten limit jest taka sama jak maksymalny limit jednostek Alokacji dla konta.
* **Priorytet**: Użytkownicy mogą przesyłać tylko zadania, które mają priorytet niższy niż lub równa tej wartości. Większa liczba wskazuje o niższym priorytecie. Domyślnie ten limit jest ustawiona na 1, która jest najwyższy możliwy priorytet.

Brak domyślnych zasad ustawić dla każdego konta. Domyślne zasady mają zastosowanie do wszystkich użytkowników, konta. Dodatkowe zasady można ustawić dla konkretnych użytkowników i grup. 

> [!NOTE]
> Zasady na poziomie konta i zasady na poziomie zadania są stosowane jednocześnie.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Dodawanie zasad dla określonego użytkownika lub grupy

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **limity przesyłania zadań**, kliknij przycisk **Dodaj zasady** przycisku. Następnie wybierz lub wprowadź następujące ustawienia:
    1. **Nazwa zasad obliczeń**: Wprowadź nazwę zasad, przypomnienia o celem zasad.
    2. **Wybierz użytkownika lub grupę**: Wybierz użytkownika lub grupy, których dotyczą te zasady.
    3. **Ustaw Limit jednostek Alokacji zadania**: Ustaw limit jednostek Alokacji, która ma zastosowanie do wybranego użytkownika lub grupę.
    4. **Ustaw Limit priorytetu**: Ustaw limit priorytetu, która ma zastosowanie do wybranego użytkownika lub grupę.

4. Kliknij przycisk **OK**.

5. Nowe zasady są wyświetlane w **domyślne** zasad tabeli, w obszarze **limity przesyłania zadań**. 

### <a name="delete-or-edit-an-existing-policy"></a>Usuń lub Edytuj istniejące zasady

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **limity przesyłania zadań**, Znajdź zasady, którą chcesz edytować.
4.  Aby wyświetlić **Usuń** i **Edytuj** opcje, w pierwszej kolumnie tabeli, kliknij przycisk `...`.

## <a name="additional-resources-for-job-policies"></a>Dodatkowe zasoby dotyczące zasad zadania
* [Wpis w blogu Przegląd zasad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Wpis w blogu zasad na poziomie konta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Wpis w blogu zasad na poziomie zadania](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

