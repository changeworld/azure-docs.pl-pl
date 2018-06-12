---
title: Pakiet Azure IoT i Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak pakiet IoT Azure używa usługi Azure Active Directory do zarządzania uprawnieniami.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4d0c7aff54ee20cf9ae4e6cdf2fc0718cd0b2f2a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304143"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Uprawnienia w witrynie the azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Co się stanie po zalogowaniu

Przy pierwszym zalogowaniu się na [azureiotsuite.com][lnk-azureiotsuite], witryny Określa poziomy uprawnień, należy mieć na podstawie aktualnie wybranej dzierżawy usługi Azure Active Directory (AAD) i subskrypcji platformy Azure.

1. Najpierw do wypełnienia listy dzierżawców widoczne obok nazwy użytkownika, lokacji znajduje na platformie Azure dzierżaw usługi AAD, które należą do. Obecnie witryny można uzyskać tokeny użytkownika dla jednego dzierżawcy w czasie. W związku z tym podczas przełączania dzierżawcy przy użyciu listy rozwijanej w prawym górnym rogu lokacji rejestruje w tej dzierżawy uzyskać tokenów dla tej dzierżawy.

2. Następnie lokacji znajduje z platformy Azure, subskrypcje, które zostały skojarzone z wybranej dzierżawy. Podczas tworzenia nowego rozwiązania wstępnie skonfigurowane są wyświetlane dostępnych subskrypcji.

3. Na koniec lokacji pobiera wszystkie zasoby w subskrypcji i grup zasobów oznakowane jako wstępnie skonfigurowanych rozwiązań i wypełnia Kafelki na stronie głównej.

W poniższych sekcjach opisano role, które kontrolują dostęp do wstępnie skonfigurowanych rozwiązań.

## <a name="aad-roles"></a>Role usługi AAD

Role AAD kontrolowanie możliwości rozwiązania wstępnie udostępnić i zarządzanie użytkownikami w wstępnie skonfigurowane rozwiązanie.

Można znaleźć więcej informacji na temat ról administratorów w usłudze AAD w [przypisywanie ról administratorów w usłudze Azure AD][lnk-aad-admin]. Bieżący artykuł skupia się na **administratora globalnego** i **użytkownika** ról katalogu jako używane przez wstępnie skonfigurowanych rozwiązań.

### <a name="global-administrator"></a>Administrator globalny

Może istnieć wiele Administratorzy globalni na dzierżawę usługi AAD:

* Podczas tworzenia dzierżawy usługi AAD jest domyślnie administratora globalnego tej dzierżawy.
* Administrator globalny może udostępnić wstępnie skonfigurowane rozwiązanie i przypisano **Admin** roli dla aplikacji w ich dzierżawę usługi AAD.
* Jeśli inny użytkownik w tej samej dzierżawie usługi AAD tworzy aplikację, domyślna rola przyznane administratora globalnego jest **tylko do odczytu**.
* Administrator globalny można przypisać użytkowników do ról aplikacji za pomocą [portalu Azure][lnk-portal].

### <a name="domain-user"></a>Użytkownik domeny

Może istnieć wiele użytkowników domeny w dzierżawie usługi AAD:

* Użytkownik domeny można udostępnić wstępnie skonfigurowane rozwiązanie za pośrednictwem [azureiotsuite.com] [ lnk-azureiotsuite] lokacji. Domyślnie udzielony jest użytkownik domeny **Admin** roli w aplikacji elastycznie.
* Użytkownik domeny, można utworzyć aplikację przy użyciu skryptu build.cmd w [azure iot — zdalnego monitorowania][lnk-rm-github-repo], [azure-iot —-konserwacji predykcyjnej][lnk-pm-github-repo], lub [azure iot — połączony fabryka] [ lnk-cf-github-repo] repozytorium. Domyślna rola przyznane użytkownikowi domeny jest jednak **tylko do odczytu**, ponieważ użytkownik domeny nie ma uprawnień do przypisywania ról.
* Jeśli inny użytkownik w dzierżawie usługi AAD tworzy aplikację, są przypisane do użytkownika domeny **tylko do odczytu** roli domyślnie dla tej aplikacji.
* Użytkownik domeny nie można przypisać role w aplikacji; w związku z tym użytkownikiem domeny nie można dodać użytkowników lub ról użytkowników dla aplikacji, nawet jeśli ich obsługi administracyjnej.

### <a name="guest-user"></a>Gość

Może istnieć wiele gości na dzierżawę usługi AAD. Goście mają ograniczony zestaw praw w dzierżawie usługi AAD. W związku z tym gości nie może obsłużyć wstępnie skonfigurowane rozwiązanie w dzierżawie usługi AAD.

