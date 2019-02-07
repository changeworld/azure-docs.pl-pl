---
title: Rejestrowanie aplikacji publicznych klienta usługi Azure Active Directory — interfejs API platformy Azure, dla FHIR
description: W tym artykule wyjaśniono, jak można zarejestrować aplikacji publicznych klienta w usłudze Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824162"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Rejestrowanie aplikacji publicznych klienta w usłudze Azure Active Directory

W tym artykule dowiesz się, jak można zarejestrować publicznych aplikacji w usłudze Azure Active Directory. Rejestracje aplikacji klienta są usługi Azure Active Directory oświadczenia aplikacji, które mogą uwierzytelniać i poproś o uprawnienia do interfejsu API w imieniu użytkownika. Klienci są aplikacje, takie jak aplikacje mobilne i jednej strony aplikacji javascript, które wpisy tajne nie może przechowywać poufne. Procedura jest podobna do [rejestrowanie klientowi poufnemu](register-confidential-azure-ad-client-app.md), ale ponieważ klienci nie jest zaufany na potrzeby przechowywania wpis tajny aplikacji, nie ma potrzeby dodać.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w witrynie Azure portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W **usługi Azure Active Directory** bloku kliknij **rejestracje aplikacji (wersja zapoznawcza)**:

    ![Witryna Azure portal. Rejestrowanie nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij przycisk **nowej rejestracji**.

## <a name="application-registration-overview"></a>Omówienie rejestracji aplikacji

1. Nadaj aplikacji nazwę wyświetlaną.

2. Podaj adres URL odpowiedzi. Adres URL odpowiedzi to, gdzie kody uwierzytelniania zostanie zwrócony do aplikacji klienckiej. Można dodać więcej adresów URL odpowiedzi i edytować istniejące zadania później.

    ![Witryna Azure portal. Publiczne Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Podobnie jak [poufne klienta aplikacji](register-confidential-azure-ad-client-app.md), musisz wybrać uprawnienia interfejsu API, które powinny mieć możliwość żądania w imieniu użytkowników tej aplikacji:

1. Otwórz **uprawnienia do interfejsu API** i wybierz swoje [rejestracji aplikacji zasobu interfejsu API FHIR](register-resource-azure-ad-client-app.md):

    ![Witryna Azure portal. Nowe uprawnienia publicznego interfejsu API.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. Wybierz zakresy, które chcesz mieć możliwość żądania aplikacji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wyjaśniono sposób rejestrowania aplikacji publicznych klienta w usłudze Azure Active Directory. Następnie Wdróż FHIR interfejsu API na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-oss-powershell-quickstart.md)
