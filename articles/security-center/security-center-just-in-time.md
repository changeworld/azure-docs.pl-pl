---
title: Tylko na maszynie wirtualnej czas dostępu w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób dokładnie na czas dostępu do maszyny Wirtualnej w usłudze Azure Security Center pomaga kontroli dostępu do usługi Azure virtual machines.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: cb13da7ad9387b7170882752b1620c2756bc3675
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124155"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Zarządzanie dostępem maszyny wirtualnej przy użyciu dokładnie na czas

Dokładnie na czas maszyny wirtualnej (VM) dostępu może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, zmniejszenia narażenia na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie.

> [!NOTE]
> Tylko w czasie funkcja jest dostępna w warstwie standardowa usługi Security Center.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>
>

## <a name="attack-scenario"></a>Ataku

Atak siłowy ataki często porty zarządzania docelowe w celu uzyskania dostępu do maszyny Wirtualnej. W przypadku powodzenia osoba atakująca może przejąć kontrolę nad maszyną Wirtualną i ustanowić przyczółka w środowisku.

Jest jednym ze sposobów, aby zmniejszyć narażenie na ataki siłowe Aby ograniczyć ilość czasu, który port jest otwarty. Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, np. aby wykonać zadania związane z zarządzaniem lub konserwacją. Gdy dokładnie na czas jest włączone, usługa Security Center używa [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules) (reguły sieciowej grupy zabezpieczeń), które ograniczają dostęp do portów zarządzania, dlatego nie może być obiektem ataków.

![Dokładnie na czas scenariusza][1]

## <a name="how-does-just-in-time-access-work"></a>Jak dostęp dokładnie na czas pracy?

Gdy zostanie włączona funkcja „dokładnie na czas”, usługa Security Center zablokuje ruch przychodzący do maszyn wirtualnych platformy Azure poprzez utworzenie reguły sieciowej grupy zabezpieczeń. Należy wybrać porty na maszynie Wirtualnej, do którego zostanie zablokowany ruch przychodzący. Te porty są kontrolowane przez tylko w rozwiązaniu do czasu.

Gdy użytkownik poprosi o dostęp do maszyny Wirtualnej, Centrum zabezpieczeń sprawdza, czy użytkownik ma [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) uprawnienia, które zapewniają dostęp do zapisu dla maszyny Wirtualnej. Jeśli mają uprawnienia do zapisu, żądanie zostanie zatwierdzone i usługi Security Center automatycznie skonfiguruje sieciowe grupy zabezpieczeń (NSG) w celu zezwolenia na ruch przychodzący na porty wybranego przez czas określony. Po upływie czasu, usługa Security Center przywraca sieciowych grup zabezpieczeń do poprzedniego stanu. Tych połączeń, które już istnieją są nie są przerywane, jednak.

> [!NOTE]
> Centrum zabezpieczeń dostęp dokładnie na czas maszyny Wirtualnej obsługuje obecnie tylko maszyny wirtualne wdrożone za pośrednictwem usługi Azure Resource Manager. Aby dowiedzieć się więcej o klasyczny, jak i modelem wdrażania usługi Resource Manager, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Za pomocą dostęp dokładnie na czas

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

2. W okienku po lewej stronie wybierz **Just in dostęp maszyny Wirtualnej w czasie**.

![Dokładnie na czas dostępu do maszyny Wirtualnej kafelka][2]

**Just in dostęp maszyny Wirtualnej w czasie** zostanie otwarte okno.

![Dokładnie na czas dostępu do maszyny Wirtualnej kafelka][10]

**Dostęp just in time do maszyny wirtualnej** zapewnia informacje o stanie maszyn wirtualnych:

