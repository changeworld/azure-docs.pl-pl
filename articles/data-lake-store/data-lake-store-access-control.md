---
title: Omówienie kontroli dostępu w Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu działania kontroli dostępu w usłudze Azure Data Lake magazynu Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 8680a8fa9c460983b88aa4845adcbe72d3a43abf
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325519"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Kontrola dostępu w usłudze Azure Data Lake magazynu Gen1

Azure Data Lake Storage Gen1 wdraża model kontroli dostępu pochodzący z systemu plików HDFS, który z kolei pochodzi z modelu kontroli dostępu POSIX. Ten artykuł zawiera podsumowanie podstaw modelu kontroli dostępu dla programu Data Lake Storage Gen1. Aby dowiedzieć się więcej o modelu kontroli dostępu systemu plików HDFS, zobacz [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listy kontroli dostępu do plików i folderów

Istnieją dwa typy list kontroli dostępu (ACL) — **Listy ACL dostępu** i **Domyślne listy ACL**.

* **Listy ACL dostępu**: listy te kontrolują dostęp do obiektu. Pliki i foldery mają listy ACL dostępu.

* **Domyślne listy ACL**: „szablon” list ACL skojarzonych z folderem, który określa listy ACL dostępu dla wszelkich elementów podrzędnych, które zostały utworzone w tym folderze. Pliki nie mają domyślnych list ACL.

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Zarówno listy ACL dostępu, jak i domyślne listy ACL mają tę samą strukturę.

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Zmiana domyślnej listy ACL w lokalizacji nadrzędnej nie wpływa na listę ACL dostępu lub domyślną listę ACL elementów podrzędnych, które już istnieją.
>
>

## <a name="users-and-identities"></a>Użytkownicy i tożsamości

Każdy plik i folder ma różne uprawnienia do tych tożsamości:

* Użytkownik będący właścicielem
* Grupa będąca właścicielem
* Użytkownicy nazwani
* Grupy nazwane
* Wszyscy pozostali użytkownicy

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (Azure AD). Więc jeśli nie określono inaczej, termin "użytkownik" w kontekście Data Lake Storage Gen1, może oznaczać użytkownika usługi Azure AD lub grupy zabezpieczeń usługi Azure AD.

## <a name="permissions"></a>Uprawnienia

Uprawnienia do obiektu systemu plików to uprawnienia do **odczytu**, **zapisu** i **wykonania**. Mogą one być używane w stosunku do plików i folderów, jak pokazano w poniższej tabeli:

|            |    Plik     |   Folder |
|------------|-------------|----------|
| **Odczyt (R)** | Może odczytywać zawartości pliku | Wymaga uprawnień do **odczytu** i **wykonania**, aby wyświetlać listę zawartości folderu|
| **Zapis (W)** | Może zapisywać w pliku lub dołączać do pliku | Wymaga uprawnień do **zapisu** i **wykonania**, aby tworzyć elementy podrzędne w folderze |
| **Wykonanie (X)** | Nie oznacza niczego w kontekście Data Lake Storage Gen1 | Wymagane w przypadku przechodzenia między elementami podrzędnymi w folderze |

### <a name="short-forms-for-permissions"></a>Krótkie formy uprawnień

Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana postać liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienia. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka |      Co to oznacza     |
|--------------|------------|------------------------|
| 7            | RWX        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | R-X        | Odczyt (R) + wykonanie (X)         |
| 4            | R--        | Odczyt                   |
| 0            | ---        | Brak uprawnień         |


### <a name="permissions-do-not-inherit"></a>Uprawnienia nie są dziedziczone

W modelu stylu POSIX używanym przez Data Lake Storage Gen1 uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy, uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych.

## <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

Poniżej przedstawiono kilka typowych scenariuszy, które pomagają zrozumieć, jakie uprawnienia są wymagane do wykonania operacji na koncie Data Lake Storage Gen1.

### <a name="permissions-needed-to-read-a-file"></a>Uprawnienia wymagane do odczytu pliku

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* W przypadku pliku, który będzie odczytywany, wywołujący musi mieć uprawnienia do **odczytu**.
* W przypadku wszystkich folderów w strukturze folderów, które zawierają plik, wywołujący musi mieć uprawnienia do **wykonania**.

### <a name="permissions-needed-to-append-to-a-file"></a>Uprawnienia wymagane do dołączania do pliku

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* W przypadku pliku, do którego będzie dołączana zawartość, wywołujący musi mieć uprawnienia do **zapisu**.
* W przypadku wszystkich folderów, które zawierają plik, wywołujący musi mieć uprawnienia do **wykonania**.

### <a name="permissions-needed-to-delete-a-file"></a>Uprawnienia wymagane do usunięcia pliku

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* W przypadku folderu nadrzędnego wywołujący musi mieć uprawnienia do **zapisu i wykonania**.
* W przypadku innych folderów w ścieżce pliku wywołujący musi mieć uprawnienia do **wykonania**.



> [!NOTE]
> Uprawnienia do zapisu dla pliku nie są wymagane do usunięcia go, jeśli dwa poprzednie warunki pozostają spełnione.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Uprawnienia wymagane do wyliczenia folderu

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* W przypadku folderu do wyliczenia wywołujący musi mieć uprawnienia do **odczytu i wykonania**.
* W przypadku wszystkich folderów nadrzędnych wywołujący musi mieć uprawnienia do **wykonania**.



## <a name="the-super-user"></a>Administrator

Administrator ma najwięcej uprawnień spośród wszystkich użytkowników usługi Data Lake Store. Administrator:

* ma uprawnienia RWX do **wszystkich** plików i folderów;
* może zmieniać uprawnienia do dowolnego pliku lub folderu;
* może zmieniać właściciela lub grupę będącą właścicielem dla dowolnego pliku lub folderu.

Na platformie Azure konta Data Lake Storage Gen1 ma kilka ról platformy Azure, w tym:

* Właściciele
* Współautorzy
* Czytelnicy

Wszyscy w **właścicieli** roli konta Data Lake Storage Gen1 jest automatycznie administratorami dla tego konta. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu oparta na rolach](../role-based-access-control/role-assignments-portal.md).
Jeśli chcesz utworzyć niestandardową rolę kontroli dostępu opartej na rolach (RBAC) z uprawnieniami administratora, musi ona mieć następujące uprawnienia:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>Użytkownik będący właścicielem

Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik będący właścicielem *nie może* zmienić użytkownika będącego właścicielem pliku lub folderu. Tylko administratorzy mogą zmieniać użytkowników będących właścicielami pliku lub folderu.
>
>

