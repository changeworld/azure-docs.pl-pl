---
title: Sposobu wypełniania określonych pól dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu wypełniania określonych pól podczas rejestrowania aplikacji opracowanych niestandardowej z usługą Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01ff1e2d0c9bc926d54bd54716e0579ef395ec0
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655993"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Sposobu wypełniania określonych pól dla aplikacji niestandardowej

Ten artykuł zawiera krótki opis dostępnych pól w formularzu rejestracji aplikacji w [witryny Azure portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

-   Aby zarejestrować nową aplikację, przejdź do [witryny Azure portal](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory.**

-   Wybierz **rejestracje aplikacji** i kliknij przycisk **Dodaj**.

-   To otwarcie formularza rejestracji aplikacji.

## <a name="fields-in-the-application-registration-form"></a>Pola w formularzu rejestracji aplikacji


| Pole            | Opis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Nazwa)             | Nazwa aplikacji. Powinien mieć co najmniej cztery znaki.                |
| Obsługiwane typy konta| Wybierz pozycję konta, które chcesz aplikacji do obsługi: kont w tym katalogu organizacji tylko kont w dowolnym katalogu organizacji i kont w dowolnym katalogu organizacji i osobistych kont Microsoft.  |
| (Opcjonalnie) identyfikator URI przekierowania | Wybierz typ aplikacji, którą tworzysz **Web** lub **klientem publicznym (mobilnych i klasycznych)** , a następnie wprowadź identyfikator URI przekierowania (lub adres URL odpowiedzi) dla aplikacji. W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg http://localhost:31544 może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego. W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną do aplikacji, takich jak myapp://auth. Aby zobaczyć konkretne przykłady dotyczące aplikacji internetowych lub natywnych, zapoznaj się z [przewodnikami Szybki start](https://docs.microsoft.com/azure/active-directory/develop).|

Po wypełnieniu pól powyżej, aplikacja jest zarejestrowana w witrynie Azure portal, a następnie nastąpi przekierowanie do strony Przegląd aplikacji. Ze strony ustawień w okienku po lewej stronie w obszarze **Zarządzaj** mają więcej pól, którą można dostosować aplikację. W poniższych tabelach opisano wszystkie pola. Byłaby widoczna tylko podzbiór tych pól, w zależności od tego, utworzyć aplikację sieci web lub klientem publicznym.

### <a name="overview"></a>Omówienie
| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identyfikator aplikacji  | Podczas rejestrowania aplikacji usługi Azure AD przypisze aplikacji, identyfikator aplikacji. Identyfikator może być używana do unikatowej identyfikacji aplikacji w żądaniach uwierzytelniania usługi Azure AD, a także uzyskać dostęp do zasobów aplikacji, np. interfejsu API programu Graph.                                                          |
| Identyfikator URI Identyfikatora aplikacji      | Powinno to być unikatowy identyfikator URI, zwykle w formie **https://&lt;dzierżawy\_nazwa&gt;/&lt;aplikacji\_nazwa&gt;.** Jest on używany podczas przepływu Udziel autoryzacji, jako unikatowy identyfikator, aby określić token powinien być wystawiane dla zasobu. Staje się również w tokenie dostępu wystawione oświadczenie "roszczenia". |

### <a name="branding"></a>Znakowanie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Przekaż nowe logo | Możesz użyć tego, aby przekazać logo aplikacji. Logo musi być w formacie BMP, jpg lub PNG, a rozmiar pliku powinien być mniejszy niż 100 KB. Wymiary obrazu należy 215 x 215 pikseli i wymiary obrazu środkowego 94 x 94 piksele.|
| Adres URL strony głównej   | Jest to adres URL logowania wskazanej podczas rejestracji aplikacji.|

### <a name="authentication"></a>Authentication

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres URL wylogowywania      | To jest adres URL wylogowania jednokrotnego wylogowania. Usługa Azure AD wysyła żądanie wylogowania do tego adresu URL po użytkownik usunie zaznaczenie ich sesji z usługą Azure AD przy użyciu zarejestrowanej aplikacji.|
| Obsługiwane typy konta  | Ten przełącznik określa, czy aplikacja może być używana przez wielu dzierżawców. Zazwyczaj oznacza to, że zewnętrznych organizacje mogą używać aplikacji przez zarejestrowanie go w ramach ich dzierżawy i udzielanie dostępu do danych organizacji.|
| Adresy URL przekierowania      | Przekieruj lub odpowiedź adresy URL są punkty końcowe gdzie zwraca wszelkie tokeny żądań aplikacji w usłudze Azure AD. Dla natywnych aplikacji jest to, gdzie użytkownik jest wysyłane po pomyślnej autoryzacji. Usługi Azure AD umożliwia sprawdzenie, czy aplikacji dostarcza żądania OAuth 2.0 identyfikatora URI przekierowania pasuje do jednego z zarejestrowanych wartości w portalu.|

### <a name="certificates-and-secrets"></a>Certyfikaty i klucze tajne

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wpisy tajne klienta            | Klucze tajne i klucze do programowego dostępu do interfejsów API sieci web zabezpieczony przez usługę Azure AD bez żadnej interakcji użytkownika, można utworzyć klienta. Z **nowy wpis tajny klienta** strony, wprowadź opis klucza i datę wygaśnięcia i Zapisz w celu wygenerowania klucza. Upewnij się go zapisać gdzieś bezpieczne, ponieważ nie można będzie później uzyskać dostęp do.             |

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](../manage-apps/what-is-application-management.md)
