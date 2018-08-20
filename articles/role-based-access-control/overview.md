---
title: Co to jest kontrola dostępu oparta na rolach na platformie Azure? | Microsoft Docs
description: Omówienie kontroli dostępu opartej na rolach na platformie Azure. Sterowanie dostępem do zasobów platformy Azure za pomocą przypisań ról.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d0d140a1656719b406567fee431d8e48a51852c5
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714455"
---
# <a name="what-is-role-based-access-control-rbac"></a>Co to jest kontrola dostępu oparta na rolach?

Zarządzanie dostępem do zasobów w chmurze to kluczowa funkcja dla każdej organizacji korzystającej z chmury. Kontrola dostępu oparta na rolach ułatwia zarządzanie osobami mającymi dostęp do zasobów platformy Azure, czynnościami, jakie mogą wykonywać, oraz obszarami, do których mają dostęp.

Kontrola dostępu oparta na rolach stanowi system autoryzacji oparty na usłudze [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), umożliwiający szczegółowe zarządzanie dostępem do zasobów platformy Azure.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Poniżej przedstawiono kilka przykładów zastosowania kontroli dostępu opartej na rolach:

- Zezwalanie jednemu użytkownikowi na zarządzanie maszynami wirtualnymi w ramach subskrypcji, a innemu na zarządzanie sieciami wirtualnymi.
- Zezwalanie grupie administratorów baz danych na zarządzanie bazami danych SQL w ramach subskrypcji.
- Zezwalanie użytkownikowi na zarządzanie wszystkimi zasobami w grupie zasobów, w tym maszynami wirtualnymi, witrynami internetowymi i podsieciami.
- Zezwalanie aplikacji na dostęp do wszystkich zasobów w grupie zasobów.

## <a name="best-practice-for-using-rbac"></a>Najlepsze rozwiązania w przypadku używania funkcji RBAC

Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Na poniższym diagramie przedstawiono sugerowany wzorzec korzystania z funkcji RBAC.

