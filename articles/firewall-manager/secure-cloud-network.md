---
title: 'Samouczek: Zabezpiecz wirtualną sieć WAN przy użyciu podglądu usługi Azure Firewall Manager'
description: W tym samouczku dowiesz się, jak zabezpieczyć wirtualną sieć WAN za pomocą usługi Azure Firewall Manager przy użyciu witryny Azure portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443061"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Samouczek: Zabezpiecz wirtualną sieć WAN przy użyciu podglądu usługi Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Korzystając z usługi Azure Firewall Manager Preview, można utworzyć zabezpieczone centra wirtualne w celu zabezpieczenia ruchu sieciowego w chmurze kierowanego do prywatnych adresów IP, usługi Azure PaaS i Internetu. Routing ruchu do zapory jest zautomatyzowany, więc nie ma potrzeby tworzenia tras zdefiniowanych przez użytkownika (UDR).

![zabezpiecz sieć w chmurze](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager obsługuje również architekturę sieci wirtualnej centrum. Aby porównać zabezpieczone typy architektury centrum wirtualnego i sieci wirtualnej, zobacz [Jakie są opcje architektury usługi Azure Firewall Manager?](vhubs-and-vnets.md)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej będącej szprychą
> * Tworzenie zabezpieczonego koncentratora wirtualnego
> * Podłączanie koncentratora i szprychowych sieci wirtualnych
> * Tworzenie zasad zapory i zabezpieczanie centrum
> * Kierowanie ruchu do centrum
> * Testowanie zapory

## <a name="create-a-hub-and-spoke-architecture"></a>Tworzenie architektury koncentratora i szprychy

Najpierw utwórz szprychową sieć wirtualną, w której można umieścić serwery.

### <a name="create-a-spoke-vnet-and-subnets"></a>Tworzenie sieci wirtualnej i podsieci szprychy

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć**wybierz pozycję **Sieć wirtualna**.
4. Dla **Name**, wpisz **Spoke-01**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy**i wpisz **FW-Manager** dla nazwy i wybierz **przycisk OK**.
8. W obszarze **Lokalizacja**wybierz **(USA) wschodnie stany USA**.
9. W **obszarze Podsieć**dla **typu Nazwa** **Workload-SN**.
10. W polu **Zakres adresów** wpisz wartość **10.0.1.0/24**.
11. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

Następnie utwórz podsieć dla serwera szybkiego dostępu.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Grup** > zasobów**FW-Manager**.
2. Wybierz sieć wirtualną **Spoke-01.**
3. Wybierz **podsieci** > **+podsieć**.
4. Dla **Name**, wpisz **Jump-SN**.
5. W polu **Zakres adresów** wpisz wartość **10.0.2.0/24**.
6. Kliknij przycisk **OK**.

### <a name="create-the-secured-virtual-hub"></a>Tworzenie zabezpieczonego koncentratora wirtualnego

Utwórz zabezpieczone centrum wirtualne za pomocą Menedżera zapór.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Wszystkie usługi**.
2. W polu wyszukiwania wpisz **Menedżera zapór i** wybierz pozycję **Menedżer zapór .**
3. Na stronie **Menedżer zapory** wybierz pozycję **Utwórz zabezpieczone centrum wirtualne**.
4. Na stronie **Tworzenie nowego zabezpieczonego centrum wirtualnego** wybierz subskrypcję i grupę zasobów **FW-Manager.**
5. Dla **nazwy zabezpieczonego koncentratora wirtualnego**wpisz **Hub-01**.
6. W obszarze **Lokalizacja**wybierz pozycję **Wschodnie stany USA**.
7. W przypadku **przestrzeni adresowej koncentratora**wpisz **10.1.0.0/16**.
8. Dla nowej nazwy vWAN wpisz **vwan-01**.
9. Pozostaw pole wyboru **Dołącz bramę sieci VPN, aby włączyć pole wyboru Zaufaj partnerom zabezpieczeń** wyczyszczone.
10. Wybierz **dalej:Zapora platformy Azure**.
11. Zaakceptuj **domyślne** ustawienie włączone **zapory platformy Azure,** a następnie wybierz pozycję **Dalej: Zaufany partner zabezpieczeń**.
12. Zaakceptuj domyślne ustawienie **Wyłączono zaufanego partnera zabezpieczeń** **Disabled** i wybierz pozycję **Dalej: Przejrzyj + utwórz**.
13. Wybierz **pozycję Utwórz**. Wdrożenie zajmie około 30 minut.

### <a name="connect-the-hub-and-spoke-vnets"></a>Podłączanie koncentratora i szprychowych sieci wirtualnych

Teraz można równorzędne centrum i szprychy sieci wirtualnych.

1. Wybierz grupę zasobów **FW-Manager,** a następnie wybierz wirtualną sieć WAN **vwan-01.**
2. W obszarze **Łączność**wybierz **pozycję Połączenia sieci wirtualnej**.
3. Wybierz **pozycję Dodaj połączenie**.
4. W przypadku **nazwy połączenia**wpisz **szprychę szprychową**.
5. W przypadku **opcji Koncentratory**wybierz **pozycję Koncentrator-01**.
6. W przypadku **sieci wirtualnej**wybierz **opcję Szprycha-01**.
7. Kliknij przycisk **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tworzenie zasad zapory i zabezpieczanie centrum

Zasady zapory definiuje kolekcje reguł do kierowania ruchu na co najmniej jeden zabezpieczony koncentrator wirtualny. Utworzysz zasady zapory, a następnie zabezpieczysz centrum.

1. W Menedżerze zapory wybierz pozycję **Utwórz zasadę zapory platformy Azure**.
2. Wybierz subskrypcję, a następnie wybierz grupę zasobów **FW-Manager.**
3. W obszarze **Szczegóły zasad**dla typu **nazwa** **Policy-01** i **regionu** wybierz **wschodnie stany USA**.
4. Wybierz **dalej:Reguły**.
5. Na karcie **Reguły** wybierz pozycję **Dodaj kolekcję reguł**.
6. Na stronie **Dodawanie reguły** wpisz **RC-01** dla **name**.
7. W obszarze **Typ kolekcji reguł**wybierz pozycję **Aplikacja**.
8. Dla **priorytetu**, typ **100**.
9. Akcja **zbierania reguł** upewnij **się, że**jest zezwalaj .
10. Dla reguły **Typ nazwa** **Allow-msft**.
11. W przypadku adresu **\*** **źródłowego**wpisz .
12. W przypadku **protokołu**wpisz **http,https**.
13. Upewnij się, że **Typem miejsca docelowego jest **FQDN**.
14. W **yj microsoft.comd.** ** \***
15. Wybierz pozycję **Dodaj**.
16. Wybierz **dalej: Zabezpieczone koncentratory wirtualne**.
17. Na karcie **Zabezpieczone koncentratory wirtualne** wybierz pozycję **Centrum-01**.
19. Wybierz pozycję **Przegląd + utwórz**.
20. Wybierz **pozycję Utwórz**.

Może to potrwać około pięciu minut lub więcej.

## <a name="route-traffic-to-your-hub"></a>Kierowanie ruchu do centrum

Teraz należy upewnić się, że ruch sieciowy zostanie przekierowany przez zaporę.

1. W Menedżerze zapory wybierz pozycję **Zabezpieczone koncentratory wirtualne**.
2. Wybierz **koncentrator-01**.
3. W obszarze **Ustawienia**wybierz pozycję **Ustawienia trasy**.
4. W obszarze **Ruch internetowy**ruch z **sieci wirtualnych**wybierz pozycję **Wyślij za pośrednictwem zapory platformy Azure**.
5. W obszarze **Ruch prywatny platformy Azure**wybierz pozycję **Wyślij** **za pośrednictwem zapory azure**.
6. Wybierz **pozycję Edytuj prefiks adresu IP.**
7. Wybierz **pozycję Dodaj prefiks adresu IP**.
8. Wpisz **10.0.1.0/24** jako adres podsieci Obciążenie i **wybierz**zapisz .
9. W obszarze **Ustawienia**wybierz pozycję **Połączenia**.
10. Wybierz połączenie **z szprychą koncentratora,** a następnie wybierz pozycję **Bezpieczny ruch internetowy,** a następnie wybierz przycisk **OK**.


## <a name="test-your-firewall"></a>Testowanie zapory

Aby przetestować reguły zapory, musisz wdrożyć kilka serwerów. Wdrożysz Workload-Srv w podsieci Workload-SN, aby przetestować reguły zapory, i Jump-Srv, dzięki czemu można używać pulpitu zdalnego do łączenia się z Internetu, a następnie łączyć się z Workload-Srv.

### <a name="deploy-the-servers"></a>Wdrażanie serwerów

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz **pozycję Centrum danych systemu Windows Server 2016** na liście **Popularne.**
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Grupa zasobów     |**Menedżer FW**|
   |Nazwa maszyny wirtualnej     |**Skok-Srv**|
   |Region     |**(Stany Zjednoczone) wschodnie stany USA)**|
   |Nazwa użytkownika administratora     |**użytkownik_azure**|
   |Hasło     |wpisz hasło|

