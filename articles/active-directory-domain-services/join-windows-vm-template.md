---
title: Przyłączanie maszyny Wirtualnej z systemem Windows do usługi Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej przy użyciu szablonów usługi Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: e4ca613059e10755056616b964cc500625fef187
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66245957"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej przy użyciu szablonu usługi Resource Manager
W tym artykule pokazano, jak dołączyć maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD przy użyciu szablonów usługi Resource Manager.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:
1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. Upewnij się, że zostały skonfigurowane adresy IP w domenie zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Zainstaluj i skonfiguruj wymagane narzędzia
Do wykonania kroków opisanych w tym dokumencie, można użyć jednej z następujących opcji:
* **Azure PowerShell**: [Instalowanie i konfigurowanie](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interfejs wiersza polecenia platformy Azure**: [Instalowanie i konfigurowanie](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opcja 1: Aprowizowanie nowej maszyny Wirtualnej systemu Windows Server, a następnie przyłączyć ją do domeny zarządzanej
**Nazwa szablonu szybkiego startu**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Aby wdrożyć maszynę wirtualną z systemem Windows Server, a następnie przyłączyć ją do domeny zarządzanej, wykonaj następujące czynności:
1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. W **wdrożenie niestandardowe** Podaj informacje wymagane do obsługi administracyjnej maszyny wirtualnej.
4. Wybierz **subskrypcji platformy Azure** w do aprowizowania maszyny wirtualnej. Wybierz tej samej subskrypcji platformy Azure, w której włączono usługi domenowe Azure AD.
5. Wybierz istniejące **grupy zasobów** lub utworzyć nowy.
6. Wybierz **lokalizacji** w której chcesz wdrożyć nową maszynę wirtualną.
7. W **istniejącej nazwy sieci Wirtualnej**, określ sieć wirtualną, w której wdrożono Twojej domeny zarządzanej usług domenowych Azure AD.
8. W **istniejącej nazwy podsieci**, określ podsieć w sieci wirtualnej, której chcesz wdrożyć tę maszynę wirtualną. Nie wybieraj podsieci bramy w sieci wirtualnej. Ponadto nie należy wybierać dedykowanej podsieci, w której jest wdrażany z domeną zarządzaną.
9. W **prefiks etykiety DNS**, określ nazwę hosta dla maszyny wirtualnej jest aprowizowane. Na przykład "contoso-win".
10. Wybierz odpowiedni **rozmiar maszyny Wirtualnej** dla maszyny wirtualnej.
11. W **domeny do przyłączenia**, określ nazwę domeny DNS z domeny zarządzanej.
12. W **domena nazwa_użytkownika**, określ nazwę konta użytkownika w Twojej domeny zarządzanej, które mają być używane do dołączenia do maszyny Wirtualnej do domeny zarządzanej.
13. W **hasła domeny**, określ hasło konta użytkownika domeny, które są określone przez parametr "domainUsername".
14. Opcjonalnie: Można określić **ścieżka jednostki Organizacyjnej** niestandardowe jednostki organizacyjnej, w którym można dodać maszyny wirtualnej. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodany do domyślnego **usługi AAD DC komputerów** jednostki Organizacyjnej w domenie zarządzanej.
15. W **nazwa użytkownika administratora maszyny Wirtualnej** pola, określ nazwę konta administratora lokalnego dla maszyny wirtualnej.
16. W **hasło administratora maszyny Wirtualnej** pola, określ hasło administratora lokalnego dla maszyny wirtualnej. Podaj hasło silne administratora lokalnego dla maszyny wirtualnej, aby zapewnić ochronę przed atakami siłowymi hasła.
17. Kliknij przycisk **zgodę na warunki i postanowienia, o których wspomniano**.
18. Kliknij przycisk **zakupu** do aprowizowania maszyny wirtualnej.

> [!WARNING]
> **Obsługi haseł z rozwagą.**
> Plik parametrów szablonu zawiera haseł do kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że komputer nie znajdzie się ten plik leżącego wokół w udziałach plików lub innych lokalizacji udostępnionej. Firma Microsoft zaleca, możesz usunąć ten plik po zakończeniu wdrażania maszyn wirtualnych.
>

Po pomyślnym zakończeniu wdrożenia użytkownicy nowo aprowizowanych maszynę wirtualną Windows jest przyłączony do domeny zarządzanej.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opcja 2: Dołącz do istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej
**Szablon szybkiego startu**: [istniejące 201-vm — domeny — sprzężenia](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Aby dołączyć istniejącej maszyny wirtualnej systemu Windows Server do domeny zarządzanej, wykonaj następujące czynności:
1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. W **wdrożenie niestandardowe** Podaj informacje wymagane do obsługi administracyjnej maszyny wirtualnej.
4. Wybierz **subskrypcji platformy Azure** w do aprowizowania maszyny wirtualnej. Wybierz tej samej subskrypcji platformy Azure, w której włączono usługi domenowe Azure AD.
5. Wybierz istniejące **grupy zasobów** lub utworzyć nowy.
6. Wybierz **lokalizacji** w której chcesz wdrożyć nową maszynę wirtualną.
7. W **listy maszyn wirtualnych** określ nazwy istniejących maszyn wirtualnych zostanie przyłączony do domeny zarządzanej. Oddziel poszczególne nazwy maszyny Wirtualnej, należy użyć przecinka. Na przykład **web firmy contoso, interfejs api contoso**.
8. W **nazwę użytkownika przyłączania do domeny**, określ nazwę konta użytkownika w Twojej domeny zarządzanej, które mają być używane do dołączenia do maszyny Wirtualnej do domeny zarządzanej.
9. W **hasło użytkownika domeny z Dołącz**, określ hasło konta użytkownika domeny, które są określone przez parametr "domainUsername".
10. W **nazwa FQDN domeny**, określ nazwę domeny DNS z domeny zarządzanej.
11. Opcjonalnie: Można określić **ścieżka jednostki Organizacyjnej** niestandardowe jednostki organizacyjnej, w którym można dodać maszyny wirtualnej. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodany do domyślnego **usługi AAD DC komputerów** jednostki Organizacyjnej w domenie zarządzanej.
12. Kliknij przycisk **zgodę na warunki i postanowienia, o których wspomniano**.
13. Kliknij przycisk **zakupu** do aprowizowania maszyny wirtualnej.

> [!WARNING]
> **Obsługi haseł z rozwagą.**
> Plik parametrów szablonu zawiera haseł do kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że komputer nie znajdzie się ten plik leżącego wokół w udziałach plików lub innych lokalizacji udostępnionej. Firma Microsoft zaleca, możesz usunąć ten plik po zakończeniu wdrażania maszyn wirtualnych.
>

Po pomyślnym zakończeniu wdrożenia określonego maszyn wirtualnych Windows są przyłączone do domeny zarządzanej.


## <a name="related-content"></a>Powiązana zawartość
* [Omówienie programu Azure PowerShell](/powershell/azure/overview)
* [Szablon szybkiego startu platformy Azure — domeny do nowej maszyny Wirtualnej](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Szablon szybkiego startu platformy Azure — przyłączanie do domeny istniejących maszyn wirtualnych](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
