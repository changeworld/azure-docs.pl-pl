---
title: Omówienie kontroli dostępu w Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu działania kontroli dostępu w usłudze Azure Data Lake magazynu Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 211cb32298b17bb9e4023bf8bc74233c3916f58d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879110"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Kontrola dostępu w usłudze Azure Data Lake magazynu Gen1

Azure Data Lake Storage Gen1 wdraża model kontroli dostępu pochodzący z systemu plików HDFS, który z kolei pochodzi z modelu kontroli dostępu POSIX. Ten artykuł zawiera podsumowanie podstaw modelu kontroli dostępu dla programu Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Listy kontroli dostępu do plików i folderów

Istnieją dwa typy list kontroli dostępu (ACL) — **Listy ACL dostępu** i **Domyślne listy ACL**.

* **Dostęp do listy ACL**: Te kontrolują dostęp do obiektu. Pliki i foldery mają listy ACL dostępu.

* **Domyślne listy ACL**: "Szablon" list ACL skojarzonych z folderem, które określają listy ACL dostępu dla wszelkich elementów podrzędnych, które są tworzone w tym folderze. Pliki nie mają domyślnych list ACL.


Zarówno listy ACL dostępu, jak i domyślne listy ACL mają tę samą strukturę.



> [!NOTE]
> Zmiana domyślnej listy ACL w lokalizacji nadrzędnej nie wpływa na listę ACL dostępu lub domyślną listę ACL elementów podrzędnych, które już istnieją.
>
>

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
| 7            | `RWX`        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | `R-X`        | Odczyt (R) + wykonanie (X)         |
| 4            | `R--`        | Odczyt                   |
| 0            | `---`        | Brak uprawnień         |


### <a name="permissions-do-not-inherit"></a>Uprawnienia nie są dziedziczone

W modelu stylu POSIX używanym przez Data Lake Storage Gen1 uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy, uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych.

## <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

Poniżej przedstawiono kilka typowych scenariuszy, które pomagają zrozumieć, jakie uprawnienia są wymagane do wykonania operacji na koncie Data Lake Storage Gen1.

| Operacja | Obiekt              |    /      | Seattle /   | Portland /   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Odczyt      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Dołączanie do | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Usuwanie    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Przycisk Utwórz    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List      | /Seattle/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Uprawnienia do zapisu dla pliku nie są wymagane do usunięcia go, jeśli dwa poprzednie warunki pozostają spełnione.
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

### <a name="the-super-user"></a>Administrator

Administrator ma najwięcej uprawnień spośród wszystkich użytkowników w ramach konta Data Lake Storage Gen1. Administrator:

* ma uprawnienia RWX do **wszystkich** plików i folderów;
* może zmieniać uprawnienia do dowolnego pliku lub folderu;
* może zmieniać właściciela lub grupę będącą właścicielem dla dowolnego pliku lub folderu.

Wszyscy użytkownicy, które są częścią **właścicieli** roli konta Data Lake Storage Gen1 są automatycznie administrator.

### <a name="the-owning-user"></a>Użytkownik będący właścicielem

Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik będący właścicielem *nie może* zmienić użytkownika będącego właścicielem pliku lub folderu. Tylko administratorzy mogą zmieniać użytkowników będących właścicielami pliku lub folderu.
>
>

### <a name="the-owning-group"></a>Grupa będąca właścicielem

**Tło**

Na listach ACL w modelu POSIX każdy użytkownik jest skojarzony z „grupą główną”. Przykładowo użytkownik „Alicja” może należeć do grupy „Finanse”. Alicja może również należeć do wielu grup, ale jedna grupa jest zawsze wyznaczona jako jej grupa główna. W modelu POSIX, gdy Alicja tworzy plik, na grupę będącą właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”. Grupa będąca właścicielem w przeciwnym razie działa podobnie do przypisanych uprawnień dla innych użytkowników/grup.

Ponieważ ma skojarzone z użytkownikami w Data Lake Storage Gen1 nie "grupą główną", grupa będąca właścicielem jest przypisany zgodnie z poniższymi instrukcjami.

**Przypisywanie grupa będąca właścicielem dla nowego pliku lub folderu**