## <a name="the-owning-group"></a>Grupa będąca właścicielem

Na listach ACL w modelu POSIX każdy użytkownik jest skojarzony z „grupą główną”. Przykładowo użytkownik „Alicja” może należeć do grupy „Finanse”. Alicja może również należeć do wielu grup, ale jedna grupa jest zawsze wyznaczona jako jej grupa główna. W modelu POSIX, gdy Alicja tworzy plik, na grupę będącą właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”.

Po utworzeniu nowego elementu systemu plików Data Lake Storage Gen1 przypisuje wartość grupie będącej właścicielem.

* **Przypadek 1**: folder główny „/”. Ten folder jest tworzony po utworzeniu konta Data Lake Storage Gen1. W takim przypadku grupa będąca właścicielem jest ustawiana na użytkownika, który utworzył konto.
* **Przypadek 2** (każdy inny przypadek): gdy tworzony jest nowy element, grupa będąca właścicielem jest kopiowana z folderu nadrzędnego.

Grupa będąca właścicielem w przeciwnym razie działa podobnie do przypisanych uprawnień dla innych użytkowników/grup.

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa będąca właścicielem *nie może* zmienić list kontroli dostępu do pliku lub folderu.  Gdy grupa będąca właścicielem zostanie ustawiona na użytkownika, który utworzył konto w przypadku folderu głównego (**Przypadek 1** powyżej), jedno konto użytkownika nie może zapewnić uprawnień za pośrednictwem grupy będącej właścicielem.  Możesz przypisać te uprawnienia do prawidłowej grupy użytkowników, jeśli ma to zastosowanie.

