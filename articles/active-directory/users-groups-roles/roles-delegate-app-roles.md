---
title: Delegowanie ról administratora aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie dostępem do aplikacji, delegowanie ról, aby przyznać prawa uprawnień w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469132"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegowanie ról administratora aplikacji w usłudze Azure Active Directory

 Usługa Azure AD umożliwia delegowanie zarządzania dostęp do aplikacji zestaw wbudowanych ról administracyjnych. Oprócz administratora globalnego obciążenie, delegowanie specjalne uprawnienia, aby zarządzać zadaniami dostępu do aplikacji można zwiększyć poziom bezpieczeństwa i zmniejszyć ryzyko uzyskania nieautoryzowanego dostępu. Delegowanie problemy i ogólne wytyczne, które zostały omówione w [delegowanie administracji w usłudze Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegowanie administracji w aplikacji

Następujące role przyznać uprawnienia do zarządzania rejestracje aplikacji, ustawień rejestracji jednokrotnej, użytkownika i przypisania grupy i wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji (z wyjątkiem programu Microsoft Graph i Azure AD Graph). Jedyna różnica polega na tym, że roli administratora aplikacji również przyznaje uprawnienia do zarządzania ustawieniami serwera Proxy aplikacji. Żadna rola daje możliwość zarządzania ustawieniami dostępu warunkowego.
> [!IMPORTANT]
> Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Ta personifikacji tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może zrobić w obszarze swoje przypisania roli w usłudze Azure AD. Użytkownicy przypisani do tej roli mogą potencjalnie utworzyć lub zaktualizować użytkowników lub innych obiektów podczas personifikacji aplikacji.

Aby udzielić możliwość zarządzania dostęp do aplikacji w witrynie Azure portal:

1. Zaloguj się do Twojej [dzierżawy usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, który kwalifikuje się do roli administratora globalnego w dzierżawie.
2. Jeśli masz wystarczające uprawnienia, otwórz [strony role i Administratorzy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Otwórz jeden z następujących ról, aby wyświetlić jej przypisaniami do elementu członkowskiego:
   * **Administrator aplikacji**
   * **Administrator aplikacji w chmurze**
4. Na **członków** strony dla tej roli, wybierz opcję **Dodawanie elementu członkowskiego**.
5. Wybierz co najmniej jednego członka do dodania do roli. <!--Members can be users or groups.-->

Możesz wyświetlić opis dla tych ról w [dostępnych ról](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegowanie rejestracji aplikacji

Domyślnie wszyscy użytkownicy mogą tworzyć rejestracje aplikacji, ale można selektywnie udzielić uprawnień do tworzenia aplikacji rejestracji lub uprawnień do wyrażenia zgody, aby autoryzować aplikację.

1. Zaloguj się do Twojej [dzierżawy usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, który kwalifikuje się do roli administratora globalnego w dzierżawie.
2. Po uzyskaniu odpowiednich uprawnień, należy ustawić jeden lub oba z następujących czynności:
   * Na [strona Ustawienia użytkownika dla Twojej dzierżawy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ustaw **użytkownicy mogą rejestrować aplikacje** na nie.
   * Na [ustawienia użytkownika dla aplikacji korporacyjnych](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)ustaw **użytkownicy mogą wyrazić zgodę aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu** na nie.
3. Następnie Przypisz użytkowników, którzy tego uprawnienia, aby być członkami roli dla deweloperów aplikacji, zgodnie z potrzebami.

Gdy użytkownik rejestruje aplikację, ich są automatycznie dodawane jako pierwszy właściciela aplikacji.

## <a name="delegate-app-ownership"></a>Delegowanie własności aplikacji

Właścicieli aplikacji i rejestracji aplikacji każdego zarządzalnych tylko aplikacje lub rejestracje aplikacji, które są właścicielami. Na przykład po dodaniu właściciel aplikacji Salesforce, tego właściciela i można zarządzać dostęp do konfiguracji dla usług Salesforce, ale nie wszystkie inne aplikacje. Aplikacja może mieć wielu właścicieli, a użytkownik może być właścicielem wiele aplikacji.

Właściciel aplikacji może:

* Zmień właściwości aplikacji, takie jak nazwa i uprawnienia żądań aplikacji
* Zarządzanie poświadczeniami
* Konfigurowanie logowania jednokrotnego
* Przypisywanie dostępu użytkownika
* Dodawanie lub usuwanie innych właścicieli
* Edytuj manifest aplikacji
* Opublikuj aplikację w galerii aplikacji

> [!IMPORTANT]
> Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Ta personifikacji tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może zrobić w obszarze swoje przypisania roli w usłudze Azure AD. Użytkownicy przypisani do tej roli mogą potencjalnie utworzyć lub zaktualizować użytkowników lub innych obiektów podczas personifikacji aplikacji.

Właściciel rejestracji aplikacji można wyświetlać i edytować rejestracji aplikacji.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Aby przypisać właściciela aplikacji

1. Zaloguj się do Twojej [dzierżawy usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, który kwalifikuje się do aplikacji administrator lub administrator aplikacji w chmurze dla dzierżawy.
2. Na [stronie rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) dla dzierżawy, wybierz aplikację, aby otworzyć **Przegląd** strony aplikacji.
3. Wybierz **właścicieli** Aby wyświetlić listę właścicieli dla aplikacji.
4. Wybierz **Dodaj** wybrać co najmniej jednego właściciela, aby dodać do aplikacji.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Przypisywanie właściciela do rejestracji aplikacji

1. Zaloguj się do Twojej [dzierżawy usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, który kwalifikuje się do administratora aplikacji lub roli administratora aplikacji chmury w dzierżawie.
2. Jeśli masz wystarczające uprawnienia na [strony aplikacji dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) dla dzierżawy, wybierz opcję rejestracji aplikacji, aby go otworzyć.
3. Wybierz **ustawienia**.
4. Wybierz **właścicieli** na **ustawienia** strony, aby zapoznać się z listą właścicieli dla aplikacji.
5. Wybierz **właściciela Dodaj** wybrać co najmniej jednego właściciela, aby dodać do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* [Odwołanie do roli administratora usługi Azure AD](directory-assign-admin-roles.md)
