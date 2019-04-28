---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal
description: W tym artykule opisano sposób użycia witryny Azure portal do zarządzania kont usługi Data Lake Analytics, źródła danych, użytkowników i zadań.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813435"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Zarządzanie przy użyciu witryny Azure portal usługi Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kont, źródła danych, użytkowników i zadania usługi Azure Data Lake Analytics przy użyciu witryny Azure portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Zarządzanie kontami usługi Data Lake Analytics

### <a name="create-an-account"></a>Tworzenie konta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** > **rozwiązania inteligentne + analiza** > **usługi Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów: 
   1. **Nazwa**: Nazwa konta usługi Data Lake Analytics.
   2. **Subskrypcja**: Subskrypcja platformy Azure, używane jako konto.
   3. **Grupa zasobów**: Grupa zasobów platformy Azure, w której chcesz utworzyć konto. 
   4. **Lokalizacja**: Centrum danych platformy Azure dla konta usługi Data Lake Analytics. 
   5. **Data Lake Store**: Domyślny magazyn, który ma być używany dla konta usługi Data Lake Analytics. Konto usługi Azure Data Lake Store i konto usługi Data Lake Analytics musi być w tej samej lokalizacji.
4. Kliknij pozycję **Utwórz**. 

### <a name="delete-a-data-lake-analytics-account"></a>Usuwanie konta usługi Data Lake Analytics

Zanim usuniesz konto usługi Data Lake Analytics, należy usunąć jego domyślnego konta Data Lake Store.

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij polecenie **Usuń**.
3. Wpisz nazwę konta.
4. Kliknij polecenie **Usuń**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Usługa Data Lake Analytics obsługuje następujące źródła danych:

* Data Lake Store
* Azure Storage

Eksplorator danych umożliwia przeglądanie źródeł danych i wykonywania operacji zarządzania podstawowy plik. 

### <a name="add-a-data-source"></a>Dodawanie źródła danych

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij przycisk **źródeł danych**.
3. Kliknij przycisk **Dodaj źródło danych**.
    
   * Aby dodać konta Data Lake Store, należy nazwę konta i uzyskiwania dostępu do konta, aby można było wykonuje zapytania.
   * Aby dodać usługi Azure Blob storage, konieczne jest konto magazynu i klucz konta. Aby je znaleźć, przejdź do konta magazynu w portalu.

## <a name="set-up-firewall-rules"></a>Konfigurowanie reguł zapory

Usługi Data Lake Analytics można użyć do dalszego blokowanie dostępu do swojego konta usługi Data Lake Analytics na poziomie sieci. Możesz Włącz zaporę, określ adres IP lub zdefiniować zakres adresów IP z zaufanych klientów. Po włączeniu tych środków, tylko w przypadku klientów, którzy mają adresy IP ze zdefiniowanego zakresu można połączyć z magazynem.

W przypadku innych usług platformy Azure, takich jak usługi Azure Data Factory lub maszyn wirtualnych, połączenie z kontem usługi Data Lake Analytics, upewnij się, że **Zezwalaj na usługi platformy Azure** włączeniu **na**. 

### <a name="set-up-a-firewall-rule"></a>Konfigurowanie reguły zapory

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. W menu po lewej stronie kliknij **zapory**.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

Możesz użyć **Kreatora dodawania użytkownika** można zainicjować obsługę nowych użytkowników usługi Data Lake.

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Po lewej stronie w obszarze **wprowadzenie**, kliknij przycisk **Kreatora dodawania użytkownika**.
3. Wybierz użytkownika, a następnie kliknij przycisk **wybierz**.
4. Wybierz rolę, a następnie kliknij przycisk **wybierz**. Aby skonfigurować nowego dla deweloperów, aby użyć usługi Azure Data Lake, wybierz **Data Lake Analytics Developer** roli.
5. Wybierz listy kontroli dostępu (ACL) dla bazy danych U-SQL. Po zakończeniu opcji kliknij przycisk **wybierz**.
6. Wybierz z listy ACL dla plików. W domyślnym magazynie nie zmieniaj listy ACL dla folderu głównego "/" i folderu/System. Kliknij pozycję **Wybierz**.
7. Przejrzyj wszystkie wybrane zmiany, a następnie kliknij przycisk **Uruchom**.
8. Po zakończeniu działania kreatora, kliknij przycisk **gotowe**.

## <a name="manage-role-based-access-control"></a>Zarządzanie kontrolą dostępu opartej na rolach

Podobnie jak inne usługi platformy Azure można użyć kontroli dostępu opartej na rolach (RBAC) do kontrolowania sposobu interakcji użytkowników z usługą.

