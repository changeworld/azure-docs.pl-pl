---
title: Pula hostów pulpitu wirtualnego systemu Windows w portalu Azure Marketplace — platforma Azure
description: Jak utworzyć pulę hostów pulpitu wirtualnego systemu Windows przy użyciu portalu Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238622"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Samouczek: Tworzenie puli hostów przy użyciu portalu Azure Marketplace

W tym samouczku dowiesz się, jak utworzyć pulę hostów w dzierżawie pulpitu wirtualnego systemu Windows przy użyciu oferty portalu Microsoft Azure Marketplace.

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w interakcje, tak jak na pulpicie fizycznym.

Zadania w tym samouczku obejmują:

> [!div class="checklist"]
>
> * Utwórz pulę hostów na pulpicie wirtualnym systemu Windows.
> * Tworzenie grupy zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure.
> * Dołącz maszyny wirtualne do domeny usługi Active Directory.
> * Zarejestruj maszyny wirtualne na pulpicie wirtualnym systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

* Dzierżawa na pulpicie wirtualnym. Poprzedni [samouczek](tenant-setup-azure-active-directory.md) tworzy dzierżawę.
* [Moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/).

Po uzyskaniu tego modułu uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Uruchamianie oferty portalu Azure Marketplace w celu aprowizowania nowej puli hostów

Aby uruchomić ofertę portalu Azure Marketplace w celu aprowizowania nowej puli hostów:

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wprowadź **pulpit wirtualny systemu Windows** w oknie wyszukiwania w portalu Marketplace.
1. Wybierz pozycję **Pulpit wirtualny systemu Windows — aprowizuj pulę hostów,** a następnie wybierz pozycję **Utwórz**.

Następnie postępuj zgodnie z instrukcjami w następnej sekcji, aby wprowadzić informacje dotyczące odpowiednich kart.

### <a name="basics"></a>Podstawy

Oto, co robisz na karcie **Podstawy:**

1. Wybierz **subskrypcję**.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy** i podaj nazwę nowej grupy zasobów.
1. Wybierz **region**.
1. Wprowadź nazwę puli hostów, która jest unikatowa w dzierżawie pulpitu wirtualnego systemu Windows.
1. Wybierz **typ pulpitu**. W przypadku **wybrania opcji Osobiste,** każdy użytkownik łączący się z tą pulą hostów jest trwale przypisany do maszyny wirtualnej.
1. Wprowadź użytkowników, którzy mogą logować się do klientów pulpitu wirtualnego systemu Windows i uzyskiwać dostęp do pulpitu. Użyj listy oddzielonej przecinkami. Na przykład, jeśli chcesz `user1@contoso.com` `user2@contoso.com` przypisać i uzyskać dostęp, wprowadź*`user1@contoso.com,user2@contoso.com`*
1. W przypadku **lokalizacji metadanych usługi**wybierz tę samą lokalizację co sieć wirtualna, która ma łączność z serwerem usługi Active Directory.

   >[!IMPORTANT]
   >Jeśli używasz czystego rozwiązania usługi domenowej Active Directory Azure (Usługi Usługi Azure AD DS) i usługi Azure Active Directory (Azure AD), należy wdrożyć pulę hostów w tym samym regionie co usługi Azure AD DS, aby uniknąć błędów sprzężenia domeny i poświadczeń.

1. Wybierz **dalej: Konfigurowanie maszyn wirtualnych**.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

Dla karty **Konfigurowanie maszyn wirtualnych:**

