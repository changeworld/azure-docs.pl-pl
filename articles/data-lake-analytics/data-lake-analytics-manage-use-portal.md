---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać kont usługi Data Lake Analytics, źródła danych, użytkowników i zadań.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 99b638279b38ce3b583208ec0b80617f9046a6d7
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu portalu Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzanie kontami, źródłami danych konta, użytkowników i zadania usługi Azure Data Lake Analytics przy użyciu portalu Azure. Aby wyświetlić tematy dotyczące zarządzania o przy użyciu innych narzędzi, kliknij kartę w górnej części strony.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Zarządzanie kontami usługi Data Lake Analytics

### <a name="create-an-account"></a>Tworzenie konta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** > **analizy i analiza** > **usługi Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów: 
   1. **Nazwa**: Nazwa konta usługi Data Lake Analytics.
   2. **Subskrypcja**: używane jako konto subskrypcji Azure.
   3. **Grupa zasobów**: Grupa zasobów Azure, w której chcesz utworzyć konto. 
   4. **Lokalizacja**: centrum danych Azure dla konta usługi Data Lake Analytics. 
   5. **Data Lake Store**: domyślny magazyn do użycia dla konta usługi Data Lake Analytics. Konto usługi Azure Data Lake Store i konta usługi Data Lake Analytics musi być w tej samej lokalizacji.
4. Kliknij przycisk **Utwórz**. 

### <a name="delete-a-data-lake-analytics-account"></a>Usuwanie konta usługi Data Lake Analytics

Aby usunąć konto usługi Data Lake Analytics, usuń jego domyślne konto usługi Data Lake Store.

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij polecenie **Usuń**.
3. Wpisz nazwę konta.
4. Kliknij polecenie **Usuń**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Zarządzaj źródłami danych

Data Lake Analytics obsługuje następujące źródła danych:

* Data Lake Store
* Azure Storage

Eksplorator danych umożliwia przeglądanie źródeł danych i wykonywać operacje zarządzania pliku podstawowego. 

### <a name="add-a-data-source"></a>Dodawanie źródła danych

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij przycisk **źródeł danych**.
3. Kliknij przycisk **Dodaj źródło danych**.
    
   * Aby dodać konto usługi Data Lake Store, należy nazwę konta i dostęp do konta, aby można było go zapytania.
   * Aby dodać magazyn obiektów Blob platformy Azure, należy konto magazynu i klucza konta. Aby je znaleźć, przejdź do konta magazynu w portalu.

## <a name="set-up-firewall-rules"></a>Konfigurowanie reguł zapory

Data Lake Analytics do dalszego blokowania dostępu służy do swojego konta usługi Data Lake Analytics na poziomie sieci. Można włączyć zapory, podaj adres IP lub zdefiniuj zakres adresów IP dla zaufanych klientów. Po włączeniu tych środków tylko w przypadku klientów, którzy mają adresy IP ze zdefiniowanego zakresu można połączyć z magazynem.

Jeśli połączenie innymi usługami Azure, takich jak fabryki danych Azure lub maszyn wirtualnych, do konta usługi Data Lake Analytics, upewnij się, że **Zezwalaj usług Azure** włączono **na**. 

### <a name="set-up-a-firewall-rule"></a>Konfigurowanie reguły zapory

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. W menu po lewej stronie kliknij **zapory**.

## <a name="add-a-new-user"></a>Dodaj nowego użytkownika

Można użyć **Kreatora dodawania użytkownika** można łatwo udostępnić nowych użytkowników do usługi Data Lake.

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Po lewej stronie w obszarze **wprowadzenie**, kliknij przycisk **Kreatora dodawania użytkownika**.
3. Wybierz użytkownika, a następnie kliknij przycisk **wybierz**.
4. Wybierz rolę, a następnie kliknij przycisk **wybierz**. Aby skonfigurować nowy developer do użycia usługi Azure Data Lake, wybierz **Data Lake Analytics Developer** roli.
5. Wybierz z listy kontroli dostępu (ACL) dla baz danych U-SQL. Po zakończeniu wybrane opcje, kliknij przycisk **wybierz**.
6. Wybierz z listy kontroli dostępu dla plików. Domyślny magazyn, nie zmieniaj list ACL dla folderu głównego "/" i folderu/System. Kliknij pozycję **Wybierz**.
7. Przejrzyj wybrane ustawienia, a następnie kliknij przycisk **Uruchom**.
8. Po zakończeniu działania kreatora, kliknij przycisk **gotowe**.

## <a name="manage-role-based-access-control"></a>Zarządzanie kontrolą dostępu opartą na rolach

Jak innymi usługami Azure kontroli dostępu opartej na rolach (RBAC) służy do kontrolowania sposobu interakcji użytkowników z usługą.

