---
title: Organizowanie zasobów przy użyciu grup zarządzania platformy Azure | Dokumentacja firmy Microsoft
description: Informacje dotyczące grupy zarządzania i sposobu ich używania.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2018
ms.author: rithorn
ms.openlocfilehash: 146ded37dbf517528af23574cd5b9325f4b5f9d0
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358773"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów przy użyciu grup zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Organizowanie subskrypcji do kontenerów o nazwie "management groups" i zastosować warunkach nadzoru do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki stosowane do grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie korporacyjnej na dużą skalę, niezależnie od tego, jakiego rodzaju subskrypcji może być.

Na przykład można zastosować zasady do grupy zarządzania, który ogranicza regionów, które są dostępne na potrzeby tworzenia maszyny wirtualnej (VM). Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, poprzez zezwolenie wyłącznie na maszyny wirtualne zostały utworzone w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcje

Można tworzyć elastyczne struktury grupy zarządzania i subskrypcji do organizowania zasobów w hierarchii dla ujednoliconego zasad i zarządzania dostępem.
Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla zarządzania przy użyciu grup zarządzania.

![drzewo](media/management-groups/MG_overview.png)

Tworząc hierarchię podobną do przykładowej można zastosować zasady, na przykład lokalizacji maszyny Wirtualnej na "infrastruktury grupa zarządzania" ograniczone do regionu zachodnie stany USA do Włącz wewnętrznego zgodności i zasad zabezpieczeń. Te zasady będą dziedziczyć na obu subskrypcji umowy EA, w ramach tej grupy zarządzania i będą stosowane do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Ponieważ te zasady dziedziczy z grupy zarządzania do subskrypcji, te zasady zabezpieczeń nie można zmienić właściciela zasobów lub subskrypcji, co zapewnia ulepszone nadzoru.

Inny scenariusz, w którym będzie korzystać z grupy zarządzania jest aby udostępniała użytkownikom dostęp do wielu subskrypcji.  Przenosząc wiele subskrypcji w ramach tej grupy zarządzania, użytkownik może utworzyć jedno przypisanie RBAC w grupie zarządzania, co będzie dziedziczyć że dostęp do wszystkich subskrypcji.  Bez konieczności przypisania RBAC skryptu przez wiele subskrypcji co przypisania grupy zarządzania można włączyć użytkownicy mieli dostęp do wszystkiego, czego potrzebują.

### <a name="important-facts-about-management-groups"></a>Ważne informacje o grupy zarządzania

- 10 000 grupy zarządzania mogą być obsługiwane w jednym katalogu.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześciu poziomach głębokości.
  - Ten limit nie zawiera poziomu głównego lub na poziomie subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jedną jednostkę nadrzędną.
