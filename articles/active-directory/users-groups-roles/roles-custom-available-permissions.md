---
title: Dostępne niestandardowe uprawnienia roli administratora — usługa Azure AD | Dokumenty firmy Microsoft
description: Niestandardowe uprawnienia roli administratora do delegowannia zarządzania tożsamościami.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025144"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Podtypy i uprawnienia rejestracji aplikacji w usłudze Azure Active Directory

Ten artykuł zawiera aktualnie dostępne uprawnienia do rejestracji aplikacji dla definicji ról niestandardowych w usłudze Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Uprawnienia do zarządzania aplikacjami z jednym katalogem

Wybierając uprawnienia do roli niestandardowej, masz możliwość udzielenia dostępu do zarządzania tylko aplikacjami z jednym katalogiem. Aplikacje z jednym katalogiem są dostępne tylko dla użytkowników w organizacji usługi Azure AD, w której aplikacja jest zarejestrowana. Aplikacje z jednym katalogem są definiowane jako z **typami obsługiwanych kont ustawionymi** na "Konta tylko w tym katalogu organizacyjnym". W interfejsie API wykresu aplikacje z jednym katalogiem mają właściwość signInAudience ustawioną na "AzureADMyOrg".

Aby udzielić dostępu do zarządzania tylko aplikacjami z jednym katalogiem, użyj poniższych uprawnień z podtypem **applications.myOrganization**. Na przykład microsoft.directory/applications.myOrganization/basic/update.

Zobacz [omówienie ról niestandardowych, aby](roles-custom-overview.md) uzyskać wyjaśnienie, co oznaczają podtyp, uprawnienie i zestaw właściwości. Poniższe informacje są specyficzne dla rejestracji aplikacji.

### <a name="create-and-delete"></a>Tworzenie i usuwanie

Istnieją dwa uprawnienia dostępne do udzielania możliwości tworzenia rejestracji aplikacji, każdy z różnych zachowań:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Przypisanie tego uprawnienia powoduje, że twórca jest dodawany jako pierwszy właściciel utworzonej rejestracji aplikacji, a utworzona rejestracja aplikacji będzie wliczane do przydziału 250 utworzonych obiektów twórcy.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Przypisanie tego uprawnienia powoduje, że twórca nie zostanie dodany jako pierwszy właściciel utworzonej rejestracji aplikacji, a utworzona rejestracja aplikacji nie będzie wliczane do przydziału 250 utworzonych obiektów twórcy. Użyj tego uprawnienia ostrożnie, ponieważ nic nie stoi na przeszkodzie, aby cesjonariusz tworzył rejestracje aplikacji, dopóki przydział na poziomie katalogu nie zostanie trafiony. Jeśli oba uprawnienia są przypisane, to uprawnienie ma pierwszeństwo.

Jeśli oba uprawnienia są przypisane, uprawnienie /create będzie mieć pierwszeństwo. Chociaż uprawnienie /createAsOwner nie automatycznie dodaje twórcy jako pierwszego właściciela, właściciele mogą być określone podczas tworzenia rejestracji aplikacji podczas korzystania z interfejsów API programu Graph lub poleceń cmdlet programu PowerShell.

Uprawnienia utwórz udziel dostępu do polecenia **Nowa rejestracja.**

[Te uprawnienia udzielają dostępu do polecenia Portal nowej rejestracji](./media/roles-create-custom/new-custom-role.png)

Istnieją dwa uprawnienia do udzielania możliwości usuwania rejestracji aplikacji:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Daje możliwość usuwania rejestracji aplikacji niezależnie od podtypu; oznacza to, że zarówno aplikacji z jedną i wieloma dzierżawcami.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganizacja/usuwanie

Umożliwia usuwanie rejestracji aplikacji ograniczonych do tych, które są dostępne tylko dla kont w organizacji lub aplikacji z jedną dzierżawą (podtyp myOrganization).

