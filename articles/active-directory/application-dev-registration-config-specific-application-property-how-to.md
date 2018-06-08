---
title: Wypełniania określonych pól dla aplikacji utworzonych niestandardowych | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu wypełniania określonych pól podczas rejestrowania aplikacji niestandardowej rozwinięte z usługą Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cd4313efb5d08842ba12ec00e6e5160214800d56
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "34055621"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Wypełniania określonych pól dla aplikacji utworzonych niestandardowych

Ten artykuł zawiera krótki opis dostępnych pól formularza rejestracji aplikacji w [portalu Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Zarejestrować nową aplikację

-   Aby zarejestrować nową aplikację, przejdź do [portalu Azure](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory.**

-   Wybierz **rejestracji aplikacji** i kliknij przycisk **Dodaj**.

-   To otwarcie formularza rejestracji aplikacji.

## <a name="fields-in-the-application-registration-form"></a>Pola formularza rejestracji aplikacji


| Pole            | Opis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Nazwa)             | Nazwa aplikacji. Powinien mieć co najmniej cztery znaki.                |
| Typ aplikacji | **Sieci Web aplikacji/interfejs API sieci Web**: aplikacja, która reprezentuje aplikacji sieci web, interfejs API sieci web lub obu 
| |**Natywny**: aplikację, którą można zainstalować na urządzeniu lub komputerze użytkownika           |
| Adres URL logowania      | Adres URL, w którym użytkownicy mogą rejestrować w korzystać z aplikacji                                  |

Po wprowadzeniu powyższego pola aplikacji jest zarejestrowany w portalu Azure, a użytkownik zostanie przekierowany do strony aplikacji. **Ustawienia** przycisk w okienku aplikacji otwartej stronie "ustawienia" ma więcej pól, które można dostosować aplikację. W poniższej tabeli opisano wszystkie pola na stronie ustawień. należy pamiętać, że byłaby widoczna tylko podzbiór tych pól, w zależności od tego, czy podczas tworzenia aplikacji sieci web lub aplikacji natywnej.

| Pole           | Opis                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identyfikator aplikacji  | Podczas rejestrowania aplikacji usługi Azure AD przypisuje aplikacji identyfikator aplikacji. Identyfikator może być używane do unikatowego identyfikowania aplikacji żądania uwierzytelniania do usługi Azure AD, a także uzyskiwać dostęp do zasobów aplikacji, takich jak interfejsu API programu Graph.                                                          |
| Identyfikator URI identyfikatora aplikacji      | Należy to unikatowy identyfikator URI, zazwyczaj formę **https://&lt;dzierżawy\_nazwa&gt;/&lt;aplikacji\_nazwa&gt;.** Jest on używany podczas przepływu grant autoryzacji, jako unikatowy identyfikator, aby określić zasób, który powinien być wydano tokenu dla. Staje się również w tokenie dostępu wystawione oświadczenie "lub". |
| Przekaż nowe logo | Możesz użyć tego, aby przekazać logo aplikacji. Logo musi być w formacie BMP, jpg lub PNG, a rozmiar pliku powinna być mniejsza niż 100KB. Wymiary obrazu należy 215 x 215 pikseli i wymiary obrazu centralnej 94 x 94 pikseli.                                                       |
| Adres URL strony głównej   | Jest to adres URL logowania określony podczas rejestracji aplikacji.                                                                                                                                                                                                                                              |
| Adres URL wylogowywania      | Ten adres URL wylogowania wylogowania pojedynczego. Usługi Azure AD wysyła żądanie wylogowania do tego adresu URL, gdy użytkownik usuwa sesję z usługą Azure AD przy użyciu zarejestrowanej aplikacji.                                                                                                                                       |
| Z wieloma dzierżawami  | Ten parametr określa, czy aplikacji mogą być używane przez wiele dzierżaw. Zwykle oznacza to, organizacje zewnętrzne można używać aplikacji przez zarejestrowanie go w swojej dzierżawy i udzielanie dostępu do danych używanych w organizacji.                                                                   |
| Adresy URL odpowiedzi      | Odpowiedź adresy URL są punkty końcowe gdzie zwraca wszystkie tokeny żądań aplikacji w usłudze Azure AD.                                                                                                                                                                                                          |
| Identyfikatory URI przekierowania   | Dla natywnych aplikacji jest wysyłania użytkownika po pomyślnej autoryzacji. Sprawdzenie Azure AD, dostarczającego przekierowania URI aplikacji w żądaniu protokołu OAuth 2.0 pasuje do jednej z wartości zarejestrowany w portalu.                                                            |
| Klucze            | Można utworzyć klucze do programowego dostępu do interfejsów API sieci web zabezpieczonych przez usługi Azure AD bez interakcji użytkownika. Z \* \*klucze\* \* strony, wprowadź opis klucza i datę wygaśnięcia i zapisywanie do wygenerowania klucza. Upewnij się zapisać go innym bezpieczne, ponieważ nie będzie można później uzyskać dostęp.             |

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](manage-apps/what-is-application-management.md)
