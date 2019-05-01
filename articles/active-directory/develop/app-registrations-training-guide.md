---
title: Rejestracje aplikacji, w przewodniku portalu szkolenia dotyczącego platformy Azure — platformy Azure
description: Tworzenie osadzonego i udzielić przepływów uwierzytelniania bez przeglądarki, przy użyciu kodu urządzenia.
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
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870120"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Przewodnik po szkolenia: Rejestracje aplikacji w witrynie Azure portal  

Możesz znaleźć wiele ulepszeń w nowym [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko w witrynie Azure portal. Jeśli jesteś lepiej zapoznać się ze starszego środowiska użytkownika, należy użyć tego przewodnika szkolenia ułatwiające rozpoczęcie pracy przy użyciu nowego środowiska.

## <a name="key-changes"></a>Zmian klucza

- Rejestracje aplikacji nie są ograniczone do jednego **aplikacji/interfejs API sieci web** lub **natywnych** aplikacji. We wszystkich przypadkach, rejestrując się odpowiednie identyfikatory URI przekierowania, można użyć tego samego rejestracji aplikacji.
- Aplikacje starszego środowiska usługi obsługiwane, które logują się organizacji (Azure AD) konta tylko. Aplikacje zostały zarejestrowane jako pojedynczej dzierżawy (Obsługa kont organizacji tylko z katalogu aplikacji został zarejestrowany w) i może zostać zmodyfikowany za wielu dzierżaw (Obsługa wszystkich kont w organizacji). Nowe środowisko pozwala na rejestrowanie aplikacji, które mogą obsługiwać obu tych opcji, a także trzecia opcja: wszystkie konta organizacyjne, a także osobistych kont Microsoft.
- Starszego była dostępna tylko po zalogowaniu w witrynie Azure portal, za pomocą konta organizacyjnego. Za pomocą nowego środowiska pracy można użyć osobistych kont Microsoft, które nie są skojarzone z katalogiem.

## <a name="list-of-applications"></a>Lista aplikacji

- Pokazuje nową listę aplikacji, aplikacje, które zostały zarejestrowane za pomocą starszej wersji aplikacji środowiska rejestracji w witrynie Azure portal (aplikacje, które logują się konta usługi Azure AD), a także aplikacje zarejestrowane jednak [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) (aplikacje, które logują się w usłudze Azure AD i osobistych kont Microsoft).
- Nowa lista aplikacji nie ma **typ aplikacji** kolumny (ponieważ rejestracja jednej aplikacji może być kilka typów) i ma dwie dodatkowe kolumny: **utworzone na** kolumny i **certyfikatów & wpisy tajne** kolumna, która pokazuje stan (bieżąca, które wkrótce wygasną lub wygasła) poświadczeń, które zostały zarejestrowane w aplikacji.

## <a name="new-app-registration"></a>Rejestrowanie nowej aplikacji

W starszej wersji środowiska Aby zarejestrować aplikację trzeba zapewnić: **Nazwa**, **typ aplikacji**, i **jednokrotnej adres URL/identyfikator URI**. Aplikacje, które zostały utworzone zostały aplikacji tylko jedną dzierżawą usługi Azure AD, co oznacza, że obsługiwane tylko konta organizacyjne z katalogu w którym aplikacja została zarejestrowana.

W nowym środowisku, musisz podać **nazwa** dla aplikacji i wybierz polecenie **obsługiwane typy kont**. Opcjonalnie możesz podać **identyfikator URI przekierowania**. Jeśli podasz identyfikator URI przekierowania, należy określić, czy jest sieci web/public (mobilnych i klasycznych). Aby uzyskać więcej informacji o sposobie rejestrowania aplikacji za pomocą nowej rejestracji aplikacji środowiska, zobacz [ten przewodnik Szybki Start](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>Strony właściwości w starszej wersji

Miał starszego **właściwości** strony, która nie ma nowego środowiska. **Właściwości** bloku miała następujące pola: **Nazwa**, **obiektu o identyfikatorze**, **identyfikator aplikacji**, **identyfikator URI Identyfikatora aplikacji**, **Logo**, **adres URL strony głównej** , **Adres URL wylogowania**, **usługi adres URL warunków użytkowania**, **adres URL zasad zachowania poufności informacji**, **typ aplikacji**, i  **Wielodostępne.**

Poniżej przedstawiono, gdzie można znaleźć równoważne funkcje w nowym środowisku:

- **Nazwa**, **Logo**, **adres URL strony głównej**, **usługi adres URL warunków użytkowania**, i **adres URL zasad zachowania poufności informacji** jest teraz dostępny w aplikacji  **Znakowanie** strony.
- **Obiekt o identyfikatorze** i **identyfikator aplikacji (klienta)** znajduje się na **Przegląd** strony.
- Funkcje w wartości clientauthtrustmode **wielodostępnych** przełącznika w starszej wersji środowiska został zastąpiony przez **obsługiwane typy kont** na **uwierzytelniania** strony. Aby uzyskać więcej informacji na temat sposób mapowania wielodostępnych opcje typu konta obsługiwanych zobacz [ten przewodnik Szybki Start](quickstart-modify-supported-accounts.md).
- **Adres URL wylogowania** jest teraz dostępny w **uwierzytelniania** strony.
- **Typ aplikacji** już nie jest prawidłowym polem. Zamiast tego identyfikatory URI przekierowania (która znajduje się na **uwierzytelniania** strony) określić, jakie typy aplikacji są obsługiwane.
- **Identyfikator URI Identyfikatora aplikacji** jest teraz nazywana **identyfikator URI Identyfikatora aplikacji** i można je znaleźć na **uwidaczniania interfejsu API** bloku. W starszego interfejsu, ta właściwość auto zarejestrowano w następującym formacie: `https://{tenantdomain}/{appID}` (na przykład `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). W nowym formacie jest generowany automatycznie jako `api://{appID}`, ale należy go jawnie zapisany.

## <a name="reply-urlsredirect-urls"></a>Adresy URL/przekierowanie adresy URL odpowiedzi

W starszego interfejsu aplikacji było **adresy URL odpowiedzi** strony. W nowym środowisku, można znaleźć adresy URL odpowiedzi aplikacji **uwierzytelniania** sekcji. Dodatkowo, dlatego są nazywane **identyfikatory URI przekierowań**. Ponadto format przekierowania URI został zmieniony. Są one wymagane do skojarzenia z typem aplikacji (sieć web lub publicznego). Ponadto, ze względów bezpieczeństwa symbole wieloznaczne i http:// schematy nie są obsługiwane (z wyjątkiem produktów http://localhost).

## <a name="keyscertificates--secrets"></a>Klucze/certyfikatów i kluczy tajnych

W starszego interfejsu aplikacji było **klucze** strony. W nowym środowisku została zmieniona na **certyfikaty i klucze tajne**. Ponadto **kluczy publicznych** są określane jako **certyfikaty** i **haseł** są określane jako **wpisów tajnych klienta**.

## <a name="required-permissionsapi-permissions"></a>Uprawnienia wymagane uprawnienia/interfejsu API

- W starszego interfejsu aplikacji było **wymagane uprawnienia** strony. W nowym środowisku została zmieniona na **uprawnienia do interfejsu API**.
- Po wybraniu interfejsu API w starszej wersji środowiska, możesz wybrać z listy małych APIs firmy Microsoft lub wyszukiwanie za pomocą jednostki usługi w dzierżawie. W nowym środowisku można wybrać wiele kart: **Microsoft APIs**, **Moja organizacja korzysta z interfejsów API**, lub **Moje interfejsy API**. Pasek wyszukiwania w **interfejsów API w mojej organizacji** używa karta wyszukiwanie za pomocą jednostki usługi w dzierżawie. 

   > [!NOTE]
   > Jeśli aplikacja nie jest skojarzona z dzierżawą, nie będziesz widzieć na tej karcie. Aby uzyskać więcej informacji dotyczących sposobu wysyłania żądania uprawnień przy użyciu nowego środowiska, zobacz [ten przewodnik Szybki Start](quickstart-configure-app-access-web-apis.md).

- Miał starszego **udzielić uprawnień** znajdujący się u góry **żądane uprawnienia** strony. W nowym środowisko znajduje się **wyrazić zgody** sekcji z **udzielić zgody administratora** przycisku w aplikacji **uprawnienia do interfejsu API** sekcji. Ponadto istnieją pewne różnice w sposób funkcja przyciski:
   - W starszej wersji środowiska logika różnić w zależności od zalogowanego użytkownika oraz wymagane uprawnienia. Logika jest:
      - Jeśli tylko zażądano uprawnień możliwe zgody użytkownika i administratora nie jest zalogowany użytkownik, użytkownik mógł udzielić zgody użytkownika na żądane uprawnienia.
      - Jeśli zażądano co najmniej jedno uprawnienie, która wymaga zgody administratora, administrator nie jest zalogowany użytkownik użytkownika napotkała błąd podczas próby wyrazić zgody.
      - Jeśli administrator zalogowany użytkownik, zgody administratora uzyskał wszystkie żądane uprawnienia.
   - W nowym środowisku tylko administrator można udzielić zgody. Gdy administrator wybierze **udzielić zgody administratora** zgody administratora przycisku, zostanie ustanowione żądane uprawnienia.

## <a name="deleting-an-app-registration"></a>Usuwanie rejestracji aplikacji

W starszego interfejsu aplikacji było jako pojedynczej dzierżawy do usunięcia. Przycisk Usuń zostało wyłączone dla aplikacji wielodostępnych. W nowym środowisku można usunąć aplikacji w dowolnym stanie, ale musisz potwierdzić działanie. Aby uzyskać więcej informacji na temat usuwania rejestracje aplikacji, zobacz [ten przewodnik Szybki Start](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikacji

Starszy i nowego środowiska korzystają z różnych wersji dla formatu JSON w edytorze manifestu. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika

Istnieje nowy interfejs użytkownika dla właściwości, które można wcześniej można ustawić tylko przy użyciu edytora manifestu lub interfejsu API lub nie istnieje.

- **Niejawne udzielić przepływ** (oauth2AllowImplicitFlow) można znaleźć na **uwierzytelniania** strony. W odróżnieniu od starszej wersji doświadczenia, można włączyć **tokeny dostępu** lub **tokeny identyfikatora**, lub obu.
- **Zakresy definiowane przez ten interfejs API** (oauth2Permissions) i **autoryzowane aplikacje klienckie** (preAuthorizedApplications) można skonfigurować za pomocą **uwidaczniania interfejsu API** strony. Aby uzyskać więcej informacji na temat konfigurowania aplikacji do internetowego interfejsu API i udostępnić uprawnienia/zakresów, zobacz [ten przewodnik Szybki Start](quickstart-configure-app-expose-web-apis.md).
- **Domena wydawcy** (który jest wyświetlany użytkownikom na [monit o wyrażenie zgody aplikacji](application-consent-experience.md)) można znaleźć na **bloku znakowania** strony. Aby uzyskać więcej informacji na temat konfigurowania domeny wydawcy, zobacz [Instruktaż](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

- Nowe środowisko nie jest obecnie dostępna w dzierżawach usługi Azure AD B2C.
- Format hasła klienta (hasła) jest inny niż w przypadku starszego interfejsu i przerywa interfejsu wiersza polecenia.
- Zmiana wartości w przypadku obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Należy użyć manifest aplikacji, chyba że przełączasz się między usługą Azure AD pojedynczej dzierżawy i wieloma dzierżawami.