- **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane pod kątem dostępu just in time do maszyn wirtualnych. Dane prezentowane jest ostatniego tygodnia i liczbie dla każdej maszyny Wirtualnej zatwierdzonych wniosków, Data ostatniego dostępu i godziny oraz ostatniego użytkownika.
- **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp just in time, ale nie zostały skonfigurowane pod tym kątem. Firma Microsoft zaleca, włączyć tylko w przypadku kontroli dostępu na maszynie Wirtualnej czas na te maszyny wirtualne. Zobacz [Konfigurowanie tylko w zasadach dostępu czasu](#configuring-a-just-in-time-access-policy).
- **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:
  - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in time wymaga sieciowej grupy zabezpieczeń.
  - Klasyczna maszyna wirtualna — dostęp just in time do maszyn wirtualnych usługi Security Center obecnie obsługuje tylko maszyny wirtualne wdrożone przy użyciu usługi Azure Resource Manager. Wdrożenia klasycznego nie jest obsługiwana przez tylko w rozwiązaniu do czasu.
  - Inne — maszyna wirtualna znajduje się w tej kategorii, jeżeli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów albo jeśli maszyna wirtualna nie ma publicznego adresu IP i sieciowej grupy zabezpieczeń.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurowanie tylko w zasadach dostępu czasu

Aby wybrać maszyny wirtualne, które chcesz włączyć:

1. W obszarze **Just in dostęp maszyny Wirtualnej w czasie**, wybierz opcję **zalecane** kartę.

  ![Włącz dostęp dokładnie na czas][3]

2. W obszarze **maszyny WIRTUALNEJ**, wybierz maszyny wirtualne, które chcesz włączyć. Spowoduje to umieszczenie znacznik wyboru obok maszyny Wirtualnej.
3. Wybierz **Włącz dostęp JIT na maszynach wirtualnych**.
4. Wybierz pozycję **Zapisz**.

### <a name="default-ports"></a>Porty domyślne

Możesz zobaczyć porty domyślne, których usługa Security Center zaleca się włączenie dokładnie na czas.

1. W obszarze **Just in dostęp maszyny Wirtualnej w czasie**, wybierz opcję **zalecane** kartę.

  ![Wyświetlanie portów domyślnych][6]

2. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną. To umieszcza znacznik wyboru obok maszyny Wirtualnej i otwiera **Konfiguracja dostępu do maszyn wirtualnych JIT**. Ten blok zawiera domyślne porty.

### <a name="add-ports"></a>Dodawanie portów

W obszarze **Konfiguracja dostępu do maszyn wirtualnych JIT**, można także dodać i skonfigurować nowy port, na którym chcesz włączyć tylko w rozwiązaniu do czasu.

1. W obszarze **Konfiguracja dostępu do maszyn wirtualnych JIT**, wybierz opcję **Dodaj**. Spowoduje to otwarcie **Dodawanie konfiguracji portu**.

  ![Konfiguracja portu][7]

2. W obszarze **Dodawanie konfiguracji portu**, zidentyfikuj port, protokół typu, dozwolone źródłowe adresy IP i maksymalny czas żądania.

  Dozwolone źródłowe adresy IP są zakresy adresów IP, które mogą uzyskać dostęp po zatwierdzonym żądaniu.

  Maksymalny czas żądania jest oknem maksymalny czas, można otworzyć określonego portu.

3. Kliknij przycisk **OK**.

> [!NOTE]
>Gdy dostęp do maszyn wirtualnych JIT jest włączone dla maszyny Wirtualnej, usługa Azure Security Center tworzy odmówić wszystkich reguł ruchu przychodzącego dla wybranych portów w grupach zabezpieczeń sieci skojarzonych z nim. Zasady będzie najwyższy priorytet o sieciowych grupach zabezpieczeń lub niższy priorytet niż istniejące reguły, które zostały już istnieje. Zależy to od analizy wykonywane przez usługę Azure Security Center, która określa, czy reguła jest bezpieczne.
>
## <a name="requesting-access-to-a-vm"></a>Żądanie dostępu do maszyny Wirtualnej

Aby poprosić o dostęp do maszyny Wirtualnej:

1. W obszarze **Just in dostęp maszyny Wirtualnej w czasie**, wybierz opcję **skonfigurowane** kartę.
2. W obszarze **maszyn wirtualnych**, wybierz maszyny wirtualne, które chcesz włączyć dostęp. Spowoduje to umieszczenie znacznik wyboru obok maszyny Wirtualnej.
3. Wybierz **żądania dostępu**. Spowoduje to otwarcie **żądania dostępu**.

  ![Poproś o dostęp do maszyny Wirtualnej][4]

4. W obszarze **żądania dostępu**, skonfiguruj dla każdej maszyny Wirtualnej porty do otwarcia wraz z otwartym port źródłowy adres IP i przedział czasu, dla którego port jest otwarty. Możesz zażądać dostępu tylko do portów, które są skonfigurowane w just w zasadach czasu. Każdy port może zawierać maksymalnie dopuszczalny czas pochodną just w zasadach czasu.
5. Wybierz **otworzyć porty**.

> [!NOTE]
> Gdy użytkownik poprosi o dostęp do maszyny Wirtualnej, Centrum zabezpieczeń sprawdza, czy użytkownik ma [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) uprawnienia, które zapewniają dostęp do zapisu dla maszyny Wirtualnej. Jeśli mają uprawnienia do zapisu, żądanie zostanie zatwierdzone.
>
>

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu znajduje się za serwerem proxy, opcja "Mój adres IP" może nie działać. Może to być, aby zdefiniować pełną gamę organizacji.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Edytowanie tylko w zasadach dostępu czasu

Można zmienić, istniejącej maszyny Wirtualnej dokładnie na czas zasad, przez dodanie i skonfigurowanie nowego portu można otworzyć dla tej maszyny Wirtualnej lub zmieniając wszelkie inne parametry związane z portem już chronione.

Aby można było edytować istniejące dokładnie na czas zasad maszyny wirtualnej, **skonfigurowane** karta jest używana:

1. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną, aby dodać port do, klikając trzy kropki znajdujące się w wierszu dla tej maszyny Wirtualnej. Spowoduje to otwarcie menu.
2. Wybierz **Edytuj** w menu. Spowoduje to otwarcie **Konfiguracja dostępu do maszyn wirtualnych JIT**.

  ![Edycja zasad][8]

3. W obszarze **Konfiguracja dostępu do maszyn wirtualnych JIT**, albo można edytować istniejące ustawienia portu już chronionego, klikając jego port lub możesz wybrać **Dodaj**. Spowoduje to otwarcie **Dodawanie konfiguracji portu**.

  ![Dodawanie portu][7]

4. W obszarze **Dodawanie konfiguracji portu**, ustalić port, protokół typu, dozwolone źródłowe adresy IP i maksymalny czas żądania.
5. Kliknij przycisk **OK**.
6. Wybierz pozycję **Zapisz**.

## <a name="auditing-just-in-time-access-activity"></a>Inspekcja dokładnie na czas dostępu do działania

Możesz uzyskać wgląd w działania maszyny Wirtualnej przy użyciu przeszukiwania dzienników. Aby wyświetlić dzienniki:

1. W obszarze **Just in dostęp maszyny Wirtualnej w czasie**, wybierz opcję **skonfigurowane** kartę.
2. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną, aby wyświetlić informacje o, klikając trzy kropki znajdujące się w wierszu dla tej maszyny Wirtualnej. Spowoduje to otwarcie menu.
3. Wybierz **dziennika aktywności** w menu. Spowoduje to otwarcie **dziennika aktywności**.

  ![Wybierz dziennik aktywności][9]

  **Dziennik aktywności** udostępnia widok filtrowany poprzednich operacji dla tej maszyny Wirtualnej wraz z godzina, Data i subskrypcji.

Informacje dziennika można pobrać, wybierając **kliknij tutaj, aby pobrać wszystkie elementy w formacie CSV**.

Modyfikowanie filtrów i wybierz **Zastosuj** do tworzenia wyszukiwania i dziennika.

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>Przy użyciu dokładnie na czas dostępu do maszyny Wirtualnej za pośrednictwem interfejsów API REST

Po prostu dostęp maszyny Wirtualnej w czasie funkcja może być używana przy użyciu interfejsu API usługi Azure Security Center. Możesz uzyskać informacje na temat skonfigurowanych maszyn wirtualnych, dodawać nowe, poproś o dostęp do maszyny Wirtualnej, a także bardziej, za pośrednictwem tego interfejsu API. Zobacz [zasad dostępu do sieci Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), aby dowiedzieć się więcej na temat just w czasie interfejsu API REST.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Przy użyciu dokładnie na czas dostępu do maszyny Wirtualnej za pomocą programu PowerShell 

Aby użyć tylko w czas rozwiązania dostępu do maszyny Wirtualnej za pomocą programu PowerShell, użyj oficjalne poleceń cmdlet programu PowerShell Centrum zabezpieczeń Azure, zwłaszcza `Set-AzureRmJitNetworkAccessPolicy`.

W poniższym przykładzie ustawiono tylko w czasie maszyna wirtualna zasady na określonej maszynie Wirtualnej dostępu i ustawia następujące czynności:
1.  Zamykanie portów 22 i 3389.
2.  Ustaw maksymalny przedział czasu wynoszącego 3 godziny dla każdego, dzięki czemu można je otworzyć za zatwierdzone żądania.
3.  Zezwala na użytkownika, który żąda dostępu do kontroli źródła adresów IP, a także umożliwia użytkownikowi ustanowić sesję pomyślne na zatwierdzonych dokładnie na czas żądania dostępu.

W programie PowerShell, aby to zrobić, uruchom następujące polecenie:

1.  Przypisz zmienna, która zawiera tylko w czasie maszyna wirtualna zasady dostępu dla maszyny Wirtualnej:

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

2.  Wstaw maszyny Wirtualnej dokładnie na czas maszyny Wirtualnej warunkowego do tablicy:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurowanie tylko w czasie maszyna wirtualna zasady na wybranej maszynie Wirtualnej dostępu:
    
        Set-AzureRmJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="requesting-access-to-a-vm"></a>Żądanie dostępu do maszyny Wirtualnej

W poniższym przykładzie widać tylko w żądaniu dostępu do maszyny Wirtualnej godziny określonej maszyny Wirtualnej w port, który 22 jest wymagane do otwarcia dla określonego adresu IP i dla określonego przedziału czasu:

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

        Start-AzureRmJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Aby uzyskać więcej informacji zobacz dokumentację poleceń cmdlet programu PowerShell.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób dokładnie na czas dostępu do maszyny Wirtualnej w usłudze Security Center pomaga, kontrolować dostęp do usługi Azure virtual machines.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- [Ustawianie zasad zabezpieczeń](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
- [Reagowanie na alerty zabezpieczeń i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
- [Monitorowanie rozwiązań partnerskich](security-center-partner-solutions.md) — informacje o sposobie monitorowania stanu kondycji rozwiązań partnerskich.
- [Usługa Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
- [Blog Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