- Jedna grupa zarządzania może mieć wiele obiektów podrzędnych.
- Wszystkie subskrypcje i grup zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [ważnych informacji o grupie zarządzania głównym](#important-facts-about-the-root-management-group) do obsługi wyjątków w wersji zapoznawczej.

## <a name="root-management-group-for-each-directory"></a>Grupa zarządzania głównego dla każdego katalogu

Każdy katalog jest podana przez pojedynczą najwyższego poziomu grupę zarządzania o nazwie grupy zarządzania "Root". Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Tej grupy zarządzania głównego umożliwia globalnych zasad i przypisania RBAC można stosować na poziomie katalogu. [Directory Administrator musi się podnieść](../role-based-access-control/elevate-access-global-admin.md) początkowo jako właściciela tej grupy katalogu głównego. Gdy administrator jest właścicielem grupy, można przypisać każdej roli RBAC, do innych użytkowników katalogu lub grup, do zarządzania w hierarchii.  

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje o głównej grupy zarządzania

- Domyślnie są nadawane nazwy i Identyfikatora grupy zarządzania głównym. Nazwa wyświetlana może zostać zaktualizowana w dowolnym momencie, aby wyświetlić różne w witrynie Azure portal.
  - Nazwą będzie "Dzierżawy głównej grupy".
  - Identyfikator będzie identyfikator usługi Azure Active Directory.
- Głównej grupy zarządzania nie przeniesiony lub usunięty, w przeciwieństwie do innych grup zarządzania.  
- Wszystkie subskrypcje i grup zarządzania składa z grupą zarządzania jeden katalog główny, w katalogu.
  - Wszystkie zasoby w katalogu składa do głównej grupy zarządzania do globalnego zarządzania.
  - Nowe subskrypcje są automatycznie ustawiana domyślnie do głównej grupy zarządzania podczas tworzenia.
- Wszystkich klientów platformy Azure może zobaczyć głównej grupy zarządzania, ale nie wszyscy klienci mają dostęp do zarządzania tej grupy zarządzania głównym.
  - Każdy, kto ma dostęp do subskrypcji można zobaczyć kontekst, w której subskrypcji znajduje się w hierarchii.  
  - Nikt nie otrzymuje dostęp do domyślnej głównej grupy zarządzania. Administratorzy globalni katalogu są tylko użytkownicy, którzy podnieść poziom konta użytkownika do uzyskania dostępu.  Gdy użytkownicy mają dostęp, Administratorzy katalogu mogą przypisywać żadnych ról RBAC, do innych użytkowników do zarządzania.  

>[!NOTE]
>Jeśli Twój katalog pracę, przy użyciu usługi grupy zarządzania przed 6 25 2018, katalogiem może nie można skonfigurować w przypadku wszystkich subskrypcji w hierarchii. Zespół zarządzający grupy wstecznie aktualizuje każdy katalog, który uruchamiany przy użyciu grup zarządzania w publicznej wersji zapoznawczej przed tą datą w lipcu/sierpnia 2018. Wszystkie subskrypcje w katalogach zostaną wprowadzone elementy podrzędne w ramach grupy zarządzania głównym poprzedniego.  
>
>Jeśli masz pytania na temat tego procesu wsteczną kontaktu: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Początkowej konfiguracji grupy zarządzania

Po uruchomieniu dowolnego użytkownika przy użyciu grup zarządzania jest procesem konfiguracji początkowej zachodzącej. Pierwszym krokiem jest, że głównej grupy zarządzania jest tworzony w katalogu. Po utworzeniu tej grupy, wszystkie istniejące subskrypcje, które istnieją w katalogu są wykonywane elementy podrzędne głównej grupy zarządzania.  Przyczyna tego procesu jest upewnij się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu.  Pojedynczą hierarchię w katalogu pozwala administracyjne klientom na stosowanie globalnego dostępu i zasad, które inni klienci w katalogu nie można pominąć. Cokolwiek przypisane w katalogu głównym będą stosowane we wszystkich grup zarządzania, subskrypcji, grupy zasobów i zasobów w ramach katalogu dzięki jednej hierarchii w katalogu.  

> [!IMPORTANT]
> Wszystkie przypisania dostępu lub zasad przypisania użytkownika w głównej grupie zarządzania **ma zastosowanie do wszystkich zasobów w katalogu**. W związku z tym wszyscy klienci powinni rozważyć zapewnienie elementy zdefiniowane w tym zakresie.  Przypisania dostępu i zasad użytkownika powinien być "Muszą mieć" tylko w tym zakresie.  
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Obsługa grup zarządzania platformy Azure [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md) dla wszystkich uzyskuje dostęp do zasobów i definicje ról. Te uprawnienia są dziedziczone z zasobami podrzędne, które istnieją w hierarchii. Wszelkie wbudowana rola RBAC można przypisać do grupy zarządzania, który będzie dziedziczyć w dół hierarchii do zasobów.  Na przykład Współautor maszyny Wirtualnej rolę RBAC można przypisać do grupy zarządzania. Ta rola ma żadnych działań w grupie zarządzania, ale będzie dziedziczyć do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.  

Poniższej tabeli przedstawiono listę ról i obsługiwane operacje na grupach zarządzania.

| Nazwa roli RBAC             | Przycisk Utwórz | Zmiana nazwy | Move | Usuwanie | Przypisywanie dostępu | Przypisz zasady | Odczyt  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X    | X      | X             |               | X     |
|Współautor                 | X      | X      | X    | X      |               |               | X     |
|Współautor MG *             | X      | X      | X    | X      |               |               | X     |
|Czytelnik                      |        |        |      |        |               |               | X     |
|Czytnik MG *                  |        |        |      |        |               |               | X     |
|Współautor zasad zasobów |        |        |      |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |      |        | X             |               |       |

*: Współautor grupą zarządzania i Grupą czytnika tylko Zezwalaj użytkownikom na wykonywanie tych czynności w zakresie grupy zarządzania.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Definicja roli RBAC niestandardowych i przypisanie

Niestandardowe role RBAC nie są obsługiwane dla grup zarządzania w tej chwili.  Zobacz [forum z opiniami grupy zarządzania](https://aka.ms/mgfeedback) Aby wyświetlić stan tego elementu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](management-groups-create.md)
- [Jak zmienić, usunąć lub zarządzać grupami zarządzania](management-groups-manage.md)
- [Instalowanie modułu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Przegląd specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
