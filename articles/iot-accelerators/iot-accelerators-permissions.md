---
title: Azure akceleratorów rozwiązania IoT i Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak akceleratorów rozwiązania Azure IoT używa usługi Azure Active Directory do zarządzania uprawnieniami.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa4e1d1f78549a8d1955def7a1c57e61d405e347
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297142"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Uprawnienia w witrynie azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Co się stanie po zalogowaniu

Przy pierwszym zalogowaniu się na [azureiotsuite.com][lnk-azureiotsolutions], witryny Określa poziomy uprawnień, należy mieć na podstawie aktualnie wybranej dzierżawy usługi Azure Active Directory (AAD) i subskrypcji platformy Azure.

1. Najpierw do wypełnienia listy dzierżawców widoczne obok nazwy użytkownika, lokacji znajduje na platformie Azure dzierżaw usługi AAD, które należą do. Obecnie witryny można uzyskać tokeny użytkownika dla jednego dzierżawcy w czasie. W związku z tym podczas przełączania dzierżawcy przy użyciu listy rozwijanej w prawym górnym rogu lokacji rejestruje w tej dzierżawy uzyskać tokenów dla tej dzierżawy.

2. Następnie lokacji znajduje z platformy Azure, subskrypcje, które zostały skojarzone z wybranej dzierżawy. Podczas tworzenia nowego akcelerator rozwiązań są wyświetlane dostępnych subskrypcji.

3. Na koniec lokacji pobiera wszystkie zasoby w subskrypcji i grup zasobów oznakowane jako akceleratorów rozwiązania i wypełnia Kafelki na stronie głównej.

W poniższych sekcjach opisano role, które kontrolują dostęp do akceleratorów rozwiązania.

## <a name="aad-roles"></a>Role usługi AAD

Role AAD kontrolować możliwość udostępniania rozwiązania akceleratorów, aby zarządzać użytkownikami i niektórych usług platformy Azure w akcelerator rozwiązań.

Można znaleźć więcej informacji na temat ról administratorów w usłudze AAD w [przypisywanie ról administratorów w usłudze Azure AD][lnk-aad-admin]. Bieżący artykuł skupia się na **administratora globalnego** i **użytkownika** ról katalogu jako używane przez akceleratory rozwiązania.

### <a name="global-administrator"></a>Administrator globalny

Może istnieć wiele Administratorzy globalni na dzierżawę usługi AAD:

* Podczas tworzenia dzierżawy usługi AAD jest domyślnie administratora globalnego tej dzierżawy.
* Administrator globalny może udostępnić akceleratory podstawowe i standardowe rozwiązanie (podstawowe wdrożenie korzysta z jednej maszyny wirtualnej Azure).

### <a name="domain-user"></a>Użytkownik domeny

Może istnieć wiele użytkowników domeny w dzierżawie usługi AAD:

* Użytkownik domeny można udostępnić akceleratora podstawowego rozwiązania, za pomocą [azureiotsolutions.com] [ lnk-azureiotsolutions] lokacji.
* Użytkownik domeny można utworzyć skrótu podstawowego rozwiązania, przy użyciu interfejsu wiersza polecenia.

### <a name="guest-user"></a>Gość

Może istnieć wiele gości na dzierżawę usługi AAD. Goście mają ograniczony zestaw praw w dzierżawie usługi AAD. W związku z tym gości nie może obsłużyć akcelerator rozwiązań w dzierżawie usługi AAD.

Aby uzyskać więcej informacji dotyczących użytkowników i role w usłudze AAD zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure AD][lnk-create-edit-users]
* [Przypisywanie użytkowników do aplikacji][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role administratorów subskrypcji platformy Azure

Role administratora platformy Azure kontrolowanie możliwości mapowania subskrypcji platformy Azure na dzierżawę usługi AAD.

Dowiedz się więcej o rolach administratora platformy Azure w artykule [jak dodać lub zmienić administratora współpracującego Azure, Administrator usługi i konto administratora][lnk-admin-roles].

## <a name="faq"></a>Często zadawane pytania

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi, a chcę zmienić katalogu mapowanie między mojej subskrypcji i określonych dzierżawę usługi AAD. Jak wykonać to zadanie?

Zobacz [Aby dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Zmienianie administratorem usługi ani Współadministratorem podczas logowania się za pomocą konta organizacyjnego

Zobacz artykuł pomocy technicznej [zmiany administratora usługi i Współadministrator podczas logowania się za pomocą konta organizacyjnego][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Spróbuj skontaktować się z administratorem konta lub za pomocą innego konta."

Szukaj na poniższym diagramie, aby uzyskać wskazówki:

![][img-flowchart]

> [!NOTE]
> Jeśli nadal wyświetlany błąd po weryfikacji można administratora globalnego dzierżawy usługi AAD i współadministratorem subskrypcji, poproś administratora konta, Usuń użytkownika i przypisać odpowiednie uprawnienia w podanej kolejności. Najpierw dodaj użytkownika jako administrator globalny, a następnie dodaj użytkownika jako współadministratorem subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [Pomoc i obsługa techniczna][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd gdy subskrypcji platformy Azure? "Subskrypcji platformy Azure jest wymagane do utworzenia wstępnie skonfigurowanych rozwiązań. Użytkownik może utworzyć bezpłatne konto próbne w zaledwie kilka minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowania dla Twojej subskrypcji dzierżawcy i upewnij się, że wybrano poprawny dzierżawy na liście rozwijanej. Jeśli zostały sprawdzone żądaną dzierżawy jest poprawna, postępuj zgodnie z wcześniejszym diagramie i sprawdzić poprawności mapowania subskrypcji i tego dzierżawę usługi AAD.

## <a name="next-steps"></a>Kolejne kroki
Aby kontynuować zapoznawanie akceleratorów rozwiązania IoT, zobacz temat [dostosować akcelerator rozwiązań][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]: ../active-directory/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
