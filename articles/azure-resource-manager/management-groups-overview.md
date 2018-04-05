---
title: Organizowanie zasobów z grupami zarządzania platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat grupy zarządzania i sposobu ich używania.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: 911043c8fda985f25023dc1224d8e67b283f4d6c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów z grupami zarządzania Azure 

Jeśli Twoja organizacja ma wiele subskrypcji, może być konieczne sposobem wydajnie zarządzać dostępem, zasad i zgodności dla tych subskrypcji. Azure grup zarządzania zapewnia poziom zakresu powyżej subskrypcji. Zorganizować subskrypcje w kontenerach o nazwie "grupy zarządzania", a Twoje warunki ładu do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie odziedziczą warunki stosowane do grupy zarządzania. Grupy zarządzania zapewniają na dużą skalę, niezależnie od tego, jakiego rodzaju subskrypcji może być zarządzania korporacyjnej.

Funkcja grupy zarządzania jest dostępna w publicznej wersji zapoznawczej. Aby rozpocząć korzystanie z zarządzania grupy, zaloguj się do [portalu Azure](https://portal.azure.com) i wyszukaj **grup zarządzania** w **wszystkie usługi** sekcji. 

Na przykład możesz zastosować zasady do grupy zarządzania, która ogranicza regiony dostępne na potrzeby tworzenia maszyny wirtualnej (VM). Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcje i zasobów w ramach tej grupy zarządzania zezwalając tylko maszyny wirtualne mogą być tworzone w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji 

Można tworzyć elastyczne struktury grupy zarządzania i subskrypcji do organizowania zasobów w hierarchii do ujednoliconego zasad i zarządzania dostępem. Na poniższym diagramie przedstawiono przykład hierarchii, które składa się z grupy zarządzania i subskrypcje są zorganizowane według działów.    

![drzewa](media/management-groups/MG_overview.png)

Tworząc hierarchii, które są grupowane według działów, to można przypisać [based kontroli dostępu (RBAC)](../active-directory/role-based-access-control-what-is.md) ról który *dziedziczą* do działów w ramach tej grupy zarządzania. Za pomocą grup zarządzania, można zmniejszyć obciążenie sieci i zmniejsza ryzyko błędu o konieczności tylko raz przypisanie roli. 

### <a name="important-facts-about-management-groups"></a>Ważne informacje o grupy zarządzania
- 10 000 grup zarządzania może być obsługiwany w jednym katalogu. 
- Drzewo grupy zarządzania może obsługiwać do sześciu poziomach głębokości.
    - To ograniczenie nie ma poziomu głównego lub na poziomie subskrypcji.
- Każdej grupie zarządzania może obsługiwać tylko jeden obiekt nadrzędny.
- Każdej grupie zarządzania może mieć wielu elementów podrzędnych. 

### <a name="preview-subscription-visibility-limitation"></a>Ograniczenie widoczności subskrypcji w wersji zapoznawczej 
Obecnie jest to ograniczenie w wersji zapoznawczej, której nie można wyświetlić subskrypcje, które zostały odziedziczone dostęp do. Dostęp jest dziedziczona do subskrypcji, ale usługi Azure Resource Manager nie jest w stanie przyjąć jeszcze dostępu dziedziczenia.  

Przy użyciu interfejsu API REST, aby uzyskać informacje o subskrypcji zwraca szczegółowe informacje, masz dostęp, ale w portalu Azure i programu Azure Powershell nie pokazuj subskrypcji. 

Ten element jest wykorzystywanej na i zostanie rozwiązany, zanim grupy zarządzania są ogłaszane jako "Ogólnej dostępności."  

### <a name="cloud-solution-providercsp-limitation-during-preview"></a>Rozwiązanie Provider(CSP) ograniczenia wersji zapoznawczej w chmurze 
Jest to aktualne ograniczenie dla chmury rozwiązania Provider(CSP) partnerów, której nie można tworzyć ani Zarządzanie grupami zarządzania ich odbiorców w katalogu ich klienta.  
Ten element jest wykorzystywanej na i zostanie rozwiązany, zanim grupy zarządzania są ogłaszane jako "Ogólnej dostępności."


## <a name="root-management-group-for-each-directory"></a>Grupa zarządzania głównego dla każdego katalogu

Każdy katalog znajduje się grupę pojedynczego zarządzania najwyższego poziomu o nazwie grupy zarządzania "Root". Tej grupy zarządzania głównego jest wbudowana w hierarchii, aby wszystkie grupy zarządzania i subskrypcje fold do niego. Tej grupy zarządzania głównego umożliwia globalnych zasad i przypisań RBAC można zastosować na poziomie katalogu. [Katalogu Administrator musi się podnieść](../active-directory/role-based-access-control-tenant-admin-access.md) początkowo jako właściciela tej grupy głównego. Gdy administrator jest właścicielem grupy, może on przypisać dowolnej roli RBAC do innego katalogu użytkowników lub grup, do zarządzania w hierarchii.  

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje o grupie zarządzania głównego
- Nazwa i identyfikator grupy zarządzania głównego podano identyfikator usługi Azure Active Directory domyślnie. Nazwa wyświetlana może zostać zaktualizowana w dowolnym momencie, aby wyświetlić różne w portalu Azure. 
- Wszystkie subskrypcje i grup zarządzania może składania w grupie zarządzania jednego katalogu głównego w katalogu.  
    - Zalecane jest, aby wszystkie elementy w złożenia katalogu do głównej grupy zarządzania do globalnego zarządzania.  
    - W publicznej wersji zapoznawczej wszystkie subskrypcje w katalogu nie zostaną automatycznie wprowadzone elementów podrzędnych elementu głównego.   
    - W publicznej wersji zapoznawczej nowe subskrypcje nie są automatycznie domyślnie do głównej grupy zarządzania. 
- Głównej grupy zarządzania nie może zostać przeniesiony lub usunięty, w przeciwieństwie do innych grup zarządzania. 
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługuje [based kontroli dostępu (RBAC)](../active-directory/role-based-access-control-what-is.md) dla wszystkich uzyskuje dostęp do zasobów i definicje ról. Te uprawnienia są dziedziczone przez zasoby podrzędne, które istnieją w hierarchii.   

Podczas żadnego [wbudowanej roli RBAC](../active-directory/role-based-access-control-what-is.md#built-in-roles) mogą być przypisane do grupy zarządzania są cztery role, które są często używane: 
- **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników. 
- **Współautor** można tworzyć i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników.
- **Współautor zasad zasobów** można utworzyć zasad i zarządzanie nimi w katalogu na zasobach.     
- **Czytnik** można wyświetlić istniejących zasobów platformy Azure. 


## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat grup zarządzania, zobacz: 
- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](management-groups-create.md)
- [Jak zmienić, usunąć lub zarządzać grupami zarządzania](management-groups-manage.md)
- [Zainstaluj moduł Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Przejrzyj specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Zainstaluj rozszerzenie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

