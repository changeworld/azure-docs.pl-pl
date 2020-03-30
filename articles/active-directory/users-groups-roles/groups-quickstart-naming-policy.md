---
title: Szybki start zasad nazewnictwa grup — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Wyjaśniono, jak dodać nowych użytkowników lub usunąć istniejących użytkowników w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026930"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Szybki start: zasady nazewnictwa grup w usłudze Azure Active Directory

W tym przewodniku Szybki start utworzysz zasady nazewnictwa w dzierżawie usługi Azure Active Directory (Azure AD) dla grup usługi Office 365 tworzonych przez użytkowników, aby umożliwić sortowanie i wyszukiwanie grup w ramach dzierżawy. Zasady nazewnictwa umożliwiają na przykład:

* Przekazywanie informacji na temat funkcji grupy, członkostwa, regionu geograficznego lub twórcy grupy.
* Łatwiejsze kategoryzowanie grup w książce adresowej.
* Blokowanie konkretnych słów, aby uniemożliwić ich używanie w nazwach grup i aliasach.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurowanie zasad nazewnictwa grup dla dzierżawy przy użyciu witryny Azure Portal

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora użytkownika.
1. Wybierz **pozycję Grupy**, a następnie wybierz pozycję Zasady **nazewnictwa,** aby otworzyć stronę zasad nazewnictwa.

    ![otwieranie strony zasad nazewnictwa w centrum administracyjnym](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetlanie lub edytowanie zasad nazewnictwa sufiksu prefiksu

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Zasada nazewnictwa grupy**.
1. Bieżący prefiks lub zasady nazewnictwa sufiksów można wyświetlać lub edytować indywidualnie, wybierając atrybuty lub ciągi, które mają być wymuszane w ramach zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, zaznacz prefiks lub sufiks, a następnie wybierz pozycję **Usuń**. Wiele elementów można usunąć w tym samym czasie.
1. Wybierz **pozycję Zapisz,** aby zmiany w zasadach weszły w życie.

### <a name="view-or-edit-the-custom-blocked-words"></a>Wyświetlanie lub edytowanie niestandardowych zablokowanych wyrazów

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Zablokowane wyrazy**.

    ![edytowanie i przesyłanie listy zablokowanych słów w celu nazewnictwa zasad](./media/groups-naming-policy/blockedwords.png)

1. Wyświetl lub edytuj bieżącą listę niestandardowych zablokowanych słów, wybierając pozycję **Pobierz**.
1. Prześlij nową listę niestandardowych zablokowanych słów, wybierając ikonę pliku.
1. Wybierz **pozycję Zapisz,** aby zmiany w zasadach weszły w życie.

Gotowe. Skonfigurowano zasady nazewnictwa i dodano niestandardowe słowa zablokowane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="remove-the-naming-policy-using-azure-portal"></a>Usuwanie zasad nazewnictwa przy użyciu witryny Azure Portal

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Usuń zasady**.
1. Po potwierdzeniu usunięcia zasady nazewnictwa zostaną usunięte, w tym wszystkie zasady nazewnictwa prefiksów i wszelkie niestandardowe zablokowane słowa.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak ustawić zasady nazewnictwa dla organizacji usługi Azure AD za pośrednictwem witryny Azure portal.

Przejdź do następnego artykułu, aby uzyskać więcej informacji, w tym polecenia cmdlet programu PowerShell dotyczące zasad nazewnictwa, ograniczenia techniczne, dodawanie listy niestandardowych zablokowanych słów i środowiska użytkownika końcowego w aplikacjach usługi Office 365.
> [!div class="nextstepaction"]
> [Zasady nazewnictwa programu PowerShell](groups-naming-policy.md)