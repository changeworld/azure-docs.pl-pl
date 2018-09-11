---
title: Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory? | Microsoft Docs
description: Usługa Azure Active Directory, aby włączyć logowanie jednokrotne do wszystkich aplikacje sieci web i SaaS, które są potrzebne dla firm.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: d17b5967519bc51e4fb33a410149d17c82c28d10
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347991"
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?
Logowanie jednokrotne oznacza, że będzie mogła uzyskać dostęp do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko wtedy, gdy za pomocą jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji bez konieczności uwierzytelnienia (na przykład, wpisz hasło) po raz drugi.

Wiele organizacji korzystają z możliwości oprogramowania jako usługi (SaaS) aplikacje takie jak usługi Office 365, Box i Salesforce, który na produktywność użytkownika końcowego. W przeszłości personel IT musi indywidualnie tworzenia i aktualizowania kont użytkowników w każdej aplikacji SaaS, a użytkownicy muszą pamiętać hasła dla każdej aplikacji SaaS.

Usługa Azure Active Directory rozszerza w lokalnej usłudze Active Directory w chmurze, umożliwiając użytkownikom za pomocą ich podstawowego konta organizacyjnego nie tylko zalogować się do swoich urządzeń przyłączonych do domeny i zasobów firmy, ale również wszystkie sieci web i aplikacji SaaS niezbędny do swojej pracy.

Więc nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazwy użytkowników i hasła, aplikacjach dostępu mogą być automatycznie aprowizowane lub cofanie aprowizacji oparte na ich członków grupy w organizacji, a także ich stan jako pracownika. Usługa Azure Active Directory wprowadza zabezpieczeniami i dostępem formantów nadzoru, które pozwalają na centralnie zarządzać dostępem użytkowników w aplikacjach SaaS.

Usługa Azure AD umożliwia łatwą integrację z wieloma współczesnych popularne aplikacje SaaS; zapewnia zarządzanie tożsamościami i dostępem i umożliwia użytkownikom logowanie jednokrotne do aplikacji bezpośrednio lub odkrywanie ich i uruchamianie z portalu, takiego jak usługi Office 365 lub w panelu dostępu usługi Azure AD.

Architektura integracji składa się z następujących czterech głównych bloków konstrukcyjnych:

* Logowanie jednokrotne umożliwia użytkownikom dostęp do swoich aplikacji SaaS, w oparciu o swoje konta organizacyjne w usłudze Azure AD. Logowanie jednokrotne jest, co pozwala użytkownikom na uwierzytelnianie aplikacji przy użyciu jednego konta organizacji.
* Inicjowanie obsługi użytkowników umożliwia inicjowanie obsługi użytkowników i anulowania obsługi administracyjnej do obiektu docelowego, który SaaS na podstawie zmian wprowadzonych w systemie Windows Server Active Directory i/lub usługi Azure AD. Konto elastycznie to, co pozwala użytkownikowi uzyskanie autoryzacji do po uwierzytelnieniu za pomocą logowania jednokrotnego za pomocą aplikacji.
* Zarządzanie dostępem do aplikacji scentralizowany dostęp do aplikacji pojedynczy punkt SaaS umożliwia portalu Azure i zarządzania, z możliwością delegować uprawnienia do podejmowania decyzji na dostęp do aplikacji i zatwierdzenia do wszystkich osób w organizacji
* Jednolite raportowanie i monitorowania aktywności użytkownika w usłudze Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Jak działa logowanie jednokrotne z usługą Azure Active Directory?
Gdy użytkownicy logują się do aplikacji, przejściu przez proces uwierzytelniania gdzie są one wymagane, aby potwierdzić, że są one Załóżmy, że są one. Bez logowania jednokrotnego ten proces uwierzytelniania jest zazwyczaj wykonywane przez wprowadzenie hasła, które są przechowywane na poziomie aplikacji, a użytkownicy muszą znać hasło.

Usługa Azure AD obsługuje trzy różne sposoby, aby zalogować się do aplikacji:

