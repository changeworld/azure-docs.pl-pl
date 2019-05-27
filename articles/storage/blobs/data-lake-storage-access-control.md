---
title: Omówienie kontroli dostępu w usłudze Azure Data Lake magazynu Gen2 | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu działania kontroli dostępu w usłudze Azure Data Lake magazynu Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 5ad7ef714147616fe55a9b978d501b974323e251
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949571"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Kontrola dostępu w usłudze Azure Data Lake magazynu Gen2

Azure Data Lake magazynu Gen2 implementuje model kontroli dostępu, który obsługuje zarówno kontroli dostępu opartej na rolach na platformie Azure (RBAC), jak i listy kontroli dostępu POSIX i podobne (kontroli dostępu ACL). Ten artykuł zawiera podsumowanie podstaw modelu kontroli dostępu dla Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Kontrola dostępu na podstawie ról

RBAC używają przypisań ról w celu efektywnego zastosowania zestawy uprawnień, aby *podmiotów zabezpieczeń*. A *podmiotu zabezpieczeń* jest obiektem, który reprezentuje użytkownika, grupy, jednostkę usługi lub tożsamość zarządzana, która jest zdefiniowana w usłudze Azure Active Directory (AD), który żąda dostępu do zasobów platformy Azure.

Zazwyczaj te zasoby platformy Azure są ograniczone do najwyższego poziomu zasobów (na przykład: Konta usługi Azure Storage). W przypadku usługi Azure Storage, a w związku z tym usługi Azure Data Lake Storage Gen2 ten mechanizm został rozszerzony do zasobów systemu plików.

Aby dowiedzieć się, jak przypisać role do podmiotów zabezpieczeń w zakresie konta usługi storage, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Wpływ przypisania roli na listach kontroli dostępu na poziomie plików i katalogów

Za pomocą przypisań ról RBAC jest zaawansowanym mechanizmem służącym do kontrolowania uprawnień dostępu, jest bardzo ziarnistości szczegółowe mechanizm względem listy kontroli dostępu. Najmniejsza stopień szczegółowości dla RBAC znajduje się na poziomie systemu plików i ocenia się to z wyższym priorytetem niż list ACL. W związku z tym czy możesz przypisać rolę do podmiotu zabezpieczeń w zakresie systemu plików tego podmiotu zabezpieczeń ma poziom autoryzacji skojarzonych z tą rolą, dla wszystkich plików i katalogów, w tym systemie plików, niezależnie od przypisań listy ACL.

Gdy podmiot zabezpieczeń jest uprawnień RBAC danych za pośrednictwem [wbudowana rola](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), lub za pośrednictwem rolę niestandardową, te uprawnienia są obliczane najpierw od autoryzacji żądania. Jeśli żądana operacja jest autoryzowany przez przypisania RBAC podmiotu zabezpieczeń, autoryzacji jest natychmiast rozwiązanych i Brak dodatkowych listy ACL są sprawdzane. Alternatywnie Jeśli podmiot zabezpieczeń nie ma przypisania RBAC lub żądania operacji jest niezgodna z przypisane uprawnienie, listy ACL testy są wykonywane w celu określenia, czy podmiot zabezpieczeń jest autoryzowany do wykonania żądanej operacji.

> [!NOTE]
> Jeśli podmiot zabezpieczeń został przypisany przypisanie wbudowanej roli właściciela danych obiektu Blob magazynu, a następnie podmiot zabezpieczeń jest uważany za *administrator* i jest przyznawany pełny dostęp do wszystkich operacji mutujące, włączając ustawienie właściciel katalogu lub pliku, a także listy ACL dla katalogów i plików, dla których nie są one właściciela. Administrator dostępu jest tylko autoryzowane sposób można zmienić właściciela zasobu.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Uwierzytelnianie klucza współużytkowanego i sygnatury dostępu współdzielonego (SAS)

Azure Data Lake magazynu Gen2 obsługuje klucza wspólnego i interfejsie SAS metody uwierzytelniania. Charakterystyka tych metod uwierzytelniania jest tożsamość nie jest skojarzony z funkcją wywołującą i dlatego nie można wykonać zabezpieczeń autoryzacji opartej na uprawnienia podmiotu zabezpieczeń.

