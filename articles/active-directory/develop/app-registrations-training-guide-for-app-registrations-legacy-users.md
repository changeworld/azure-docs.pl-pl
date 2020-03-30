---
title: Nowy przewodnik szkoleniowy na temat rejestracji aplikacji w witrynie Azure Portal
description: Przedstawia nowe środowisko rejestracji aplikacji portalu Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154580"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Nowy przewodnik szkoleniowy na temat rejestracji aplikacji w witrynie Azure Portal

Wiele ulepszeń można znaleźć w nowym środowiska [rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) w witrynie Azure portal. Jeśli znasz środowisko rejestracji aplikacji (starszych) w witrynie Azure Portal, skorzystaj z tego przewodnika szkoleniowego, aby rozpocząć korzystanie z nowego środowiska.

W usłudze Azure Active Directory nowe środowisko rejestracji aplikacji opisane w tym miejscu jest ogólnie dostępne (GA). W usłudze Azure Active Directory B2C (Azure AD B2C) to środowisko jest w wersji zapoznawczej.

## <a name="key-changes"></a>Kluczowe zmiany

- Rejestracje aplikacji nie są ograniczone do bycia *aplikacją internetową/interfejsem API* lub *aplikacją* macierzystą. Możesz użyć tej samej rejestracji aplikacji dla wszystkich tych aplikacji, rejestrując odpowiednie identyfikatory URI przekierowania.

- Starsze środowisko obsługiwane aplikacje, które logują się przy użyciu kont instytucji (Usługi Azure AD) tylko. Aplikacje zostały zarejestrowane jako pojedyncza dzierżawa. Aplikacje obsługiwały tylko konta instytucji z katalogu, w który została zarejestrowana aplikacja. Aplikacje mogą być modyfikowane jako wielodostępne i obsługują wszystkie konta organizacyjne. Nowe środowisko umożliwia rejestrowanie aplikacji, które obsługują zarówno te opcje, jak i trzecią opcję: wszystkie konta organizacyjne, a także osobiste konta Microsoft.

- Starsze środowisko było dostępne tylko po zalogowaniu się do witryny Azure portal przy użyciu konta organizacji. Dzięki nowemu doświadczeniu można używać osobistych kont Microsoft, które nie są skojarzone z katalogiem.

## <a name="list-of-applications"></a>Lista wniosków

Nowa lista aplikacji zawiera aplikacje, które zostały zarejestrowane za pośrednictwem środowiska rejestracji starszych aplikacji w witrynie Azure portal. Te aplikacje logują się przy użyciu kont usługi Azure AD. Nowa lista aplikacji zawiera również aplikacje zarejestrowane przez portal rejestracji aplikacji. Te aplikacje logują się przy użyciu usługi Azure AD i osobistych kont Microsoft.

>[!NOTE]
>Portal rejestracji aplikacji został przestarzały.

Nowa lista aplikacji nie ma kolumny **typu aplikacji,** ponieważ pojedyncza rejestracja aplikacji może być kilka typów. Lista zawiera dwie dodatkowe kolumny: **Utworzone na** i **Certyfikaty & wpisy tajne**. **Certyfikaty & wpisy tajne** pokazują stan poświadczeń, które zostały zarejestrowane w aplikacji. Stany obejmują **bieżące**, **wygasające wkrótce**i **wygasłe**.

## <a name="new-app-registration"></a>Rejestrowanie nowej aplikacji

W starszej wersji środowiska, aby zarejestrować aplikację, którą trzeba było podać: **Nazwa**, **Typ aplikacji**i **Adres URL logowania/Przekierowanie URI**. Aplikacje, które zostały utworzone były usługi Azure AD tylko aplikacji jednej dzierżawy. Obsługiwane były tylko konta organizacji z katalogu, w który została zarejestrowana aplikacja.

