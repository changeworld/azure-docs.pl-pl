---
title: Tworzenie puli hostów programu Windows Virtual Desktop w wersji zapoznawczej przy użyciu portalu Azure Marketplace — Azure
description: Jak utworzyć pulę hostów dla systemu Windows Virtual Desktop w wersji zapoznawczej przy użyciu witryny Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 32628296c5d43a11371b486abc426da2e243d0a7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138225"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy systemu Windows Virtual Desktop w wersji zapoznawczej. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w pracę, tak jak na pulpicie fizycznym.

W tym samouczku opisano sposób tworzenia puli hostów w ramach dzierżawy pulpitu wirtualnego systemu Windows przy użyciu oferty Microsoft Azure Marketplace. Zadania obejmują:

> [!div class="checklist"]
> * Utwórz pulę hostów w programie Virtual Desktop systemu Windows.
> * Utwórz grupę zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure.
> * Dołącz maszyny wirtualne do domeny Active Directory.
> * Zarejestruj maszyny wirtualne za pomocą pulpitu wirtualnego systemu Windows.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Uruchamianie oferty portalu Azure Marketplace w celu aprowizacji nowej puli hostów

Aby uruchomić ofertę Azure Marketplace w celu aprowizacji nowej puli hostów:

1. Wybierz **+** lub **+ Utwórz zasób**.
2. W oknie wyszukiwania portalu Marketplace wprowadź **Windows Virtual Desktop** .
3. Wybierz pozycję **pulpit wirtualny systemu Windows — zapewnij pulę hostów**, a następnie wybierz pozycję **Utwórz**.

Postępuj zgodnie ze wskazówkami, aby wprowadzić informacje dla odpowiednich bloków.

### <a name="basics"></a>Ustawienia podstawowe

Oto co należy zrobić w przypadku bloku **podstawy** :

1. Wprowadź nazwę puli hostów, która jest unikatowa w ramach dzierżawy pulpitu wirtualnego systemu Windows.
2. Wybierz odpowiednią opcję dla pulpitu osobistego. Jeśli wybierzesz opcję **tak**, każdy użytkownik łączący się z tą pulą hostów zostanie trwale przypisany do maszyny wirtualnej.
3. Wprowadź rozdzieloną przecinkami listę użytkowników, którzy mogą logować się do klientów pulpitu wirtualnego systemu Windows i uzyskiwać dostęp do pulpitu po zakończeniu oferty portalu Azure Marketplace. Na przykład jeśli chcesz user1@contoso.com przypisać i user2@contoso.com uzyskać dostęp, wprowadź "user1@contoso.com,user2@contoso.com."
4. Wybierz pozycję **Utwórz nowy** i podaj nazwę nowej grupy zasobów.
5. W polu **Lokalizacja**wybierz tę samą lokalizację co sieć wirtualna, która ma łączność z serwerem Active Directory.
6. Kliknij przycisk **OK**.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

W bloku **Konfiguruj maszyny wirtualne** :

1. Zaakceptuj wartości domyślne lub Dostosuj liczbę i rozmiar maszyn wirtualnych.
2. Wprowadź prefiks nazw maszyn wirtualnych. Na przykład w przypadku wprowadzenia nazwy "prefiks" maszyny wirtualne będą nazywane "prefiksem-0", "prefiksem-1" i tak dalej.
3. Kliknij przycisk **OK**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

W bloku **ustawienia maszyny wirtualnej** :

>[!NOTE]
> W przypadku dołączania maszyn wirtualnych do środowiska Azure Active Directory Domain Services (Azure AD DS) Upewnij się, że użytkownik przyłączania do domeny jest również członkiem [grupy Administratorzy kontrolera domeny usługi AAD](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group).

1. W polu **Źródło obrazu**wybierz źródło i wprowadź odpowiednie informacje na temat sposobu ich znalezienia i sposobu ich przechowywania. W przypadku wybrania opcji nie używaj dysków zarządzanych wybierz konto magazynu zawierające plik VHD.
2. Wprowadź główną nazwę użytkownika i hasło konta domeny, które będzie przyłączać maszyny wirtualne do domeny Active Directory. Ta sama nazwa użytkownika i hasło zostaną utworzone na maszynach wirtualnych jako konto lokalne. Możesz zresetować te konta lokalne później.
3. Wybierz sieć wirtualną, która ma łączność z serwerem Active Directory, a następnie wybierz podsieć do hostowania maszyn wirtualnych.
4. Kliknij przycisk **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informacje o dzierżawie w wersji zapoznawczej pulpitu wirtualnego systemu Windows

