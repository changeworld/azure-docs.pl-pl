---
title: Zarządzanie zasadami usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Informacje o sposobie kontrolowania użycia konta usługi Data Lake Analytics przy użyciu zasad.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/30/2018
ms.author: saveenr
ms.openlocfilehash: db2712f0b57f2a5d60eae4aec21a4070c7a907a3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Azure Data Lake Analytics przy użyciu zasad zarządzania

Za pomocą zasad konta, można kontrolować sposób zasobów konta usługi Azure Data Lake Analytics są używane. Te zasady umożliwiają kontrolowanie kosztów przy użyciu usługi Azure Data Lake Analytics. Na przykład przy użyciu tych zasad można zapobiec nagłego nieoczekiwany koszt przez ograniczenie liczby AUs konta można jednocześnie używać.

## <a name="account-level-policies"></a>Zasady na poziomie konta

Te zasady są stosowane do wszystkich zadań w ramach konta usługi Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maksymalna liczba AUs w ramach konta usługi Data Lake Analytics
Zasady regulują całkowita liczba jednostek Analytics (AUs) można używać konta usługi Data Lake Analytics. Domyślna wartość wynosi 250. Na przykład, jeśli ta wartość jest równa 250 AUs, może mieć jedno zadanie uruchomione z 250 AUs przypisane do lub 10 zadań uruchamianych z 25 AUs każdego. Dodatkowe zadania, które są przesyłane są umieszczane w kolejce do momentu zakończenia są wykonywane zadania. Po zakończeniu operacji wykonywanych zadań są AUs są zwalniane dla zadań w kolejce do uruchomienia.

Aby zmienić liczbę AUs konto usługi Data Lake Analytics:

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **maksymalna AUs**, przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym. 
4. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Jeśli potrzebujesz więcej niż domyślny (250) AUs, w portalu kliknij **pomoc + Obsługa** można przesłać żądania pomocy technicznej. Można zwiększyć liczbę AUs dostępne w ramach konta usługi Data Lake Analytics.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maksymalna liczba zadań, które można uruchomić jednocześnie
Zasady określa, ile zadania może uruchamiać jednocześnie. Domyślnie ta wartość wynosi 20. Jeśli usługi Data Lake Analytics ma AUs dostępne, nowe zadania zaplanowane do uruchomienia natychmiast, dopóki liczba uruchomionych zadań osiągnie wartość tej zasady. Po osiągnięciu maksymalna liczba zadań, które można uruchomić jednocześnie, kolejne zadania są umieszczane w kolejce w kolejności priorytetu do momentu ukończenia co najmniej jedno zadanie uruchomione (w zależności od dostępności Australia).

Aby zmienić liczbę zadań, które można uruchomić jednocześnie:

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **maksymalny numer z uruchomione zadania**, przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym. 
4. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Jeśli musisz uruchomić więcej niż domyślny (20) liczba zadań, w portalu, kliknij przycisk **pomoc + Obsługa** można przesłać żądania pomocy technicznej. Można zwiększyć liczbę zadań, które można uruchomić jednocześnie w ramach konta usługi Data Lake Analytics.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak długo, aby zachować zadanie metadanych i zasobów 
Po wykonaniu zadań U-SQL usługi Data Lake Analytics zachowuje wszystkie powiązane pliki. Powiązane pliki obejmują skrypt U-SQL, pliki DLL odwołuje się do skryptu U-SQL, skompilowanych zasobów i statystyki. Pliki znajdują się w folderze /system/ domyślnego konta usługi Azure Data Lake Storage. Ta zasada kontroluje, jak długo są przechowywane tych zasobów, zanim zostaną automatycznie usunięte (wartość domyślna to 30 dni). Można używać tych plików do debugowania i dostrajania wydajności zadań, które będzie można ponownie uruchomić w przyszłości.

Aby zmienić czas przechowywania metadanych zadania i zasoby:

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **dni, aby zachować zadanie odpytuje**, przesuń suwak, aby wybrać wartość, lub wprowadź wartość w polu tekstowym.  
4. Kliknij pozycję **Zapisz**.

## <a name="job-level-policies"></a>Zasady na poziomie zadania

Za pomocą zasad na poziomie zadania można kontrolować maksymalny AUs i maksymalny priorytet poszczególnych użytkowników (lub członków określonych grup zabezpieczeń), które można ustawić dla zadania, które przesyłają. Ta zasada umożliwia kontrolę kosztów ponoszonych przez użytkowników. Umożliwia również kontroli wpływ, jaki zaplanowane zadania mogą mieć na zadania produkcji o wysokim priorytecie, które są uruchomione na tym samym koncie usługi Data Lake Analytics.

Data Lake Analytics ma dwie zasady, które można ustawić na poziomie zadania:

* **Australia limit dla zadania**: użytkownicy, można kierować tylko zadania, które mają tę liczbę AUs. Domyślnie ten limit, jest taka sama jak maksymalny limit Australia dla konta.
* **Priorytet**: użytkownicy, można kierować tylko zadania, które mają priorytet niższy niż lub równa tej wartości. Większa liczba wskazuje o niższym priorytecie. Domyślnie ten limit wynosi 1, która jest najwyższy możliwy priorytet.

Brak domyślnych zasad ustawić dla każdego konta. Domyślna zasada ma zastosowanie do wszystkich użytkowników, konta. Dodatkowe zasady można ustawić dla poszczególnych użytkowników i grup. 

> [!NOTE]
> Zasady na poziomie konta i zasad na poziomie zadania się jednocześnie.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Dodaj zasady dla określonego użytkownika lub grupy

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **limitów przesyłanie zadań**, kliknij przycisk **Dodaj zasady** przycisku. Następnie wybierz lub wprowadź następujące ustawienia:
    1. **Nazwa zasad obliczeniowe**: Wprowadź nazwę zasady w celu odnotowania zastosowania zasad.
    2. **Wybierz użytkownika lub grupy**: Wybierz użytkownika lub grupy, które dotyczą te zasady.
    3. **Ustaw Limit Australia zadania**: Ustaw limit Australia, która ma zastosowanie do wybranego użytkownika lub grupę.
    4. **Ustaw Limit priorytet**: Ustaw limit priorytet, która ma zastosowanie do wybranego użytkownika lub grupę.

4. Kliknij przycisk **OK**.

5. Nowa zasada zostanie wyświetlona w **domyślne** w sekcji tabela zasad **limitów przesyłanie zadań**. 

### <a name="delete-or-edit-an-existing-policy"></a>Usuń lub Edytuj istniejące zasady

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij pozycję **Właściwości**.
3. W obszarze **limitów przesyłanie zadań**, Znajdź zasady, którą chcesz edytować.
4.  Aby wyświetlić **usunąć** i **Edytuj** kliknij przycisk Opcje w prawej kolumnie tabeli, `...`.

## <a name="additional-resources-for-job-policies"></a>Dodatkowe zasoby dotyczące zasad zadania
* [Omówienie zasad wpis w blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Wpis w blogu zasad na poziomie konta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Zasady na poziomie zadania wpis w blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

