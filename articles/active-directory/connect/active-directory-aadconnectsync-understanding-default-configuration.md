---
title: 'Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej | Dokumentacja firmy Microsoft'
description: W tym artykule opisano domyślnej konfiguracji synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f4278dc3af1074b6de299444d2b205396bc0a9c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595312"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej
W tym artykule opisano reguły konfiguracji out-of-box. Dokumenty go zasady i wpływ tych reguł konfiguracji. On również przeprowadzi Cię przez domyślną konfigurację synchronizacji usługi Azure AD Connect. Celem jest czytnik rozumie, jak model konfiguracji o nazwie aprowizacją deklaratywną działa w przykładzie rzeczywistych. W tym artykule założono, że zainstalowano i skonfiguruj synchronizację programu Azure AD Connect przy użyciu Kreatora instalacji.

Aby poznać szczegóły model konfiguracji, przeczytaj [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Reguły Out-of-box z lokalnego do usługi Azure AD
W konfiguracji out-of-box można znaleźć następujących wyrażeń.

### <a name="user-out-of-box-rules"></a>Reguły out-of-box użytkownika
Te reguły dotyczą również iNetOrgPerson typu obiektu.

Obiekt użytkownika musi spełniać następujące do zsynchronizowania:

* Musi mieć sourceAnchor.
* Po utworzeniu obiektu w usłudze Azure AD, nie można zmienić sourceAnchor. Jeśli wartość jest zmienione lokalnymi, obiekt zatrzymuje synchronizowanie do momentu powrót do poprzedniej wartości zmiany sourceAnchor.
* Musi mieć atrybut accountEnabled (userAccountControl) wypełnione. Z lokalnej usługi Active Directory ten atrybut jest zawsze obecne i wypełnione.

Następujące obiekty użytkownika są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, wiele obiektów out-of-box w usłudze Active Directory, takie jak wbudowanego konta administratora, nie są zsynchronizowane.
* `IsPresent([sAMAccountName]) = False`. Upewnij się, że obiekty użytkownika z atrybutem nie sAMAccountName nie są zsynchronizowane. Ten przypadek tylko praktycznie się stanie w domenie uaktualnienia z NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Konto usługi używane przez synchronizacja programu Azure AD Connect i jego wcześniejsze wersje nie są synchronizowane.
* Nie Synchronizuj kont serwera Exchange, które nie będzie działać w programie Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nie są synchronizowane obiekty, które nie będzie działać w programie Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ta maska bitowa (& H21C07000) filtruje się następujące obiekty:
  * (W wersji zapoznawczej począwszy od wersji 1.1.524.0) folderu publicznego obsługujące pocztę
  * System towarzyszącej skrzynki pocztowej
  * Skrzynka pocztowa bazy danych skrzynki pocztowej (Skrzynka pocztowa systemu)
  * Uniwersalna grupa zabezpieczeń (nie odnoszą się do użytkownika, ale jest stosowany w przypadku starszych powodów)
  * Grupa uniwersalna inne niż (nie odnoszą się do użytkownika, ale jest stosowany w przypadku starszych powodów)
  * Plan skrzynki pocztowej
  * Skrzynka pocztowa odnajdywania
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj wszystkie obiekty ofiara replikacji.

Mają zastosowanie następujące reguły atrybutu:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atrybut sourceAnchor nie przyczynił się on z połączoną skrzynkę pocztową. Zakłada się, że jeśli znaleziono połączoną skrzynkę pocztową własnego konta jest dołączony później.
* Exchange powiązane atrybuty są synchronizowane tylko, jeśli atrybut **mailNickName** ma wartość.
* Gdy istnieje wiele lasów, atrybuty są używane w następującej kolejności:
  1. Atrybuty powiązane z logowania (na przykład userPrincipalName) są tworzone z lasu z włączonym kontem.
  2. Atrybuty, które znajdują się w wymiany usługi GAL (globalna lista adresów) są tworzone z lasu z skrzynki pocztowej programu Exchange.
  3. W przypadku nieodnalezienia nie skrzynek pocztowych, te atrybuty mogą pochodzić z dowolnym lesie.
  4. Exchange dotyczące atrybutów (techniczne atrybutów nie są widoczne w globalnej) są tworzone z lasu gdzie `mailNickname ISNOTNULL`.
  5. Jeśli istnieje wiele lasów, które spełniają jeden z tych zasad, następnie kolejność tworzenia (daty/godziny) łącznikami (lasów) służy do określania las, który wspiera atrybutów.

### <a name="contact-out-of-box-rules"></a>Skontaktuj się z reguł out-of-box
Skontaktuj się z pomocą obiektów musi spełniać następujące do zsynchronizowania:

* Kontakt musi być z włączoną obsługą poczty. Sprawdza z następującymi regułami:
  * `IsPresent([proxyAddresses]) = True)`. Atrybut proxyAddresses, muszą być wypełnione.
  * W atrybucie proxyAddresses lub atrybut poczty można znaleźć podstawowego adresu e-mail. Obecność @ służy do sprawdzania, czy zawartość jest adres e-mail. Jeden z tych dwóch reguł musi przyjąć wartość True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Istnieje już wpis z "SMTP:", a jeśli istnieje, można @ można znaleźć w ciągu?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Atrybut poczty wypełnione i jeśli tak jest, można @ można znaleźć w ciągu?

Następujące obiekty kontaktowe są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, skontaktuj się z pomocą obiektów oznaczone jako krytyczne są synchronizowane. Nie powinny być dowolny z konfiguracji domyślnej.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Te obiekty nie działają w usłudze Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj wszystkie obiekty ofiara replikacji.

### <a name="group-out-of-box-rules"></a>Grupa reguł out-of-box
Obiekt grupy musi spełniać następujące do zsynchronizowania:

* Musi mieć mniej niż 50 000 członków. Ta liczba jest liczba członków do grupy lokalnej.
  * Jeśli przed synchronizacji rozpocznie się po raz pierwszy, ma więcej członków, grupa nie jest zsynchronizowany.
  * Liczba elementów członkowskich wzrostu od kiedy został utworzony, następnie po dotarciu 50 000 członków zatrzymuje, synchronizowanie, dopóki liczba członkostwa jest ponownie niższy niż 50 000.
  * Uwaga: Liczba 50 000 członkostwa również jest wymuszane przez usługę Azure AD. Nie jesteś można zsynchronizować grupy z większej liczby elementów członkowskich, nawet jeśli zmodyfikować lub usunąć tę regułę.
* Jeśli grupa jest **grupy dystrybucji**, a następnie włączoną obsługę poczty musi mieć również. Zobacz [skontaktuj się z reguł out-of-box](#contact-out-of-box-rules) dla tej reguły jest wymuszana.

Następujące obiekty grupy są **nie** synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, wiele obiektów out-of-box w usłudze Active Directory, takie jak grupy wbudowanych administratorów, nie są zsynchronizowane.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starsze grupy używane przez narzędzie DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupa roli.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj wszystkie obiekty ofiara replikacji.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Reguły out-of-box ForeignSecurityPrincipal
FSP są przyłączone do "dowolne" (\*) obiektów w magazynie metaverse. W rzeczywistości tego przyłączenia odbywa się tylko dla użytkowników i grup zabezpieczeń. Ta konfiguracja temu członkostwa w różnych lasach są rozwiązany i poprawnie reprezentowana w usłudze Azure AD.

### <a name="computer-out-of-box-rules"></a>Reguły out-of-box komputera
Obiekt komputera musi spełniać następujące do zsynchronizowania:

* `userCertificate ISNOTNULL`. Tylko komputery z systemem Windows 10 wypełniania tego atrybutu. Wszystkie obiekty komputerów z wartością w tym atrybucie są synchronizowane.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Opis scenariusza out-of-box reguły
W tym przykładzie użyto wdrożenie z jednym lesie konta (A), jeden las zasobów (R) i jeden katalog usługi Azure AD.

![Obraz z opis scenariusza](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

W tej konfiguracji zakłada się, że jest włączone konto w lesie konta i wyłączonych kont w lesie zasobów z połączoną skrzynkę pocztową.

Naszym celem z domyślnej konfiguracji jest:

* Atrybuty związane z logowaniem są synchronizowane z lasu z włączonym kontem.
* Atrybuty, które znajdują się w globalnej (globalna lista adresów) są synchronizowane z lasu za pomocą skrzynki pocztowej. W przypadku nieodnalezienia nie skrzynkę pocztową, jest używany innym lesie.
* Jeśli zostanie znaleziony połączoną skrzynkę pocztową, można znaleźć połączonego konta włączone obiektów, które mają zostać wyeksportowane do usługi Azure AD.

### <a name="synchronization-rule-editor"></a>Edytor reguł synchronizacji
Konfiguracja można było wyświetlać i modyfikować za pomocą narzędzia Edytor reguł synchronizacji (SRE) i skrót do niego znajduje się w start menu.

![Ikona Edytor reguł synchronizacji](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE jest narzędziem do zestawu zasobów i zainstalować go z synchronizacji Azure AD Connect. Aby można było go uruchomić, musi być członkiem grupy ADSyncAdmins. Podczas uruchamiania, zobacz podobny do następującego:

![Reguły synchronizacji ruchu przychodzącego](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

W tym okienku zobacz temat wszystkie reguły synchronizacji utworzone dla danej konfiguracji. Każdy wiersz w tabeli jest jedna reguła synchronizacji. Po lewej stronie w obszarze typów reguł, są wyświetlane dwa różne typy: przychodzących i wychodzących. Ruchu przychodzącego i wychodzącego jest z widoku metaverse. Przede wszystkim ma skoncentrować się na reguł ruchu przychodzącego w tym omówieniu. Rzeczywista lista reguł synchronizacji zależy od wykrytych schematu w usłudze AD. W powyższym rysunku lasu kont (fabrikamonline.com) nie ma żadnych usług, takich jak Exchange i Lync, i zostały utworzone żadne reguły synchronizacji dla tych usług. Jednak w lesie zasobów (res.fabrikamonline.com) można znaleźć reguły synchronizacji dla tych usług. Zawartość reguły jest różne w zależności od wykryto wersję. Na przykład w przypadku wdrożenia z programu Exchange 2013 istnieje więcej przepływów atrybutów skonfigurowane niż w Exchange 2010 2007.

### <a name="synchronization-rule"></a>Reguła synchronizacji
Reguła synchronizacji jest obiekt konfiguracji przy użyciu zestawu atrybutów przepływu po spełnieniu warunku. Służy również opisano, jak obiekt przestrzeni łącznika jest powiązane z obiektem w magazynie metaverse, znany jako **sprzężenia** lub **odpowiada**. Reguły synchronizacji mają pierwszeństwo przed wartość wskazującą, jaki sposób są powiązane ze sobą. Reguła synchronizacji z niższą wartość liczbową ma wyższy priorytet i konflikt przepływu atrybutu wyższy priorytet wins rozwiązywania konfliktów.

Na przykład przyjrzeć się reguły synchronizacji **w z usługi Active Directory — AccountEnabled użytkownika**. Oznacz ten wiersz w SRE i wybierz **Edytuj**.

Ponieważ ta reguła jest regułą out-of-box, pojawi się ostrzeżenie podczas otwierania reguły. Nie należy wprowadzać dowolne [zmian reguł out-of-box](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), więc użytkownik jest pytany, czy zamiaru. W takim przypadku tylko mają dotyczyć zasady. Wybierz **nr**.

![Synchronizacja ostrzeżenia reguły](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Reguła synchronizacji ma cztery sekcje konfiguracji: opis zakresu filtru, reguły sprzężenia i przekształcenia.

#### <a name="description"></a>Opis
Pierwsza sekcja zawiera podstawowe informacje, takie jak nazwa i opis.

![Opis elementu karcie Edytor reguł synchronizacji ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Można również znaleźć informacje o który połączony system powiązany jest regułą, które obiekt typu w system połączony, których on obowiązuje, a typ obiektu metaverse. Typ obiektu metaverse jest zawsze osoby niezależnie od tego, gdy typ obiektu źródłowego jest użytkownik, iNetOrgPerson lub skontaktuj się z pomocą. Typ obiektu metaverse nigdy nie należy zmieniać, aby została utworzona jako typu ogólnego. Do sprzężenia, StickyJoin lub udostępnić można ustawić typu łącza. To ustawienie działa razem z sekcji dołączyć reguły i jest objęte później.

Można również sprawdzić, czy ta reguła synchronizacji jest używana do synchronizacji haseł. Jeśli użytkownik jest w zakresie dla tej reguły synchronizacji, hasło jest zsynchronizowany z lokalnymi do chmury (przy założeniu, że została włączona funkcja synchronizacji hasła).

#### <a name="scoping-filter"></a>Filtr zakresów
Sekcja filtru zakresu jest używana do skonfiguruj, kiedy ma zostać zastosowana reguła synchronizacji. Ponieważ nazwy reguły synchronizacji poszukujesz wskazuje powinny być stosowane tylko włączonych użytkowników, zakres jest skonfigurowany tak atrybutu AD **userAccountControl** nie może mieć bit 2 ustawione. Aparat synchronizacji znajduje użytkownika w usłudze AD, zastosowanie tej synchronizacji regułę, gdy **userAccountControl** ma ustawioną wartość dziesiętna 512 (włączone zwykłego użytkownika). Nie dotyczy reguła, gdy użytkownik ma **userAccountControl** ustawioną 514 (wyłączone zwykłego użytkownika).

![Określanie zakresu kartę w edytorze reguły synchronizacji ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Filtr zakresu ma grup i klauzule, które mogą być zagnieżdżone. Wszystkie klauzule wewnątrz grupy muszą być spełnione dla reguły synchronizacji do zastosowania. Po zdefiniowaniu wiele grup co najmniej jedną grupę, muszą być spełnione reguły do zastosowania. Oznacza to operatora logicznego OR jest obliczane między grupami i logicznych i jest obliczane w grupie. Przykładem takiej konfiguracji można znaleźć w reguła synchronizacji ruchu wychodzącego **Out do usługi AAD — Join grupy**. Istnieje kilka grup filtru synchronizacji, na przykład jeden dla grup zabezpieczeń (`securityEnabled EQUAL True`) i jeden dla grup dystrybucyjnych (`securityEnabled EQUAL False`).

![Określanie zakresu kartę w edytorze reguły synchronizacji ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Ta reguła jest używana do definiowania grupy, które powinny być przygotowana do usługi Azure AD. Grupy dystrybucji muszą być włączone mają być synchronizowane z usługą Azure AD poczty, ale dla grup zabezpieczeń wiadomości e-mail nie jest wymagana.

#### <a name="join-rules"></a>Dołącz zasady
Trzecia sekcja służy do konfigurowania, jak obiekty w przestrzeni łącznika odnoszą się do obiektów w magazynie metaverse. Reguła sprawdzono wcześniej nie ma żadnej konfiguracji w przypadku dołączenia reguł, dlatego zamiast tego ma aby przyjrzeć się **w z usługi Active Directory — użytkownik przyłączyć**.

![Dołącz kartę reguły w edytorze reguły synchronizacji ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Zawartość reguły sprzężenia zależy od opcji dopasowywania wybrane w Kreatorze instalacji. Reguła ruchu przychodzącego oceny rozpoczyna się od obiektu do przestrzeni łącznika źródła i każdej grupy w regułach sprzężenia jest obliczane w sekwencji. Jeśli obiekt źródłowy jest ocena odpowiadające dokładnie jednego obiektu w magazynie metaverse przy użyciu jednej reguły sprzężenia, są połączone obiekty. Jeśli zostały ocenione wszystkie reguły i nie ma dopasowania, jest używany typ łącza na stronie z opisem. Jeśli ustawiono tę konfigurację **udostępniania**, a następnie tworzony jest nowy obiekt w miejscu docelowym metaverse. Aby udostępnić nowy obiekt do środowiska metaverse jest nazywane również do **projektu** obiekt do środowiska metaverse.

Raz oceniane są tylko reguły sprzężenia. Obiekt miejsca łącznika i obiektu metaverse są połączone, pozostają połączone tak długo, jak zakres reguły synchronizacji jest nadal spełnione.

Podczas oceny zasad synchronizacji, tylko jedna reguła synchronizacji z zdefiniowanych reguł sprzężenia musi być w zakresie. W przypadku znalezienia wielu reguł synchronizacji przy użyciu reguł sprzężenia dla jednego obiektu, jest zgłaszany błąd. Z tego powodu najlepszym rozwiązaniem jest mieć tylko jedną regułę synchronizacji z sprzężenia zdefiniowany w przypadku wielu reguł synchronizacji znajdują się w zakresie dla obiekt. W konfiguracji out-of-box synchronizacja programu Azure AD Connect, te reguły można można znaleźć, sprawdzając nazwę i znaleźć słowem **Join** na końcu nazwy. Reguła synchronizacji nie zdefiniowano żadnych reguł sprzężenia stosuje przepływów atrybutów innych reguł synchronizacji połączone obiekty lub udostępniane nowy obiekt w obiekcie docelowym.

Jeśli przyjrzymy się widoczne na obrazie, zobaczysz, że reguła próbuje dołączyć do **objectSID** z **msExchMasterAccountSid** (Exchange) i **msRTCSIP-OriginatorSid** (Lync), która jest Oczekujemy w topologii lasu zasobów dla konta. Możesz znaleźć tę samą zasadę na wszystkich lasach. Zakłada się, że w każdym lesie może być lasu konto lub zasobu. Ta konfiguracja działa także jeśli masz konta, które znajdują się w jednym lesie ani nie musi być przyłączony.

#### <a name="transformations"></a>Przekształcenia
W sekcji transformacji definiuje wszystkie przepływy atrybutów mające zastosowanie do obiektu docelowego, gdy są połączone obiekty i spełnieniu Filtr zakresu. Po powrocie do **w z usługi Active Directory — AccountEnabled użytkownika** reguły synchronizacji, można znaleźć przekształcenia następujące:

![Przekształcenia karcie Edytor reguł synchronizacji ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Aby umieścić tę konfigurację w kontekście we wdrożeniu lasu zasobów konta oczekuje się znaleźć włączone konto w lesie konta i wyłączonych kont w lesie zasobów przy użyciu ustawień programu Exchange i Lync. Reguła synchronizacji poszukujesz zawiera atrybuty wymagane do logowania i te atrybuty powinny przepływać z lasu w przypadku, gdy jest włączone konto. Te przepływy atrybutów są umieszczane razem w jednej reguły synchronizacji.

Przekształcenie może mieć różne typy: stała, bezpośredni i wyrażenie.

* Stały przepływ zawsze przepływ wartości zapisane na stałe. W przypadku powyższych zawsze ustawia wartości **True** atrybut metaverse o nazwie **accountEnabled**.
* Bezpośrednie przepływu przepływa zawsze wartość atrybutu w źródle atrybut docelowy jako — jest.
* Trzeci typ przepływu jest wyrażeniem i umożliwia bardziej zaawansowane konfiguracje.

Język wyrażeń jest VBA (Visual Basic for Applications), to osoby z doświadczeniem pakietu Microsoft Office lub VBScript rozpoznają format. Atrybuty są ujęte w nawiasy kwadratowe, [attributeName]. Atrybut nazwy i nazwy funkcji jest rozróżniana wielkość liter, ale edytor reguł synchronizacji ocenia wyrażenia i podaj ostrzeżenie, jeśli wyrażenie jest nieprawidłowe. Wszystkie wyrażenia są wyrażane w jednym wierszu z zagnieżdżonych funkcji. Aby pokazać zasilania języka konfiguracji, Oto przepływu dla pwdLastSet, ale dodatkowe komentarze wstawione:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Zobacz [opis deklaratywne inicjowania obsługi administracyjnej wyrażenia](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) Aby uzyskać więcej informacji na temat język wyrażeń dla przepływów atrybutów.

### <a name="precedence"></a>Pierwszeństwo
Teraz przeglądał niektórych poszczególne reguły synchronizacji, ale zasady współpracują ze sobą w konfiguracji. W niektórych przypadkach wartość atrybutu jest z wielu reguł synchronizacji przyczyniły się do powstania tego samego atrybutu docelowego. W takim przypadku pierwszeństwo atrybutów jest używany do określenia, który atrybut wins. Na przykład sprawdzić atrybut sourceAnchor. Ten atrybut jest atrybutem ważne, aby móc zalogować się do usługi Azure AD. Dla tego atrybutu w dwie różne reguły synchronizacji, można znaleźć przepływu atrybutu **w z usługi Active Directory — AccountEnabled użytkownika** i **w z usługi Active Directory — typowe użytkownika**. Z powodu pierwszeństwo reguły synchronizacji atrybut sourceAnchor jest przyczyniły się do lasu z włączonym kontem najpierw przypadku wiele obiektów dołączonych do obiektu metaverse. Jeśli nie ma żadnych kont włączone, a następnie reguły synchronizacji wychwytywania korzysta z aparatu synchronizacji **w z usługi Active Directory — typowe użytkownika**. Taka konfiguracja powoduje, że nawet w przypadku konta, które są wyłączone, jest nadal sourceAnchor.

![Reguły synchronizacji ruchu przychodzącego](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Pierwszeństwo dla reguły synchronizacji jest ustawiona w grupach przez Kreatora instalacji. Wszystkie reguły w grupie mają taką samą nazwę, ale są one połączone z różnych katalogach połączonych. Kreator instalacji umożliwia reguły **w z usługi Active Directory — użytkownik przyłączyć** najwyższy priorytet i iteracje przez wszystkie połączone AD katalogów. Kontynuuje następnie dalej grup reguł w preferowanej kolejności. Wewnątrz grupy zasady zostaną dodane w kolejności zgodnej z łączników zostały dodane w kreatorze. Jeśli innego łącznika zostanie dodany za pomocą kreatora, zostaną ponownie uporządkowane reguły synchronizacji i nowy łącznik reguły są wstawiane ostatnio w każdej grupie.

### <a name="putting-it-all-together"></a>Składanie wszystkiego razem
Teraz wiemy wystarczająco o reguły synchronizacji, aby można było zrozumieć sposób działania konfiguracji przy użyciu różnych reguł synchronizacji. Jeśli przyjrzymy się użytkownika i atrybuty, które są przyczyniły się do środowiska metaverse, zasady są stosowane w następującej kolejności:

| Name (Nazwa) | Komentarz |
|:--- |:--- |
| W z usługi Active Directory — sprzężenia użytkownika |Reguła łączenia obiektów miejsca metaverse łącznika. |
| W z usługi Active Directory — UserAccount włączone |Atrybuty wymagane do logowania do usługi Azure AD i Office 365. Chcemy tych atrybutów z włączonym kontem. |
| W z usługi Active Directory — typowe użytkownika z programu Exchange |Znaleziono na globalnej liście adresowej atrybutów. Przyjęto założenie, że najlepiej jakości danych w lesie, w którym znaleźliśmy skrzynki pocztowej użytkownika. |
| W z usługi Active Directory — typowe użytkownika |Znaleziono na globalnej liście adresowej atrybutów. W przypadku, gdy nie znaleziono skrzynki pocztowej, dowolnego dołączonego do obiektu może przyczynić się wartość atrybutu. |
| W z usługi Active Directory — Exchange użytkownika |Istnieje tylko jeśli wykryto programu Exchange. Przepływ wszystkie atrybuty Exchange infrastruktury. |
| W z usługi Active Directory — Lync użytkownika |Istnieje tylko jeśli wykryto Lync. Przepływ wszystkie atrybuty Lync infrastruktury. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o model konfiguracji w [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażenia w [opis deklaratywne inicjowania obsługi administracyjnej wyrażenia](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Kontynuuj czytanie działanie konfiguracji out-of-box w [opis użytkowników i kontaktów](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* W temacie jak zrobić praktyczne przy użyciu aprowizacją deklaratywną w [jak wprowadzić zmianę do domyślnej konfiguracji](active-directory-aadconnectsync-change-the-configuration.md).

**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)