Aby uzyskać więcej informacji dotyczących użytkowników i role w usłudze AAD zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure AD][lnk-create-edit-users]
* [Przypisywanie użytkowników do aplikacji][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role administratorów subskrypcji platformy Azure

Role administratora platformy Azure kontrolować możliwość mapowania subskrypcji platformy Azure do dzierżawy usługi AD.

Dowiedz się więcej o rolach administratora platformy Azure w artykule [jak dodać lub zmienić administratora współpracującego Azure, Administrator usługi i konto administratora][lnk-admin-roles].

## <a name="application-roles"></a>Role aplikacji

Role aplikacji kontroli dostępu na urządzeniach w wstępnie skonfigurowane rozwiązanie.

Istnieją dwa zdefiniowanych ról i jedną rolę niejawne zdefiniowanych w aplikacji udostępnione:

* **Administrator:** ma pełną kontrolę do dodawania, zarządzania, usuń urządzenia i zmodyfikować ustawienia.
* **Tylko do odczytu:** mogą wyświetlać urządzenia, zasad, akcje, zadania i dane telemetryczne.

Można znaleźć uprawnienia przypisane do każdej roli w [RolePermissions.cs] [ lnk-resource-cs] pliku źródłowego.

### <a name="changing-application-roles-for-a-user"></a>Zmiana aplikacji ról użytkownika

Poniższa procedura służy do wyznaczenia użytkownika w usłudze Active Directory administratora wstępnie skonfigurowanych rozwiązań.

Musi być administratorem globalnym usługi AAD, aby zmienić role dla użytkownika:

1. Przejdź do witryny [Azure Portal][lnk-portal].
2. Wybierz pozycję **Azure Active Directory**.
3. Upewnij się, że używasz katalogu, który wybrano w azureiotsuite.com podczas przydzielania rozwiązania. Jeśli masz wiele katalogów, skojarzonymi z Twoją subskrypcją, można przełączać się między nimi po kliknięciu nazwę swojego konta w prawym górnym rogu portalu.
4. Kliknij przycisk **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.
4. Pokaż **wszystkie aplikacje** z **żadnych** stanu. Następnie wyszukaj aplikację o nazwie wstępnie skonfigurowanych rozwiązań.
5. Kliknij nazwę aplikacji, która jest zgodna z nazwą wstępnie skonfigurowane rozwiązanie.
6. Kliknij pozycję **Użytkownicy i grupy**.
7. Wybierz użytkownika, aby przełączyć role.
8. Kliknij przycisk **przypisać** i wybierz rolę (takich jak **Admin**) ma zostać przypisany do użytkownika, kliknij znacznik wyboru.

## <a name="faq"></a>Często zadawane pytania

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi, a chcę zmienić katalogu mapowanie między mojej subskrypcji i określonych dzierżawę usługi AAD. Jak wykonać to zadanie?

Zobacz [Aby dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jestem użytkownika/członek domeny na dzierżawę usługi AAD i utworzono wstępnie skonfigurowanych rozwiązań. Jak pobrać przypisane roli dla mojej aplikacji?

Skontaktuj się administratorem globalnym, aby utworzyć administratora globalnego dla dzierżawcy usługi AAD i przypisz role użytkowników samodzielnie. Alternatywnie poproś administratora globalnego przypisanie roli bezpośrednio. Jeśli chcesz zmienić dzierżawę usługi AAD wstępnie skonfigurowane rozwiązanie zostało wdrożone do, zobacz następne pytanie.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Jak zmienić dzierżawę usługi AAD, który Moje zdalnego monitorowania wstępnie skonfigurowane rozwiązanie i aplikacji, które są przypisane do?

Można uruchamiać wdrożeń w chmurze z <https://github.com/Azure/azure-iot-remote-monitoring> i wdrożenie z nowo utworzonego dzierżawę usługi AAD. Ponieważ jesteś, domyślnie administratorem globalnym, podczas tworzenia dzierżawy usługi AAD, masz uprawnienia do dodawania użytkowników i przypisać role do tych użytkowników.

1. Tworzenie katalogu usługi AAD w [portalu Azure][lnk-portal].
2. Przejdź do pozycji <https://github.com/Azure/azure-iot-remote-monitoring> (Plik > Nowy > Inny).
3. Uruchom `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (na przykład `build.cmd cloud debug myRMSolution`)
4. Po wyświetleniu monitu, ustaw **tenantid** się nowo utworzone dzierżawy zamiast dzierżawy poprzedniej.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Zmienianie administratorem usługi ani Współadministratorem podczas logowania się za pomocą konta organizacyjnego

Zobacz artykuł pomocy technicznej [zmiany administratora usługi i Współadministrator podczas logowania się przy użyciu konta organizacyjne][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Spróbuj skontaktować się z administratorem konta lub za pomocą innego konta."

Szukaj na poniższym diagramie, aby uzyskać wskazówki:

![][img-flowchart]

> [!NOTE]
> Jeśli nadal wyświetlany błąd po weryfikacji można administratora globalnego dla dzierżawcy usługi AAD i współadministratorem subskrypcji, poproś administratora konta, Usuń użytkownika i przypisać odpowiednie uprawnienia w podanej kolejności. Najpierw dodaj użytkownika jako administrator globalny, a następnie dodaj użytkownika jako współadministratorem subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [Pomoc i obsługa techniczna][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd gdy subskrypcji platformy Azure? "Subskrypcji platformy Azure jest wymagane do utworzenia wstępnie skonfigurowanych rozwiązań. Użytkownik może utworzyć bezpłatne konto próbne w zaledwie kilka minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowania dla Twojej subskrypcji dzierżawcy i upewnij się, że wybrano poprawny dzierżawy na liście rozwijanej. Jeśli zostały sprawdzone żądaną dzierżawy jest poprawna, postępuj zgodnie z wcześniejszym diagramie i sprawdzić poprawności mapowania subskrypcji i tego dzierżawę usługi AAD.

## <a name="next-steps"></a>Kolejne kroki
Aby kontynuować zapoznawanie pakiet IoT, zobacz temat [dostosować wstępnie skonfigurowane rozwiązanie][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
