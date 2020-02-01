---
title: Integracja z usługą Azure Private Link Service
description: Dowiedz się, jak zintegrować Azure Key Vault z usługą Azure Private Link Service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907075"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Integracja Key Vault z prywatnym łączem platformy Azure (wersja zapoznawcza)

Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany za pośrednictwem prywatnego punktu końcowego, dzięki czemu nie będzie wymagane użycie bram, urządzeń NAT, połączeń ExpressRoute, połączeń VPN ani publicznych adresów IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link (wersja zapoznawcza)?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Magazyn kluczy z prywatnym łączem platformy Azure (wersja zapoznawcza), potrzebne są następujące elementy:

- Magazyn kluczy.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora dla magazynu kluczy i sieci wirtualnej.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku wybrania regionu dla prywatnego punktu końcowego przy użyciu portalu program automatycznie odfiltruje tylko te sieci wirtualne, które znajdują się w tym regionie. Magazyn kluczy może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Ustanów połączenie prywatne z magazynem kluczy

Najpierw Utwórz sieć wirtualną, wykonując czynności opisane w temacie [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md)

Następnie można utworzyć nowy magazyn kluczy lub nawiązać połączenie prywatne z istniejącym magazynem kluczy.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Tworzenie nowego magazynu kluczy i nawiązywanie połączenia prywatnego

Nowy magazyn kluczy można utworzyć, wykonując kroki z [zestawu i pobierając klucz tajny z Azure Key Vault przy użyciu Azure Portal](quick-create-portal.md)

Po skonfigurowaniu podstawy magazynu kluczy wybierz kartę Sieć i wykonaj następujące czynności:

1. Wybierz przycisk radiowy prywatny punkt końcowy (wersja zapoznawcza) na karcie Sieć.
1. Kliknij przycisk "+ Dodaj", aby dodać prywatny punkt końcowy.

    ![Obraz](./media/private-link-service-1.png)
 
1. W polu "lokalizacja" bloku Utwórz prywatny punkt końcowy wybierz region, w którym znajduje się Twoja sieć wirtualna. 
1. W polu "name" (nazwa) Utwórz opisową nazwę, która umożliwi zidentyfikowanie tego prywatnego punktu końcowego. 
1. Wybierz sieć wirtualną i podsieć, dla której ma zostać utworzony ten prywatny punkt końcowy z menu rozwijanego. 
1. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  
1. Wybierz pozycję "OK".

    ![Obraz](./media/private-link-service-2.png)
 
Teraz będzie można zobaczyć skonfigurowany prywatny punkt końcowy. Masz teraz możliwość usunięcia i edytowania tego prywatnego punktu końcowego. Wybierz przycisk "Przejrzyj + Utwórz" i Utwórz magazyn kluczy. Ukończenie wdrożenia zajmie 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Ustanów połączenie prywatne z istniejącym magazynem kluczy

Jeśli masz już Magazyn kluczy, możesz utworzyć połączenie prywatne, wykonując następujące czynności:

1. Zaloguj się do Portalu Azure. 
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Z listy wybierz magazyn kluczy, do którego chcesz dodać prywatny punkt końcowy.
1. Wybierz kartę "Sieć" w obszarze Ustawienia
1. Wybierz kartę prywatne połączenia punktu końcowego (wersja zapoznawcza) w górnej części strony
1. Wybierz przycisk "+ prywatny punkt końcowy" w górnej części strony.

    obraz ![obrazu](./media/private-link-service-3.png) ![](./media/private-link-service-4.png)

Za pomocą tego bloku można utworzyć prywatny punkt końcowy dla dowolnego zasobu platformy Azure. Możesz użyć menu rozwijanych, aby wybrać typ zasobu i wybrać zasób w katalogu, lub połączyć się z dowolnym zasobem platformy Azure przy użyciu identyfikatora zasobu. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  

obraz ![obrazu](./media/private-link-service-3.png)
![](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Zarządzaj połączeniem prywatnego linku

Podczas tworzenia prywatnego punktu końcowego należy zatwierdzić połączenie. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, będzie można zatwierdzić żądanie połączenia, pod warunkiem że masz wystarczające uprawnienia; Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi Twoje żądanie połączenia.

Istnieją cztery Stany aprowizacji:

| Akcja udostępniania usługi | Stan prywatnego punktu końcowego klienta usługi | Opis |
|--|--|--|
| Brak | Oczekujące | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu link prywatny. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia. |
| Reject | Odrzucono | Połączenie zostało odrzucone przez właściciela zasobu link prywatny. |
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela zasobu link prywatny, a prywatny punkt końcowy zmieni się na format i powinien zostać usunięty do oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Jak zarządzać połączeniem prywatnego punktu końcowego z magazynem kluczy

1. Zaloguj się w witrynie Azure Portal.
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Wybierz magazyn kluczy, którym chcesz zarządzać.
1. Wybierz kartę Sieć.
1. W przypadku braku oczekujących połączeń zostanie wyświetlone połączenie z opcją "oczekiwanie" w stanie aprowizacji. 
1. Wybierz prywatny punkt końcowy, który chcesz zatwierdzić
1. Wybierz przycisk Zatwierdź.
1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to oczekujące żądanie, czy istniejące połączenie, wybierz połączenie i kliknij przycisk Odrzuć.

    ![Obraz](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdź, czy połączenie z linkiem prywatnym działa

Należy sprawdzić, czy zasoby znajdujące się w tej samej podsieci zasobu prywatnego punktu końcowego są połączone z magazynem kluczy za pośrednictwem prywatnego adresu IP i czy mają poprawną integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując czynności opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na karcie "Sieć":

1. Określ sieć wirtualną i podsieć. Można utworzyć nową sieć wirtualną lub wybrać istniejącą. Jeśli wybierzesz jeden z istniejących, upewnij się, że region jest zgodny.
1. Określ zasób publicznego adresu IP.
1. W obszarze "sieciowa Grupa zabezpieczeń kart sieciowych" Wybierz pozycję "Brak".
1. W obszarze "Równoważenie obciążenia" Wybierz pozycję "nie".

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

W przypadku uruchomienia polecenia wyszukiwania NS w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem publicznego punktu końcowego zobaczysz wynik, który wygląda następująco:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

W przypadku uruchomienia polecenia wyszukiwania NS w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem prywatnego punktu końcowego zobaczysz wynik, który wygląda następująco:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link (wersja zapoznawcza)](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia**: prywatny punkt końcowy dla Azure Key Vault jest w publicznej wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Key Vault**: 64.

**Maksymalna liczba magazynów kluczy z prywatnymi punktami końcowymi na subskrypcję**: 64.

Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Private link (wersja zapoznawcza)](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Key Vault](key-vault-overview.md)
