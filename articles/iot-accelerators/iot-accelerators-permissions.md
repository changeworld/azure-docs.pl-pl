---
title: Akceleratory rozwiązań Azure IoT i usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak akceleratorów rozwiązań Azure IoT używa usługi Azure Active Directory, aby zarządzać uprawnieniami.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e45954389c8dd1b484a7009460c541bf35266973
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713854"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Uprawnienia w witrynie azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Co się stanie po zalogowaniu

Przy pierwszym zalogowaniu się na [azureiotsuite.com][lnk-azureiotsolutions], witryny Określa poziomy uprawnień, możesz mieć na podstawie aktualnie wybranej dzierżawy usługi Azure Active Directory (AAD) i subskrypcji platformy Azure.

1. Najpierw do wypełnienia listy dzierżaw widoczny obok nazwy użytkownika, witryna dowiaduje się na platformie Azure dzierżaw usługi AAD, które należą do. Obecnie lokacji mogą uzyskać tokeny użytkowników dla jednej dzierżawy w danym momencie. W związku z tym po przełączeniu dzierżaw przy użyciu listy rozwijanej w prawym górnym rogu witryny rejestruje w tej dzierżawie, umożliwiające uzyskanie tokenów dla tej dzierżawy.

2. Następnie lokacji znajduje subskrypcje, które zostało skojarzone z wybranej dzierżawy platformy Azure. Zobaczysz dostępne subskrypcje, tworząc nowy akcelerator rozwiązań.

3. Na koniec lokacji pobiera wszystkie zasoby w subskrypcji i grup zasobów oznaczone jako akceleratorów rozwiązań i wypełnia kafelków na stronie głównej.

W poniższych sekcjach opisano role, które kontrolują dostęp do akceleratorów rozwiązania.

## <a name="aad-roles"></a>Role usługi AAD

Role usługi AAD odpowiedzialne za aprowizowanie akceleratorach rozwiązań, do zarządzania użytkownikami i niektórych usług platformy Azure w akcelerator rozwiązań.

Możesz znaleźć więcej informacji na temat ról administratora w usłudze AAD w [przypisywanie ról administratorów w usłudze Azure AD][lnk-aad-admin]. Bieżący artykuł koncentruje się na **administratora globalnego** i **użytkownika** role w katalogach usługi używane przez akceleratorów rozwiązań.

### <a name="global-administrator"></a>Administrator globalny

Może to być wiele Administratorzy globalni dla dzierżawy usługi AAD:

* Podczas tworzenia dzierżawy usługi AAD jest domyślnie administratora globalnego z tej dzierżawy.
* Administrator globalny może aprowizować akceleratorów rozwiązania podstawowa i standardowa (podstawowe wdrożenie używa pojedynczej maszyny wirtualnej platformy Azure).

### <a name="domain-user"></a>Domena użytkownik

Może to być wielu użytkowników domeny w dzierżawie usługi AAD:

* Użytkownik domeny można udostępnić akceleratora rozwiązań podstawowych za pośrednictwem [azureiotsolutions.com] [ lnk-azureiotsolutions] lokacji.
* Użytkownik domeny, można utworzyć akceleratora rozwiązań podstawowa przy użyciu interfejsu wiersza polecenia.

### <a name="guest-user"></a>Użytkownik-Gość

Może istnieć wiele użytkowników-gości na dzierżawę usługi AAD. Użytkownicy-goście mają ograniczony zestaw praw w dzierżawie usługi AAD. W rezultacie użytkownicy-goście nie można zainicjować obsługi akceleratora rozwiązań w dzierżawie usługi AAD.

Aby uzyskać więcej informacji dotyczących użytkowników i ról w usłudze AAD zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure AD][lnk-create-edit-users]
* [Przypisywanie użytkowników do aplikacji][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role administratorów subskrypcji platformy Azure

Role administratora platformy Azure kontrolują możliwość mapowania subskrypcji platformy Azure z dzierżawą usługi AAD.

Dowiedz się więcej o rolach administratora platformy Azure, w artykule [Administratorzy subskrypcji platformy Azure Dodaj lub zmień][lnk-admin-roles].

## <a name="faq"></a>Często zadawane pytania

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi, i chcę zmienić katalog mapowanie między moją subskrypcję i z określoną dzierżawą usługi AAD. Jak wykonać to zadanie?

Zobacz [dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chcę zmienić administratora usługi lub administratora współpracującego z poziomu konta

Zobacz artykuł pomocy technicznej [Zmienianie administratora usługi i administratora współpracującego z poziomu konta][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Należy skontaktować się z administratorem konta lub spróbuj przy użyciu innego konta."

Przyjrzyj się poniższym diagramie, aby uzyskać wskazówki:

![][img-flowchart]

> [!NOTE]
> Jeśli będziesz kontynuować wyświetlić błąd, po upewnieniu się, możesz tylko administrator globalny dzierżawy usługi AAD i współadministratorem subskrypcji, poproś administratora konta, Usuń użytkownika i przypisać odpowiednie uprawnienia w tej kolejności. Najpierw dodaj użytkownika jako administrator globalny, a następnie dodać użytkowników jako współadministrator subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [Pomoc i obsługa techniczna][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd, gdy mam subskrypcję platformy Azure? "Subskrypcja platformy Azure jest wymagana do utworzenia wstępnie skonfigurowanych rozwiązań. Możesz można utworzyć bezpłatne konto próbne w zaledwie kilka minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowanie dla Twojej subskrypcji dzierżawy i upewnij się, że wybrano poprawny dzierżawy na liście rozwijanej. Jeśli została zweryfikowana żądanej dzierżawy jest poprawna, postępuj zgodnie z powyższym diagramie i sprawdź poprawność mapowania subskrypcji i tej dzierżawy usługi AAD.

## <a name="next-steps"></a>Kolejne kroki
Aby kontynuować zapoznawanie się akceleratorów rozwiązań IoT, zobacz, jak można [Dostosowywanie akceleratora rozwiązań][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/remote-monitoring-services-dotnet
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
