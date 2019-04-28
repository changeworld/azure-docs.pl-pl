---
title: 'Azure Active Directory Domain Services: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 3020d7b29f19ec2ab578acbebac8db8ea320a844
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103578"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal


## <a name="before-you-begin"></a>Przed rozpoczęciem
Zapoznaj się z tematem [Networking considerations for Azure Active Directory Domain Services](active-directory-ds-networking.md) (Zagadnienia dotyczące sieci w usłudze Azure Active Directory Domain Services).


## <a name="task-2-configure-network-settings"></a>Zadanie 2. Konfigurowanie ustawień sieciowych
Następne zadanie konfiguracji jest tworzenie sieci wirtualnej platformy Azure i dedykowanej podsieci w nim. Usługi Azure Active Directory Domain Services w tej podsieci możesz włączyć w sieci wirtualnej. Możesz również wybrać istniejącą sieć wirtualną i Utwórz dedykowanej podsieci w obrębie tej.

1. Kliknij przycisk **sieć wirtualna** do wybrania sieci wirtualnej.
    > [!NOTE]
    > **Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń.** Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejące domeny zarządzane wdrożone w klasycznych sieciach wirtualnych w dalszym ciągu być obsługiwane. Microsoft umożliwią migracji istniejącej domeny zarządzanej z klasyczną siecią wirtualną z siecią wirtualną Menedżera zasobów w niedalekiej przyszłości.
    >

2. Na **wybierz sieć wirtualną** strony, zobacz wszystkie istniejące sieci wirtualne. Zostanie wyświetlony tylko sieci wirtualne, które należą do grupy zasobów i lokalizacji platformy Azure, wybranego na **podstawy** strony kreatora.
3. Wybierz sieć wirtualną, w którym można włączyć usługi domenowe Azure AD. Możesz wybrać istniejącą sieć wirtualną lub utworzyć nową.

   > [!TIP]
   > **Twoja domena zarządzana nie można przenieść do innej sieci wirtualnej, po włączeniu usług domenowych Azure AD.** Wybrać właściwą sieć włączyć domenę zarządzaną. Po utworzeniu domeny zarządzanej, nie można jej przenieść do innej sieci wirtualnej bez usuwania domeny zarządzanej. Zaleca się, przeglądając [sieć zagadnienia dotyczące usługi Azure Active Directory Domain Services](active-directory-ds-networking.md) przed kontynuowaniem.  
   >

4. **Tworzenie sieci wirtualnej:** Kliknij przycisk **Utwórz nową** do tworzenia nowej sieci wirtualnej. Użyj dedykowanej podsieci dla usługi Azure AD Domain Services. Na przykład utwórz podsieć o nazwie "DomainServices", co ułatwia dla innych administratorów zrozumieć, co to jest wdrażana w obrębie podsieci. Kliknij przycisk **OK** po zakończeniu.

    ![Wybierz sieć wirtualną](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Pamiętaj wybrać przestrzeń adresów, który znajduje się w prywatnej przestrzeni adresów IP. Adresy IP, które nie są jego własnością, które znajdują się w przestrzeni adresów publicznych powodują błędy w usługach domenowych Azure AD.

5. **Istniejącej sieci wirtualnej:** Jeśli planujesz wybrać istniejącą sieć wirtualną [utworzyć dedykowaną podsieć przy użyciu rozszerzenia sieci wirtualnych](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), a następnie wybierz odpowiednią podsieć. Kliknij przycisk **sieci wirtualnej** wybrać istniejącą sieć wirtualną. Kliknij przycisk **podsieci** pobrania dedykowaną podsieć w istniejącej sieci wirtualnej, w którym należy włączyć domenę zarządzaną. Kliknij przycisk **OK** po zakończeniu.

    ![Wybierz podsieci w sieci wirtualnej](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Wskazówki dotyczące wybierania podsieci**
   > 1. Użyj dedykowanej podsieci dla usługi Azure AD Domain Services. Nie należy wdrażać żadnych maszyn wirtualnych z tą podsiecią. Ta konfiguracja pozwala na konfigurowanie grup zabezpieczeń sieci (NSG) dla maszyn wirtualnych/obciążeń bez przerywania Twojej domeny zarządzanej. Aby uzyskać więcej informacji, zobacz [sieć zagadnienia dotyczące usługi Azure Active Directory Domain Services](active-directory-ds-networking.md).
   > 2. Nie wybieraj podsieci bramy do wdrożenia usług domenowych Azure AD, ponieważ nie jest obsługiwaną konfiguracją.
   > 3. Podsieć, którą wybrano musi mieć co najmniej 3 – 5 dostępnych adresów IP w jego przestrzeni adresowej.

6. Gdy wszystko będzie gotowe, kliknij przycisk **OK** aby przejść do **grupy Administrator** strony kreatora.


## <a name="next-step"></a>Następny krok
[Zadanie 3: skonfigurować grupy administracyjnej, a także włączyć usługi domenowe Azure AD](active-directory-ds-getting-started-admingroup.md)