## <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Następujące psuedocode przedstawia algorytm kontroli dostępu dla kont usługi Data Lake Storage Gen1.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users
    if (is_superuser(user)) :
      return True

  # Handle the owning user. Note that mask is not used.
    if (is_owning_user(path, user))
      perms = get_perms_for_owning_user(path)
      return ( (desired_perms & perms) == desired_perms )

  # Handle the named user. Note that mask is used.
  if (user in get_named_users( path )) :
      perms = get_perms_for_named_user(path, user)
      mask = get_mask( path )
      return ( (desired_perms & perms & mask ) == desired_perms)

  # Handle groups (named groups and owning group)
  belongs_to_groups = [g for g in get_groups(path) if is_member_of(user, g) ]
  if (len(belongs_to_groups)>0) :
    group_perms = [get_perms_for_group(path,g) for g in belongs_to_groups]
    perms = 0
    for p in group_perms : perms = perms | p # bitwise OR all the perms together
    mask = get_mask( path )
    return ( (desired_perms & perms & mask ) == desired_perms)

  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

## <a name="the-mask"></a>Maska

Jak pokazano w algorytmu kontroli dostępu, maski ogranicza dostęp do **użytkowników nazwanych**, **grupy będącej właścicielem**, i **nazwanych grup**.  

> [!NOTE]
> W przypadku nowego konta Data Lake Storage Gen1 maski dla listy ACL dostępu folderu głównego ("/") są domyślnie maskami RWX.
>
>

### <a name="the-sticky-bit"></a>Atrybut sticky bit

Sticky bit jest bardziej zaawansowaną funkcją systemu plików POSIX. W kontekście usługi Data Lake Storage Gen1 jest mało prawdopodobne, że działania atrybutu sticky bit będzie potrzebny.

Poniższej tabeli przedstawiono sposób działania atrybutu sticky bit w Data Lake Storage Gen1.

| Grupa użytkowników         | Plik    | Folder |
|--------------------|---------|-------------------------|
| Sticky bit **WYŁ.** | Brak wpływu   | Brak wpływu.           |
| Sticky bit **WŁ.**  | Brak wpływu   | Uniemożliwia wszystkich użytkownikom poza **administratorami** i **użytkownikiem będącym właścicielem** elementu podrzędnego usunięcie lub zmianę nazwy tego elementu podrzędnego.               |

Atrybut sticky bit nie jest wyświetlany w witrynie Azure Portal.

## <a name="permissions-on-new-files-and-folders"></a>Uprawnienia do nowych plików i folderów

Gdy nowy plik lub folder jest tworzony w istniejącym folderze, domyślna lista ACL w folderze nadrzędnym określa:

- domyślną listę ACL i listę ACL dostępu folderu podrzędnego;
- listę ACL dostępu pliku podrzędnego (pliki nie mają domyślnej listy ACL);

### <a name="the-access-acl-of-a-child-file-or-folder"></a>listę ACL dostępu pliku lub folderu podrzędnego.

Gdy tworzony jest plik lub folder podrzędny, domyślna lista ACL lokalizacji nadrzędnej jest kopiowana jako lista ACL dostępu pliku lub folderu podrzędnego. Ponadto, jeśli **inny** użytkownik ma uprawnienia RWX w domyślnej liście ACL lokalizacji nadrzędnej, zostaje usunięty z listy ACL dostępu elementu podrzędnego.

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

W większości przypadków poprzednie informacje są wszystkim, co należy wiedzieć o sposobie określania listy ACL dostępu elementu podrzędnego. Niemniej jednak jeśli znasz systemy POSIX i chcesz lepiej zrozumieć sposób działania tej transformacji, zapoznaj się z sekcją [Rola maski umask w tworzeniu listy ACL dostępu do nowych plików i folderów](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) poniżej w tym artykule.


### <a name="a-child-folders-default-acl"></a>Domyślna lista ACL folderu podrzędnego

Gdy folder podrzędny jest tworzony w folderze nadrzędnym, domyślna lista ACL folderu nadrzędnego jest kopiowana bez zmian do domyślnej listy ACL folderu podrzędnego.

