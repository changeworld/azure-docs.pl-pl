---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal
description: W tym artykule opisano sposób używania witryny Azure Portal do zarządzania kontami usługi Data Lake Analytics, źródłami danych, użytkownikami & zadaniami.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265703"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Manage Azure Data Lake Analytics using the Azure portal (Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal)
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami usługi Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu witryny Azure portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Zarządzanie kontami usługi Data Lake Analytics

### <a name="create-an-account"></a>Tworzenie konta

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij **pozycję Utwórz analizę zasobów** > **+ analizę** > **Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów: 
   1. **Nazwa**: Nazwa konta Usługi Data Lake Analytics.
   2. **Subskrypcja**: Subskrypcja platformy Azure używana dla konta.
   3. **Grupa zasobów:** Grupa zasobów platformy Azure, w której ma utworzyć konto. 
   4. **Lokalizacja:** centrum danych platformy Azure dla konta usługi Data Lake Analytics. 
   5. **Data Lake Store**: domyślny magazyn, który ma być używany dla konta Usługi Data Lake Analytics. Konto usługi Azure Data Lake Store i konto usługi Data Lake Analytics muszą znajdować się w tej samej lokalizacji.
4. Kliknij przycisk **Utwórz**. 

### <a name="delete-a-data-lake-analytics-account"></a>Usuwanie konta usługi Data Lake Analytics

Przed usunięciem konta Usługi Data Lake Analytics usuń jego domyślne konto w sklepie Data Lake Store.

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Usuń**.
3. Wpisz nazwę konta.
4. Kliknij **pozycję Usuń**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Usługa Data Lake Analytics obsługuje następujące źródła danych:

* Data Lake Store
* Azure Storage

Eksploratora danych służy do przeglądania źródeł danych i wykonywania podstawowych operacji zarządzania plikami. 

### <a name="add-a-data-source"></a>Dodawanie źródła danych

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Źródła danych**.
3. Kliknij **pozycję Dodaj źródło danych**.
    
   * Aby dodać konto w sklepie Data Lake Store, potrzebujesz nazwy konta i dostępu do konta, aby móc go zbadać.
   * Aby dodać magazyn obiektów Blob platformy Azure, potrzebujesz konta magazynu i klucza konta. Aby je znaleźć, przejdź do konta magazynu w portalu.

## <a name="set-up-firewall-rules"></a>Konfigurowanie reguł zapory

Za pomocą usługi Data Lake Analytics możesz dodatkowo zablokować dostęp do konta Usługi Data Lake Analytics na poziomie sieci. Można włączyć zaporę, określić adres IP lub zdefiniować zakres adresów IP dla zaufanych klientów. Po włączeniu tych środków tylko klienci, którzy mają adresy IP w zdefiniowanym zakresie, mogą łączyć się ze sklepem.

Jeśli inne usługi platformy Azure, takie jak usługa Azure Data Factory lub maszyny wirtualne, połącz się z kontem usługi Data Lake Analytics, upewnij się, że **opcja Zezwalaj na usługi platformy Azure** jest **włączona.** 

### <a name="set-up-a-firewall-rule"></a>Konfigurowanie reguły zapory

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. W menu po lewej stronie kliknij pozycję **Zapora .**

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

**Kreatora dodawania użytkownika** umożliwia łatwe aprowizowanie nowych użytkowników usługi Data Lake.

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Po lewej stronie w obszarze **Wprowadzenie**kliknij pozycję **Dodaj Kreatora użytkowników**.
3. Wybierz użytkownika, a następnie kliknij przycisk **Wybierz**.
4. Wybierz rolę, a następnie kliknij przycisk **Wybierz**. Aby skonfigurować nowego dewelopera do korzystania z usługi Azure Data Lake, wybierz rolę **dewelopera usługi Data Lake Analytics.**
5. Wybierz listy kontroli dostępu (ACL) dla baz danych U-SQL. Gdy wybory są zadowalające, kliknij przycisk **Wybierz**.
6. Wybierz listy ACL dla plików. W przypadku magazynu domyślnego nie zmieniaj list ACL dla folderu głównego "/" i dla folderu /system. Kliknij **pozycję Wybierz**.
7. Przejrzyj wszystkie wybrane zmiany, a następnie kliknij przycisk **Uruchom**.
8. Po zakończeniu pracy kreatora kliknij przycisk **Gotowe**.

## <a name="manage-role-based-access-control"></a>Zarządzanie kontrolą dostępu opartą na rolach

Podobnie jak inne usługi platformy Azure, można użyć kontroli dostępu opartej na rolach (RBAC) do kontrolowania sposobu interakcji użytkowników z usługą.

Standardowe role RBAC mają następujące możliwości:
* **Właściciel**: Może przesyłać zadania, monitorować zadania, anulować zadania od dowolnego użytkownika i skonfigurować konto.
* **Współautor:** Może przesyłać zadania, monitorować zadania, anulować zadania od dowolnego użytkownika i skonfigurować konto.
* **Czytnik:** Może monitorować zadania.