* **Federacyjne logowanie jednokrotne** umożliwia aplikacjom przekierowanie do usługi Azure AD do uwierzytelniania użytkowników zamiast monitowania o własne hasło. Federacyjne logowanie jednokrotne jest obsługiwana w przypadku aplikacji, które obsługują protokoły, takie jak SAML 2.0, WS-Federation i OpenID Connect i jest najszerszym tryb logowania jednokrotnego.
* **Na podstawie hasła logowania jednokrotnego** umożliwia bezpieczne przechowywanie haseł aplikacji i oparte na metodzie powtórzeń przy użyciu rozszerzenia przeglądarki sieci web lub aplikacji mobilnej. Oparte na hasłach logowania jednokrotnego wykorzystuje istniejący proces udostępniany przez aplikację, ale umożliwia administratorowi Zarządzanie hasłami i wymaga aby użytkownik znał hasło.
* **Połączone logowanie jednokrotne** umożliwia usłudze Azure AD i wykorzystać wszystkie istniejące logowanie jednokrotne zostało skonfigurowane dla aplikacji, ale umożliwia te aplikacje, być połączone z portali panelu dostępu usługi Office 365 lub Azure AD i umożliwia również dodatkowe Raportowanie w usłudze Azure AD, gdy istnieje uruchomienia aplikacji.

Po użytkownik został uwierzytelniony przy użyciu aplikacji, muszą mieć rekord konta zainicjowane w aplikacji, która informuje aplikację, w których uprawnienia i poziom dostępu wewnątrz aplikacji. Aprowizacja ten rekord konta może mieć miejsce, automatycznie lub może być wykonywane ręcznie przez administratora przed dostępu rejestracji jednokrotnej jest podany przez użytkownika.

 Szczegółowe informacje na temat tych pojedynczego trybów logowania jednokrotnego i aprowizacji poniżej.

### <a name="federated-single-sign-on"></a>Federacyjne logowanie jednokrotne
Federacyjne logowanie jednokrotne umożliwia użytkownikom automatyczne logowanie do aplikacji SaaS innych firm przez usługę Azure AD, podając dane konta użytkownika z usługi Azure AD w organizacji.

W tym scenariuszu użytkownik ma już zalogowany w usłudze Azure AD, gdy chcesz uzyskać dostęp do zasobów, które są kontrolowane przez w przypadku aplikacji SaaS innych firm Federacji eliminuje potrzebę użytkownik może być ponownie uwierzytelniany.

Usługa Azure AD można obsługuje federacyjnego logowania jednokrotnego przy użyciu aplikacji obsługujących SAML 2.0, WS-Federation, lub OpenID connect protokołów.

Zobacz również: [zarządzaniu certyfikatami federacyjnego logowania jednokrotnego](manage-certificates-for-federated-single-sign-on.md)

### <a name="password-based-single-sign-on"></a>Logowanie jednokrotne oparte na hasłach
Konfigurowanie opartego na hasłach logowanie jednokrotne umożliwia użytkownikom automatyczne logowanie do aplikacji SaaS innych firm przez usługę Azure AD, podając dane konta użytkownika z aplikacji SaaS innych firm w organizacji. Po włączeniu tej funkcji usługi Azure AD zbiera i bezpiecznie przechowa Twoje informacje o koncie użytkownika i powiązane hasło.

Usługa Azure AD może obsługiwać opartego na hasłach logowanie jednokrotne dla dowolnej aplikacji opartej na chmurze, który jest oparty na języku HTML strony logowania. Za pomocą wtyczki przeglądarki niestandardowych, AAD automatyzuje proces logowania za pośrednictwem bezpiecznego pobierania poświadczeń aplikacji, takie jak nazwa użytkownika i hasło z katalogu i wprowadza te poświadczenia do strony logowania w aplikacji w imieniu użytkownika. Istnieją dwa przypadki użycia:

