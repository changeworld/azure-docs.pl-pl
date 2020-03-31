---
title: Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dane w usłudze Azure Data Lake Storage Gen1 przy użyciu grup i list kontroli dostępu
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260308"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Securing data stored in Azure Data Lake Storage Gen1 (Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1)
Zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1 to podejście trzyetapowe.  Zarówno oparte na rolach kontroli dostępu (RBAC) i listy kontroli dostępu (ACL) musi być ustawiona, aby w pełni umożliwić dostęp do danych dla użytkowników i grup zabezpieczeń.

1. Zacznij od utworzenia grup zabezpieczeń w usłudze Azure Active Directory (AAD). Te grupy zabezpieczeń są używane do implementowania kontroli dostępu opartej na rolach (RBAC) w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Przypisz grupy zabezpieczeń usługi AAD do konta Data Lake Storage Gen1. Służy to do sterowania dostępem do konta Usługi Data Lake Storage Gen1 z portalu i operacji zarządzania z portalu lub interfejsów API.
3. Przypisz grupy zabezpieczeń usługi AAD jako listy kontroli dostępu (ACL) w systemie plików Data Lake Storage Gen1.
4. Ponadto można również ustawić zakres adresów IP dla klientów, którzy mogą uzyskiwać dostęp do danych w umiasienia danych Lake Gen1.

