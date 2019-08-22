---
title: Dostęp do maszyny wirtualnej just in Time w Azure Security Center | Microsoft Docs
description: W tym dokumencie pokazano, jak dostęp just in Time do maszyny wirtualnej w Azure Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: f3e6cc0464c8f395db7cac0ebf8a16230f5ebcbe
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872928"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Zarządzanie dostępem do maszyny wirtualnej przy użyciu funkcji just in Time

Dostęp do maszyn wirtualnych w czasie just-in-Time (JIT) może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, co pozwala ograniczyć narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.

> [!NOTE]
> Funkcja just in time jest dostępna w warstwie Standardowa Security Center.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.


> [!NOTE]
> Security Center dostęp do maszyny wirtualnej just in Time obsługuje obecnie tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. Aby dowiedzieć się więcej na temat modeli wdrażania klasycznego i Menedżer zasobów, zobacz [Azure Resource Manager a wdrożenie klasyczne](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Scenariusz ataku

Bezprawne ataki są często docelowymi portami zarządzania jako środek do uzyskania dostępu do maszyny wirtualnej. Jeśli to się powiedzie, osoba atakująca może przejąć kontrolę nad maszyną wirtualną i ustanowić przyczółka w swoim środowisku.

Jednym ze sposobów zmniejszenia narażenia na ataki z wykorzystaniem bezprawnego ataku jest ograniczenie czasu, przez który port jest otwarty. Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, np. aby wykonać zadania związane z zarządzaniem lub konserwacją. Gdy jest włączone just-in-Time, Security Center korzysta z zasad [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md#security-rules) (sieciowej grupy zabezpieczeń) i zapory platformy Azure, które ograniczają dostęp do portów zarządzania, więc nie mogą być objęte przez osoby atakujące.

![Scenariusz just in Time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak działa dostęp JIT?

Gdy jest włączone just-in-Time, Security Center blokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Wybierasz porty na maszynie wirtualnej, do której zostanie zablokowany ruch przychodzący. Te porty są kontrolowane przez rozwiązanie just-in-Time.

Gdy użytkownik zażąda dostępu do maszyny wirtualnej, Security Center sprawdza, czy użytkownik ma uprawnienia [Access Control (RBAC) oparte na rolach](../role-based-access-control/role-assignments-portal.md) , które pozwalają na pomyślne zażądanie dostępu do maszyny wirtualnej. Jeśli żądanie zostało zatwierdzone, Security Center automatycznie konfiguruje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i zaporę platformy Azure w celu zezwolenia na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów, przez określony czas. Po upływie tego czasu program Security Center Przywraca poprzedni stan sieciowych grup zabezpieczeń. Te połączenia, które są już ustanowione, nie są jednak przerywane.

 > [!NOTE]
 > Jeśli żądanie dostępu JIT zostanie zatwierdzone dla maszyny wirtualnej za zaporą platformy Azure, Security Center automatycznie zmieni reguły zasad sieciowej grupy zabezpieczeń i zapory. Przez określony czas, reguły zezwalają na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów. Po upływie tego czasu program Security Center przywraca poprzednie Stany zapory i reguły sieciowej grupy zabezpieczeń.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Uprawnienia wymagające konfigurowania i używania JIT

| Aby umożliwić użytkownikowi: | Uprawnienia do ustawienia|
| --- | --- |
| Konfigurowanie lub edytowanie zasad JIT dla maszyny wirtualnej | *Przypisz następujące akcje do roli:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|Zażądaj dostępu JIT do maszyny wirtualnej | *Przypisz następujące akcje do użytkownika:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>Konfigurowanie JIT na maszynie wirtualnej

Istnieją trzy sposoby konfigurowania zasad JIT na maszynie wirtualnej:

- [Konfigurowanie dostępu JIT w Azure Security Center](#jit-asc)
- [Konfigurowanie dostępu JIT w bloku maszyny wirtualnej platformy Azure](#jit-vm)
- [Programowe Konfigurowanie zasad JIT na maszynie wirtualnej](#jit-program)

## <a name="configure-jit-in-asc"></a>Konfigurowanie JIT w ASC

Z poziomu ASC można skonfigurować zasady JIT i zażądać dostępu do maszyny wirtualnej przy użyciu zasad JIT


### Konfigurowanie dostępu JIT na maszynie wirtualnej w języku ASC<a name="jit-asc"></a>

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

2. W lewym okienku wybierz pozycję **dostęp just in Time do maszyny wirtualnej**.

    ![Kafelek dostępu just in Time do maszyny wirtualnej](./media/security-center-just-in-time/just-in-time.png)

    Zostanie otwarte okno **dostęp just in Time do maszyny wirtualnej** .

      ![Włącz dostęp just in Time](./media/security-center-just-in-time/enable-just-in-time.png)

    **Dostęp just in Time do maszyny wirtualnej** zapewnia informacje o stanie maszyn wirtualnych:

    - **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu just in Time do maszyny wirtualnej. Przedstawione dane dotyczą ostatniego tygodnia i obejmują dla każdej maszyny wirtualnej liczbę zatwierdzonych żądań, datę ostatniego dostępu i godzinę ostatniego użytkownika.
    - **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyny wirtualnej just-in-Time, ale nie zostały skonfigurowane do programu. Zalecamy włączenie kontroli dostępu just in Time do maszyn wirtualnych. 
    - **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:
      - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in Time wymaga, aby sieciowej grupy zabezpieczeń.
      - Klasyczna maszyna wirtualna — Security Center dostęp do maszyny wirtualnej just in Time aktualnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. Wdrożenie klasyczne nie jest obsługiwane przez rozwiązanie just-in-Time. 
      - Inne — maszyna wirtualna jest w tej kategorii, jeśli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub jeśli maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciowej grupy zabezpieczeń.

3. Wybierz kartę **zalecane** .

4. W obszarze **maszyna wirtualna**kliknij maszyny wirtualne, które chcesz włączyć. Spowoduje to umieszczenie znacznika wyboru obok maszyny wirtualnej.

5. Kliknij pozycję **Włącz JIT na maszynach wirtualnych**.
   -. Ten blok przedstawia porty domyślne zalecane przez Azure Security Center:
      - 22 — SSH
      - 3389 - RDP
      - 5985 — WinRM 
      - 5986 — WinRM
6. Możesz również skonfigurować porty niestandardowe:

      1. Kliknij przycisk **Dodaj**. Zostanie otwarte okno **Dodaj konfigurację portu** .
      2. Dla każdego skonfigurowanego portu, zarówno domyślnego, jak i niestandardowego, można dostosować następujące ustawienia:

    - **Typ protokołu**— protokół, który jest dozwolony na tym porcie w przypadku zatwierdzenia żądania.
    - **Dozwolone źródłowe adresy IP**— zakresy adresów IP, które są dozwolone na tym porcie, gdy żądanie zostanie zatwierdzone.
    - **Maksymalny czas żądania**— maksymalny przedział czasu, w którym można otworzyć określony port.

     3. Kliknij przycisk **OK**.

1. Kliknij polecenie **Zapisz**.

> [!NOTE]
>Gdy dla maszyny wirtualnej jest włączony dostęp JIT dla maszyny wirtualnej, Azure Security Center tworzy reguły "Odmów wszystkim ruchem przychodzącym" dla wybranych portów w grupach zabezpieczeń sieci skojarzonych i z tą zaporą platformy Azure. Jeśli zostały utworzone inne reguły dla wybranych portów, istniejące reguły mają pierwszeństwo przed nowym regułą "odmowa całego ruchu przychodzącego". Jeśli nie ma żadnych istniejących reguł na wybranych portach, nowy reguły "Odrzuć cały ruch przychodzący" mają najwyższy priorytet w grupach zabezpieczeń sieci i zaporze platformy Azure.


## <a name="request-jit-access-via-asc"></a>Żądaj dostępu JIT za pośrednictwem ASC

Aby zażądać dostępu do maszyny wirtualnej za pomocą funkcji ASC:

1. W obszarze **dostęp just in Time do maszyny wirtualnej**wybierz kartę **skonfigurowane** .

2. W obszarze **maszyna wirtualna**kliknij maszyny wirtualne, dla których chcesz uzyskać dostęp. Spowoduje to umieszczenie znacznika wyboru obok maszyny wirtualnej.


    - Ikona w kolumnie **szczegóły połączenia** wskazuje, czy JIT jest włączona w sieciowej grupy zabezpieczeń czy PD. Jeśli ta opcja jest włączona, zostanie wyświetlona tylko ikona zapory.

    - Kolumna **szczegóły połączenia** zawiera poprawne informacje wymagane do połączenia z maszyną wirtualną, a także wskazuje otwarte porty.

      ![Żądanie dostępu just in time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Kliknij pozycję **Żądaj dostępu**. Zostanie otwarte okno **żądania dostępu** .

      ![Szczegóły JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. W obszarze **Żądaj dostępu**dla każdej maszyny wirtualnej Skonfiguruj porty, które chcesz otworzyć, oraz źródłowe adresy IP, na których jest otwierany port, oraz przedział czasu, w którym port zostanie otwarty. Możliwe będzie tylko żądanie dostępu do portów skonfigurowanych w zasadach just in Time. Każdy port ma maksymalny dozwolony czas uzyskany na podstawie zasad just in Time.

5. Kliknij pozycję **Otwórz porty**.

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu, znajduje się za serwerem proxy, opcja **My IP** może nie zadziałać. Może być konieczne zdefiniowanie pełnego zakresu adresów IP organizacji.

## <a name="edit-a-jit-access-policy-via-asc"></a>Edytowanie zasad dostępu JIT za pośrednictwem ASC

Istniejące zasady just in Time maszyny wirtualnej można zmienić, dodając i konfigurując nowy port do ochrony dla tej maszyny wirtualnej albo zmieniając inne ustawienia związane z już chronionym portem.

Aby edytować istniejące zasady just in Time maszyny wirtualnej:
1. Na karcie **Konfiguracja** w obszarze **maszyny wirtualne**wybierz maszynę wirtualną, do której chcesz dodać port, klikając trzy kropki w wierszu dla tej maszyny wirtualnej. 

1. Wybierz pozycję **Edit** (Edytuj).
1. W obszarze **Konfiguracja dostępu JIT do maszyny wirtualnej**można edytować istniejące ustawienia już chronionego portu lub dodać nowy port niestandardowy. 
  ![dostęp JIT do maszyny wirtualnej](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Inspekcja aktywności dostępu JIT w ASC

Szczegółowe informacje na temat działań maszyn wirtualnych można uzyskać przy użyciu funkcji przeszukiwania dzienników. Aby wyświetlić dzienniki:

1. W obszarze **dostęp do maszyny wirtualnej just-in-Time**wybierz kartę **skonfigurowane** .
2. W obszarze **maszyny wirtualne**wybierz maszynę wirtualną, aby wyświetlić informacje, klikając trzy kropki w wierszu dla tej maszyny wirtualnej, a następnie wybierz pozycję **Dziennik aktywności** w menu. Zostanie otwarty **Dziennik aktywności** .

   ![Wybierz Dziennik aktywności](./media/security-center-just-in-time/select-activity-log.png)

   **Dziennik aktywności** zawiera filtrowany widok poprzednich operacji dla tej maszyny wirtualnej oraz godzinę, datę i subskrypcję.

Informacje dziennika można pobrać, wybierając **pozycję kliknij tutaj, aby pobrać wszystkie elementy jako wolumin CSV**.

Zmodyfikuj filtry i kliknij przycisk **Zastosuj** , aby utworzyć wyszukiwanie i dziennik.



## Konfigurowanie dostępu JIT w bloku maszyny wirtualnej platformy Azure<a name="jit-vm"></a>

Dla wygody można nawiązać połączenie z maszyną wirtualną przy użyciu JIT bezpośrednio z poziomu bloku maszyny wirtualnej na platformie Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Konfigurowanie dostępu JIT na maszynie wirtualnej za pośrednictwem bloku maszyny wirtualnej platformy Azure

Aby ułatwić uzyskiwanie dostępu just in Time do maszyn wirtualnych, można ustawić maszynę wirtualną tak, aby zezwalała na dostęp tylko just in Time bezpośrednio z poziomu maszyny wirtualnej.

1. W Azure Portal wybierz pozycję **maszyny wirtualne**.
2. Kliknij maszynę wirtualną, którą chcesz ograniczyć do dostępu just in Time.
3. W menu kliknij pozycję **Konfiguracja**.
4. W obszarze **dostęp just in Time** kliknij pozycję **Włącz zasady just in Time**. 

Pozwala to na dostęp just in Time do maszyny wirtualnej przy użyciu następujących ustawień:

- Serwery z systemem Windows:
    - Port RDP 3389
    - 3 godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na wartość any
- Serwery z systemem Linux:
    - Port SSH 22
    - 3 godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na wartość any
     
Jeśli maszyna wirtualna ma już włączoną funkcję just-in-Time, po przejściu na stronę konfiguracji będzie można zobaczyć, że jest włączona funkcja just in Time. można także użyć linku, aby otworzyć zasady w Azure Security Center, aby wyświetlić i zmienić ustawienia.

![Konfiguracja JIT w maszynie wirtualnej](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Zażądaj dostępu JIT do maszyny wirtualnej za pośrednictwem bloku maszyny wirtualnej platformy Azure

W Azure Portal podczas próby nawiązania połączenia z maszyną wirtualną platforma Azure sprawdzi, czy dla tej maszyny wirtualnej skonfigurowano zasady dostępu just in Time.

- Jeśli na maszynie wirtualnej skonfigurowano zasady JIT, można kliknąć pozycję **Żądaj dostępu** , aby umożliwić dostęp zgodnie z zasadami JIT ustawionymi dla maszyny wirtualnej. 

  >![żądanie JIT](./media/security-center-just-in-time/jit-request.png)

  Wymagany jest dostęp z następującymi domyślnymi parametrami:

  - **źródłowy adres IP**: "Any" (*) (nie można zmienić)
  - **zakres czasu**: 3 godziny (nie można zmienić)  <!--Isn't this set in the policy-->
  - **numer portu** Port RDP 3389 dla Windows/port 22 dla systemu Linux (można go zmienić)

    > [!NOTE]
    > Po zatwierdzeniu żądania dla maszyny wirtualnej chronionej przez zaporę platformy Azure Security Center udostępnia użytkownikowi odpowiednie szczegóły połączenia (mapowanie portów z tabeli DNAT), które ma być używane do nawiązywania połączenia z maszyną wirtualną.

- Jeśli nie skonfigurowano JIT na maszynie wirtualnej, zostanie wyświetlony monit o skonfigurowanie zasad JIT.

  ![monit JIT](./media/security-center-just-in-time/jit-prompt.png)

## Programowe Konfigurowanie zasad JIT na maszynie wirtualnej<a name="jit-program"></a>

Można skonfigurować i używać just-in-Time za pośrednictwem interfejsów API REST i programu PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Dostęp JIT do maszyny wirtualnej za pośrednictwem interfejsów API REST

Funkcja dostępu just in Time do maszyny wirtualnej może być używana za pośrednictwem interfejsu API Azure Security Center. Możesz uzyskać informacje na temat skonfigurowanych maszyn wirtualnych, dodać nowe, zażądać dostępu do maszyny wirtualnej, a nie tylko za pośrednictwem tego interfejsu API. Zobacz [zasady dostępu do sieci JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), aby dowiedzieć się więcej na temat interfejsu API REST just-in-Time.

## <a name="jit-vm-access-via-powershell"></a>Dostęp JIT do maszyny wirtualnej za pośrednictwem programu PowerShell

Aby korzystać z rozwiązania dostępu just in Time do maszyny wirtualnej za pośrednictwem programu PowerShell, należy użyć oficjalnych poleceń cmdlet programu `Set-AzJitNetworkAccessPolicy`PowerShell Azure Security Center i w ten sposób.

Poniższy przykład ustawia zasady dostępu just-in-Time dla określonej maszyny wirtualnej i ustawia następujące elementy:

1.  Zamknij porty 22 i 3389.

2.  Dla każdego z nich ustaw maksymalne przedziały czasu (3 godziny), tak aby mogły być otwierane zgodnie z zatwierdzonymi żądaniami.
3.  Zezwala użytkownikowi, który żąda dostępu do kontrolowania źródłowych adresów IP i umożliwia użytkownikowi ustanowienie pomyślnej sesji na podstawie zatwierdzonego żądania dostępu just in Time.

Uruchom następujące polecenia w programie PowerShell, aby to zrobić:

1.  Przypisz zmienną, która zawiera zasady dostępu just in Time do maszyny wirtualnej:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Wstaw zasady dostępu just in Time do maszyny wirtualnej do tablicy:
    
        $JitPolicyArr=@($JitPolicy)

3.  Skonfiguruj zasady dostępu just in Time do maszyny wirtualnej na wybranej maszynie wirtualnej:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Żądanie dostępu do maszyny wirtualnej za pośrednictwem programu PowerShell

W poniższym przykładzie można zobaczyć żądanie dostępu just in Time do określonej maszyny wirtualnej, w której zażądano otwarcia portu 22 dla określonego adresu IP i przez określony czas:

Uruchom następujące polecenia w programie PowerShell:
1.  Konfigurowanie właściwości dostępu żądania maszyny wirtualnej

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Wstawianie parametrów żądania dostępu do maszyny wirtualnej w tablicy:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Wyślij żądanie dostępu (Użyj identyfikatora zasobu, który został uzyskany w kroku 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Aby uzyskać więcej informacji, zobacz dokumentację poleceń cmdlet programu PowerShell.

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak dostęp just in Time do maszyny wirtualnej w Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zarządzanie zaleceniami](security-center-recommendations.md) dotyczącymi zabezpieczeń — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
- [Zarządzanie alertami zabezpieczeń i reagowanie na](security-center-managing-and-responding-alerts.md) nie — informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
- [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Security Center często zadawane](security-center-faq.md) pytania — Znajdź często zadawane pytania dotyczące korzystania z usługi.
- [Blog Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