* **Przypadek 1**: Folder główny "/". Ten folder jest tworzony po utworzeniu konta Data Lake Storage Gen1. W takim przypadku grupa będąca właścicielem jest równa identyfikator GUID wszystko od zera.  Ta wartość nie zezwala na dostęp do wszystkich.  Symbol zastępczy jest do tego czasu, które grupa jest przypisana.
* **Przypadek 2** (każdy inny przypadek): Gdy zostanie utworzony nowy element, grupa będąca właścicielem jest kopiowana z folderu nadrzędnego.

**Zmiana grupy będącej właścicielem**

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa będąca właścicielem *nie może* zmienić list kontroli dostępu do pliku lub folderu.
>
> W przypadku kont utworzone przed września 2018 r. grupa będąca właścicielem została ustawiona na użytkownika, który utworzył konto w przypadku folderu głównego elementu **przypadek 1**powyżej.  Pojedyncze konto użytkownika nie jest prawidłową zapewnić uprawnień za pośrednictwem grupy będącej właścicielem, dlatego nie uprawnienia są przyznawane przez to ustawienie domyślne. To uprawnienie można przypisać do prawidłowej grupy użytkowników.


## <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Poniższym pseudokodzie przedstawia algorytm kontroli dostępu dla kont usługi Data Lake Storage Gen1.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & e.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Maska

Jak pokazano w algorytmu kontroli dostępu, maski ogranicza dostęp do **użytkowników nazwanych**, **grupy będącej właścicielem**, i **nazwanych grup**.  

> [!NOTE]
> W przypadku nowego konta Data Lake Storage Gen1 maski dla listy ACL dostępu folderu głównego ("/") są domyślnie maskami RWX.
>
>

### <a name="the-sticky-bit"></a>Atrybut sticky bit

Sticky bit jest bardziej zaawansowaną funkcją systemu plików POSIX. W kontekście usługi Data Lake Storage Gen1 jest mało prawdopodobne, że działania atrybutu sticky bit będzie potrzebny. Podsumowując Jeśli atrybut sticky bit jest włączona na folder, element podrzędny tylko można usunąć lub zmieniona przez użytkownika będącego właścicielem elementu podrzędnego.

Atrybut sticky bit nie jest wyświetlany w witrynie Azure Portal.

## <a name="default-permissions-on-new-files-and-folders"></a>Uprawnienia domyślne dla nowych plików i folderów

Gdy nowy plik lub folder jest tworzony w istniejącym folderze, domyślna lista ACL w folderze nadrzędnym określa:

- domyślną listę ACL i listę ACL dostępu folderu podrzędnego;
- listę ACL dostępu pliku podrzędnego (pliki nie mają domyślnej listy ACL);

### <a name="umask"></a>maska umask

Podczas tworzenia pliku lub folderu, maska umask jest używana do modyfikowania konfiguracji domyślnej listy ACL dla elementu podrzędnego. Maska umask jest 9-bitową 9-bitową wartością w foldery nadrzędne, które zawiera wartość RWX **użytkownika będącego właścicielem**, **grupy będącej właścicielem**, i **innych**.

Maska umask dla usługi Azure Data Lake Storage Gen1 stałą wartość, która jest równa 007. Ta wartość przekłada się na

| Maska umask składnika     | Forma liczbowa | Forma krótka | Znaczenie |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Dla użytkownika będącego właścicielem, skopiuj nadrzędnego domyślnej listy ACL do listy ACL dostępu elementu podrzędnego | 
| umask.owning_group  |    0         |   `---`      | Dla grupy będącej właścicielem, skopiuj nadrzędnego domyślnej listy ACL do listy ACL dostępu elementu podrzędnego | 
| umask.Other         |    7         |   `RWX`      | Dla pozostałych Usuń wszystkie uprawnienia dla listy ACL dostępu elementu podrzędnego |

Wartość maski umask, używane przez usługi Azure Data Lake Storage Gen1 skutecznie oznacza, że wartość dla innych nigdy nie są przesyłane domyślnie na nowe elementy podrzędne — niezależnie od tego, co wskazuje domyślną listę ACL. 

Poniższym pseudokodzie pokazuje, jak maska umask jest stosowane podczas tworzenia listy ACL dla elementu podrzędnego.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
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
* [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX — często zadawane pytania](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Listy ACL modelu POSIX w systemie Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Listy ACL korzystające z list kontroli dostępu w systemie Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zobacz także

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
