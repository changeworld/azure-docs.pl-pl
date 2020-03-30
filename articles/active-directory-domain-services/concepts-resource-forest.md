---
title: Pojęcia dotyczące lasu zasobów dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, co znajduje się w lesie zasobów w usługach domenowych Active Directory platformy Azure i jak przynoszą one korzyści organizacji w środowisku hybrydowym z ograniczonymi opcjami uwierzytelniania użytkowników lub problemami z zabezpieczeniami.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233612"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Pojęcia i funkcje lasu zasobów dla Usług domenowych Active Directory platformy Azure

Usługi domenowe Active Directory (AD DS) platformy Azure zapewniają środowisko logowania dla starszych, lokalnych aplikacji biznesowych. Użytkownicy, grupy i skróty haseł użytkowników lokalnych i chmurowych są synchronizowane z domeną zarządzaną usługą Azure AD DS. Te zsynchronizowane skróty haseł są tym, co daje użytkownikom jeden zestaw poświadczeń, których mogą używać w lokalnych usługach AD DS, usłudze Office 365 i usłudze Azure Active Directory.

Chociaż bezpieczne i zapewnia dodatkowe korzyści zabezpieczeń, niektóre organizacje nie można zsynchronizować te skróty haseł użytkownika do usługi Azure AD lub usługi Azure AD DS. Użytkownicy w organizacji mogą nie znać swojego hasła, ponieważ używają tylko uwierzytelniania kartą inteligentną. Te ograniczenia uniemożliwiają niektórym organizacjom korzystanie z usług Azure AD DS w celu przenoszenia i przenoszenia lokalnych aplikacji klasycznych na platformę Azure.

Aby zaspokoić te potrzeby i ograniczenia, można utworzyć domenę zarządzaną usługą Azure AD DS, która używa lasu zasobów. W tym artykule koncepcyjnym wyjaśniono, czym są lasy i jak ufają innym zasobom w celu zapewnienia bezpiecznej metody uwierzytelniania. Lasy zasobów usług Azure AD DS są obecnie w wersji zapoznawczej.

> [!IMPORTANT]
> Lasy zasobów usługi Azure AD DS obecnie nie obsługują usługi Azure HDInsight lub usługi Azure Files. Domyślne lasy użytkowników usług Azure AD DS obsługują obie te dodatkowe usługi.

## <a name="what-are-forests"></a>Czym są lasy?

*Las* jest konstrukcją logiczną używaną przez Usługi domenowe Active Directory (AD DS) do grupowania jednej lub kilku *domen*. Domeny następnie przechowywać obiekty dla użytkowników lub grup i świadczenia usług uwierzytelniania.

W usłudze Azure AD DS las zawiera tylko jedną domenę. Lokalne lasy usług AD DS często zawierają wiele domen. W dużych organizacjach, zwłaszcza po fuzjach i przejęciach, może się to skończyć z wieloma lasami lokalnymi, z których każda zawiera wiele domen.

Domyślnie domena zarządzana usługą Azure AD DS jest tworzona jako las *użytkownika.* Ten typ lasu synchronizuje wszystkie obiekty z usługi Azure AD, w tym wszystkie konta użytkowników utworzone w lokalnym środowisku usług AD DS. Konta użytkowników można bezpośrednio uwierzytelniać względem domeny zarządzanej usług Azure AD DS, na przykład do logowania się do maszyny Wirtualnej przyłączone do domeny. Las użytkownika działa, gdy skróty haseł mogą być synchronizowane, a użytkownicy nie używają wyłącznych metod logowania, takich jak uwierzytelnianie za pomocą karty inteligentnej.

W lesie *zasobów usług* Azure AD DS użytkownicy uwierzytelniają się za pomocą jednokierunkowego *zaufania* lasu z lokalnych usług AD DS. Dzięki takiemu podejściu obiekty użytkownika i skróty haseł nie są synchronizowane z usługą Azure AD DS. Obiekty użytkownika i poświadczenia istnieją tylko w lokalnych usługach AD DS. Takie podejście umożliwia przedsiębiorstwom hostować zasoby i platformy aplikacji na platformie Azure, które zależą od uwierzytelniania klasycznego, takiego jak LDAPS, Kerberos lub NTLM, ale wszelkie problemy z uwierzytelnianiem lub problemy są usuwane. Lasy zasobów usług Azure AD DS są obecnie w wersji zapoznawczej.

Lasy zasobów zapewniają również możliwość podnoszenia i przesuwania aplikacji po jednym składniku naraz. Wiele starszych aplikacji lokalnych jest wielowarstwowych, często przy użyciu serwera sieci web lub frontonu i wielu składników związanych z bazą danych. Te warstwy utrudniają podnoszenie i przenoszenie całej aplikacji do chmury w jednym kroku. Dzięki lasom zasobów można podnieść aplikację do chmury w podejściu etapowym, co ułatwia przenoszenie aplikacji na platformę Azure.

## <a name="what-are-trusts"></a>Co to są trusty?