4. W obszarze **Reguły portów przychodzących**dla **publicznych portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty**.
5. W obszarze **Wybierz porty wejściowe** wybierz pozycję **RDP (3389)**.

6. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Dalej: Dyski**.
7. Zaakceptuj ustawienia domyślne dysku i wybierz **pozycję Dalej: Sieć**.
8. Upewnij się, że **spoke-01** jest wybrany dla sieci wirtualnej, a podsiecią jest **Jump-SN**.
9. W przypadku **publicznego adresu IP**zaakceptuj domyślną nową nazwę publicznego adresu IP (Jump-Srv-ip).
11. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Dalej: Zarządzanie**.
12. Wybierz **opcję Wyłącz,** aby wyłączyć diagnostykę rozruchu. Zaakceptuj inne ustawienia domyślne i wybierz **pozycję Przejrzyj + utwórz**.
13. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

Użyj informacji w poniższej tabeli, aby skonfigurować inną maszynę wirtualną o nazwie **Workload-Srv**. Pozostała część konfiguracji jest taka sama jak w przypadku maszyny wirtualnej Srv-Jump.

|Ustawienie  |Wartość  |
|---------|---------|
|Podsieć|**Workload-SN**|
|Publiczny adres IP|**Brak**|
|Publiczne porty wejściowe|**Brak**|

