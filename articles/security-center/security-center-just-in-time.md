---
title: Dostęp just in time maszyny wirtualnej w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono kontrolować dostęp do maszyny Wirtualnej jak just-in-time w usłudze Azure Security Center ułatwia dostęp do usługi Azure virtual machines.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: monhaber
ms.openlocfilehash: 66a7171aff7b9bab5f320df1d71e9cab4ce0477d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758300"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Zarządzanie dostępem maszyny wirtualnej przy użyciu just-in-time

Just-in-time (JIT) maszyny wirtualnej (VM) dostępu może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, zmniejszenia narażenia na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie.

> [!NOTE]
> Funkcja just in time jest dostępne w warstwie standardowa usługi Security Center.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Ataku

Atak siłowy ataki często porty zarządzania docelowe w celu uzyskania dostępu do maszyny Wirtualnej. W przypadku powodzenia osoba atakująca może przejąć kontrolę nad maszyną Wirtualną i ustanowić przyczółka w środowisku.

Jest jednym ze sposobów, aby zmniejszyć narażenie na ataki siłowe Aby ograniczyć ilość czasu, który port jest otwarty. Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, np. aby wykonać zadania związane z zarządzaniem lub konserwacją. Gdy just in time jest włączone, usługa Security Center używa [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules) (reguły sieciowej grupy zabezpieczeń), które ograniczają dostęp do portów zarządzania, dlatego nie może być obiektem ataków.

