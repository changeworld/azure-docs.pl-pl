---
title: Access Control oparte na rolach i Privileged Identity Management w Australii platformy Azure
description: Wskazówki dotyczące implementowania Access Control opartej na rolach i Privileged Identity Management w regionach australijskich w celu spełnienia określonych wymagań obowiązujących w obowiązujących przepisach instytucji rządowych.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a9451b5dcd572eee27809cf66df889f06da960ed
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571499"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Access Control oparte na rolach (RBAC) i Privileged Identity Management (PIM)

Zarządzanie uprawnieniami administracyjnymi jest krytycznym krokiem w zakresie zapewniania bezpieczeństwa w każdym środowisku IT. Ograniczanie uprawnień administracyjnych przy użyciu zabezpieczeń najniższych uprawnień jest wymaganiem [ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) i formą części [ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) list zaleceń dotyczących zabezpieczeń.

Firma Microsoft oferuje zestaw kontrolek umożliwiających implementację just-in-Time i dostęp do nich tylko w ramach Microsoft Azure. Zrozumienie tych formantów jest niezbędne dla efektywnego stan zabezpieczeń w chmurze. W tym przewodniku przedstawiono przegląd samych formantów i kluczowych zagadnień projektowych podczas ich wdrażania.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Access Control oparte na rolach to centralne zarządzanie dostępem do wszystkich zasobów w ramach Microsoft Azure i zarządzania Azure Active Directory (Azure AD). Kontrolę RBAC można zaimplementować wraz z szeregiem dodatkowych funkcji dostępnych na platformie Azure. Ten artykuł koncentruje się na implementowaniu efektywnej kontroli RBAC przy użyciu usługi Azure Grupy zarządzania, grup Azure Active Directory i platformy Azure Privileged Identity Management (PIM).

Na wysokim poziomie implementacja RBAC wymaga trzech składników:

![RBAC — przegląd](media/rbac-overview.png)

