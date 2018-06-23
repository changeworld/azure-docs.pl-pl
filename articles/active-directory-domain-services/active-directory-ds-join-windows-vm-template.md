---
title: Dołącz do systemu Windows Server maszyny Wirtualnej do usług domenowych Azure Active Directory | Dokumentacja firmy Microsoft
description: Przyłącz maszynę wirtualną systemu Windows Server do domeny zarządzanej przy użyciu szablonów usługi Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 85dd7e73afe4558e58084cc133e3cfdc93e4088d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333561"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej przy użyciu szablonu usługi Resource Manager
W tym artykule przedstawiono sposób Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD przy użyciu szablonów usługi Resource Manager.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:
1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. Upewnij się, że skonfigurowano adresy IP domeny zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Zainstaluj i skonfiguruj wymagane narzędzia
Do wykonania kroków opisanych w tym dokumencie, można użyć jednej z następujących opcji:
* **Program Azure PowerShell**: [zainstalować i skonfigurować](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interfejs wiersza polecenia platformy Azure i platform**: [zainstalować i skonfigurować](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opcja 1: Udostępnianie nowej maszyny Wirtualnej systemu Windows Server i przyłączyć go do domeny zarządzanej
**Nazwa szablonu szybki start**: [201-vm-przyłączania do domeny](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Aby wdrożyć maszynę wirtualną systemu Windows Server i przyłączyć go do domeny zarządzanej, wykonaj następujące czynności:
1. Przejdź do [szablonów szybki start](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. W **wdrożenie niestandardowe** Podaj informacje wymagane do aprowizowania maszyny wirtualnej.
4. Wybierz **subskrypcji platformy Azure** umożliwiający aprowizowanie maszyny wirtualnej. Wybierz tej samej subskrypcji platformy Azure, w której włączono usługi domenowe Azure AD.
5. Wybierz istniejące **grupy zasobów** lub Utwórz nową.
6. Wybierz **lokalizacji** w której chcesz wdrożyć nową maszynę wirtualną.
7. W **istniejącej nazwy sieci Wirtualnej**, określ sieć wirtualną, w której wdrożono domeny zarządzanej usług domenowych Azure AD.
8. W **istniejącej nazwy podsieci**, określ podsieć w sieci wirtualnej, w której chcesz wdrożyć tej maszyny wirtualnej. Nie należy wybierać podsieci bramy sieci wirtualnej. Ponadto nie należy wybierać dedykowanych podsieci, w której jest wdrażane domeny zarządzanej.
9. W **prefiksu etykiety DNS**, określ nazwę hosta dla maszyny wirtualnej zainicjowanie obsługi. Na przykład "contoso Windows".
10. Wybierz odpowiednie **rozmiar maszyny Wirtualnej** dla maszyny wirtualnej.
11. W **do przyłączanie do domeny**, określ nazwę domeny DNS domeny zarządzanej.
12. W **nazwa domeny użytkownika**, określ nazwę konta użytkownika na Twojej domeny zarządzanej, które mają być używane do przyłączenia do domeny zarządzanej maszyny Wirtualnej.
13. W **hasła domeny**, określ hasło konta użytkownika domeny, które są określone przez parametr "domainUsername".
14. Opcjonalnie: Można określić **ścieżka jednostki Organizacyjnej** do niestandardowej jednostki Organizacyjnej, w którym można dodać maszyny wirtualnej. Jeśli nie określisz wartości tego parametru, maszyna wirtualna jest dodane do domyślnej **komputerów kontrolera domeny usługi AAD** jednostki Organizacyjnej w domenie zarządzanej.
15. W **nazwa użytkownika administratora maszyny Wirtualnej** Określ nazwę konta administratora lokalnego dla maszyny wirtualnej.
16. W **hasło administratora maszyny Wirtualnej** Określ hasło administratora lokalnego dla maszyny wirtualnej. Podaj hasło administratora lokalnego silne dla maszyny wirtualnej zapewnić ochronę przed atakami siłowymi hasła.
17. Kliknij przycisk **akceptuję warunki i postanowienia, o których wspomniano**.
18. Kliknij przycisk **zakupu** do aprowizowania maszyny wirtualnej.

> [!WARNING]
> **Obsługi haseł z rozwagą.**
> Plik parametrów szablonu zawiera haseł dla kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że komputer nie znajdzie się ten plik leżącego wokół w udziałach plików lub innych lokalizacji udostępnionej. Firma Microsoft zaleca usunięcia tego pliku, po zakończeniu wdrażania maszyn wirtualnych.
>

Po wdrożeniu zakończy się pomyślnie, nowo aprowizowanej maszyny wirtualnej systemu Windows jest przyłączony do domeny zarządzanej.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opcja 2: Dołącz istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej
**Szybki start szablonu**: [istniejących 201-vm domeny sprzężenia](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Aby przyłączyć się do domeny zarządzanej istniejącej maszyny wirtualnej systemu Windows Server, wykonaj następujące czynności:
1. Przejdź do [szablonów szybki start](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. W **wdrożenie niestandardowe** Podaj informacje wymagane do aprowizowania maszyny wirtualnej.
4. Wybierz **subskrypcji platformy Azure** umożliwiający aprowizowanie maszyny wirtualnej. Wybierz tej samej subskrypcji platformy Azure, w której włączono usługi domenowe Azure AD.
5. Wybierz istniejące **grupy zasobów** lub Utwórz nową.
6. Wybierz **lokalizacji** w której chcesz wdrożyć nową maszynę wirtualną.
7. W **listy wirtualna** określ nazwy istniejących maszyn wirtualnych być przyłączony do domeny zarządzanej. Użyj przecinka, aby Oddziel poszczególne nazwy maszyny Wirtualnej. Na przykład **sieci web firmy contoso, contoso-api**.
8. W **nazwę użytkownika przyłączania do domeny**, określ nazwę konta użytkownika na Twojej domeny zarządzanej, które mają być używane do przyłączenia do domeny zarządzanej maszyny Wirtualnej.
9. W **hasło użytkownika Dołącz do domeny**, określ hasło konta użytkownika domeny, które są określone przez parametr "domainUsername".
10. W **nazwa FQDN domeny**, określ nazwę domeny DNS domeny zarządzanej.
11. Opcjonalnie: Można określić **ścieżka jednostki Organizacyjnej** do niestandardowej jednostki Organizacyjnej, w którym można dodać maszyny wirtualnej. Jeśli nie określisz wartości tego parametru, maszyna wirtualna jest dodane do domyślnej **komputerów kontrolera domeny usługi AAD** jednostki Organizacyjnej w domenie zarządzanej.
12. Kliknij przycisk **akceptuję warunki i postanowienia, o których wspomniano**.
13. Kliknij przycisk **zakupu** do aprowizowania maszyny wirtualnej.

> [!WARNING]
> **Obsługi haseł z rozwagą.**
> Plik parametrów szablonu zawiera haseł dla kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że komputer nie znajdzie się ten plik leżącego wokół w udziałach plików lub innych lokalizacji udostępnionej. Firma Microsoft zaleca usunięcia tego pliku, po zakończeniu wdrażania maszyn wirtualnych.
>

Po wdrożenie zakończy się pomyślnie, określony maszyn wirtualnych systemu Windows są przyłączone do domeny zarządzanej.


## <a name="related-content"></a>Powiązana zawartość
* [Omówienie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Przyłącz do domeny szablonu szybki start Azure - nowej maszyny Wirtualnej](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Przyłącz do domeny szablonu szybki start Azure - istniejące maszyny wirtualne](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
