---
title: Rejestrowanie aplikacji usługi Azure Active Directory — interfejs API platformy Azure, dla FHIR
description: W tym artykule wyjaśniono, jak zarejestrować aplikację usługi Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824174"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Zarejestruj aplikacja kliencka usługi Azure Active Directory

W tym artykule dowiesz się, jak zarejestrować aplikacji klienckiej usługi Azure Active Directory. Rejestracje aplikacji klienta są usługi Azure Active Directory oświadczenia aplikacji, które mogą służyć do uwierzytelniania i uzyskiwania tokenów. Klient usługi jest przeznaczona do użycia przez aplikację do uzyskania tokenu dostępu bez interakcyjnego uwierzytelniania użytkownika. Wówczas mają pewne uprawnienia aplikacji i użyć klucza tajnego aplikacji (hasło) podczas uzyskiwania tokenów dostępu.

Wykonaj poniższe kroki, aby utworzyć nowy klient usługi.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w witrynie Azure portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W **usługi Azure Active Directory** bloku kliknij pozycję **rejestracje aplikacji (wersja zapoznawcza)**:

    ![Witryna Azure portal. Rejestrowanie nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij przycisk **nowej rejestracji**.

## <a name="service-client-application-details"></a>Szczegóły aplikacji klienta usługi

* Klient usługi muszą nazwę wyświetlaną i możesz także podać adres URL odpowiedzi, ale jej zwykle nie będą używane.

    ![Witryna Azure portal. Usługa klienta Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Należy przyznać klienta usługi ról aplikacji. 

1. Otwórz **uprawnienia do interfejsu API** i wybierz swoje [rejestracji aplikacji zasobu interfejsu API FHIR](register-resource-azure-ad-client-app.md):

    ![Witryna Azure portal. Uprawnienia do interfejsu API klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Wybierz jedną z ról aplikacji z tymi, które zdefiniowano w aplikacji zasobów:

    ![Witryna Azure portal. Uprawnienia aplikacji klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Udziel zgody na aplikację. Jeśli nie masz wymaganych uprawnień, skontaktuj się z administratorem usługi Azure Active Directory:

    ![Witryna Azure portal. Zgoda administratora klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Klucz tajny aplikacji

Klient usługi musi secret (hasło), które będą używane podczas uzyskiwania tokenów.

1. Kliknij przycisk **certyfikaty &amp; wpisów tajnych**

2. Kliknij przycisk **nowy wpis tajny klienta**

    ![Witryna Azure portal. Klucz tajny klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Podaj czas trwania klucza tajnego.

4. Po jego wygenerowaniu jej będzie wyświetlane tylko raz w portalu. Zanotuj je i przechowuj w bezpieczny sposób.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wyjaśniono sposób rejestrowania aplikacji klienckiej usługi Azure Active Directory. Następnie należy wdrożyć interfejs API FHIR na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-oss-powershell-quickstart.md)