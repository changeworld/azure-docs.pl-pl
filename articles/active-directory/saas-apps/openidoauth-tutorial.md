---
title: Konfigurowanie aplikacji OpenID/OAuth za pomocą galerii aplikacji Azure AD | Dokumentacja firmy Microsoft
description: Kroki konfigurowania aplikacji OpenID/OAuth za pomocą galerii aplikacji Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 04639e6d27854d9c25b97936b163cfaaa25fc375
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287442"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurowanie aplikacji OpenID/OAuth za pomocą galerii aplikacji Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces dodawania aplikacji OpenID z galerii

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Bloku aplikacje w przedsiębiorstwie](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Wybierz **nową aplikację** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](./media/openidoauth-tutorial/tutorial_general_03.png)

4. W polu wyszukiwania wpisz nazwę aplikacji. Wybierz żądaną aplikację z poziomu panelu wyników i Zarejestruj się do aplikacji.

    ![Dodawanie aplikacji](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > W przypadku aplikacji OpenID Connect i OAuth **Dodaj** przycisk jest domyślnie wyłączona. W tym miejscu należy wybrać rejestracji Administrator dzierżawy znajdujący się i zgody w aplikacji. Aplikacja jest dodawane do dzierżawy klienta, gdzie można zrobić konfiguracje. Nie ma potrzeby jawnie dodać aplikację.

    ![Przycisk dodawania](./media/openidoauth-tutorial/addbutton.png)

5. Po wybraniu link umożliwiający rejestrację, są przekierowywane do strony usługi Azure Active Directory (Azure AD) dla poświadczeń logowania.

6. Po pomyślnym uwierzytelnieniu akceptujesz zgody strona zgody użytkownika. Po tym zostanie wyświetlona strona główna aplikacji.

    > [!NOTE]
    > Można dodać tylko jedno wystąpienie aplikacji. Jeśli zostało już dodane, a próba ponownego podania zgody, go nie zostaną dodane ponownie w dzierżawie. Dlatego logicznie służy tylko jedno wystąpienie aplikacji w dzierżawie.

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania za pomocą protokołu OpenID Connect

Najbardziej podstawowa przepływ logowania zawiera następujące czynności:

![Przepływ uwierzytelniania za pomocą protokołu OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplikacji wielodostępnej 
Wielodostępnej aplikacji jest przeznaczony do użycia w wielu organizacjach nie tylko jednej z organizacji. Są to zazwyczaj software-as-a-service (SaaS) aplikacji napisanych przez niezależnego dostawcę oprogramowania (ISV). 

Wielodostępne aplikacje muszą być obsługiwana w każdym katalogu, w którym będzie używana. Wymagają one zgody użytkownika lub administratora można je zarejestrować. Ten proces zgody rozpoczyna się, gdy aplikacja została zarejestrowana w katalogu i otrzymuje dostęp do interfejsu API programu Graph lub może być inny interfejs API sieci web. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, okno dialogowe Wyświetla uprawnienia, które są wymagane przez aplikację. 

Użytkownik lub administrator może następnie zgoda na aplikację. Zgoda zapewnia dostęp do aplikacji do podanych danych, a na koniec rejestruje aplikację w katalogu.

> [!NOTE]
> Jeśli wprowadzasz aplikacji dostępnych dla użytkowników w wielu katalogach, potrzebujesz mechanizmu, aby określić, której dzierżawy znajdują się w. Aplikacja jednej dzierżawy musi się tylko do wyszukiwania w katalogu dla użytkownika. Wielodostępnej aplikacji musi zidentyfikować określonego użytkownika ze wszystkich katalogów w usłudze Azure AD.
> 
> Aby wykonać to zadanie, usługa Azure AD zapewnia wspólnego punktu końcowego uwierzytelniania gdzie dowolnej aplikacji wielodostępnych można kierować żądań logowania, zamiast punktem końcowym specyficznym dla dzierżawy. Ten punkt końcowy jest [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) dla wszystkich katalogów w usłudze Azure AD. Może być punktem końcowym specyficznym dla dzierżawy [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> Typowe punktu końcowego jest ważne, aby wziąć pod uwagę podczas tworzenia aplikacji. Potrzebna będzie logikę potrzebną do obsługi wielu dzierżaw podczas logowania, wylogowywania i walidacji tokenów.

Domyślnie program Azure AD promuje aplikacji wielodostępnych. Łatwo jest uzyskać dostęp między organizacjami i są one łatwe w użyciu po zaakceptowaniu zgody.

## <a name="consent-framework"></a>Platforma wyrażania zgody

Platformy wyrażania zgody w usłudze Azure AD służy do tworzenia, wielodostępna sieci web i natywne aplikacje klienckie. Te aplikacje Zezwalaj na logowanie za pomocą kont użytkownika z dzierżawy usługi Azure AD, inny niż ten, na którym aplikacja jest zarejestrowany. One może być również konieczne dostępu do interfejsów API sieci web, takich jak:
- Microsoft interfejsu API programu Graph, aby dostęp do usługi Azure AD, usługa Intune i usługi w usłudze Office 365. 
- Interfejsy API z innymi usługami firmy Microsoft.
- Własnych interfejsów API sieci web. 

Struktura opiera się na użytkownika lub administratora wyrażenia zgody na aplikację, która prosi do zarejestrowania się w jego katalogu. Rejestracja może obejmować, uzyskiwanie dostępu do danych katalogu. Po zgoda zostanie podany, aplikacja kliencka może wywołania interfejsu API programu Microsoft Graph w imieniu użytkownika i skorzystaj z informacji, zgodnie z potrzebami.

[Interfejsu API Microsoft Graph](https://developer.microsoft.com/graph/) zapewnia dostęp do danych w usłudze Office 365, takie jak:

- Kalendarze i komunikaty z programu Exchange.
- Witryny i listy z poziomu programu SharePoint.
- Dokumenty z usługi OneDrive.
- Notesy w programie OneNote.
- Zadania w aplikacji Planner.
- Skoroszyty programu Excel.

Interfejs API programu Graph umożliwia również dostęp dla użytkowników i grup z usługi Azure AD i innych obiektów danych z więcej usług w chmurze firmy Microsoft.

Poniższe kroki pokazują, jak zgody środowisko działa dla deweloperów aplikacji i użytkownika:

1. Załóżmy, że masz klienta aplikacji sieci web, który wymaga, aby żądać określonych uprawnień dostępu do zasobu lub interfejsu API. Witryna Azure portal jest używane do deklarowania żądań dotyczących uprawnień podczas konfiguracji. Podobnie jak inne ustawienia konfiguracji staną się częścią aplikacji usługi Azure AD rejestracji:

    ![Interfejs API programu Graph](./media/openidoauth-tutorial/graphapi.png)

2. Należy wziąć pod uwagę, że uprawnienia aplikacji został zaktualizowany. Aplikacja jest uruchomiona, a użytkownik ma użyć go po raz pierwszy. Najpierw aplikację Aby uzyskać kod autoryzacji z usługi Azure AD punktu końcowego / authorize. Kod autoryzacji mogą następnie służyć do uzyskania nowego dostępu i token odświeżania.

3. Jeśli użytkownik nie jest już uwierzytelniony, usługi Azure AD / authorize punktu końcowego monity podczas logowania.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Po użytkownik zalogował się w usłudze Azure AD Określa, jeśli użytkownik powinien być wyświetlony strona zgody użytkownika. To jest na podstawie tego, czy użytkownik (lub administratora w organizacji) już udzielił zgody aplikacji.

   Jeśli nie udzielono zgody, usługi Azure AD monituje użytkownika o zgodę i wyświetli wymagane uprawnienia, których potrzebuje do działania. Uprawnienia, które są wyświetlane w oknie dialogowym wyrażania zgody odpowiadały tym wybrane w delegowane uprawnienia w witrynie Azure portal.

    ![Strona zgody użytkownika](./media/openidoauth-tutorial/consentpage.png)

Zwykły użytkownik mogą wyrazić zgodę na niektóre uprawnienia. Inne uprawnienia wymagają zgody administratora dzierżawy.

## <a name="difference-between-admin-consent-and-user-consent"></a>Różnica między zgody administratora i zgoda użytkownika

Jako administrator mogą również wyrazić zgodę na delegowane uprawnienia aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgody administratora zapobiega wyświetlane dla każdego użytkownika w dzierżawie w oknie dialogowym wyrażania zgody. Użytkownicy, którzy mają roli administrator może dostarczyć zgody w witrynie Azure portal. Z **ustawienia** strona aplikacji, wybierz pozycję **wymagane uprawnienia** > **Udziel uprawnień**.

![Przycisk Udziel uprawnień](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Udzielanie wyraźnej zgody przy użyciu **Udziel uprawnień** przycisk jest obecnie wymagane dla aplikacji jednostronicowych (źródła), które używają ADAL.js. W przeciwnym razie aplikacja ulegnie awarii, gdy żąda tokenu dostępu.

Uprawnień dotyczących tylko aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli Twoja aplikacja żąda uprawnienia tylko do aplikacji, a użytkownik próbuje zalogować się do aplikacji, zostanie wyświetlony komunikat o błędzie. Wiadomość wyświetlana jest informacja, że użytkownik nie jest w stanie do wyrażenia zgody.

Jeśli aplikacja używa uprawnień, które wymagają zgody administratora, musisz mieć gest, takich jak przycisk lub łącze, której administrator można uruchomić akcji. Żądania, które wysyła Twoja aplikacja ta akcja jest zwykle żądanie autoryzacji OAuth2/OpenID Connect. To żądanie zawiera *prompt = admin_consent* parametr ciągu zapytania. 

Gdy administrator wyraził zgodę i nazwy głównej usługi jest tworzony w dzierżawie klienta, nie ma potrzeby nowszych żądań logowania *prompt = admin_consent* parametru. Ponieważ administrator zdecydował, że żądane uprawnienia są akceptowane, żaden użytkownik w dzierżawie monit o zgodę od tego momentu.

Administrator dzierżawy może wyłączyć przez regularne użytkownikom wyrażanie zgody na aplikacje. Jeśli ta funkcja jest wyłączona, zawsze jest wymagana dla aplikacji do użycia w dzierżawie zgoda administratora. Aby przetestować aplikację, za zgodą użytkownika końcowego wyłączone, możesz znaleźć przełącznika konfiguracji w [witryny Azure portal](https://portal.azure.com/). Jest on [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) sekcji **aplikacje dla przedsiębiorstw**.

*Prompt = admin_consent* parametru używać przez aplikacje, które zażądać uprawnień, które nie wymagają zgody administratora. Przykładem jest aplikacja, która wymaga środowiska, w którym dzierżawy administratora "zarejestruje się w" jeden czasu i żadne inne użytkownicy są monitowani o zgodę od tego momentu na.

Wyobraź sobie, że aplikacja wymaga zgody administratora, a administrator loguje się bez *prompt = admin_consent* parametru są wysyłane. Gdy administrator pomyślnie wyraża zgodę na aplikację, ma zastosowanie tylko dla swojego konta użytkownika. Zwykli użytkownicy nadal będzie mógł zalogować się lub wyrazić zgodę na aplikację. Ta funkcja jest przydatna, jeśli chcesz nadać administratorowi dzierżawy możliwość badania aplikacji przed zezwoleniem na dostęp do innych użytkowników.