![Scenariusz just-in-time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak działa dostęp JIT

Gdy just in time jest włączone, usługa Security Center zablokuje ruch przychodzący do maszyn wirtualnych platformy Azure, tworząc regułę sieciowej grupy zabezpieczeń. Należy wybrać porty na maszynie Wirtualnej, do którego zostanie zablokowany ruch przychodzący. Te porty są kontrolowane przez to rozwiązanie just in time.

Gdy użytkownik poprosi o dostęp do maszyny Wirtualnej, Centrum zabezpieczeń sprawdza, czy użytkownik ma [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) uprawnienia, które umożliwia pomyślnie poprosić o dostęp do maszyny Wirtualnej. Jeśli żądanie zostanie zatwierdzone, usługa Security Center automatycznie skonfiguruje sieciowe grupy zabezpieczeń (NSG), aby zezwolić na ruch przychodzący do wybranych portów i żądane źródłowe adresy IP lub zakresów ilości czasu, który został określony. Po upływie czasu, usługa Security Center przywraca sieciowych grup zabezpieczeń do poprzedniego stanu. Tych połączeń, które już istnieją są nie są przerywane, jednak.

> [!NOTE]
> Usługa Security Center just-in-time w maszynie Wirtualnej dostęp do aktualnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem usługi Azure Resource Manager. Aby dowiedzieć się więcej o klasyczny, jak i modelem wdrażania usługi Resource Manager, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Aby uzyskać dostęp JIT, za pomocą:
- [Przy użyciu dostępu JIT w usłudze Azure Security Center](#jit-asc)
- [W bloku maszyny Wirtualnej platformy Azure przy użyciu dostępu JIT do](#jit-vm)

## Przy użyciu dostępu JIT w usłudze Azure Security Center <a name="jit-asc"></a>

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

2. W okienku po lewej stronie wybierz **Just-in-time dostęp do maszyny Wirtualnej**.

![Kafelek dostęp do maszyny Wirtualnej just-in-time](./media/security-center-just-in-time/just-in-time.png)

**Just-in-time dostęp do maszyny Wirtualnej** zostanie otwarte okno.

![Kafelek dostęp do maszyny Wirtualnej just-in-time](./media/security-center-just-in-time/just-in-time-access.png)

**Dostęp do maszyny Wirtualnej just-in-time** zawiera informacje o stanie maszyn wirtualnych:

- **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu do maszyny Wirtualnej just-in-time. Dane prezentowane jest ostatniego tygodnia i liczbie dla każdej maszyny Wirtualnej zatwierdzonych wniosków, Data ostatniego dostępu i godziny oraz ostatniego użytkownika.
- **Zaleca się** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyny Wirtualnej w czasie, ale nie został skonfigurowany do. Firma Microsoft zaleca, włączyć kontrolę dostępu maszyny Wirtualnej just-in-time dla tych maszyn wirtualnych. Zobacz [skonfigurowaniem zasad dostępu just in time](#jit-config).
- **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:
  - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in time wymaga sieciowej grupy zabezpieczeń w miejscu.
  - Klasyczna maszyna wirtualna — dostęp do maszyny Wirtualnej just-in-time w usłudze Security Center obecnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem usługi Azure Resource Manager. Wdrożenie klasyczne nie jest obsługiwana przez rozwiązanie just in time.
  - Inne — maszyna wirtualna jest w tej kategorii, jeżeli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub że maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciową grupę zabezpieczeń w miejscu.

### Konfigurowanie zasad dostępu JIT<a name="jit-config"></a>

Aby wybrać maszyny wirtualne, które chcesz włączyć:

1. W obszarze **Just-in-time dostęp do maszyny Wirtualnej**, wybierz opcję **zalecane** kartę.

   ![Włącz dostęp just in time](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. W obszarze **maszyny WIRTUALNEJ**, wybierz maszyny wirtualne, które chcesz włączyć. Spowoduje to umieszczenie znacznik wyboru obok maszyny Wirtualnej.
3. Wybierz **Włącz dostęp JIT na maszynach wirtualnych**.
   1. Ten blok zawiera porty domyślne zalecane przez usługę Azure Security Center:
      - 22 - PROTOKOŁU SSH
      - 3389 - RDP
      - 5985 - Usługa WinRM 
      - 5986 - Usługa WinRM
   2. Można również skonfigurować porty niestandardowe. Aby to zrobić, wybierz **Dodaj**. 
   3. W **Dodawanie konfiguracji portu**, dla każdego portu, należy wybrać opcję skonfigurowania zarówno domyślne i niestandardowe, możesz dostosować następujące ustawienia:
      - **Typ protokołu**— protokół, który jest dozwolone na tym porcie, gdy żądanie zostanie zatwierdzone.
      - **Dozwolone źródłowe adresy IP**-zakresów IP, które są dozwolone dla tego portu, gdy żądanie zostanie zatwierdzone.
      - **Maksymalny czas żądania**— okno maksymalny czas, w którym można otworzyć określonego portu.

4. Wybierz pozycję **Zapisz**.


> [!NOTE]
>Po włączeniu dostępu do maszyny Wirtualnej JIT dla maszyny Wirtualnej usługi Azure Security Center tworzy "odmowa cały ruch przychodzący" dla wybranych portów w grupach zabezpieczeń sieci skojarzonych z nim. Jeśli inne reguły została utworzona na wybranych portach, następnie istniejące reguły pierwszeństwo przed nowe zasady "odmowa cały ruch przychodzący". Jeśli nie istnieją żadne istniejące reguły na wybranych portach, nowe zasady "odmowa cały ruch przychodzący" zabrać najwyższy priorytet w grupach zabezpieczeń sieci.
>

### <a name="request-jit-access-to-a-vm"></a>Żądanie dostępu JIT do maszyny Wirtualnej

Aby poprosić o dostęp do maszyny Wirtualnej:
1.  W obszarze **Just in dostęp maszyny Wirtualnej w czasie**, wybierz opcję **skonfigurowane**.
2.  W obszarze **maszyn wirtualnych**, sprawdź maszyn wirtualnych, które chcesz włączyć dostęp just in time dla.
3.  Wybierz **żądania dostępu**. 
  ![Poproś o dostęp do maszyny wirtualnej](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  W obszarze **żądania dostępu**źródłowych adresów IP, które port jest otwarty na i przedział czasu, dla którego numer portu będzie otwierać i dla każdej maszyny Wirtualnej, skonfiguruj porty, które chcesz otworzyć. Tylko będzie to możliwe zażądać dostępu do portów, które są skonfigurowane w zasadach just-in-time. Każdy port ma maksymalny czas dozwolony pochodną zasad just-in-time.
5.  Wybierz **otworzyć porty**.

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu jest za serwerem proxy, opcja **Mój adres IP** może nie działać. Może być konieczne zdefiniowanie pełny zakres adresów IP w organizacji.

### <a name="editing-a-jit-access-policy"></a>Edytowanie zasad dostępu JIT

Możesz zmienić istniejące zasady just-in-time maszyny Wirtualnej przez dodanie i skonfigurowanie nowego portu do ochrony dla tej maszyny Wirtualnej lub zmieniając wszelkie inne ustawienia związane z portem już chronione.

Aby edytować istniejące zasady just-in-time maszyny wirtualnej:
1. W **skonfigurowane** , w obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną, dla której chcesz dodać port, klikając trzy kropki znajdujące się w wierszu dla tej maszyny Wirtualnej. 
2. Wybierz pozycję **Edit** (Edytuj).
3. W obszarze **Konfiguracja dostępu do maszyn wirtualnych JIT**, możesz edytować istniejące ustawienia portu już chronione lub dodać nowy port niestandardowy. Aby uzyskać więcej informacji, zobacz [skonfigurowaniem zasad dostępu just in time](#jit-config). 
  ![dostęp do maszyn wirtualnych JIT](./media/security-center-just-in-time/edit-policy.png)

## W bloku maszyny Wirtualnej platformy Azure przy użyciu dostępu JIT do <a name="jit-vm"></a>

Dla Twojej wygody możesz połączyć z maszyną wirtualną przy użyciu JIT bezpośrednio z w ramach bloku maszyny Wirtualnej na platformie Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Konfigurowanie zasad dostępu just in time 
Ułatwia to dystrybucję dostępu just in time dla maszyn wirtualnych, można ustawić maszynę Wirtualną, aby zezwolić na dostęp tylko just in time do bezpośrednio z poziomu maszyny Wirtualnej.

1. W witrynie Azure portal wybierz **maszyn wirtualnych**.
2. Kliknij maszynę wirtualną, którą chcesz ograniczyć dostęp just in time do.
3. W menu, kliknij **konfiguracji**.
4. W obszarze **tylko w czasie dostępu** kliknij **Włącz zasady just-in-time**. 

Dzięki temu dostęp just in time do maszyny wirtualnej, używając następujących ustawień:

- Serwery Windows:
    - Port 3389 protokołu RDP
    - 3 godzin maksymalny dozwolony dostęp
    - Dozwolone źródłowe adresy IP jest ustawiony do dowolnej
- Serwery z systemem Linux:
    - Port 22 protokołu SSH
    - 3 godzin maksymalny dozwolony dostęp
    - Dozwolone źródłowe adresy IP jest ustawiony do dowolnej
     
Jeśli maszyna wirtualna ma już just-in-time włączone, po przejściu do strony konfiguracji będzie czy just in time jest włączone, i użyć linku, aby otworzyć zasady w usłudze Azure Security Center, aby wyświetlić i zmienić ustawienia.

![Konfiguracja JIT na maszynie wirtualnej](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Żądanie dostępu JIT do maszyny Wirtualnej

W witrynie Azure portal podczas próby nawiązania połączenia z maszyną wirtualną Azure sprawdza w przypadku zasad dostępu just in time, skonfigurowane na tej maszynie Wirtualnej.

- Jeśli nie masz JIT skonfigurowane na maszynie Wirtualnej, użytkownik jest monitowany, aby skonfigurować zasady JIT go.

  ![wiersz JIT](./media/security-center-just-in-time/jit-prompt.png)

- Jeśli masz zasady JIT skonfigurowane na maszynie Wirtualnej, możesz kliknąć **żądania dostępu** umożliwiają dostęp zgodnie z zasadami JIT, ustaw dla maszyny Wirtualnej. Żądania dostępu za pomocą następujących parametrów domyślnych:
    - **źródłowy adres IP**: "Any" (*) (nie można zmienić)
    - **zakres czasu**: 3 godziny (nie można zmienić)
    - **numer portu** RDP port 3389 dla Windows / port 22 dla systemu Linux (można zmienić numer portu w **Połącz z maszyną wirtualną** okno dialogowe.)


  >![Poproś o dostęp JIT](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Inspekcja aktywności dostęp JIT

Możesz uzyskać wgląd w działania maszyny Wirtualnej przy użyciu przeszukiwania dzienników. Aby wyświetlić dzienniki:

1. W obszarze **Just-in-time dostęp do maszyny Wirtualnej**, wybierz opcję **skonfigurowane** kartę.
2. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną, aby wyświetlić informacje o, klikając trzy kropki znajdujące się w wierszu dla tej maszyny Wirtualnej. Spowoduje to otwarcie menu.
3. Wybierz **dziennika aktywności** w menu. Spowoduje to otwarcie **dziennika aktywności**.

   ![Wybierz dziennik aktywności](./media/security-center-just-in-time/select-activity-log.png)

   **Dziennik aktywności** udostępnia widok filtrowany poprzednich operacji dla tej maszyny Wirtualnej wraz z godzina, Data i subskrypcji.

Informacje dziennika można pobrać, wybierając **kliknij tutaj, aby pobrać wszystkie elementy w formacie CSV**.

Modyfikowanie filtrów i wybierz **Zastosuj** do tworzenia wyszukiwania i dziennika.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Uprawnienia wymagane do konfigurowania i używania JIT

Ustaw te wymaganych uprawnień, aby umożliwić użytkownikowi konfigurowanie lub edytowanie zasad JIT dla maszyny Wirtualnej.

Przypisać te *akcje* dla roli: 
- W zakresie subskrypcji lub grupy zasobów, jest skojarzona z maszyną Wirtualną:
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- W zakresie subskrypcji lub grupy zasobów lub maszyny Wirtualnej:
  - Microsoft.Compute/virtualMachines/write 

Należy ustawić te uprawnienia, aby użytkownik mógł pomyślnie żądanie dostępu JIT do maszyny Wirtualnej: Przypisać te *akcje* dla użytkownika:
- W zakresie subskrypcji lub grupy zasobów, jest skojarzona z maszyną Wirtualną:
  - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ initiate/action
- W zakresie subskrypcji lub grupy zasobów lub maszyny Wirtualnej:
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Usługa JIT umożliwia programowe
Można skonfigurować i używać just-in-time za pośrednictwem interfejsów API REST i przy użyciu programu PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Za pomocą just in time access maszyny Wirtualnej za pośrednictwem interfejsów API REST

Funkcja dostępu just in time maszyny Wirtualnej może służyć za pośrednictwem interfejsu API usługi Azure Security Center. Możesz uzyskać informacje na temat skonfigurowanych maszyn wirtualnych, dodawać nowe, poproś o dostęp do maszyny Wirtualnej, a także bardziej, za pośrednictwem tego interfejsu API. Zobacz [zasad dostępu do sieci Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), aby dowiedzieć się więcej na temat interfejsu API REST just-in-time.

### <a name="using-jit-vm-access-via-powershell"></a>Przy użyciu maszyn wirtualnych JIT dostępu za pomocą programu PowerShell 

Aby użyć rozwiązania dostępu just in time maszyny Wirtualnej, za pomocą programu PowerShell, użyj oficjalne Azure Security Center poleceń cmdlet programu PowerShell, a w szczególności `Set-AzJitNetworkAccessPolicy`.

Poniższy przykład ustawia zasady dostępu just in time maszyn wirtualnych w określonej maszyny Wirtualnej, a następnie ustawia następujące czynności:
1.  Zamykanie portów 22 i 3389.
2.  Ustaw maksymalny przedział czasu wynoszącego 3 godziny dla każdego, dzięki czemu można je otworzyć za zatwierdzone żądania.
3.  Umożliwia użytkownikowi, który żąda dostępu do kontrolowania źródłowych adresów IP i umożliwia użytkownikowi nawiązać pomyślnej sesji na żądanie zatwierdzone dostępu just in time.

W programie PowerShell, aby to zrobić, uruchom następujące polecenie:

1.  Przypisz zmienna, która zawiera zasady dostępu just in time maszyny Wirtualnej dla maszyny Wirtualnej:

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

2.  Wstaw zasad maszyny Wirtualnej just-in-time maszyny Wirtualnej dostępu do tablicy:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurowanie zasad dostępu just in time maszyny Wirtualnej na wybranej maszynie Wirtualnej:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Żądanie dostępu do maszyny Wirtualnej

W poniższym przykładzie widać just-in-time żądanie dostępu dla maszyny Wirtualnej do określonej maszyny Wirtualnej w port, który 22 jest wymagane do otwarcia dla określonego adresu IP i dla określonego przedziału czasu:

W programie PowerShell, uruchom następujące polecenie:
1.  Konfigurowanie właściwości maszyny Wirtualnej żądania dostępu

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Wstaw parametry żądania dostępu do maszyny Wirtualnej w tablicy:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Wyślij żądanie dostępu (Użyj Identyfikatora zasobu uzyskany w kroku 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Aby uzyskać więcej informacji zobacz dokumentację poleceń cmdlet programu PowerShell.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono kontrolować dostęp do maszyny Wirtualnej jak just-in-time w usłudze Security Center ułatwia dostęp do usługi Azure virtual machines.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
- [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
- [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
- [Usługa Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
- [Blog Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

