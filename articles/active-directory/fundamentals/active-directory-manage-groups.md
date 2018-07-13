---
title: Zarządzanie dostępem do zasobów w usłudze Azure AD za pomocą grup | Microsoft Docs
description: Jak używać grup w usłudze Azure Active Directory w celu zarządzania dostępem użytkowników do aplikacji i zasobów lokalnie i w chmurze.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: overview
ms.date: 08/28/2017
ms.author: lizross
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: cce3b4f15acbcc6ce78ea0623c6b9f3c23941cb0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859629"
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory
Usługa Azure Active Directory (Azure AD) to kompleksowe rozwiązanie do zarządzania tożsamościami i dostępem, które oferuje niezawodny zestaw funkcji służących do zarządzania dostępem do aplikacji i zasobów lokalnie oraz w chmurze, w tym do usług online firmy Microsoft, takich jak Office 365, a także działających w modelu SaaS aplikacji firm innych niż Microsoft. Ten artykuł zawiera omówienie tematu, ale jeśli chcesz rozpocząć korzystanie z grup usługi Azure AD już teraz, wykonaj czynności zawarte w artykule [Managing security groups in Azure AD (Zarządzanie grupami zabezpieczeń w usłudze Azure AD)](active-directory-groups-create-azure-portal.md). Jeśli chcesz się dowiedzieć, jak zarządzać grupami w usłudze Azure Active Directory za pomocą programu PowerShell, możesz przeczytać więcej na ten temat w artykule [Azure Active Directory cmdlets for group management (Polecenia cmdlet do zarządzania grupami w usłudze Azure Active Directory)](../users-groups-roles/groups-settings-v2-cmdlets.md).

> [!NOTE]
> Aby korzystać z usługi Azure Active Directory, potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
>
>

W usłudze Azure AD jedną z głównych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, jak w przypadku uprawnień do zarządzania obiektami za pośrednictwem ról w katalogu, lub znajdować się poza katalogiem, jak w przypadku aplikacji SaaS, usług platformy Azure, witryn programu SharePoint lub zasobów lokalnych. Istnieją cztery sposoby przypisania użytkownikowi praw dostępu do zasobu:

1. Przypisanie bezpośrednie

    Użytkownicy mogą zostać bezpośrednio przypisani do zasobu przez właściciela tego zasobu.
2. Członkostwo w grupie

    Grupa może zostać przypisana do zasobu przez właściciela zasobu. Wówczas dostęp do zasobu jest udzielany członkom tej grupy. Następnie właściciel grupy może zarządzać członkostwem w grupie. W rezultacie właściciel zasobu deleguje na właściciela grupy uprawnienie do przypisywania użytkowników do swojego zasobu.
3. Na podstawie reguł

    Właściciel zasobu może użyć reguły, aby określić, którym użytkownikom należy przypisać dostęp do zasobu. Efekt reguły zależy od atrybutów użytych w tej regule oraz ich wartości dla konkretnych użytkowników. W ten sposób właściciel zasobu deleguje prawo do zarządzania dostępem do swojego zasobu na autorytatywne źródło w zakresie atrybutów używanych w tej regule. Właściciel zasobu nadal zarządza samą regułą i określa, jakie atrybuty i wartości zapewniają dostęp do jego zasobu.
4. Źródło zewnętrzne

    Dostęp do zasobu pochodzi z zewnętrznego źródła, na przykład z grupy zsynchronizowanej ze źródłem autorytatywnym, takim jak katalog lokalny lub aplikacja SaaS, na przykład WorkDay. Właściciel zasobu przypisuje grupę, aby umożliwić dostęp do zasobu, a źródło zewnętrzne zarządza członkami grupy.

   ![Diagram przedstawiający przegląd zarządzania dostępem](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Obejrzyj wideo, w którym wyjaśniono zarządzanie dostępem
Możesz obejrzeć krótkie wideo zawierające więcej informacji na ten temat:

**Azure AD: Introduction to dynamic membership for groups (Azure AD: wprowadzenie do członkostwa dynamicznego dla grup)**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Jak działa zarządzanie dostępem w usłudze Azure Active Directory?
Kluczowym elementem rozwiązania do zarządzania dostępem w usłudze Azure AD jest grupa zabezpieczeń. Korzystanie z grupy zabezpieczeń do zarządzania dostępem do zasobów to dobrze znany paradygmat, który oferuje elastyczny i zrozumiały sposób zapewniania dostępu do zasobu wybranej grupie użytkowników. Właściciel zasobu (lub administrator katalogu) może przypisać grupę, aby udzielić określonych uprawnień dostępu do jego zasobów. Członkowie grupy uzyskają dostęp, a właściciel zasobu może delegować prawo do zarządzania listą członków grupy na kogoś innego, na przykład kierownika działu lub administratora działu technicznego.

![Diagram zarządzania dostępem w usłudze Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

Właściciel grupy może również włączyć żądania samoobsługowe dla grupy. Dzięki temu użytkownik końcowy może wyszukać i znaleźć grupę, a następnie zażądać dołączenia do niej, czyli w praktyce zwrócić się o przydzielenie dostępu do zasobów, które są zarządzane za pośrednictwem tej grupy. Właściciel grupy może skonfigurować grupę tak, aby żądania dołączenia były zatwierdzane automatycznie lub aby wymagały zatwierdzenia przez właściciela grupy. Gdy użytkownik zażąda dołączenia do grupy, żądanie dołączenia jest przekazywane do właścicieli grupy. Jeśli jeden z właścicieli zatwierdzi żądanie, użytkownik zostanie o tym powiadomiony i dołączy do grupy. Jeśli jeden z właścicieli odrzuci żądanie, użytkownik zostanie o tym powiadomiony, ale nie dołączy do grupy.

## <a name="getting-started-with-access-management"></a>Wprowadzenie do zarządzania dostępem
Chcesz zacząć? Wypróbuj kilka podstawowych zadań, które można wykonać w grupach usługi Azure AD. Za pomocą tych opcji możesz zapewnić różnym grupom użytkowników wyspecjalizowany dostęp do różnych zasobów w organizacji. Lista podstawowych kroków jest przedstawiona poniżej.

* [Creating a simple rule to configure dynamic memberships for a group (Tworzenie prostej reguły w celu skonfigurowania dynamicznego członkostwa w grupie)](active-directory-groups-create-azure-portal.md)
* [Using a group to manage access to SaaS applications (Używanie grupy do zarządzania dostępem do aplikacji SaaS)](../users-groups-roles/groups-saasapps.md)
* [Włączanie samoobsługi użytkowników końcowych w grupie](../users-groups-roles/groups-self-service-management.md)
* [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md)
* [Managing owners for a group (Zarządzanie właścicielami grupy)](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Następne kroki
Podstawowe kwestie związane z zarządzaniem dostępem są już jasne. Oto kilka dodatkowych zaawansowanych funkcji zarządzania dostępem do aplikacji i zasobów w usłudze Azure Active Directory.

* [Using attributes to create advanced rules (Używanie atrybutów do tworzenia reguł zaawansowanych)](../users-groups-roles/groups-dynamic-membership.md)
* [Managing security groups in Azure AD (Zarządzanie grupami zabezpieczeń w usłudze Azure AD)](active-directory-groups-create-azure-portal.md)
* [Graph API reference for groups (Dokumentacja interfejsu API programu Graph dla grup)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md)
