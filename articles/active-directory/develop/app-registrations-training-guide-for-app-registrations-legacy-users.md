---
title: Przewodnik treningowy rejestracji aplikacji Azure Portal (starsza wersja) — Azure
description: Wprowadzenie do nowego środowiska rejestracji aplikacji na platformie tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905396"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>Przewodnik szkoleniowy: Rejestracje aplikacji w Azure Portal (starsza wersja)

W Azure Portal można znaleźć liczne ulepszenia środowiska [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) . Jeśli znasz już starsze środowisko, Skorzystaj z tego przewodnika szkoleniowego, aby rozpocząć korzystanie z nowego środowiska.

W Azure Active Directory nowe środowisko rejestracji aplikacji opisane w tym miejscu jest ogólnie dostępne (GA). W Azure Active Directory B2C (Azure AD B2C) to środowisko jest w wersji zapoznawczej.

## <a name="key-changes"></a>Zmiany kluczy

- Rejestracje aplikacji nie są ograniczone do **aplikacji sieci Web ani interfejsu API** ani aplikacji **natywnych** . Możesz użyć tej samej rejestracji aplikacji dla wszystkich tych elementów, rejestrując odpowiednie identyfikatory URI przekierowania.
- Starsze wersje obsługują aplikacje, które logują się tylko na kontach w organizacji (Azure AD). Aplikacje zostały zarejestrowane jako pojedyncze dzierżawy (obsługujące tylko konta organizacji z katalogu, w którym zarejestrowano aplikację) i mogą być modyfikowane jako wielodostępne (obsługujące wszystkie konta organizacji). Nowe środowisko pozwala zarejestrować aplikacje, które mogą obsługiwać obie te opcje, a także trzecią opcję: wszystkie konta organizacji, a także osobiste konta Microsoft.
- Starsze środowisko było dostępne tylko po zalogowaniu się do Azure Portal przy użyciu konta organizacyjnego. Korzystając z nowego środowiska, możesz używać osobistych kont Microsoft, które nie są skojarzone z katalogiem.

## <a name="list-of-applications"></a>Lista aplikacji

- Nowa lista aplikacji zawiera aplikacje, które zostały zarejestrowane za pośrednictwem środowiska rejestracji starszej aplikacji w Azure Portal (aplikacje, które logują konta usługi Azure AD), a także aplikacje zarejestrowane przy użyciu [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) (aplikacje, które logują się Azure AD i osobiste konta Microsoft).
- Nowa lista aplikacji nie ma kolumny **typu aplikacji** (ponieważ Rejestracja jednokrotna może mieć kilka typów) i ma dwie dodatkowe kolumny: kolumna **utworzona w** kolumnie i **Certyfikaty &** Secret, która wyświetla stan (bieżące, wygasa wkrótce lub wygasło) poświadczeń, które zostały zarejestrowane w aplikacji.

## <a name="new-app-registration"></a>Rejestracja nowej aplikacji

W starszej wersji, aby zarejestrować aplikację, należy podać: **nazwę**, **Typ aplikacji**i **adres URL logowania/identyfikator URI przekierowania**. Utworzone aplikacje były przeznaczone tylko dla aplikacji z jedną dzierżawą usługi Azure AD, co oznacza, że tylko obsługiwane konta organizacji z katalogu, w którym zarejestrowano aplikację.

