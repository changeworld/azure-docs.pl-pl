---
title: Używanie grup do zarządzania dostępem do zasobów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak używać grup w usłudze Azure Active Directory do zarządzania dostępem użytkowników do lokalnych i aplikacji w chmurze i zasobów.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 6163f8d2466637933024760c6d748c6007803b1d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712950"
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory
Azure Active Directory (Azure AD) to kompleksowe tożsamościami i dostępem zarządzania rozwiązanie, które oferuje niezawodny zestaw funkcji do zarządzania dostępem do lokalnego i aplikacje w chmurze i zasobów w tym usługi online firmy Microsoft, takich jak usługi Office 365 i World aplikacji SaaS innych niż Microsoft. Ten artykuł zawiera omówienie, ale jeśli chcesz rozpocząć używanie programu Azure AD grup od razu, postępuj zgodnie z instrukcjami [Zarządzanie grupami zabezpieczeń w usłudze Azure AD](active-directory-groups-create-azure-portal.md). Jeśli chcesz zobaczyć, jak można użyć programu PowerShell do zarządzania grupami w usłudze Azure Active directory można znaleźć więcej informacji, zobacz [polecenia cmdlet usługi Azure Active Directory dla grupy zarządzania](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Aby korzystać z usługi Azure Active Directory, potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
>
>

W usłudze Azure AD jednym z najważniejszych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, tak jak w przypadku uprawnień do zarządzania obiektami za pomocą ról w katalogu lub zasobów, które są zewnętrzne względem katalogu, na przykład aplikacji SaaS, usług platformy Azure i witryn programu SharePoint lub zasobów lokalnych. Istnieją cztery sposoby użytkownika można przypisać prawa dostępu do zasobu:

1. Przypisania bezpośredniego

    Użytkownicy można przypisać bezpośrednio do zasobu przez właściciela tego zasobu.
2. Członkostwo w grupie

    Grupy można przypisać do zasobu przez właściciela zasobu i w ten sposób, udzielanie członkami tej grupy dostęp do zasobu. Członkostwo w grupie następnie mogą być zarządzane przez właściciela grupy. W rezultacie właściciel zasobu przekazuje uprawnienie do przypisywania użytkowników do ich zasobów do właściciela grupy.
3. Na podstawie reguł

    Właściciel zasobu za pomocą reguły wyrazić, użytkowników, którzy powinien zostać przypisany dostęp do zasobu. Wyniki reguły zależy od atrybuty używane w tej regule i ich wartości dla określonych użytkowników, i w ten sposób właściciel zasobu przekazuje efektywne zarządzanie dostępem do ich zasobów w źródle autorytatywnych dla atrybutów, które są używane w prawo reguła. Właściciel zasobu nadal zarządza się reguły i określa atrybuty i wartości, które zapewniają dostęp do swojego zasobu.
4. Zewnętrznego urzędu

    Dostęp do zasobu pochodzi ze źródła zewnętrznego; na przykład grupa, która jest zsynchronizowany z wiarygodnego źródła, takich jak katalog lokalny lub aplikacji SaaS, takich jak produktu WorkDay. Właściciel zasobu przypisuje grupy w celu zapewnienia dostępu do zasobu, a źródła zewnętrznego zarządza członków grupy.

   ![Omówienie dostępu administracyjnego diagramu](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Obejrzyj klip wideo, który objaśnia zarządzanie dostępem
Można obejrzeć krótki film, który objaśnia, więcej informacji na ten temat:

**Azure AD: Wprowadzenie do członkostwo dynamiczne w grupach**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Jak uzyskać dostępu do zarządzania w pracy z usługą Azure Active Directory?
W Centrum usługi Azure AD rozwiązania do zarządzania dostępu jest grupą zabezpieczeń. Zarządzanie dostępem do zasobów przy użyciu grupy zabezpieczeń jest dobrze znanego modelu, który pozwala na elastyczne i zrozumiałymi sposobem zapewnienia dostępu do zasobu grupie użytkowników. Właściciel zasobu (lub administratora katalogu) można przypisać grupę w celu zapewnienia niektóre uprawnienia dostępu do zasobów, w których są właścicielami. Członkowie grupy zapewnia dostęp i właściciel zasobu można delegować prawa do zarządzania listy członków grupy do innej osoby, takie jak Menedżer działu lub administratora pomocy technicznej.

![Azure diagram zarządzania dostęp do usługi Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Właściciel grupy może także udostępnić tej grupy dla żądań samoobsługi. W ten sposób użytkownika końcowego można wyszukiwać i znaleźć grupy i wykona żądanie dołączenia, efektywnie znalezienia uprawnień do uzyskania dostępu do zasobów, które są zarządzane przez grupę. Właściciel grupy można skonfigurować grupy, dzięki czemu żądań dołączenia są automatycznie zatwierdzane lub wymagają zatwierdzenia przez właściciela grupy. Gdy użytkownik wysyła żądanie do dołączenia do grupy, jego żądanie dołączenia jest przekazywana do właścicieli grupy. Jeśli jeden właścicieli zatwierdza żądanie, żądania użytkownik jest powiadamiany i użytkownik jest przyłączony do grupy. Jeśli jeden właścicieli nie zezwala na żądanie, użytkownik zgłaszający żądanie jest powiadomienie, ale nie jest przyłączona do grupy.

## <a name="getting-started-with-access-management"></a>Wprowadzenie do zarządzania dostępem
Chcesz zacząć? Należy spróbować niektórych podstawowych zadań, które można wykonać za pomocą grup usługi Azure AD. Użyj tych funkcji, aby zapewnić specjalne dostępu różne grupy użytkowników dla różnych zasobów w organizacji. Poniżej przedstawiono listę podstawowych pierwsze kroki.

* [Tworzenie prostego zasadę, aby skonfigurować dynamiczne zarządzanie członkostwem w grupie](active-directory-groups-create-azure-portal.md)
* [Zarządzanie dostępem do aplikacji SaaS przy użyciu grupy](active-directory-accessmanagement-group-saasapps.md)
* [Udostępnianie grupy dla użytkownika samoobsługi](active-directory-accessmanagement-self-service-group-management.md)
* [Trwa synchronizowanie grupy lokalnej na platformie Azure za pomocą usługi Azure AD Connect](active-directory-aadconnect.md)
* [Zarządzanie właścicielami grupy](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Kolejne kroki
Teraz, możesz zrozumienie podstaw zarządzania dostępem poniżej przedstawiono niektóre dodatkowe zaawansowanych możliwości dostępne w usłudze Azure Active Directory dotyczące zarządzania dostępem do aplikacji i zasobów.

* [Tworzenie zaawansowanych reguł przy użyciu atrybutów](active-directory-groups-dynamic-membership-azure-portal.md)
* [Zarządzanie grupami zabezpieczeń w usłudze Azure AD](active-directory-groups-create-azure-portal.md)
* [Konfigurowanie grupy dedykowane w usłudze Azure AD](active-directory-accessmanagement-dedicated-groups.md)
* [Interfejs API Graph odwołania dla grup](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](active-directory-accessmanagement-groups-settings-cmdlets.md)
