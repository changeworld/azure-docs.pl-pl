---
title: Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dane w usłudze Azure Data Lake magazynu Gen1 przy użyciu grup i listy kontroli dostępu
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198266"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1
Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1 jest to podejście w trzech krokach.  Zarówno opartej na rolach kontroli (RBAC) dostępu i listy kontroli dostępu (ACL) musi być ustawione na w pełni włączyć dostęp do danych użytkowników i grup zabezpieczeń.

1. Rozpocznij od utworzenia grup zabezpieczeń w usłudze Azure Active Directory (AAD). Te grupy zabezpieczeń są używane do implementowania kontroli dostępu opartej na rolach (RBAC) w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [opartej na rolach kontrola dostępu w systemie Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Przypisz grupy zabezpieczeń usługi AAD do konta Data Lake Storage Gen1. Ta opcja kontroluje dostęp do konta Data Lake Storage Gen1 z portalu i zarządzanie operacji z portalu lub interfejsów API.
3. Przypisywanie grup zabezpieczeń usługi AAD, jak listy kontroli dostępu (ACL) w systemie plików Data Lake Storage Gen1.
4. Ponadto można również ustawić zakres adresów IP dla klientów, którzy mają dostęp do danych w Data Lake Storage Gen1.

Ten artykuł zawiera instrukcje dotyczące sposobu używania witryny Azure portal do wykonywania zadań powyżej. Aby uzyskać szczegółowe informacje na temat jak Data Lake Storage Gen1 implementuje zabezpieczenia na poziomie konta i danych, zobacz [zabezpieczeń w usłudze Azure Data Lake magazynu Gen1](data-lake-store-security-overview.md). Szczegółowe instrukcje dotyczące sposobu implementacji list ACL w Data Lake Storage Gen1, zobacz [Omówienie kontroli dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konta Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Utwórz grupy zabezpieczeń w usłudze Azure Active Directory
Aby uzyskać instrukcje dotyczące sposobu tworzenia grup zabezpieczeń usługi AAD i dodawanie użytkowników do grupy, zobacz [Zarządzanie grupami zabezpieczeń w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> W usłudze Azure AD przy użyciu witryny Azure portal można dodać do grupy użytkowników i innych grup. Jednak aby można było dodać nazwę główną usługi do grupy, należy użyć [modułu programu PowerShell usługi Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Przypisywanie użytkowników lub grup zabezpieczeń do konta Data Lake Storage Gen1
Po przypisaniu użytkowników lub grup zabezpieczeń do konta Data Lake Storage Gen1, możesz kontrolować dostęp do operacji zarządzania na koncie przy użyciu witryny Azure portal i interfejsów API usługi Azure Resource Manager. 

1. Otwieranie konta Data Lake Storage Gen1. W okienku po lewej stronie kliknij **wszystkie zasoby**, a następnie w bloku wszystkie zasoby kliknij nazwę konta, do którego chcesz przypisać użytkownikowi lub grupie zabezpieczeń.

2. W bloku konta usługi Data Lake Storage Gen1 kliknij **kontrola dostępu (IAM)** . Blok domyślnie wyświetla właściciele subskrypcji jako właściciel.
   
    ![Przypisz grupy zabezpieczeń do konta usługi Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Przypisz grupę zabezpieczeń do konta usługi Azure Data Lake Storage Gen1")

3. W **kontrola dostępu (IAM)** bloku kliknij **Dodaj** otworzyć **Dodaj uprawnienia** bloku. W **Dodaj uprawnienia** bloku wybierz **roli** dla użytkownika/grupy. Wyszukaj grupy zabezpieczeń utworzonej wcześniej w usłudze Azure Active Directory i wybierz ją. Jeśli masz wiele użytkowników i grup, rozpocznie się wyszukiwanie od, użyj **wybierz** pole tekstowe umożliwiające filtrowanie na nazwę grupy. 
   
    ![Dodaj rolę użytkownika](./media/data-lake-store-secure-data/adl.add.user.1.png "Dodaj rolę użytkownika")
   
    **Właściciela** i **Współautor** rola zapewnia dostęp do różnych funkcji administracyjnych na konta usługi data lake. Dla użytkowników, którzy będą wchodzić w interakcje z danymi w usłudze data lake, ale nadal potrzeba, aby wyświetlić informacje o zarządzaniu kontem, możesz dodać je do **czytnika** roli. Zakres tych ról jest ograniczony do obsługi operacji zarządzania związane z kontem Data Lake Storage Gen1.
   
    Operacje na danych uprawnienia systemu plików poszczególnych definiują, które mogą wykonywać użytkownicy. W związku z tym użytkownika z roli Czytelnik mogą tylko wyświetlać ustawienia administracyjne skojarzone z kontem, ale może potencjalnie odczytywać i zapisywać dane w oparciu o uprawnienia systemu plików przypisane do nich. Uprawnienia systemu plików Gen1 magazynu jeziora danych są opisane na [Przypisz grupę zabezpieczeń jako list ACL do systemu plików usługi Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Tylko **właściciela** automatycznie aktywuje konkretna rola dostępu do systemu plików. **Współautor**, **czytnika**, i inne role wymagają listy ACL, aby włączyć dowolny poziom dostępu do plików i folderów.  **Właściciela** rola zapewnia także administrator uprawnienia pliki i foldery, które nie mogą być zastępowane za pośrednictwem list ACL. Aby uzyskać więcej informacji na temat sposobu mapowania RBAC, zasady dostępu do danych, zobacz [RBAC dla konta zarządzania](data-lake-store-security-overview.md#rbac-for-account-management).

4. Jeśli chcesz dodać grupy/użytkownika, który nie znajduje się w **Dodaj uprawnienia** bloku, możesz zaprosić je, wpisując ich adres e-mail podany w **wybierz** pola tekstowego, a następnie wybierając je z listy.
   
    ![Dodaj grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.2.png "dodać grupę zabezpieczeń")
   
5. Kliknij pozycję **Zapisz**. Powinieneś zobaczyć grupy zabezpieczeń dodaje się, jak pokazano poniżej.
   
    ![Grupy zabezpieczeń dodaje](./media/data-lake-store-secure-data/adl.add.user.3.png "grupę zabezpieczeń, które dodano")

6. Grupy zabezpieczeń/użytkownika ma teraz dostęp do konta Data Lake Storage Gen1. Jeśli chcesz uzyskać dostęp do określonych użytkowników, możesz je dodać do grupy zabezpieczeń. Podobnie jeśli chcesz odwołać prawa dostępu dla użytkownika, można je usunąć z grupy zabezpieczeń. Można także przypisać wiele grup zabezpieczeń do konta. 

## <a name="filepermissions"></a>Przypisywanie użytkowników lub grup zabezpieczeń jako list ACL do systemu plików Data Lake Storage Gen1
Przypisując użytkowników/grup zabezpieczeń do systemu plików Data Lake Storage Gen1, możesz ustawić kontrolę dostępu do danych przechowywanych w Data Lake Storage Gen1.

1. W bloku konta usługi Data Lake Storage Gen1 kliknij **Eksplorator danych**.
   
    ![Wyświetlanie danych za pomocą Eksploratora danych](./media/data-lake-store-secure-data/adl.start.data.explorer.png "wyświetlanie danych za pomocą Eksploratora danych")
2. W **Eksplorator danych** bloku, kliknij folder, dla którego chcesz skonfigurować listy ACL, a następnie kliknij przycisk **dostępu**. Aby przypisać listy kontroli dostępu do pliku, należy najpierw kliknąć plik w wersji zapoznawczej go, a następnie kliknij przycisk **dostępu** z **Podgląd pliku** bloku.
   
    ![Ustaw listy kontroli dostępu w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików Data Lake Storage Gen1")
3. **Dostępu** blok zawiera listę właścicieli i przypisać uprawnienia już przypisany do katalogu głównego. Kliknij przycisk **Dodaj** ikonę, aby dodać dodatkowe listy ACL dostępu.
    > [!IMPORTANT]
    > Ustawienie uprawnienia dostępu do pojedynczego pliku nie musi udzielić dostępu użytkownika/grupy, do tego pliku. Ścieżka do pliku muszą być dostępne do przypisanego użytkownika/grupy. Aby uzyskać więcej informacji i przykładów, zobacz [typowych scenariuszy, powiązanych z uprawnieniami](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Listę standardowych i niestandardowych dostępu](./media/data-lake-store-secure-data/adl.acl.2.png "dostępu do standardowych i niestandardowych List")
   
   * **Właścicieli** i **wszyscy** zapewniają dostęp typu UNIX, której można określić Odczyt, zapis, wykonywanie (rwx) do trzech klas unikatowych użytkowników: właściciela, grupy i inne.
   * **Przypisane uprawnienia** odnosi się do listy ACL modelu POSIX, które umożliwiają skonfigurowanie uprawnień dla określonego nazwanego użytkowników lub grup, poza właściciela pliku lub grupy. 
     
     Aby uzyskać więcej informacji, zobacz [listy ACL systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Aby uzyskać więcej informacji na temat sposobu implementacji list ACL w Data Lake Storage Gen1, zobacz [kontroli dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kliknij przycisk **Dodaj** ikonę, aby otworzyć **przypisać uprawnienia** bloku. W tym bloku kliknij **zaznacz użytkownika lub grupę**, a następnie w polu **zaznacz użytkownika lub grupę** bloku, zwróć uwagę na grupy zabezpieczeń utworzonej wcześniej w usłudze Azure Active Directory. Jeśli masz wiele grup objętych wyszukiwaniem z, użyj pola tekstowego u góry, do filtrowania przesyłanej nazwy grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij przycisk **wybierz**.
   
    ![Dodaj grupę](./media/data-lake-store-secure-data/adl.acl.3.png "Dodaj grupę")
5. Kliknij przycisk **wybierz uprawnienia**, wybierz uprawnienia, czy uprawnienia powinny dotyczyć cyklicznie i tego, czy chcesz przypisać uprawnienia jako listy ACL, access domyślne listy ACL i / lub. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.4.png "przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w Data Lake Storage Gen1 i dostęp do i domyślnej listy ACL, zobacz [kontroli dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po kliknięciu przycisku **Ok** w **wybierz uprawnienia** bloku, nowo dodana grupa i skojarzonych z nimi uprawnień zostanie dodany do listy w **dostępu** bloku.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.5.png "przypisywanie uprawnień do grupy")
   
   > [!IMPORTANT]
   > W bieżącej wersji, może mieć maksymalnie 28 wpisy w obszarze **przypisane uprawnienia**. Jeśli chcesz dodać więcej niż 28 użytkowników, należy utworzyć grupy zabezpieczeń, dodawanie użytkowników do grup zabezpieczeń, Dodaj zapewniają dostęp do tych grup zabezpieczeń dla konta Data Lake Storage Gen1.
   > 
   > 
7. W razie potrzeby można również zmodyfikować uprawnienia dostępu, po dodaniu grupy. Wyczyść lub zaznacz pole wyboru dla każdego typu uprawnienia (Odczyt, zapis, wykonywanie) oparte na tego, czy chcesz usunąć lub przypisać to uprawnienie do grupy zabezpieczeń. Kliknij przycisk **Zapisz** można zapisać zmian, lub **odrzucić** cofnięcie zmian.

## <a name="set-ip-address-range-for-data-access"></a>Ustaw zakres adresów IP, aby uzyskać dostęp do danych
Data Lake Storage Gen1 pozwala na dalsze blokowanie dostępu do magazynu danych na poziomie sieci. Możesz Włącz zaporę, określ adres IP lub zdefiniować zakres adresów IP z zaufanych klientów. Po włączeniu tylko klienci, którzy mają adresy IP zdefiniowanego zakresu można połączyć z magazynem.

![Dostęp do ustawień zapory i IP](./media/data-lake-store-secure-data/firewall-ip-access.png "zapory adresów IP i ustawienia")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Usuwanie grupy zabezpieczeń dla konta Data Lake Storage Gen1
Po usunięciu grupy zabezpieczeń z kont usługi Data Lake Storage Gen1 tylko zmienić dostęp do operacji zarządzania na koncie przy użyciu witryny Azure Portal i interfejsów API usługi Azure Resource Manager.  

Dostęp do danych pozostaje niezmieniony i nadal jest zarządzany przez listy ACL dostępu.  Wyjątkiem są użytkownicy/grupy, roli właścicieli.  Użytkownicy/grupy usunięte z roli właścicieli nie są już superużytkowników i ich dostęp powraca do ustawienia listy ACL dostępu. 

1. W bloku konta usługi Data Lake Storage Gen1 kliknij **kontrola dostępu (IAM)** . 
   
    ![Przypisz grupy zabezpieczeń do konta Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Przypisz grupę zabezpieczeń do konta Data Lake Storage Gen1")
2. W **kontrola dostępu (IAM)** bloku kliknij opcję grupy zabezpieczeń, aby usunąć. Kliknij pozycję **Usuń**.
   
    ![Grupa zabezpieczeń została usunięta](./media/data-lake-store-secure-data/adl.remove.group.png "grupa zabezpieczeń została usunięta")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Usuwanie grupy zabezpieczeń listy kontroli dostępu systemu plików Data Lake Storage Gen1
Podczas usuwania grupy zabezpieczeń listy kontroli dostępu w systemie plików Data Lake Storage Gen1, możesz zmienić dostęp do danych w ramach konta Data Lake Storage Gen1.

1. W bloku konta usługi Data Lake Storage Gen1 kliknij **Eksplorator danych**.
   
    ![Tworzenie katalogów w ramach konta Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "tworzyć katalogi na koncie usługi Data Lake Storage Gen1")
2. W **Eksplorator danych** bloku, kliknij folder, dla którego chcesz usunąć listę ACL, a następnie kliknij przycisk **dostępu**. Aby usunąć listy ACL dla pliku, należy najpierw kliknąć plik w wersji zapoznawczej go, a następnie kliknij przycisk **dostępu** z **Podgląd pliku** bloku. 
   
    ![Ustaw listy kontroli dostępu w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustaw listy kontroli dostępu w systemie plików Data Lake Storage Gen1")
3. W **dostępu** bloku kliknij grupę zabezpieczeń, którą chcesz usunąć. W **dostęp do szczegółów** bloku kliknij **Usuń**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.remove.acl.png "przypisywanie uprawnień do grupy")

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dostęp do dzienników diagnostycznych usługi Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

