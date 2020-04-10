---
title: Omówienie kontroli dostępu w usłudze Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft
description: Dowiedz się, jak działa kontrola dostępu w usłudze Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992219"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Kontrola dostępu w usłudze Azure Data Lake Storage Gen2

Usługa Azure Data Lake Storage Gen2 implementuje model kontroli dostępu, który obsługuje zarówno azure kontroli dostępu opartej na rolach (RBAC) i POSIX-like list kontroli dostępu (Listy kontroli dostępu). W tym artykule podsumowano podstawy modelu kontroli dostępu dla usługi Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

RBAC używa przypisań ról do skutecznego stosowania zestawów uprawnień do *podmiotów zabezpieczeń*. *Podmiot zabezpieczeń* jest obiektem reprezentującym użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną zdefiniowaną w usłudze Azure Active Directory (AD), która żąda dostępu do zasobów platformy Azure.

Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład: konta usługi Azure Storage). W przypadku usługi Azure Storage, a w konsekwencji usługi Azure Data Lake Storage Gen2, ten mechanizm został rozszerzony na zasób kontenera (systemu plików).

Aby dowiedzieć się, jak przypisać role do podmiotów zabezpieczeń w zakresie konta magazynu, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejki za pomocą funkcji RBAC w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Użytkownik-gość nie może utworzyć przypisania roli.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Wpływ przypisań ról na listy kontroli dostępu do plików i katalogów

Podczas korzystania z przypisania roli RBAC jest potężnym mechanizmem do kontrolowania uprawnień dostępu, jest to mechanizm bardzo grubo ziarnisty w stosunku do list ACL. Najmniejsza szczegółowość dla RBAC jest na poziomie kontenera i zostanie to ocenione z wyższym priorytetem niż listy ACL. W związku z tym jeśli przypisać rolę do podmiotu zabezpieczeń w zakresie kontenera, że podmiot zabezpieczeń ma poziom autoryzacji skojarzony z tej roli dla wszystkich katalogów i plików w tym kontenerze, niezależnie od przypisań ACL.

Gdy podmiot zabezpieczeń otrzymuje uprawnienia danych RBAC za pośrednictwem [wbudowanej roli](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)lub za pośrednictwem roli niestandardowej, uprawnienia te są oceniane najpierw po autoryzacji żądania. Jeśli żądana operacja jest autoryzowana przez przypisania RBAC podmiotu zabezpieczeń, autoryzacja jest natychmiast rozpoznawana i nie przeprowadza się żadnych dodatkowych kontroli listy ACL. Alternatywnie jeśli podmiot zabezpieczeń nie ma przypisania RBAC lub operacja żądania nie jest zgodna z przypisanym uprawnieniem, następnie sprawdza listę ACL są wykonywane w celu ustalenia, czy podmiot zabezpieczeń jest upoważniony do wykonania żądanej operacji.

> [!NOTE]
> Jeśli podmiot zabezpieczeń został przypisany do wbudowanego przypisania roli Właściciel obiektu blob magazynu, podmiot zabezpieczeń jest uważany za *superużyt i* ma pełny dostęp do wszystkich operacji mutowania, w tym do ustawienia właściciela katalogu lub pliku, a także list ACL dla katalogów i plików, dla których nie są właścicielami. Dostęp do superużytów jest jedynym autoryzowanym sposobem zmiany właściciela zasobu.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Uwierzytelnianie klucza udostępnionego i sygnatury dostępu współdzielonego (SAS)

Usługa Azure Data Lake Storage Gen2 obsługuje metody uwierzytelniania klucza udostępnionego i sygnatury dostępu Współdzielonego. Cechą charakterystyczną tych metod uwierzytelniania jest to, że żadna tożsamość nie jest skojarzona z wywołującym i dlatego nie można wykonać autoryzacji opartej na uprawnieniach jednostki zabezpieczeń.

W przypadku klucza udostępnionego wywołujący skutecznie uzyskuje dostęp "super-user", co oznacza pełny dostęp do wszystkich operacji na wszystkich zasobach, w tym ustawienia właściciela i zmiany list ACL.

Tokeny sygnatury dostępu Współdzielonego obejmują dozwolone uprawnienia jako część tokenu. Uprawnienia zawarte w tokenie sygnatury dostępu Współdzielonego są skutecznie stosowane do wszystkich decyzji dotyczących autoryzacji, ale nie są wykonywane żadne dodatkowe kontrole listy ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Listy kontroli dostępu w plikach i katalogach