* **Podmioty zabezpieczeń**: Podmiot zabezpieczeń może być jednym z następujących elementów: Użytkownik, Grupa, [nazwy główne usług lub [tożsamość zarządzana](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Do podmiotów zabezpieczeń należy przypisać uprawnienia przy użyciu grup Azure Active Directory.

* **Definicje ról**: Definicja roli, nazywana również rolą, jest kolekcją uprawnień. Te uprawnienia definiują operacje, które mogą być wykonywane przez podmioty zabezpieczeń przypisane do definicji roli. Ta funkcja jest udostępniana przez role zasobów platformy Azure i role administratorów Azure Active Directory. Platforma Azure udostępnia zestaw wbudowanych ról (link), które można rozszerzyć za pomocą ról niestandardowych.

* **Zakres**: Zakresem jest zestaw zasobów platformy Azure, do których jest stosowana definicja roli. Role platformy Azure można przypisywać do zasobów platformy Azure przy użyciu usługi Azure Grupy zarządzania.

Te trzy składniki są łączone w celu przyznania podmiotom zabezpieczeń dostępu zdefiniowanego w definicjach ról do wszystkich zasobów, które są objęte zakresem Grupy zarządzania platformy Azure, jest to nazywane przypisaniem roli. Do podmiotu zabezpieczeń można przypisać wiele definicji ról, a do jednego zakresu można przypisać wiele podmiotów zabezpieczeń.

### <a name="azure-active-directory-groups"></a>Grupy Azure Active Directory

W przypadku przypisywania uprawnień osobom lub zespołom, jeśli to możliwe, przypisanie powinno być połączone z grupą Azure Active Directory i nie jest przypisane bezpośrednio do danego użytkownika. Jest to takie samo zalecane rozwiązanie dziedziczone z lokalnych implementacji Active Directory. W miarę możliwości można tworzyć grupy Azure Active Directory dla poszczególnych zespołów, uzupełniając do struktury logicznej Grupy zarządzania utworzonego platformy Azure.

W scenariuszu chmury hybrydowej lokalne grupy zabezpieczeń systemu Windows Server Active Directory mogą być synchronizowane z wystąpieniem Azure Active Directory. Jeśli zaimplementowano już lokalne usługi RBAC przy użyciu tych grup zabezpieczeń systemu Windows Server Active Directory, te grupy po zsynchronizowaniu mogą być używane do implementowania RBAC dla zasobów platformy Azure. W przeciwnym razie środowisko chmury może być widoczne jako czysty, aby zaprojektować i zaimplementować niezawodny plan zarządzania uprawnieniami, który został utworzony na podstawie implementacji Azure Active Directory.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Role zasobów platformy Azure i role administratorów Azure Active Directory

Microsoft Azure oferuje szeroką gamę wbudowanych ról dla [zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) i [Azure Active Directory administrowania nimi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Oba typy ról zapewniają szczegółowy dostęp do zasobów platformy Azure lub dla administratorów usługi Azure AD. Należy pamiętać, że ról zasobów platformy Azure nie można używać w celu zapewnienia dostępu administracyjnego do usługi Azure AD, a role usługi Azure AD nie zapewniają określonego dostępu do zasobów platformy Azure.

Niektóre przykłady typów dostępu, które można przypisać do zasobu platformy Azure przy użyciu wbudowanej roli, to:

* Zezwalanie jednemu użytkownikowi na zarządzanie maszynami wirtualnymi w ramach subskrypcji, a innemu na zarządzanie sieciami wirtualnymi.
* Zezwalanie grupie administratorów baz danych na zarządzanie bazami danych SQL w ramach subskrypcji.
* Zezwalanie użytkownikowi na zarządzanie wszystkimi zasobami w grupie zasobów, w tym maszynami wirtualnymi, witrynami internetowymi i podsieciami.
* Zezwalanie aplikacji na dostęp do wszystkich zasobów w grupie zasobów.

Przykłady typów dostępu, które mogą być przypisane do administrowania usługą Azure AD:

* Zezwalaj pracownikom pomocy technicznej na resetowanie haseł użytkowników
* Zezwalaj pracownikom na zapraszanie zewnętrznych użytkowników do wystąpienia usługi Azure AD na potrzeby współpracy B2B
* Zezwalaj pracownikom administracyjnym na dostęp do odczytu do raportów logowania i inspekcji
* Zezwalaj personelowi na zarządzanie wszystkimi użytkownikami i grupami, w tym resetowanie haseł.

Ważne jest zapoznanie się z pełną listą dozwolonych akcji, a wbudowana rola zapewnia, że nieuprawniony dostęp do nie zostanie udzielony. Lista wbudowanych ról i uzyskiwany przez nie dostęp są stale rozwijane. Pełna lista ról i ich definicji można wyświetlić, przeglądając dokumentację połączoną powyżej lub przy użyciu polecenia cmdlet Azure PowerShell:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

lub interfejsu wiersza polecenia platformy Azure:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Istnieje również możliwość utworzenia niestandardowych ról zasobów platformy Azure zgodnie z potrzebami. Te role niestandardowe można tworzyć w Azure Portal za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Podczas tworzenia ról niestandardowych konieczna jest upewnienie się, że rola jest unikatowa i że jej funkcja nie została jeszcze udostępniona przez istniejącą rolę zasobów platformy Azure. Zmniejsza to ciągłość zarządzania i zmniejsza ryzyko, że podmioty zabezpieczeń nie otrzymują niepotrzebnych uprawnień. Przykładem może być utworzenie niestandardowej roli zasobów platformy Azure, która znajduje się między wbudowanymi rolami zasobów platformy Azure "Współautor maszyny wirtualnej" i "Logowanie administratora maszyny wirtualnej".

Rola niestandardowa może opierać się na istniejącej roli współautor, która przyznaje następujący dostęp:

| Zasób platformy Azure | Poziom dostępu |
| --- | --- |
| Maszyny wirtualne | Może zarządzać, ale nie może uzyskać dostępu |
| Virtual Network dołączone do maszyny wirtualnej | Nie można uzyskać dostępu |
| Magazyn dołączony do maszyny wirtualnej | Nie można uzyskać dostępu |
|

Rola niestandardowa może zachować ten podstawowy dostęp, ale umożliwia wyznaczenim użytkownikom pewne dodatkowe uprawnienia do modyfikowania konfiguracji sieci maszyn wirtualnych.

Role zasobów platformy Azure mają również możliwość przypisywania do zasobów za pośrednictwem usługi Azure Grupy zarządzania.

### <a name="azure-management-groups"></a>Grupy zarządzania platformy Azure

Usługa Azure Grupy zarządzania może być używana przez organizację do zarządzania przypisaniem ról do wszystkich subskrypcji i ich zasobów w ramach dzierżawy platformy Azure. Usługa Azure Grupy zarządzania została zaprojektowana tak, aby można było tworzyć hierarchie zarządzania, w tym możliwość mapowania struktury organizacyjnej hierarchicznie na platformę Azure. Tworzenie organizacyjnych jednostek roboczych jako oddzielne jednostki logiczne pozwala na stosowanie uprawnień w organizacji na podstawie określonych wymagań poszczególnych zespołów. Za pomocą usługi Azure Grupy zarządzania można zdefiniować hierarchię zarządzania o maksymalnym sześciu poziomach.

![Grupy zarządzania](media/management-groups.png)

Usługa Azure Grupy zarządzania jest zamapowana na subskrypcje platformy Azure w ramach dzierżawy platformy Azure. Dzięki temu organizacja może oddzielić zasoby platformy Azure należące do określonych jednostek biznesowej i zapewnić poziom szczegółowości kontroli nad zarządzaniem kosztami i przypisaniem uprawnień.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Firma Microsoft zaimplementował just in Time (JIT) i tylko wystarczający dostęp (JEA) za pomocą usługi Azure Privileged Identity Management. Ta usługa umożliwia pracownikom administracyjnym kontrolowanie i monitorowanie uprzywilejowanego dostępu do zasobów platformy Azure oraz zarządzanie nim. Program PIM umożliwia podmiotom zabezpieczeń "kwalifikowanie się" dla roli przez pracowników administracyjnych, co pozwala użytkownikom na żądanie aktywacji roli za pośrednictwem Azure Portal lub poleceń cmdlet programu PowerShell. Domyślnie przypisanie roli można aktywować przez okres od 1 do 72 godzin. Jeśli to konieczne, użytkownik może zażądać rozszerzenia do przypisania roli, a istnieje możliwość trwałego przypisania roli. Opcjonalnie można wymusić wymaganie uwierzytelniania wieloskładnikowego, gdy użytkownicy zażądają aktywacji odpowiednich ról. Po wygaśnięciu przydzielonego okresu aktywacji roli podmiot zabezpieczeń nie ma już uprzywilejowanego dostępu udzielonego przez rolę.

Korzystanie z programu PIM uniemożliwia wykonywanie typowych problemów z przypisaniem uprawnień, które mogą wystąpić w środowiskach, które nie korzystają z dostępu just in Time lub nie przeprowadzają rutynowych inspekcji przypisania uprawnień. Typowym problemem jest przypisanie podniesionych uprawnień i pozostało do czasu ukończenia zadania wymagającego podwyższonego poziomu uprawnień. Innym problemem jest proliferacja podwyższonych uprawnień w środowisku poprzez klonowanie dostępu przypisanego do podmiotu zabezpieczeń podczas konfigurowania innych podobnych podmiotów zabezpieczeń.

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

Podczas projektowania strategii RBAC z zamiarem wymuszania najniższych uprawnień należy wziąć pod uwagę następujące wymagania dotyczące zabezpieczeń:

* Żądania dostępu uprzywilejowanego są weryfikowane
* Uprawnienia administracyjne są ograniczone do minimalnego dostępu wymaganego do wykonania określonych obowiązków
* Uprawnienia administracyjne są ograniczone do minimalnego przedziału czasu wymaganego do wykonania określonych obowiązków
* Podejmowane są regularne przeglądy przyznanych uprawnień administracyjnych

Proces projektowania strategii RBAC będzie wymagał szczegółowego przeglądu funkcji firmy, aby zrozumieć różnicę dostępu między różnymi rolami biznesowymi oraz typ i częstotliwość pracy wymagającej podwyższonego poziomu uprawnień. Różnica w działaniu między operatorem kopii zapasowej, administratorem zabezpieczeń i audytorem będzie wymagała różnych poziomów dostępu w różnym czasie z różnymi poziomami trwającego przeglądu.

## <a name="validate-requests-for-access"></a>Weryfikuj żądania dostępu

Podwyższone uprawnienia muszą być jawnie zatwierdzone. Aby to umożliwić, należy opracować proces zatwierdzania i odpowiedni personel odpowiedzialny za sprawdzenie, czy wszystkie żądania dotyczące dodatkowych uprawnień są wiarygodne. Privileged Identity Management udostępnia wiele opcji zatwierdzania przypisywania ról. Żądanie aktywacji roli można skonfigurować tak, aby zezwalało na samozatwierdzenie lub być warunkowe i wymagało, aby mianowani osoby zatwierdzające mogli ręcznie przejrzeć i zatwierdzić wszystkie żądania aktywacji roli. Żądania aktywacji można także skonfigurować tak, aby wymagały dodatkowych informacji pomocniczych zawartych w żądaniu aktywacji, takich jak numery biletów.

### <a name="restrict-privilege-based-on-duties"></a>Ograniczanie uprawnień na podstawie obowiązków

Ograniczanie poziomu uprawnień przyznawanych podmiotom zabezpieczeń ma krytyczne znaczenie, ponieważ przez przydzielenie uprawnień jest wspólnym wektorem ataków w zabezpieczeniach IT. Typy zarządzanych zasobów i zespoły odpowiedzialne muszą być oceniane, aby można było przypisać minimalny poziom uprawnień wymagany do wykonywania codziennych obowiązków. Dodatkowe uprawnienia, które wykraczają poza te wymagane do wykonywania codziennych obowiązków, powinny być tylko w okresie potrzebnym do wykonania określonego zadania. Przykładem może być umożliwienie użytkownikowi dostępu "Współautor" do administratora klienta, ale umożliwienie im żądania uprawnień "właściciel" dla zasobu platformy Azure dla określonego zadania wymagającego tymczasowego dostępu wysokiego poziomu.

Dzięki temu każdy administrator indywidualny ma tylko podwyższony poziom dostępu do najkrótszego okresu. Przestrzeganie tych praktyk zmniejsza ogólną powierzchnię ataku dla wszystkich organizacji infrastruktury IT.

### <a name="regular-evaluation-of-administrative-privilege"></a>Regularna Ocena uprawnień administracyjnych

Należy pamiętać, że podmioty zabezpieczeń w środowisku podlegają rutynowej inspekcji, aby upewnić się, że jest obecnie przypisany prawidłowy poziom uprawnień. Microsoft Azure zapewnia wiele środków na inspekcję i ocenę uprawnień przypisanych do podmiotów zabezpieczeń platformy Azure. Privileged Identity Management umożliwia pracownikom administracyjnym okresowe wykonywanie "przeglądów dostępu" ról przyznanych podmiotom zabezpieczeń. Przegląd dostępu można wykonać w celu przeprowadzenia inspekcji zarówno przypisania roli zasobów platformy Azure, jak i Azure Active Directory przypisania roli administracyjnej. Przegląd dostępu można skonfigurować z następującymi właściwościami:

* **Przejrzyj nazwę i przejrzyj datę początkową i końcową**: Przeglądy należy skonfigurować tak, aby były wystarczająco długie, aby użytkownicy mianowani.

* **Rola do przejrzenia**: Każdy przegląd dostępu koncentruje się na jednej roli platformy Azure.

* **Nominowani recenzenci**: Dostępne są trzy opcje przeprowadzania przeglądu. Możesz przypisać przegląd do kogoś innego, aby zakończyć pracę, możesz zrobić to samodzielnie lub każdy użytkownik może przejrzeć własny dostęp.

* **Wymagaj od użytkowników podania przyczyny dostępu**: Użytkownicy mogą być zobowiązani do wprowadzenia przyczyny utrzymania poziomu uprawnień podczas kończenia przeglądu dostępu.

Postęp oczekujących przeglądów dostępu można monitorować w dowolnym momencie za pośrednictwem pulpitu nawigacyjnego w Azure Portal. Dostęp do przeglądanej roli pozostanie niezmieniony do momentu ukończenia przeglądu dostępu. Istnieje również możliwość [inspekcji](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) wszystkich przypisań użytkowników i aktywacji w określonym okresie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem na temat [monitorowania systemu w Australii platformy Azure](system-monitor.md).