W nowym środowiskach należy podać **nazwę** aplikacji i wybrać **typy obsługiwanych kont**. Opcjonalnie można podać **identyfikator URI przekierowania**. Jeśli podasz identyfikator URI przekierowania, musisz określić, czy jest on internetowy/publiczny (mobilny i stacjonarny). Aby uzyskać więcej informacji, zobacz [Szybki start: Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md). W przypadku usługi Azure AD B2C zobacz [Rejestrowanie aplikacji w usłudze Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Różnice między portalem rejestracji aplikacji a stroną rejestracji aplikacji

### <a name="the-legacy-properties-page"></a>Starsza strona Właściwości

Starsze środowisko miało stronę **Właściwości.** **Właściwości** miały następujące pola:

- **Nazwa**
- **Identyfikator obiektu**
- **Identyfikator aplikacji**
- **Identyfikator URI identyfikatora aplikacji**
- **Logo**
- **Adres URL strony głównej**
- **Adres URL wylogowywania**
- **Adres URL warunków korzystania z usługi**
- **Adres URL zasad zachowania poufności informacji**
- **Typ aplikacji**
- **Wielodostępne**

Nowe środowisko nie ma tej strony. Oto, gdzie można znaleźć równoważne funkcje:

- **Nazwa**, **Logo**, **Adres URL strony głównej,** **Warunki korzystania z usługi URL**i adres URL zasad zachowania **poufności informacji** znajdują się teraz na stronie **znakowanie** aplikacji.
- **Identyfikator obiektu** i **identyfikator aplikacji (klient)** znajdują się na stronie **Przegląd.**
- Funkcje kontrolowane przez przełącznik **wielu dzierżawców** w starszej wersji środowiska zostały zastąpione przez **obsługiwane typy kont** na stronie **Uwierzytelnianie.** Aby uzyskać więcej informacji, zobacz [Szybki start: Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md).
- **Adres URL wylogowania** znajduje się teraz na stronie **Uwierzytelnianie.**
- **Typ aplikacji** nie jest już prawidłowym polem. Zamiast tego przekieruj identyfikatory URI, które można znaleźć na stronie **Uwierzytelnianie,** określ, które typy aplikacji są obsługiwane.
- **Identyfikator URI identyfikatora aplikacji** jest teraz nazywany **identyfikatorem URI identyfikatora aplikacji** i można go znaleźć na **expose interfejsu API**. W starszej wersji tej właściwości została autoryzowana przy `https://{tenantdomain}/{appID}`użyciu następującego formatu: , na przykład. `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b` W nowym doświadczeniu jest automatyczniegenerowany `api://{appID}`jako , ale musi być jawnie zapisany. W dzierżawach usługi Azure AD `https://{tenantdomain}/{appID}` B2C format jest nadal używany.

### <a name="reply-urlsredirect-urls"></a>Odpowiedz na adresy URL/przekieruj rl

W starszej wersji aplikacja miała stronę **Adresów URL odpowiedzi.** W nowym środowiskach adresy URL odpowiedzi można znaleźć na stronie **uwierzytelniania** aplikacji. Są one teraz określane jako **przekierowanie identyfikatorów URI.**

Format przekierowania identyfikatorów URI uległ zmianie. Muszą być skojarzone z typem aplikacji, zarówno w sieci Web, jak i z usługą publiczną. Ze względów bezpieczeństwa `http://` symbole wieloznaczne i schematy nie są obsługiwane, z wyjątkiem *http://localhost*.

### <a name="keyscertificates--secrets"></a>Klucze/certyfikaty & wpisy tajne

W starszej wersji aplikacji **miał** keys strony. W nowym doświadczeniu został on przemianowany na **Certyfikaty & tajemnice**.

**Klucze publiczne** są teraz określane jako **Certyfikaty**. **Hasła** są teraz określane jako **tajemnice klienta**.

### <a name="required-permissionsapi-permissions"></a>Wymagane uprawnienia/uprawnienia interfejsu API

W starszej wersji aplikacji była strona **Wymagane uprawnienia.** W nowym środowiskach została zmieniona na **uprawnienia interfejsu API**.

Po wybraniu interfejsu API w starszej wersji środowiska można wybrać z małej listy interfejsów API firmy Microsoft. Można również przeszukiwać jednostki usługi w dzierżawie. W nowym środowiskach można wybierać spośród wielu kart: **Interfejsy API firmy Microsoft**, **interfejsy API używane przez moją organizację**lub Moje **interfejsy API**. Pasek wyszukiwania w **interfejsach API mojej organizacji** używa tabulatorów przeszukuje jednostki usługi w dzierżawie.

> [!NOTE]
> Ta karta nie zostanie wyświetlona, jeśli aplikacja nie jest skojarzona z dzierżawą. Aby uzyskać więcej informacji na temat sposobu żądania uprawnień, zobacz [Szybki start: Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md).

Starsze środowisko miało przycisk **Uprawnienia grantu** u góry strony **Żądane uprawnienia.** W nowym środowiskau strony **zgody grantu** ma przycisk **Zgody administratora grantu** w sekcji **uprawnień interfejsu API** aplikacji. Istnieją również pewne różnice w sposób działania przycisków.

W starszej wersji logiki różniła się w zależności od zalogowany użytkownik i wymagane uprawnienia. Logika była:

- Jeśli wymagane były tylko uprawnienia z możliwością wyrażenia zgody przez użytkownika, a zalogowany użytkownik nie był administratorem, użytkownik może udzielić zgody użytkownika na żądane uprawnienia.
- Jeśli zażądano co najmniej jednego uprawnienia, które wymaga zgody administratora, a zalogowany użytkownik nie był administratorem, użytkownik wystąpił z błędem podczas próby udzielenia zgody.
- Jeśli zalogowany użytkownik był administratorem, udzielono zgody administratora dla wszystkich wymaganych uprawnień.

W nowym środowiskach tylko administrator może udzielić zgody. Gdy administrator wybierze **opcję Udzielić zgody administratora,** zgoda administratora jest udzielana wszystkim żądanym uprawnieniom.

## <a name="deleting-an-app-registration"></a>Usuwanie rejestracji aplikacji

W starszej wersji środowiska można usunąć tylko aplikacje z jedną dzierżawą. Przycisk usuwania został wyłączony dla aplikacji z wieloma dzierżawami. W nowym środowiskach można usunąć aplikacje w dowolnym stanie, ale musisz potwierdzić akcję. Aby uzyskać więcej informacji, zobacz [Szybki start: Usuwanie aplikacji zarejestrowanej na platformie tożsamości firmy Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikacji

Starsze i nowe środowiska używają różnych wersji dla formatu JSON w edytorze manifestów. Aby uzyskać więcej informacji, zobacz [manifest aplikacji usługi Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika

Nowe środowisko dodaje formanty interfejsu użytkownika dla następujących właściwości:

- Strona **Uwierzytelnianie** zawiera **niejawny przepływ dotacji** (`oauth2AllowImplicitFlow`). W przeciwieństwie do starszego środowiska, można włączyć **tokeny dostępu** lub **tokeny identyfikatorów**lub obu.
- Strona **Uwidacznianie interfejsu API** zawiera`oauth2Permissions` **zakresy zdefiniowane przez ten interfejs API** ( ) i autoryzowane **aplikacje klienckie** (`preAuthorizedApplications`). Aby uzyskać więcej informacji na temat konfigurowania aplikacji jako internetowego interfejsu API i udostępniania uprawnień/zakresów, zobacz [Szybki start: Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md).
- Strona **Znakowanie** zawiera **domenę programu Publisher**. Domena wydawcy jest wyświetlana użytkownikom w [wierszu zgody aplikacji](application-consent-experience.md). Aby uzyskać więcej informacji, zobacz [Jak: Konfigurowanie domeny wydawcy aplikacji](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

- Format wpisów tajnych klienta (hasła aplikacji) różni się od starszego środowiska i może złamać interfejsu wiersza polecenia.
- Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Musisz użyć manifestu aplikacji, chyba że przełączasz się między usługą Azure AD pojedynczej dzierżawy i wielu dzierżawców.
