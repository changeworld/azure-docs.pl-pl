---
title: 'Usług domenowych Azure Active Directory: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services przy użyciu portalu Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: 607ca1900200f3b163d69a7aa4cb700b842f9334
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586835"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Włączanie usługi Azure Active Directory Domain Services przy użyciu portalu Azure


## <a name="before-you-begin"></a>Przed rozpoczęciem
Zapoznaj się z tematem [Networking considerations for Azure Active Directory Domain Services](active-directory-ds-networking.md) (Zagadnienia dotyczące sieci w usłudze Azure Active Directory Domain Services).


## <a name="task-2-configure-network-settings"></a>Zadanie 2: Konfigurowanie ustawień sieciowych
Następne zadanie konfiguracji to aby utworzyć sieć wirtualną platformy Azure i dedykowanych podsieci w niej. Usługi Azure Active Directory Domain Services w tej podsieci możesz włączyć w sieci wirtualnej. Może również wybierają istniejącej sieci wirtualnej i Utwórz dedykowane podsieć w niej.

1. Kliknij przycisk **sieci wirtualnej** do wybrania sieci wirtualnej.
    > [!NOTE]
    > **Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń.** Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejących domen zarządzanych wdrożone w klasycznej sieci wirtualnych w dalszym ciągu obsługiwana. Microsoft umożliwi migracji istniejącej domeny zarządzanej z klasycznej sieci wirtualnej do Menedżera zasobów sieci wirtualnej w bliskiej przyszłości.
    >

2. Na **sieci wirtualnej wybierz** strony, zobacz wszystkie istniejące sieci wirtualne. Zostanie wyświetlony tylko sieci wirtualnych, które należą do grupy zasobów i lokalizacja platformy Azure, wybranego na **podstawy** strony kreatora.
3. Wybierz sieć wirtualną, w którym można włączyć usługi domenowe Azure AD. Można wybrać istniejącej sieci wirtualnej lub Utwórz nową.

  > [!TIP]
  > **Domeny zarządzanej nie można przenieść do innej sieci wirtualnej, po włączeniu usług domenowych Azure AD.** Wybierz właściwą sieci, aby umożliwić domeny zarządzanej. Po utworzeniu domeny zarządzanej, nie można jej przenieść do innej sieci wirtualnej, nie usuwając domeny zarządzanej. Firma Microsoft zaleca przejrzenie [sieci zagadnienia dotyczące usługi Azure Active Directory Domain Services](active-directory-ds-networking.md) przed kontynuowaniem.  
  >

4. **Utwórz sieć wirtualną:** kliknij **Utwórz nowy** do tworzenia nowej sieci wirtualnej. Użyj dedykowanych podsieci dla usług domenowych Azure AD. Na przykład utworzyć podsieć o nazwie "DomainServices", dzięki czemu można łatwo dla innych administratorów zrozumieć, co jest wdrażana w obrębie podsieci. Kliknij przycisk **OK** po zakończeniu.

    ![Wybierz sieć wirtualną](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Upewnij się, że wybierz przestrzeń adresów, która znajduje się w prywatnej przestrzeń adresów IP. Adresy IP, które nie ma, które znajdują się w przestrzeni adresów publicznych powodować błędy w ramach usług domenowych Azure AD.

5. **Istniejącej sieci wirtualnej:** Jeśli planowane jest pobranie istniejącej sieci wirtualnej, [Utwórz dedykowane podsieć przy użyciu rozszerzenia sieci wirtualnych](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), a następnie wybierz tej podsieci. Kliknij przycisk **sieci wirtualnej** do wybrania istniejącej sieci wirtualnej. Kliknij przycisk **podsieci** pobrania dedykowanych podsieci w istniejącej sieci wirtualnej, w którym można włączyć nowe domeny zarządzanej. Kliknij przycisk **OK** po zakończeniu.

    ![Wybierz podsieć w sieci wirtualnej](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Wskazówki dotyczące wybierania podsieci**
  > 1. Użyj dedykowanych podsieci dla usług domenowych Azure AD. Nie należy wdrażać innych maszyn wirtualnych do tej podsieci. Ta konfiguracja umożliwia konfigurowanie grup zabezpieczeń sieci (NSG) dla maszyn wirtualnych/obciążeń bez przerywania Twojej domeny zarządzanej. Aby uzyskać więcej informacji, zobacz [sieci zagadnienia dotyczące usługi Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Nie zaznaczaj podsieci bramy do wdrażania usług domenowych Azure AD, ponieważ nie jest obsługiwana konfiguracja.
  3. Wybrane podsieci musi mieć co najmniej 3 – 5 dostępnych adresów IP w jego przestrzeni adresowej.
  >

6. Gdy wszystko będzie gotowe, kliknij przycisk **OK** do przejścia do **grupy administratorów** stronie kreatora.


## <a name="next-step"></a>Następny krok
[Zadanie 3: Konfigurowanie grupy administracyjnej i włączyć usługi domenowe Azure AD](active-directory-ds-getting-started-admingroup.md)
