---
title: Zrozumienie tożsamość platformy Azure | Dokumentacja firmy Microsoft
description: Uzyskaj podstawową wiedzę na temat warunków rozwiązania tożsamości Microsoft Azure, pojęcia i zalecenia dotyczące najlepszych decyzji ładu tożsamości dla Twojej organizacji.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: bd8e122324ab2d4c783fb6d4e09a9f4f197f91ca
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="understand-azure-identity-solutions"></a>Zrozumienie Azure tożsamościach
Microsoft Azure Active Directory (Azure AD) to rozwiązanie chmury zarządzania tożsamościami i dostępem, który udostępnia usługi katalogowe, Zarządzanie tożsamościami i zarządzania dostępem do aplikacji. Usługi Azure AD szybko [umożliwia logowanie jednokrotne (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) do 1 000 na wstępnie zintegrowanych aplikacji handlowych i niestandardowych w [galerii aplikacji Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Wiele z tych aplikacji prawdopodobnie już używasz usługi Office 365, witryny Salesforce.com, pola, usługi ServiceNow i produktu Workday.

Jeden katalog usługi Azure AD jest automatycznie kojarzony z subskrypcją platformy Azure, podczas jego tworzenia. Jako usługa tożsamości na platformie Azure następnie usługi Azure AD zapewnia wszystkie Zarządzanie tożsamościami i funkcji kontroli dostępu do zasobów w chmurze. Tych zasobów może zawierać użytkowników, aplikacji i grup dla poszczególnych dzierżawców (organizacja), jak pokazano na poniższym diagramie:

![Usługa Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure oferuje kilka sposobów korzystania z tożsamości jako usługa (IDaaS) o różnej złożoności, aby spełnić potrzeby poszczególnych organizacji. Dalszej części tego artykułu pomaga w zrozumieniu terminologii podstawowe tożsamość platformy Azure i pojęć, a także zalecenia dotyczące najlepszych wyboru z dostępnych opcji.

## <a name="terms-to-know"></a>Warunki wiedzieć

Przed podjęciem decyzji na rozwiązanie tożsamość platformy Azure dla organizacji, należy podstawową wiedzę na temat terminów używanych w przypadku usług Azure tożsamości.

|Termin wiedzieć| Opis|
|-----|-----|
|Subskrypcja platformy Azure |Subskrypcje są używane w celu opłacenia usług w chmurze Azure i zwykle są połączone z karty kredytowej. Może mieć kilka subskrypcji, ale może być trudne do udostępniania zasobów między subskrypcjami.|
|Dzierżawą platformy Azure | Dzierżawa usługi Azure AD jest reprezentatywna dla pojedynczej organizacji. Jest dedykowany zaufanych wystąpienie usługi Azure AD, który jest automatycznie tworzony, gdy organizacja rejestruje się w subskrypcji usługi chmury firmy Microsoft, takich jak Azure, Intune lub usługi Office 365. Dzierżawcy mogą uzyskać dostęp do usług w dedykowanym środowisku (pojedynczej dzierżawy) lub współużytkowanego środowiska z innymi organizacjami (wielodostępnej).|
|Katalog usługi Azure AD | Poszczególne dzierżawy Azure zawierają Azure dedykowanym, zaufany katalog AD, który zawiera użytkowników, grup i aplikacji dzierżawcy. Służy do wykonywania tożsamości i dostępu do funkcji zarządzania dla zasobów dzierżawy. Ponieważ unikatowego katalogu usługi Azure AD jest udostępniany automatycznie do reprezentowania organizacji po utworzeniu konta dla usługi w chmurze Microsoft Azure, Microsoft Intune lub usługi Office 365, zobaczysz czasami warunki *dzierżawy*,  *Usługi Azure AD*, i *katalog usługi Azure AD* używane zamiennie. |
|Domena niestandardowa | Po utworzeniu najpierw konta dla subskrypcji usługi Microsoft cloud, dzierżawy (organizacja) używa *. onmicrosoft.com* nazwy domeny. Jednak większość organizacji ma jedną lub więcej nazw domen, które są używane do działalności biznesowej i przez użytkowników końcowych umożliwia dostęp do zasobów firmy. Można dodać niestandardową nazwę domeny do usługi Azure AD, aby nazwa domeny jest znajome dla użytkowników, takich jak *alice@contoso.com* zamiast *alice@contoso.onmicrosoft.com*. |
|Konto Azure AD | Są to tożsamości, które są tworzone za pomocą usługi Azure AD lub innej usługi chmury firmy Microsoft, takich jak Office 365. Są one przechowywane w usłudze Azure AD i dostępny dla żadnego z subskrypcji usługi w chmurze w organizacji. |
|Administratorem subskrypcji platformy Azure| Konto administratora jest osobą, która utworzył konto na lub zakupiono subskrypcję platformy Azure. Mogą używać [Centrum konta](https://account.azure.com/Subscriptions) do wykonywania różnych zadań zarządzania, takich jak tworzyć subskrypcje, anulowanie subskrypcji, zmienić rozliczeń dla subskrypcji lub zmienić administratora usługi. |
|Administrator globalny usługi Azure AD | Administratorzy globalni usługi Azure AD mają pełny dostęp do wszystkich funkcji administracyjnych usługi Azure AD. Osoba, która zarejestruje się w subskrypcji usługi Microsoft cloud automatycznie staje się administratorem globalnym domyślnie. Może mieć więcej niż jednego administratora globalnego, ale wyłącznie administratorzy globalni mogą przypisywać każdą z [innych ról administratora](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) dla użytkowników. |
|Konto Microsoft | Konta Microsoft (utworzone przez Ciebie do użytku osobistego) umożliwiają dostęp do konsumentów produktów firmy Microsoft i usługami w chmurze, takich jak Outlook (usługi Hotmail), OneDrive, Xbox LIVE lub usługi Office 365. Te tożsamości są tworzone i przechowywane w Microsoft konta system obsługi tożsamości konsumentów firmy Microsoft.|
|Konta służbowe | Konta służbowe (wystawiony przez administratora dla firm/uczelni Użyj) zapewniają dostęp do przedsiębiorstwa biznesowej usług chmurowych firmy Microsoft, takich jak Azure, Intune lub usługi Office 365.|


## <a name="concepts-to-understand"></a>Pojęć

Teraz, znając postanowienia podstawowe tożsamość platformy Azure można należy dowiedzieć się więcej o tych pojęć tożsamość platformy Azure, które ułatwiają podjęcie decyzji usługi świadomych tożsamość platformy Azure.

|Koncepcja zrozumienie |Opis|
|-----|-----|
|[Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Każda subskrypcja platformy Azure ma relację zaufania z katalogiem Azure AD do uwierzytelniania użytkowników, usług i urządzeń. *Wiele subskrypcji może ufać ten sam katalog usługi Azure AD, ale subskrypcji będą ufać tylko jeden katalog usługi Azure AD*. Relacja zaufania między różni się od relacji, która subskrypcji z innych zasobów platformy Azure (witrynami sieci Web, baz danych i tak dalej), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do zasobów związanych z subskrypcji innych niż Azure AD również zatrzymywany. Jednak katalog usługi Azure AD pozostanie na platformie Azure, dzięki czemu można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie zasoby dzierżawcy.|
|[Jak Azure AD licencjonowania działania](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Katalog materiałów szkoleniowych Enterprise Mobility Suite, Azure AD Premium lub usługi Azure AD podstawowa, zostaje zaktualizowany z subskrypcją, w tym jego okresu ważności i przedpłaty licencji. Gdy subskrypcja jest aktywna, można zarządza Administratorzy globalni usługi Azure AD i używane przez licencjonowani użytkownicy usługi. Informacje o Twojej subskrypcji, wraz z liczbą przydzielonych lub dostępnych licencji, jest dostępna w portalu Azure z **usługi Azure Active Directory** > **licencji** bloku. Jest to również najlepszym miejscem, aby zarządzać przypisaniami licencji.|
|[Oparta na rolach kontrola dostępu w portalu Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)|Azure opartej na rolach kontroli dostępu (RBAC) zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure. Zbyt wiele uprawnienia można ujawnić i konta dla atakujących. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. Przy użyciu funkcji RBAC, zapewnić pracownikom dokładne uprawnienia, których potrzebują oparte na trzy podstawowe role, które są stosowane do wszystkich grup zasobów: właściciela, współautora, czytnika. Można również utworzyć maksymalnie 2000 własny [niestandardowe role RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) do konkretnych potrzeb. |
|[Tożsamość hybrydowa](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Tożsamość hybrydowa jest osiągane dzięki integracji z lokalnymi Windows Server Active Directory (AD DS) przy użyciu usługi Azure AD [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Dzięki temu można posługiwać się wspólną tożsamością dla użytkowników usługi Office 365, Azure i aplikacjami lokalnymi lub aplikacji SaaS zintegrowanych z usługą Azure AD. Tożsamość hybrydowa można skutecznie rozszerzanie środowiska lokalnego do chmury na potrzeby tożsamościami i dostępem.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Różnica między systemu Windows Server AD DS i Azure AD
Zarówno usługi Azure Active Directory (Azure AD) i lokalnej usługi Active Directory (usługi domenowe Active Directory lub usług AD DS) są systemy, które przechowują dane katalogów i zarządzania komunikacją między użytkownikami i zasobów, w tym procesami logowania użytkowników, uwierzytelniania i wyszukiwania w katalogu.

Jeśli już znasz z lokalnego systemu Windows serwera usług domenowych Active Directory (AD DS), po raz pierwszy wprowadzone w systemie Windows 2000 Server, a następnie prawdopodobnie rozumiesz podstawowe pojęcia usługa tożsamości. Jednak jest również wziąć pod uwagę, że usługi Azure AD nie jest po prostu kontrolera domeny w chmurze. Jest to całkowicie nowa sposób zapewnienia tożsamości jako usługa (IDaaS) na platformie Azure, wymagającego całkowicie nowy sposób myśląc pełni obejmuje funkcje oparte na chmurze i chronić organizację przed współczesnych zagrożeń. 

Usługi AD DS to rola serwera w systemie Windows Server, co oznacza, że można wdrożyć na fizycznych lub maszynach wirtualnych. Ma strukturę hierarchiczną oparte na X.500. Lokalizowanie obiektów, może być interakcji z przy użyciu protokołu LDAP, a przede wszystkim używa protokołu Kerberos do uwierzytelniania używa DNS. Usługi Active Directory umożliwia jednostek organizacyjnych (OU) i obiektów zasad grupy (GPO) oprócz przyłączania komputerów do domeny i relacje zaufania są tworzone między domenami.

IT zabezpieczył ich granicznej zabezpieczeń dla lat za pomocą usług AD DS, ale przedsiębiorstw nowoczesny, obwodowej bez obsługi potrzeb tożsamości dla pracowników, klienci i partnerzy wymagają nowej płaszczyzny formantu. Usługi Azure AD jest tej płaszczyzny kontroli tożsamości. Zabezpieczeń został przeniesiony poza zaporą korporacyjną w chmurze, jeśli usługi Azure AD chroni dostępu i zasobów firmy, podając jedną wspólną tożsamość użytkowników (lokalnie lub w chmurze). Dzięki temu użytkownicy elastyczność bezpiecznego dostępu do aplikacji potrzebnych do pracy z prawie każdego urządzenia. Formanty ochrony bezproblemowe danych opartych na ryzyko, obsługiwane przez możliwości usługi machine learning szczegółowe raporty, są również pod warunkiem, że zabezpieczenia trzeba chronić dane firmowe.

Usługi Azure AD to usługa katalogowa publicznego wielu klientów, co oznacza, że w ramach usługi Azure AD można utworzyć dzierżawy dla serwerów w chmurze i aplikacje, takie jak usługi Office 365. Użytkownicy i grupy są tworzone w płaskiej strukturze bez jednostek organizacyjnych i obiektów zasad grupy. Uwierzytelnianie jest przeprowadzane za pośrednictwem protokołów, takich jak SAML, WS-Federation i OAuth. Istnieje możliwość zapytań usługi Azure AD, ale zamiast LDAP należy użyć interfejsu API REST wywołuje interfejs API Graph usługi AD. Te wszystkie pracy za pośrednictwem protokołu HTTP i HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Rozszerzyć możliwości zarządzania i zabezpieczeń usługi Office 365
Już używasz usługi Office 365? Twoje przekształcania cyfrowego można przyspieszyć przez rozszerzanie wbudowanych możliwości usługi Office 365 z usługą Azure AD można zabezpieczyć wszystkie zasoby, włączanie bezpiecznego wydajności dla całej pracowników. Gdy używasz usługi Azure AD, oprócz możliwości usługi Office 365, można zabezpieczyć portfela całej aplikacji z jedną tożsamość, która umożliwia logowanie jednokrotne dla wszystkich aplikacji. Możesz rozszerzyć możliwości dostępu warunkowego, nie tylko na podstawie lokalizacji stanu, ale użytkownika, urządzenia, aplikacji i również ryzyko. Możliwości usługi Multi-Factor authentication (MFA) podaj dodatkową ochronę, gdy zajdzie taka potrzeba. Będzie uzyskanie dodatkowych nadzoru uprawnień użytkownika i zapewnienia dostępu administracyjnego na żądanie, w czasie. Użytkownicy są bardziej wydajni i utworzyć mniej biletów pomocy technicznej dzięki użyciu funkcji samoobsługi, który usługa Azure AD zapewnia takie jak zresetować zapomniane hasła, żądania dostępu do aplikacji oraz tworzenie grup i zarządzanie nimi.

> [!TIP]
> Chcesz dowiedzieć się więcej na temat korzystania z usługi Office 365 zarządzania tożsamościami w usłudze Azure AD? [Pobierz Książka elektroniczna](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Tożsamości na platformie Microsoft Azure

Microsoft Azure oferuje kilka sposobów zarządzania tożsamościami użytkowników, czy są obsługiwane pełni lokalnymi, tylko w chmurze, lub nawet gdzieś w między. Możliwe opcje: Zrób usług AD DS (DIY) w Azure, Azure Active Directory (Azure AD), tożsamość hybrydowa i usługi domenowe Azure AD.

### <a name="do-it-yourself-diy-ad-ds"></a>Zrób (DIY) AD DS
Dla przedsiębiorstw, które wymagają niewielkie rozmiary w chmurze **Zrób usług AD DS (DIY)** na platformie Azure mogą być używane. Ta opcja obsługuje wiele scenariuszy systemu Windows Server AD DS, które dobrze nadają się do wdrażania maszyn wirtualnych (VM) na platformie Azure. Na przykład można utworzyć maszyny Wirtualnej platformy Azure jako kontroler domeny z systemem w odległych centrum danych, który jest podłączony do sieci zdalnej. Z tego miejsca maszyna wirtualna będzie mogła obsługiwać żądania uwierzytelniania od zdalnych użytkowników i poprawić wydajność uwierzytelniania. Ta opcja jest również odpowiednie w zastępstwie stosunkowo ekonomicznych do lokacji odzyskiwania po awarii w przeciwnym razie kosztowne, udostępniając niewielkiej liczby kontrolerów domeny i jednej sieci wirtualnych na platformie Azure. Ponadto może być konieczne wdrażanie aplikacji na platformie Azure, takich jak SharePoint, która wymaga usługi AD DS w systemie Windows Server, ale ma nie zależności w sieci lokalnej lub firmowych systemu Windows serwer usługi Active Directory. W takim przypadku można wdrożyć odizolowanym lesie na platformie Azure, aby spełnić wymagania farmy serwerów programu SharePoint. Również obsługiwane wdrożenia aplikacji sieciowych, które wymagają łączności z siecią lokalną i w lokalnej usłudze Active Directory.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Azure AD autonomiczny** jest całkowicie opartej na chmurze zarządzania tożsamościami i dostępem jako rozwiązaniem Service (IDaaS). Usługi Azure AD zapewnia rozbudowany zestaw funkcji do zarządzania użytkownikami i grupami. Ułatwia zabezpieczenie dostępu do aplikacji lokalnych i aplikacji w chmurze, w tym usług sieci Web firmy Microsoft, takich jak Office 365, oraz wielu aplikacji typu oprogramowanie jako usługa (SaaS, Software as a Service) firm innych niż Microsoft. Usługi Azure AD jest dostarczany w trzech wersjach: bezpłatne, Basic i Premium. Usługi Azure AD zwiększa wydajność organizacji i zwiększa zabezpieczeń poza zaporze obwodowej do nowego płaszczyzny kontroli chroniona przez uczenie maszynowe Azure i inne zaawansowane funkcje zabezpieczeń.

### <a name="hybrid-identity"></a>Tożsamość hybrydowa
Zamiast wybór między lokalnymi lub tożsamościach oparte na chmurze, wiele planowania do przodu dyrektorzy działu informatyki i firm, które zaczęli przewidywanie kierunek długoterminowe firmy rozszerzania ich katalogów lokalnych do chmury za pomocą **tożsamość hybrydowa** rozwiązania. Tożsamość hybrydowa uzyskać tożsamość prawdziwie globalna i rozwiązanie do zarządzania dostępu, które udostępnia bezpieczne i produktywności użytkowników aplikacji należy wykonać swoje zadania.

> [!TIP]
> Aby uzyskać więcej informacji jak dyrektorzy działu informatyki zostały wykonane usługi Azure Active Directory centralnej częścią strategii IT, Pobierz [dyrektorów przewodnik dotyczący usługi Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Usługi domenowe Azure AD** zawiera opcję oparte na chmurze, aby używać usług AD DS lekką kontrolkę konfiguracji maszyny Wirtualnej platformy Azure i sposób spełniają wymagania tożsamości lokalnej sieci projektowanie i testowanie aplikacji. Usługi domenowe Azure AD nie jest przeznaczona do podnoszenia i przesunięcia lokalnej infrastruktury usług AD DS na maszynach wirtualnych platformy Azure zarządzanych przez usługi domenowe Azure AD. Zamiast tego maszynach wirtualnych platformy Azure w domenach zarządzanych powinien służyć do obsługi programowania, testowania i przenoszenie aplikacji lokalnych, które wymagają metod uwierzytelniania usług AD DS do chmury.

## <a name="common-scenarios-and-recommendations"></a>Typowe scenariusze i zalecenia

Poniżej przedstawiono kilka typowych scenariuszy tożsamościami i dostępem zalecenia dotyczące Azure, która opcja tożsamości może być najbardziej odpowiednie dla każdego.

|Scenariusza z tożsamością| Zalecenie|
|-----|-----|
|Moja organizacja wprowadził dużych inwestycji w usłudze Active Directory lokalnego systemu Windows Server, ale chcemy rozszerzyć tożsamości w chmurze.| Najczęściej używanym rozwiązaniem tożsamość platformy Azure jest [tożsamość hybrydowa](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Jeśli zostały już wprowadzone inwestycji w lokalnej instalacji usług AD DS, można z łatwością rozszerzyć tożsamości w chmurze za pomocą usługi Azure AD Connect.|
|Moja firma powstał w chmurze i mamy nie inwestycji w lokalnych rozwiązaniach tożsamości.| [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest najlepszym wyborem umożliwiającym tylko w chmurze firmy bez lokalnego inwestycji.|
|Potrzebuję lekkie konfiguracji maszyny Wirtualnej platformy Azure i kontroli w celu spełnienia wymagań dotyczących tożsamości do tworzenia aplikacji i testowania lokalnymi.|[Usługi domenowe Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) jest dobrym rozwiązaniem, jeśli trzeba używać usług AD DS dla lekką kontrolkę konfiguracji maszyny Wirtualnej platformy Azure lub szukasz lub migracji aplikacji starszy, obsługujący katalogu lokalnego do chmury.|  
|Muszę obsługuje kilka maszyn wirtualnych na platformie Azure, ale mojej firmy jest nadal silnie zainwestowały w lokalnej Active Directory (AD DS).|Użyj [DIY usług AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) do użycia maszyn wirtualnych platformy Azure, gdy należy obsługiwać kilka maszyn wirtualnych i dużych inwestują usług AD DS lokalnymi. |

## <a name="where-can-i-learn-more"></a>Gdzie można dowiedzieć się więcej?
Będziemy mieć ogromne dużą zasobów online ułatwiające Dowiedz się wszystkiego o usłudze Azure AD. Poniżej przedstawiono listę artykułów dużą ułatwiających rozpoczęcie pracy:

* [Włączanie katalogu dla hybrydowego zarządzania za pomocą usługi Azure AD Connect](active-directory-aadconnect.md)
* [Dodatkowe zabezpieczenia kiedykolwiek połączonych świata](authentication/multi-factor-authentication.md)
* [Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Wprowadzenie do raportów usługi Azure AD](active-directory-reporting-getting-started.md)
* [Zarządzanie haseł z dowolnego miejsca](active-directory-passwords-update-your-own-password.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](active-directory-application-proxy-get-started.md)
* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)
* [Co to jest licencjonowania usługi Microsoft Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Jak odnajdywać niezatwierdzone aplikacje w chmurze używanych mojej organizacji](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Kolejne kroki

Znasz koncepcji tożsamość platformy Azure i dostępne opcje można użyć następujących zasobów, aby rozpocząć wdrażanie, wybrana opcja:

[Dowiedz się więcej o tożsamościach hybrydowe platformy Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Dowiedz się więcej w środowisku Azure koncepcji](https://aka.ms/aad-poc)

[Wdrażanie usługi Azure AD w środowisku produkcyjnym](https://aka.ms/aad-onboard)