W bloku **Informacje o dzierżawie pulpitu wirtualnego systemu Windows** :

1. W polu **Nazwa grupy dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę grupy dzierżawców zawierającej dzierżawcę. Pozostaw to ustawienie domyślne, chyba że podano konkretną nazwę grupy dzierżawców.
2. W polu **Nazwa dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę dzierżawy, w której chcesz utworzyć pulę hostów.
3. Określ typ poświadczeń, które mają być używane do uwierzytelniania jako właściciel usług pulpitu wirtualnego systemu Windows. Jeśli ukończono tworzenie jednostek [usługi i przypisań ról przy użyciu programu PowerShell](./create-service-principal-role-powershell.md), wybierz pozycję Nazwa **główna usługi**. Gdy zostanie wyświetlony **Identyfikator dzierżawy usługi Azure AD** , wprowadź identyfikator wystąpienia Azure Active Directory, które zawiera jednostkę usługi.
4. Wprowadź poświadczenia dla konta administratora dzierżawy. Obsługiwane są tylko jednostki usługi z poświadczeniami hasła.
5. Kliknij przycisk **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Ukończ instalację i Utwórz maszynę wirtualną

Dla ostatnich dwóch bloków:

1. W bloku **Podsumowanie** Przejrzyj informacje o instalacji. Jeśli musisz zmienić coś, Wróć do odpowiedniego bloku i wprowadź zmiany przed kontynuowaniem. Jeśli informacje wyglądają prawidłowo, wybierz przycisk **OK**.
2. W bloku **kupowanie** zapoznaj się z dodatkowymi informacjami na temat zakupów w portalu Azure Marketplace.
3. Wybierz pozycję **Utwórz** , aby wdrożyć pulę hostów.

W zależności od liczby tworzonych maszyn wirtualnych proces ten może potrwać 30 minut lub dłużej.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Obowiązkowe Przypisywanie dodatkowych użytkowników do grupy aplikacji klasycznych

Po zakończeniu oferty portalu Azure Marketplace można przypisać większą liczbę użytkowników do grupy aplikacji klasycznych przed rozpoczęciem testowania pełnych pulpitów sesji na maszynach wirtualnych. Jeśli dodano już domyślnych użytkowników do oferty portalu Azure Marketplace i nie chcesz dodawać więcej, możesz pominąć tę sekcję.

Aby przypisać użytkowników do grupy aplikacji klasycznych, należy najpierw otworzyć okno programu PowerShell. Następnie musisz wprowadzić następujące dwa polecenia cmdlet.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Dodaj użytkowników do grupy aplikacji klasycznych za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Nazwa UPN użytkownika powinna być zgodna z tożsamością użytkownika w Azure Active Directory (na przykład user1@contoso.com). Jeśli chcesz dodać wielu użytkowników, musisz uruchomić to polecenie cmdlet dla każdego użytkownika.

Po wykonaniu tych kroków użytkownicy dodani do grupy aplikacji klasycznych mogą zalogować się do pulpitu wirtualnego systemu Windows z obsługiwanymi Pulpit zdalny klientami i zobaczyć zasób pulpitu sesji.

Oto obecnie obsługiwani klienci:

- [Pulpit zdalny Client for Windows 7 i Windows 10](connect-windows-7-and-10.md)
- [Klient sieci Web pulpitu wirtualnego systemu Windows](connect-web.md)

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu puli hostów i przypisaniu użytkownikom dostępu do jej pulpitu, można wypełnić pulę hostów za pomocą programów RemoteApp. Aby dowiedzieć się więcej na temat zarządzania aplikacjami w programie Virtual Desktop systemu Windows, zobacz ten samouczek:

> [!div class="nextstepaction"]
> [Samouczek zarządzania grupami aplikacji](./manage-app-groups.md)