Użyj roli dewelopera analizy usługi Data Lake, aby umożliwić deweloperom U-SQL korzystanie z usługi Data Lake Analytics. Za pomocą roli Deweloper analizy usługi Data Lake Analytics możesz:
* Prześlij oferty pracy.
* Monitoruj stan zadania i postęp zadań przesłanych przez dowolnego użytkownika.
* Zobacz skrypty U-SQL z zadań przesłanych przez dowolnego użytkownika.
* Anuluj tylko własne zadania.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Dodawanie użytkowników lub grup zabezpieczeń do konta usługi Data Lake Analytics

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Kontrola dostępu (IAM)** > Dodaj**przypisanie roli**.
3. Wybierz rolę.
4. Dodaj użytkownika.
5. Kliknij przycisk **OK**.

>[!NOTE]
>Jeśli użytkownik lub grupa zabezpieczeń musi przesłać zadania, potrzebują również uprawnień na koncie sklepu. Aby uzyskać więcej informacji, zobacz [Bezpieczne dane przechowywane w magazynie Data Lake .](../data-lake-store/data-lake-store-secure-data.md)
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Zarządzanie zadaniami

### <a name="submit-a-job"></a>Przesyłanie zadania

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.

2. Kliknij **pozycję Nowe zadanie**. Dla każdego zadania skonfiguruj:

    1. **Nazwa zadania**: Nazwa zadania.
    2. **Priorytet:** Niższe liczby mają wyższy priorytet. Jeśli dwa zadania są umieszczane w kolejce, najpierw zostanie uruchamiane jedno z niższą wartością priorytetu.
    3. **Równoległość:** Maksymalna liczba procesów obliczeniowych do zarezerwowania dla tego zadania.

3. Kliknij przycisk **Prześlij zadanie**.

### <a name="monitor-jobs"></a>Monitorowanie zadań

1. W witrynie Azure portal przejdź do konta usługi Data Lake Analytics.
2. Kliknij **pozycję Wyświetl wszystkie zadania**. Zostanie wyświetlona lista wszystkich aktywnych i ostatnio zakończonych zadań na koncie.
3. Opcjonalnie kliknij przycisk **Filtruj,** aby ułatwić znajdowanie zadań według **przedziału czasu,** **nazwy zlecenia**i wartości **autora.** 

### <a name="monitoring-pipeline-jobs"></a>Monitorowanie zadań potoku
Zadania, które są częścią potoku, współpracują ze sobą, zwykle sekwencyjnie, aby osiągnąć określony scenariusz. Na przykład można mieć potoku, który czyści, wyodrębnia, przekształca, agreguje użycie dla szczegółowych informacji o klientach. Zadania potoku są identyfikowane przy użyciu właściwości "Potok" podczas przesyłania zadania. Zadania zaplanowane przy użyciu ADF V2 automatycznie będą wypełniane tą właściwością. 

Aby wyświetlić listę zadań U-SQL, które są częścią potoków: 

1. W witrynie Azure portal przejdź do kont usługi Data Lake Analytics.
2. Kliknij **pozycję Wgląd w zadania**. Karta "Wszystkie zadania" zostanie domyślna, wyświetlając listę uruchomionych, w kolejce i zakończonych zadań.
3. Kliknij kartę **Zadania potoku.** Zostanie wyświetlona lista zadań potoku wraz z zagregowanymi statystykami dla każdego potoku.

### <a name="monitoring-recurring-jobs"></a>Monitorowanie zadań cyklicznych
Zadanie cykliczne to zadanie, które ma tę samą logikę biznesową, ale używa różnych danych wejściowych za każdym razem, gdy jest uruchamiane. Najlepiej, jeśli zadania cykliczne powinny zawsze zakończyć się powodzeniem i mieć stosunkowo stabilny czas wykonywania; monitorowanie tych zachowań pomoże zapewnić, że praca jest w dobrej kondycji. Zadania cykliczne są identyfikowane przy użyciu właściwości "Cykl". Zadania zaplanowane przy użyciu ADF V2 automatycznie będą wypełniane tą właściwością.

Aby wyświetlić listę cyklicznych zadań U-SQL: 

1. W witrynie Azure portal przejdź do kont usługi Data Lake Analytics.
2. Kliknij **pozycję Wgląd w zadania**. Karta "Wszystkie zadania" zostanie domyślna, wyświetlając listę uruchomionych, w kolejce i zakończonych zadań.
3. Kliknij kartę **Zadania cykliczne.** Zostanie wyświetlona lista zadań cyklicznych wraz z zagregowanymi statystykami dla każdego zadania cyklicznego.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu usługi Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu zasad](data-lake-analytics-account-policies.md)
