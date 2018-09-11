---
title: Pakiet Azure IoT Suite i usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak pakiet Azure IoT Suite używa usługi Azure Active Directory, aby zarządzać uprawnieniami.
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
ms.openlocfilehash: a56d535ee06a097c7c18bcd507c25708f6a33f91
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296928"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Uprawnienia w witrynie the azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Co się stanie po zalogowaniu

Przy pierwszym zalogowaniu się na [azureiotsuite.com][lnk-azureiotsuite], witryny Określa poziomy uprawnień, możesz mieć na podstawie aktualnie wybranej dzierżawy usługi Azure Active Directory (AAD) i subskrypcji platformy Azure.

1. Najpierw do wypełnienia listy dzierżaw widoczny obok nazwy użytkownika, witryna dowiaduje się na platformie Azure dzierżaw usługi AAD, które należą do. Obecnie lokacji mogą uzyskać tokeny użytkowników dla jednej dzierżawy w danym momencie. W związku z tym po przełączeniu dzierżaw przy użyciu listy rozwijanej w prawym górnym rogu witryny rejestruje w tej dzierżawie, umożliwiające uzyskanie tokenów dla tej dzierżawy.

2. Następnie lokacji znajduje subskrypcje, które zostało skojarzone z wybranej dzierżawy platformy Azure. Zobaczysz dostępne subskrypcje, podczas tworzenia nowego wstępnie skonfigurowanego rozwiązania.

3. Na koniec lokacji pobiera wszystkie zasoby w subskrypcji i grup zasobów oznaczone jako wstępnie skonfigurowane rozwiązania i wypełnia kafelków na stronie głównej.

W poniższych sekcjach opisano role, które kontrolują dostęp do wstępnie skonfigurowanych rozwiązań.

## <a name="aad-roles"></a>Role usługi AAD

Role usługi AAD kontrolowania zdolność aprowizacji wstępnie skonfigurowanego rozwiązania i zarządzania użytkownikami we wstępnie skonfigurowanym rozwiązaniu.

Możesz znaleźć więcej informacji na temat ról administratora w usłudze AAD w [przypisywanie ról administratorów w usłudze Azure AD][lnk-aad-admin]. Bieżący artykuł koncentruje się na **administratora globalnego** i **użytkownika** role w katalogach usługi używane przez wstępnie skonfigurowanych rozwiązań.

### <a name="global-administrator"></a>Administrator globalny

Może to być wiele Administratorzy globalni dla dzierżawy usługi AAD:

* Podczas tworzenia dzierżawy usługi AAD jest domyślnie administratora globalnego z tej dzierżawy.
* Administrator globalny może aprowizować wstępnie skonfigurowanego rozwiązania i przypisano **administratora** roli dla aplikacji wewnątrz swojej dzierżawy usługi AAD.
* Jeśli inny użytkownik w tej samej dzierżawie usługi AAD tworzy aplikację, domyślna rola przyznane przez administratora globalnego jest **tylko do odczytu**.
* Administrator globalny można przypisać użytkowników do ról dla aplikacji za pomocą [witryny Azure portal][lnk-portal].

### <a name="domain-user"></a>Domena użytkownik

Może to być wielu użytkowników domeny w dzierżawie usługi AAD:

* Użytkownik domeny można udostępnić wstępnie skonfigurowanego rozwiązania za pośrednictwem [azureiotsuite.com] [ lnk-azureiotsuite] lokacji. Domyślnie udzielony jest użytkownik domeny **administratora** roli w aplikacji elastycznie.
* Użytkownik domeny, można utworzyć aplikację przy użyciu skryptu build.cmd [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance] [ lnk-pm-github-repo], lub [azure-iot-connected-factory] [ lnk-cf-github-repo] repozytorium. Jednak to domyślna rola przyznane użytkownikowi domeny **tylko do odczytu**, ponieważ użytkownik domeny nie ma uprawnienia do przypisywania ról.
* Jeśli inny użytkownik w dzierżawie usługi AAD tworzy aplikację, użytkownika domeny zostanie przypisany **tylko do odczytu** roli domyślny dla tej aplikacji.
* Użytkownik domeny nie można przypisać role aplikacji; w związku z tym użytkownikiem domeny nie można dodać użytkowników lub ról użytkowników dla aplikacji, nawet wtedy, gdy są aprowizowane.

### <a name="guest-user"></a>Użytkownik-Gość

Może istnieć wiele użytkowników-gości na dzierżawę usługi AAD. Użytkownicy-goście mają ograniczony zestaw praw w dzierżawie usługi AAD. W rezultacie użytkownicy-goście nie można zainicjować obsługi wstępnie skonfigurowanego rozwiązania w dzierżawie usługi AAD.

