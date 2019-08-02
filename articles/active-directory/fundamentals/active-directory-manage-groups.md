---
title: Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak zarządzać dostępem do aplikacji w chmurze, lokalnych aplikacji i zasobów przy użyciu grup usługi Azure Active Directory w organizacji.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbc19c2148a2b62ff1aa28d705591430b4fad20
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561823"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup usługi Azure Active Directory
Azure Active Directory (Azure AD) pomaga w zarządzaniu aplikacji opartych na chmurze, lokalnych aplikacji i zasoby przy użyciu grup w organizacji. Twoje zasoby mogą być częścią katalogu, takie jak uprawnienia do zarządzania obiektami za pomocą ról w katalogu lub zewnętrznie w stosunku do katalogu, takie jak do oprogramowania jako usługi (SaaS), Azure services witryn programu SharePoint i zasobów lokalnych.

>[!NOTE]
>Aby korzystać z usługi Azure Active Directory, potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="how-does-access-management-in-azure-ad-work"></a>Jak uzyskać dostęp do zarządzania w pracy w usłudze Azure AD?
Azure AD pomaga w zapewnieniu dostępu do zasobów organizacji, zapewniając praw dostępu do jednego użytkownika lub dla całej usługi Azure AD grupy. Przy użyciu grup umożliwia zasobu właściciela (lub właściciela katalogu usługi Azure AD), przypisać zestaw uprawnień dostępu do wszystkich członków tej grupy, nie trzeba podać praw pojedynczo. Właściciel zasobu lub katalogu można również przyznać uprawnienia do zarządzania dla listy elementów członkowskich do kogoś innego, takiego jak Menedżer działu lub administrator pomocy technicznej, umożliwiając mu Dodawanie i usuwanie elementów członkowskich, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat zarządzenie właścicielami grup, zobacz [zarządzenie właścicielami grup](active-directory-accessmanagement-managing-group-owners.md)

![Diagram zarządzania dostępem w usłudze Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Sposoby przypisywania praw dostępu
Istnieją cztery sposoby przypisywania zasobów prawa dostępu dla użytkowników:

- **Bezpośrednie przypisania.** Właściciel zasobu przypisuje bezpośrednio do użytkownika do zasobu.

- **Przypisania grupy.** Właściciel zasobu przypisuje grupę usługi Azure AD do zasobu, który automatycznie zapewnia wszystkie dostępu członkom grupy zasobów. Członkostwo w grupie jest zarządzane zarówno przez właściciela grupy i właściciela zasobu, umożliwiając właściciel, albo dodać lub usunąć członków z grupy. Aby uzyskać więcej informacji na temat dodawania lub usuwania członkostwa w [grupach, zobacz How to: Dodaj lub Usuń grupę z innej grupy przy użyciu portalu](active-directory-groups-membership-azure-portal.md)Azure Active Directory. 

- **Przypisanie oparte na regułach.** Właściciel zasobu tworzy grupę i używa regułę w celu zdefiniowania, czyli użytkownicy przypisani do określonego zasobu. Reguła opiera się na atrybuty, które są przypisane do poszczególnych użytkowników. Właściciel zasobu zarządza reguły, określania atrybutów i wartości, które są wymagane, aby zezwolić na dostęp do zasobu. Aby uzyskać więcej informacji, zobacz [utworzyć grupę dynamiczną i sprawdzić stan](../users-groups-roles/groups-create-rule.md).

    Możesz też obejrzeć ten krótki film wideo, szybkie informacje na temat tworzenia i używania grup dynamicznych:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Przypisanie zewnętrznego urzędu.** Dostępu pochodzi z zewnętrznego źródła, takich jak katalogu lokalnego lub aplikacji SaaS. W takiej sytuacji właściciel zasobu przypisuje grupę do przydzielenia dostępu do zasobu, i następnie źródła zewnętrznego zarządza członków grupy.

   ![Diagram przedstawiający przegląd zarządzania dostępem](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Użytkownicy dołączyć do grup bez przypisania?
Właściciel grupy, można pozwolić użytkownikom znajdowanie własne grupy, aby dołączyć, zamiast przypisywać je. Właściciel również skonfigurować grupy Automatyczne akceptowanie wszystkich użytkowników, którzy join lub wymagają zatwierdzenia.

Po użytkownik zażąda dołączenia do grupy, żądanie jest przekazywane do właściciela grupy. Jeśli jest to wymagane, właściciel może zatwierdzić żądanie, a użytkownik jest powiadamiany o członkostwo w grupie. Jednak jeśli masz wielu właścicielom, a jeden z nich nie uwzględni, użytkownik zostanie powiadomiony, ale nie została dodana do grupy. Aby uzyskać więcej informacji oraz instrukcje na temat umożliwić użytkownikom żądanie dołączenia do grup, zobacz [Konfigurowanie usługi Azure AD, dzięki czemu użytkownicy mogą żądać dołączenia do grup](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz znacznej liczby wprowadzenie do zarządzania dostępem przy użyciu grup, możesz rozpocząć zarządzanie zasobami i aplikacji.

- [Utwórz nową grupę za pomocą usługi Azure Active Directory](active-directory-groups-create-azure-portal.md) lub [tworzenie i zarządzanie nimi nową grupę za pomocą poleceń cmdlet programu PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Używanie grup do udzielania dostępu do zintegrowanych aplikacji SaaS](../users-groups-roles/groups-saasapps.md)

- [Synchronizuj grupy lokalnej na platformę Azure za pomocą usługi Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
