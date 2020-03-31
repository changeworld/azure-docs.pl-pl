---
title: Dołączanie maszyny wirtualnej systemu Windows do usług Azure AD DS za pomocą szablonu | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z szablonów usługi Azure Resource Manager w celu dołączenia nowej lub istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych usługi active directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: c9b25fe7bc47e05972aebb194e9d94c1ea6dd247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298738"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych usługi active directory platformy Azure przy użyciu szablonu Menedżera zasobów

Aby zautomatyzować wdrażanie i konfigurację maszyn wirtualnych platformy Azure (maszyn wirtualnych), można użyć szablonu Menedżera zasobów. Te szablony umożliwiają tworzenie spójnych wdrożeń za każdym razem. Rozszerzenia mogą być również zawarte w szablonach, aby automatycznie skonfigurować maszynę wirtualną w ramach wdrożenia. Jedno przydatne rozszerzenie łączy maszyny wirtualne do domeny, która może być używana z domenami zarządzanymi usług domenowych Usługi Active Directory (Azure AD DS) usługi Azure.

W tym artykule pokazano, jak utworzyć maszynę wirtualną systemu Windows Server i dołączyć do niej w domenie zarządzanej usług Ad DS przy użyciu szablonów Menedżera zasobów. Dowiesz się również, jak dołączyć istniejącą maszynę wirtualną systemu Windows Server do domeny usług Ad DS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby pierwszy samouczek [tworzy i konfiguruje wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Konto użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS.

## <a name="azure-resource-manager-template-overview"></a>Omówienie szablonu usługi Azure Resource Manager

Szablony Usługi Resource Manager umożliwiają definiowanie infrastruktury platformy Azure w kodzie. Wymagane zasoby, połączenia sieciowe lub konfigurację maszyn wirtualnych można zdefiniować w szablonie. Szablony te tworzą spójne, powtarzalne wdrożenia za każdym razem i mogą być wersjonowane podczas wprowadzania zmian. Aby uzyskać więcej informacji, zobacz [omówienie szablonów usługi Azure Resource Manager][template-overview].

Każdy zasób jest zdefiniowany w szablonie przy użyciu notacji obiektu JavaScript (JSON). W poniższym przykładzie JSON użyto typu zasobu *Microsoft.Compute/virtualMachines/extensions* do zainstalowania rozszerzenia dołączania do domeny usługi Active Directory. Parametry są używane, które można określić w czasie wdrażania. Po wdrożeniu rozszerzenia maszyna wirtualna jest przyłączana do określonej domeny zarządzanej usługi Azure AD DS.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

To rozszerzenie maszyny Wirtualnej można wdrożyć, nawet jeśli nie tworzysz maszyny Wirtualnej w tym samym szablonie. Przykłady w tym artykule pokazują oba następujące podejścia:

* [Tworzenie maszyny Wirtualnej systemu Windows Server i dołączanie do domeny zarządzanej](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Dołączanie istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Tworzenie maszyny Wirtualnej systemu Windows Server i dołączanie do domeny zarządzanej

Jeśli potrzebujesz maszyny Wirtualnej systemu Windows Server, możesz ją utworzyć i skonfigurować przy użyciu szablonu Menedżera zasobów. Po wdrożeniu maszyny Wirtualnej jest instalowane rozszerzenie, aby dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS. Jeśli masz już maszynę wirtualną, którą chcesz dołączyć do domeny zarządzanej usług Azure AD DS, przejdź do poziomu [Dołącz do istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej](#join-an-existing-windows-server-vm-to-a-managed-domain).

Aby utworzyć maszynę wirtualną systemu Windows Server, a następnie dołącz ją do domeny zarządzanej usług Azure AD DS, wykonaj następujące kroki:

1. Przejdź do [szablonu przewodnika Szybki start](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Wybierz opcję **wdrażania na platformie Azure**.
1. Na stronie **Wdrożenia niestandardowego** wprowadź następujące informacje, aby utworzyć maszynę wirtualną systemu Windows Server i dołączyć do domeny zarządzanej usług Azure AD DS:

    | Ustawienie                   | Wartość |
    |---------------------------|-------|
    | Subskrypcja              | Wybierz tę samą subskrypcję platformy Azure, w której włączono usługi domenowe usługi Azure AD Domain Services. |
    | Grupa zasobów            | Wybierz grupę zasobów dla maszyny Wirtualnej. |
    | Lokalizacja                  | Wybierz lokalizację maszyny Wirtualnej. |
    | Istniejąca nazwa sieci wirtualnej        | Nazwa istniejącej sieci wirtualnej do podłączenia maszyny Wirtualnej, takich jak *myVnet*. |
    | Istniejąca nazwa podsieci      | Nazwa istniejącej podsieci sieci wirtualnej, takiej jak *Obciążenia*. |
    | Prefiks etykiety DNS          | Wprowadź nazwę DNS, która ma być używana dla maszyny Wirtualnej, na przykład *myvm*. |
    | Rozmiar maszyny wirtualnej                   | Określ rozmiar maszyny Wirtualnej, na przykład *Standard_DS2_v2*. |
    | Domena do dołączenia            | Nazwa DNS domeny zarządzanej przez usługę Azure AD DS, na przykład *aaddscontoso.com*. |
    | Nazwa użytkownika domeny           | Konto użytkownika w domenie zarządzanej usług Azure AD DS, które powinno być `contosoadmin@aaddscontoso.com`używane do przyłączenia maszyny Wirtualnej do domeny zarządzanej, takiej jak . To konto musi być częścią domeny zarządzanej usług Azure AD DS. |
    | Hasło domeny           | Hasło dla konta użytkownika określone w poprzednim ustawieniu. |
    | Opcjonalna ścieżka OU          | Niestandardowa instalacja organizacyjna, w której można dodać maszynę wirtualną. Jeśli nie określisz wartości dla tego parametru, maszyna wirtualna zostanie dodana do domyślnej ou *dolegać komputerów kontrolera domeny usługi AAD.* |
    | Nazwa administratora maszyny Wirtualnej         | Określ konto administratora lokalnego do utworzenia na maszynie wirtualnej. |
    | Hasło administratora maszyny Wirtualnej         | Określ hasło administratora lokalnego maszyny Wirtualnej. Utwórz silne hasło administratora lokalnego, aby chronić przed atakami typu "brutalne użycie hasłem". |

1. Przejrzyj warunki, a następnie zaznacz **pole, w które zgadzam się z warunkami podanymi powyżej.** Gdy jest to gotowe, wybierz **pozycję Zakup,** aby utworzyć maszynę wirtualną i dołączyć ją do domeny zarządzanej usług Azure AD DS.

> [!WARNING]
> **Należy obchodzić się z hasłami ostrożnie.**
> Plik parametru szablonu żąda hasła dla konta użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS. Nie wprowadzaj ręcznie wartości do tego pliku i nie pozostawiaj go dostępnego w udziałach plików lub innych udostępnionych lokalizacjach.

Pomyślne zakończenie wdrożenia zajmuje kilka minut. Po zakończeniu maszyny Wirtualnej systemu Windows jest tworzony i przyłączany do domeny zarządzanej usług Azure AD DS. Maszynę wirtualną można zarządzać lub zalogować się przy użyciu kont domeny.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Dołączanie istniejącej maszyny Wirtualnej systemu Windows Server do domeny zarządzanej

Jeśli masz istniejącą maszynę wirtualną lub grupę maszyn wirtualnych, które chcesz dołączyć do domeny zarządzanej usług Azure AD DS, możesz użyć szablonu Menedżera zasobów, aby po prostu wdrożyć rozszerzenie maszyny Wirtualnej.

Aby dołączyć istniejącą maszynę wirtualną systemu Windows Server do domeny zarządzanej usług Azure AD DS, wykonaj następujące kroki:

1. Przejdź do [szablonu przewodnika Szybki start](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Wybierz opcję **wdrażania na platformie Azure**.
1. Na stronie **wdrożenia niestandardowego** wprowadź następujące informacje, aby dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS:

    | Ustawienie                   | Wartość |
    |---------------------------|-------|
    | Subskrypcja              | Wybierz tę samą subskrypcję platformy Azure, w której włączono usługi domenowe usługi Azure AD Domain Services. |
    | Grupa zasobów            | Wybierz grupę zasobów z istniejącą maszyną wirtualną. |
    | Lokalizacja                  | Wybierz lokalizację istniejącej maszyny Wirtualnej. |
    | Lista maszyn wirtualnych                   | Wprowadź listę oddzielnych przecinkami istniejących maszyn wirtualnych, które mają dołączyć do domeny zarządzanej usługi Azure AD DS, takiej jak *myVM1,myVM2*. |
    | Nazwa użytkownika dołączania do domeny     | Konto użytkownika w domenie zarządzanej usług Azure AD DS, które powinno być `contosoadmin@aaddscontoso.com`używane do przyłączenia maszyny Wirtualnej do domeny zarządzanej, takiej jak . To konto musi być częścią domeny zarządzanej usług Azure AD DS. |
    | Hasło użytkownika dołączania do domeny | Hasło dla konta użytkownika określone w poprzednim ustawieniu. |
    | Opcjonalna ścieżka OU          | Niestandardowa instalacja organizacyjna, w której można dodać maszynę wirtualną. Jeśli nie określisz wartości dla tego parametru, maszyna wirtualna zostanie dodana do domyślnej ou *dolegać komputerów kontrolera domeny usługi AAD.* |

1. Przejrzyj warunki, a następnie zaznacz **pole, w które zgadzam się z warunkami podanymi powyżej.** Gdy jest to gotowe, wybierz **pozycję Zakup,** aby dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS.

> [!WARNING]
> **Należy obchodzić się z hasłami ostrożnie.**
> Plik parametru szablonu żąda hasła dla konta użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS. Nie wprowadzaj ręcznie wartości do tego pliku i nie pozostawiaj go dostępnego w udziałach plików lub innych udostępnionych lokalizacjach.

Pomyślne zakończenie wdrożenia zajmuje kilka chwil. Po zakończeniu określone maszyny wirtualne systemu Windows są dołączane do domeny zarządzanej usług Azure AD DS i mogą być zarządzane lub zalogowane przy użyciu kont domeny.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto witryny Azure Portal do konfigurowania i wdrażania zasobów przy użyciu szablonów. Można również wdrożyć zasoby za pomocą szablonów Usługi Resource Manager przy użyciu [programu Azure PowerShell][deploy-powershell] lub [interfejsu wiersza polecenia platformy Azure.][deploy-cli]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