Standardowe role RBAC mają następujące możliwości:
* **Właściciel**: można przesłać zadania, monitorowania zadań anulować zadania z dowolnego użytkownika i skonfiguruj konto.
* **Współautor**: można przesłać zadania, monitorowania zadań anulować zadania z dowolnego użytkownika i skonfiguruj konto.
* **Czytnik**: można monitorować zadania.

Użyj roli Data Lake Analytics Developer producentów U-SQL do korzystania z usługi Data Lake Analytics. Można użyć roli Data Lake Analytics Developer, aby:
* Przesyłanie zadań.
* Monitorowanie stanu zadania i postęp zadań wysłanego przez każdego użytkownika.
* Zobacz skryptów U-SQL z zadań wysłanego przez każdego użytkownika.
* Anulowanie tylko własnych zadań.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Dodawanie użytkowników lub grup zabezpieczeń do konta usługi Data Lake Analytics

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij przycisk **(IAM) kontroli dostępu** > **Dodaj**.
3. Wybierz rolę.
4. Dodawanie użytkownika.
5. Kliknij przycisk **OK**.

>[!NOTE]
>Jeśli użytkownik lub grupa zabezpieczeń wymaga przesyłania zadań, muszą uprawnienie dla konta magazynu. Aby uzyskać więcej informacji, zobacz [zabezpieczyć dane przechowywane w usłudze Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Zarządzanie zadaniami

### <a name="submit-a-job"></a>Prześlij zadanie

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.

2. Kliknij przycisk **nowe zadanie**. Dla każdego zadania należy skonfigurować:

    1. **Nazwa zadania**: Nazwa zadania.
    2. **Priorytet**: niższych numerach mają wyższy priorytet. Dwa zadania są umieszczane w kolejce, pierwszy uruchamia z niższą wartość priorytetu.
    3. **Równoległość**: Maksymalna liczba procesów obliczeń do zarezerwowania dla tego zadania.

3. Kliknij przycisk **Prześlij zadanie**.

### <a name="monitor-jobs"></a>Monitorowanie zadań

1. W portalu Azure przejdź do swojego konta usługi Data Lake Analytics.
2. Kliknij przycisk **wyświetlić wszystkie zadania**. Jest wyświetlana lista wszystkich zadań ostatnio zakończone i active w ramach konta.
3. Opcjonalnie kliknij **filtru** ułatwia znajdowanie zadania według **zakres czasu**, **Nazwa zadania**, i **autora** wartości. 

### <a name="monitoring-pipeline-jobs"></a>Monitorowanie zadań w potoku
Zadania, które są częścią potoku działają razem, zwykle po kolei, do wykonania danego scenariusza. Na przykład można mieć potok, który czyści, wyodrębnianie, przekształca, agreguje użycia klienta szczegółowe informacje o. Potok zadania są identyfikowane za pomocą właściwości "Potoku", gdy zadanie zostało przesłane. Zadania zaplanowane przy użyciu fabryki danych AZURE w wersji 2 ma automatycznie tej właściwości wypełnione. 

Aby wyświetlić listę zadań U-SQL, które są częścią potoków: 

1. W portalu Azure przejdź do konta usługi Data Lake Analytics.
2. Kliknij przycisk **zadania Insights**. Karta "Wszystkie zadania" jest ustawiana domyślnie, przedstawiający listę uruchomiona, w kolejce i zakończenia zadania.
3. Kliknij przycisk **zadania potoku** kartę. Lista zadań potoku pojawi się wraz z zagregowanych danych statystycznych dla każdego potoku.

### <a name="monitoring-recurring-jobs"></a>Monitorowanie zadań cyklicznych
Cyklicznych zadań to taki, który ma tej samej logiki biznesowej, ale używa różnych danych wejściowych w każdym uruchomieniu. W idealnym przypadku cyklicznych zadań należy zawsze powiodło się i mają względnie stały czas wykonania; monitorowanie zachowania te pomogą upewnij się, że zadanie jest w dobrej kondycji. Cykliczne zadania są identyfikowane za pomocą właściwości "Cyklu". Zadania zaplanowane przy użyciu fabryki danych AZURE w wersji 2 ma automatycznie tej właściwości wypełnione.

Aby wyświetlić listę zadań U-SQL, które są cykliczne: 

1. W portalu Azure przejdź do konta usługi Data Lake Analytics.
2. Kliknij przycisk **zadania Insights**. Karta "Wszystkie zadania" jest ustawiana domyślnie, przedstawiający listę uruchomiona, w kolejce i zakończenia zadania.
3. Kliknij przycisk **cyklicznych zadań** kartę. Lista zadań cyklicznych pojawi się wraz z zagregowanych danych statystycznych dla każdego zadania cyklicznego.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Azure Data Lake Analytics przy użyciu zasad zarządzania](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-policies)
