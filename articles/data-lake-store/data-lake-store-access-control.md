---
title: Omówienie kontroli dostępu w programie Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak działa kontrola dostępu w usłudze Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 276e691351d852d6dcb0075d47bf33af6767fc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260334"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Access control in Azure Data Lake Storage Gen1 (Kontrola dostępu w usłudze Azure Data Lake Storage Gen1)

Usługa Azure Data Lake Storage Gen1 implementuje model kontroli dostępu, który wywodzi się z usługi HDFS, która z kolei wywodzi się z modelu kontroli dostępu POSIX. W tym artykule podsumowano podstawy modelu kontroli dostępu dla usługi Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Listy kontroli dostępu do plików i folderów

Istnieją dwa typy list kontroli dostępu (ACL) — **Listy ACL dostępu** i **Domyślne listy ACL**.

* **Listy ACL dostępu**: listy te kontrolują dostęp do obiektu. Pliki i foldery mają listy ACL dostępu.

* **Domyślne listy ACL**: „szablon” list ACL skojarzonych z folderem, który określa listy ACL dostępu dla wszelkich elementów podrzędnych, które zostały utworzone w tym folderze. Pliki nie mają domyślnych list ACL.


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
| **Wykonanie (X)** | Nic nie znaczy w kontekście Data Lake Storage Gen1 | Wymagane w przypadku przechodzenia między elementami podrzędnymi w folderze |

### <a name="short-forms-for-permissions"></a>Krótkie formy uprawnień

Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana postać liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienia. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka |      Co to oznacza     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | `R-X`        | Odczyt (R) + wykonanie (X)         |
| 4            | `R--`        | Odczyt                   |
| 0            | `---`        | Brak uprawnień         |


### <a name="permissions-do-not-inherit"></a>Uprawnienia nie są dziedziczone

W modelu w stylu POSIX, który jest używany przez Data Lake Storage Gen1 uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy, uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych.

## <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

Oto kilka typowych scenariuszy, które pomogą Ci zrozumieć, które uprawnienia są potrzebne do wykonywania niektórych operacji na koncie Usługi Data Lake Storage Gen1.

| Operacja | Obiekt              |    /      | Okręg wyborczy Seattle/   | Portland/   | Plik Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Odczyt      | Plik Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Dołącz do | Plik Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Usuń    | Plik Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Utwórz    | Plik Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


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

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (Azure AD). Tak więc, o ile nie zaznaczono inaczej, "użytkownik" w kontekście usługi Data Lake Storage Gen1 może oznaczać użytkownika usługi Azure AD lub grupę zabezpieczeń usługi Azure AD.

### <a name="the-super-user"></a>Administrator

Superużyt użytkownik ma najwięcej praw wszystkich użytkowników na koncie Data Lake Storage Gen1. Administrator:

* ma uprawnienia RWX do **wszystkich** plików i folderów;
* może zmieniać uprawnienia do dowolnego pliku lub folderu;
* może zmieniać właściciela lub grupę będącą właścicielem dla dowolnego pliku lub folderu.

Wszyscy użytkownicy, którzy są częścią **roli Właściciele** dla konta Data Lake Storage Gen1 są automatycznie superużytami.

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

Ponieważ nie ma żadnej "grupy podstawowej" skojarzonej z użytkownikami w umiań magazynu usługi Data Lake, grupa posiadająca jest przypisywana poniżej.

**Przypisywanie grupy stanowiącej nowy plik lub folder**

* **Przypadek 1**: folder główny „/”. Ten folder jest tworzony podczas tworzenia konta Data Lake Storage Gen1. W takim przypadku grupa właścicielka jest ustawiona na identyfikator GUID z zerowym.  Ta wartość nie zezwala na dostęp.  Jest to symbol zastępczy do czasu przypisania grupy.
* **Przypadek 2** (każdy inny przypadek): gdy tworzony jest nowy element, grupa będąca właścicielem jest kopiowana z folderu nadrzędnego.

**Zmiana grupy właścicielki**

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa będąca właścicielem *nie może* zmienić list kontroli dostępu do pliku lub folderu.
>
> W przypadku kont utworzonych we wrześniu 2018 r. lub wcześniej grupa posiadaczy została ustawiona na użytkownika, który utworzył konto w przypadku folderu głównego dla **sprawy 1**powyżej.  Jedno konto użytkownika nie jest prawidłowe do udzielania uprawnień za pośrednictwem grupy stanowiącej, w związku z tym żadne uprawnienia nie są przyznawane przez to ustawienie domyślne. Uprawnienie to można przypisać do prawidłowej grupy użytkowników.


