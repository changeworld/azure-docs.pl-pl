---
title: Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dane w usłudze Azure Data Lake Store przy użyciu grup i listy kontroli dostępu
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 5d91d8d00a37422dcead195354447d602b801f9e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Store
Zabezpieczanie danych w usłudze Azure Data Lake Store jest podejście trzech etapów.  Zarówno opartej na rolach kontrola (RBAC) dostępu i listy kontroli dostępu (ACL) musi być ustawiona do pełnego włączenia dostępu do danych użytkowników i grup zabezpieczeń.

1. Rozpocznij od utworzenia grupy zabezpieczeń w usłudze Azure Active Directory (AAD). Te grupy zabezpieczeń są używane do implementowania kontroli dostępu opartej na rolach (RBAC) w portalu Azure. Aby uzyskać więcej informacji, zobacz [opartej na rolach kontroli dostępu w systemie Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Grup zabezpieczeń usługi AAD przypisane do konta usługi Azure Data Lake Store. Kontroluje dostęp do konta usługi Data Lake Store z portalu i zarządzanie operacjami z portalu lub interfejsów API.
3. Przypisz grup zabezpieczeń usługi AAD dostęp formantu listy (kontroli dostępu ACL) w systemie plików usługi Data Lake Store.
4. Ponadto można również ustawić zakresu adresów IP dla klientów, którzy mają dostęp do danych w usłudze Data Lake Store.

Ten artykuł zawiera instrukcje dotyczące sposobu używania portalu Azure do wykonywania zadań powyżej. Aby uzyskać szczegółowe informacje na temat sposobu usługi Data Lake Store implementuje zabezpieczeń na poziomie konta i danych, zobacz [zabezpieczeń w usłudze Azure Data Lake Store](data-lake-store-security-overview.md). Aby szczegółowo informacji na temat sposobu implementacji listy kontroli dostępu w usłudze Azure Data Lake Store, zobacz [Omówienie kontroli dostępu w usłudze Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Utwórz grupy zabezpieczeń w usłudze Azure Active Directory
Aby uzyskać instrukcje dotyczące sposobu tworzenia grup zabezpieczeń usługi AAD i dodawanie użytkowników do grupy, zobacz [Zarządzanie grupami zabezpieczeń w usłudze Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> W usłudze Azure AD przy użyciu portalu Azure można dodać do grupy użytkowników i innych grup. Jednak aby można było dodać nazwy głównej usługi do grupy, należy użyć [moduł PowerShell usługi Azure AD](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Przypisywanie użytkowników lub grup zabezpieczeń do kont usługi Azure Data Lake Store
Po przypisaniu do usługi Azure Data Lake Store kont użytkowników lub grup zabezpieczeń, możesz kontrolować dostęp do operacji zarządzania na koncie przy użyciu portalu Azure i interfejsów API usługi Azure Resource Manager. 

1. Otwieranie konta usługi Azure Data Lake Store. W okienku po lewej stronie kliknij **wszystkie zasoby**, a następnie w bloku wszystkich zasobów, kliknij nazwę konta, do którego chcesz przypisać użytkownikowi lub grupie zabezpieczeń.

2. W bloku konta usługi Data Lake Store, kliknij przycisk **kontroli dostępu (IAM)**. Blok domyślnie wyświetla właściciele subskrypcji jako właściciel.
   
    ![Przypisz grupę zabezpieczeń do konta usługi Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "grupy zabezpieczeń Przypisz do konta usługi Azure Data Lake Store")

3. W **kontroli dostępu (IAM)** bloku, kliknij przycisk **Dodaj** otworzyć **dodać uprawnienia** bloku. W **dodać uprawnienia** bloku, wybierz opcję **roli** grupy/użytkownika. Wyszukaj grupy zabezpieczeń, który został utworzony wcześniej w usłudze Azure Active Directory i zaznacz go. Jeśli masz wiele użytkowników i grup, aby wyszukać od użyj **wybierz** pole tekstowe służące do filtrowania nazwę grupy. 
   
    ![Dodaj rolę dla użytkownika](./media/data-lake-store-secure-data/adl.add.user.1.png "Dodaj rolę dla użytkownika")
   
    **Właściciela** i **współautora** roli zapewniają dostęp do różnych funkcji administracyjnych na konta usługi data lake. Dla użytkowników, którzy będą interakcji z danymi w usłudze data lake, ale nadal potrzeba, aby wyświetlić informacje o zarządzaniu kontem, należy dodać je do **czytnika** roli. Zakres tych ról jest ograniczona do operacji zarządzania związane z konta usługi Azure Data Lake Store.
   
    Dla operacji danych poszczególnych plików zdefiniować, co mogą zrobić użytkownicy. W związku z tym użytkownika mającego rolę czytelnika mogą jedynie wyświetlać ustawienia administracyjne skojarzone z kontem, ale może potencjalnie odczytywania i zapisywania danych oparte na przypisane im uprawnienia systemu plików. Uprawnienia systemu plików usługi Data Lake Store są opisane w [przypisanie grupy zabezpieczeń jako listy kontroli dostępu w systemie plików usługi Azure Data Lake Store](#filepermissions).

    > [!IMPORTANT]
    > Tylko **właściciela** roli automatycznie włącza dostępu do systemu plików. **Współautora**, **czytnika**, i innych ról wymaga listy kontroli dostępu włączyć dowolny poziom dostępu do plików i folderów.  **Właściciela** roli zawiera plik administratora i uprawnień do folderu, których nie można zastąpić za pomocą listy kontroli dostępu. Aby uzyskać więcej informacji dotyczących sposobu mapowania RBAC zasady dostępu do danych, zobacz [RBAC dla zarządzania kontem](data-lake-store-security-overview.md#rbac-for-account-management).

4. Jeśli chcesz dodać grupy/użytkownika, który nie znajduje się w **dodać uprawnienia** bloku, możesz poprosić ich, wpisując adres e-mail użytkownika w **wybierz** pola tekstowego, a następnie wybierając je z listy.
   
    ![Dodaj grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.2.png "Dodaj grupę zabezpieczeń")
   
5. Kliknij pozycję **Zapisz**. Powinny pojawić się do grupy zabezpieczeń dodaje, jak pokazano poniżej.
   
    ![Dodaje grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.3.png "dodać grupę zabezpieczeń")

6. Grupy zabezpieczeń użytkowników/ma teraz dostęp do konta usługi Azure Data Lake Store. Jeśli chcesz zapewnić dostęp do określonych użytkowników, możesz je dodać do grupy zabezpieczeń. Podobnie jeśli chcesz odwołać prawa dostępu dla użytkownika, można je usunąć z grupy zabezpieczeń. Można także przypisać wiele grup zabezpieczeń do konta. 

## <a name="filepermissions"></a>Przypisywanie użytkowników lub grup zabezpieczeń jako listy kontroli dostępu w systemie plików usługi Azure Data Lake Store
Przypisując użytkownika/zabezpieczeń w systemie plików usługi Azure Data Lake, należy ustawić kontrolę dostępu do danych przechowywanych w usłudze Azure Data Lake Store.

1. W bloku konta usługi Data Lake Store kliknij pozycję **Eksplorator danych**.
   
    ![Wyświetlanie danych za pomocą Eksploratora danych](./media/data-lake-store-secure-data/adl.start.data.explorer.png "wyświetlać dane za pomocą Eksploratora danych")
2. W **Eksploratora danych** bloku, kliknij folder, dla której chcesz skonfigurować listy ACL, a następnie kliknij przycisk **dostępu**. Aby przypisać listy kontroli dostępu do pliku, należy najpierw kliknąć plik w celu wyświetlania podglądu, a następnie kliknij przycisk **dostępu** z **podglądu pliku** bloku.
   
    ![Ustaw listy kontroli dostępu w systemie plików usługi Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików usługi Data Lake")
3. **Dostępu** bloku wymieniono właściciele i przypisać uprawnienia już przypisany do katalogu głównego. Kliknij przycisk **Dodaj** ikonę, aby dodać dodatkowe dostęp do listy kontroli dostępu.
    > [!IMPORTANT]
    > Ustawiania uprawnień dostępu do jednego pliku nie zawsze udziela dostępu użytkownika/grupy do tego pliku. Ścieżka do pliku muszą być dostępne dla przypisanych użytkowników/grupy. Aby uzyskać dodatkowe informacje i przykłady, zobacz [typowe scenariusze dotyczące uprawnień](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista dostępu standardowe i niestandardowe](./media/data-lake-store-secure-data/adl.acl.2.png "listy dostępu standardowe i niestandardowe")
   
   * **Właścicieli** i **inne osoby** dostęp typu UNIX, której możesz określić Odczyt, zapis, wykonać trzy klasy unikatowych użytkowników (rwx): właściciela, grupy i inne.
   * **Uprawnienia** odpowiada POSIX listy kontroli dostępu, które umożliwiają skonfigurowanie uprawnień dla określonych użytkowników o nazwie lub grup poza właściciela pliku lub grupy. 
     
     Aby uzyskać więcej informacji, zobacz [listy ACL systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Aby uzyskać więcej informacji dotyczących sposobu implementacji listy kontroli dostępu w usłudze Data Lake Store, zobacz [kontroli dostępu w usłudze Data Lake Store](data-lake-store-access-control.md).
4. Kliknij przycisk **Dodaj** ikonę, aby otworzyć **przypisać uprawnienia** bloku. W tym bloku, kliknij przycisk **wybierz użytkownika lub grupę**, a następnie w **wybierz użytkownika lub grupę** bloku, wyszukaj grupę zabezpieczeń został utworzony wcześniej w usłudze Azure Active Directory. Jeśli masz dużą liczbę grup objętych wyszukiwaniem z, użyj pola tekstowego u góry, aby odfiltrować nazwę grupy. Kliknij grupę, w której chcesz dodać, a następnie kliknij przycisk **wybierz**.
   
    ![Dodaj grupę](./media/data-lake-store-secure-data/adl.acl.3.png "Dodaj grupę")
5. Kliknij przycisk **wybierz uprawnienia**, wybierz uprawnienia, czy uprawnienia powinien być zastosowany rekursywnie i określa, czy chcesz przypisać uprawnienia jako listy ACL, access domyślne listy ACL, lub obie. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.4.png "przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w usłudze Data Lake Store i dostęp do i domyślnej listy kontroli dostępu, zobacz [kontroli dostępu w usłudze Data Lake Store](data-lake-store-access-control.md).
6. Po kliknięciu przycisku **Ok** w **wybierz uprawnienia** bloku, nowo dodanego grupę oraz skojarzone uprawnienia będzie teraz wyświetlany w **dostępu** bloku.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.5.png "przypisywanie uprawnień do grupy")
   
   > [!IMPORTANT]
   > W bieżącej wersji może mieć maksymalnie 28 wpisy w **przypisane uprawnienia**. Jeśli chcesz dodać więcej niż 28 użytkowników, należy utworzyć grupy zabezpieczeń, dodawanie użytkowników do grup zabezpieczeń, Dodaj zapewnianie dostępu do tych grup zabezpieczeń dla konta usługi Data Lake Store.
   > 
   > 
7. W razie potrzeby można również zmodyfikować uprawnienia dostępu, po dodaniu grupy. Usuń zaznaczenie lub zaznacz pole wyboru dla każdego typu uprawnienia (Odczyt, zapis, wykonywanie) oparte na czy chcesz usunąć lub przypisać te uprawnienia do grupy zabezpieczeń. Kliknij przycisk **zapisać** można zapisać zmian, lub **odrzucić** cofnięcie zmian.

## <a name="set-ip-address-range-for-data-access"></a>Ustaw zakres adresów IP dla dostępu do danych
Azure Data Lake Store umożliwia dalsze blokowania dostępu do magazynu danych na poziomie sieci. Można włączyć zapory, podaj adres IP lub zdefiniuj zakres adresów IP dla zaufanych klientów. Po włączeniu tylko w przypadku klientów, którzy mają zdefiniowanego zakresu adresów IP można połączyć z magazynem.

![Ustawienia zapory i IP dostępu](./media/data-lake-store-secure-data/firewall-ip-access.png "ustawienia i adres IP zapory")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Usuwanie grupy zabezpieczeń dla konta usługi Azure Data Lake Store
Po usunięciu grupy zabezpieczeń z konta usługi Azure Data Lake Store tylko zmienić dostęp do operacji zarządzania na koncie przy użyciu portalu Azure i interfejsów API usługi Azure Resource Manager.  

Dostęp do danych jest bez zmian i jest nadal zarządzana przez dostęp do listy kontroli dostępu.  Wyjątkiem są użytkownicy/grupy do roli właścicieli.  Użytkownicy/grupy usunięty z roli właścicieli nie są już superużytkowników i ich dostęp powraca do ustawienia listy ACL dostępu. 

1. W bloku konta usługi Data Lake Store, kliknij przycisk **kontroli dostępu (IAM)**. 
   
    ![Przypisz grupę zabezpieczeń do konta usługi Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "grupy zabezpieczeń Przypisz do konta usługi Azure Data Lake")
2. W **kontroli dostępu (IAM)** bloku, kliknij przycisk grup zabezpieczeń do usunięcia. Kliknij przycisk **Usuń**.
   
    ![Grupa zabezpieczeń usunięte](./media/data-lake-store-secure-data/adl.remove.group.png "usunięta grupa zabezpieczeń")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Usuń grupę zabezpieczeń listy kontroli dostępu w systemie plików usługi Azure Data Lake Store
Podczas usuwania grupy zabezpieczeń listy kontroli dostępu w systemie plików usługi Azure Data Lake Store, możesz zmienić dostępu do danych w usłudze Data Lake Store.

1. W bloku konta usługi Data Lake Store kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów w ramach konta usługi Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Tworzenie katalogów w ramach konta usługi Data Lake")
2. W **Eksploratora danych** bloku, kliknij folder, dla którego chcesz usunąć listy ACL, a następnie kliknij przycisk **dostępu**. Aby usunąć listy ACL dla pliku, należy najpierw kliknąć plik w celu wyświetlania podglądu, a następnie kliknij przycisk **dostępu** z **podglądu pliku** bloku. 
   
    ![Ustaw listy kontroli dostępu w systemie plików usługi Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików usługi Data Lake")
3. W **dostępu** bloku, kliknij grupę zabezpieczeń, które chcesz usunąć. W **dostęp do szczegółów** bloku, kliknij przycisk **Usuń**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.remove.acl.png "przypisywanie uprawnień do grupy")

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Wprowadzenie do usługi Data Lake Store za pomocą programu PowerShell](data-lake-store-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Store przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dostęp do dzienników diagnostycznych usługi Data Lake Store](data-lake-store-diagnostic-logs.md)

