---
title: 'Konfigurowanie uwierzytelniania usługi Azure AD dla połączenia sieci VPN użytkownika: wirtualna sieć WAN'
description: Dowiedz się, jak skonfigurować uwierzytelnianie usługi Azure Active Directory dla sieci VPN użytkownika.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059486"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurowanie uwierzytelniania usługi Azure Active Directory dla sieci VPN użytkowników

W tym artykule pokazano, jak skonfigurować uwierzytelnianie usługi Azure AD dla sieci VPN użytkownika w wirtualnej sieci WAN, aby połączyć się z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN OpenVPN. Uwierzytelnianie usługi Azure Active Directory jest dostępne tylko dla bram korzystających z protokołu OpenVPN i klientów z systemem Windows.

Ten typ połączenia wymaga skonfigurowania klienta na komputerze klienckim. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie koncentratora
> * Tworzenie konfiguracji P2S
> * Pobieranie profilu klienta sieci VPN
> * Stosowanie konfiguracji P2S do koncentratora
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji klienta sieci VPN
> * Wyświetlanie wirtualnej sieci WAN

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Masz sieć wirtualną, z którą chcesz się połączyć. Sprawdź, czy żadna z podsieci sieci lokalnych nie pokrywa się z sieciami wirtualnymi, z którymi chcesz się połączyć. Aby utworzyć sieć wirtualną w witrynie Azure portal, zobacz [przewodnik Szybki start](../virtual-network/quick-create-portal.md).

* Sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (sieć VPN lub usługę ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga połączenia sieci wirtualnych z bramą koncentratora wirtualnej sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla koncentratora nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi nawiązujesz połączenie. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, należy koordynować z kimś, kto może podać te szczegóły dla Ciebie.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do strony Wirtualna sieć WAN. W portalu kliknij pozycję **+Utwórz zasób**. Wpisz **wirtualną sieć WAN** w polu wyszukiwania i wybierz pozycję Enter.
2. Wybierz **wirtualną sieć WAN** z wyników. Na stronie Wirtualna sieć WAN kliknij pozycję **Utwórz,** aby otworzyć stronę Utwórz sieć WAN.
3. Na stronie **Tworzenie wan** na karcie **Podstawy** wypełnij następujące pola:

   ![Wirtualna sieć WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów** — tworzenie nowych lub używanie istniejących.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Name** - Wpisz nazwę, którą chcesz wywołać w wan.
   * **Typ:** Standardowych. Jeśli utworzysz podstawową sieć WAN, można utworzyć tylko centrum podstawowe. Podstawowe koncentratory mogą ować tylko łączność między lokacjami sieci VPN.
4. Po zakończeniu wypełniania pól wybierz pozycję **Przejrzyj +Utwórz**.
5. Po przejściu weryfikacji wybierz pozycję **Utwórz,** aby utworzyć wirtualną sieć WAN.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Tworzenie pustego centrum wirtualnego

1. W wirtualnej sieci WAN wybierz pozycję Koncentratory i kliknij **+Nowe centrum**.

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na stronie Tworzenie centrum wirtualnego wypełnij następujące pola.

   **Region** — wybierz region, w którego chcesz wdrożyć centrum wirtualne.

   **Nazwa** — wprowadź nazwę, którą chcesz wywołać centrum wirtualnego.

   **Prywatna przestrzeń adresowa koncentratora** — zakres adresów koncentratora w notacji CIDR.

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Kliknij pozycję **Przegląd + utwórz**.
4. Na stronie **weryfikacji przekazanej** kliknij przycisk **Utwórz**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Tworzenie nowej konfiguracji P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. W wirtualnej sieci WAN wybierz **pozycję Konfiguracje sieci VPN użytkownika**.

   ![nowy config](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. kliknij **+Utwórz konfigur sieci VPN użytkownika**.

   ![nowy config](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Wprowadź informacje i kliknij przycisk **Utwórz**

   ![nowy config](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Edytowanie przypisania koncentratora

1. Przejdź do **centrum** bloku w wirtualnej sieci WAN.
2. Wybierz koncentrator, z którego chcesz skojarzyć konfigurację serwera sieci VPN, i kliknij wielokropek (...).

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Kliknij **pozycję Edytuj koncentrator wirtualny**.
4. Zaznacz pole wyboru **Dołącz bramę typu punkt-lokacja** i wybierz **odpowiednią jednostkę skalowania bramy.**

   ![nowa lokacja](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Wprowadź **pulę adresów,** z której klientom sieci VPN będą przypisywane adresy IP.
6. Kliknij pozycję **Potwierdź**.
7. Operacja może potrwać do 30 minut.

## <a name="download-vpn-profile"></a><a name="device"></a>Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **Konfiguracje sieci VPN użytkownika**.
2. U góry strony kliknij pozycję **Pobierz konfigur sieci VPN użytkownika**.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

## <a name="configure-user-vpn-clients"></a>Konfigurowanie klientów sieci VPN użytkowników

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i zaimportować profil klienta sieci VPN, który został pobrany w poprzednich krokach na każdym komputerze, który chce połączyć się z siecią wirtualną.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest&reg; obsługiwane tylko dla połączeń protokołu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [łącza,](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) aby pobrać klienta sieci VPN platformy Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Przejdź do pliku xml profilu i wybierz go. Po wybraniu wybranego pliku wybierz pozycję **Otwórz**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Wybierz **pozycję Połącz,** aby połączyć się z siecią VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Po podłączeniu ikona zmieni kolor na zielony i na **powiedzmię Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek (...) obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Wybierz **pozycję Usuń,** aby usunąć.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnozowanie problemów z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia **Diagnozowanie.** Wybierz wielokropek (...) obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz **polecenie Diagnozuj**.

    ![Zdiagnozować](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Zdiagnozować](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu poświadczeń.

    ![Zdiagnozować](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnozy.

    ![Zdiagnozować](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
