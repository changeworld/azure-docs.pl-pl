---
title: Organizowanie zasobów z grupami zarządzania platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat grupy zarządzania i sposobu ich używania.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/28/2018
ms.author: rithorn
ms.openlocfilehash: 611faef7e4b94b1734896fb64ca29540b12bc057
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102549"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów z grupami zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, może być konieczne sposobem wydajnie zarządzać dostępem, zasad i zgodności dla tych subskrypcji. Grupy zarządzania platformy Azure umożliwiają na poziomie zakresu powyżej subskrypcji. Zorganizować subskrypcje w kontenerach o nazwie "grupy zarządzania", a Twoje warunki ładu do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie odziedziczą warunki stosowane do grupy zarządzania. Grupy zarządzania zapewniają na dużą skalę, niezależnie od tego, jakiego rodzaju subskrypcji może być zarządzania korporacyjnej.

Funkcja grupy zarządzania jest dostępna w publicznej wersji zapoznawczej. Aby rozpocząć korzystanie z grup zarządzania, zaloguj się w [portalu Azure](https://portal.azure.com) i wyszukaj **grup zarządzania** w **wszystkie usługi** sekcji.

Na przykład możesz zastosować zasady do grupy zarządzania, która ogranicza regiony dostępne na potrzeby tworzenia maszyny wirtualnej (VM). Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcje i zasobów w ramach tej grupy zarządzania zezwalając tylko maszyny wirtualne mogą być tworzone w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można tworzyć elastyczne struktury grupy zarządzania i subskrypcji do organizowania zasobów w hierarchii do ujednoliconego zasad i zarządzania dostępem.
Na poniższym diagramie przedstawiono przykład hierarchii, które składa się z grupy zarządzania i subskrypcje są zorganizowane według działów.

![Drzewa](media/management-groups/MG_overview.png)

Tworząc hierarchii, które są grupowane według działów, można przypisać [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md) ról który *dziedziczą* do działów w ramach tej grupy zarządzania. Za pomocą grup zarządzania, można zmniejszyć obciążenie sieci i zmniejsza ryzyko błędu o konieczności tylko raz przypisanie roli.

### <a name="important-facts-about-management-groups"></a>Ważne informacje o grupy zarządzania

- 10 000 grup zarządzania może być obsługiwany w jednym katalogu.
- Drzewo grupy zarządzania może obsługiwać do sześciu poziomach głębokości.
  - Ten limit nie zawiera poziomu głównego lub na poziomie subskrypcji.
- Każda grupa zarządzania i subskrypcji może obsługiwać tylko jeden obiekt nadrzędny.
- Każdej grupie zarządzania może mieć wielu elementów podrzędnych.
- Wszystkie subskrypcje i grup zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [ważnych informacji o grupie zarządzania głównego](#important-facts-about-the-root-management-group) wyjątków w wersji zapoznawczej.

### <a name="preview-subscription-visibility-limitation"></a>Ograniczenie widoczności subskrypcji w wersji zapoznawczej

Obecnie jest to ograniczenie w wersji zapoznawczej, której nie można wyświetlić subskrypcje, które zostały odziedziczone dostęp do. Dostęp jest dziedziczona do subskrypcji, ale usługi Azure Resource Manager nie jest w stanie przyjąć jeszcze dostępu dziedziczenia.  

Przy użyciu interfejsu API REST, aby uzyskać informacje o subskrypcji zwraca szczegółowe informacje, masz dostęp, ale w portalu Azure i programu Azure Powershell nie pokazuj subskrypcji.

Ten element jest wykorzystywanej na i zostanie rozwiązany przed zarządzania, które grupy są ogłaszane jako "Ogólnej dostępności."  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Cloud Solution Provider (CSP) ograniczenia wersji zapoznawczej

Jest to aktualne ograniczenie dla partnerów Cloud Solution Provider (CSP), której nie można tworzyć ani Zarządzanie grupami zarządzania ich odbiorców w katalogu ich klienta.  
Ten element jest wykorzystywanej na i zostanie rozwiązany przed zarządzania, które grupy są ogłaszane jako "Ogólnej dostępności."

## <a name="root-management-group-for-each-directory"></a>Grupa zarządzania głównego dla każdego katalogu

Każdy katalog znajduje się grupę pojedynczego zarządzania najwyższego poziomu o nazwie grupy zarządzania "Root". Tej grupy zarządzania głównego jest wbudowana w hierarchii, aby wszystkie grupy zarządzania i subskrypcje fold do niego. Tej grupy zarządzania głównego umożliwia globalnych zasad i przypisań RBAC można zastosować na poziomie katalogu. [Katalogu Administrator musi się podnieść](../role-based-access-control/elevate-access-global-admin.md) początkowo jako właściciela tej grupy głównego. Gdy administrator jest właścicielem grupy, może on przypisać dowolnej roli RBAC do innego katalogu użytkowników lub grup, do zarządzania w hierarchii.  

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje o grupie zarządzania głównego

- Domyślnie nadawane są nazwy i Identyfikatora grupy zarządzania głównego. Nazwa wyświetlana może zostać zaktualizowana w dowolnym momencie, aby wyświetlić różne w portalu Azure.
  - Nazwa będzie "Dzierżawy głównej grupy".
  - Identyfikator będzie identyfikator usługi Azure Active Directory.
- Głównej grupy zarządzania nie może zostać przeniesiony lub usunięty, w przeciwieństwie do innych grup zarządzania.  
- Wszystkie subskrypcje i grup zarządzania składania w grupie zarządzania jednego katalogu głównego w katalogu.
  - Wszystkie zasoby w katalogu składania do głównej grupy zarządzania do zarządzania globalnego.
  - Nowe subskrypcje są automatycznie ustawiana domyślnie do głównej grupy zarządzania podczas tworzenia.
- Wszyscy klienci Azure widzą głównej grupy zarządzania, ale nie wszyscy klienci mają dostęp do zarządzania tej grupy zarządzania głównego.
  - Każdego, kto ma dostęp do subskrypcji można znajdują się w kontekście, z którym subskrypcji znajduje się w hierarchii.  
  - Nie podano domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni katalogu są tylko użytkownicy, którzy mogą podniesienia uprawnień użytkownika do uzyskania dostępu.  Gdy użytkownicy mają dostęp, Administratorzy katalogu można przypisać dowolnej roli RBAC do innych użytkowników do zarządzania.  

>[!NOTE]
>Jeśli katalogu uruchomiony przy użyciu usługi grupy zarządzania przed 6/25/2018, katalogu może nie być skonfigurowany z wszystkie subskrypcje w hierarchii. Zespół grupy zarządzania jest wstecznie aktualizowania każdego katalogu, w którym uruchomiony przy użyciu grup zarządzania w publicznej wersji zapoznawczej przed tą datą w lipcu 2018. Wszystkie subskrypcje w katalogach będą dzieci w ramach grupy zarządzania głównego wcześniejszych.  
>
>Jeśli masz pytania dotyczące tego procesu wsteczny, skontaktuj się z: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Początkowej konfiguracji grupy zarządzania

Po uruchomieniu dowolnego użytkownika przy użyciu grup zarządzania jest procesem początkowej konfiguracji, wykonywanej. Pierwszym krokiem jest głównej grupy zarządzania jest tworzony w katalogu. Po utworzeniu tej grupy, wszystkie istniejące subskrypcje, które istnieją w katalogu są wprowadzane elementy podrzędne głównej grupy zarządzania.  Przyczyna tego procesu jest upewnij się, że istnieje tylko jedna hierarchia grupy zarządzania w katalogu.  Pojedynczą hierarchię w katalogu umożliwia administracyjne stosowanie globalnego dostępu i zasad, które innych klientów w katalogu nie można pominąć. Cokolwiek przypisane w katalogu głównym zostaną zastosowane do wszystkich grup zarządzania, subskrypcji, grupy zasobów i zasobów w ramach katalogu przez jedną hierarchię w katalogu.  

> [!IMPORTANT]
> Wszystkie przypisania przypisywanie dostępu lub zasad użytkownika w głównej grupie zarządzania **ma zastosowanie do wszystkich zasobów w katalogu**. W związku z tym wszystkich klientów należy ocenić, trzeba mieć elementów zdefiniowanych w tym zakresie.  Przypisania dostępu i zasad użytkowników powinny być "Musi mieć" tylko w tym zakresie.  
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Obsługa grup zarządzania Azure [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md) dla wszystkich uzyskuje dostęp do zasobów i definicje ról. Te uprawnienia są dziedziczone przez zasoby podrzędne, które istnieją w hierarchii. Wszelkie wbudowane role RBAC można przypisać do grupy zarządzania, który będzie dziedziczyć w dół hierarchii do zasobów.  Na przykład Współautor RBAC roli maszyny Wirtualnej można przypisać do grupy zarządzania. Ta rola nie ma działań w grupie zarządzania, ale będzie dziedziczyć na wszystkich maszynach wirtualnych w ramach tej grupy zarządzania.  

W poniższej tabeli przedstawiono listę ról i obsługiwane akcje w grupach zarządzania.

| Nazwa roli RBAC             | Przycisk Utwórz | Zmiana nazwy | Move | Usuwanie | Przypisywanie dostępu | Przypisz zasady | Odczyt  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X    | X      | X             |               | X     |
|Współautor                 | X      | X      | X    | X      |               |               | X     |
|Czytelnik                      |        |        |      |        |               |               | X     |
|Współautor zasad zasobów |        |        |      |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Definicja roli RBAC niestandardowych i przypisania

Role RBAC niestandardowe nie są obsługiwane na grup zarządzania w tej chwili.  Zobacz [forum opinii grupy zarządzania](https://aka.ms/mgfeedback) do wyświetlenia stanu tego elementu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](management-groups-create.md)
- [Jak zmienić, usunąć lub zarządzać grupami zarządzania](management-groups-manage.md)
- [Zainstaluj moduł Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Przejrzyj specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Zainstaluj rozszerzenie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