W przypadku klucza wspólnego obiekt wywołujący dysków uzyskuje skutecznie dostęp "Administrator", co oznacza pełny dostęp do wszystkich operacji wykonywanych na wszystkich zasobów, w tym ustawianie właściciela i zmiana listy kontroli dostępu.

Tokeny sygnatur dostępu Współdzielonego obejmują dozwolone uprawnienia jako część tokenu. Uprawnienia uwzględnionym w tokenie sygnatury dostępu Współdzielonego skutecznie są stosowane do wszystkich decyzji dotyczących autoryzacji, ale nie dodatkowych listy ACL są sprawdzane.

## <a name="access-control-lists-on-files-and-directories"></a>Listy kontroli dostępu do plików i katalogów

Podmiot zabezpieczeń można skojarzyć z poziomu dostępu do plików i katalogów. Skojarzenia te są przechwytywane *listy kontroli dostępu (ACL)*. Poszczególnych plików i katalogów w ramach konta magazynu ma listy kontroli dostępu.

Jeśli rola jest przypisany do podmiotu zabezpieczeń na poziomie konta magazynu, można użyć listy kontroli dostępu przyznanie tego podmiotu zabezpieczeń z podwyższonym poziomem uprawnień dostępu do określonych plików i katalogów.

Nie można użyć listy kontroli dostępu w celu zapewnienia poziomu dostępu, który jest niższy niż poziom przyznane przez przypisanie roli. Na przykład, jeśli przypisujesz [Współautor danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) roli podmiot zabezpieczeń, nie można użyć kontroli dostępu list, aby uniemożliwić zapisywanie w katalogu tego podmiotu zabezpieczeń.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Zestaw plików i katalogów poziomu uprawnień przy użyciu list kontroli dostępu

Aby ustawić uprawnienia na poziomie plików i katalogów, zobacz jedną z następujących artykułów:

