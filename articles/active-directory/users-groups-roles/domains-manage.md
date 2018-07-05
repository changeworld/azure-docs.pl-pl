---
title: Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie pojęcia i kwestie dotyczące zarządzania nazwę domeny w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: b503ef4c5cd922d4f7b58940cfc98a83a78ae986
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450082"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory
Nazwa domeny jest ważną częścią identyfikatora dla wielu zasobów katalogu: jest częścią użytkownika nazwy lub adresu e-mail użytkownika, część adresu w grupie i mogą być częścią aplikacji identyfikator URI dla aplikacji. Zasób w usłudze Azure Active Directory (Azure AD) mogą obejmować nazwę domeny, która została już zweryfikowana jako należące do katalogu, który zawiera zasób. Tylko administratorzy globalni mogą wykonywać zadania zarządzania domeny, w usłudze Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ustaw nazwę domeny głównej dla katalogu usługi Azure AD
Po utworzeniu katalogu początkowa nazwa domeny, takich jak "contoso.onmicrosoft.com", jest również nazwa domeny głównej. Domena podstawowa jest domyślna nazwa domeny dla nowego użytkownika, po utworzeniu nowego użytkownika. Ustawianie nazwy domeny głównej usprawnia proces dla administratora do tworzenia nowych użytkowników w portalu. Aby zmienić nazwę głównej domeny:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz pozycję **Azure Active Directory**.
3. Wybierz **niestandardowe nazwy domen**.
     
   ![Otwieranie okna zarządzania użytkownikami](./media/domains-manage/add-custom-domain.png)
4. Wybierz nazwę domeny, która ma być podstawową domeną.
5. Wybierz **Oznacz jako podstawową** polecenia. Potwierdź wybór po wyświetleniu monitu.
   
   ![Wprowadź nazwę domeny głównej](./media/domains-manage/make-primary-domain.png)

Można zmienić podstawowej nazwy domeny dla katalogu jako zweryfikowanej domenie niestandardowej, która nie obejmuje Federacja. Zmiana domeny podstawowej dla katalogu nie spowoduje zmiany nazw użytkowników dla wszystkich istniejących użytkowników.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Dodawanie niestandardowych nazw domen do dzierżawy usługi Azure AD
Możesz dodać do maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny dla federacji z usługą Active Directory w środowisku lokalnym, możesz dodać do maksymalnie 450 nazwy domeny w każdym katalogu. Aby uzyskać więcej informacji, zobacz [federacyjnych i zarządzanych nazwach domen](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Dodać poddomeny domeny niestandardowej
Jeśli chcesz dodać nazwy domen trzeciego poziomu, takie jak "europe.contoso.com" do katalogu, należy najpierw dodać i weryfikowania domeny drugiego poziomu, np. contoso.com. Domenę podrzędną zostanie automatycznie zweryfikowana przez usługę Azure AD. Aby wyświetlić zweryfikowano domenę podrzędną, który właśnie został dodany, Odśwież stronę w przeglądarce, która zawiera listę domen.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co należy zrobić, jeśli zmienisz rejestratora DNS dla swojej niestandardowej nazwy domeny
Jeśli zmienisz rejestratora DNS dla swojej niestandardowej nazwy domeny, można nadal używać niestandardowej nazwy domeny za pomocą samej usługi Azure AD z produktu bez przeszkód i bez dodatkowych zadań konfiguracji. Jeśli używasz niestandardowej nazwy domeny za pomocą usługi Office 365, Intune lub innych usług, które zależą od nazwy domeny niestandardowej w usłudze Azure AD, zapoznaj się z dokumentacją dla tych usług.

## <a name="delete-a-custom-domain-name"></a>Usuwanie nazwy domeny niestandardowej
Jeśli Twoja organizacja już używa tej nazwy domeny, czy należy użyć tej nazwy domeny za pomocą innej usługi Azure AD, możesz usunąć nazwy domeny niestandardowej z usługi Azure AD.

Aby usunąć niestandardową nazwę domeny, należy się najpierw upewnić, że żadne zasoby w Twoim katalogu zależeć od nazwy domeny. Nie można usunąć nazwy domeny z katalogu, jeśli:

* Każdy użytkownik ma nazwę użytkownika, adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda grupa ma adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda aplikacja w usłudze Azure AD ma identyfikator URI, który zawiera nazwę domeny aplikacji.

Należy zmienić ani usunąć tych zasobów w katalogu usługi Azure AD, aby można było usunąć nazwy domeny niestandardowej.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Użyj programu PowerShell lub interfejsu API programu Graph, zarządzanie nazwami domen
Większości zadań administracyjnych dla nazw domen w usłudze Azure Active Directory można również przeprowadzić za pomocą programu Microsoft PowerShell albo programowo z użyciem interfejsu API usługi Azure AD Graph.

* [Zarządzanie nazwami domen w usłudze Azure AD przy użyciu programu PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Zarządzanie nazwami domen w usłudze Azure AD przy użyciu interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie niestandardowych nazw domen](../fundamentals/add-custom-domain.md)

