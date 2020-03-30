---
title: Jak działają relacje zaufania w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się więcej o tym, jak zaufanie do lasu działa z usługami domenowymi usługi Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 8b79e0fb24c15d2e9f16640e90d62f7df5c21f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233703"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Jak działają relacje zaufania dla lasów zasobów w usługach domenowych Usługi active directory platformy Azure

Usługi domenowe Active Directory (AD DS) zapewniają bezpieczeństwo w wielu domenach lub lasach za pośrednictwem relacji zaufania domeny i lasu. Zanim uwierzytelnianie może nastąpić między relacjami zaufania, system Windows musi najpierw sprawdzić, czy domena żądana przez użytkownika, komputer lub usługę ma relację zaufania z domeną konta żądającego.

Aby sprawdzić tę relację zaufania, system zabezpieczeń systemu Windows oblicza ścieżkę zaufania między kontrolerem domeny (DC) dla serwera, który odbiera żądanie, a kontrolerem domeny w domenie konta żądającego.

Mechanizmy kontroli dostępu dostarczane przez usługi AD DS i rozproszony model zabezpieczeń systemu Windows zapewniają środowisko do działania relacji zaufania domen i lasu. Aby te relacje zaufania działały poprawnie, każdy zasób lub komputer musi mieć ścieżkę bezpośredniego zaufania do kontrolera domeny w domenie, w której się znajduje.

Ścieżka zaufania jest implementowana przez usługę Net Logon przy użyciu uwierzytelnionego połączenia procedury zdalnej (RPC) z urzędem domeny zaufanej. Zabezpieczony kanał rozciąga się również na inne domeny usług AD DS za pośrednictwem relacji zaufania międzydomenowych. Ten zabezpieczony kanał służy do uzyskiwania i weryfikowania informacji o zabezpieczeniach, w tym identyfikatorów zabezpieczeń (IDENTYFIKATORY) dla użytkowników i grup.

## <a name="trust-relationship-flows"></a>Przepływy relacji zaufania

Przepływ zabezpieczonej komunikacji za pomocą relacji zaufania określa elastyczność zaufania. Sposób tworzenia lub konfigurowania zaufania określa, jak daleko komunikacja rozciąga się w obrębie lasów lub między lasami.

Przepływ komunikacji za pomocą relacji zaufania zależy od kierunku zaufania. Relacje zaufania mogą być jednokierunkowe lub dwukierunkowe i mogą być przechodnie lub nieprzejściowe.

Na poniższym diagramie pokazano, że wszystkie domeny w *drzewie 1* i *drzewie 2* mają domyślnie przechodnie relacje zaufania. W rezultacie użytkownicy w *drzewie 1* mogą uzyskiwać dostęp do zasobów w domenach w *drzewie 2,* a użytkownicy w *drzewie 1* mogą uzyskiwać dostęp do zasobów w *drzewie 2,* gdy odpowiednie uprawnienia są przypisane do zasobu.

