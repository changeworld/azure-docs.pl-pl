---
title: 'Azure Active Directory Domain Services: Przewodnik administrowania | Dokumentacja firmy Microsoft'
description: Tworzenie jednostki organizacyjnej (OU) w domenach usług domenowych Azure AD zarządzane
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: e7ff91a87377deed06fa4214cc1e5764e8b2da92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246272"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Tworzenie jednostki organizacyjnej (OU) w domenie zarządzanej usług domenowych Azure AD
Usługa Azure AD Domain Services domen zarządzanych obejmują dwóch kontenerów wbudowanych odpowiednio o nazwie "Komputery usługi AAD DC" i "Użytkownicy usługi AAD DC". Kontener "Komputery usługi AAD DC" obejmuje obiekty komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej. Kontener "Użytkownicy usługi AAD DC" obejmuje użytkowników i grup w dzierżawie usługi Azure AD. Od czasu do czasu może być konieczne utworzenie konta usługi w domenie zarządzanej wdrożyć obciążenia. W tym celu można utworzyć niestandardowe jednostki organizacyjnej (OU) w domenie zarządzanej i utworzyć konta usługi w tej jednostce Organizacyjnej. W tym artykule pokazano, jak utworzyć jednostkę Organizacyjną w domenie zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. Maszynę wirtualną przyłączonych do domeny, z którego administrujesz domeny usług domenowych Azure AD zarządzane. Jeśli nie masz maszynę wirtualną, wykonaj wszystkie zadania, które są opisane w artykule o nazwie [Dołącz maszynę wirtualną Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Musisz mieć poświadczenia z **konta użytkownika należącego do grupy "Administratorzy usługi AAD DC"** w katalogu, aby utworzyć niestandardowe jednostki Organizacyjnej w domenie zarządzanej.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Zainstalować narzędzia administracji usługi AD na maszynie wirtualnej z przyłączonym do domeny dla administracji zdalnej
Usługa Azure AD Domain Services domeny zarządzane mogą być zarządzane zdalnie przy użyciu znanych narzędzi administracyjnych usługi Active Directory, takie jak Active Directory administracyjne Center (ADAC) lub programu PowerShell usługi AD. Administratorzy dzierżawy nie mają uprawnień do połączenia z kontrolerami domeny w domenie zarządzanej za pośrednictwem pulpitu zdalnego. Administrowanie domeną zarządzaną, należy zainstalować funkcję narzędzi administracji usługi AD na maszynie wirtualnej, przyłączone do domeny zarządzanej. Zapoznaj się z artykułem pod tytułem [Zarządzanie domeny usług domenowych Azure AD](manage-domain.md) instrukcje.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Utwórz jednostkę organizacyjną w domenie zarządzanej
Teraz, gdy narzędzia administracyjne AD są zainstalowane na domenie przyłączone do maszyny wirtualnej, możemy użyć tych narzędzi, aby utworzyć jednostki organizacyjnej w domenie zarządzanej. Wykonaj poniższe czynności:

> [!NOTE]
> Tylko członkowie grupy "Administratorzy usługi AAD DC" mają wymagane uprawnienia do tworzenia niestandardowej jednostki Organizacyjnej. Upewnij się, wykonaj następujące kroki jako użytkownik należący do tej grupy.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinieneś zobaczyć AD narzędzia administracyjne zainstalowane na maszynie wirtualnej.

    ![Narzędzia administracyjne zainstalowane na serwerze](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kliknij przycisk **Centrum administracyjne usługi Active Directory**.

    ![Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Aby wyświetlić domeny, kliknij nazwę domeny w okienku po lewej stronie (na przykład "contoso100.com").

    ![Usługach ADAC — widok domeny](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Po prawej stronie **zadania** okienku kliknij **New** w węźle nazwy domeny. W tym przykładzie firma Microsoft kliknij **New** po prawej stronie, w węźle "contoso100(local)" **zadania** okienka.

    ![Usługach ADAC — nowej jednostki Organizacyjnej](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Powinien zostać wyświetlony opcję, aby utworzyć jednostkę organizacyjną. Kliknij przycisk **jednostki organizacyjnej** można uruchomić **Tworzenie jednostki organizacyjnej** okna dialogowego.
6. W **Tworzenie jednostki organizacyjnej** okno dialogowe, określ **nazwa** nowej jednostki organizacyjnej. Podaj krótki opis jednostki organizacyjnej. Możesz też ustawić opcję **zarządzany przez** pole jednostki Organizacyjnej. Aby utworzyć niestandardowe jednostki Organizacyjnej, kliknij przycisk **OK**.

    ![Usługach ADAC - Tworzenie jednostki Organizacyjnej, okno dialogowe](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Nowo utworzonej jednostki Organizacyjnej, która teraz powinna zostać wyświetlona w AD administracyjne Center (usługach ADAC).

    ![Utworzone jednostki Organizacyjnej w usługach ADAC-](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Uprawnienia/zabezpieczeń dla nowo utworzonej jednostki organizacyjne
Domyślnie użytkownik (członek grupy "Administratorzy usługi AAD DC"), który utworzył niestandardowe jednostki Organizacyjnej jest przyznawać uprawnień administracyjnych (Pełna kontrola), za pośrednictwem jednostki Organizacyjnej. Użytkownik może następnie przejdź dalej i przyznawanie uprawnień do innych użytkowników lub grupy "Administratorzy usługi AAD DC" zgodnie z potrzebami. Jak widać na poniższym zrzucie ekranu użytkownika "bob@domainservicespreview.onmicrosoft.com" twórcę nowej jednostki organizacyjnej "MyCustomOU" jest uprawnienie do pełnej kontroli nad nim.

 ![Usługach ADAC - nowych zabezpieczeń jednostki Organizacyjnej](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Uwagi dotyczące administrowania niestandardowe jednostki organizacyjne
Teraz, po utworzeniu niestandardowej jednostki Organizacyjnej, można teraz i tworzenie użytkowników, grup, komputerów i kont usług w tej jednostce Organizacyjnej. Użytkownicy lub grupy z "Użytkownicy usługi AAD DC" jednostki Organizacyjnej nie można przenieść do niestandardowych jednostkach organizacyjnych.

> [!WARNING]
> Konta użytkowników, grup, konta usług i obiektów komputerów, tworzone w ramach niestandardowej jednostki organizacyjne nie są dostępne w Twojej dzierżawie usługi Azure AD. Innymi słowy te obiekty nie są wyświetlane przy użyciu interfejsu API programu Graph usługi Azure AD lub w Interfejsie użytkownika usługi Azure AD. Te obiekty są dostępne tylko w domenie zarządzanej usług domenowych Azure AD.
>
>

## <a name="related-content"></a>Powiązana zawartość
* [Zarządzaj domeną usługi Azure AD Domain Services](manage-domain.md)
* [Zarządzanie zasadami grupy dla usług domenowych Azure AD](manage-group-policy.md)
* [Centrum administracyjne usługi Active Directory: Wprowadzenie](https://technet.microsoft.com/library/dd560651.aspx)
* [Przewodnik krok po kroku konta usług](https://technet.microsoft.com/library/dd548356.aspx)