Organizacje, które mają więcej niż jedną domenę, często potrzebują użytkowników do uzyskiwania dostępu do zasobów udostępnionych w innej domenie. Dostęp do tych zasobów udostępnionych wymaga, aby użytkownicy w jednej domenie uwierzytelniali się w innej domenie. Aby zapewnić te możliwości uwierzytelniania i autoryzacji między klientami i serwerami w różnych domenach, musi istnieć *zaufanie* między dwiema domenami.

W przypadku relacji zaufania domeny mechanizmy uwierzytelniania dla każdej domeny ufają uwierzytelnianiu pochodzącym z innej domeny. Relacje zaufania pomagają zapewnić kontrolowany dostęp do zasobów udostępnionych w domenie zasobów (domenie *ufającej),* sprawdzając, czy przychodzące żądania uwierzytelniania pochodzą od zaufanego urzędu (domeny *zaufanej).* Relacje zaufania działają jako mosty, które zezwalają tylko na przesyłanie zweryfikowanych żądań uwierzytelniania między domenami.

Sposób, w jaki zaufanie przekazuje żądania uwierzytelniania, zależy od sposobu jego skonfigurowania. Relacje zaufania można skonfigurować w jeden z następujących sposobów:

* **Jednokierunkowo** — zapewnia dostęp z domeny zaufanej do zasobów w domenie ufającej.
* **Dwukierunkowy** — zapewnia dostęp z każdej domeny do zasobów w innej domenie.

Relacje zaufania są również skonfigurowane do obsługi dodatkowych relacji zaufania w jeden z następujących sposobów:

* **Nieprzechodne** — zaufanie istnieje tylko między dwiema domenami partnerów zaufania.
* **Przechodnie** — zaufanie automatycznie rozciąga się na inne domeny, którym ufa jeden z partnerów.

W niektórych przypadkach relacje zaufania są ustanawiane automatycznie podczas tworzenia domen. Innym razem należy wybrać rodzaj zaufania i jawnie ustanowić odpowiednie relacje. Określone typy używanych relacji zaufania i struktura tych relacji zaufania zależą od organizacji usługi katalogowej Active Directory i od tego, czy różne wersje systemu Windows współistnieją w sieci.

## <a name="trusts-between-two-forests"></a>Relacje zaufania między dwoma lasami

Relacje zaufania domeny w obrębie jednego lasu można rozszerzyć do innego lasu, ręcznie tworząc jednokierunkowe lub dwukierunkowe zaufanie lasu. Zaufanie lasu to przechodnie zaufanie, które istnieje tylko między domeną główną lasu a drugą domeną katalogu głównego lasu.

* Jednokierunkowe zaufanie lasu umożliwia wszystkim użytkownikom w jednym lesie ufanie wszystkim domenom w drugim lesie.
* Dwukierunkowe zaufanie lasu tworzy przechodnią relację zaufania między każdą domeną w obu lasach.

Przechodnia zaufania lasów jest ograniczona do dwóch partnerów leśnych. Zaufanie lasu nie obejmuje dodatkowych lasów zaufanych przez jednego z partnerów.