### <a name="add-a-route-table-and-default-route"></a>Dodawanie tabeli trasy i trasy domyślnej

Aby zezwolić na połączenie internetowe z jump-srv, należy utworzyć tabelę marszruty i domyślną trasę bramy do Internetu z podsieci **Jump-SN.**

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wpisz **tabelę tras** w polu wyszukiwania, a następnie wybierz pozycję **Tabela Trasy**.
3. Wybierz **pozycję Utwórz**.
4. Wpisz **RT-01** dla **Name**.
5. Wybierz subskrypcję, **FW-Manager** dla grupy zasobów i **(USA) wschodnie stany USA** dla regionu.
6. Wybierz **pozycję Utwórz**.
7. Po zakończeniu wdrażania wybierz tabelę tras **RT-01.**
8. Wybierz **pozycję Trasy,** a następnie wybierz pozycję **Dodaj**.
9. Wpisz **polecenie jump-to-inet** dla **nazwy trasy**.
10. Typ **0.0.0.0/0** dla **prefiksu adresu**.
11. Wybierz **internet** dla **następnego typu przeskoku**.
12. Kliknij przycisk **OK**.
13. Po zakończeniu wdrażania wybierz **podsieci**, a następnie wybierz pozycję **Skojarz**.
14. Wybierz **Szprychę-01** dla **sieci wirtualnej**.
15. Wybierz **jump-SN** dla **podsieci**.
16. Kliknij przycisk **OK**.

### <a name="test-the-rules"></a>Testowanie reguł

Teraz przetestuj reguły zapory, aby potwierdzić, że działa zgodnie z oczekiwaniami.

1. W witrynie Azure portal przejrzyj ustawienia sieciowe maszyny wirtualnej **Workload-Srv** i zanotuj prywatny adres IP.
2. Podłącz pulpit zdalny do maszyny wirtualnej **Jump-Srv** i zaloguj się. Stamtąd otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **Workload-Srv.**

3. Otwórz program Internet Explorer i przejdź do https://www.microsoft.com.
4. Wybierz **przycisk OK** > **Zamknij** w alertach zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna firmy Microsoft.

5. Przejdź do https://www.google.com.

   Dostęp powinien zostać zablokowany przez zaporę.

Więc teraz masz zweryfikowane, że reguły zapory działają:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zaufanych partnerach w dziedzinie zabezpieczeń](trusted-security-partners.md)