W nowym środowisku należy podać **nazwę** aplikacji i wybrać **obsługiwane typy kont**. Opcjonalnie możesz podać **Identyfikator URI przekierowania**. Jeśli podasz identyfikator URI przekierowania, musisz określić, czy jest on w sieci Web, czy publicznej (Mobile and Desktop). Aby uzyskać więcej informacji na temat sposobu rejestrowania aplikacji przy użyciu nowego środowiska rejestracji aplikacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md). Aby uzyskać Azure AD B2C, zobacz [Rejestrowanie aplikacji w programie Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>Strona właściwości starszej wersji

Starsze środowisko miało stronę **Właściwości** , która nie ma nowego środowiska. Blok **Właściwości** ma następujące pola: **Nazwa**, **Identyfikator obiektu**, **Identyfikator aplikacji**, identyfikator **URI aplikacji**, **logo**, **adres URL strony głównej**, **adres URL wylogowania**, **adres URL warunków świadczenia usługi**, **zasady zachowania poufności informacji Adres URL**, **Typ aplikacji**i **wiele dzierżawców.**

Tutaj można znaleźć odpowiednie funkcje w nowym środowisku:

- **Nazwa**, **logo**, **adres URL strony głównej**, **adres URL warunków**użytkowania i **adres URL zasady zachowania poufności informacji** znajduje się teraz na stronie **znakowania** aplikacji.
- Identyfikator **obiektu** i **Identyfikator aplikacji (klienta)** znajduje się na stronie **Przegląd** .
- Funkcje kontrolowane przez przełącznik z **wieloma dzierżawcami** w starszym środowisku zostały zastąpione przez **obsługiwane typy kont** na stronie **uwierzytelnianie** . Aby uzyskać więcej informacji o tym, jak wiele dzierżawców jest mapowanych na obsługiwane opcje typu konta, zobacz [ten przewodnik Szybki Start](quickstart-modify-supported-accounts.md).
- **Adres URL wylogowywania** znajduje się teraz na stronie **uwierzytelnianie** .
- **Typ aplikacji** nie jest już prawidłowym polem. Zamiast tego identyfikatory URI przekierowania (które można znaleźć na stronie **uwierzytelniania** ) określają, które typy aplikacji są obsługiwane.
- **Identyfikator URI aplikacji** jest teraz nazywany identyfikatorem **URI aplikacji** i można go znaleźć w bloku **Uwidacznianie interfejsu API** . W starszej wersji ta właściwość została zarejestrowana w sposób autorejestrowania przy użyciu następującego formatu: `https://{tenantdomain}/{appID}` (na przykład `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). W nowym środowisku jest automatycznie generowana jako `api://{appID}`, ale musi być jawnie zapisana. W Azure AD B2C dzierżawy format `https://{tenantdomain}/{appID}` nadal jest używany.

## <a name="reply-urlsredirect-urls"></a>Adresy URL odpowiedzi/adresy URL przekierowań

W starszej wersji aplikacja zawierała **adres URL odpowiedzi** . W nowym środowisku adresy URL odpowiedzi można znaleźć w sekcji **uwierzytelniania** aplikacji. Ponadto są one określane jako **identyfikatory URI przekierowania**. Ponadto format identyfikatorów URI przekierowania został zmieniony. Muszą być skojarzone z typem aplikacji (sieć Web lub publiczna). Ponadto ze względów bezpieczeństwa nie są obsługiwane symbole wieloznaczne i schematy http://(z wyjątkiem http://localhost).

## <a name="keyscertificates--secrets"></a>Klucze/certyfikaty & wpisy tajne

W starszym środowisku aplikacja zawierała **klucze** na stronie. W nowym środowisku została zmieniona nazwa na **certificates &** Secret. Ponadto **klucze publiczne** nazywa się **certyfikatami** i **hasłami** są określane jako wpisy **tajne klienta**.

## <a name="required-permissionsapi-permissions"></a>Wymagane uprawnienia/uprawnienia interfejsu API

- W starszym środowisku aplikacja miała **wymaganą stronę uprawnień** . W nowym środowisku zmieniono nazwę na **uprawnienia interfejsu API**.
- W przypadku wybrania interfejsu API w starszym środowisku można wybrać jedną z małych list interfejsów API firmy Microsoft lub przeszukać nazwy główne usług w dzierżawie. W nowym środowisku można wybierać spośród wielu kart: **interfejsów API firmy Microsoft**, **interfejsów API używanych przez moją organizację**lub **moich interfejsów API**. Pasek wyszukiwania w obszarze **interfejsy API Moja organizacja** używa wyszukiwania na kartach za pośrednictwem jednostek usługi w dzierżawie.

   > [!NOTE]
   > Ta karta nie zostanie wyświetlona, jeśli aplikacja nie jest skojarzona z dzierżawcą. Aby uzyskać więcej informacji na temat żądania uprawnień przy użyciu nowego środowiska, zobacz [ten przewodnik Szybki Start](quickstart-configure-app-access-web-apis.md).

- Starsze środowisko ma przycisk **Udziel uprawnień** w górnej części strony **żądane uprawnienia** . W nowym doświadczeniu znajduje się sekcja **zgody na przyznanie** uprawnień **administratora** w sekcji **uprawnienia do interfejsu API** aplikacji. Ponadto istnieją pewne różnice w sposobach działania funkcji Button:
   - W starszym środowisku logika jest zróżnicowana w zależności od zalogowanego użytkownika i żądanych uprawnień. Logika:
      - Jeśli zażądano tylko uprawnień użytkownika, a zalogowany użytkownik nie jest administratorem, użytkownik był w stanie udzielić zgody użytkownika na wymagane uprawnienia.
      - Jeśli wymagane jest co najmniej jedno uprawnienie, które wymaga zgody administratora, a zalogowany użytkownik nie jest administratorem, podczas próby udzielenia zgody Wystąpił błąd.
      - Jeśli zalogowany użytkownik był administratorem, przyznano zgodę administratora na wszystkie żądane uprawnienia.
   - W nowym środowisku tylko administrator może udzielić zgody. Gdy administrator wybierze przycisk **Udziel zgody administratora** , zgoda administratora zostanie udzielona na wszystkie żądane uprawnienia.

## <a name="deleting-an-app-registration"></a>Usuwanie rejestracji aplikacji

W starszym środowisku aplikacja musiała zostać usunięta z jednej dzierżawy. Przycisk Usuń został wyłączony dla aplikacji wielodostępnych. W nowym środowisku aplikacje można usuwać w dowolnym stanie, ale należy potwierdzić akcję. Więcej informacji o usuwaniu rejestracji aplikacji można znaleźć w [tym przewodniku szybki start](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikacji

Starsze i nowe środowiska używają różne wersje formatu JSON w edytorze manifestu. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika

Istnieje nowy interfejs użytkownika dla właściwości, które wcześniej mogły być ustawiane tylko przy użyciu edytora manifestu lub interfejsu API lub nie istnieją.

- **Niejawny przepływ grantu** (oauth2AllowImplicitFlow) można znaleźć na stronie **uwierzytelniania** . W przeciwieństwie do starszych wersji środowiska, można włączyć **tokeny dostępu** lub **tokeny identyfikatora**lub oba te elementy.
- **Zakresy zdefiniowane przez ten interfejs API** (oauth2Permissions) i **autoryzowane aplikacje klienckie** (preAuthorizedApplications) można skonfigurować za pomocą strony **Uwidacznianie interfejsu API** . Aby uzyskać więcej informacji na temat konfigurowania aplikacji jako interfejsu API sieci Web i uwidaczniania uprawnień/zakresów, zobacz [ten przewodnik Szybki Start](quickstart-configure-app-expose-web-apis.md).
- **Domenę wydawcy** (wyświetlaną dla użytkowników z [monitem o zgodę aplikacji](application-consent-experience.md)) można znaleźć na stronie **bloku znakowania** . Aby uzyskać więcej informacji na temat konfigurowania domeny wydawcy, zobacz [ten sposób](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

- Format wpisów tajnych klienta (hasła aplikacji) różni się od starszego środowiska i może spowodować przerwanie interfejsu wiersza polecenia.
- Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Musisz używać manifestu aplikacji, chyba że przełączasz się między jedną dzierżawą i wieloma dzierżawcami usługi Azure AD.