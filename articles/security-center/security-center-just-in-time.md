---
title: Dostęp do maszyny wirtualnej just-in-time w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym dokumencie pokazano, jak dostęp do maszyn wirtualnych just-in-time w usłudze Azure Security Center ułatwia kontrolowanie dostępu do maszyn wirtualnych platformy Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 51985c5fa4b2296e43c0a062d0af84a1bb51e89c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397754"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpiecz swoje porty zarządzania dzięki dostępowi just-in-time

Jeśli korzystasz ze standardowej warstwy cenowej usługi Security Center (zobacz [cennik),](/azure/security-center/security-center-pricing)możesz zablokować ruch przychodzący do maszyn wirtualnych platformy Azure za pomocą dostępu do maszyny wirtualnej just-in-time (JIT). Zmniejsza to narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.

> [!NOTE]
> Dostęp do maszyn wirtualnych usługi Security Center just-in-time obsługuje obecnie tylko maszyny wirtualne wdrożone za pośrednictwem usługi Azure Resource Manager. Aby dowiedzieć się więcej o modelach wdrażania klasycznego i resource managera, zobacz [Usługa Azure Resource Manager a wdrożenie klasyczne](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurowanie JIT na maszynie wirtualnej

Istnieją trzy sposoby konfigurowania zasad JIT na maszynie Wirtualnej:

- [Konfigurowanie dostępu JIT w usłudze Azure Security Center](#jit-asc)
- [Konfigurowanie dostępu JIT na stronie maszyny Wirtualnej platformy Azure](#jit-vm)
- [Programowo konfigurowanie zasad JIT na maszynie wirtualnej](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurowanie JIT w usłudze Azure Security Center

W u centrum zabezpieczeń można skonfigurować zasady JIT i zażądać dostępu do maszyny Wirtualnej przy użyciu zasad JIT

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurowanie dostępu JIT na maszynie Wirtualnej w Centrum zabezpieczeń<a name="jit-asc"></a>

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

1. W lewym okienku wybierz pozycję **Dostęp do maszyny Wirtualnej just-in-time**.

    ![Kafelek dostępu do maszyny wirtualnej just-in-time](./media/security-center-just-in-time/just-in-time.png)

    Okno **dostępu do maszyny wirtualnej just-in-time** otwiera się i zawiera informacje o stanie maszyn wirtualnych:

    - **Skonfigurowano** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu do maszyn wirtualnych just-in-time. Przedstawione dane są dla ostatniego tygodnia i zawiera dla każdej maszyny Wirtualnej liczbę zatwierdzonych żądań, data i godzina ostatniego dostępu i ostatniego użytkownika.
    - **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyn wirtualnych tylko w czasie, ale nie zostały skonfigurowane. Zaleca się włączenie kontroli dostępu just-in-time VM dla tych maszyn wirtualnych.
    - **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:
      - Brak nsg - rozwiązanie just-in-time wymaga nsg być na miejscu.
      - Klasyczna maszyna wirtualna — usługa Security Center just-in-time VM access obecnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem usługi Azure Resource Manager. Klasyczne wdrożenie nie jest obsługiwane przez rozwiązanie just-in-time. 
      - Inne — maszyna wirtualna znajduje się w tej kategorii, jeśli rozwiązanie just-in-time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub jeśli maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciowej grupy zabezpieczeń.

1. Wybierz kartę **Zalecane.**

1. W **obszarze MASZYNA WIRTUALNA**kliknij maszyny wirtualne, które chcesz włączyć. Spowoduje to zaznaczenie wyboru obok maszyny Wirtualnej.

      ![Włącz dostęp just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Kliknij **pozycję Włącz JIT na maszynach wirtualnych**. Zostanie otwarte okienko wyświetlające domyślne porty zalecane przez usługę Azure Security Center:
    - 22 - SSH
    - 3389 - PROW
    - 5985 - WinRM 
    - 5986 - WinRM
1. Opcjonalnie można dodać porty niestandardowe do listy:

      1. Kliknij przycisk **Dodaj**. Zostanie otwarte okno **Dodaj konfigurację portu.**
      1. Dla każdego portu, który chcesz skonfigurować, zarówno domyślnego, jak i niestandardowego, można dostosować następujące ustawienia:
            - **Typ protokołu**— protokół dozwolony na tym porcie po zatwierdzeniu żądania.
            - **Dozwolone źródłowe adresy IP**— zakresy adresów IP, które są dozwolone na tym porcie po zatwierdzeniu żądania.
            - **Maksymalny czas żądania**— maksymalny przedział czasu, w którym można otworzyć określony port.

     1. Kliknij przycisk **OK**.

1. Kliknij przycisk **Zapisz**.

> [!NOTE]
>Gdy dostęp do maszyn wirtualnych JIT jest włączony dla maszyny Wirtualnej, Usługa Azure Security Center tworzy reguły "odmów całego ruchu przychodzącego" dla wybranych portów w skojarzonych grup zabezpieczeń sieciowych i z nią zaporą platformy Azure. Jeśli dla wybranych portów utworzono inne reguły, istniejące reguły mają pierwszeństwo przed nowymi regułami "odmów wszystkich ruchów przychodzących". Jeśli nie ma żadnych istniejących reguł dla wybranych portów, nowe reguły "odmów wszystkich ruchu przychodzącego" mają najwyższy priorytet w sieciowych grupach zabezpieczeń i zaporze platformy Azure.


## <a name="request-jit-access-via-security-center"></a>Poproś o dostęp do JIT za pośrednictwem Centrum zabezpieczeń

Aby zażądać dostępu do maszyny Wirtualnej za pośrednictwem centrum zabezpieczeń:

1. W obszarze **Dostęp do maszyny wirtualnej just-in-time**wybierz kartę **Skonfigurowano.**

1. W **obszarze Maszyna wirtualna**kliknij maszyny wirtualne, do których chcesz uzyskać dostęp. Spowoduje to zaznaczenie wyboru obok maszyny Wirtualnej.

    - Ikona w kolumnie **Szczegóły połączenia** wskazuje, czy JIT jest włączony w sieciowej sieciowej sieciowej lub fw. Jeśli jest włączona w obu, pojawi się tylko ikona zapory.

    - **Kolumna Szczegóły połączenia** zawiera informacje wymagane do podłączenia maszyny Wirtualnej i jej otwartych portów.

      ![Żądanie dostępu just in time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Kliknij **pozycję Poproś o dostęp**. Zostanie otwarte okno **Żądanie dostępu.**

      ![Szczegóły JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. W obszarze **Żądanie dostępu**dla każdej maszyny Wirtualnej skonfiguruj porty, które chcesz otworzyć, oraz źródłowe adresy IP, na których port jest otwarty, oraz okno czasu, dla którego port będzie otwarty. Będzie można zażądać dostępu tylko do portów, które są skonfigurowane w zasadach just-in-time. Każdy port ma maksymalny dozwolony czas wynikający z zasad just-in-time.

1. Kliknij **pozycję Otwórz porty**.

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu, znajduje się za serwerem proxy, opcja **Mój adres IP** może nie działać. Może być konieczne zdefiniowanie pełnego zakresu adresów IP organizacji.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Edytowanie zasad dostępu JIT za pośrednictwem Centrum zabezpieczeń

Można zmienić istniejące zasady just-in-time maszyny Wirtualnej, dodając i konfigurując nowy port w celu ochrony tej maszyny Wirtualnej lub zmieniając inne ustawienie związane z już chronionym portem.

Aby edytować istniejące zasady just-in-time maszyny Wirtualnej:

1. Na karcie **Skonfigurowano** w obszarze **Maszyny wirtualne**wybierz maszynę wirtualną, do której chcesz dodać port, klikając trzy kropki w wierszu dla tej maszyny Wirtualnej. 

1. Wybierz pozycję **Edit** (Edytuj).

1. W **konfiguracji dostępu JIT VM**można edytować istniejące ustawienia już chronionego portu lub dodać nowy port niestandardowy. 
  ![jit vm dostęp](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Inspekcja działania dostępu JIT w Centrum zabezpieczeń

Można uzyskać wgląd w działania maszyny Wirtualnej za pomocą wyszukiwania dziennika. Aby wyświetlić dzienniki:

1. W obszarze **Dostęp do maszyny wirtualnej just-in-time**wybierz kartę **Skonfigurowano.**
2. W obszarze **Maszyny wirtualne**wybierz maszynę wirtualną, aby wyświetlić informacje o tym, klikając trzy kropki w wierszu dla tej maszyny wirtualnej i wybierz z menu **dziennik aktywności.** Zostanie otwarty **dziennik aktywności.**

   ![Wybierz dziennik aktywności](./media/security-center-just-in-time/select-activity-log.png)

   **Dziennik aktywności** udostępnia filtrowany widok poprzednich operacji dla tej maszyny Wirtualnej wraz z godziną, datą i subskrypcją.

Możesz pobrać informacje dziennika, wybierając **Kliknij tutaj, aby pobrać wszystkie elementy jako CSV**.

Zmodyfikuj filtry i kliknij przycisk **Zastosuj,** aby utworzyć wyszukiwanie i dziennik.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurowanie dostępu JIT ze strony maszyny Wirtualnej platformy Azure<a name="jit-vm"></a>

Dla Twojej wygody można połączyć się z maszyną wirtualną przy użyciu JIT bezpośrednio ze strony maszyny Wirtualnej w Centrum zabezpieczeń.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurowanie dostępu JIT na maszynie wirtualnej za pośrednictwem strony maszyny Wirtualnej platformy Azure

Aby ułatwić wdrażanie dostępu just-in-time na maszynach wirtualnych, można ustawić maszynę wirtualną, aby zezwalała tylko na dostęp tylko w czasie bezpośrednio z poziomu maszyny Wirtualnej.

1. Z [witryny Azure portal](https://ms.portal.azure.com)wyszukuj i wybierz **opcję Maszyny wirtualne**. 
2. Wybierz maszynę wirtualną, którą chcesz ograniczyć do dostępu just-in-time.
3. W menu wybierz polecenie **Konfiguracja**.
4. W obszarze **Dostęp just-in-time**wybierz pozycję **Włącz just-in-time**. 

Umożliwia to dostęp just-in-time dla maszyny Wirtualnej przy użyciu następujących ustawień:

- Serwery systemu Windows:
    - Port RDP 3389
    - Trzy godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na
- Serwery Linuksa:
    - Port SSH 22
    - Trzy godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na
     
Jeśli maszyna wirtualna ma już włączoną funkcję just-in-time, po przejściem do jej strony konfiguracji będzie można zobaczyć, że just-in-time jest włączona i można użyć łącza, aby otworzyć zasady w usłudze Azure Security Center, aby wyświetlić i zmienić ustawienia.

![jit config w vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Żądanie dostępu JIT do maszyny Wirtualnej za pośrednictwem strony maszyny Wirtualnej platformy Azure

W witrynie Azure portal podczas próby nawiązania połączenia z maszyną wirtualną platforma Azure sprawdza, czy masz zasady dostępu just-in-time skonfigurowane na tej maszynie wirtualnej.

- Jeśli masz zasady JIT skonfigurowane na maszynie Wirtualnej, można kliknąć **przycisk Żądaj dostępu,** aby udzielić dostępu zgodnie z zestawem zasad JIT dla maszyny Wirtualnej. 

  >![jit wniosek](./media/security-center-just-in-time/jit-request.png)

  Dostęp jest wymagany z następującymi parametrami domyślnymi:

  - **źródłowy adres IP:**'Any' (*) (nie można zmienić)
  - **zakres czasu:** Trzy godziny (nie można zmienić) <!--Isn't this set in the policy-->
  - **numer portu** Port RDP 3389 dla Windows / port 22 dla Linuksa (można zmienić)

    > [!NOTE]
    > Po zatwierdzeniu żądania dla maszyny Wirtualnej chronionej przez zaporę platformy Azure, Usługa Security Center udostępnia użytkownikowi odpowiednie szczegóły połączenia (mapowanie portów z tabeli DNAT) do połączenia się z maszyną wirtualną.

- Jeśli nie masz JIT skonfigurowany na maszynie Wirtualnej, zostanie wyświetlony monit o skonfigurowanie zasad JIT na nim.

  ![jit monit](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Programowo konfigurowanie zasad JIT na maszynie wirtualnej<a name="jit-program"></a>

Interfejsy API REST i programu PowerShell można skonfigurować i używać tylko w czasie.

### <a name="jit-vm-access-via-rest-apis"></a>Dostęp do maszyny wirtualnej JIT za pośrednictwem interfejsów API REST

Funkcja dostępu do maszyny wirtualnej just-in-time może być używana za pośrednictwem interfejsu API usługi Azure Security Center. Za pośrednictwem tego interfejsu API można uzyskać informacje o skonfigurowanych maszynach wirtualnych, dodawać nowe, żądać dostępu do maszyny Wirtualnej i nie tylko. Aby dowiedzieć się więcej o interfejsie API REST just-in-time, zobacz [zasady dostępu do sieci Jit.](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)

### <a name="jit-vm-access-via-powershell"></a>Dostęp do maszyny Wirtualnej JIT za pośrednictwem programu PowerShell

Aby korzystać z rozwiązania dostępu do maszyn wirtualnych just-in-time za pośrednictwem programu PowerShell, `Set-AzJitNetworkAccessPolicy`należy użyć oficjalnych poleceń cmdlet programu Azure Security Center PowerShell, a w szczególności.

W poniższym przykładzie ustawia zasadę dostępu do maszyn wirtualnych just-in-time na określonej maszynie wirtualnej i ustawia następujące elementy:

1.    Zamknij porty 22 i 3389.

2.    Ustaw maksymalny przedział czasu 3 godziny dla każdego, aby można je było otworzyć na zatwierdzone żądanie.
3.    Umożliwia użytkownikowi, który żąda dostępu, kontrolowanie źródłowych adresów IP i umożliwia użytkownikowi ustanowienie pomyślnej sesji po zatwierdzeniu żądania dostępu just-in-time.

Aby wykonać ten problem, uruchom w programie PowerShell następujące czynności:

1.    Przypisywanie zmiennej, która przechowuje zasady dostępu do maszyn wirtualnych just-in-time dla maszyny Wirtualnej:

        $JitPolicy = (@{ id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" ports=(@{ number=22;        protocol="*";        allowedSourceAddressPrefix=@("*");        maxRequestAccessDuration="PT3H"}, @{ number=3389;        protocol="*";        allowedSourceAddressPrefix=@("*");        maxRequestAccessDuration="PT3H"}}})

2.    Wstawianie zasady dostępu maszyny Wirtualnej just-in-time do tablicy:
    
        $JitPolicyArr=@($JitPolicy)

3.    Skonfiguruj zasady dostępu do maszyn wirtualnych just-in-time na wybranej maszynie wirtualnej:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Żądanie dostępu do maszyny Wirtualnej za pośrednictwem programu PowerShell

W poniższym przykładzie można wyświetlić żądanie dostępu just-in-time do określonej maszyny Wirtualnej, w którym port 22 jest żądany do otwarcia dla określonego adresu IP i przez określony czas:

Uruchom następujące czynności w programie PowerShell:
1.    Konfigurowanie właściwości dostępu żądania maszyny Wirtualnej

        $JitPolicyVm1 = (@{ id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" ports=(@{ number=22;      endTimeUtc="2018-09-17T17:00:00.3658798Z";      allowedSourceAddressPrefix=@("IPV4ADDRESS")}})})
2.    Wstaw parametry żądania dostępu maszyny Wirtualnej do tablicy:

        $JitPolicyArr=@($JitPolicyVm1)
3.    Wyślij dostęp do żądania (użyj identyfikatora zasobu, który otrzymałeś w kroku 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Aby uzyskać więcej informacji, zobacz [dokumentację polecenia cmdlet programu PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatyczne oczyszczanie zbędnych reguł JIT 

Za każdym razem, gdy aktualizujesz zasady JIT, narzędzie oczyszczania jest uruchamiane automatycznie w celu sprawdzenia ważności całego zestawu reguł. Narzędzie wyszukuje niezgodności między regułami w zasadach i regułami sieciowej grupy bezpieczeństwa sieciowego. Jeśli narzędzie do oczyszczania znajdzie niezgodność, określa przyczynę, a gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są już potrzebne. Program czyszczący nigdy nie usuwa utworzonych reguł.

Przykłady scenariuszy, w których program czyszczący może usunąć wbudowaną regułę:

- Jeśli istnieją dwie reguły z identycznymi definicjami, a jedna ma wyższy priorytet niż druga (co oznacza, że reguła niższego priorytetu nigdy nie będzie używana)
- Gdy opis reguły zawiera nazwę maszyny Wirtualnej, która nie jest zgodna z docelowym adresem IP w regule 


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak dostęp do maszyn wirtualnych just-in-time w usłudze Security Center ułatwia kontrolowanie dostępu do maszyn wirtualnych platformy Azure.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- Moduł Microsoft Learn [Chroń swoje serwery i maszyny wirtualne przed atakami typu "brutalne i złośliwe oprogramowanie" za pomocą usługi Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.
- [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.