1. **Administrator zarządza poświadczeniami** — Administratorzy mogą tworzyć i zarządzać poświadczenia aplikacji i przypisz tych poświadczeń do użytkowników lub grupy, którzy potrzebują dostępu do aplikacji. W takich przypadkach użytkownik końcowy musi znać poświadczenia, ale nadal uzyskuje dostęp pojedynczego logowania jednokrotnego do aplikacji po prostu, klikając je w swoich panelach dostępu lub za pośrednictwem podany link. Ten proces umożliwia zarówno zarządzania cyklem życia poświadczeń przez administratora, a także wygodę dla użytkowników końcowych, zgodnie z którą nie muszą pamiętać lub zarządzać hasłami specyficzny dla aplikacji. Poświadczenia są zaciemniony plikach użytkownika końcowego podczas automatycznego logowania; jednak są one z technicznego punktu widzenia wykrywane przez użytkownika przy użyciu narzędzia do debugowania w sieci web, a użytkownikom i administratorom należy stosować te same zasady zabezpieczeń, tak, jakby poświadczenia zostały przedstawione bezpośrednio przez użytkownika. Poświadczenia administratora są przydatne, gdy zapewnianie dostępu do konta, która jest współużytkowana przez wielu użytkowników, takich jak mediów społecznościowych i aplikacji do udostępniania dokumentów.
2. **Użytkownik zarządza poświadczeniami** — Administratorzy mogą przypisywać aplikacje do użytkowników lub grup i zezwolić użytkownikom końcowym o wprowadzenie poświadczeń bezpośrednio na uzyskiwanie dostępu do aplikacji po raz pierwszy w swoich panelach dostępu. Spowoduje to utworzenie wygodę dla użytkowników końcowych, zgodnie z którą nie muszą ciągle wprowadzać haseł specyficzny dla aplikacji za każdym razem, mogą uzyskiwać dostęp do aplikacji. Użytkownicy mogą nadal Zarządzam ich hasłami, aktualizowanie i usuwanie ich odpowiednio do potrzeb. Ten przypadek użycia może również służyć jako kamień przechodzenia krok po kroku do zarządzanie administracyjne poświadczeń, według której administrator może ustawić nowe poświadczenia dla aplikacji w przyszłości bez konieczności zmieniania aplikacji środowisko dostępu użytkownika końcowego.

W obu przypadkach poświadczenia są przechowywane w stanie zaszyfrowane w katalogu i są tylko przekazywane za pośrednictwem protokołu HTTPS w trakcie zautomatyzowanego procesu logowania. Za pomocą opartego na hasłach logowanie jednokrotne, usługa Azure AD oferuje wygodny dostęp do rozwiązania do zarządzania tożsamościami dla aplikacji, które nie są w stanie obsługiwać protokoły federacji.

Usługa rejestracji Jednokrotnej z opartego na hasłach opiera się na rozszerzenia przeglądarki do bezpiecznego pobierania aplikacji i informacje specyficzne dla użytkownika z usługi Azure AD i zastosować je do usługi. Większość aplikacji SaaS innych firm, które są obsługiwane przez usługę Azure AD obsługują tę funkcję.

W przypadku logowania jednokrotnego opartego na hasłach może być przeglądarki przez użytkownika końcowego:
* Internet Explorer 11 — Windows 7 lub nowszy
* Edge w systemie Windows 10 Anniversary Edition lub nowszy 
* Chrome — W systemie Windows 7 lub nowszy i System MacOS x lub nowszym
* Firefox 26.0 lub później — w Windows XP z dodatkiem SP2 lub nowszy oraz w systemie Mac OS X 10,6 lub nowszej

### <a name="linked-single-sign-on"></a>Połączone logowanie jednokrotne
Podczas konfigurowania logowania jednokrotnego dla aplikacji, witryny Azure portal zapewnia trzecią opcję z "połączone logowanie jednokrotne". Ta opcja umożliwia po prostu administratorem, aby utworzyć link do aplikacji i umieść ją na panelu dostępu dla wybranych użytkowników.

Na przykład w przypadku aplikacji, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2.0, administrator może użyć opcji "połączone logowanie jednokrotne" Aby utworzyć link do niego w panelu dostępu. Gdy użytkownicy uzyskują dostęp do łącza, są uwierzytelniani, za pomocą Active Directory Federation Services 2.0 lub dowolnego istniejącego pojedynczego logowania jednokrotnego rozwiązania jest udostępniany przez aplikację.