|Jeśli chcesz użyć tego narzędzia:    |Znajduje się w artykule:    |
|--------|-----------|
|Eksplorator usługi Azure Storage    |[Zestaw plików i katalogów poziomu uprawnień przy użyciu Eksploratora usługi Azure Storage za pomocą usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|Interfejs API REST    |[Ścieżka - Update](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Jeśli podmiot zabezpieczeń jest *usługi* jednostki, ważne jest, aby używać identyfikator obiektu nazwy głównej usługi i nie identyfikator obiektu rejestracji aplikacji. Aby uzyskać identyfikator obiektu jednostki usługi Otwieranie wiersza polecenia platformy Azure, a następnie użyj tego polecenia: `az ad sp show --id <Your App ID> --query objectId`. Upewnij się zastąpić `<Your App ID>` symbol zastępczy przy użyciu Identyfikatora aplikacji w Twojej rejestracji aplikacji.

### <a name="types-of-access-control-lists"></a>Typy list kontroli dostępu

Istnieją dwa typy list kontroli dostępu: *listy ACL dostępu* i *domyślne listy ACL*.

Listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

Domyślne listy ACL są szablony list ACL skojarzony z katalogiem, które określają listy ACL dostępu dla wszelkich elementów podrzędnych, które zostały utworzone w tym katalogu. Pliki nie mają domyślnych list ACL.

Zarówno listy ACL dostępu i domyślnej listy ACL mają tę samą strukturę.

Zarówno listy ACL dostępu i domyślnej listy ACL mają tę samą strukturę.

> [!NOTE]
> Zmienianie domyślnego listy ACL w nadrzędnej nie wpływa na dostęp do listy ACL lub domyślnych listę ACL elementów podrzędnych, które już istnieją.

### <a name="levels-of-permission"></a>Poziomy uprawnień

Uprawnienia do obiektu systemu plików są **odczytu**, **zapisu**, i **Execute**, i mogą być używane w pliki i katalogi, jak pokazano w poniższej tabeli:

|            |    Plik     |   Katalog |
|------------|-------------|----------|
| **Odczyt (R)** | Może odczytywać zawartości pliku | Wymaga **odczytu** i **Execute** wyświetlania zawartości katalogu |
| **Zapis (W)** | Może zapisywać w pliku lub dołączać do pliku | Wymaga **zapisu** i **Execute** utworzyć podrzędne elementy w katalogu |
| **Wykonanie (X)** | Nie oznacza niczego w kontekście Data Lake Storage Gen2 | Wymagane na przechodzenie przez elementy podrzędne w katalogu |

#### <a name="short-forms-for-permissions"></a>Krótkie formy uprawnień

Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana postać liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienia. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka |      Co to oznacza     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | `R-X`        | Odczyt (R) + wykonanie (X)         |
| 4            | `R--`        | Odczyt                   |
| 0            | `---`        | Brak uprawnień         |

#### <a name="permissions-inheritance"></a>Dziedziczenie uprawnień

W modelu stylu POSIX używanym przez Data Lake Storage Gen2 uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy uprawnienia dla elementu nie może być dziedziczona z elementów nadrzędnych, jeśli uprawnienia zostały ustawione po podrzędny element został już utworzony. Uprawnienia są dziedziczone tylko, jeśli domyślne uprawnienia zostały ustawione na elementów nadrzędnych przed elementy podrzędne zostały utworzone.

### <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

W poniższej tabeli wymieniono niektóre typowe scenariusze, które pomagają zrozumieć, jakie uprawnienia są wymagane do wykonania operacji na koncie magazynu.

|    Operacja             |    /    | Oregon / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Przeczytaj Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Dołącz do Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Usuń Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Utwórz Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Zapisu uprawnienia do pliku, nie są wymagane do usunięcia, tak długo, jak dwa poprzednie warunki są spełnione.

### <a name="users-and-identities"></a>Użytkownicy i tożsamości

Każdy plik i katalog ma różne uprawnienia do tych tożsamości:

- Użytkownik będący właścicielem
- Grupa będąca właścicielem
- Użytkownicy nazwani
- Grupy nazwane
- Jednostki usługi o nazwie
- Nazwane zarządzanych tożsamości.
- Wszyscy pozostali użytkownicy

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (Azure AD). Tak, chyba że określono inaczej, *użytkownika*, w kontekście usługi Data Lake Storage Gen2, można odwoływać się do użytkownika usługi Azure AD, usługi jednostki, zarządzanych tożsamości lub grupy zabezpieczeń.

#### <a name="the-owning-user"></a>Użytkownik będący właścicielem

Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik będący właścicielem *nie* zmienić użytkownika będącego właścicielem pliku lub katalogu. Tylko administrator może zmienić użytkownik będący właścicielem pliku lub katalogu.

#### <a name="the-owning-group"></a>Grupa będąca właścicielem

Na listach ACL modelu POSIX, każdy użytkownik jest skojarzony z *grupa podstawowa*. Na przykład użytkownik "Alicja" może należeć do grupy "Finanse". Alicja może również należeć do wielu grup, ale jedna grupa jest zawsze wyznaczona jako swojej grupy podstawowej. W modelu POSIX, gdy Alicja tworzy plik, na grupę będącą właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”. Grupa będąca właścicielem w przeciwnym razie działa podobnie do przypisanych uprawnień dla innych użytkowników/grup.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Przypisywanie grupa będąca właścicielem dla nowego pliku lub katalogu

* **Przypadek 1**: Katalog główny "/". Ten katalog jest tworzony po utworzeniu systemu plików Data Lake Storage Gen2. W takim przypadku grupa będąca właścicielem jest ustawiona dla użytkownika, która utworzyła systemu plików, jeśli zostało to zrobione, przy użyciu protokołu OAuth. Jeśli system plików jest tworzony przy użyciu klucza wspólnego, sygnatury dostępu Współdzielonego konta lub sygnatury dostępu Współdzielonego usługi, a następnie właścicielem i grupy będącej właścicielem są ustawione na **$superuser**.
* **Przypadek 2** (każdy inny przypadek): Gdy zostanie utworzony nowy element, grupa będąca właścicielem jest kopiowany z katalogu nadrzędnego.

##### <a name="changing-the-owning-group"></a>Zmiana grupy będącej właścicielem

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa będąca właścicielem nie można zmienić list kontroli dostępu pliku lub katalogu.  Gdy grupa będąca właścicielem jest ustawiony na użytkownika, który utworzył konto w przypadku katalogu głównego **przypadek 1** powyżej jednego konta użytkownika nie jest prawidłowa dla zapewnić uprawnień za pośrednictwem grupy będącej właścicielem. Możesz przypisać te uprawnienia do prawidłowej grupy użytkowników, jeśli ma to zastosowanie.

### <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Poniższym pseudokodzie przedstawia algorytm kontroli dostępu dla kont magazynu.

```
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

Jak pokazano w algorytmu kontroli dostępu, maski ogranicza dostęp do nazwani użytkownicy, grupy będącej właścicielem i nazwanej grupy.  

> [!NOTE]
> Nowy system plików Data Lake Storage Gen2 maski dla listy ACL katalogu głównego ("/") dostęp domyślnie 750 dla katalogów i 640 dla plików. Pliki nie mają bitowy X, ponieważ jest nieodpowiednie do plików w systemie tylko do magazynu.
>
> Maska mogą być określone dla poszczególnych wywołań. Dzięki temu inne systemy odbierająca komunikaty, takich jak klastry mają różne maski obowiązujące dla ich operacji na plikach. Jeśli maska jest określony dla danego żądania, całkowicie zastępuje domyślną maskę.

#### <a name="the-sticky-bit"></a>Atrybut sticky bit

Atrybut sticky bit jest bardziej zaawansowaną funkcją systemu plików POSIX. W kontekście usługi Data Lake Storage Gen2 jest mało prawdopodobne, że działania atrybutu sticky bit będzie potrzebny. Podsumowując Jeśli atrybut sticky bit jest włączona w katalogu, element podrzędny tylko można usunąć lub zmieniona przez użytkownika będącego właścicielem elementu podrzędnego.

Atrybut sticky bit nie jest wyświetlany w witrynie Azure portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Uprawnienia domyślne dla nowych plików i katalogów

Gdy nowy plik lub katalog jest tworzony w istniejącym katalogiem, domyślne listy ACL w katalogu nadrzędnym określa:

- Katalogu podrzędnego domyślnej listy ACL i listę ACL dostępu.
- Plik podrzędnego dostępu ACL (pliki nie mają domyślnej listy ACL).

#### <a name="umask"></a>maska umask

Podczas tworzenia pliku lub katalogu, maska umask jest używana do modyfikowania konfiguracji domyślnej listy ACL dla elementu podrzędnego. Maska umask jest 9-bitową wartością w katalogi nadrzędne, które zawiera wartość RWX **użytkownika będącego właścicielem**, **grupy będącej właścicielem**, i **innych**.

Maska umask for Azure Data Lake Storage Gen2 stałą wartość, która jest równa 007. This value translates to:

| Maska umask składnika     | Forma liczbowa | Forma krótka | Znaczenie |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Dla użytkownika będącego właścicielem, skopiuj domyślnej nadrzędnej listy ACL do dostępu elementu podrzędnego listy ACL | 
| umask.owning_group  |    0         |   `---`      | Dla grupy będącej właścicielem, skopiuj domyślnej nadrzędnej listy ACL do dostępu elementu podrzędnego listy ACL | 
| umask.Other         |    7         |   `RWX`      | Dla pozostałych Usuń wszystkie uprawnienia dostępu elementu podrzędnego listy ACL |

Wartość maski umask używane przez usługi Azure Data Lake Storage Gen2 skutecznie oznacza, że wartość **innych** nigdy nie są przesyłane domyślnie na nowych elementów podrzędnych, niezależnie od tego, jakie domyślne listy ACL wskazuje. 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Często zadawane pytania dotyczące list ACL w Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Czy muszę włączyć obsługę list ACL?

Nie. Kontrola dostępu za pośrednictwem list ACL jest włączona dla konta magazynu, tak długo, jak hierarchiczne Namespace (SNS) funkcja jest włączone.

Jeśli SNS jest wyłączony, reguły autoryzacji RBAC platformy Azure nadal mają zastosowanie.

### <a name="what-is-the-best-way-to-apply-acls"></a>Co to jest najlepszym sposobem na stosowanie list ACL?

Zawsze należy używać grup zabezpieczeń usługi Azure AD jako przypisanej jednostki na listach ACL w. Siłowym możliwość bezpośrednio przypisać poszczególnych użytkowników lub jednostki usługi. Za pomocą tej struktury pozwala dodawać i usuwać użytkowników lub jednostek usług bez konieczności ponownego zastosowania list ACL do całej struktury katalogów. ) Po prostu zamiast tego należy dodać lub usunąć je z odpowiedniej grupy zabezpieczeń usługi Azure AD. Należy pamiętać, że list ACL nie są dziedziczone, a więc w celu ponownego zastosowania list ACL wymaga zaktualizowania listy ACL dla każdego pliku i podkatalogu. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Jakie uprawnienia są wymagane do rekursywnego usunięcia katalogu i jego zawartość?

- Obiekt wywołujący ma uprawnienia "Administrator"

Lub

- Katalog nadrzędny musi mieć zapisu + uprawnienia do wykonywania.
- Katalog do usunięcia i każdy katalog znajdujący się w nim, wymaga odczytu, zapisu i uprawnienia do wykonywania.

> [!NOTE]
> Użytkownik nie potrzebujesz uprawnienia do zapisu do usuwania plików w katalogach. Ponadto katalog główny "/" nigdy nie może zostać usunięty.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kto jest właścicielem pliku lub katalogu?

Twórca pliku lub katalogu staje się właścicielem. W przypadku katalogu głównego jest tożsamość użytkownika, który utworzył systemu plików.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Grupę, do której jest ustawiona jako grupa będąca właścicielem pliku lub katalogu w momencie jego tworzenia?

Grupa będąca właścicielem jest kopiowana z grupy będącej właścicielem katalogu nadrzędnego, w którym tworzony jest nowy plik lub katalog.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jestem właścicielem pliku, ale nie mam wymaganych uprawnień RWX. Co mam zrobić?

Użytkownik będący właścicielem może zmienić uprawnienia do pliku, aby przydzielić sobie wymagane uprawnienia RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Dlaczego czasami wyświetlane identyfikatory GUID list ACL?

Identyfikator GUID jest wyświetlany, jeśli wpis reprezentuje użytkownika, a użytkownik nie istnieje w usłudze Azure AD już. Zwykle dzieje się tak, gdy użytkownik opuścił firmę lub jego konto w usłudze Azure AD zostało usunięte. Ponadto jednostki usługi i grupy zabezpieczeń nie mają główną nazwę użytkownika (UPN) umożliwiający ich zidentyfikowanie i dlatego są reprezentowane przez ich atrybut identyfikatora OID (guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Jak ustawić list ACL poprawnie usługi głównej?

Podczas definiowania listy ACL dla nazw głównych usług, ważne jest, aby użyć Identyfikatora obiektu (OID) z *nazwy głównej usługi* rejestracji aplikacji, który został utworzony. Ważne jest, aby należy zauważyć, że zarejestrowanych aplikacji nazwy głównej usługi oddzielne w konkretnym dzierżawy usługi Azure AD. Zarejestrowane aplikacje mają identyfikatora OID, który jest widoczny w witrynie Azure portal, ale *nazwy głównej usługi* ma inny identyfikator OID w (inny).

Aby uzyskać identyfikator OID dla jednostki usługi, który odnosi się do rejestracji aplikacji, można użyć `az ad sp show` polecenia. Określ identyfikator aplikacji jako parametr. Oto przykład umożliwiające uzyskanie OID dla jednostki usługi, umożliwiająca rejestrowanie aplikacji przy użyciu Identyfikatora aplikacji = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Jeśli masz prawidłowy identyfikator OID dla jednostki usługi, przejdź do Eksploratora usługi Storage **zarządzanie dostępem** strony, aby dodać identyfikator OID i przypisać odpowiednie uprawnienia, aby uzyskać identyfikator OID. Upewnij się, możesz wybrać **Zapisz**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Usługa Data Lake Storage Gen2 obsługuje dziedziczenie list ACL?

Dziedziczenie przypisania RBAC platformy Azure. Przydziały przepływać z subskrypcji, grupy zasobów i zasobów konta magazynu w dół do zasobów systemu plików.

List ACL nie dziedziczą. Jednak domyślne listy ACL może służyć do ustawienia list ACL dla podrzędnych podkatalogi i pliki tworzone w katalogu nadrzędnym. 

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

* [Omówienie usługi Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
