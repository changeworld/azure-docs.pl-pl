---
title: 'Szybki start: dodawanie zasad nadawania nazw dla grup usługi Office 365 — Azure Active Directory | Microsoft Docs'
description: Wyjaśniono, jak dodać nowych użytkowników lub usunąć istniejących użytkowników w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e600204479bc54a590df6bf1bbcd634eaac7fc
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605644"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Szybki start: Zasady nazewnictwa grup w usłudze Azure Active Directory

W tym przewodniku Szybki start utworzysz zasady nazewnictwa w dzierżawie usługi Azure Active Directory (Azure AD) dla grup usługi Office 365 tworzonych przez użytkowników, aby umożliwić sortowanie i wyszukiwanie grup w ramach dzierżawy. Zasady nazewnictwa umożliwiają na przykład:

* Przekazywanie informacji na temat funkcji grupy, członkostwa, regionu geograficznego lub twórcy grupy.
* Łatwiejsze kategoryzowanie grup w książce adresowej.
* Blokowanie konkretnych słów, aby uniemożliwić ich używanie w nazwach grup i aliasach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>Konfigurowanie grupy, zasady nazewnictwa dla dzierżawcy przy użyciu witryny Azure portal (wersja zapoznawcza)

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkowników.
1. Wybierz **grup**, a następnie wybierz **zasady nazewnictwa** aby otworzyć stronę Zasady nazewnictwa.

    ![Otwórz stronę Zasady nazewnictwa w Centrum administracyjnym](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetl lub Edytuj zasady nazewnictwa prefiksu i sufiksu

1. Na **zasady nazewnictwa** wybierz opcję **zasady nazewnictwa grupy**.
1. Można wyświetlić lub edytować bieżący prefiks lub sufiks nadawanie zasadom nazw indywidualnie, wybierając atrybutów lub ciągów, które mają zostać wymuszone jako część zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, wybierz prefiks lub sufiks, a następnie wybierz **Usuń**. W tym samym czasie można usunąć wielu elementów.
1. Wybierz **Zapisz** zmiany zasady zaczną obowiązywać.

### <a name="view-or-edit-the-custom-blocked-words"></a>Wyświetl lub Edytuj niestandardowe wyrazy zablokowane

1. Na **zasady nazewnictwa** wybierz opcję **zablokowane wyrazy**.

    ![edytować i przekazywać listy zablokowanych słowa dla zasady nazewnictwa](./media/groups-naming-policy/blockedwords-preview.png)

1. Wyświetlenie lub Edycja bieżącą listę zablokowanych podasz niestandardowe wyrazy, wybierając **Pobierz**.
1. Przekaż nową listę zablokowanych podasz niestandardowe wyrazy, wybierając ikonę pliku.
1. Wybierz **Zapisz** zmiany zasady zaczną obowiązywać.

Gotowe. Skonfigurowano zasady nazewnictwa i dodano niestandardowe słowa zablokowane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Usuń zasady nazewnictwa przy użyciu witryny Azure portal (wersja zapoznawcza)

1. Na **zasady nazewnictwa** wybierz opcję **usuwanie zasady**.
1. Po użytkownik potwierdzi usunięcie zasad nazewnictwa zostanie usunięty, w tym wszystkie sufiks prefiks nazwy zasad i wszystkie zablokowane podasz niestandardowe wyrazy.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wyjaśniono sposób ustawiania zasad nazewnictwa dla Twojej organizacji usługi Azure AD w witrynie Azure portal.

Przejdź do następnego artykułu, aby uzyskać więcej informacji, łącznie z poleceń cmdlet programu PowerShell dla nazwy zasady, ograniczenia techniczne, Dodawanie listy zablokowanych podasz niestandardowe wyrazy i środowiska użytkownika końcowego w aplikacjach usługi Office 365.
> [!div class="nextstepaction"]
> [Nazwy zasad programu PowerShell](groups-naming-policy.md)