![Diagram relacji zaufania między dwoma lasami](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Zaufanie jednokierunkowe i dwukierunkowe

Relacje zaufania umożliwiają dostęp do zasobów może być jednokierunkowy lub dwukierunkowy.

Zaufanie jednokierunkowe jest jednokierunkową ścieżką uwierzytelniania utworzoną między dwiema domenami. W jednokierunkowym zaufaniu między *domeną A* a *domeną B*użytkownicy w *domenie A* mogą uzyskiwać dostęp do zasobów w *domenie B*. Jednak użytkownicy w *domenie B* nie mogą uzyskać dostępu do zasobów w *domenie A*.

Niektóre relacje zaufania jednokierunkowe mogą być nieprzejściowe lub przechodnie w zależności od typu tworzonego zaufania.

W relacji zaufania dwukierunkowego *domena A* ufa *domenie B,* a *domena B* ufa *domenie A*. Ta konfiguracja oznacza, że żądania uwierzytelniania mogą być przekazywane między dwiema domenami w obu kierunkach. Niektóre relacje dwukierunkowe mogą być nieprzechodnie lub przechodnie w zależności od typu tworzonego zaufania.

Wszystkie relacje zaufania domeny w lesie usług AD DS są dwukierunkowymi, przechodnimi zaufaniemi. Po utworzeniu nowej domeny podrzędnej między nową domeną podrzędną a domeną nadrzędną jest automatycznie tworzona dwukierunkowa, przechodnia.

### <a name="transitive-and-non-transitive-trusts"></a>Zaufania przechodnie i nieprzejedtnicze

Przechodnie określa, czy zaufanie można rozszerzyć poza dwie domeny, z którymi został utworzony.

* Zaufanie przechodnie może służyć do rozszerzania relacji zaufania z innymi domenami.
* Nieprzejściowe zaufanie może służyć do odmowy relacji zaufania z innymi domenami.

Za każdym razem, gdy tworzysz nową domenę w lesie, dwukierunkowa, przechodnia relacja zaufania jest automatycznie tworzona między nową domeną a jej domeną nadrzędną. Jeśli domeny podrzędne są dodawane do nowej domeny, ścieżka zaufania przepływa w górę przez hierarchię domen, rozszerzając początkową ścieżkę zaufania utworzoną między nową domeną a jej domeną nadrzędną. Przechodnie relacje zaufania przepływają w górę przez drzewo domen w miarę jego tworzenia, tworząc przechodnie relacje zaufania między wszystkimi domenami w drzewie domen.

Żądania uwierzytelniania są zgodne z tymi ścieżkami zaufania, dzięki czemu konta z dowolnej domeny w lesie mogą być uwierzytelnione przez dowolną inną domenę w lesie. W przypadku jednego procesu logowania konta z odpowiednimi uprawnieniami mogą uzyskiwać dostęp do zasobów w dowolnej domenie w lesie.

## <a name="forest-trusts"></a>Relacje zaufania lasów

Relacje zaufania lasu ułatwiają zarządzanie posegmentową infrastrukturą usług AD DS i zapewniają dostęp do zasobów i innych obiektów w wielu lasach. Trusty leśne są przydatne dla usługodawców, firm poddawanych fuzjom lub przejęciom, ekstranetów biznesowych opartych na współpracy oraz firm poszukujących rozwiązania dla autonomii administracyjnej.

Korzystając z relacji zaufania lasu, można połączyć dwa różne lasy, tworząc jednokierunkową lub dwukierunkową przechodnią relację zaufania. Zaufanie lasu umożliwia administratorom łączenie dwóch lasów usług AD DS z jedną relacją zaufania w celu zapewnienia bezproblemowego uwierzytelniania i autoryzacji w lasach.

Zaufanie lasu można utworzyć tylko między domeną katalogu głównego lasu w jednym lesie a domeną główną lasu w innym lesie. Relacje zaufania lasu można tworzyć tylko między dwoma lasami i nie można ich niejawnie rozszerzyć na trzeci las. To zachowanie oznacza, że jeśli zaufanie lasu jest tworzone między *lasem 1* i *lasem 2,* a inne zaufanie lasu jest tworzone między *lasem 2* i *lasem 3,* *las 1* nie ma niejawnego zaufania z *lasem 3*.

Na poniższym diagramie przedstawiono dwie oddzielne relacje zaufania lasu między trzema lasami usług AD DS w jednej organizacji.

![Diagram of forest trusts relationships within a single organization](./media/concepts-forest-trust/forest-trusts.png)

Ta przykładowa konfiguracja zapewnia następujący dostęp:

* Użytkownicy w *lesie 2* mogą uzyskiwać dostęp do zasobów w dowolnej domenie w *lesie 1* lub *lesie 3*
* Użytkownicy w *lesie 3* mogą uzyskiwać dostęp do zasobów w dowolnej domenie w *lesie 2*
* Użytkownicy w *lesie 1* mogą uzyskiwać dostęp do zasobów w dowolnej domenie w *lesie 2*

Ta konfiguracja nie zezwala użytkownikom w *lesie 1* na dostęp do zasobów w *lesie 3* lub odwrotnie. Aby umożliwić użytkownikom zarówno w *lesie 1,* jak i *lesie 3* udostępnianie zasobów, między dwoma lasami należy utworzyć dwukierunkowe przechodnie zaufanie.

Jeśli między dwoma lasami tworzone jest jednokierunkowe zaufanie lasu, członkowie zaufanego lasu mogą korzystać z zasobów znajdujących się w lesie ufającym. Jednak zaufanie działa tylko w jednym kierunku.

Na przykład podczas jednokierunkowego zaufania lasu między *lasem 1* (las zaufany) a *lasem 2* (lasem ufającym):

* Członkowie *lasu 1* mogą uzyskać dostęp do zasobów znajdujących się w *lesie 2*.
* Członkowie *lasu 2* nie mogą uzyskać dostępu do zasobów znajdujących się w *lesie 1* przy użyciu tego samego zaufania.

> [!IMPORTANT]
> Las zasobów usług domenowych usługi Azure AD obsługuje tylko jednokierunkowe zaufanie lasu do lokalnej usługi Active Directory.

### <a name="forest-trust-requirements"></a>Wymagania dotyczące zaufania lasu

Przed utworzeniem relacji zaufania lasu należy sprawdzić, czy masz prawidłową infrastrukturę systemu nazw domen (DNS). Relacje zaufania lasu można tworzyć tylko wtedy, gdy dostępna jest jedna z następujących konfiguracji DNS:

* Jeden główny serwer DNS jest głównym serwerem DNS dla obu leśnych obszarów nazw DNS - strefa główna zawiera delegacje dla każdego z obszarów nazw DNS, a główne wskazówki wszystkich serwerów DNS obejmują główny serwer DNS.
* W przypadku braku udostępnionego głównego serwera DNS, a główne serwery DNS dla każdej leśnej przestrzeni nazw DNS używają usług przesyłania dalej dns dla każdej przestrzeni nazw DNS do rozsyłania kwerend nazw w innym obszarze nazw.

    > [!IMPORTANT]
    > Las zasobów usług domenowych usługi Azure AD musi używać tej konfiguracji DNS. Hostowanie obszaru nazw DNS innego niż obszar nazw DNS lasu zasobów nie jest funkcją usług domenowych usługi Azure AD Domain Services. Warunkowe usługi przesyłania dalej to właściwa konfiguracja.

* Tam, gdzie nie ma udostępnionego głównego serwera DNS, a główne serwery DNS dla każdego lasu DNS są używane strefy pomocnicze DNS są konfigurowane w każdej przestrzeni nazw DNS do kierowania kwerend dla nazw w innym obszarze nazw.

Aby utworzyć zaufanie lasu, musisz być członkiem grupy Administratorzy domeny (w domenie głównej lasu) lub grupy Administratorzy przedsiębiorstwa w usłudze Active Directory. Każdemu zaufaniu jest przypisywane hasło, które muszą znać administratorzy w obu lasach. Członkowie administratorów przedsiębiorstwa w obu lasach mogą tworzyć relacje zaufania w obu lasach jednocześnie, a w tym scenariuszu hasło, które jest kryptograficznie losowe, jest generowane automatycznie i zapisywane dla obu lasów.

Wychodzące zaufanie lasu dla usług domenowych usługi Azure AD jest tworzony w witrynie Azure portal. Nie należy ręcznie tworzyć zaufania z samej domeny zarządzanej. Przychodzące zaufanie lasu musi być skonfigurowane przez użytkownika z uprawnieniami wcześniej odnotowanymi w lokalnej usłudze Active Directory.

## <a name="trust-processes-and-interactions"></a>Procesy zaufania i interakcje

Wiele transakcji między domenami i między lasami zależy od relacji zaufania domeny lub lasu w celu wykonania różnych zadań. W tej sekcji opisano procesy i interakcje, które występują w miarę uzyskiwania dostępu do zasobów między zaufaniami, a odwołania uwierzytelniania są oceniane.

### <a name="overview-of-authentication-referral-processing"></a>Omówienie przetwarzania odwołań uwierzytelniania

Gdy żądanie uwierzytelniania jest odwoływane do domeny, kontroler domeny w tej domenie musi określić, czy istnieje relacja zaufania z domeną, z której pochodzi żądanie. Kierunek zaufania i czy zaufanie jest przechodnie lub nieprzechodni należy również określić, zanim uwierzytelnia użytkownika, aby uzyskać dostęp do zasobów w domenie. Proces uwierzytelniania, który występuje między domenami zaufanymi, różni się w zależności od używanego protokołu uwierzytelniania. Protokoły Kerberos V5 i NTLM przetwarzają odwołania w celu uwierzytelnienia do domeny w inny sposób

### <a name="kerberos-v5-referral-processing"></a>Przetwarzanie odwołań kerberos v5

Protokół uwierzytelniania Kerberos V5 jest zależny od usługi Net Logon na kontrolerach domeny w celu uzyskania informacji o uwierzytelnianiu klienta i autoryzacji. Protokół Kerberos łączy się z centrum dystrybucji kluczy online (KDC) i magazynem kont usługi Active Directory dla biletów sesji.

Protokół Kerberos używa również relacji zaufania do usług przyznawania biletów między obszarami (TGS) i sprawdzania poprawności certyfikatów atrybutów uprawnień (PACs) w bezpiecznym kanale. Protokół Kerberos wykonuje uwierzytelnianie między obszarami tylko w przypadku obszarów protokołu Kerberos, takich jak obszar MIT Kerberos, i nie musi wchodzić w interakcje z usługą Net Logon.

Jeśli klient używa protokołu Kerberos V5 do uwierzytelniania, żąda biletu do serwera w domenie docelowej z kontrolera domeny w domenie konta. Protokół KDC protokołu Kerberos działa jako zaufany pośrednik między klientem a serwerem i udostępnia klucz sesji, który umożliwia obu stronom wzajemne uwierzytelnianie. Jeśli domena docelowa różni się od bieżącej domeny, kedc następuje logiczny proces, aby ustalić, czy żądanie uwierzytelniania można skierować:

1. Czy bieżąca domena jest zaufana bezpośrednio przez żądaną domenę serwera?
    * Jeśli tak, wyślij klientowi odwołanie do żądanej domeny.
    * Jeśli nie, przejdź do następnego kroku.

2. Czy istnieje przechodnia relacja zaufania między bieżącą domeną a następną domeną na ścieżce zaufania?
    * Jeśli tak, wyślij klientowi odwołanie do następnej domeny na ścieżce zaufania.
    * Jeśli nie, wyślij klientowi wiadomość odmowy logowania.

### <a name="ntlm-referral-processing"></a>Przetwarzanie odwołań NTLM

Protokół uwierzytelniania NTLM jest zależny od usługi Net Logon na kontrolerach domeny w celu uzyskania informacji o uwierzytelnianiu i autoryzacji klienta. Ten protokół uwierzytelnia klientów, którzy nie używają uwierzytelniania Kerberos. Usługa NTLM używa relacji zaufania do przekazywania żądań uwierzytelniania między domenami.

Jeśli klient używa NTLM do uwierzytelniania, początkowe żądanie uwierzytelniania przechodzi bezpośrednio od klienta do serwera zasobów w domenie docelowej. Ten serwer tworzy wyzwanie, na które klient odpowiada. Następnie serwer wysyła odpowiedź użytkownika do kontrolera domeny w domenie konta komputera. Ten kontroler domeny sprawdza konto użytkownika pod względem bazy danych kont zabezpieczeń.

Jeśli konto nie istnieje w bazie danych, kontroler domeny określa, czy wykonać uwierzytelnianie przekazywane, przesłać dalej żądanie lub odrzucić żądanie przy użyciu następującej logiki:

1. Czy bieżąca domena ma bezpośrednią relację zaufania z domeną użytkownika?
    * Jeśli tak, kontroler domeny wysyła poświadczenia klienta do kontrolera domeny w domenie użytkownika w celu uwierzytelniania przekazywanego.
    * Jeśli nie, przejdź do następnego kroku.

2. Czy bieżąca domena ma przechodnią relację zaufania z domeną użytkownika?
    * Jeśli tak, przekaż żądanie uwierzytelnienia do następnej domeny w ścieżce zaufania. Ten kontroler domeny powtarza proces, sprawdzając poświadczenia użytkownika w bazie danych własnych kont zabezpieczeń.
    * Jeśli nie, wyślij klientowi wiadomość odmowy logowania.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Przetwarzanie żądań uwierzytelniania oparte na protokół Kerberos za pomocą relacji zaufania lasu

Gdy dwa lasy są połączone przez zaufanie lasu, żądania uwierzytelniania wykonane przy użyciu protokołów Kerberos V5 lub NTLM mogą być kierowane między lasami w celu zapewnienia dostępu do zasobów w obu lasach.

Gdy po raz pierwszy zostanie ustanowione zaufanie lasu, każdy las zbiera wszystkie zaufane przestrzenie nazw w lesie partnerskim i przechowuje informacje w [obiekcie domeny zaufanej](#trusted-domain-object). Zaufane przestrzenie nazw obejmują nazwy drzew domen, sufiksy głównej nazwy użytkownika (UPN), sufiksy głównej nazwy usługi (SPN) oraz obszary nazw identyfikatorów zabezpieczeń (SID) używane w innym lesie. Obiekty TDO są replikowane do wykazu globalnego.

Zanim protokoły uwierzytelniania będą mogły podążać ścieżką zaufania lasu, nazwa główna usługi (SPN) komputera zasobów musi zostać rozpoznana w lokalizacji w innym lesie. Nazwa SPN może być jedną z następujących czynności:

* Nazwa DNS hosta.
* Nazwa DNS domeny.
* Nazwa wyróżniająca obiektu punktu połączenia usługi.

Gdy stacja robocza w jednym lesie próbuje uzyskać dostęp do danych na komputerze zasobów w innym lesie, proces uwierzytelniania Kerberos kontaktuje się z kontrolerem domeny w celu uzyskania biletu usługi do nazwy SPN komputera zasobów. Gdy kontroler domeny wysyła zapytanie do wykazu globalnego i stwierdzi, że nazwa SPN nie znajduje się w tym samym lesie co kontroler domeny, kontroler domeny wysyła odwołanie do domeny nadrzędnej z powrotem na stację roboczą. W tym momencie stacja robocza wysyła zapytanie do domeny nadrzędnej dla biletu usługi i kontynuuje podążanie za łańcuchem odwołań, dopóki nie osiągnie domeny, w której znajduje się zasób.

Poniższy diagram i kroki zawierają szczegółowy opis procesu uwierzytelniania Kerberos, który jest używany, gdy komputery z systemem Windows próbują uzyskać dostęp do zasobów z komputera znajdującego się w innym lesie.

![Diagram procesu Kerberos nad zaufaniem lasu](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *Użytkownik1* loguje się do *stacji roboczej1* przy użyciu poświadczeń z *domeny europe.tailspintoys.com.* Następnie użytkownik próbuje uzyskać dostęp do zasobu udostępnionego na *serwerze FileServer1* znajdującym się w lesie *usa.wingtiptoys.com.*

2. *Stacja robocza1* kontaktuje się z kdc protokołu Kerberos na kontrolerze domeny w swojej domenie *ChildDC1*i żąda biletu usługi dla nazwy SPN *fileserver1.*

3. *Usługa ChildDC1* nie znajduje nazwy SPN w bazie danych domeny i wysyła zapytanie do wykazu globalnego, aby sprawdzić, czy wszystkie domeny w lesie *tailspintoys.com* zawierają tę nazwę SPN. Ponieważ wykaz globalny jest ograniczony do własnego lasu, nie można odnaleźć nazwy SPN.

    Następnie wykaz globalny sprawdza swoją bazę danych pod kątem informacji o wszelkich zaufaniach lasu, które są ustanawiane za pomocą lasu. Jeśli zostanie znaleziony, porównuje sufiksy nazw wymienione w obiekcie zaufana domena lasu (TDO) z sufiksem docelowej nazwy SPN, aby znaleźć dopasowanie. Po znalezieniu dopasowania wykaz globalny zawiera wskazówkę dotyczącą routingu z powrotem do *childdc1*.

    Wskazówki dotyczące routingu pomagają w kierowaniu żądaniami uwierzytelniania w kierunku lasu docelowego. Wskazówki są używane tylko wtedy, gdy wszystkie tradycyjne kanały uwierzytelniania, takie jak lokalny kontroler domeny, a następnie wykaz globalny, nie można zlokalizować nazwy SPN.

4. *ChildDC1* wysyła odwołanie do domeny nadrzędnej z powrotem do *Workstation1*.

5. *Stacja robocza1* kontaktuje się z kontrolerem domeny w *forestrootDC1* (jego domenie nadrzędnej) w celu odwołania do kontrolera domeny *(ForestRootDC2)* w domenie głównej lasu *wingtiptoys.com* lasu.

6. *Stacja robocza1* kontaktuje *forestrootDC2* w lesie *wingtiptoys.com* dla biletu usługi do żądanej usługi.

7. *ForestRootDC2* kontaktuje się z wykazem globalnym, aby znaleźć nazwę SPN, a wykaz globalny znajdzie dopasowanie dla nazwy SPN i wysyła go z powrotem do *forestrootDC2*.

8. *ForestRootDC2* następnie wysyła odwołanie do *usa.wingtiptoys.com* z powrotem do *Workstation1*.

9. *Workstation1* kontaktuje się z centrum dystrybucji danych nr 1 na *serwerze ChildDC2* i negocjuje bilet dla *użytkownika1* w celu uzyskania dostępu do *pliku FileServer1*.

10. Gdy *Workstation1* ma bilet usługi, wysyła bilet usługi do *FileServer1*, który odczytuje poświadczenia zabezpieczeń *Użytkownika1*i odpowiednio konstruuje token dostępu.

## <a name="trusted-domain-object"></a>Zaufany obiekt domeny

Każde zaufanie domeny lub lasu w organizacji jest reprezentowane przez obiekt domeny zaufanej (TDO) przechowywany w kontenerze *systemowym* w jego domenie.

### <a name="tdo-contents"></a>Zawartość TDO

Informacje zawarte w TDO różnią się w zależności od tego, czy TDO został utworzony przez zaufanie domeny lub przez zaufanie lasu.

Po utworzeniu zaufania domeny atrybuty, takie jak nazwa domeny DNS, identyfikator SID domeny, typ zaufania, przechodniość zaufania i wzajemna nazwa domeny są reprezentowane w tk. Urządzenia TDO zaufania lasu przechowują dodatkowe atrybuty w celu identyfikowania wszystkich zaufanych obszarów nazw z lasu partnera. Atrybuty te obejmują nazwy drzewa domen, sufiksy głównej nazwy użytkownika (UPN), sufiksy głównej nazwy usługi (SPN) i obszary nazw identyfikatorów zabezpieczeń (SID).

Ponieważ relacje zaufania są przechowywane w usłudze Active Directory jako urządzenia TDO, wszystkie domeny w lesie mają wiedzę na temat relacji zaufania, które są stosowane w całym lesie. Podobnie, gdy dwa lub więcej lasów są połączone ze sobą za pośrednictwem relacji zaufania lasu, domen głównych lasu w każdym lesie mają wiedzę na temat relacji zaufania, które są w miejscu we wszystkich domenach w zaufanych lasach.

### <a name="tdo-password-changes"></a>Zmiany hasła TDO

Obie domeny w relacji zaufania współużytkuje hasło, które jest przechowywane w obiekcie TDO w usłudze Active Directory. W ramach procesu konserwacji konta co 30 dni zaufany kontroler domeny zmienia hasło przechowywane w tk. Ponieważ wszystkie zaufania dwukierunkowe są w rzeczywistości dwa jednokierunkowe zaufania będzie w przeciwnych kierunkach, proces występuje dwa razy dla zaufania dwukierunkowe.

Zaufanie ma zaufaną i zaufaną stronę. Po stronie zaufanej do procesu można użyć dowolnego zapisywalnyego kontrolera domeny. Po stronie ufającej emulator kontrolera PDC wykonuje zmianę hasła.

Aby zmienić hasło, kontrolery domeny kończą następujący proces:

1. Emulator podstawowego kontrolera domeny (PDC) w domenie ufającej tworzy nowe hasło. Kontroler domeny w domenie zaufanej nigdy nie inicjuje zmiany hasła. Jest zawsze inicjowany przez emulator kontrolera PDC domeny ufającej.

2. Emulator kontrolera PDC w domenie ufającej ustawia pole *OldPassword* obiektu TDO na bieżące pole *NewPassword.*

3. Emulator kontrolera PDC w domenie ufającej ustawia pole *NewPassword* obiektu TDO na nowe hasło. Przechowywanie kopii poprzedniego hasła umożliwia powrót starego hasła, jeśli kontroler domeny w domenie zaufanej nie otrzyma zmiany lub jeśli zmiana nie zostanie zreplikowana przed złożeniem żądania, które używa nowego hasła zaufania.

4. Emulator kontrolera PDC w domenie ufającej wywołuje zdalnie do kontrolera domeny w zaufanej domenie z prośbą o ustawienie hasła na koncie powierniczym na nowe hasło.

5. Kontroler domeny w domenie zaufanej zmienia hasło zaufania na nowe hasło.

6. Po obu stronach zaufania aktualizacje są replikowane do innych kontrolerów domeny w domenie. W domenie ufającej zmiana wyzwala pilną replikację obiektu domeny zaufanej.

Hasło zostanie zmienione na obu kontrolerach domeny. Normalna replikacja dystrybuuje obiekty TDO do innych kontrolerów domeny w domenie. Jednak kontroler domeny w domenie ufającej może zmienić hasło bez pomyślnego aktualizowania kontrolera domeny w domenie zaufanej. W tym scenariuszu może wystąpić, ponieważ nie można ustanowić zabezpieczonego kanału, który jest wymagany do przetworzenia zmiany hasła. Jest również możliwe, że kontroler domeny w zaufanej domenie może być niedostępny w pewnym momencie procesu i może nie otrzymać zaktualizowanego hasła.

Aby poradzić sobie z sytuacjami, w których zmiana hasła nie została pomyślnie przekazana, kontroler domeny w domenie ufającej nigdy nie zmienia nowego hasła, chyba że pomyślnie uwierzytelnił (skonfigurował bezpieczny kanał) przy użyciu nowego hasła. To zachowanie jest, dlaczego zarówno stare, jak i nowe hasła są przechowywane w obiekcie TDO domeny ufającej.

Zmiana hasła nie zostanie sfinalizowana, dopóki uwierzytelnianie przy użyciu hasła nie powiedzie się. Stare, zapisane hasło może być używane przez bezpieczny kanał, dopóki kontroler domeny w zaufanej domenie nie otrzyma nowego hasła, umożliwiając w ten sposób nieprzerwaną obsługę.

Jeśli uwierzytelnianie przy użyciu nowego hasła nie powiedzie się, ponieważ hasło jest nieprawidłowe, ufający kontroler domeny próbuje uwierzytelnić się przy użyciu starego hasła. Jeśli pomyślnie uwierzytelnia się przy użyciu starego hasła, wznawia proces zmiany hasła w ciągu 15 minut.

Aktualizacje hasła zaufania muszą być replikowane do kontrolerów domeny obu stron zaufania w ciągu 30 dni. Jeśli hasło zaufania zostanie zmienione po 30 dniach, a kontroler domeny ma tylko hasło N-2, nie może używać zaufania od strony ufającej i nie może utworzyć bezpiecznego kanału po stronie zaufanej.

## <a name="network-ports-used-by-trusts"></a>Porty sieciowe używane przez relacje zaufania

Ponieważ relacje zaufania muszą być wdrażane w różnych granicach sieci, mogą one obejmować jedną lub więcej zapór. W takim przypadku można tunelować ruch zaufania przez zaporę lub otworzyć określone porty w zaporze, aby umożliwić przepuszczanie ruchu.

> [!IMPORTANT]
> Usługi domenowe Active Directory nie obsługują ograniczania ruchu RPC usługi Active Directory do określonych portów.

Przeczytaj sekcję **Windows Server 2008 i nowszych w** artykule Pomocy technicznej firmy Microsoft Jak [skonfigurować zaporę dla domen i relacji zaufania usługi Active Directory,](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) aby dowiedzieć się więcej o portach potrzebnych do obsługi lasu.

## <a name="supporting-services-and-tools"></a>Usługi i narzędzia pomocnicze

Do obsługi relacji zaufania i uwierzytelniania używane są dodatkowe funkcje i narzędzia do zarządzania.

### <a name="net-logon"></a>Netlogon

Usługa Net Logon utrzymuje bezpieczny kanał z komputera z systemem Windows do kontrolera domeny. Jest również używany w następujących procesach związanych z zaufaniem:

* Konfiguracja zaufania i zarządzanie — net logon pomaga w utrzymaniu haseł zaufania, zbiera informacje o zaufaniu i weryfikuje relacje zaufania poprzez interakcję z procesem LSA i TDO.

    W przypadku relacji zaufania lasu informacje o zaufaniu obejmują rekord Informacje o zaufaniu lasu *(FTInfo),* który zawiera zestaw obszarów nazw, którymi zaufany las twierdzi do zarządzania, o których adnotacja jest z adnotacjami o polu wskazującym, czy każde oświadczenie jest zaufane przez las ufający.

* Uwierzytelnianie — dostarcza poświadczenia użytkownika za pomocą zabezpieczonego kanału do kontrolera domeny i zwraca identyfikatory IDENTYFIKATORY DOMENY i prawa użytkownika.

* Lokalizacja kontrolera domeny — pomaga w znajdowaniu lub lokalizowaniu kontrolerów domeny w domenie lub w różnych domenach.

* Sprawdzanie poprawności przekazywania — poświadczenia użytkowników w innych domenach są przetwarzane przez net logon. Gdy domena ufająca musi zweryfikować tożsamość użytkownika, przekazuje poświadczenia użytkownika za pośrednictwem net logon do domeny zaufanej w celu weryfikacji.

* Weryfikacja certyfikatu atrybutu uprawnień (PAC) — gdy serwer używający protokołu Kerberos do uwierzytelniania musi zweryfikować pac w bilecie usługi, wysyła pac przez bezpieczny kanał do kontrolera domeny w celu weryfikacji.

### <a name="local-security-authority"></a>Urząd zabezpieczeń lokalnych

Urząd bezpieczeństwa lokalnego (LSA) jest chronionym podsystemem, który przechowuje informacje o wszystkich aspektach zabezpieczeń lokalnych w systemie. LSA, znane jako lokalne zasady zabezpieczeń, zapewnia różne usługi tłumaczenia między nazwami i identyfikatorami.

Podsystem zabezpieczeń LSA udostępnia usługi zarówno w trybie jądra, jak i w trybie użytkownika w celu sprawdzania poprawności dostępu do obiektów, sprawdzania uprawnień użytkownika i generowania komunikatów inspekcji. LSA jest odpowiedzialny za sprawdzenie ważności wszystkich biletów sesyjnych prezentowanych przez usługi w zaufanych lub niezaufanych domenach.

### <a name="management-tools"></a>Narzędzia do zarządzania

Administratorzy mogą używać *domen i relacji zaufania usługi Active Directory,* *Netdom* i *Nltest* do ujawniania, tworzenia, usuwania lub modyfikowania relacji zaufania.

* *Domeny i relacje zaufania usługi Active Directory* to program Microsoft Management Console (MMC), który jest używany do administrowania zaufaniem domeny, poziomami funkcjonalności domeny i lasu oraz sufiksami głównej nazwy użytkownika.
* Narzędzia wiersza polecenia *Netdom* i *Nltest* mogą być używane do znajdowania, wyświetlania, tworzenia i zarządzania zaufaniem. Narzędzia te komunikują się bezpośrednio z urzędem LSA na kontrolerze domeny.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o lasach zasobów, zobacz [Jak działają relacje zaufania lasów w usługach Azure AD DS?][concepts-trust]

Aby rozpocząć tworzenie domeny zarządzanej usług Azure AD DS z lasem zasobów, zobacz [Tworzenie i konfigurowanie domeny zarządzanej usług Azure AD DS][tutorial-create-advanced]. Następnie można [utworzyć wychodzące zaufanie lasu do domeny lokalnej (wersja zapoznawcza)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
