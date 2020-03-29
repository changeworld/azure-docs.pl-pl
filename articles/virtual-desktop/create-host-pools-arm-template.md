---
title: Usługa Azure Resource Manager — platforma Azure, pula hostów pulpitu wirtualnego systemu Windows — Azure
description: Jak utworzyć pulę hostów na pulpicie wirtualnym systemu Windows z szablonem Usługi Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292325"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w interakcje, tak jak na pulpicie fizycznym.

Postępuj zgodnie z instrukcjami tej sekcji, aby utworzyć pulę hostów dla dzierżawy pulpitu wirtualnego systemu Windows z szablonem usługi Azure Resource Manager dostarczonym przez firmę Microsoft. W tym artykule dowiesz się, jak utworzyć pulę hostów na pulpicie wirtualnym systemu Windows, utworzyć grupę zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure, dołączyć te maszyny wirtualne do domeny usługi AD i zarejestrować maszyny wirtualne na pulpicie wirtualnym systemu Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co jest potrzebne do uruchomienia szablonu usługi Azure Resource Manager

Przed uruchomieniem szablonu usługi Azure Resource Manager upewnij się, że znasz następujące czynności:

- Gdzie jest źródło obrazu, którego chcesz użyć. Czy jest z usługi Azure Gallery, czy jest to niestandardowe?
- Poświadczenia dołączania do domeny.
- Poświadczenia pulpitu wirtualnego systemu Windows.

Podczas tworzenia puli hostów pulpitu wirtualnego systemu Windows z szablonem Usługi Azure Resource Manager można utworzyć maszynę wirtualną z galerii platformy Azure, obrazu zarządzanego lub obrazu niezarządzanego. Aby dowiedzieć się więcej o tworzeniu obrazów maszyn wirtualnych, zobacz Przygotowywanie dysku [VHD lub VHDX systemu Windows do przekazania na platformę Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) i [tworzenie zarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Uruchamianie szablonu usługi Azure Resource Manager w celu inicjowania obsługi administracyjnej nowej puli hostów

Aby rozpocząć, przejdź do [tego adresu URL usługi GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

Jeśli wdrażasz w ramach subskrypcji enterprise, przewiń w dół i wybierz pozycję Wdrażanie na **platformie Azure**, a następnie pomiń z wyprzedzeniem wypełnij parametry oparte na źródle obrazu.

Jeśli wdrażasz w subskrypcji dostawcy rozwiązań w chmurze, wykonaj następujące kroki, aby wdrożyć na platformie Azure:

1. Przewiń w dół i kliknij prawym przyciskiem myszy **pozycję Wdrażanie na platformie Azure,** a następnie wybierz pozycję **Kopiuj lokalizację łącza**.
2. Otwórz edytor tekstu, taki jak Notatnik, i wklej tam link.
3. Zaraz pohttps://portal.azure.com/" " i przed hasztagiem (#) wprowadź znak (@), po którym następuje nazwa domeny dzierżawy. Oto przykład formatu, którego powinieneś `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`użyć: .
4. Zaloguj się do witryny Azure portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
5. Wklej skopiowane łącze do edytora tekstu na pasku adresu.

Aby uzyskać wskazówki dotyczące parametrów, które należy wprowadzić w scenariuszu, zobacz [plik Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)pulpitu wirtualnego systemu Windows . Readme jest zawsze aktualizowane o najnowsze zmiany.

## <a name="assign-users-to-the-desktop-application-group"></a>Przypisywanie użytkowników do grupy aplikacji klasycznych

Po zakończeniu szablonu Usługi Azure Resource Manager usługi GitHub przypisz dostęp do użytkownika przed rozpoczęciem testowania pełnych pulpitów sesji na maszynach wirtualnych.

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś.

Aby przypisać użytkowników do grupy aplikacji klasycznych, otwórz okno programu PowerShell i uruchom to polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie dodaj użytkowników do grupy aplikacji klasycznych za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Nazwa UPN użytkownika powinna być zgodna z tożsamością użytkownika w user1@contoso.comusłudze Azure Active Directory (na przykład). Jeśli chcesz dodać wielu użytkowników, musisz uruchomić to polecenie cmdlet dla każdego użytkownika.

Po wykonaniu tych kroków użytkownicy dodani do grupy aplikacji pulpitu mogą logować się do pulpitu wirtualnego systemu Windows za pomocą obsługiwanych klientów pulpitu zdalnego i wyświetlać zasób pulpitu sesji.

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389, aby użytkownicy mogli uzyskiwać dostęp do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu do maszyn wirtualnych just-in-time](../security-center/security-center-just-in-time.md).