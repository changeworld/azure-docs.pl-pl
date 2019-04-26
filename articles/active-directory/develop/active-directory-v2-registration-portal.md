---
title: Tematy pomocy z portalu rejestracji aplikacji | Dokumentacja firmy Microsoft
description: Opis różnych funkcji w portalu rejestracji aplikacji firmy Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411219"
---
# <a name="app-registration-reference"></a>Dokumentacja rejestracji aplikacji
Ten dokument zawiera kontekstu i opisy różnych funkcji dostępnych w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Firma Microsoft nie będą obsługiwać rejestrowanie aplikacji i zarządzaniem nimi osiągnięcia zbieżności i Azure AD w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) od maja 2019 r. Zaleca się zarządzanie istniejącymi aplikacjami i zarejestrować nowe aplikacje za pomocą [rejestracje aplikacji (wersja zapoznawcza)](https://aka.ms/appregistrations) środowisko w witrynie Azure portal.

## <a name="my-applications-or-converged-applications"></a>Moje aplikacje lub zbieżne aplikacje
Ta lista zawiera wszystkie aplikacje zarejestrowana do użycia z punktem końcowym v2.0 usługi Azure AD. Te aplikacje mają możliwość logowanie użytkowników mających konta służbowe z usługi Azure Active Directory i osobistych kont Microsoft. Aby dowiedzieć się więcej na temat punktu końcowego v2.0 usługi Azure AD, zobacz [Przegląd v2.0](active-directory-appmodel-v2-overview.md). Aplikacje te można również zintegrować z punktu końcowego uwierzytelniania konta Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Tylko aplikacje usługi Azure AD
Ta lista zawiera wszystkie aplikacje zarejestrowana do użycia z punktem końcowym usługi Azure AD w wersji 1.0. Te aplikacje mają tylko możliwość logowanie użytkowników mających konta służbowe z usługi Azure Active Directory. Ta lista zawiera aplikacje, które zostały zarejestrowane przy użyciu **rejestracje aplikacji** środowiska w [witryny Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Zestaw Live SDK aplikacje
Ta lista zawiera wszystkie aplikacje zarejestrowana do użycia wyłącznie przy użyciu konta Microsoft. Nie są włączone do użycia z usługą Azure Active Directory. Jest to, gdzie znaleźć wszystkie aplikacje, które zostały wcześniej zarejestrowane za pomocą portalu dla deweloperów MSA w `https://account.live.com/developers/applications`. Wszystkie funkcje, które były wykonywane w `https://account.live.com/developers/applications` można teraz wykonać w nowym portalu, `https://apps.dev.microsoft.com`.

## <a name="application-secrets"></a>Wpisy tajne aplikacji
Wpisy tajne aplikacji są poświadczenia, które umożliwią niezawodnych aplikacji do wykonywania [uwierzytelnianie klienta](https://tools.ietf.org/html/rfc6749#section-2.3) z usługą Azure AD. W OAuth i OpenID Connect, wpis tajny aplikacji jest często nazywany `client_secret`. Za pomocą protokołu w wersji 2.0, każda aplikacja, która odbiera token zabezpieczający lokalizacji adresy sieci web (przy użyciu `https` schemat) należy użyć wpis tajny aplikacji do zidentyfikowania się do usługi Azure AD podczas realizacji tego tokenu zabezpieczającego. Ponadto natywnego klienta, który odbiera tokenów na urządzeniu z systemem będzie zabronione z klucz tajny aplikacji w celu przeprowadzenia uwierzytelniania klienta. Przechowywanie kluczy tajnych w środowiskach niezabezpieczone to zniechęcać.

Każda aplikacja może zawierać dwóch wpisów tajnych aplikacji prawidłowy w danym momencie. Dzięki przechowywaniu dwóch wpisów tajnych, masz możliwość wykonywania okresowe przerzucania klucza w środowisku całej aplikacji. Gdy zakończysz migrację całej aplikacji, aby nowe hasło, możesz usunąć stary klucz tajny i aprowizować nowe.

W tej chwili w portalu rejestracji aplikacji dozwolone są tylko dwa typy wpisów tajnych aplikacji. Wybieranie **wygenerować nowe hasło** generuje i wspólny klucz tajny są przechowywane w magazynie odpowiednich danych, który można użyć w aplikacji. Wybieranie **Generuj nową parę klucz** tworzy nową parę kluczy publiczny/prywatny, który można pobrać i używany do uwierzytelniania klientów do usługi Azure AD. Wybieranie **Przekaż klucz publiczny** pozwala na używanie własnych pary kluczy publiczny/prywatny.
Musisz przekazać certyfikat, który zawiera klucz publiczny.

## <a name="profile"></a>Profil
Sekcji profilu w portalu rejestracji aplikacji może służyć do dostosowania strony logowania w aplikacji. W tej chwili można zmieniać logowania strony aplikacji logo, warunków użytkowania adres URL usługi i adres URL zasad zachowania poufności informacji. Logo musi być przezroczystym obrazem o wymiarach 48 x 48 lub 50 x 50 pikseli w formacie pliku GIF, PNG lub JPEG o maksymalnym rozmiarze 15 KB. Spróbuj zmianę wartości i wyświetlanie wynikowych strony logowania!

## <a name="live-sdk-support"></a>Zestaw Live SDK pomocy technicznej
Po włączeniu "Zestaw SDK pomocy technicznej na żywo" do usługi Azure AD będą udostępniane żadnych wpisów tajnych aplikacji, możesz utworzyć i magazynów danych Account firmy Microsoft. Dzięki temu aplikację, aby łączyć bezpośrednio z usługi Microsoft Account (login.live.com). Jeśli chcesz tworzyć aplikacje przy użyciu Account Microsoft bezpośrednio (zamiast przy użyciu punktu końcowego v2.0 usługi Azure AD), należy upewnić się, czy włączona jest obsługa zestawu SDK na żywo.

Wyłączanie obsługi zestawu Live SDK zapewnia, że klucz tajny aplikacji wyłącznie zostanie zapisane dane usługi Azure AD przechowywania. Dane usługi Azure AD magazynu zawiera wykonawczych przeznaczonych dla przedsiębiorstw, umożliwiających spełnić pewne kryteria, takich jak zgodność ustawy FISMA. Jeśli zostanie włączona obsługa zestawu Live SDK, aplikacja może nie osiągnąć z niektórych z tych standardów.

Jeśli planowane jest tylko użycie punktu końcowego v2.0 usługi Azure AD, można bezpiecznie wyłączyć Obsługa zestawu Live SDK.