Standardowa role RBAC mają następujące możliwości:
* **Właściciel**: Można przesyłać zadania, monitorować zadania, anulować zadania z dowolnego użytkownika i skonfigurować konto.
* **Współautor**: Można przesyłać zadania, monitorować zadania, anulować zadania z dowolnego użytkownika i skonfigurować konto.
* **Czytnik**: Można monitorować zadania.

Aby umożliwić programistom języka U-SQL do korzystania z usługi Data Lake Analytics za pomocą roli Data Lake Analytics dewelopera. Można użyć roli Data Lake Analytics dewelopera:
* Przesyłanie zadań.
* Monitoruj stan zadania i postępu zadania przesłane przez żadnego użytkownika.
* Zobacz skryptów U-SQL z zadania przesłane przez żadnego użytkownika.
* Anuluj tylko własne zadania.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Dodaj użytkowników lub grup zabezpieczeń do konta usługi Data Lake Analytics

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij przycisk **kontrola dostępu (IAM)** > **Dodaj przypisanie roli**.
3. Wybierz rolę.
4. Dodaj użytkownika.
5. Kliknij przycisk **OK**.

>[!NOTE]
>Jeśli użytkownik lub grupa zabezpieczeń wymaga przesyłania zadań, muszą uprawnienie dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Zarządzanie zadaniami

### <a name="submit-a-job"></a>Przesyłanie zadania

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.

2. Kliknij przycisk **nowe zadanie**. Dla każdego zadania należy skonfigurować:

    1. **Nazwa zadania**: Nazwa zadania.
    2. **Priorytet**: Niższych numerach mają wyższy priorytet. Jeśli dwa zadania są umieszczane w kolejce, tą o niższym priorytecie działać pierwszego.
    3. **Równoległość**: Maksymalna liczba procesów obliczeniowych do zarezerwowania dla tego zadania.

3. Kliknij przycisk **Prześlij zadanie**.

### <a name="monitor-jobs"></a>Monitorowanie zadań

1. W witrynie Azure portal przejdź na swoje konto usługi Data Lake Analytics.
2. Kliknij przycisk **Wyświetl wszystkie zadania**. Wyświetlana jest lista wszystkich aktywnych i ostatnio zakończonych zadań w ramach konta.
3. Opcjonalnie kliknij **filtru** ułatwią znalezienie zadania według **zakres czasu**, **Nazwa zadania**, i **Autor** wartości. 

### <a name="monitoring-pipeline-jobs"></a>Monitorowanie zadań w potoku
Zadania, które są częścią potoku współdziałają ze sobą, zwykle po kolei, w celu wykonania konkretnego scenariusza. Można na przykład istnienie potoku, który czyści, wyodrębnia, przekształca, agreguje użycia dla usługi customer insights. Zadania potoku są identyfikowane za pomocą właściwości "Pipeline", jeśli zadanie zostało przesłane. Zaplanowane za pomocą usługi ADF w wersji 2 mają automatycznie tej właściwości wypełnione. 

Aby wyświetlić listę zadań U-SQL, które są częścią potoków: 

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij przycisk **Insights zadania**. Karta "Wszystkie zadania" jest ustawiana domyślnie, pokazanie listy działanie w kolejce i zakończenia zadania.
3. Kliknij przycisk **zadania potoku** kartę. Lista zadań potoku będą wyświetlane wraz z zagregowanych danych statystycznych dla każdego potoku.

### <a name="monitoring-recurring-jobs"></a>Monitorowanie zadań cyklicznych
To zadanie cykliczne to taki, który ma ten sam logikę biznesową, ale używa różnych danych wejściowych w każdym uruchomieniu. W idealnym przypadku zadań cyklicznych powinien zawsze powiedzie się i mają względnie stały czas wykonywania; monitorowanie zachowań może pomóc upewnić się, że zadanie jest w dobrej kondycji. Zadania cykliczne są identyfikowane za pomocą właściwości "Cykl". Zaplanowane za pomocą usługi ADF w wersji 2 mają automatycznie tej właściwości wypełnione.

Aby wyświetlić listę zadań U-SQL, które są cykliczne: 

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij przycisk **Insights zadania**. Karta "Wszystkie zadania" jest ustawiana domyślnie, pokazanie listy działanie w kolejce i zakończenia zadania.
3. Kliknij przycisk **zadania cykliczne** kartę. Lista zadań cyklicznych będą wyświetlane wraz z zagregowanych danych statystycznych dla każdego zadania cykliczne.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Zarządzanie przy użyciu zasad usługi Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
