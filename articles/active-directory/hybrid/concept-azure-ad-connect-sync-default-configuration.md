---
title: 'Synchronizacja programu Azure AD Connect: Opis konfiguracji domyślnej | Dokumentacja firmy Microsoft'
description: W tym artykule opisano domyślna konfiguracja przedstawiona w synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42a6b667a8708aeb2edeb0c80a5ab747b6c60a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246168"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: Opis konfiguracji domyślnej
W tym artykule wyjaśniono reguły konfiguracji out-of-box. Dokumentują reguł i wpływie na te reguły konfiguracji. On również przeprowadzi Cię przez domyślną konfigurację synchronizacji programu Azure AD Connect. Celem jest, że czytnik rozumie, jak działa model konfiguracji o nazwie aprowizacja deklaratywna w przykładzie rzeczywistych. W tym artykule założono, że masz już zainstalowany i Konfigurowanie synchronizacji usługi Azure AD Connect przy użyciu Kreatora instalacji.

Aby poznać szczegóły model konfiguracji, przeczytaj [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Reguły Out-of-box ze środowiska lokalnego do usługi Azure AD
W konfiguracji out-of-box można znaleźć następujących wyrażeń.

### <a name="user-out-of-box-rules"></a>Reguły out-of-box użytkownika
Te reguły dotyczą również iNetOrgPerson typu obiektu.

Obiekt użytkownika musi spełniać następujące polecenie, aby być synchronizowane:

* Musi mieć sourceAnchor.
* Po utworzeniu obiektu w usłudze Azure AD, nie można zmienić sourceAnchor. Jeśli wartość jest zmienione w środowisku lokalnym, obiekt zatrzymuje, synchronizowanie, dopóki nie zostanie zmieniony sourceAnchor, wróć do poprzedniej wartości.
* Musi mieć atrybut accountEnabled (userAccountControl) wypełnione. Za pomocą lokalnej usługi Active Directory ten atrybut jest zawsze obecne i wypełnione.

Następujące obiekty użytkownika są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, wiele obiektów out-of-box w usłudze Active Directory, takie jak wbudowane konto administratora, nie są zsynchronizowane.
* `IsPresent([sAMAccountName]) = False`. Upewnij się, że obiekty użytkowników o nie atrybutu sAMAccountName nie są zsynchronizowane. Ten przypadek sytuacja tylko praktycznie może mieć miejsce w domenie uaktualnienia z NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nie Synchronizuj konto usługi używane przez program Azure AD Connect sync, jak i jego wcześniejszych wersji.
* Nie Synchronizuj kont serwera Exchange, które nie będzie działać w programie Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nie są synchronizowane obiekty, które nie będzie działać w programie Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ta maska bitów (& H21C07000) filtruje się następujące obiekty:
  * (W wersji zapoznawczej począwszy od wersji w 1.1.524.0) folderu publicznego wykorzystującego pocztę
  * System użytkownik skrzynki pocztowej
  * Skrzynka pocztowa bazy danych skrzynki pocztowej (Skrzynka pocztowa System)
  * Uniwersalna grupa zabezpieczeń (nie ma zastosowania dla użytkownika, ale jest obecny dla starszych powodów)
  * Grupę uniwersalną inne niż (w tym celu należy w takich sytuacjach przydałaby się dla użytkownika, ale jest obecny dla starszych powodów)
  * Plan skrzynki pocztowej
  * Odnajdywanie skrzynki pocztowej
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchronizuje wszelkie obiekty ofiarą replikacji.

Obowiązują następujące reguły atrybutu:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atrybut sourceAnchor nie przyczyniły się z połączoną skrzynkę pocztową. Zakłada się, że jeśli połączona Skrzynka pocztowa została znaleziona, konta rzeczywistego sprzężony później.
* Exchange dotyczące atrybutów są synchronizowane tylko, jeśli atrybut **mailNickName** ma wartość.
* W przypadku wielu lasów atrybuty są używane w następującej kolejności:
  1. Atrybuty powiązane z logowania (np. userPrincipalName) są tworzone z lasu za pomocą włączone konto.
  2. Atrybuty, które znajdują się w wymiany usługi GAL (globalna lista adresów) są tworzone z lasu za pomocą skrzynki pocztowej programu Exchange.
  3. Jeśli można znaleźć nie skrzynki pocztowej, te atrybuty mogą pochodzić z dowolnym lesie.
  4. Program Exchange dotyczące atrybutów (Technical Preview atrybuty nie są widoczne w globalnej) są tworzone z lasu gdzie `mailNickname ISNOTNULL`.
  5. W przypadku wielu lasów, które będą spełniać jeden z tych reguł następnie kolejność tworzenia (Data/godzina), łączników (lasów) służy do określania las, który przyczynia się atrybutów.

### <a name="contact-out-of-box-rules"></a>Skontaktuj się z reguł out-of-box
Skontaktuj się z obiektu muszą spełniać następujące polecenie, aby być synchronizowane:

* Kontakt musi być z włączoną obsługą poczty. Jest on weryfikowany za pomocą następujących reguł:
  * `IsPresent([proxyAddresses]) = True)`. Należy określić atrybut proxyAddresses.
  * Podstawowy adres e-mail można znaleźć w atrybucie proxyAddresses lub atrybut poczty. Obecność \@ służy do Sprawdź, czy zawartość jest adres e-mail. Jedną z tych dwóch reguł musi przyjąć wartość PRAWDA.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Czy istnieje wpis z "SMTP:", a jeśli, mogą \@ można znaleźć w ciągu?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Jest wypełniona atrybut poczty, a jeśli tak jest, można \@ można znaleźć w ciągu?

Skontaktuj się z pomocą następujące obiekty są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, żadne obiekty skontaktuj się z pomocą oznaczone jako krytyczne są synchronizowane. Nie powinien być dowolny z domyślną konfiguracją.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Te obiekty nie działa w programie Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchronizuje wszelkie obiekty ofiarą replikacji.

### <a name="group-out-of-box-rules"></a>Grupa reguł out-of-box
Obiekt grupy, musi spełniać następujące polecenie, aby być synchronizowane:

* Musi mieć mniej niż 50 000 członków. Ta liczba jest liczba członków w grupie w środowisku lokalnym.
  * Jeśli ma on więcej elementów członkowskich, zanim synchronizacji rozpocznie się po raz pierwszy, grupy nie są zsynchronizowane.
  * Liczba elementów członkowskich rozwój od kiedy został utworzony, następnie po dotarciu serwerów do 50 000 członków zatrzymuje, synchronizowanie, dopóki liczba członków jest mniejszy niż 50 000 ponownie.
  * Uwaga: Liczba 50 000 członków również są wymuszane przez usługę Azure AD. Nie jest możliwe zsynchronizować grup z więcej elementów członkowskich, nawet jeśli zmodyfikować lub usunąć tę regułę.
* Jeśli grupa jest **grupy dystrybucyjnej**, a następnie komputer musi także mieć włączoną obsługę poczty. Zobacz [skontaktuj się z reguł out-of-box](#contact-out-of-box-rules) dla tej reguły jest wymuszany.

Następujące obiekty grupy są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, wiele obiektów out-of-box w usłudze Active Directory, takie jak wbudowanej grupy Administratorzy, nie są zsynchronizowane.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupa starszej wersji, używane przez narzędzie DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupy ról.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchronizuje wszelkie obiekty ofiarą replikacji.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Reguły out-of-box ForeignSecurityPrincipal
FSP są przyłączone do "dowolne" (\*) obiektów w magazynie metaverse. W rzeczywistości tego przyłączenia odbywa się tylko dla użytkowników i grup zabezpieczeń. Ta konfiguracja gwarantuje, że między lasami w grupach są rozwiązane i poprawnie reprezentowana w usłudze Azure AD.

### <a name="computer-out-of-box-rules"></a>Reguły out-of-box komputera
Obiekt komputera musi spełniać następujące polecenie, aby być synchronizowane:

* `userCertificate ISNOTNULL`. Tylko komputery z systemem Windows 10 wypełnienie tego atrybutu. Wszystkie obiekty komputerów z wartością w tym atrybucie są synchronizowane.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Omówienie scenariusza reguły out-of-box
W tym przykładzie użyto wdrożenie z jednym lesie konta (A), jednego lasu zasobów (R) i jednego katalogu usługi Azure AD.

![Obraz z opisem scenariusza](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

W tej konfiguracji zakłada się, że włączone konto w lesie konta i wyłączonych kont w lesie zasobów z połączoną skrzynkę pocztową.

Naszym celem przy użyciu domyślnej konfiguracji jest:

* Atrybuty powiązane z logowania są synchronizowane z lasu za pomocą włączone konto.
* Atrybuty, które znajdują się w globalnej (globalna lista adresów) są synchronizowane z lasu za pomocą skrzynki pocztowej. Jeśli można znaleźć nie skrzynki pocztowej, używana jest dowolnym innym lesie.
* Jeśli zostanie znaleziony połączoną skrzynkę pocztową, połączone włączone konto musi zostać znaleziony dla obiektu, które mają zostać wyeksportowane do usługi Azure AD.

### <a name="synchronization-rule-editor"></a>Edytor reguł synchronizacji
Konfigurację można wyświetlać i zmieniać przy użyciu narzędzia Edytor reguł synchronizacji (SRE) i skrót do niego znajduje się w start menu.

![Ikona Edytor reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE jest narzędziem resource kit, który jest zainstalowany za pomocą synchronizacji programu Azure AD Connect. Aby można było go uruchomić, musi być członkiem grupy ADSyncAdmins. Po uruchomieniu, zostanie wyświetlony podobny do poniższego:

![Reguły synchronizacji ruchu przychodzącego](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

W tym okienku zobaczysz wszystkie reguły synchronizacji utworzone dla danej konfiguracji. Każdy wiersz w tabeli jest jedna reguła synchronizacji. Po lewej stronie w obszarze typów reguł zostaną wyświetlone dwa różne typy: Dla ruchu przychodzącego i wychodzącego. Przychodzące i wychodzące są z widoku obiektu metaverse. Przede wszystkim ma skoncentrować się na reguły dla ruchu przychodzącego w tym omówieniu. Właściwą listę reguł synchronizacji jest zależna od schematu wykryte w AD. Na powyższym rysunku las kont (fabrikamonline.com) nie ma żadnych usług, takich jak Exchange i usługi Lync, a żadne reguły synchronizacji zostały utworzone dla tych usług. Jednak w lesie zasobów (res.fabrikamonline.com) można znaleźć reguły synchronizacji dla tych usług. Zawartość reguły różni się zależnie od wersji wykryte. Na przykład w przypadku wdrożenia przy użyciu programu Exchange 2013 istnieją więcej przepływów atrybutów skonfigurowanych niż w 2007-Exchange 2010.

### <a name="synchronization-rule"></a>Reguła synchronizacji
Reguła synchronizacji jest obiekt konfiguracji z zestawem atrybutów przepływu, gdy zostanie spełniony jakiś warunek. Również służy do opisywania, jak obiektu w przestrzeni łącznika jest powiązany z obiektem w magazynie metaverse, znane jako **sprzężenia** lub **dopasowania**. Reguły synchronizacji mają pierwszeństwo przed wskazujące, jak powiązane są ze sobą. Reguła synchronizacji z niższą wartość liczbową ma wyższy priorytet i wystąpi konflikt przepływu atrybutu wyższy priorytet wins rozwiązywania konfliktów.

Na przykład Przyjrzyj się reguły synchronizacji **w z usługi AD — AccountEnabled użytkownika**. Oznacz ten wiersz w SRE, a następnie wybierz pozycję **Edytuj**.

Ponieważ ta reguła jest regułą out-of-box, zostanie wyświetlone ostrzeżenie, po otwarciu reguły. Nie należy podejmować żadnego [zmian reguł out-of-box](how-to-connect-sync-best-practices-changing-default-configuration.md), więc zostanie wyświetlony monit, jakie są Twoje intencji. W takim przypadku tylko chcesz wyświetlić reguły. Wybierz **nie**.

![Synchronizacja ostrzeżenia reguły](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Reguła synchronizacji ma cztery sekcje konfiguracji: Opis filtru Scoping, reguły dołączania i przekształcenia.

#### <a name="description"></a>Opis
Pierwsza sekcja zawiera podstawowe informacje, takie jak nazwa i opis.

![Opis karty Edytor reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Można również znaleźć informacje o który połączonego systemu dotyczy tej reguły, które obiekt typu w połączony system, w których on obowiązuje i typ obiektu metaverse. Typ obiektu metaverse zawsze jest osoba, niezależnie od tego, po użytkownik, iNetOrgPerson lub skontaktuj się z pomocą typ obiektu źródłowego. Typ obiektu metaverse nigdy nie należy zmieniać, dlatego jest tworzony jako typ ogólny. Join, StickyJoin lub Aprowizacja można ustawić typu łącza. To ustawienie działa razem z sekcji Dołącz do reguły i jest podawany później.

Widać również, że ta reguła synchronizacji jest używany do synchronizacji haseł. Jeśli użytkownik znajduje się w zakresie dla tej reguły synchronizacji, hasło jest zsynchronizowany ze środowiska lokalnego do chmury (przy założeniu, że włączono funkcji synchronizacji haseł).

#### <a name="scoping-filter"></a>Filtru określania zakresu
Sekcja filtru określania zakresu jest używana do konfigurowania podczas stosowania reguły synchronizacji. Ponieważ nazwy reguły synchronizacji przeglądasz wskazuje, powinny być stosowane tylko do użytkowników z włączoną, zakres jest skonfigurowany tak atrybutu usługi AD **userAccountControl** nie może mieć bitu 2 ustawione. Aparat synchronizacji znajduje użytkownika w AD, zastosowanie tej synchronizacji regułę, gdy **userAccountControl** jest ustawiona na wartość dziesiętną 512 (włączone zwykłego użytkownika). Nie dotyczy reguła, gdy użytkownik ma **userAccountControl** równa 514 (wyłączone zwykłego użytkownika).

![Wyznaczanie zakresu karta w edytorze reguły synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Filtru określania zakresu ma grup i klauzule, które mogą być zagnieżdżone. Wszystkie klauzule wewnątrz grupy, muszą być spełnione dla reguły synchronizacji do zastosowania. Po zdefiniowaniu wiele grup co najmniej jednej grupy, muszą być spełnione dla reguły do zastosowania. Oznacza to logiczne OR jest oceniany między grupami i wartość logiczna, a jest szacowana wewnątrz grupy. Przykład tej konfiguracji można znaleźć w reguły synchronizacji ruchu wychodzącego **Out do usługi AAD — Dołącz do grupy**. Istnieje kilka grup filtr synchronizacji, na przykład jednego dla grup zabezpieczeń (`securityEnabled EQUAL True`) i jeden dla grup dystrybucji (`securityEnabled EQUAL False`).

![Wyznaczanie zakresu karta w edytorze reguły synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Ta reguła jest używana do definiowania, które grupy powinny zostać aprowizowane do usługi Azure AD. Grupy dystrybucji musi mieć włączoną obsługę mają być synchronizowane z usługą Azure AD poczty, ale dla grup zabezpieczeń wiadomość e-mail nie jest wymagana.

#### <a name="join-rules"></a>Dołącz do reguły
Trzecia sekcja służy do konfigurowania, jak obiekty w przestrzeni łącznika odnoszą się do obiektów w obiekcie metaverse. Reguła sprawdzono wcześniej nie ma żadnej konfiguracji dla Dołącz do reguł, zamiast przechodzenia do wzięcia pod **w z usługi AD — użytkownik przyłączyć**.

![Dołącz kartę reguły Edytor reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Zawartość reguły dołączania zależy od opcji dopasowania wybrane w Kreatorze instalacji. Reguła ruchu przychodzącego ocena zaczyna się od obiektu w przestrzeni łącznika źródła i każdej grupy reguł dołączania jest obliczane w kolejności. Jeśli obiekt źródłowy jest oceniany w celu dopasowania dokładnie jeden obiekt w magazynie metaverse przy użyciu jednej reguły dołączania, obiekty są łączone. Jeśli wszystkie reguły zostały ocenione i nie zostanie odnaleziony odpowiednik, jest używany typ łącza na stronie z opisem. Jeśli ustawiono tę konfigurację **aprowizacji**, a następnie jest tworzony nowy obiekt w obiekcie metaverse w lokalizacji docelowej. Aprowizację, nazywane również jest nowy obiekt do obiektu metaverse **projektu** obiektu metaverse.

Reguły dołączania są jedynie oceniane jeden raz. Obiektu przestrzeni łącznika i obiektu metaverse są łączone, pozostają połączone tak długo, jak zakresu reguły synchronizacji jest nadal spełnione.

Podczas oceny reguły synchronizacji, tylko jedna reguła synchronizacji z zdefiniowanych reguł dołączania musi być w zakresie. Jeśli wiele reguł synchronizacji przy użyciu reguł dołączania znajdują się w jeden obiekt, jest zgłaszany błąd. Z tego powodu najlepszym rozwiązaniem jest tylko jedna reguła synchronizacji z sprzężenia definiuje wiele reguł synchronizacji znajdują się w zakresie dla obiektu. W konfiguracji out-of-box do synchronizacji Azure AD Connect, te reguły można można znaleźć, sprawdzając nazwę i Znajdź te wyrazami **Dołącz** na końcu nazwy. Reguły synchronizacji, bez żadnych reguł dołączania zdefiniowane dotyczy przepływy atrybutów innych reguł synchronizacji połączone obiekty lub aprowizowane nowy obiekt w elemencie docelowym.

Spojrzeć na powyższej ilustracji widać, że reguła próbuje dołączyć **objectSID** z **msExchMasterAccountSid** (Exchange) i **msRTCSIP-OriginatorSid** (Lync) jest to, jakiego się spodziewaliśmy w topologii lasu zasobów dla konta. Możesz znaleźć tę samą regułę na wszystkich lasów. Zakłada się, że w każdym lesie, może być lasu kont lub zasobów. Ta konfiguracja działa także w przypadku kont, które znajdują się w jednym lesie i nie musi być przyłączony.

#### <a name="transformations"></a>Przekształcenia
W sekcji transformacji zdefiniowano wszystkie przepływy atrybutów, które mają zastosowanie do obiektu docelowego, gdy obiekty są łączone i Filtr zakresu jest spełniony. Po powrocie do **w z usługi AD — AccountEnabled użytkownika** reguły synchronizacji, Znajdź następujące przekształcenia:

![Przekształcenia kartę Edytor reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Aby przełączyć tę konfigurację w kontekście we wdrożeniu lasu zasobów dla konta, oczekuje się znaleźć włączone konto w lesie konta i wyłączonych kont w lesie zasobów przy użyciu ustawień programu Exchange i usługi Lync. Reguła synchronizacji przeglądasz zawiera atrybuty wymagane do logowania i przepływu te atrybuty z lasu w przypadku, gdy jest włączone konto. Te przepływy atrybutów zostały zbudowane w ramach jednej reguły synchronizacji.

Przekształcenie mogą mieć różne typy: Stała, bezpośrednie i wyrażenia.

* Przepływ są przekazywane zawsze wartości zapisane na stałe. W przypadku powyższego zawsze ustawia wartości **True** atrybut metaverse o nazwie **accountEnabled**.
* Bezpośrednie przepływu zawsze przepływy wartość atrybutu w źródle w atrybucie docelowym jako-to.
* Trzeci typ przepływu jest wyrażeniem, i umożliwia bardziej zaawansowanych ustawień konfiguracji.

Język wyrażeń jest VBA (Visual Basic for Applications), więc osoby z doświadczeniem pakietu Microsoft Office lub VBScript rozpoznają format. Atrybuty są ujęte w nawiasy kwadratowe [attributeName]. Atrybut nazwy i nazwy funkcji jest rozróżniana wielkość liter, ale narzędzia Synchronization Rules Editor ocenia wyrażenia i odpowiedniego ostrzeżenia, jeśli wyrażenie jest nieprawidłowe. Wszystkie wyrażenia są wyrażane w pojedynczym wierszu za pomocą funkcji zagnieżdżonych. Aby wyświetlić możliwości języka konfiguracji, Oto przepływ dla pwdLastSet, ale z komentarzami dodatkowy, wstawione:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Zobacz [interpretacji wyrażenia inicjowania obsługi administracyjnej deklaratywne](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) więcej informacji na temat języka wyrażeń dla przepływów atrybutów.

### <a name="precedence"></a>Pierwszeństwo
Teraz sprawdzono, niektóre poszczególne reguły synchronizacji, ale zasady współpracują ze sobą w konfiguracji. W niektórych przypadkach wartość atrybutu jest przyczynił się z wielu reguł synchronizacji do tego samego atrybutu docelowego. W takim przypadku pierwszeństwo atrybutów jest używany do określenia, który atrybut wins. Na przykład Przyjrzyj się atrybut sourceAnchor. Ten atrybut jest atrybutem ważne, aby móc zalogować się do usługi Azure AD. Dla tego atrybutu w dwie różne reguły synchronizacji, można znaleźć przepływ atrybutu **w z usługi AD — AccountEnabled użytkownika** i **w z usługi AD — typowe użytkownika**. Ze względu na pierwszeństwo reguły synchronizacji atrybut sourceAnchor jest przyczynił się z lasu za pomocą włączone konto najpierw w przypadku wielu obiektów dołączony do obiektu metaverse. Jeśli nie ma żadnych włączonych kont, a następnie korzysta z aparatu synchronizacji reguły synchronizacji wychwytywania **w z usługi AD — typowe użytkownika**. Ta konfiguracja gwarantuje, że nawet w przypadku konta, które są wyłączone, jest nadal sourceAnchor.

![Reguły synchronizacji ruchu przychodzącego](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Pierwszeństwo reguły synchronizacji jest ustawiona w grupach przez Kreatora instalacji. Wszystkie reguły w grupie mają taką samą nazwę, ale są one połączone z różnymi katalogami połączonych. Kreator instalacji umożliwia reguły **w z usługi AD — użytkownik przyłączyć** najwyższy priorytet i go wykonuje iterację przez wszystkie połączone katalogów usługi AD. Następnie kontynuuje z grupami następnym reguły w kolejności wstępnie zdefiniowane. Wewnątrz grupy zasady są dodawane w kolejności, w której zostały dodane łączniki, w kreatorze. Jeśli inny łącznik został dodany za pomocą kreatora, reguły synchronizacji zostaną ponownie uporządkowane i nowy łącznik reguły są wstawiane ostatnia w każdej grupie.

### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Teraz wiemy wystarczająco o reguły synchronizacji, aby móc zrozumieć, jak działa Konfiguracja przy użyciu innej reguły synchronizacji. Przyjrzyj się użytkownika i atrybutów, które są przyczyniły się do środowiska metaverse, zasady są stosowane w następującej kolejności:

| Name (Nazwa) | Komentarz |
|:--- |:--- |
| W z usługi AD — sprzężenia użytkownika |Reguła dołączania łącznika przestrzeni obiektów z obiektu metaverse. |
| W z usługi AD — UserAccount włączone |Atrybuty wymagane do logowania do usługi Azure AD i Office 365. Chcemy, aby te atrybuty z włączone konto. |
| W z usługi AD — typowe użytkownika z programu Exchange |Znaleziono na globalnej liście adresowej atrybutów. Przyjęto założenie, że dobrej jakości danych jest najlepsze w lesie, w których wykryto skrzynki pocztowej użytkownika. |
| W z usługi AD — typowe użytkownika |Znaleziono na globalnej liście adresowej atrybutów. W przypadku, gdy nie znaleźliśmy skrzynki pocztowej, inne dołączonym do obiektu może współtworzyć zawartość wartość atrybutu. |
| W z usługi AD — użytkownik programu Exchange |Istnieje tylko, jeśli wykryto programu Exchange. Przepływa ona wszystkie atrybuty Exchange infrastruktury. |
| W z usługi AD — Lync użytkownika |Istnieje tylko, jeśli wykryto Lync. Przepływa ona wszystkie atrybuty Lync infrastruktury. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o modelu konfiguracji w [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [interpretacji wyrażenia inicjowania obsługi administracyjnej deklaratywne](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Kontynuuj czytanie, jak działa Konfiguracja out-of-box w [opis użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md)
* Zobacz, jak zrobić praktyczne przy użyciu aprowizacja deklaratywna w [sposób wprowadzania zmian w domyślnej konfiguracji](how-to-connect-sync-change-the-configuration.md).

**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