Aby uzyskać więcej informacji dotyczących użytkowników i ról w usłudze AAD zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure AD][lnk-create-edit-users]
* [Przypisywanie użytkowników do aplikacji][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role administratorów subskrypcji platformy Azure

Role administratora platformy Azure kontrolują możliwość mapowania subskrypcji platformy Azure do dzierżawy usługi AD.

Dowiedz się więcej o rolach administratora platformy Azure, w artykule [Administratorzy subskrypcji platformy Azure Dodaj lub zmień][lnk-admin-roles].

## <a name="application-roles"></a>Role aplikacji

Role aplikacji kontroli dostępu na urządzeniach w wstępnie skonfigurowanego rozwiązania.

Istnieją dwa zdefiniowanych ról i jednej roli niejawnego zdefiniowanych w elastycznie aplikacji:

* **Administrator:** ma pełną kontrolę do dodawania, zarządzanie, usuń urządzenia i zmodyfikować ustawienia.
* **Tylko do odczytu:** można wyświetlić urządzenia, reguły, akcje, zadania i dane telemetryczne.

Możesz znaleźć uprawnienia przypisane do każdej roli w [RolePermissions.cs] [ lnk-resource-cs] pliku źródłowego.

### <a name="changing-application-roles-for-a-user"></a>Zmiana ról aplikacji dla użytkownika

Aby użytkownika w usłudze Active Directory administrator wstępnie skonfigurowanego rozwiązania, można użyć poniższej procedury.

Musisz być administratorem globalnym usługi AAD, aby zmienić role dla użytkownika:

1. Przejdź do witryny [Azure Portal][lnk-portal].
2. Wybierz pozycję **Azure Active Directory**.
3. Upewnij się, że używasz katalogu, który został wybrany w witrynie azureiotsuite.com podczas aprowizacji rozwiązania. Jeśli masz wiele katalogów, powiązaną z Twoją subskrypcją, możesz przełączać się między nimi po kliknięciu nazwę swojego konta w prawym górnym rogu portalu.
4. Kliknij przycisk **aplikacje dla przedsiębiorstw**, następnie **wszystkie aplikacje**.
4. Pokaż **wszystkie aplikacje** z **wszelkie** stanu. Następnie wyszukaj aplikację o nazwie wstępnie skonfigurowanego rozwiązania.
5. Kliknij nazwę aplikacji, która odpowiada nazwę wstępnie skonfigurowanego rozwiązania.
6. Kliknij pozycję **Użytkownicy i grupy**.
7. Wybierz użytkownika, o którym chcesz się przełączyć role.
8. Kliknij przycisk **przypisać** i wybierz rolę (takie jak **administratora**) chcesz przypisać do użytkownika, kliknij znacznik wyboru.

## <a name="faq"></a>Często zadawane pytania

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi, i chcę zmienić katalog mapowanie między moją subskrypcję i z określoną dzierżawą usługi AAD. Jak wykonać to zadanie?

Zobacz [dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jestem użytkownika/do domeny w dzierżawie usługi AAD i utworzono wstępnie skonfigurowanego rozwiązania. Jak mogę uzyskać przypisaną rolę dla mojej aplikacji?

Poproś administratora globalnego, aby utworzyć administratora globalnego dla dzierżawy usługi AAD, a następnie role można przydzielić użytkownikom samodzielnie. Alternatywnie poproś administratora globalnego, aby przypisać możesz rolę bezpośrednio. Jeśli chcesz zmienić wstępnie skonfigurowanego rozwiązania jest dzierżawą usługi AAD po wdrożeniu, zobacz następne pytanie.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Jak przełączać się dzierżawą usługi AAD, który Moje wstępnie skonfigurowanego rozwiązania monitorowania zdalnego i aplikacji, które są przypisane do?

Można uruchomić wdrożenia w chmurze z <https://github.com/Azure/azure-iot-remote-monitoring> i ponowne wdrażanie przy użyciu nowo utworzonej dzierżawy usługi AAD. Ponieważ jesteś, domyślnie administrator globalny, gdy utworzysz dzierżawę usługi AAD masz uprawnienia do dodawania użytkowników i przypisać role do tych użytkowników.

1. Tworzenie katalogu usługi AAD w [witryny Azure portal][lnk-portal].
2. Przejdź do pozycji <https://github.com/Azure/azure-iot-remote-monitoring> (Plik > Nowy > Inny).
3. Uruchom `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (na przykład `build.cmd cloud debug myRMSolution`)
4. Po wyświetleniu monitu ustaw **tenantid** do nowo utworzonej dzierżawy zamiast poprzedniej dzierżawy.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Chcę zmienić administratora usługi lub administratorów współpracujących tylko po zalogowaniu się przy użyciu konta organizacyjne

Zobacz artykuł pomocy technicznej [Zmienianie administratora usługi i administratora współpracującego z konta organizacyjne][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Należy skontaktować się z administratorem konta lub spróbuj przy użyciu innego konta."

Przyjrzyj się poniższym diagramie, aby uzyskać wskazówki:

![][img-flowchart]

> [!NOTE]
> Jeśli będziesz kontynuować wyświetlić błąd, po upewnieniu się, możesz administratorem globalnym dzierżawy usługi AAD i jako współadministratora w ramach subskrypcji, poproś administratora konta, Usuń użytkownika i przypisać odpowiednie uprawnienia w tej kolejności. Najpierw dodaj użytkownika jako administrator globalny, a następnie dodać użytkowników jako współadministratora w ramach subskrypcji Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [Pomoc i obsługa techniczna][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd, gdy mam subskrypcję platformy Azure? "Subskrypcja platformy Azure jest wymagana do utworzenia wstępnie skonfigurowanych rozwiązań. Możesz można utworzyć bezpłatne konto próbne w zaledwie kilka minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowanie dla Twojej subskrypcji dzierżawy i upewnij się, że wybrano poprawny dzierżawy na liście rozwijanej. Jeśli została zweryfikowana żądanej dzierżawy jest poprawna, postępuj zgodnie z powyższym diagramie i sprawdź poprawność mapowania subskrypcji i tej dzierżawy usługi AAD.

## <a name="next-steps"></a>Kolejne kroki
Aby kontynuować zapoznawanie się IoT Suite, zobaczyć, jak [dostosowanie wstępnie skonfigurowanego rozwiązania][lnk-customize].

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
