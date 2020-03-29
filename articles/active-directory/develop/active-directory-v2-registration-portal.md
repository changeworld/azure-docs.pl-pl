---
title: Informacje o portalu rejestracji aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Opis funkcji w portalu rejestracji aplikacji firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 26ebee446523c138569b9d5379c9a5e1b9e93e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698445"
---
# <a name="app-registration-reference"></a>Dokumentacja rejestracji aplikacji

Ten dokument zawiera kontekst i opisy różnych funkcji znalezionych w środowiska [rejestracji aplikacji](https://aka.ms/appregistrations) w witrynie Azure portal.

## <a name="my-applications-or-converged-applications"></a>Moje aplikacje lub aplikacje konwergentne

Ta lista zawiera wszystkie aplikacje zarejestrowane do użytku z punktem końcowym platformy tożsamości firmy Microsoft (wersja 2.0). Te aplikacje mają możliwość logowania użytkowników za pomocą osobistych kont Microsoft i kont służbowych/szkolnych z usługi Azure Active Directory. Aby dowiedzieć się więcej o punkcie końcowym platformy tożsamości, zobacz [omówienie wersji 2.0](active-directory-appmodel-v2-overview.md). Aplikacje te mogą być również używane do integracji z punktem końcowym uwierzytelniania konta Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Aplikacje tylko usługi Azure AD

Ta lista zawiera wszystkie aplikacje zarejestrowane do użytku z punktem końcowym usługi Azure AD w wersji 1.0. Te aplikacje mają tylko możliwość logowania użytkowników za pomocą kont służbowych/szkolnych z usługi Azure Active Directory. Ta lista zawiera aplikacje, które zostały **zarejestrowane** przy użyciu środowiska rejestracji aplikacji w [witrynie Azure portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplikacje SDK na żywo

Ta lista zawiera wszystkie aplikacje zarejestrowane do użytku wyłącznie z kontem Microsoft. Nie są one włączone do użytku z usługą Azure Active Directory. W tym miejscu można znaleźć wszystkie aplikacje, które zostały `https://account.live.com/developers/applications`wcześniej zarejestrowane w portalu deweloperskim MSA pod adresem . Wszystkie funkcje, które `https://account.live.com/developers/applications` zostały wcześniej wykonane w mogą być teraz wykonywane w [rejestracji aplikacji](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Tajemnice aplikacji

Wpisy tajne aplikacji to poświadczenia, które umożliwiają aplikacji wykonywanie [niezawodnego uwierzytelniania klienta za](https://tools.ietf.org/html/rfc6749#section-2.3) pomocą usługi Azure AD. W OAuth & OpenID Connect klucz tajny aplikacji jest powszechnie `client_secret`określany jako . W protokole w wersji 2.0 każda aplikacja, która odbiera `https` token zabezpieczający w lokalizacji adresowalnej sieci Web (przy użyciu schematu) musi używać klucza tajnego aplikacji do identyfikowania się w usłudze Azure AD po wykupieniu tego tokenu zabezpieczającego. Ponadto każdy klient macierzysty, który odbiera tokeny na urządzeniu, będzie zabroniony przy użyciu klucza tajnego aplikacji do wykonywania uwierzytelniania klienta. Zniechęca to do przechowywania wpisów tajnych w niezabezpieczonych środowiskach.

Każda aplikacja może zawierać dwa prawidłowe wpisy tajne aplikacji w danym momencie. Zachowując dwa wpisy tajne, masz możliwość wykonywania okresowego przerzucenia klucza w całym środowisku aplikacji. Po migracji całej aplikacji do nowego klucza tajnego można usunąć stary klucz tajny i aprowizować nowy.

W tej chwili tylko dwa typy wpisów tajnych aplikacji są dozwolone w portalu rejestracji aplikacji. Wybranie **opcji Generuj nowe hasło** generuje i przechowuje wspólny klucz tajny w odpowiednim magazynie danych, którego można używać w aplikacji. Wybranie **opcji Generuj nową parę kluczy** tworzy nową parę kluczy publicznych i prywatnych, które można pobrać i użyć do uwierzytelniania klienta na platformie Azure AD. Wybranie **opcji Przekaż klucz publiczny** umożliwia korzystanie z własnej pary kluczy publicznych/prywatnych.
Wymagane jest przekazanie certyfikatu zawierającego klucz publiczny.

## <a name="profile"></a>Profil

Sekcja profilu portalu rejestracji aplikacji może służyć do dostosowywania strony logowania dla aplikacji. W tej chwili możesz zmienić logo aplikacji strony logowania, adres URL warunków usługi i adres URL zasad zachowania poufności informacji. Logo musi być przezroczystym obrazem 48 x 48 lub 50 x 50 pikseli w pliku GIF, PNG lub JPEG o rozmiarze 15 KB lub mniejszym. Spróbuj zmienić wartości i wyświetlając wynikową stronę logowania!

## <a name="live-sdk-support"></a>Obsługa sdk na żywo

Po włączeniu "Obsługa live SDK", wszelkie utworzone wpisy tajne aplikacji zostaną aprowione zarówno w magazynach danych usługi Azure AD, jak i microsoft account. Dzięki temu aplikacja może zintegrować się bezpośrednio z usługą konta Microsoft (login.live.com). Jeśli chcesz utworzyć aplikację przy użyciu konta Microsoft bezpośrednio (w przeciwieństwie do korzystania z punktu końcowego w wersji 2.0), należy upewnić się, że obsługa live SDK jest włączona.

Wyłączenie obsługi zestawów SDK na żywo gwarantuje, że klucz tajny aplikacji jest zapisywany tylko w magazynie danych usługi Azure AD. Magazyn danych usługi Azure AD zawiera przepisy klasy korporacyjnej, które umożliwiają spełnienie pewnych standardów, takich jak zgodność z zasadami FISMA. Jeśli włączysz obsługę live SDK, aplikacja może nie osiągnąć zgodności z niektórymi z tych standardów.

Jeśli kiedykolwiek planujesz używać punktu końcowego w wersji 2.0, można bezpiecznie wyłączyć obsługę live SDK.
