---
title: Wprowadzenie do aplikacji Azure AD Privileged Identity Management | Microsoft Docs
description: Informacje o sposobie zarządzania uprzywilejowanymi tożsamościami przy użyciu aplikacji Azure Active Directory Privileged Identity Management w witrynie Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: protection
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1c6464dbc471b17baec702efa1f518baeeef3de6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590359"
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Rozpoczynanie używania aplikacji Azure AD Privileged Identity Management

Aplikacja Azure Active Directory (AD) Privileged Identity Management umożliwia kontrolę i monitorowanie dostępu, a także zarządzanie nim w ramach danej organizacji. Ten zakres obejmuje dostęp do zasobów platformy Azure, usługi Azure AD i innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

W tym artykule wyjaśniono, jak dodać aplikację Azure AD PIM do pulpitu nawigacyjnego witryny Azure Portal.

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management

Przed użyciem aplikacji Azure AD Privileged Identity Management należy dodać ją do pulpitu nawigacyjnego witryny Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny katalogu.
2. Jeśli organizacja dysponuje więcej niż jednym katalogiem, wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure Portal. Wybierz katalog, w którym chcesz używać aplikacji PIM.
3. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.
4. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

Jeśli jesteś pierwszą osobą używającą usługi Azure AD Privileged Identity Management w katalogu, będziesz mieć automatycznie przypisane role **Administrator zabezpieczeń** i **Administrator ról uprzywilejowanych** w katalogu. Tylko administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami ról użytkowników w katalogu usługi Azure AD. Ponadto można zdecydować się na uruchomienie [kreatora zabezpieczeń](pim-security-wizard.md), który przeprowadzi Cię przez środowisko początkowego odnajdywania i przypisywania.

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu usługi Azure AD Privileged Identity Management zobaczysz blok nawigacji pojawiający się przy każdym otwarciu aplikacji. Użyj tego bloku w celu wykonywania zadań związanych z zarządzaniem tożsamościami.

![Zrzut ekranu przedstawiający zadania najwyższego poziomu dla aplikacji PIM](./media/pim-getting-started/PIM_Tasks_New.png)

- **Moje role** — wyświetla listę kwalifikujących się i aktywnych ról, które Ci przypisano. Możesz tu aktywować dowolne przypisane, kwalifikujące się role.
- **Zatwierdzanie żądań (wersja zapoznawcza)** — wyświetla listę przeznaczonych do zatwierdzenia żądań użytkowników dotyczących aktywowania kwalifikujących się ról katalogu usługi Azure AD. [Dowiedz się więcej.](./azure-ad-pim-approval-workflow.md)
- **Oczekujące żądania (wersja zapoznawcza)** — wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról.
- **Sprawdź dostęp** — zawiera listę aktywnych przeglądów dostępu, które Ci przypisano, zarówno tych obejmujących własny dostęp, jak i tych obejmujących dostęp innych osób.
- **Role w katalogach usługi Azure AD** — dostępna w obszarze zarządzania lewego menu nawigacji zawiera pulpit nawigacyjny dla administratorów ról uprzywilejowanych, który umożliwia m.in. zarządzanie przypisaniami ról, zmienianie ustawień aktywacji ról i rozpoczynanie przeglądów dostępu. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie.
- **Role zasobów platformy Azure (wersja zapoznawcza)** — dostępna w obszarze zarządzania lewego menu nawigacji zawiera listę zasobów subskrypcji wybranych przez przypisania ról. 

## <a name="next-steps"></a>Kolejne kroki
Temat [Azure AD Privileged Identity Management overview](pim-configure.md) (Omówienie aplikacji Azure AD Privileged Identity Management) zawiera więcej informacji dotyczących zarządzania dostępem administracyjnym w organizacji.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