## <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Poniższy pseudokod reprezentuje algorytm sprawdzania dostępu dla kont Usługi Data Lake Storage Gen1.

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
      return ( (desired_perms & entry.permissions) == desired_perms )

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

Jak pokazano w algorytmie sprawdzania dostępu, maska ogranicza dostęp dla **nazwanych użytkowników,** **grupy stanowiącej**i **nazwanych grup.**  

> [!NOTE]
> W przypadku nowego konta Data Lake Storage Gen1 maska listy ACL dostępu folderu głównego ("/") jest domyślnie domyślna na RWX.
>
>

### <a name="the-sticky-bit"></a>Atrybut sticky bit

Sticky bit jest bardziej zaawansowaną funkcją systemu plików POSIX. W kontekście Data Lake Storage Gen1 jest mało prawdopodobne, że bit lepki będzie potrzebny. Podsumowując, jeśli bit przyklejony jest włączony w folderze, element podrzędny może zostać usunięty lub zmieniony tylko przez użytkownika elementu podrzędnego.

Atrybut sticky bit nie jest wyświetlany w witrynie Azure Portal.

## <a name="default-permissions-on-new-files-and-folders"></a>Domyślne uprawnienia do nowych plików i folderów

Gdy nowy plik lub folder jest tworzony w istniejącym folderze, domyślna lista ACL w folderze nadrzędnym określa:

- domyślną listę ACL i listę ACL dostępu folderu podrzędnego;
- listę ACL dostępu pliku podrzędnego (pliki nie mają domyślnej listy ACL);

### <a name="umask"></a>maska umask

Podczas tworzenia pliku lub folderu maska umask służy do modyfikowania sposobu ustawiania domyślnych list ACL na elemencie podrzędnym. umask jest wartością 9-bitową w folderach nadrzędnych, która zawiera wartość RWX dla **użytkownika,** **grupy posiadającej**i **innych**.

Maska umask dla usługi Azure Data Lake Storage Gen1 jest stałą wartością ustawioną na 007. Wartość ta przekłada się na

| składnik maski umask     | Forma liczbowa | Forma krótka | Znaczenie |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | W przypadku posiadania użytkownika skopiuj domyślnącl ACL rodzica do listy ACL dostępu dziecka | 
| umask.owning_group  |    0         |   `---`      | W przypadku grupy właścicielki skopiuj domyślnącl ACL nadrzędnego do listy ACL dostępu dziecka | 
| umask.other         |    7         |   `RWX`      | W przypadku innych, usuń wszystkie uprawnienia do listy ACL dostępu dziecka |

Wartość maski używane przez usługę Azure Data Lake Storage Gen1 skutecznie oznacza, że wartość dla innych nigdy nie jest przesyłany domyślnie na nowe dzieci — niezależnie od tego, co wskazuje domyślna k. ACL. 

Poniższy pseudokod pokazuje, jak maska umask jest stosowana podczas tworzenia list ACL dla elementu podrzędnego.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Typowe pytania dotyczące list ACL w umiań magazynowania usługi Data Lake

### <a name="do-i-have-to-enable-support-for-acls"></a>Czy muszę włączyć obsługę list ACL?

Nie. Kontrola dostępu za pośrednictwem list ACL jest zawsze włączeniu dla konta Data Lake Storage Gen1.

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

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Czy Data Lake Storage Gen1 obsługuje dziedziczenie list ACL?

Nie, ale domyślne listy kontroli dostępu mogą być używane do ustawienia list ACL dla podrzędnych plików i folderów, które zostały nowo utworzone w folderze nadrzędnym.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Gdzie można dowiedzieć się więcej na temat modelu kontroli dostępu POSIX?

* [Listy kontroli dostępu w modelu POSIX w systemie Linux](https://www.linux.com/news/posix-acls-linux)
* [Przewodnik po uprawnieniach hdfs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX — często zadawane pytania](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Listy ACL modelu POSIX w systemie Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Listy ACL korzystające z list kontroli dostępu w systemie Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zobacz też

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
