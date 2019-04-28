---
title: Utwórz pulę hosta Windows wirtualnego pulpitu (wersja zapoznawcza) przy użyciu szablonu usługi Azure Resource Manager — platformy Azure
description: Jak utworzyć pulę hosta w wersji zapoznawczej pulpitu wirtualnego Windows przy użyciu szablonu usługi Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870561"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager

Pule hosta to zbiór przynajmniej jednej identycznych maszyn wirtualnych w środowiskach dzierżawy Windows wirtualnego Desktop w wersji zapoznawczej. Każda pula hosta może zawierać grupy aplikacji, które użytkownicy mogą korzystać z tak, jak w fizycznych pulpitu.

Wykonaj instrukcje w tej sekcji, aby utworzyć pulę hosta dla dzierżawy Windows pulpitu wirtualnego przy użyciu szablonu usługi Azure Resource Manager dostarczane przez firmę Microsoft. W tym artykule informacje dotyczące tworzenia puli hosta w Windows pulpitu wirtualnego, Utwórz grupę zasobów z maszynami wirtualnymi w subskrypcji platformy Azure, Dołącz do tych maszyn wirtualnych do domeny usługi AD i rejestrowanie maszyn wirtualnych w usłudze Windows pulpitu wirtualnego.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co jest potrzebne uruchomić szablon usługi Azure Resource Manager

Upewnij się, że wiesz, przed uruchomieniem szablonu usługi Azure Resource Manager następujących czynności:

- Gdzie jest źródło obrazu, którego chcesz użyć. Jest ona w galerii systemu Azure, czy jest niestandardowych?
- Twoje poświadczenia dołączania do domeny.
- Poświadczenia Windows pulpitu wirtualnego.

Po utworzeniu puli hosta Windows pulpitu wirtualnego przy użyciu szablonu usługi Azure Resource Manager można utworzyć maszynę wirtualną z poziomu galerii Azure, obrazu zarządzanego lub obrazu niezarządzanego. Aby dowiedzieć się więcej o sposobie tworzenia obrazów maszyn wirtualnych, zobacz [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) i [utworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Uruchom szablon usługi Azure Resource Manager do aprowizacji nowej puli hosta

Aby rozpocząć, przejdź do [ten adres URL usługi GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

Jeśli wdrażasz subskrypcji Enterprise, przewiń w dół i wybierz **Wdróż na platformie Azure**, następnie pomiń z wyprzedzeniem wypełnić parametry, na podstawie źródła obrazu.

Jeśli jest wdrażany w ramach subskrypcji Cloud Solution Provider, wykonaj następujące kroki, aby wdrożyć na platformie Azure:

1. Przewiń w dół i kliknij prawym przyciskiem myszy **Wdróż na platformie Azure**, a następnie wybierz **lokalizacja Linku kopiowania**.
2. Otwórz Edytor tekstów, takiego jak Notatnik i Wklej link istnieje.
3. Od razu po "https://portal.azure.com/" i przed hasztagiem (#) wprowadź znak (@) następuje nazwa domeny dzierżawy. Oto przykład formatu, należy użyć: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Zaloguj się do witryny Azure portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
5. Wklej łącze, które zostały skopiowane do edytora tekstu na pasku adresu.

Aby uzyskać wskazówki dotyczące parametrów, które należy wprowadzić dla danego scenariusza, zobacz pulpit wirtualny Windows [pliku Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Plik Readme zawsze jest aktualizowany z najnowszymi zmianami.

## <a name="assign-users-to-the-desktop-application-group"></a>Przypisywanie użytkowników do grupy aplikacji klasycznej

Po zakończeniu szablonu GitHub usługi Azure Resource Manager, należy przypisać dostęp użytkownika przed rozpoczęciem testowania pulpity sesji pełną na maszynach wirtualnych.

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Aby przypisać użytkowników do grupy aplikacji pulpitu, Otwórz okno programu PowerShell i uruchom to polecenie cmdlet, aby zarejestrować się w środowisku Windows pulpitu wirtualnego:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie ustaw kontekst do grupy dzierżawy określonych w szablonie usługi Azure Resource Manager za pomocą tego polecenia cmdlet:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Po tym należy dodać użytkowników do grupy aplikacji klasycznej przy użyciu tego polecenia cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Nazwa UPN użytkownika powinna być zgodna tożsamość użytkownika w usłudze Azure Active Directory (na przykład user1@contoso.com). Jeśli chcesz dodać wielu użytkowników, należy uruchomić to polecenie cmdlet dla każdego użytkownika.

Po wykonaniu tych czynności, dodano użytkowników do grupy aplikacji klasycznej można zalogować się do Windows pulpitu wirtualnego przy użyciu obsługiwanych klientów usług pulpitu zdalnego i zobacz zasób sesji komputerów stacjonarnych.

>[!IMPORTANT]
>Aby zabezpieczyć środowisku pulpitu wirtualnego Windows na platformie Azure, zalecamy nie otwieraj portu wejściowego 3389 na maszynach wirtualnych. Pulpit wirtualny Windows nie wymaga otwartego portu dla ruchu przychodzącego 3389 dla użytkowników puli hosta maszyn wirtualnych. Jeśli musisz otworzyć port 3389 na potrzeby rozwiązywania problemów, zalecamy użycie [dostęp do maszyny Wirtualnej just-in-time](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).