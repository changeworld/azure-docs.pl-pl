---
title: 'Synchronizacja usługi Azure AD Connect: opis konfiguracji domyślnej | Dokumenty firmy Microsoft'
description: W tym artykule opisano domyślną konfigurację w synchronizacji usługi Azure AD Connect.
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
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548870"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej
W tym artykule wyjaśniono reguły konfiguracji out-of-box. Dokumentuje reguły i jak te reguły wpływają na konfigurację. Przeprowadzi Cię również przez domyślną konfigurację synchronizacji usługi Azure AD Connect. Celem jest, że czytelnik rozumie, jak model konfiguracji, o nazwie deklaratywny inicjowania obsługi administracyjnej, działa w przykładzie rzeczywistych. W tym artykule przyjęto założenie, że synchronizacja usługi Azure AD Connect została już zainstalowana i skonfigurowana przy użyciu kreatora instalacji.

Aby zrozumieć szczegóły modelu konfiguracji, przeczytaj [opis aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Reguły gotowe do użycia z lokalnego do usługi Azure AD
Następujące wyrażenia można znaleźć w konfiguracji out-of-box.

### <a name="user-out-of-box-rules"></a>Reguły użytkownika gotowe do użycia
Reguły te mają również zastosowanie do typu obiektu iNetOrgPerson.

Obiekt użytkownika musi spełniać następujące wymagania, aby zsynchronizować:

* Musi mieć źródłoAnchor.
* Po utworzeniu obiektu w usłudze Azure AD, sourceAnchor nie można zmienić. Jeśli wartość zostanie zmieniona lokalnie, obiekt przestaje synchronizować, dopóki sourceAnchor zostanie zmieniony z powrotem na poprzednią wartość.
* Musi mieć atrybut accountEnabled (userAccountControl) wypełniony. W lokalnej usłudze Active Directory ten atrybut jest zawsze obecny i wypełniany.

Następujące obiekty użytkownika **nie** są synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że wiele gotowych obiektów w usłudze Active Directory, takich jak wbudowane konto administratora, nie jest synchronizowanych.
* `IsPresent([sAMAccountName]) = False`. Upewnij się, że obiekty użytkownika bez atrybutu sAMAccountName nie są synchronizowane. Ten przypadek będzie praktycznie zdarzyć się tylko w domenie uaktualniony z NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nie synchronizuj konta usługi używanego przez synchronizację usługi Azure AD Connect i jego wcześniejszych wersji.
* Nie synchronizuj kont programu Exchange, które nie będą działać w usłudze Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nie synchronizuj obiektów, które nie będą działać w usłudze Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ta maska bitowa (&H21C07000) odfiltruje następujące obiekty:
  * Folder publiczny z włączoną obsługą poczty (w wersji zapoznawczej w wersji 1.1.524.0)
  * Skrzynka pocztowa obsługująca system
  * Skrzynka pocztowa bazy danych skrzynek pocztowych (systemowa skrzynka pocztowa)
  * Universal Security Group (nie będzie ubiegać się o użytkownika, ale jest obecny ze względu na starsze)
  * Grupa nieniwersyci (nie dotyczyłaby użytkownika, ale jest obecna ze względu na starsze wersje)
  * Plan skrzynki pocztowej
  * Skrzynka pocztowa odnajdywania
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie synchronizuj żadnych obiektów ofiary replikacji.

Obowiązują następujące reguły atrybutów:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atrybut sourceAnchor nie jest współtworzony z połączonej skrzynki pocztowej. Zakłada się, że jeśli połączona skrzynka pocztowa została znaleziona, rzeczywiste konto zostanie połączone później.
* Atrybuty powiązane z programem Exchange są synchronizowane tylko wtedy, gdy atrybut **mailNickName** ma wartość.
* Gdy istnieje wiele lasów, atrybuty są używane w następującej kolejności:
  1. Atrybuty związane z logowania (na przykład userPrincipalName) są współtworzone z lasu z włączonym kontem.
  2. Atrybuty, które można znaleźć w exchange gal (globalna lista adresowa) są współtworzone z lasu z skrzynki pocztowej programu Exchange.
  3. Jeśli nie można znaleźć skrzynki pocztowej, atrybuty te mogą pochodzić z dowolnego lasu.
  4. Atrybuty związane z wymianą (atrybuty techniczne niewidoczne w GAL) są wpłacane z lasu, w którym `mailNickname ISNOTNULL`.
  5. Jeśli istnieje wiele lasów, które spełniają jedną z tych reguł, a następnie kolejność tworzenia (data/godzina) łączników (lasy) jest używany do określenia, który las przyczynia się atrybuty. Pierwszy połączony las będzie pierwszym lasem do synchronizacji. 

### <a name="contact-out-of-box-rules"></a>Skontaktuj się z regułami gotowymi do użycia
Obiekt kontaktowy musi spełniać następujące wymagania, aby można było zsynchronizować:

* Kontakt musi być włączony pocztą. Jest weryfikowany za pomocą następujących zasad:
  * `IsPresent([proxyAddresses]) = True)`. Atrybut proxyAddresses musi być wypełniony.
  * Podstawowy adres e-mail można znaleźć w atrybucie proxyAddresses lub w atrybucie mail. Obecność an \@ służy do sprawdzenia, czy zawartość jest adresem e-mail. Jedna z tych dwóch reguł musi zostać oceniona na wartość True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Czy istnieje wpis z "SMTP:" i jeśli \@ istnieje, można znaleźć w ciągu?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Czy atrybut mail jest wypełniony i jeśli \@ tak jest, czy można go znaleźć w ciągu?

Następujące obiekty kontaktu **nie** są synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że żadne obiekty kontaktowe oznaczone jako krytyczne nie są synchronizowane. Nie powinien być dowolny z domyślną konfiguracją.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Te obiekty nie będą działać w usłudze Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie synchronizuj żadnych obiektów ofiary replikacji.

### <a name="group-out-of-box-rules"></a>Grupowe reguły typu out-of-box
Obiekt grupy musi spełniać następujące wymagania, aby zsynchronizować:

* Musi mieć mniej niż 50 000 członków. Ta liczba jest liczbą członków w grupie lokalnej.
  * Jeśli ma więcej elementów członkowskich przed rozpoczęciem synchronizacji po raz pierwszy, grupa nie jest synchronizowana.
  * Jeśli liczba członków wzrośnie od momentu jego utworzenia, po osiągnięciu 50 000 członków przestanie się synchronizować, dopóki liczba członków nie zostanie ponownie niższa niż 50 000.
  * Uwaga: Liczba 50 000 członków jest również wymuszana przez usługę Azure AD. Nie można synchronizować grup z większą liczrą członków, nawet jeśli ta reguła zostanie zmodyfikująca lub 7.
* Jeśli grupa jest **grupą dystrybucyjną,** musi ona być również włączona poczta. Zobacz [Reguły kontaktu out-of-box](#contact-out-of-box-rules) dla tej reguły jest wymuszane.

Następujące obiekty grupy **nie** są synchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że wiele gotowych obiektów w usłudze Active Directory, takich jak wbudowana grupa administratorów, nie jest synchronizowanych.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starsza grupa używana przez program DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupa ról.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie synchronizuj żadnych obiektów ofiary replikacji.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box zasady
FsPs są połączone do "any" (\*) obiektu w metaverse. W rzeczywistości to sprzężenie odbywa się tylko dla użytkowników i grup zabezpieczeń. Ta konfiguracja zapewnia, że członkostwa między lasami są rozpoznawane i reprezentowane poprawnie w usłudze Azure AD.

### <a name="computer-out-of-box-rules"></a>Reguły gotowe do użycia komputera
Obiekt komputera musi spełniać następujące wymagania, aby można było zsynchronizować:

* `userCertificate ISNOTNULL`. Tylko komputery z systemem Windows 10 wypełniają ten atrybut. Wszystkie obiekty komputera o wartości w tym atrybucie są synchronizowane.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Opis scenariusza reguł gotowych do użycia
W tym przykładzie używamy wdrożenia z jednym lasem konta (A), jednym lasem zasobów (R) i jednym katalogiem usługi Azure AD.

![Obraz z opisem scenariusza](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

W tej konfiguracji zakłada się, że w lesie kont znajduje się włączone konto, a w lesie zasobów z połączoną skrzynką pocztową jest wyłączone konto.

Naszym celem w konfiguracji domyślnej jest:

* Atrybuty związane z logacją są synchronizowane z lasu z włączonym kontem.
* Atrybuty, które można znaleźć w gal (globalna lista adresów) są synchronizowane z lasu ze skrzynką pocztową. Jeśli nie można znaleźć skrzynki pocztowej, używany jest inny las.
* Jeśli zostanie znaleziona połączona skrzynka pocztowa, należy znaleźć połączone włączone konto, aby obiekt został wyeksportowany do usługi Azure AD.

### <a name="synchronization-rule-editor"></a>Edytor reguł synchronizacji
Konfigurację można przeglądać i zmieniać za pomocą narzędzia Edytor reguł synchronizacji (SRE), a skrót do niej można znaleźć w menu Start.

![Ikona Edytora reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE jest narzędziem zestawu zasobów i jest zainstalowany z synchronizacją usługi Azure AD Connect. Aby móc go uruchomić, musisz być członkiem grupy ADSyncAdmins. Po uruchomieniu widać coś takiego:

![Przychodzące reguły synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

W tym okienku są widoczne wszystkie reguły synchronizacji utworzone dla konfiguracji. Każdy wiersz w tabeli jest jedną regułą synchronizacji. Po lewej stronie w obszarze Typy reguł są wyświetlane dwa różne typy: Przychodzące i Wychodzące. Przychodzące i wychodzące jest z widoku metaverse. W tym przeglądzie skupisz się głównie na regułach przychodzących. Rzeczywista lista reguł synchronizacji zależy od wykrytego schematu w u. Na powyższym rysunku las kont (fabrikamonline.com) nie ma żadnych usług, takich jak Exchange i Lync, i nie utworzono reguł synchronizacji dla tych usług. Jednak w lesie zasobów (res.fabrikamonline.com) można znaleźć reguły synchronizacji dla tych usług. Zawartość reguł różni się w zależności od wykrytej wersji. Na przykład we wdrożeniu z programem Exchange 2013 skonfigurowano więcej przepływów atrybutów niż w programie Exchange 2010/2007.

### <a name="synchronization-rule"></a>Reguła synchronizacji
Reguła synchronizacji jest obiektem konfiguracji z zestawem atrybutów płynących po spełnieniu warunku. Jest również używany do opisywania, jak obiekt w przestrzeni łącznika jest powiązany z obiektem w metaverse, znany jako **sprzężenie** lub **dopasowanie**. Reguły synchronizacji mają wartość pierwszeństwa wskazującą, w jaki sposób odnoszą się do siebie nawzajem. Reguła synchronizacji z niższą wartością liczbową ma wyższy priorytet i w konflikcie przepływu atrybutów wyższy priorytet wygrywa rozwiązywanie konfliktów.

Na przykład przyjrzyj się regułie synchronizacji **w u użytkownika z usługi AD — Konto użytkownika**. Oznacz ten wiersz w SRE i wybierz pozycję **Edytuj**.

Ponieważ ta reguła jest regułą nieuwzłaną, po otwarciu reguły jest to ostrzeżenie. Nie należy wprowadzać żadnych [zmian w zasadach out-of-box,](how-to-connect-sync-best-practices-changing-default-configuration.md)więc jesteś pytany, jakie są Twoje intencje. W takim przypadku chcesz tylko wyświetlić regułę. Wybierz **nr**.

![Ostrzeżenie o regułach synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Reguła synchronizacji ma cztery sekcje konfiguracji: Opis, Filtr zakresu, Reguły sprzężenia i Przekształcenia.

#### <a name="description"></a>Opis
Pierwsza sekcja zawiera podstawowe informacje, takie jak nazwa i opis.

![Karta Opis w edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Można również znaleźć informacje o tym, który połączony system ta reguła jest związana z, jaki typ obiektu w połączonym systemie stosuje się do i typ obiektu metaverse. Typ obiektu metaverse jest zawsze osoba, niezależnie od tego, kiedy typ obiektu źródłowego jest użytkownik, iNetOrgPerson lub kontakt. Typ obiektu metaverse nigdy nie powinien się zmieniać, więc jest tworzony jako typ ogólny. Typ łącza można ustawić na Join, StickyJoin lub Provision. To ustawienie współpracuje z sekcją Reguły dołączania i jest omówione później.

Widać również, że ta reguła synchronizacji jest używana do synchronizacji haseł. Jeśli użytkownik jest w zakresie dla tej reguły synchronizacji, hasło jest synchronizowane z lokalnego do chmury (przy założeniu, że włączono funkcję synchronizacji hasła).

#### <a name="scoping-filter"></a>Filtr zakresu
Sekcja Filtr zakresu służy do konfigurowania, kiedy powinna obowiązywać reguła synchronizacji. Ponieważ nazwa reguły synchronizacji, na którą patrzysz, wskazuje, że powinna być stosowana tylko dla włączonych użytkowników, zakres jest skonfigurowany, więc atrybut AD **userAccountControl** nie może mieć zestawu bit 2. Gdy aparat synchronizacji znajdzie użytkownika w udaniu AD, stosuje tę regułę synchronizacji, gdy **userAccountControl** jest ustawiony na wartość dziesiętną 512 (włączony zwykły użytkownik). Nie stosuje reguły, gdy użytkownik ma **userAccountControl** ustawiona na 514 (wyłączony zwykły użytkownik).

![Karta Określanie zakresu w edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Filtr zakresu ma grupy i klauzule, które mogą być zagnieżdżone. Wszystkie klauzule wewnątrz grupy muszą być spełnione, aby reguła synchronizacji miała zastosowanie. Po zdefiniowaniu wielu grup, co najmniej jedna grupa musi być spełniona, aby reguła miała zastosowanie. Oznacza to, że logiczne OR jest oceniane między grupami i logiczne I jest oceniane wewnątrz grupy. Przykład tej konfiguracji można znaleźć w reguły synchronizacji wychodzącej **do AAD — Group Join**. Istnieje kilka grup filtrów synchronizacji, na`securityEnabled EQUAL True`przykład jedna dla grup`securityEnabled EQUAL False`zabezpieczeń ( ) i jedna dla grup dystrybucyjnych ( ).

![Karta Określanie zakresu w edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Ta reguła jest używana do definiowania grup, które powinny być aprowidzone do usługi Azure AD. Grupy dystrybucyjne muszą być włączone pocztę, aby były synchronizowane z usługą Azure AD, ale dla grup zabezpieczeń nie jest wymagana wiadomość e-mail.

#### <a name="join-rules"></a>Reguły dołączania
Trzecia sekcja służy do konfigurowania, jak obiekty w przestrzeni łącznika odnoszą się do obiektów w metaverse. Reguła, na którą patrzysz wcześniej, nie ma żadnej konfiguracji dla reguł dołączania, więc zamiast tego zamierzasz przyjrzeć się **In from AD — User Join**.

![Karta Dołączanie do reguł w edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Zawartość reguły sprzężenia zależy od opcji dopasowania wybranej w kreatorze instalacji. Dla reguły przychodzącej ocena rozpoczyna się od obiektu w przestrzeni łącznika źródłowego, a każda grupa w regułach sprzężenia jest oceniana w sekwencji. Jeśli obiekt źródłowy jest oceniany w celu dopasowania dokładnie jeden obiekt w metaverse przy użyciu jednej z reguł sprzężenia, obiekty są łączone. Jeśli wszystkie reguły zostały ocenione i nie ma dopasowania, a następnie typ łącza na stronie opisu jest używany. Jeśli ta konfiguracja jest **ustawiona na Provision**, a następnie nowy obiekt jest tworzony w miejscu docelowym, metaverse, jeśli co najmniej jeden atrybut w kryteriach sprzężenia jest obecny (ma wartość). Aby aprowizować nowy obiekt do metaverse jest również znany jako **projekt** obiektu do metaverse.

Reguły sprzężenia są oceniane tylko raz. Gdy obiekt przestrzeni łącznika i obiekt metaverse są połączone, pozostają one połączone tak długo, jak zakres reguły synchronizacji jest nadal spełniony.

Podczas oceny reguł synchronizacji tylko jedna reguła synchronizacji ze zdefiniowanymi regułami sprzężenia musi być w zakresie. Jeśli dla jednego obiektu zostanie znalezionych wiele reguł synchronizacji z regułami sprzężenia, zostanie wyświetlony błąd. Z tego powodu najlepszym rozwiązaniem jest mieć tylko jedną regułę synchronizacji z sprzężenia zdefiniowane, gdy wiele reguł synchronizacji są w zakresie dla obiektu. W konfiguracji out-of-box dla synchronizacji usługi Azure AD Connect, te reguły można znaleźć, patrząc na nazwę i znaleźć te z wyrazem **Dołącz** na końcu nazwy. Reguła synchronizacji bez zdefiniowanych reguł sprzężenia stosuje przepływy atrybutów, gdy inna reguła synchronizacji połączyła obiekty lub aprowizować nowy obiekt w obiekcie docelowym.

Jeśli spojrzysz na powyższy obrazek, zobaczysz, że reguła próbuje połączyć **objectSID** z **msExchMasterAccountSid** (Exchange) i **msRTCSIP-OriginatorSid** (Lync), czego oczekujemy w topologii lasu zasobów kont. Ta sama reguła znajduje się we wszystkich lasach. Założenie jest takie, że każdy las może być albo kontem, albo lasem zasobów. Ta konfiguracja działa również, jeśli masz konta, które mieszkają w jednym lesie i nie muszą być przyłączone.

#### <a name="transformations"></a>Przekształcenia
Sekcja transformacji definiuje wszystkie przepływy atrybutów, które mają zastosowanie do obiektu docelowego, gdy obiekty są połączone i filtr zakresu jest spełniony. Wracając do reguły synchronizacji **in z usługi AD — użytkownik rozliczane,** można znaleźć następujące przekształcenia:

![Karta Przekształcenia w edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Aby umieścić tę konfigurację w kontekście, we wdrożeniu lasu zasoby konta oczekuje się znalezienia włączonego konta w lesie kont i wyłączonego konta w lesie zasobów z ustawieniami programu Exchange i Lync. Reguła synchronizacji, na której patrzysz, zawiera atrybuty wymagane do logowania, a te atrybuty powinny przepływać z lasu, w którym istnieje włączone konto. Wszystkie te przepływy atrybutów są połączone w jedną regułę synchronizacji.

Transformacja może mieć różne typy: stała, bezpośrednia i wyrażenie.

* Stały przepływ zawsze przepływa wartość zakodowaną na stałe. W powyższym przypadku zawsze ustawia wartość **True** w atrybutie metaverse o nazwie **accountEnabled**.
* Przepływ bezpośredni zawsze przepływa wartość atrybutu w źródle do atrybutu docelowego w stanie— jest.
* Trzeci typ przepływu to Expression i pozwala na bardziej zaawansowane konfiguracje.

Językiem wyrażenia jest VBA (Visual Basic for Applications), więc osoby z doświadczeniem pakietu Microsoft Office lub VBScript rozpoznają format. Atrybuty są ujęte w nawiasy kwadratowe[atrybuty]. W nazwach atrybutów i nazwach funkcji rozróżniana jest wielkość liter, ale edytor reguł synchronizacji ocenia wyrażenia i ostrzega, jeśli wyrażenie jest nieprawidłowe. Wszystkie wyrażenia są wyrażone w jednym wierszu z zagnieżdżonych funkcji. Aby pokazać moc języka konfiguracji, oto przepływ dla pwdLastSet, ale z dodatkowymi komentarzami wstawionymi:

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

Zobacz [Opis wyrażenia deklaratywne inicjowania obsługi administracyjnej,](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) aby uzyskać więcej informacji na temat języka wyrażeń dla przepływów atrybutów.

### <a name="precedence"></a>Pierwszeństwo
Teraz spojrzał na niektóre poszczególne reguły synchronizacji, ale reguły współpracują ze sobą w konfiguracji. W niektórych przypadkach wartość atrybutu jest przekazywała z wielu reguł synchronizacji do tego samego atrybutu docelowego. W takim przypadku pierwszeństwo atrybutu jest używany do określenia, który atrybut wins. Na przykład spójrz na źródło atrybutuAnchor. Ten atrybut jest ważnym atrybutem, aby móc zalogować się do usługi Azure AD. Przepływ atrybutów dla tego atrybutu można znaleźć w dwóch różnych regułach synchronizacji, **w u: AD — Konto użytkownika i** W u użytkownika z usługi AD — Wspólne dla **użytkownika**. Ze względu na pierwszeństwo reguły synchronizacji atrybut sourceAnchor jest współtworzony z lasu z włączonym kontem, gdy istnieje kilka obiektów połączonych z obiektem metaverse. Jeśli nie ma włączonych kont, aparat synchronizacji używa reguły synchronizacji catch-all **in z usługi AD — Wspólne dla użytkownika**. Ta konfiguracja zapewnia, że nawet dla kont, które są wyłączone, nadal istnieje sourceAnchor.

![Przychodzące reguły synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Pierwszeństwo dla reguł synchronizacji jest ustawiane w grupach przez kreatora instalacji. Wszystkie reguły w grupie mają taką samą nazwę, ale są połączone z różnymi połączonymi katalogami. Kreator instalacji daje regułę **w od usługi AD — użytkownik dołącz** najwyższy priorytet i iteruje nad wszystkimi połączonymi katalogami usługi AD. Następnie kontynuuje kolejne grupy reguł w wstępnie zdefiniowanej kolejności. Wewnątrz grupy reguły są dodawane w kolejności łączniki zostały dodane w kreatorze. Jeśli inny łącznik zostanie dodany za pośrednictwem kreatora, reguły synchronizacji zostaną ponownie zamówione, a reguły nowego łącznika zostaną wstawione jako ostatnie w każdej grupie.

### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Wiemy już wystarczająco dużo o reguły synchronizacji, aby móc zrozumieć, jak konfiguracja działa z różnych reguł synchronizacji. Jeśli spojrzysz na użytkownika i atrybuty, które są współtworzone do metaverse, reguły są stosowane w następującej kolejności:

| Nazwa | Komentarz |
|:--- |:--- |
| W od ad — dołączanie do użytkownika |Reguła łączenia obiektów przestrzeni łącznika z metaverse. |
| In from AD – UserAccount Enabled |Atrybuty wymagane do logowania się do usługi Azure AD i usługi Office 365. Chcemy, aby te atrybuty z włączonego konta. |
| In from AD — użytkownik wspólny z programu Exchange |Atrybuty znalezione na globalnej liście adresów. Zakładamy, że jakość danych jest najlepsza w lesie, w którym znaleźliśmy skrzynkę pocztową użytkownika. |
| W od AD – Często użytkownik |Atrybuty znalezione na globalnej liście adresów. W przypadku, gdy nie znaleźliśmy skrzynki pocztowej, każdy inny połączony obiekt może przyczynić się do wartości atrybutu. |
| In from AD — User Exchange |Istnieje tylko wtedy, gdy program Exchange został wykryty. Przepływa wszystkie atrybuty programu Exchange infrastruktury. |
| W usłudze AD — usługa User Lync |Istnieje tylko wtedy, gdy program Lync został wykryty. Przepływa wszystkie atrybuty programu Lync infrastruktury. |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o modelu konfiguracji w [opisie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [opisie wyrażeń deklaratywnej inicjowania obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Kontynuuj czytanie, jak działa konfiguracja out-of-box w [opisie użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md)
* Zobacz, jak wprowadzić praktyczną zmianę za pomocą deklaratywnych inicjowania obsługi administracyjnej w [jak wprowadzić zmiany w konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).

**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

