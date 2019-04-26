---
title: Sposobu wypełniania określonych pól dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu wypełniania określonych pól podczas rejestrowania aplikacji opracowanych niestandardowej z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e4362e45ff8eee13b9203b9103266c60b4833a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299060"
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
| Typ aplikacji | **Sieci Web aplikacji/internetowy interfejs API**: Aplikację reprezentującą aplikację sieci web, internetowy interfejs API lub oba 
| |**Natywne**: Aplikację, którą można zainstalować na urządzeniu lub komputerze użytkownika           |
| Adres URL logowania      | Adres URL, w którym użytkownicy mogą logować się do korzystania z aplikacji                                  |

Po wypełnieniu pól powyżej, aplikacja jest zarejestrowana w witrynie Azure portal, a następnie nastąpi przekierowanie do strony aplikacji. **Ustawienia** przycisk w okienku aplikacji zostanie otwarty na stronie ustawień, które ma więcej pól, którą można dostosować aplikację. W poniższej tabeli opisano wszystkie pola na stronie ustawień. należy pamiętać, że byłaby widoczna tylko podzbiór tych pól, w zależności od tego, czy utworzona aplikacja sieci web lub aplikacji natywnej.

| Pole           | Opis                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identyfikator aplikacji  | Podczas rejestrowania aplikacji usługi Azure AD przypisze aplikacji, identyfikator aplikacji. Identyfikator może być używana do unikatowej identyfikacji aplikacji w żądaniach uwierzytelniania usługi Azure AD, a także uzyskać dostęp do zasobów aplikacji, np. interfejsu API programu Graph.                                                          |
| Identyfikator URI identyfikatora aplikacji      | Powinno to być unikatowy identyfikator URI, zwykle w formie **https://&lt;dzierżawy\_nazwa&gt;/&lt;aplikacji\_nazwa&gt;.** Jest on używany podczas przepływu Udziel autoryzacji, jako unikatowy identyfikator, aby określić zasób, który powinien być wystawiony token na. Staje się również w tokenie dostępu wystawione oświadczenie "roszczenia". |
| Przekaż nowe logo | Możesz użyć tego, aby przekazać logo aplikacji. Logo musi być w formacie BMP, jpg lub PNG, a rozmiar pliku powinien być mniejszy niż 100KB. Wymiary obrazu należy 215 x 215 pikseli i wymiary obrazu środkowego 94 x 94 piksele.                                                       |
| Adres URL strony głównej   | Jest to adres URL logowania wskazanej podczas rejestracji aplikacji.                                                                                                                                                                                                                                              |
| Adres URL wylogowywania      | Ten adres URL wylogowania wylogowania jednokrotnego. Usługa Azure AD wysyła żądanie wylogowania do tego adresu URL po użytkownik usunie zaznaczenie ich sesji z usługą Azure AD przy użyciu zarejestrowanej aplikacji.                                                                                                                                       |
| Z wieloma dzierżawami  | Ten przełącznik określa, czy aplikacja może być używana przez wielu dzierżawców. Zazwyczaj oznacza to, że zewnętrznych organizacje mogą używać aplikacji przez zarejestrowanie go w ramach ich dzierżawy i udzielanie dostępu do danych organizacji.                                                                   |
| Adresy URL odpowiedzi      | Odpowiedź adresy URL są punkty końcowe gdzie zwraca wszelkie tokeny żądań aplikacji w usłudze Azure AD.                                                                                                                                                                                                          |
| Identyfikatory URI przekierowania   | Dla natywnych aplikacji jest to, gdzie użytkownik jest wysyłane po pomyślnej autoryzacji. Usługa Azure AD czy identyfikator URI przekierowania aplikacji dostarcza żądania OAuth 2.0 pasuje do jednego z zarejestrowanych wartości w portalu.                                                            |
| Klucze            | Klucze można utworzyć w celu programowego dostępu do interfejsów API sieci web zabezpieczony przez usługę Azure AD bez żadnej interakcji użytkownika. Z \* \*klucze\* \* strony, wprowadź opis klucza i datę wygaśnięcia i Zapisz w celu wygenerowania klucza. Upewnij się go zapisać gdzieś bezpieczne, ponieważ nie można będzie później uzyskać dostęp do.             |

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](../manage-apps/what-is-application-management.md)