![Diagram zaufania lasu od usług Azure AD DS do lokalnych usług AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

W zależności od struktury usługi Active Directory w organizacji można tworzyć różne konfiguracje zaufania domeny i lasu. Usługa Azure AD DS obsługuje tylko jednokierunkowe zaufanie lasu. W tej konfiguracji zasoby w usługach Azure AD DS mogą ufać wszystkim domenom w lesie lokalnym.

## <a name="supporting-technology-for-trusts"></a>Wspieranie technologii zaufania

Relacje zaufania używają różnych usług i funkcji, takich jak DNS, aby zlokalizować kontrolery domeny w lasach partnerskich. Relacje zaufania zależą również od protokołów uwierzytelniania NTLM i Protokołu Kerberos oraz od mechanizmów autoryzacji i kontroli dostępu opartych na systemie Windows, aby zapewnić bezpieczną infrastrukturę komunikacyjną w domenach i lasach usługi Active Directory. Następujące usługi i funkcje pomagają obsługiwać pomyślne relacje zaufania.

### <a name="dns"></a>DNS

Usługi AD DS wymagają systemu DNS dla lokalizacji i nazewnictwa kontrolera domeny(DC). Następująca obsługa systemu DNS jest dostępna dla usług AD DS, aby działać pomyślnie:

* Usługa rozpoznawania nazw, która umożliwia hostom sieciowym i usługom lokalizowanie kontrolerów domeny.
* Struktura nazewnictwa, która umożliwia przedsiębiorstwu odzwierciedlanie jego struktury organizacyjnej w nazwach domen usług katalogowych.

Zwykle wdrażany jest obszar nazw domeny DNS, który odzwierciedla obszar nazw domeny usług AD DS. Jeśli przed wdrożeniem usług AD DS istnieje istniejący obszar nazw DNS, obszar nazw DNS jest zazwyczaj podzielony na partycje dla usługi Active Directory i tworzony jest poddomenę DNS i delegowanie katalogu głównego lasu usługi Active Directory. Dodatkowe nazwy domen DNS są następnie dodawane dla każdej domeny podrzędnej usługi Active Directory.

Usługa DNS jest również używana do obsługi lokalizacji kontrolerów domeny usługi Active Directory. Strefy DNS są wypełniane rekordami zasobów DNS, które umożliwiają hostom sieciowym i usługom lokalizowanie kontrolerów domeny usługi Active Directory.

### <a name="applications-and-net-logon"></a>Aplikacje i logowanie do sieci

Zarówno aplikacje, jak i usługa Net Logon są składnikami modelu rozproszonego kanału zabezpieczeń systemu Windows. Aplikacje zintegrowane z systemami Windows Server i usługą Active Directory używają protokołów uwierzytelniania do komunikowania się z usługą Net Logon, dzięki czemu można ustanowić bezpieczną ścieżkę, nad którą może nastąpić uwierzytelnianie.

### <a name="authentication-protocols"></a>Protokoły uwierzytelniania

Kontrolery domeny usługi Active Directory uwierzytelniają użytkowników i aplikacje przy użyciu jednego z następujących protokołów:

* **Protokół uwierzytelniania Kerberos w wersji 5**
    * Protokół Kerberos w wersji 5 jest domyślnym protokołem uwierzytelniania używanym przez komputery lokalne z systemem Windows i obsługujący systemy operacyjne innych firm. Ten protokół jest określony w RFC 1510 i jest w pełni zintegrowany z usługą Active Directory, blokiem komunikatów serwera (SMB), protokołem HTTP i zdalnym wywołaniem procedury (RPC), a także z aplikacjami klienckimi i serwerowymi korzystającymi z tych protokołów.
    * Gdy używany jest protokół Kerberos, serwer nie musi kontaktować się z kontrolerem domeny. Zamiast tego klient pobiera bilet na serwer, żądając jednego z kontrolera domeny w domenie konta serwera. Następnie serwer sprawdza poprawność biletu bez konsultacji z innymi organami.
    * Jeśli jakikolwiek komputer biorący udział w transakcji nie obsługuje protokołu Kerberos w wersji 5, używany jest protokół NTLM.

* **Protokół uwierzytelniania NTLM**
    * Protokół NTLM to klasyczny protokół uwierzytelniania sieciowego używany przez starsze systemy operacyjne. Ze względu na zgodność jest on używany przez domeny usługi Active Directory do przetwarzania żądań uwierzytelniania sieciowego pochodzących z aplikacji przeznaczonych dla wcześniejszych klientów i serwerów z systemem Windows oraz systemów operacyjnych innych firm.
    * Gdy protokół NTLM jest używany między klientem a serwerem, serwer musi skontaktować się z usługą uwierzytelniania domeny na kontrolerze domeny, aby zweryfikować poświadczenia klienta. Serwer uwierzytelnia klienta, przesyłając dalej poświadczenia klienta do kontrolera domeny w domenie konta klienta.
    * Gdy dwie domeny lub lasy usługi Active Directory są połączone przez zaufanie, żądania uwierzytelniania wykonane przy użyciu tych protokołów mogą być kierowane w celu zapewnienia dostępu do zasobów w obu lasach.

## <a name="authorization-and-access-control"></a>Autoryzacja i kontrola dostępu

Technologie autoryzacji i zaufania współpracują ze sobą w celu zapewnienia zabezpieczonej infrastruktury komunikacyjnej w domenach lub lasach usługi Active Directory. Autoryzacja określa, jaki poziom dostępu użytkownik ma do zasobów w domenie. Relacje zaufania ułatwiają autoryzację między domenami użytkowników, udostępniając ścieżkę uwierzytelniania użytkowników w innych domenach, dzięki czemu ich żądania do zasobów udostępnionych w tych domenach mogą być autoryzowane.

Gdy żądanie uwierzytelnienia wykonane w domenie ufającej jest sprawdzane przez domenę zaufaną, jest przekazywane do zasobu docelowego. Zasób docelowy określa następnie, czy zezwolić na określone żądanie wykonane przez użytkownika, usługę lub komputer w domenie zaufanej na podstawie jego konfiguracji kontroli dostępu.

Relacje zaufania zapewniają ten mechanizm sprawdzania poprawności żądań uwierzytelniania, które są przekazywane do domeny ufającej. Mechanizmy kontroli dostępu na komputerze zasobów określają ostateczny poziom dostępu przyznanego żądaczowi w domenie zaufanej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaufaniach, zobacz [Jak działają relacje zaufania lasu w usługach Azure AD DS?][concepts-trust]

Aby rozpocząć tworzenie domeny zarządzanej usług Azure AD DS z lasem zasobów, zobacz [Tworzenie i konfigurowanie domeny zarządzanej usług Azure AD DS][tutorial-create-advanced]. Następnie można [utworzyć wychodzące zaufanie lasu do domeny lokalnej (wersja zapoznawcza)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