![Data Lake Storage Gen1 listy kontroli dostępu](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-storage-gen1"></a>Zaawansowane tematy związane z listami ACL w Data Lake Storage Gen1

Poniżej przedstawiono niektóre zaawansowane tematy, które pomagają zrozumieć, jak listy ACL są określane dla plików Data Lake Storage Gen1 lub folderów.

### <a name="umask"></a>maska umask

Podczas tworzenia pliku lub folderu, maska umask jest używana do modyfikowania konfiguracji domyślnej listy ACL dla elementu podrzędnego. Maska umask jest 9-bitową 9-bitową wartością w foldery nadrzędne, które zawiera wartość RWX **użytkownika będącego właścicielem**, **grupy będącej właścicielem**, i **innych**.

Maska umask dla usługi Azure Data Lake Storage Gen1 stałą wartość, która jest równa 007. Ta wartość przekłada się na

* umask.owning_user = 0 #---
* umask.owning_group = 0 #---
* umask.Other = 7 # RWX

Ta wartość maski umask skutecznie oznacza, że wartość dla innych nigdy nie są przesyłane domyślnie na nowe elementy podrzędne — niezależnie od tego, co wskazuje domyślną listę ACL. 

Następujące psuedocode pokazuje, jak maska umask jest stosowane podczas tworzenia listy ACL dla elementu podrzędnego.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    foreach entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```



## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Często zadawane pytania dotyczące list ACL w Data Lake Storage Gen1

Poniżej zamieszczono kilka pytań, które często zadawanych dotyczące list ACL w Data Lake Storage Gen1.

### <a name="do-i-have-to-enable-support-for-acls"></a>Czy muszę włączyć obsługę list ACL?

Nie. Kontrola dostępu za pośrednictwem list ACL jest zawsze włączona dla konta Data Lake Storage Gen1.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Jakie uprawnienia są wymagane do rekursywnego usunięcia folderu i jego zawartości?

* Folder nadrzędny musi mieć uprawnienia do **zapisu i wykonania**.
* Folder do usunięcia i każdy folder w tym folderze muszą mieć uprawnienia do **odczytu, zapisu i wykonania**.

> [!NOTE]
> Do usuwania plików w folderach nie potrzebujesz uprawnienia do zapisu. Ponadto **nigdy** nie można usunąć folderu głównego „/”.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Kto jest właścicielem pliku lub folderu?

Twórca pliku lub folderu staje się jego właścicielem.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Jaka grupa zostaje ustawiona jako grupa będąca właścicielem pliku lub folderu w momencie jego tworzenia?

Grupa będąca właścicielem jest kopiowana z grupy będącej właścicielem folderu nadrzędnego, w którym tworzony jest nowy plik lub folder.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jestem właścicielem pliku, ale nie mam wymaganych uprawnień RWX. Co mam zrobić?

Użytkownik będący właścicielem może zmienić uprawnienia do pliku, aby przydzielić sobie wymagane uprawnienia RWX.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Na listach ACL przeglądanych w witrynie Azure Portal są widoczne nazwy użytkowników, natomiast w przypadku korzystania z interfejsów API wyświetlane są identyfikatory GUID. Dlaczego?

Wpisy na listach ACL są przechowywane jako identyfikatory GUID odpowiadające użytkownikom w usłudze Azure AD. Interfejsy API zwracają identyfikator GUID w faktycznej postaci. Witryna Azure Portal stara się ułatwić korzystanie z list ACL, dlatego identyfikatory GUID są w miarę możliwości zamieniane na przyjazne nazwy.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Dlaczego na listach ACL w witrynie Azure Portal są czasami wyświetlane identyfikatory GUID?

Identyfikator GUID jest wyświetlany w przypadku, gdy dany użytkownik nie istnieje już w usłudze Azure AD. Zwykle dzieje się tak, gdy użytkownik opuścił firmę lub jego konto w usłudze Azure AD zostało usunięte.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Usługa Data Lake Storage Gen1 obsługuje dziedziczenie list ACL?

Nie, ale domyślne listy kontroli dostępu mogą być używane do ustawienia list ACL dla podrzędnych plików i folderów, które zostały nowo utworzone w folderze nadrzędnym.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Gdzie można dowiedzieć się więcej na temat modelu kontroli dostępu POSIX?

* [Listy kontroli dostępu w modelu POSIX w systemie Linux](https://www.linux.com/news/posix-acls-linux)

* [Przewodnik po uprawnieniach systemu plików HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [POSIX — często zadawane pytania](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [Listy ACL modelu POSIX w systemie Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [Listy ACL korzystające z list kontroli dostępu w systemie Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zobacz także

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