1. Zaakceptuj ustawienia domyślne lub dostosuj liczbę i rozmiar maszyn wirtualnych.

    >[!NOTE]
    >Jeśli określony rozmiar maszyny wirtualnej, którego szukasz, nie jest wyświetlany w selektorze rozmiaru, to dlatego, że nie przywdzieliliśmy go jeszcze do narzędzia portalu Azure Marketplace. Aby zażądać rozmiaru, utwórz żądanie lub prześlij żądanie na [forum użytkownika pulpitu wirtualnego systemu Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Wprowadź prefiks nazw maszyn wirtualnych. Na przykład po wprowadzeniu *prefiksu*maszyny wirtualne będą nazywane **prefiksem-0,** **prefiksem-1**itd.
1. Wybierz **dalej: Ustawienia maszyny wirtualnej**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

Na karcie **Ustawienia maszyny wirtualnej:**

1. W przypadku **źródła obrazu**wybierz źródło i wprowadź odpowiednie informacje dotyczące znajdowania go i przechowywania. Opcje różnią się w przypadku magazynu obiektów Blob, obrazu zarządzanego i galerii.

   Jeśli nie chcesz używać dysków zarządzanych, wybierz konto magazynu, które zawiera plik *vhd.*
1. Wprowadź nazwę główną użytkownika i hasło. To konto musi być kontem domeny, które dołączy maszyny wirtualne do domeny usługi Active Directory. Ta sama nazwa użytkownika i hasło zostaną utworzone na maszynach wirtualnych jako konto lokalne. Możesz zresetować te konta lokalne później.

   >[!NOTE]
   > Jeśli łączysz maszyny wirtualne ze środowiskiem usług Azure AD DS, upewnij się, że użytkownik dołączania do domeny jest członkiem [grupy Administratorzy kontrolera domeny usługi AAD.](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)
   >
   > Konto musi również być częścią domeny zarządzanej usług Azure AD DS lub dzierżawy usługi Azure AD. Konta z katalogów zewnętrznych skojarzonych z dzierżawą usługi Azure AD nie mogą poprawnie uwierzytelniać się podczas procesu dołączania do domeny.

1. Wybierz **sieć wirtualną,** która ma łączność z serwerem usługi Active Directory, a następnie wybierz podsieć do obsługi maszyn wirtualnych.
1. Wybierz **dalej: Informacje o pulpicie wirtualnym systemu Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informacje o dzierżawie pulpitu wirtualnego systemu Windows

Karta **Informacje o dzierżawie pulpitu wirtualnego systemu Windows:**

1. W przypadku **nazwy grupy dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę grupy dzierżawy zawierającej dzierżawę. Pozostaw go jako wartość domyślną, chyba że podano określoną nazwę grupy dzierżawy.
1. W przypadku **nazwy dzierżawy pulpitu wirtualnego systemu Windows**wprowadź nazwę dzierżawy, w której będzie ona tworzyć tę pulę hosta.
1. Określ typ poświadczeń, których chcesz użyć do uwierzytelnienia jako właściciela usług RDS dzierżawy pulpitu wirtualnego systemu Windows. Wprowadź jednostkę upn lub service i hasło.

   Jeśli wykonane [tworzenie podmiotów usługi i przypisań ról za pomocą samouczka programu PowerShell](./create-service-principal-role-powershell.md), wybierz pozycję **Usługodawca jednostkowy**.

1. W przypadku **jednostki usługi**— dla **identyfikatora dzierżawy usługi Azure AD**wprowadź konto administratora dzierżawy dla wystąpienia usługi Azure AD, które zawiera jednostkę usługi. Obsługiwane są tylko jednostki usługi z poświadczeniem hasła.
1. Wybierz **dalej: Przejrzyj + utwórz**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Zakończ konfigurację i utwórz maszynę wirtualną

W **przeglądzie i tworzeniu**przejrzyj informacje o konfiguracji. Jeśli chcesz coś zmienić, wróć i wprowadzać zmiany. Gdy będziesz gotowy, wybierz pozycję **Utwórz,** aby wdrożyć pulę hostów.

W zależności od liczby maszyn wirtualnych, które tworzysz, ten proces może potrwać co najmniej 30 minut.

>[!IMPORTANT]
> Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389, aby użytkownicy mogli uzyskiwać dostęp do maszyn wirtualnych puli hostów.
>
> Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie dostępu just-in-time. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie portów zarządzania z dostępem just-in-time](../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcjonalnie) Przypisywanie dodatkowych użytkowników do grupy aplikacji klasycznych

Po zakończeniu tworzenia puli w portalu Azure Marketplace można przypisać więcej użytkowników do grupy aplikacji klasycznych. Jeśli nie chcesz dodawać więcej, pomiń tę sekcję.

Aby przypisać użytkowników do grupy aplikacji klasycznych:

1. Otwórz okno programu PowerShell.

1. Uruchom następujące polecenie, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Dodaj użytkowników do grupy aplikacji klasycznych za pomocą tego polecenia:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Nazwa UPN użytkownika powinna być zgodna z tożsamością użytkownika *user1@contoso.com*w usłudze Azure AD, na przykład . Jeśli chcesz dodać wielu użytkowników, uruchom polecenie dla każdego użytkownika.

Użytkownicy dodani do grupy aplikacji klasycznych mogą logować się do pulpitu wirtualnego systemu Windows za pomocą obsługiwanych klientów pulpitu zdalnego i wyświetlać zasób pulpitu sesji.

Oto bieżące obsługiwane klienty:

* [Klient pulpitu zdalnego dla systemów Windows 7 i Windows 10](connect-windows-7-and-10.md)
* [Klient sieci Web pulpitu wirtualnego systemu Windows](connect-web.md)

## <a name="next-steps"></a>Następne kroki

Masz już pulę hostów i przypisano użytkownikom dostęp do jego pulpitu. Możesz wypełnić pulę hosta za pomocą programów RemoteApp. Aby dowiedzieć się więcej o zarządzaniu aplikacjami na pulpicie wirtualnym systemu Windows, zobacz ten samouczek:

> [!div class="nextstepaction"]
> [Samouczek Zarządzanie grupami aplikacji](./manage-app-groups.md)
