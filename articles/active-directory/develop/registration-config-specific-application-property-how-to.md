---
title: Pola rejestracji portalu Azure dla aplikacji opracowanych na zamówienie
description: Wskazówki dotyczące rejestrowania niestandardowej aplikacji opracowanej za pomocą usługi Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 36d74b9926639bb4ec49821a3d73b5d615016394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702678"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Pola rejestracji portalu Azure dla aplikacji opracowanych na zamówienie

W tym artykule przedstawiono krótki opis wszystkich dostępnych pól w formularzu rejestracji aplikacji w [witrynie Azure portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

-   Aby zarejestrować nową aplikację, przejdź do [witryny Azure portal](https://portal.azure.com).

-   W lewym okienku nawigacji kliknij pozycję **Usługa Azure Active Directory.**

-   Wybierz **pozycję Rejestracje aplikacji** i kliknij pozycję **Dodaj**.

-   Spowoduje to otwarcie formularza rejestracyjnego zgłoszenia.

## <a name="fields-in-the-application-registration-form"></a>Pola w formularzu rejestracyjnym aplikacji

| Pole            | Opis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nazwa             | Nazwa aplikacji. Powinien mieć co najmniej cztery znaki.                |
| Obsługiwane typy konta| Wybierz konta, które chcesz obsługiwać aplikacji: tylko konta w tym katalogu organizacyjnym, konta w dowolnym katalogu organizacyjnym lub konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft.  |
| Przekierowanie identyfikatora URI (opcjonalnie) | Wybierz typ aplikacji, którą budujesz, **klienta sieci Web** lub klienta **publicznego (mobilnego & pulpitu),** a następnie wprowadź adres URI (lub adres URL odpowiedzi) dla aplikacji. W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg http://localhost:31544 może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego. W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla aplikacji, taką jak myapp://auth. Aby zobaczyć konkretne przykłady dla aplikacji internetowych lub aplikacji natywnych, zapoznaj się z naszymi [przewodnikami Szybki start.](https://docs.microsoft.com/azure/active-directory/develop)|

Po wypełnieniu powyższych pól aplikacja jest zarejestrowana w witrynie Azure portal i zostanie przekierowana do strony przeglądu aplikacji. Strony ustawień w lewym okienku w obszarze **Zarządzanie** mają więcej pól, aby dostosować aplikację. W poniższych tabelach opisano wszystkie pola. Zobaczysz tylko podzbiór tych pól, w zależności od tego, czy utworzono aplikację sieci web, czy publiczną aplikację kliencką.

### <a name="overview"></a>Omówienie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identyfikator aplikacji  | Podczas rejestrowania aplikacji usługa Azure AD przypisuje aplikacji identyfikator aplikacji. Identyfikator aplikacji może służyć do jednoznacznej identyfikacji aplikacji w żądaniach uwierzytelniania do usługi Azure AD, a także do uzyskiwania dostępu do zasobów, takich jak interfejs API wykresu.                                                          |
| Identyfikator URI identyfikatora aplikacji      | Powinien to być unikatowy identyfikator URI, zwykle formularza **https://&gt;nazwę aplikacji nazwy&gt;/&lt;\_&lt;dzierżawy.\_** Jest to używane podczas przepływu udzielania autoryzacji, jako unikatowy identyfikator, aby określić zasób, dla którego token powinien zostać wystawiony. Staje się również "aud" roszczenia w tokenie dostępu wystawionego. |

### <a name="branding"></a>Znakowanie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prześlij nowe logo | Można użyć tego, aby przesłać logo dla aplikacji. Logo musi być w formacie .bmp, jpg lub png, a rozmiar pliku powinien być mniejszy niż 100 KB. Wymiary obrazu powinny wynosić 215x215 pikseli, z centralnymi wymiarami obrazu 94x94 pikseli.|
| Adres URL strony głównej   | Jest to adres URL logowania określony podczas rejestracji aplikacji.|

### <a name="authentication"></a>Uwierzytelnianie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres URL wylogowywania      | Jest to adres URL wylogowania pojedynczego. Usługa Azure AD wysyła żądanie wylogowania do tego adresu URL, gdy użytkownik czyści sesję za pomocą usługi Azure AD przy użyciu innej zarejestrowanej aplikacji.|
| Obsługiwane typy konta  | Ten przełącznik określa, czy aplikacja może być używana przez wielu dzierżawców. Zazwyczaj oznacza to, że organizacje zewnętrzne mogą korzystać z aplikacji, rejestrując ją w dzierżawie i udzielając dostępu do danych swojej organizacji.|
| Adresy URL przekierowania      | Adresy URL przekierowania lub odpowiedzi są punktami końcowymi, w których usługa Azure AD zwraca wszystkie tokeny, których żąda aplikacja. W przypadku aplikacji natywnych jest to miejsce, w którym użytkownik jest wysyłany po pomyślnej autoryzacji. Usługa Azure AD sprawdza, czy identyfikator URI przekierowania dostarcza aplikacji w żądaniu OAuth 2.0 pasuje do jednej z zarejestrowanych wartości w portalu.|

### <a name="certificates-and-secrets"></a>Certyfikaty i wpisy tajne

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klucze tajne klienta            | Można utworzyć wpisy tajne klienta lub klucze, aby programowo uzyskiwać dostęp do internetowych interfejsów API zabezpieczonych przez usługę Azure AD bez żadnej interakcji z użytkownikiem. Na stronie **Nowy klucz tajny klienta** wprowadź opis klucza i datę wygaśnięcia i zapisz, aby wygenerować klucz. Pamiętaj, aby zapisać go w bezpiecznym miejscu, ponieważ nie będziesz mieć do niego dostępu później.             |

## <a name="next-steps"></a>Następne kroki

[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](../manage-apps/what-is-application-management.md)
