---
title: Rejestrowanie aplikacji zawierających poufne dane klienta w usłudze Azure Active Directory — interfejs API platformy Azure, dla FHIR
description: W tym artykule wyjaśniono, jak można zarejestrować aplikacji zawierających poufne dane klienta w usłudze Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 064cf4bb0b6e9454a4049cc6c32b51b09e5b2a53
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824132"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Rejestrowanie aplikacji zawierających poufne dane klienta w usłudze Azure Active Directory

W tym artykule dowiesz się, jak można zarejestrować aplikacji zawierających poufne dane klienta w usłudze Azure Active Directory. Rejestrowanie aplikacji klienta jest reprezentacja aplikację, która może służyć do uwierzytelniania w imieniu użytkownika i żądania dostępu do usługi Azure Active Directory [aplikacji zasobów](register-resource-azure-ad-client-app.md). Aplikacja kliencka poufne jest aplikacja, która może charakteryzować się do przechowywania klucza tajnego i prezentować ten klucz tajny, żądając tokenów dostępu. Przykłady aplikacji zawierających poufne dane są aplikacje po stronie serwera.

Aby zarejestrować nowej aplikacji zawierających poufne dane w portalu, wykonaj poniższe kroki.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w witrynie Azure portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W **usługi Azure Active Directory** bloku kliknij pozycję **rejestracje aplikacji (wersja zapoznawcza)**:

    ![Witryna Azure portal. Rejestrowanie nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij przycisk **nowej rejestracji**.

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

1. Nadaj aplikacji nazwę wyświetlaną.

2. Podaj adres URL odpowiedzi. Te informacje można zmienić później, ale jeśli znasz adres URL odpowiedzi aplikacji, wprowadź go teraz.

    ![Rejestracja nowej aplikacji poufne klienta.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Następnie Dodaj uprawnienia do interfejsu API:

1. Otwórz **uprawnienia do interfejsu API** i wybierz swoje [rejestracji aplikacji zasobu interfejsu API FHIR](register-resource-azure-ad-client-app.md):

    ![Poufne klienta. Uprawnienia do interfejsu API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Kliknij przycisk **Dodaj uprawnienia**

3. Wybierz interfejs API zasobów:

    ![Poufne klienta. Moje interfejsy API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Wybierz zakresy (uprawnienia), które aplikacji zawierających poufne dane powinny być w stanie poprosić o w imieniu użytkownika:

    ![Poufne klienta. Uprawnienia delegowane](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Klucz tajny aplikacji

1. Utwórz klucz tajny aplikacji (klucz tajny klienta):

    ![Poufne klienta. Klucz tajny aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Podaj opis i czas trwania klucza tajnego.

3. Wygenerowany, która będzie wyświetlana w portalu tylko raz. Zanotuj je i przechowuj w bezpiecznym miejscu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wyjaśniono sposób rejestrowania aplikacji zawierających poufne dane klienta w usłudze Azure Active Directory. Następnie należy wdrożyć interfejs API FHIR na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-oss-powershell-quickstart.md)