![RBAC i najniższe uprawnienie](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Jak działa kontrola dostępu oparta na rolach

Jeśli korzystasz z kontroli dostępu opartej na rolach, sterowanie dostępem polega na tworzeniu przypisań ról. To kluczowa koncepcja, opisująca sposób egzekwowania uprawnień. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu.

### <a name="security-principal"></a>Podmiot zabezpieczeń

*Podmiot zabezpieczeń* to obiekt reprezentujący użytkownika, grupę lub jednostkę usługi, które żądają dostępu do zasobów na platformie Azure.

![Podmiot zabezpieczeń w przypisaniu roli](./media/overview/rbac-security-principal.png)

- Użytkownik — osoba mająca profil w usłudze Azure Active Directory. Można również przypisywać role do użytkowników w innych dzierżawach. Aby uzyskać informacje na temat użytkowników w innych organizacjach, zobacz [Azure Active Directory B2B (Współpraca B2B w usłudze Azure Active Directory)](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Grupa — zbiór użytkowników, utworzony w usłudze Azure Active Directory. W przypadku przypisania roli do grupy wszyscy użytkownicy w grupie otrzymają tę rolę. 
- Jednostka usługi — tożsamość zabezpieczeń używana przez aplikacje lub usługi w celu uzyskania dostępu do określonych zasobów platformy Azure. Można traktować ją jako odpowiednik *tożsamości użytkownika* (nazwy użytkownika i hasła lub certyfikatu) w przypadku aplikacji.

### <a name="role-definition"></a>Definicja roli

*Definicja roli* to zbiór uprawnień. Czasami jest nazywana po prostu *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Role mogą być ogólne, na przykład „właściciel”, lub szczegółowe, na przykład „czytelnik maszyny wirtualnej”.

![Definicja roli w przypisaniu roli](./media/overview/rbac-role-definition.png)

Na platformie Azure można korzystać z kilku [ról wbudowanych](built-in-roles.md). Poniżej wymieniono cztery podstawowe role wbudowane. Pierwsze trzy są stosowane do wszystkich typów zasobów.

- [Właściciel](built-in-roles.md#owner) — ma pełny dostęp do wszystkich zasobów i jest uprawniony do przydzielania dostępu innym osobom.
- [Współautor](built-in-roles.md#contributor) — może tworzyć wszystkie typy zasobów platformy Azure i zarządzać nimi, ale nie może przydzielać dostępu innym osobom.
- [Czytelnik](built-in-roles.md#reader) — może wyświetlać istniejące zasoby platformy Azure.
- [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) — może zarządzać dostępem użytkowników do zasobów platformy Azure.

Pozostałe role wbudowane umożliwiają zarządzanie określonymi zasobami platformy Azure. Na przykład rola [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) umożliwia użytkownikowi tworzenie maszyn wirtualnych i zarządzanie nimi. Jeśli role wbudowane nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne [role niestandardowe](custom-roles.md).

Na platformie Azure wprowadzono operacje na danych (obecnie dostępne w wersji zapoznawczej), umożliwiające przydzielanie dostępu do danych w obiekcie. Jeśli na przykład użytkownik ma uprawnienie do odczytu na koncie magazynu, może odczytywać obiekty blob lub komunikaty na tym koncie magazynu. Aby uzyskać więcej informacji, zobacz [Understand role definitions (Omówienie definicji ról)](role-definitions.md).

### <a name="scope"></a>Zakres

*Zakres* określa granice, w ramach których jest przydzielany dostęp. Podczas przypisywania roli możesz dodatkowo ograniczyć dozwolone czynności, określając zakres. Jest to przydatne na przykład wówczas, gdy chcesz przypisać użytkownikowi rolę [współautora witryny internetowej](built-in-roles.md#website-contributor), ale tylko w jednej grupie zasobów.

Na platformie Azure można określić zakres na różnych poziomach: [grupy zarządzania](../azure-resource-manager/management-groups-overview.md), subskrypcji, grupy zasobów lub zasobu. Zakresy mają strukturę relacji element nadrzędny-element podrzędny.

![Zakres w przypisaniu roli](./media/overview/rbac-scope.png)

Gdy udzielisz dostępu w zakresie nadrzędnym, te uprawnienia są dziedziczone przez zakresy podrzędne. Na przykład:

- Jeśli przypiszesz użytkownikowi rolę [Właściciel](built-in-roles.md#owner) w zakresie grupy zarządzania, ten użytkownik będzie mógł zarządzać wszystkimi elementami we wszystkich subskrypcjach w grupie zarządzania.
- Jeśli przypiszesz rolę [czytelnika](built-in-roles.md#reader) do grupy na poziomie subskrypcji, członkowie tej grupy będą mogli wyświetlać wszystkie grupy zasobów i zasoby w ramach tej subskrypcji.
- Jeśli przypiszesz rolę [współautora](built-in-roles.md#contributor) do aplikacji na poziomie grupy zasobów, ta aplikacja będzie mogła zarządzać zasobami dowolnego typu w tej grupie zasobów, ale nie w innych grupach zasobów w ramach subskrypcji.

### <a name="role-assignment"></a>Przypisanie roli

*Przypisanie roli* to proces powiązania definicji roli z użytkownikiem, grupą lub jednostką usługi oraz wybranym zakresem w celu udzielenia dostępu. Udzielenie dostępu polega na utworzeniu przypisania roli, a odwołanie dostępu — na usunięciu przypisania roli.

Na poniższym diagramie przedstawiono przykład przypisania roli. W tym przykładzie do grupy Marketing przypisano rolę [współautora](built-in-roles.md#contributor) w grupie zasobów pharma-sales. To oznacza, że użytkownicy należący do grupy Marketing mogą tworzyć dowolne zasoby platformy Azure w grupie zasobów pharma-sales i zarządzać nimi. Użytkownicy należący do grupy Marketing nie mają dostępu do zasobów spoza grupy zasobów pharma-sales, o ile nie przypisano do nich innej roli.

![Przypisanie roli w celu kontroli dostępu](./media/overview/rbac-overview.png)

Przypisania ról można tworzyć, korzystając z witryny Azure Portal, interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell, zestawów Azure SDK lub interfejsów API REST. W ramach jednej subskrypcji można utworzyć maksymalnie 2000 przypisań ról. Aby móc tworzyć i usuwać przypisania ról, potrzebne są uprawnienia `Microsoft.Authorization/roleAssignments/*`. Te uprawnienia można przyznać za pomocą ról [Właściciel](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

## <a name="next-steps"></a>Następne kroki

- [Szybki start: udzielanie dostępu użytkownikowi za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](quickstart-assign-role-user-portal.md)
- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](role-assignments-portal.md)
- [Understand the different roles in Azure (Omówienie ról na platformie Azure)](rbac-and-directory-admin-roles.md)
