---
title: Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 5dda2eafe86d037faab6284c2af0d8026c194d11
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921143"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure, dzięki czemu będzie wiadomo, czego można oczekiwać, gdy przy użyciu ról w witrynie Azure portal i możesz rozwiązać problemy z dostępem do.

## <a name="problems-with-rbac-role-assignments"></a>Problemy z przypisaniami ról kontroli dostępu opartej na rolach

- Jeśli to nie można dodać przypisania roli w witrynie Azure portal na **kontrola dostępu (IAM)** ponieważ **Dodaj** > **Dodaj przypisanie roli** opcja jest wyłączona lub ponieważ otrzymasz błąd uprawnień "klient o identyfikatorze obiektu nie ma autoryzacji do wykonania akcji", sprawdź, czy obecnie logujesz się jako użytkownik, któremu przypisano rolę, która ma `Microsoft.Authorization/roleAssignments/write` uprawnienia, takie jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) w zakresie, o których chcesz przypisać rolę.
- Jeśli otrzymasz komunikat o błędzie "nie ma więcej przypisań ról, które mogą być tworzone (kod: RoleAssignmentLimitExceeded)” podczas próby przypisania roli, spróbuj zmniejszyć liczbę przypisań ról, przypisując zamiast tego role do grup. Platforma Azure obsługuje maksymalnie **2000** przypisań ról na subskrypcję.

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Czynności, jak utworzyć rolę niestandardową, odwiedź stronę samouczków rolę niestandardową przy użyciu [programu Azure PowerShell](tutorial-custom-role-powershell.md) lub [wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).
- Jeśli nie można zaktualizować istniejącej roli niestandardowej Sprawdź, czy obecnie logujesz się jako użytkownik, któremu przypisano rolę, która ma `Microsoft.Authorization/roleDefinition/write` uprawnienia, takie jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).
- Jeśli nie możesz usunąć niestandardowej roli i otrzymujesz komunikat o błędzie „Istnieją przypisania ról odwołujące się do roli (kod: RoleDefinitionHasAssignments)”, wówczas istnieją przypisania ról nadal używające roli niestandardowej. Usuń te przypisania ról i spróbuj ponownie usunąć rolę niestandardową.
- Jeśli otrzymujesz komunikat o błędzie „Przekroczono limit definicji ról. Można utworzyć żadnych więcej definicji roli (kod: RoleDefinitionLimitExceeded) "podczas próby utworzenia nowej niestandardowej roli, Usuń role niestandardowe, które nie są używane. Platforma Azure obsługuje maksymalnie **5000** ról niestandardowych w dzierżawie. (Dla chmur specjalne, takie jak platforma Azure Government, Azure (Niemcy) i Azure China 21Vianet limit to 2000 ról niestandardowych).
- Jeśli zostanie wyświetlony komunikat o błędzie podobny do "klient ma uprawnienia do wykonania akcji"Microsoft.Authorization/roleDefinitions/write"w zakresie"/ subscriptions / {subscriptionid}", ale nie znaleziono połączonej subskrypcji" podczas próby aktualizacji roli niestandardowej Sprawdź czy co najmniej jeden [zakresy możliwe do przypisania](role-definitions.md#assignablescopes) zostały usunięte w dzierżawie. Jeśli zakres został usunięty, utwórz bilet pomocy technicznej, ponieważ w tej chwili nie jest dostępne żadne rozwiązanie samoobsługowe.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Odzyskiwanie kontroli dostępu opartej na rolach po przeniesieniu subskrypcji między dzierżawami

- Jeśli potrzebujesz kroki dotyczące przeniesienia subskrypcji do innej usługi Azure AD dzierżawy, zobacz [przenieść własność subskrypcji platformy Azure do innego konta](../billing/billing-subscription-transfer.md).
- Jeśli przeniesiesz subskrypcję do innej dzierżawy usługi Azure AD, wszystkie przypisania ról zostaną trwale usunięte ze źródłowej dzierżawy usługi Azure AD i nie zostaną zmigrowane do docelowej dzierżawy usługi Azure AD. Należy ponownie utworzyć swoje przypisania ról w dzierżawie docelowej. Musisz również ręcznie odtworzyć utracone zarządzanych tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy związane z zarządzanych tożsamości](../active-directory/managed-identities-azure-resources/known-issues.md).
- Jeśli korzystasz z usługi Azure AD administratora globalnego i nie mają dostępu do subskrypcji, po przeniesieniu między dzierżawami, użyj **Access management dla zasobów platformy Azure** przełącznik, aby tymczasowo [podniesienie poziomu dostępu](elevate-access-global-admin.md) do uzyskania dostępu do subskrypcji.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemy z administratorami usług lub współadministratorami

