---
title: Rejestrowanie zasobów aplikacji w usłudze Azure Active Directory — interfejs API platformy Azure, dla FHIR
description: W tym artykule wyjaśniono, jak można zarejestrować aplikacji zasobu w usłudze Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e8305c5a69fa3fda29f4f1292b7faa59f8ec3608
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870150"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Rejestrowanie zasobów aplikacji w usłudze Azure Active Directory

W tym artykule dowiesz się, jak zarejestrować zasobów (lub interfejsu API) aplikacji w usłudze Azure Active Directory. Aplikacja zasobów jest reprezentacją usługi Azure Active Directory serwera FHIR sam interfejs API i aplikacje klienckie mogą żądać dostępu do zasobu podczas uwierzytelniania. Aplikacja zasobów jest również nazywany *odbiorców* w sprzężeniem OAuth.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w witrynie Azure portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W **usługi Azure Active Directory** bloku kliknij pozycję **rejestracje aplikacji (wersja zapoznawcza)**:

    ![Witryna Azure portal. Rejestrowanie nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij przycisk **nowej rejestracji**.

## <a name="add-a-new-application-registration"></a>Dodawanie nowej rejestracji aplikacji

Wypełnij szczegóły nowej aplikacji. Istnieją ma określonych wymagań dotyczących nazwy wyświetlanej, ale ustawienie identyfikatora URI serwera FHIR można łatwo znaleźć:

![Rejestrowanie nowej aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>Ustaw identyfikator URI i Definiowanie zakresów

Aplikacja zasobów ma identyfikator URI (identyfikator URI Identyfikatora aplikacji), które klienci mogą używać podczas żądania dostępu do zasobu. Ta wartość zostanie wypełniony `aud` oświadczeń tokenu dostępu. Zaleca się, że ustawiony jest ten identyfikator URI jako identyfikator URI serwera FHIR. Dla inteligentnych aplikacji FHIR, zakłada się, że *odbiorców* jest identyfikator URI serwera FHIR.

1. Kliknij przycisk **uwidaczniania interfejsu API**

2. Kliknij przycisk **ustaw** obok *identyfikator URI Identyfikatora aplikacji*.

3. Wprowadź identyfikator URI, a następnie kliknij przycisk **Zapisz**. Dobry identyfikator URI będzie identyfikator URI serwera FHIR.

4. Kliknij przycisk **Dodaj zakres** i dodać wszystkie zakresy, które chcesz zdefiniować dla Ciebie interfejsu API. Usługa Azure AD nie zezwala obecnie ukośniki (`/`) w zakresie nazwy. Firma Microsoft zaleca używanie `$` zamiast tego. Zakres, takich jak `patient/*.read` będzie `patient$*.read`.

    ![Grupy odbiorców i zakresu](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>Definiowanie ról aplikacji

Interfejs API Azure FHIR i serwerem FHIR OSS dla korzystanie z platformy Azure [ról aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) kontroli dostępu opartej na rolach. Aby zdefiniować, które role powinna być dostępna dla Twojego interfejsu API serwera FHIR, Otwórz aplikację zasobu [manifestu](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/):

1. Kliknij przycisk **manifestu**:

    ![Role aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. W `appRoles` właściwości, Dodaj role chcesz użytkowników lub aplikacji, aby:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wyjaśniono sposób rejestrowania aplikacji zasobów w usłudze Azure Active Directory. Następnie należy wdrożyć interfejs API FHIR na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-oss-powershell-quickstart.md)