Ten artykuł zawiera instrukcje dotyczące korzystania z witryny Azure Portal do wykonywania powyższych zadań. Aby uzyskać szczegółowe informacje na temat implementacji zabezpieczeń magazynu usługi Data Lake Storage Gen1 na poziomie konta i danych, zobacz [Zabezpieczenia w usłudze Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Aby uzyskać szczegółowe informacje na temat implementacji list ACL w programie Data Lake Storage Gen1, zobacz [Omówienie kontroli dostępu w pamięci masowej w uł.o.](data-lake-store-access-control.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Tworzenie grup zabezpieczeń w usłudze Azure Active Directory
Aby uzyskać instrukcje dotyczące tworzenia grup zabezpieczeń usługi AAD i dodawania użytkowników do grupy, zobacz [Zarządzanie grupami zabezpieczeń w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Możesz dodać zarówno użytkowników, jak i inne grupy do grupy w usłudze Azure AD przy użyciu witryny Azure portal. Jednak aby dodać jednostkę usługi do grupy, należy użyć [modułu programu PowerShell usługi Azure AD.](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Przypisywanie użytkowników lub grup zabezpieczeń do kont Usługi Data Lake Storage Gen1
Podczas przypisywania użytkowników lub grup zabezpieczeń do kont Data Lake Storage Gen1 można kontrolować dostęp do operacji zarządzania na koncie za pomocą witryny Azure portal i interfejsów API usługi Azure Resource Manager. 

1. Otwórz konto Data Lake Storage Gen1. W lewym okienku kliknij pozycję **Wszystkie zasoby**, a następnie w bloku Wszystkie zasoby kliknij nazwę konta, do którego chcesz przypisać użytkownika lub grupę zabezpieczeń.

2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Kontrola dostępu (IAM)**. Bloku domyślnie wyświetla właścicieli subskrypcji jako właściciela.
   
    ![Przypisywanie grupy zabezpieczeń do konta usługi Azure Data Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Przypisywanie grupy zabezpieczeń do konta usługi Azure Data Storage Gen1")

3. W bloku **Kontrola dostępu (IAM)** kliknij przycisk **Dodaj,** aby otworzyć blok **Dodaj uprawnienia.** W bloku **Dodaj uprawnienia** wybierz **rolę** dla użytkownika/grupy. Poszukaj grupy zabezpieczeń utworzonej wcześniej w usłudze Azure Active Directory i wybierz ją. Jeśli masz wielu użytkowników i grupy do wyszukiwania, użyj pola tekstowego **Zaznacz,** aby filtrować nazwę grupy. 
   
    ![Dodawanie roli dla użytkownika](./media/data-lake-store-secure-data/adl.add.user.1.png "Dodawanie roli dla użytkownika")
   
    **Właściciel** i **współautor** roli zapewniają dostęp do różnych funkcji administracyjnych na koncie usługi data lake. Dla użytkowników, którzy będą wchodzić w interakcje z danymi w uliczce danych, ale nadal muszą wyświetlać informacje o zarządzaniu kontem, można dodać je do roli **czytnika.** Zakres tych ról jest ograniczony do operacji zarządzania związanych z kontem Data Lake Storage Gen1.
   
    W przypadku operacji na danych poszczególne uprawnienia systemu plików określają, co użytkownicy mogą robić. W związku z tym użytkownik mający rolę czytelnika może wyświetlać tylko ustawienia administracyjne skojarzone z kontem, ale może potencjalnie odczytywać i zapisywać dane na podstawie przypisanych im uprawnień systemu plików. Uprawnienia systemu plików Data Lake Storage Gen1 są opisane w sekcji [Przypisywanie grupy zabezpieczeń jako list ACL do systemu plików Usługi Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Tylko rola **Właściciel** automatycznie umożliwia dostęp do systemu plików. **Współautor,** **Czytnik**i wszystkie inne role wymagają list ACL, aby włączyć dowolny poziom dostępu do folderów i plików.  Rola **Właściciel** udostępnia uprawnienia do plików i folderów superużytnika, których nie można zastąpić za pomocą list ACL. Aby uzyskać więcej informacji na temat sposobu mapowania zasad RBAC do dostępu do danych, zobacz [RBAC dla zarządzania kontem](data-lake-store-security-overview.md#rbac-for-account-management).

4. Jeśli chcesz dodać grupę/użytkownika, której nie ma na liście w bloku **Dodaj uprawnienia,** możesz zaprosić go, wpisując ich adres e-mail w polu **tekstowym Zaznacz,** a następnie wybierając go z listy.
   
    ![Dodawanie grupy zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.2.png "Dodawanie grupy zabezpieczeń")
   
5. Kliknij przycisk **Zapisz**. Powinna zostać wyświetlona grupa zabezpieczeń dodana w sposób pokazany poniżej.
   
    ![Dodano grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.3.png "Dodano grupę zabezpieczeń")

6. Twój użytkownik/grupa zabezpieczeń ma teraz dostęp do konta Data Lake Storage Gen1. Jeśli chcesz zapewnić dostęp do określonych użytkowników, możesz dodać ich do grupy zabezpieczeń. Podobnie, jeśli chcesz odwołać dostęp dla użytkownika, możesz usunąć go z grupy zabezpieczeń. Do konta można również przypisać wiele grup zabezpieczeń. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Przypisywanie użytkowników lub grup zabezpieczeń jako list ACL do systemu plików Data Lake Storage Gen1
Przypisując grupy użytkowników/zabezpieczeń do systemu plików Data Lake Storage Gen1, można ustawić kontrolę dostępu na danych przechowywanych w programie Data Lake Storage Gen1.

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Wyświetlanie danych za pośrednictwem Eksploratora danych](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Wyświetlanie danych za pośrednictwem Eksploratora danych")
2. W bloku **Eksploratora danych** kliknij folder, dla którego chcesz skonfigurować acl, a następnie kliknij pozycję **Dostęp**. Aby przypisać listy ACL do pliku, należy najpierw kliknąć plik, aby wyświetlić jego podgląd, a następnie kliknąć **pozycję Dostęp** z bloku **Podgląd pliku.**
   
    ![Ustawianie list ACL w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustawianie list ACL w systemie plików Data Lake Storage Gen1")
3. **Blok programu Access** zawiera listę właścicieli i przypisane uprawnienia już przypisane do katalogu głównego. Kliknij ikonę **Dodaj,** aby dodać dodatkowe listy ACL dostępu.
    > [!IMPORTANT]
    > Ustawienie uprawnień dostępu dla pojedynczego pliku nie musi przyznawać użytkownikowi/grupie dostępu do tego pliku. Ścieżka do pliku musi być dostępna dla przypisanego użytkownika/grupy. Aby uzyskać więcej informacji i przykładów, zobacz [Typowe scenariusze związane z uprawnieniami](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista dostępu standardowego i niestandardowego](./media/data-lake-store-secure-data/adl.acl.2.png "Lista dostępu standardowego i niestandardowego")
   
   * **Właściciele** i **wszyscy inni** zapewniają dostęp w stylu UNIX, gdzie można określić odczyt, zapis, wykonanie (rwx) do trzech różnych klas użytkowników: właściciel, grupa i inne.
   * **Przypisane uprawnienia** odpowiadają listom ACL POSIX, które umożliwiają ustawianie uprawnień dla określonych nazwanych użytkowników lub grup poza właścicielem lub grupą pliku. 
     
     Aby uzyskać więcej informacji, zobacz [listy ACL HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Aby uzyskać więcej informacji na temat implementacji list ACL w programie Data Lake Storage Gen1, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kliknij ikonę **Dodaj,** aby otworzyć blok **Przypisz uprawnienia.** W tym bloku kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie w obszarze **Wybierz blok użytkownika lub grupy** poszukaj grupy zabezpieczeń utworzonej wcześniej w usłudze Azure Active Directory. Jeśli masz wiele grup do wyszukania, użyj pola tekstowego u góry, aby filtrować nazwę grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij pozycję **Wybierz**.
   
    ![Dodawanie grupy](./media/data-lake-store-secure-data/adl.acl.3.png "Dodawanie grupy")
5. Kliknij **przycisk Wybierz uprawnienia**, wybierz uprawnienia, czy uprawnienia mają być stosowane do cyklicznie i czy chcesz przypisać uprawnienia jako listy ACL dostępu, domyślnej listy ACL lub obu. Kliknij przycisk **OK**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.4.png "Przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji na temat uprawnień w obszarze Data Lake Storage Gen1 i Default/Access ACL, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po kliknięciu **przycisku Ok** w bloku **Wybierz uprawnienia** nowo dodana grupa i skojarzone uprawnienia zostaną wyświetlone w bloku **programu Access.**
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.acl.5.png "Przypisywanie uprawnień do grupy")
   
   > [!IMPORTANT]
   > W bieżącej wersji można mieć maksymalnie 28 wpisów w obszarze **Przypisane uprawnienia**. Jeśli chcesz dodać więcej niż 28 użytkowników, należy utworzyć grupy zabezpieczeń, dodać użytkowników do grup zabezpieczeń, dodać dostęp do tych grup zabezpieczeń dla konta Data Lake Storage Gen1.
   > 
   > 
7. W razie potrzeby można również zmodyfikować uprawnienia dostępu po dodaniu grupy. Wyczyść lub zaznacz pole wyboru dla każdego typu uprawnień (Odczyt, Zapis, Wykonanie) w zależności od tego, czy chcesz usunąć lub przypisać to uprawnienie do grupy zabezpieczeń. Kliknij **przycisk Zapisz,** aby zapisać zmiany, lub **odrzuć,** aby cofnąć zmiany.

## <a name="set-ip-address-range-for-data-access"></a>Ustawianie zakresu adresów IP w celu dostępu do danych
Data Lake Storage Gen1 umożliwia dalsze blokowanie dostępu do magazynu danych na poziomie sieci. Można włączyć zaporę, określić adres IP lub zdefiniować zakres adresów IP dla zaufanych klientów. Po włączeniu tylko klienci, którzy mają adresy IP w zdefiniowanym zakresie, mogą łączyć się ze sklepem.

![Ustawienia zapory i dostęp do adresu IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Ustawienia zapory i adres IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Usuwanie grup zabezpieczeń dla konta Gen1 magazynu usługi Data Lake
Po usunięciu grup zabezpieczeń z kont Data Lake Storage Gen1 zmieniasz tylko dostęp do operacji zarządzania na koncie przy użyciu interfejsów API usługi Azure Portal i Azure Resource Manager.  

Dostęp do danych pozostaje niezmieniony i nadal jest zarządzany przez listy ACL dostępu.  Wyjątkiem są użytkownicy/grupy w owners roli.  Użytkownicy/grupy usunięte z roli Właściciele nie są już superużytami, a ich dostęp powraca do ustawień listy ACL. 

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Kontrola dostępu (IAM)**. 
   
    ![Przypisywanie grupy zabezpieczeń do konta Usługi Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Przypisywanie grupy zabezpieczeń do konta Usługi Data Lake Storage Gen1")
2. W bloku **Kontrola dostępu (IAM)** kliknij grupy zabezpieczeń, które chcesz usunąć. Kliknij pozycję **Usuń**.
   
    ![Usunięto grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.remove.group.png "Usunięto grupę zabezpieczeń")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Usuwanie list ACL grupy zabezpieczeń z systemu plików Data Lake Storage Gen1
Po usunięciu list ACL grupy zabezpieczeń z systemu plików Data Lake Storage Gen1 można zmienić dostęp do danych na koncie Data Lake Storage Gen1.

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów na koncie Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Tworzenie katalogów na koncie Data Lake Storage Gen1")
2. W bloku **Eksplorator danych** kliknij folder, dla którego chcesz usunąć acl, a następnie kliknij pozycję **Dostęp**. Aby usunąć listy ACL dla pliku, należy najpierw kliknąć plik, aby wyświetlić jego podgląd, a następnie kliknąć **pozycję Dostęp** z bloku **Podgląd pliku.** 
   
    ![Ustawianie list ACL w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustawianie list ACL w systemie plików Data Lake Storage Gen1")
3. W **bloku Programu Access** kliknij grupę zabezpieczeń, którą chcesz usunąć. W bloku **Szczegóły programu Access** kliknij pozycję **Usuń**.
   
    ![Przypisywanie uprawnień do grupy](./media/data-lake-store-secure-data/adl.remove.acl.png "Przypisywanie uprawnień do grupy")

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Rozpocznij pracę z usługą Data Lake Storage 1. generacji przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)
* [Wprowadzenie do usługi Data Lake Storage Gen1 przy użyciu sdk .NET](data-lake-store-get-started-net-sdk.md)
* [Dostęp do dzienników diagnostycznych dla pamięci masowej usługi Data Lake Gen1](data-lake-store-diagnostic-logs.md)