### <a name="user-provisioning"></a>Inicjowanie obsługi użytkowników
Wybierz aplikacje usługi Azure AD umożliwia automatyczne użytkownika aprowizację i anulowanie obsługi kont w innych aplikacji SaaS pochodzących od w witrynie Azure portal przy użyciu informacji o tożsamości systemu Windows Server Active Directory lub Azure AD. Po użytkownik otrzyma uprawnienia w usłudze Azure AD dla jednego z tych aplikacji, konta mogą być automatycznie tworzone (udostępnione) w lokalizacji docelowej aplikacji SaaS.

Gdy użytkownik zostanie usunięty lub jego informacje zmian w usłudze Azure AD, tych zmian również są odzwierciedlane w aplikacji SaaS. Oznacza to, że zarządzanie cyklem życia tożsamości automatyczne konfigurowanie umożliwia administratorom sterować i zapewnić zautomatyzowaną aprowizację i cofanie aprowizacji w aplikacjach SaaS. W usłudze Azure AD ta Automatyzacja zarządzania cyklem życia tożsamości jest włączane przez Inicjowanie obsługi użytkowników.

Aby dowiedzieć się więcej, zobacz [automatyczna Aprowizacja użytkowników i anulowania zastrzeżenia do aplikacji SaaS](user-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Rozpoczynanie pracy z galerii aplikacji usługi Azure AD
Chcesz zacząć? Do wdrożenia logowania jednokrotnego między usługą Azure AD oraz aplikacje SaaS, które Twoja organizacja używa, przestrzegać następujących wytycznych.

### <a name="using-the-azure-ad-application-gallery"></a>Za pomocą galerii aplikacji usługi Azure AD
[Galeria aplikacji usługi Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) daje dostęp do listy aplikacji, które obsługujących formę logowanie jednokrotne z usługą Azure Active Directory.

![Galeria aplikacji online platformy Azure](media/what-is-single-sign-on/onlineappgallery.png)

Poniżej przedstawiono kilka wskazówek, służące do znajdowania aplikacji, jakie funkcje, które obsługują:

* Usługa Azure AD obsługuje automatyczne aprowizację i anulowanie obsługi dla wszystkich aplikacji "Proponowany" w [Galeria aplikacji usługi Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Listę aplikacji federacyjnych przeznaczone do obsługi federacyjnego logowania jednokrotnego przy użyciu protokołu, takie jak SAML, WS-Federation i OpenID Connect można znaleźć [tutaj](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Po znalezieniu aplikacji, możesz rozpocząć zgodnie z instrukcjami krok po kroku w galerii aplikacji i w witrynie Azure portal umożliwia logowanie jednokrotne.

### <a name="application-not-in-the-gallery"></a>Aplikacja nie ma w galerii?
Jeśli aplikacja nie zostanie znaleziony w galerii aplikacji usługi Azure AD, masz następujące opcje:

* **Dodaj aplikację spoza listy używasz** — kategoria niestandardowe w galerii aplikacji w witrynie Azure portal umożliwia łączenie nieznajdujące się na liście aplikacji, które Twoja organizacja używa. Możesz dodać dowolnej aplikacji, który obsługuje SAML 2.0 jako aplikacji federacyjnych lub dowolnej aplikacji, który jest oparty na języku HTML strony logowania jako hasło logowania jednokrotnego aplikacji. Aby uzyskać więcej informacji znajduje się w artykule na [Dodawanie własnych aplikacji](configure-federated-single-sign-on-non-gallery-applications.md).
* **Dodaj własną aplikację, tworzysz** — Jeśli opracowano aplikację samodzielnie, postępuj zgodnie z wytycznymi podanymi w dokumentacji dla deweloperów usługi Azure AD, aby zaimplementować federacyjnego logowania jednokrotnego lub interfejs API programu graph aprowizacji przy użyciu usługi Azure AD. Aby uzyskać więcej informacji zobacz następujące zasoby:
  
  * [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Żądanie integracji aplikacji** -żądania pomocy technicznej dla aplikacji przy użyciu [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Rozszerzenie usługi Active Directory w witrynie Azure portal służy do konfigurowania aplikacji logowania jednokrotnego. Pierwszym krokiem musisz wybrać katalog z sekcji usługi Active Directory w portalu:

![](./media/what-is-single-sign-on/azuremgmtportal.png)

Aby zarządzać aplikacji SaaS innych firm, można przełączyć się na karcie aplikacje w wybranym katalogu. Ten widok umożliwia administratorom:

* Dodawanie nowych aplikacji z galerii usługi Azure AD, a także aplikacje, które tworzysz
* Usuń zintegrowanych aplikacji
* Zarządzanie aplikacjami, które już zintegrowano

Typowe zadania administracyjne dla aplikacji SaaS innych firm są:

* Włączanie logowania jednokrotnego z usługą Azure AD przy użyciu hasła logowania jednokrotnego lub, jeśli są dostępne dla obiektu docelowego SaaS, Federacyjna usługa rejestracji Jednokrotnej
* Opcjonalnie włączenie użytkownika Inicjowanie obsługi administracyjnej dla użytkownika aprowizację i anulowanie obsługi (Zarządzanie cyklem życia tożsamości)
* W przypadku aplikacji 
* w przypadku, gdy użytkownik aprowizacja jest włączona, Wybieranie użytkowników, którzy mają dostęp do tej aplikacji

W przypadku aplikacji galerii, które obsługują federacyjnego logowania jednokrotnego Konfiguracja zwykle wymaga podania dodatkowe ustawienia konfiguracji, takich jak certyfikaty i metadane utworzyć relację zaufania federacji między aplikacji innych firm i Azure AD. Kreator konfiguracji przedstawiono szczegółowe informacje i zapewnia łatwy dostęp do danych specyficznych dla aplikacji SaaS i instrukcje.

Dla aplikacji galerii, które obsługują automatyczne aprowizowanie użytkowników należy nadać uprawnienia usługi Azure AD do zarządzania kontami usługi w aplikacji SaaS. Jako minimum musisz podać poświadczenia usługi Azure AD ma być użyte podczas uwierzytelniania za pośrednictwem aplikacji docelowej. Czy trzeba podać dodatkowe ustawienia konfiguracji, zależy od wymagań aplikacji.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Wdrażanie aplikacji zintegrowanych z usługą AD systemu Azure dla użytkowników
Usługi Azure AD oferuje kilka sposobów dostosowania do wdrażania aplikacji dla użytkowników końcowych w organizacji:

* Panel dostępu usługi Azure AD
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Metody, które chcesz wdrożyć w organizacji jest uznania.

### <a name="azure-ad-access-panel"></a>Panel dostępu usługi Azure AD
Panel dostępu w https://myapps.microsoft.com jest oparte na sieci web portalu, który umożliwia użytkownikowi końcowemu za pomocą konta organizacyjnego usługi Azure Active Directory, aby wyświetlić i uruchamiania aplikacji opartych na chmurze do których zostały one udzielony dostęp administrator usługi Azure AD. Jeśli użytkownik końcowy za pomocą [usługi Azure Active Directory — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/), możesz również korzystać z funkcji zarządzania grupami samoobsługi za pomocą panelu dostępu.

![Panel dostępu usługi Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Panel dostępu różni się w witrynie Azure portal i nie wymaga, aby użytkownicy mieli subskrypcję platformy Azure lub usługi Office 365.

Aby uzyskać więcej informacji na panelu dostępu do usługi Azure AD, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Uruchamianie aplikacji usługi Office 365
W przypadku organizacji, które zostały wdrożone usługi Office 365, aplikacje przypisane do użytkowników za pomocą usługi Azure AD pojawiają się w portalu usługi Office 365 w https://portal.office.com/myapps. To sprawia, że można łatwo i wygodnie dla użytkowników w organizacji można uruchomić aplikacji bez konieczności używania drugiej portalu i jest zalecaną aplikację uruchamiania rozwiązania dla organizacji przy użyciu usługi Office 365.

![](./media/what-is-single-sign-on/officeapphub.png)

Aby uzyskać więcej informacji na temat uruchamiania aplikacji usługi Office 365, zobacz [aplikacji są wyświetlane w obszarze uruchamiania aplikacji usługi Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Bezpośrednie logowanie do aplikacji federacyjnych
Większość aplikacji federacyjnych obsługuje SAML 2.0, WS-Federation i OpenID connect również pomocy technicznej przez użytkowników na poziomie aplikacji, a następnie Pobierz zalogowany za pomocą usługi Azure AD przez automatyczne przekierowanie lub klikając łącze do logowania. Jest to określane jako dostawca usług-zainicjowania logowania jednokrotnego, a większość aplikacji federacyjnych w galerii aplikacji usługi Azure AD obsługuje tego (znajduje się dokumentacja połączone za pomocą Kreatora konfiguracji rejestracji logowania jednokrotnego aplikacji w witrynie Azure portal, aby uzyskać szczegółowe informacje).

![](./media/what-is-single-sign-on/workdaymobile.png)

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Łącza bezpośrednie logowania federacyjnego, opartego na hasłach lub istniejących aplikacji
Usługa Azure AD obsługuje również pojedynczego logowania jednokrotnego łączy bezpośrednich dla poszczególnych aplikacji, które obsługują opartego na hasłach logowanie jednokrotne połączonej logowania jednokrotnego i jakiejkolwiek formy federacyjnego logowania jednokrotnego.

Te łącza są specjalnie przygotowane adresów URL, które wysyłają użytkownika przez proces logowania w usłudze Azure AD dla określonej aplikacji bez konieczności uruchamiania użytkownik je z usługi Azure AD dostęp do panelu lub usługi Office 365. Te pojedynczego logowania jednokrotnego adresy URL można znaleźć w obszarze karty Pulpit nawigacyjny wstępnie zintegrowanych aplikacji w sekcji usługi Active Directory w witrynie Azure Portal, jak pokazano na poniższym zrzucie ekranu.

![](./media/what-is-single-sign-on/deeplink.png)

Te linki można kopiować i wkleić z dowolnego miejsca, chcesz udostępnić Link umożliwiający zalogowanie się do wybranej aplikacji. Może to być w wiadomości e-mail lub w dowolnym opartych na sieci web portalu niestandardowym skonfigurowanej dla dostępu aplikacji użytkownika. Oto przykład z usługi Azure AD bezpośrednie pojedynczy adres URL logowania do usługi Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobnie jak adresów URL specyficznych dla organizacji do obsługi panelu dostępu, można dostosować ten adres URL, dodając jeden aktywny lub zweryfikowanych domen dla katalogu po nazwie domeny myapps.microsoft.com. Gwarantuje to, że wszelkie znakowanie organizacji jest ładowany bezpośrednio na stronie logowania bez użytkownik nie musi najpierw wprowadź swój identyfikator użytkownika:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Po kliknięciu autoryzowanym użytkownikiem na jednym z poniższych linków, specyficzne dla aplikacji, są najpierw Zobacz ich organizacji strony logowania (przy założeniu, że ich nie jest już zalogowany), a po zalogowaniu nastąpi przekierowanie do swoich aplikacji bez konieczności zatrzymywania w panelu dostępu, najpierw. Jeśli użytkownik nie ma dostępu do aplikacji, takich jak rozszerzenie przeglądarki logowania jednokrotnego opartego na hasłach, wymagania wstępne łącze będzie monitował użytkownika można zainstalować brakujące rozszerzenia. Adres URL linku również pozostaje na stałym, jeśli zmiany jednej konfiguracji logowania jednokrotnego dla aplikacji.

Te linki pełnić te same mechanizmy kontroli dostępu do panelu dostępu i usługi Office 365, a tylko tych użytkowników lub grupy, którzy zostali przypisani do aplikacji w witrynie Azure portal będzie mógł pomyślnie wykonać uwierzytelnienia. Jednak każdy użytkownik, który nie jest autoryzowany, zostanie wyświetlony komunikat wyjaśniający, że nie przyznano dostęp i podano łącza do załadowania panelu dostępu, aby wyświetlić dostępne aplikacje, dla których mają dostęp.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurowanie rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)
* [Wprowadzenie do zarządzania dostępem do aplikacji](what-is-access-management.md)
* [Porównanie możliwości zarządzania tożsamości zewnętrznych w usłudze Azure AD](../active-directory-b2b-compare-b2c.md)