- Jeśli występują problemy z administratorów usługi lub administratorów współpracujących, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](../billing/billing-add-change-azure-subscription-administrator.md) i [ról administratora subskrypcji klasyczne, ról RBAC platformy Azure i usługi Azure AD role administratorów](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odmowa dostępu lub błędy uprawnień

- Jeśli otrzymujesz błąd przypisania „Klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji w zakresie (kod: AuthorizationFailed)” podczas próby utworzenia zasobu, upewnij się, że obecnie logujesz się jako użytkownik mający przypisaną rolę, która ma uprawnienia zapisu do zasobu w wybranym zakresie. Na przykład do zarządzania maszynami wirtualnymi w grupie zasobów musisz mieć rolę [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w grupie zasobów (lub zakresie nadrzędnym). Aby uzyskać listę uprawnień dla każdej roli wbudowanej, zobacz [Role wbudowane dla zasobów platformy Azure](built-in-roles.md).
- Jeśli zostanie wyświetlony błąd uprawnień "Nie masz uprawnień, aby utworzyć żądanie pomocy technicznej" podczas próby utworzenia lub zaktualizowania bilet pomocy technicznej, sprawdź, czy obecnie logujesz się jako użytkownik, któremu przypisano rolę, która ma `Microsoft.Support/supportTickets/write` uprawnienia, takie jak [Współautor żądania obsługi](built-in-roles.md#support-request-contributor).

## <a name="rbac-changes-are-not-being-detected"></a>RBAC zmiany nie są wykrywane

Usługa Azure Resource Manager czasami przechowuje w pamięci podręcznej konfiguracji i danych w celu zwiększenia wydajności. Podczas tworzenia lub usuwania przypisania roli, może potrwać do 30 minut, aby zmiany zaczęły obowiązywać. Jeśli używasz witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmiany przypisania roli, wylogowywania i logowania. Jeśli w przypadku wprowadzania zmian przypisania roli przy użyciu wywołań interfejsu API REST, możesz wymusić odświeżenie, odświeżając tokenu dostępu.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji sieci Web, które wymagają dostępu do zapisu

Jeśli możesz udzielić użytkownikowi dostępu tylko do odczytu do pojedynczej aplikacji sieci web, niektóre funkcje są wyłączone, że może nie oczekujesz. Następujące funkcje zarządzania wymagają **zapisu** dostęp do aplikacji sieci web (współautora lub właściciela), a nie są dostępne w każdym scenariuszu tylko do odczytu.

* Poleceń (takich jak uruchamianie, zatrzymywanie i itp.)
* Zmiany ustawień, takich jak konfiguracja ogólna, ustawienia skalowania, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i inne wpisy tajne, takie jak ustawienia aplikacji i parametry połączenia
* Przesyłanie strumieniowe dzienników
* Dzienniki diagnostyczne konfiguracji
* W konsoli (wiersza polecenia)
* Aktywne i ostatnie wdrożenia (ciągłe wdrażanie lokalnej usługi git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczne tylko dla czytnika, jeśli wcześniej skonfigurowano sieci wirtualnej przez użytkownika z uprawnieniami do zapisu).

Jeśli nie masz dostępu do żadnego z tych kafelków, należy poprosić administratora o prawa dostępu współautora do aplikacji sieci web.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji sieci Web, które wymagają dostępu do zapisu

Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które wzajemne oddziaływania wykresów. Poniżej przedstawiono grupy typowych zasobów przy użyciu kilku witryn sieci Web:

![Grupy zasobów aplikacji sieci Web](./media/troubleshooting/website-resource-model.png)

W rezultacie, gdy przyznasz ktoś dostęp tylko w aplikacji sieci web, wiele funkcji w bloku witryny sieci Web w witrynie Azure portal jest wyłączony.

Te elementy wymagają **zapisu** dostęp do **planu usługi App Service** odnosi się do witryny sieci Web:  

* Wyświetlanie w aplikacji sieci web cenowych warstwy (bezpłatna lub standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia automatycznego skalowania)  
* Przydziały (magazynu, przepustowości, procesor CPU)  

Te elementy wymagają **zapisu** dostęp do całego **grupy zasobów** zawierający witryny sieci Web:  

* Certyfikaty SSL i powiązań (certyfikaty SSL mogą być współużytkowane między lokacjami w tej samej grupie zasobów i lokalizacja geograficzna)  
* Reguły alertów  
* Ustawienia automatycznego skalowania  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w aplikacji sieci web, niektóre funkcje, w bloku maszyny wirtualnej wymagają dostępu do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są związane z nazwy domeny, sieci wirtualne, konta magazynu i reguł alertów.

Te elementy wymagają **zapisu** dostęp do **maszyny wirtualnej**:

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one **zapisu** dostęp do obu **maszyny wirtualnej**i **grupy zasobów** (wraz z nazwą domeny) aby znajdował się w:  

* Zestaw dostępności  
* Zestaw o zrównoważonym obciążeniu  
* Reguły alertów  

Jeśli nie masz dostępu do żadnego z tych kafelków, poproś administratora dostępu współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Usługa Azure Functions i dostęp do zapisu

Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik jest przypisany roli Czytelnik, nie będą mogli wyświetlać funkcje w ramach aplikacji funkcji. Wyświetli portalu **(Brak dostępu)**.

![Brak dostępu aplikacje funkcji](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik mogą kliknąć **funkcje platformy** kartę, a następnie kliknij przycisk **wszystkie ustawienia** Aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobne do aplikacji sieci web), ale nie mogą modyfikować dowolne z tych ustawień.

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](role-assignments-portal.md)
* [Wyświetlanie dzienników aktywności dla zmian RBAC do zasobów platformy Azure](change-history-report.md)