![Te uprawnienia udzielają dostępu do polecenia Usuń rejestrację aplikacji](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Podczas przypisywania roli, która zawiera uprawnienia do tworzenia, przypisanie roli musi być dokonane w zakresie katalogu. Uprawnienie do tworzenia przypisane do zakresu zasobu nie umożliwia tworzenia rejestracji aplikacji.

### <a name="read"></a>Odczyt

Wszyscy użytkownicy w organizacji mogą domyślnie odczytywać informacje o rejestracji aplikacji. Jednak użytkownicy-goście i jednostki usługi aplikacji nie mogą. Jeśli planujesz przypisać rolę do użytkownika lub aplikacji-gościa, musisz dołączyć odpowiednie uprawnienia do odczytu.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Możliwość odczytywania wszystkich właściwości aplikacji jednodostępnych i wielodostępnych poza właściwościami, których nie można odczytać w żadnej sytuacji, takiej jak poświadczenia.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganizacja/wszystkieWłaścienia/odczyt

Udziela tych samych uprawnień co microsoft.directory/applications/allProperties/read, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Przyznaje możliwość odczytu właścicieli nieruchomości w aplikacjach jednodostępnych i wielodostępnych. Udziela dostępu do wszystkich pól na stronie właścicieli rejestracji aplikacji:

![To uprawnienie daje dostęp do strony właścicieli rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Udziela dostępu do odczytu standardowych właściwości rejestracji aplikacji. Obejmuje to właściwości na stronach rejestracji aplikacji.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganizacja/standard/odczyt

Udziela tych samych uprawnień co microsoft.directory/applications/standard/read, ale tylko dla aplikacji z jedną dzierżawą.

### <a name="update"></a>Aktualizacja

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Możliwość aktualizowania wszystkich właściwości w aplikacjach jednosektorowych i wielosektorowych.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganizacja/wszystkieWłaścienia/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/allProperties/update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Możliwość aktualizacji właściwości obsługiwanego typu konta (signInAudience) w aplikacjach jednosepołowych i wieloseznakowych.

![To uprawnienie udziela dostępu do usługi obsługiwanego konta rejestracji aplikacji na stronie uwierzytelniania](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganizacja/publiczność/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/audience/update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Możliwość aktualizowania adresu URL odpowiedzi, adresu URL wylogowania, przepływu niejawnego i właściwości domeny wydawcy w aplikacjach z jedną i wieloma dzierżawcami. Udziela dostępu do wszystkich pól na stronie uwierzytelniania rejestracji aplikacji z wyjątkiem obsługiwanych typów kont:

![Udziela dostępu do uwierzytelniania rejestracji aplikacji, ale nie jest obsługiwanych typów kont](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganizacja/uwierzytelnianie/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/authentication/update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Możliwość aktualizacji nazwy, logo, adresu URL strony głównej, warunków adresu URL usług i właściwości adresu URL zasad zachowania poufności informacji w aplikacjach z jedną i wieloma dzierżawcami. Udziela dostępu do wszystkich pól na stronie znakowania rejestracji aplikacji:

![To uprawnienie zapewnia dostęp do strony znakowania rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganizacja/podstawowa/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/basic/update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Możliwość aktualizowania certyfikatów i wpisów tajnych klienta w aplikacjach jednodostępnych i wielodostępnych. Udziela dostępu do wszystkich pól na stronie świadectw rejestracji aplikacji & wpisów tajnych:

![To uprawnienie udziela dostępu do strony certyfikatów rejestracji aplikacji & wpisów tajnych](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganizacja/poświadczenia/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/credentials/update, ale tylko dla aplikacji z jednym katalogiem.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Możliwość aktualizacji właściwości właściciela w jednej dzierżawie i wielu dzierżawców. Udziela dostępu do wszystkich pól na stronie właścicieli rejestracji aplikacji:

![To uprawnienie daje dostęp do strony właścicieli rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganizacja/właściciele/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/owners/update, ale tylko dla aplikacji z jedną dzierżawą.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Możliwość aktualizowania uprawnień delegowanych, uprawnień aplikacji, autoryzowanych aplikacji klienckich, wymaganych uprawnień i udzielania właściwości zgody w aplikacjach jednodostępnych i wielodostępnych. Nie daje możliwości wykonywania zgody. Udziela dostępu do wszystkich pól w uprawnieniach interfejsu API rejestracji aplikacji i uwidaczniać strony interfejsu API:

![To uprawnienie udziela dostępu do strony uprawnień interfejsu API rejestracji aplikacji](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![To uprawnienia udziela dostępu do rejestracji aplikacji Uwidaczniać stronę interfejsu API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganizacja/uprawnienia/aktualizacja

Udziela tych samych uprawnień co microsoft.directory/applications/permissions/update, ale tylko dla aplikacji z jedną dzierżawą.

## <a name="required-license-plan"></a>Wymagany plan licencji

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Tworzenie ról niestandardowych przy użyciu [witryny Azure Portal, usługi Azure AD PowerShell i interfejsu API wykresu](roles-create-custom.md)
- [Wyświetlanie przydziałów dla roli niestandardowej](roles-view-assignments.md)
