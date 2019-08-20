---
title: Przyłączanie maszyny wirtualnej z systemem Windows Server do Azure Active Directory Domain Services | Microsoft Docs
description: Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej przy użyciu szablonów Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612277"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej przy użyciu szablonu Menedżer zasobów
W tym artykule opisano sposób przyłączania maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure AD Domain Services przy użyciu szablonów Menedżer zasobów.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:
1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](tutorial-create-instance.md).
4. Upewnij się, że adresy IP domeny zarządzanej zostały skonfigurowane jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="install-and-configure-required-tools"></a>Instalowanie i Konfigurowanie wymaganych narzędzi
Aby wykonać kroki opisane w tym dokumencie, można użyć jednej z następujących opcji:
* **Azure PowerShell**: [Instalowanie i Konfigurowanie](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interfejs wiersza polecenia platformy Azure**: [Instalowanie i Konfigurowanie](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Option 1: Inicjowanie obsługi administracyjnej nowej maszyny wirtualnej z systemem Windows Server i Przyłącz ją do domeny zarządzanej
**Nazwa szablonu szybkiego startu**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Aby wdrożyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej, wykonaj następujące czynności:
1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. Na stronie **wdrożenie niestandardowe** podaj wymagane informacje, aby zainicjować obsługę administracyjną maszyny wirtualnej.
4. Wybierz **subskrypcję platformy Azure** , w której chcesz zainicjować obsługę administracyjną maszyny wirtualnej. Wybierz tę samą subskrypcję platformy Azure, w której włączono Azure AD Domain Services.
5. Wybierz istniejącą **grupę zasobów** lub Utwórz nową.
6. Wybierz **lokalizację** , w której chcesz wdrożyć nową maszynę wirtualną.
7. W polu **istniejąca nazwa**sieci wirtualnej Określ sieć wirtualną, w której wdrożono Azure AD Domain Services domenę zarządzaną.
8. W polu **istniejąca nazwa podsieci**Określ podsieć w sieci wirtualnej, w której chcesz wdrożyć tę maszynę wirtualną. Nie wybieraj podsieci bramy w sieci wirtualnej. Nie należy również wybierać dedykowanej podsieci, w której wdrożona jest domena zarządzana.
9. W polu **prefiks etykiety DNS**Określ nazwę hosta maszyny wirtualnej, która jest obsługiwana. Na przykład "contoso-win".
10. Wybierz odpowiedni **rozmiar maszyn** wirtualnych dla maszyny wirtualnej.
11. W polu **domena do**przyłączenia Podaj nazwę domeny DNS domeny zarządzanej.
12. W polu **Nazwa użytkownika domeny**Określ nazwę konta użytkownika w domenie zarządzanej, która ma zostać użyta do przyłączenia maszyny wirtualnej do domeny zarządzanej.
13. W polu **hasło domeny**określ hasło do konta użytkownika domeny, do którego odwołuje się parametr "domainUsername".
14. Opcjonalnie: Można określić **ścieżkę jednostki organizacyjnej** do niestandardowej jednostki organizacyjnej, w której ma zostać dodana maszyna wirtualna. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodana do domyślnej jednostki organizacyjnej domeny usługi **AAD** w domenie zarządzanej.
15. W polu **Nazwa użytkownika administratora maszyny** wirtualnej Określ nazwę konta administratora lokalnego dla maszyny wirtualne.
16. W polu **hasło administratora maszyny** wirtualnej należy określić hasło administratora lokalnego dla maszyn wirtualnych. Podaj silne hasło administratora lokalnego dla maszyny wirtualnej w celu ochrony przed atakami polegającymi na rozliczeniu.
17. Kliknij przycisk **Zgadzam się na powyższe warunki i postanowienia**.
18. Kliknij przycisk **Kup** , aby zainicjować obsługę administracyjną maszyny wirtualnej.

> [!WARNING]
> **Obsługa haseł z zachowaniem ostrożności.**
> Plik parametrów szablonu zawiera hasła dla kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że ten plik nie jest pozostawiony w udziałach plików ani w innych lokalizacjach udostępnionych. Zalecamy, aby usunąć ten plik po zakończeniu wdrażania maszyn wirtualnych.
>

Po pomyślnym zakończeniu wdrożenia nowo zainicjowana maszyna wirtualna z systemem Windows zostanie przyłączona do domeny zarządzanej.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opcja 2: Przyłączanie istniejącej maszyny wirtualnej z systemem Windows Server do domeny zarządzanej
**szablon szybkiego startu**: [201-VM-Domain-Join-exist](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Aby przyłączyć istniejącą maszynę wirtualną z systemem Windows Server do domeny zarządzanej, wykonaj następujące czynności:
1. Przejdź do [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kliknij przycisk **Wdrażaj na platformie Azure**.
3. Na stronie **wdrożenie niestandardowe** podaj wymagane informacje, aby zainicjować obsługę administracyjną maszyny wirtualnej.
4. Wybierz **subskrypcję platformy Azure** , w której chcesz zainicjować obsługę administracyjną maszyny wirtualnej. Wybierz tę samą subskrypcję platformy Azure, w której włączono Azure AD Domain Services.
5. Wybierz istniejącą **grupę zasobów** lub Utwórz nową.
6. Wybierz **lokalizację** , w której chcesz wdrożyć nową maszynę wirtualną.
7. W polu **listy maszyn** wirtualnych określ nazwy istniejących maszyn, które mają być przyłączone do domeny zarządzanej. Poszczególne nazwy maszyn wirtualnych należy rozdzielić przecinkami. Na przykład **contoso-Web, contoso-API**.
8. W polu **Nazwa użytkownika**przyłączania do domeny określ nazwę konta użytkownika w domenie zarządzanej, która ma zostać użyta do przyłączenia maszyny wirtualnej do domeny zarządzanej.
9. W obszarze **hasło użytkownika**dołączanie do domeny określ hasło do konta użytkownika domeny, do którego odwołuje się parametr "domainUsername".
10. W polu Nazwa **FQDN domeny**Podaj nazwę domeny DNS domeny zarządzanej.
11. Opcjonalnie: Można określić **ścieżkę jednostki organizacyjnej** do niestandardowej jednostki organizacyjnej, w której ma zostać dodana maszyna wirtualna. Jeśli nie określisz wartości tego parametru, maszyna wirtualna zostanie dodana do domyślnej jednostki organizacyjnej domeny usługi **AAD** w domenie zarządzanej.
12. Kliknij przycisk **Zgadzam się na powyższe warunki i postanowienia**.
13. Kliknij przycisk **Kup** , aby zainicjować obsługę administracyjną maszyny wirtualnej.

> [!WARNING]
> **Obsługa haseł z zachowaniem ostrożności.**
> Plik parametrów szablonu zawiera hasła dla kont domeny, a także hasła administratora lokalnego dla maszyny wirtualnej. Upewnij się, że ten plik nie jest pozostawiony w udziałach plików ani w innych lokalizacjach udostępnionych. Zalecamy, aby usunąć ten plik po zakończeniu wdrażania maszyn wirtualnych.
>

Po pomyślnym zakończeniu wdrożenia określone maszyny wirtualne z systemem Windows są przyłączone do domeny zarządzanej.


## <a name="related-content"></a>Powiązana zawartość
* [Omówienie programu Azure PowerShell](/powershell/azure/overview)
* [Szablon szybkiego startu platformy Azure — domena Dołącz do nowej maszyny wirtualnej](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Szablon szybkiego startu platformy Azure — domena przyłączanie istniejących maszyn wirtualnych](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