Podmiot zabezpieczeń można skojarzyć z poziomem dostępu dla plików i katalogów. Te skojarzenia są przechwytywane *na liście kontroli dostępu (ACL)*. Każdy plik i katalog na koncie magazynu ma listę kontroli dostępu.

> [!NOTE]
> Listy ACL mają zastosowanie tylko do podmiotów zabezpieczeń w tej samej dzierżawie. 

Jeśli przypisano rolę do podmiotu zabezpieczeń na poziomie konta magazynu, można użyć listy kontroli dostępu, aby udzielić tej jednostki zabezpieczeń podwyższonego dostępu do określonych plików i katalogów.

Nie można użyć listy kontroli dostępu, aby zapewnić poziom dostępu, który jest niższy niż poziom przyznany przez przypisanie roli. Na przykład jeśli przypisano rolę [współautora danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) do podmiotu zabezpieczeń, nie można użyć listy kontroli dostępu, aby zapobiec zapisywaniu tego podmiotu zabezpieczeń do katalogu.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Ustawianie uprawnień do poziomu plików i katalogów przy użyciu list kontroli dostępu

Aby ustawić uprawnienia do poziomu plików i katalogów, zobacz dowolne z następujących artykułów:

|||
|--------|-----------|
|Eksplorator usługi Azure Storage |[Używanie Eksploratora usługi Azure Storage do zarządzania katalogami, plikami i listami AL w usłudze Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Zarządzanie katalogami, plikami i listami ALs w usłudze Azure Data Lake Storage Gen2 za pomocą platformy .NET](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Używanie języka Java do zarządzania katalogami, plikami i listami ACl w usłudze Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Używanie języka Python do zarządzania katalogami, plikami i listami AL w usłudze Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Zarządzanie katalogami, plikami i listami ALs w usłudze Azure Data Lake Storage Gen2 za pomocą programu PowerShell](data-lake-storage-directory-file-acl-powershell.md)|
|Interfejs wiersza polecenia platformy Azure|[Zarządzanie katalogami, plikami i listami ALs w usłudze Azure Data Lake Storage Gen2 za pomocą interfejsu wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)|
|Interfejs API REST |[Ścieżka — aktualizacja](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Jeśli podmiot zabezpieczeń jest jednostką *usługi,* ważne jest, aby użyć identyfikatora obiektu jednostki usługi, a nie identyfikator obiektu rejestracji powiązanej aplikacji. Aby uzyskać identyfikator obiektu jednostki usługi, otwórz narzędzie platformy Azure `az ad sp show --id <Your App ID> --query objectId`CLI, a następnie użyj tego polecenia: . pamiętaj, aby `<Your App ID>` zastąpić symbol zastępczy identyfikatorem aplikacji podczas rejestracji aplikacji.

### <a name="types-of-access-control-lists"></a>Typy list kontroli dostępu

Istnieją dwa rodzaje list kontroli dostępu: *dostęp do list ACL* i *domyślne listy ACL*.

Listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

Domyślne listy ACL to szablony list ACL skojarzonych z katalogiem, które określają listy ACL dostępu dla wszystkich elementów podrzędnych utworzonych w tym katalogu. Pliki nie mają domyślnych list ACL.

Zarówno listy ACL dostępu, jak i domyślne listy ACL mają tę samą strukturę.

> [!NOTE]
> Zmiana domyślnej listy ACL w elementów nadrzędnych nie ma wpływu na dostęp do listy ACL lub domyślnej listy ACL elementów podrzędnych, które już istnieją.

### <a name="levels-of-permission"></a>Poziomy uprawnień

Uprawnienia do obiektu kontenera to **Odczyt,** **Zapis**i **Wykonywanie**i mogą być używane w plikach i katalogach, jak pokazano w poniższej tabeli:

|            |    Plik     |   Katalog |
|------------|-------------|----------|
| **Odczyt (R)** | Może odczytywać zawartości pliku | Wymaga **odczytu** i **wykonania,** aby wyświetlić listę zawartości katalogu |
| **Zapis (W)** | Może zapisywać w pliku lub dołączać do pliku | Wymaga **zapisu** i **wykonywania** do tworzenia elementów podrzędnych w katalogu |
| **Wykonanie (X)** | Nic nie znaczy w kontekście Data Lake Storage Gen2 | Wymagane do przechodzenia przez elementy podrzędne katalogu |

> [!NOTE]
> Jeśli udzielasz uprawnień przy użyciu tylko listy ACL (bez RBAC), a następnie udzielić podmiotu zabezpieczeń odczytu lub zapisu dostępu do pliku, należy nadać podmiotowi zabezpieczeń **Wykonywanie** uprawnień do kontenera i do każdego folderu w hierarchii folderów, które prowadzą do pliku.

#### <a name="short-forms-for-permissions"></a>Krótkie formy uprawnień

Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana postać liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienia. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka |      Co to oznacza     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | `R-X`        | Odczyt (R) + wykonanie (X)         |
| 4            | `R--`        | Odczyt                   |
| 0            | `---`        | Brak uprawnień         |

#### <a name="permissions-inheritance"></a>Dziedziczenie uprawnień

W modelu w stylu POSIX, który jest używany przez Program Data Lake Storage Gen2, uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych, jeśli uprawnienia są ustawiane po utworzeniu elementu podrzędnego. Uprawnienia są dziedziczone tylko wtedy, gdy domyślne uprawnienia zostały ustawione na element nadrzędny przed utworzeniem elementów podrzędnych.

### <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

W poniższej tabeli wymieniono niektóre typowe scenariusze ułatwiające zrozumienie, które uprawnienia są potrzebne do wykonywania niektórych operacji na koncie magazynu.

|    Operacja             |    /    | Oregon/ | Portland/ | Plik Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Przeczytaj plik Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Dołącz do pliku Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Usuń plik Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Utwórz plik Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Uprawnienia do zapisu w pliku nie są wymagane, aby go usunąć, tak długo, jak poprzednie dwa warunki są spełnione.

### <a name="users-and-identities"></a>Użytkownicy i tożsamości

Każdy plik i katalog ma różne uprawnienia dla tych tożsamości:

- Użytkownik będący właścicielem
- Grupa będąca właścicielem
- Użytkownicy nazwani
- Grupy nazwane
- Nazwane jednostki usługi
- Nazwane tożsamości zarządzane
- Wszyscy pozostali użytkownicy

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (Azure AD). Tak więc, o ile nie zaznaczono inaczej, *użytkownik*, w kontekście Usługi Data Lake Storage Gen2, może odwołać się do użytkownika usługi Azure AD, jednostki usługi, tożsamości zarządzanej lub grupy zabezpieczeń.

#### <a name="the-owning-user"></a>Użytkownik będący właścicielem

Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik posiadający *nie może* zmienić użytkownika posiadającego plik lub katalog. Tylko superużycie mogą zmieniać użytkownika posiadania pliku lub katalogu.

#### <a name="the-owning-group"></a>Grupa będąca właścicielem

W listach ACL POSIX każdy użytkownik jest skojarzony z *grupą podstawową*. Na przykład użytkownik "Alicja" może należeć do grupy "finanse". Alicja może również należeć do wielu grup, ale jedna grupa jest zawsze wyznaczona jako ich grupa podstawowa. W modelu POSIX, gdy Alicja tworzy plik, na grupę będącą właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”. Grupa będąca właścicielem w przeciwnym razie działa podobnie do przypisanych uprawnień dla innych użytkowników/grup.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Przypisywanie grupy stanowiącej nowy plik lub katalog

* **Przypadek 1:** Katalog główny "/". Ten katalog jest tworzony podczas tworzenia kontenera Data Lake Storage Gen2. W takim przypadku grupa właścicielem jest ustawiona na użytkownika, który utworzył kontener, jeśli zostało to zrobione przy użyciu OAuth. Jeśli kontener jest tworzony przy użyciu klucza udostępnionego, sygnatury dostępu Współdzielonego konta lub sygnatury dostępu Współdzielonego usługi, właściciel i grupa właścicielka są ustawione na **$superuser**.
* **Przypadek 2** (Co drugi przypadek): Po utworzeniu nowego elementu grupa będąca właścicielem jest kopiowana z katalogu nadrzędnego.

##### <a name="changing-the-owning-group"></a>Zmiana grupy właścicielki

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa właścicielka nie może zmienić list ACL pliku lub katalogu.  Podczas gdy grupa będąca właścicielem jest ustawiona na użytkownika, który utworzył konto w przypadku katalogu głównego, **sprawa 1** powyżej, jedno konto użytkownika nie jest prawidłowe do udzielania uprawnień za pośrednictwem grupy będącej właścicielem. Możesz przypisać te uprawnienia do prawidłowej grupy użytkowników, jeśli ma to zastosowanie.

### <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Poniższy pseudokod reprezentuje algorytm sprawdzania dostępu dla kont magazynu.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

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

#### <a name="the-mask"></a>Maska

Jak pokazano w algorytmie sprawdzania dostępu, maska ogranicza dostęp dla nazwanych użytkowników, grupy stanowiącej i nazwanych grup.  

> [!NOTE]
> W przypadku nowego kontenera Data Lake Storage Gen2 maska listy ACL dostępu do katalogu głównego ("/") domyślnie ma wartość 750 dla katalogów i 640 dla plików. Pliki nie otrzymują bitu X, ponieważ nie ma znaczenia dla plików w systemie tylko do sklepu.
>
> Maska może być określona na podstawie na wezwanie. Dzięki temu różne systemy zużywające, takie jak klastry, mają różne skuteczne maski dla ich operacji plików. Jeśli maska jest określona w danym żądaniu, całkowicie zastępuje maskę domyślną.

#### <a name="the-sticky-bit"></a>Atrybut sticky bit

Lepki bit jest bardziej zaawansowaną funkcją kontenera POSIX. W kontekście Data Lake Storage Gen2 jest mało prawdopodobne, że bit lepki będzie potrzebny. Podsumowując, jeśli bit przyklejony jest włączony w katalogu, element podrzędny może zostać usunięty lub zmieniony tylko przez użytkownika elementu podrzędnego.

Bit przyklejony nie jest wyświetlany w witrynie Azure portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Domyślne uprawnienia do nowych plików i katalogów

Podczas tworzenia nowego pliku lub katalogu w istniejącym katalogu domyślna listy ACL w katalogu nadrzędnym określa:

- Domyślna listy ACL katalogu podrzędnego i dostęp do listy ACL.
- ACL dostępu do pliku podrzędnego (pliki nie mają domyślnej listy ACL).

#### <a name="umask"></a>maska umask

Podczas tworzenia pliku lub katalogu maska umask służy do modyfikowania sposobu ustawiania domyślnych list ACL w elemencie podrzędnym. umask jest wartością 9-bitową w katalogach nadrzędnych, która zawiera wartość RWX dla **użytkownika,** **grupy posiadającej**i **innych**.

Maska umask dla usługi Azure Data Lake Storage Gen2 stała wartość, która jest ustawiona na 007. Wartość ta przekłada się na:

| składnik maski umask     | Forma liczbowa | Forma krótka | Znaczenie |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | W przypadku posiadania użytkownika skopiuj domyślnącl ACL rodzica do listy ACL dostępu dziecka | 
| umask.owning_group  |    0         |   `---`      | W przypadku grupy właścicielki skopiuj domyślną acl nadrzędnego do listy ACL dostępu dziecka | 
| umask.other         |    7         |   `RWX`      | W przypadku innych, usuń wszystkie uprawnienia do listy ACL dostępu dziecka |

Wartość maski używane przez usługę Azure Data Lake Storage Gen2 skutecznie oznacza, że wartość dla **innych** nigdy nie jest przesyłana domyślnie na nowe dzieci, niezależnie od tego, co wskazuje domyślna listy ACL. 

Poniższy pseudokod pokazuje, jak maska umask jest stosowana podczas tworzenia list ACL dla elementu podrzędnego.

```console
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Typowe pytania dotyczące list ACL w umiań magazynowania w ułowia danych

### <a name="do-i-have-to-enable-support-for-acls"></a>Czy muszę włączyć obsługę list ACL?

Nie. Kontrola dostępu za pośrednictwem list ACL jest włączona dla konta magazynu, o ile funkcja hierarchicznego obszaru nazw (HNS) jest włączona.

Jeśli usługa HNS jest wyłączona, nadal obowiązują reguły autoryzacji RBAC platformy Azure.

### <a name="what-is-the-best-way-to-apply-acls"></a>Jaki jest najlepszy sposób stosowania list ACL?

Zawsze używaj grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu w listach ACL. Oprzeć się możliwości bezpośredniego przypisania poszczególnych użytkowników lub jednostek usługi. Za pomocą tej struktury pozwoli na dodawanie i usuwanie użytkowników lub podmiotów usługi bez konieczności ponownego stosowania list ACL do całej struktury katalogu. ) Zamiast tego wystarczy dodać lub usunąć je z odpowiedniej grupy zabezpieczeń usługi Azure AD. Należy pamiętać, że listy ACL nie są dziedziczone, a więc ponowne nakładanie list ACL wymaga aktualizacji listy ACL w każdym pliku i podkatalogu. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Jakie uprawnienia są wymagane do cyklicznego usuwania katalogu i jego zawartości?

- Rozmówca ma uprawnienia "super-użytkownika",

Lub

- Katalog nadrzędny musi mieć uprawnienia Zapis + Wykonywanie.
- Katalog do usunięcia i każdy katalog w nim, wymaga odczytu + zapisu + wykonaj uprawnienia.

> [!NOTE]
> Nie potrzebujesz uprawnień do zapisu, aby usunąć pliki w katalogach. Ponadto nigdy nie można usunąć katalogu głównego "/".

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kto jest właścicielem pliku lub katalogu?

Twórca pliku lub katalogu staje się właścicielem. W przypadku katalogu głównego jest to tożsamość użytkownika, który utworzył kontener.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Która grupa jest ustawiona jako grupa właścicielka pliku lub katalogu podczas tworzenia?

Grupa właścicielka jest kopiowana z grupy właścicielów katalogu nadrzędnego, w ramach której tworzony jest nowy plik lub katalog.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jestem właścicielem użytkownika pliku, ale nie mam uprawnień RWX muszę. Co mam zrobić?

Użytkownik będący właścicielem może zmienić uprawnienia do pliku, aby przydzielić sobie wymagane uprawnienia RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Dlaczego czasami widzę identyfikatory GUID w listach ACL?

Identyfikator GUID jest wyświetlany, jeśli wpis reprezentuje użytkownika i że użytkownik nie istnieje już w usłudze Azure AD. Zwykle dzieje się tak, gdy użytkownik opuścił firmę lub jego konto w usłudze Azure AD zostało usunięte. Ponadto jednostki usługi i grupy zabezpieczeń nie mają nazwy głównej użytkownika (UPN) do ich identyfikowania, a więc są reprezentowane przez ich atrybut OID (guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Jak poprawnie ustawić listy ACL dla jednostki usługi?

Podczas definiowania list ACL dla podmiotów usługi, ważne jest, aby użyć identyfikatora obiektu (OID) *jednostki usługi* dla rejestracji aplikacji, który został utworzony. Należy pamiętać, że zarejestrowane aplikacje mają oddzielny podmiot usługi w określonej dzierżawie usługi Azure AD. Zarejestrowane aplikacje mają oid, który jest widoczny w witrynie Azure portal, ale *podmiot usługi* ma inny (inny) OID.

Aby uzyskać identyfikator OID dla jednostki usługi, która odpowiada `az ad sp show` rejestracji aplikacji, można użyć polecenia. Określ identyfikator aplikacji jako parametr. Oto przykład uzyskiwania identyfikatora OID dla jednostki usługi, który odpowiada rejestracji aplikacji o identyfikatorze aplikacji = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

Zostanie wyświetlony OID.

Jeśli masz poprawną jednostkę oid dla jednostki usługi, przejdź do Eksploratora magazynu **zarządzaj dostępem** strony, aby dodać OID i przypisać odpowiednie uprawnienia dla OID. Upewnij się, że wybrano **pozycję Zapisz**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Czy Data Lake Storage Gen2 obsługuje dziedziczenie list ACL?

Przypisania RBAC platformy Azure dziedziczą. Przydziały przepływają z zasobów kont subskrypcji, grupy zasobów zasobów zasobów magazynu do zasobu kontenera.

Listy ACL nie dziedziczą. Jednak domyślne listy ACL mogą być używane do ustawiania list ACL dla podrzędnych podkatalogów i plików utworzonych w katalogu nadrzędnym. 

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

* [Omówienie usługi Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
