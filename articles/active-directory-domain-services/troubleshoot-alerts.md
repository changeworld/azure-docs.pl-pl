---
title: Typowe alerty i rozwiązania w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe alerty generowane w ramach stanu kondycji usług domenowych Usługi domenowe Active Directory azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612914"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Znane problemy: typowe alerty i rozwiązania w usługach domenowych Usługi domenowe Active Directory platformy Azure

Centralna część tożsamości i uwierzytelniania aplikacji usługi domenowe Active Directory (Azure AD DS) czasami występują problemy. Jeśli napotkasz problemy, istnieją pewne typowe alerty i skojarzone kroki rozwiązywania problemów, które pomogą Ci ponownie uruchomić. W dowolnym momencie można również [otworzyć żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z typowymi alertami w usługach Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Brak katalogu

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Katalog usługi Azure AD skojarzony z domeną zarządzana mógł zostać usunięty. Domena zarządzana nie jest już w obsługiwanej konfiguracji. Firma Microsoft nie może monitorować domeny zarządzanej, zarządzać nią, łatać i synchronizować.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest zwykle spowodowane, gdy subskrypcja platformy Azure jest przenoszona do nowego katalogu usługi Azure AD i starego katalogu usługi Azure AD, który jest skojarzony z usługą Azure AD DS jest usuwany.

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [usuń istniejącą domenę zarządzana usługą Azure AD DS](delete-aadds.md) i ponownie stwórz ją w nowym katalogu. Jeśli masz problemy z usunięciem domeny zarządzanej usług Azure AD DS, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>Usługa AADDS101: Usługa Azure AD B2C jest uruchomiona w tym katalogu

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Usługi domenowe usługi Azure AD nie można włączyć w katalogu usługi Azure AD B2C.*

### <a name="resolution"></a>Rozwiązanie

Usługi Azure AD DS automatycznie synchronizuje się z katalogiem usługi Azure AD. Jeśli katalog usługi Azure AD jest skonfigurowany dla usługi B2C, nie można wdrożyć i zsynchronizować usługi Azure AD DS.

Aby korzystać z usług Azure AD DS, należy ponownie utworzyć domenę zarządzaną w katalogu usług B2C innych niż usługi Azure, wykonując następujące kroki:

1. [Usuń domenę zarządzana usługą Azure AD DS](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
1. Utwórz nowy katalog usługi Azure AD, który nie jest katalogiem usługi Azure AD B2C.
1. [Utwórz zastępczą domenę zarządzaną usługą Azure AD DS](tutorial-create-instance.md).

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adres znajduje się w publicznym zakresie adresów IP

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Zakres adresów IP dla sieci wirtualnej, w której włączono usługi domenowe usługi Azure AD Domain Services, znajduje się w publicznym zakresie adresów IP. Usługi domenowe usługi Azure AD muszą być włączone w sieci wirtualnej z zakresem prywatnych adresów IP. Ta konfiguracja wpływa na zdolność firmy Microsoft do monitorowania, zarządzania, instalowania poprawek i synchronizowania domeny zarządzanej.*

### <a name="resolution"></a>Rozwiązanie

Przed rozpoczęciem upewnij się, że znasz [prywatne przestrzenie adresowe IP v4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

W sieci wirtualnej maszyny wirtualne mogą żądać zasobów platformy Azure w tym samym zakresie adresów IP, co skonfigurowano dla podsieci. Jeśli skonfigurujesz zakres publicznego adresu IP dla podsieci, żądania kierowane w sieci wirtualnej mogą nie dotrzeć do zamierzonych zasobów sieci web. Ta konfiguracja może prowadzić do nieprzewidywalnych błędów w usługach Azure AD DS.

> [!NOTE]
> Jeśli jesteś właścicielem zakresu adresów IP w Internecie, który jest skonfigurowany w sieci wirtualnej, ten alert może być ignorowany. Jednak usługi domenowe usługi Azure AD nie można zatwierdzić do [umowy SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] z tej konfiguracji, ponieważ może to prowadzić do błędów nieprzewidywalne.

Aby rozwiązać ten alert, usuń istniejącą domenę zarządzaną usługą Azure AD DS i ponownie stwórz ją w sieci wirtualnej z zakresem prywatnych adresów IP. Ten proces jest destrukcyjny, ponieważ domena zarządzana usługą Azure AD DS jest niedostępna, a wszystkie utworzone zasoby niestandardowe, takie jak jednostki organizacyjne lub konta usług, zostaną utracone.

1. [Usuń domenę zarządzana usługą Azure AD DS](delete-aadds.md) z katalogu.
1. Aby zaktualizować zakres adresów IP sieci wirtualnej, wyszukaj i wybierz *sieć wirtualną* w witrynie Azure portal. Wybierz sieć wirtualną dla usług Azure AD DS, która niepoprawnie ma ustawiony zakres publicznego adresu IP.
1. W obszarze **Ustawienia**wybierz pozycję *Przestrzeń adresowa*.
1. Zaktualizuj zakres adresów, wybierając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów IP znajduje się w prywatnym zakresie adresów IP. Gdy wszystko będzie gotowe, **zapisz** zmiany.
1. Wybierz **podsieci** w nawigacji po lewej stronie.
1. Wybierz podsieć, którą chcesz edytować, lub utwórz dodatkową podsieć.
1. Zaktualizuj lub określ zakres prywatnych adresów IP, a następnie **zapisz** zmiany.
1. [Utwórz zastępczą domenę zarządzaną usługą Azure AD DS](tutorial-create-instance.md). Upewnij się, że wytypowana podsieć sieci wirtualnej została pobrana z zakresem prywatnych adresów IP.

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Nie znaleziono subskrypcji platformy Azure

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Twoja subskrypcja platformy Azure skojarzona z domeną zarządzana została usunięta.  Usługi domenowe usługi Azure AD services wymaga aktywnej subskrypcji, aby kontynuować prawidłowe działanie.*

### <a name="resolution"></a>Rozwiązanie

Usługi Azure AD DS wymaga aktywnej subskrypcji i nie można przenieść do innej subskrypcji. Jeśli subskrypcja platformy Azure, z którą została skojarzona domena zarządzana usługą Azure AD DS, została usunięta, należy ponownie utworzyć subskrypcję platformy Azure i domenę zarządzaną usługą Azure AD DS.

1. [Utwórz subskrypcję platformy Azure](../cost-management-billing/manage/create-subscription.md).
1. [Usuń domenę zarządzana usługą Azure AD DS](delete-aadds.md) z istniejącego katalogu usługi Azure AD.
1. [Utwórz zastępczą domenę zarządzaną usługą Azure AD DS](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Subskrypcja platformy Azure jest wyłączona

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Twoja subskrypcja platformy Azure skojarzona z domeną zarządzana nie jest aktywna.  Usługi domenowe usługi Azure AD services wymaga aktywnej subskrypcji, aby kontynuować prawidłowe działanie.*

### <a name="resolution"></a>Rozwiązanie

Usługi Azure AD DS wymaga aktywnej subskrypcji. Jeśli subskrypcja platformy Azure, z którą została skojarzona domena zarządzana usługą Azure AD DS, nie jest aktywna, należy ją odnowić, aby ponownie aktywować subskrypcję.

1. [Odnów swoją subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji powiadomienie usługi Azure AD DS umożliwia ponowne włączenie domeny zarządzanej.

Gdy domena zarządzana jest ponownie włączona, kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Subskrypcja przeniesiona katalogi

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Subskrypcja używana przez usługi domenowe usługi azure ad została przeniesiona do innego katalogu. Usługi domenowe usługi Azure AD muszą mieć aktywną subskrypcję w tym samym katalogu, aby działały poprawnie.*

### <a name="resolution"></a>Rozwiązanie

Usługi Azure AD DS wymaga aktywnej subskrypcji i nie można przenieść do innej subskrypcji. Jeśli subskrypcja platformy Azure, z którą była skojarzona domena zarządzana usługą Azure AD DS, zostanie przeniesiona, przenieś subskrypcję z powrotem do poprzedniego katalogu lub [usuń domenę zarządzaną](delete-aadds.md) z istniejącego katalogu i [utwórz zastępczą domenę zarządzaną usługą Azure AD DS w wybranej subskrypcji.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Nie można odnaleźć zasobów dla domeny zarządzanej

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Zasób używany dla domeny zarządzanej został usunięty. Ten zasób jest potrzebny do prawidłowego działania usług domenowych usługi Azure AD.*

### <a name="resolution"></a>Rozwiązanie

Usługa Azure AD DS tworzy dodatkowe zasoby do poprawnego działania, takie jak publiczne adresy IP, interfejsy sieci wirtualnej i moduł równoważenia obciążenia. Jeśli którykolwiek z tych zasobów zostaną usunięte, domena zarządzana jest w stanie nieobsługiwanym i uniemożliwia zarządzanie domeną. Aby uzyskać więcej informacji na temat tych zasobów, zobacz [Zasoby sieciowe używane przez usługi Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Ten alert jest generowany po usunięciu jednego z tych wymaganych zasobów. Jeśli zasób został usunięty mniej niż 4 godziny temu, istnieje prawdopodobieństwo, że platforma Azure może automatycznie odtworzyć usunięty zasób. W poniższych krokach opisano sposób sprawdzania stanu kondycji i sygnatury czasowej pod kątem usuwania zasobów:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe**. Wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*.
1. W nawigacji po lewej stronie wybierz pozycję **Zdrowie**.
1. Na stronie kondycji wybierz alert o identyfikatorze *AADDS109*.
1. Alert ma sygnaturę czasową dla kiedy został znaleziony po raz pierwszy. Jeśli ten sygnatura czasowa jest mniej niż 4 godziny temu, platforma Azure może być w stanie automatycznie odtworzyć zasób i rozwiązać alert przez siebie.

    Jeśli alert ma więcej niż 4 godziny, domena zarządzana usługą Azure AD DS jest w stanie nieodwracalnym. [Usuń domenę zarządzaną usług Azure AD DS,](delete-aadds.md) a następnie [utwórz zastępczą domenę zarządzaną](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Podsieć skojarzona z domeną zarządzaną jest pełna

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Podsieć wybrana do wdrożenia usług domenowych usługi azure ad jest pełna i nie ma miejsca na dodatkowy kontroler domeny, który musi zostać utworzony.*

### <a name="resolution"></a>Rozwiązanie

Podsieć sieci wirtualnej dla usług Azure AD DS wymaga wystarczającej liczby adresów IP dla automatycznie utworzonych zasobów. Ta przestrzeń adresowa IP obejmuje konieczność tworzenia zasobów zastępczych, jeśli występuje zdarzenie konserwacji. Aby zminimalizować ryzyko wyczerpania się dostępnych adresów IP, nie należy wdrażać dodatkowych zasobów, takich jak własne maszyny wirtualne, w tej samej podsieci sieci wirtualnej co usługi Azure AD DS.

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [usuń istniejącą domenę zarządzana usługą Azure AD DS](delete-aadds.md) i ponownie ją utworzyć. Jeśli masz problemy z usunięciem domeny zarządzanej usług Azure AD DS, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Nieupoważniona jednostka usługi

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Podmiot usługi, którego usługi Azure AD Domain Services używa do obsługi domeny, nie jest autoryzowany do zarządzania zasobami w ramach subskrypcji platformy Azure. Podmiot usługi musi uzyskać uprawnienia do obsługi domeny zarządzanej.*

### <a name="resolution"></a>Rozwiązanie

Niektóre automatycznie generowane jednostki usługi są używane do zarządzania zasobami i tworzenia zasobów dla domeny zarządzanej usług Azure AD DS. Jeśli uprawnienia dostępu dla jednej z tych podmiotów usługi zostaną zmienione, domena nie może poprawnie zarządzać zasobami. Poniższe kroki pokazują, jak zrozumieć, a następnie udzielić uprawnień dostępu do jednostki usługi:

1. Przeczytaj o [kontroli dostępu opartej na rolach i o tym, jak udzielić dostępu do aplikacji w witrynie Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Przejrzyj dostęp podmiotu usługi o *identyfikatorze abba844e-bc0e-44b0-947a-dc74e5d09022* i udzielij dostępu, który został odrzucony wcześniej.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Za mało adresu IP w domenie zarządzanej

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Firma Microsoft zidentyfikowała, że podsieć sieci wirtualnej w tej domenie może nie mieć wystarczającej liczby adresów IP. Usługi domenowe usługi Azure AD wymagają co najmniej dwóch dostępnych adresów IP w podsieci, w jakiej jest włączona. Zalecamy posiadanie co najmniej 3-5 wolnych adresów IP w podsieci. Może to mieć miejsce, jeśli inne maszyny wirtualne są wdrażane w podsieci, co powoduje wyczerpanie liczby dostępnych adresów IP lub jeśli istnieje ograniczenie liczby dostępnych adresów IP w podsieci.*

### <a name="resolution"></a>Rozwiązanie

Podsieć sieci wirtualnej dla usług Azure AD DS wymaga wystarczającej liczby adresów IP dla automatycznie utworzonych zasobów. Ta przestrzeń adresowa IP obejmuje konieczność tworzenia zasobów zastępczych, jeśli występuje zdarzenie konserwacji. Aby zminimalizować ryzyko wyczerpania się dostępnych adresów IP, nie należy wdrażać dodatkowych zasobów, takich jak własne maszyny wirtualne, w tej samej podsieci sieci wirtualnej co usługi Azure AD DS.

Aby rozwiązać ten alert, usuń istniejącą domenę zarządzaną usługą Azure AD DS i ponownie utwórz ją w sieci wirtualnej z wystarczająco dużym zakresem adresów IP. Ten proces jest destrukcyjny, ponieważ domena zarządzana usługą Azure AD DS jest niedostępna, a wszystkie utworzone zasoby niestandardowe, takie jak jednostki organizacyjne lub konta usług, zostaną utracone.

1. [Usuń domenę zarządzana usługą Azure AD DS](delete-aadds.md) z katalogu.
1. Aby zaktualizować zakres adresów IP sieci wirtualnej, wyszukaj i wybierz *sieć wirtualną* w witrynie Azure portal. Wybierz sieć wirtualną dla usług Azure AD DS, która ma mały zakres adresów IP.
1. W obszarze **Ustawienia**wybierz pozycję *Przestrzeń adresowa*.
1. Zaktualizuj zakres adresów, wybierając istniejący zakres adresów i edytując go lub dodając dodatkowy zakres adresów. Upewnij się, że nowy zakres adresów IP jest wystarczająco duży dla zakresu podsieci usługi Azure AD DS. Gdy wszystko będzie gotowe, **zapisz** zmiany.
1. Wybierz **podsieci** w nawigacji po lewej stronie.
1. Wybierz podsieć, którą chcesz edytować, lub utwórz dodatkową podsieć.
1. Zaktualizuj lub określ wystarczająco duży zakres adresów IP, a następnie **zapisz** zmiany.
1. [Utwórz zastępczą domenę zarządzaną usługą Azure AD DS](tutorial-create-instance.md). Upewnij się, że wytypowana podsieć sieci wirtualnej została pobrana z wystarczająco dużym zakresem adresów IP.

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Zasoby nie można nieodwracalnie

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Zasoby używane przez usługi domenowe usługi azure ad zostały wykryte w nieoczekiwanym stanie i nie można odzyskać.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [usuń istniejącą domenę zarządzana usługą Azure AD DS](delete-aadds.md) i ponownie ją utworzyć. Jeśli masz problemy z usunięciem domeny zarządzanej usług Azure AD DS, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Nieprawidłowa podsieć

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Podsieć wybrana do wdrożenia usług domenowych usługi azure ad jest nieprawidłowa i nie można jej używać.*

### <a name="resolution"></a>Rozwiązanie

Ten błąd jest nieodwracalny. Aby rozwiązać ten alert, [usuń istniejącą domenę zarządzana usługą Azure AD DS](delete-aadds.md) i ponownie ją utworzyć. Jeśli masz problemy z usunięciem domeny zarządzanej usług Azure AD DS, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Zasoby są zablokowane

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Nie można obsługiwać co najmniej jednego zasobu sieciowego używanego przez domenę zarządzaną, ponieważ zakres docelowy został zablokowany.*

### <a name="resolution"></a>Rozwiązanie

Blokady zasobów można zastosować do zasobów platformy Azure, aby zapobiec zmianom lub usunięciu. Ponieważ usługi Azure AD DS jest usługą zarządzaną, platforma Azure potrzebuje możliwości wprowadzania zmian konfiguracji. Jeśli blokada zasobów jest stosowana w niektórych składnikach usług Ad DS platformy Azure, platforma Azure nie może wykonywać swoich zadań zarządzania.

Aby sprawdzić, czy blokady zasobów w składnikach usług Azure AD DS i usunąć je, wykonaj następujące kroki:

1. Dla każdego składnika sieci usługi Azure AD DS w grupie zasobów, takich jak sieć wirtualna, interfejs sieciowy lub publiczny adres IP, sprawdź dzienniki operacji w witrynie Azure portal. Te dzienniki operacji powinny wskazywać, dlaczego operacja nie powiódł się i gdzie jest stosowana blokada zasobów.
1. Wybierz zasób, w którym zastosowano blokadę, a następnie w obszarze **Blokady**wybierz i usuń blokady.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Zasoby nie nadają się do

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Nie można obsługiwać co najmniej jednego zasobu sieciowego używanego przez domenę zarządzaną z powodu ograniczeń zasad.*

### <a name="resolution"></a>Rozwiązanie

Zasady są stosowane do zasobów platformy Azure i grup zasobów, które kontrolują, jakie akcje konfiguracji są dozwolone. Ponieważ usługi Azure AD DS jest usługą zarządzaną, platforma Azure potrzebuje możliwości wprowadzania zmian konfiguracji. Jeśli zasady są stosowane w niektórych składnikach usług Ad DS platformy Azure, platforma Azure może nie być w stanie wykonywać swoich zadań zarządzania.

Aby sprawdzić, czy są stosowane zasady w składnikach usług Ad DS platformy Azure i zaktualizuj je, wykonaj następujące kroki:

1. Dla każdego składnika sieci usługi Azure AD DS w grupie zasobów, takich jak sieć wirtualna, karta sieciowa lub publiczny adres IP, sprawdź dzienniki operacji w witrynie Azure portal. Te dzienniki operacji powinny wskazywać, dlaczego operacja nie powiódł się i gdzie są stosowane restrykcyjne zasady.
1. Wybierz zasób, w którym stosowana jest zasada, a następnie w obszarze **Zasady**wybierz i edytuj zasady, aby była mniej restrykcyjna.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizacja nie została zakończona w ciągupewien czasu

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Domena zarządzana została ostatnio zsynchronizowana z usługą Azure AD w dniu [data]. Użytkownicy mogą nie być w stanie zalogować się w domenie zarządzanej lub członkostwa w grupach może nie być zsynchronizowany z usługą Azure AD.*

### <a name="resolution"></a>Rozwiązanie

[Sprawdź kondycję usług Ad DS platformy Azure](check-health.md) pod kątem alertów wskazujących problemy w konfiguracji domeny zarządzanej. Problemy z konfiguracją sieci można zablokować synchronizacji z usługi Azure AD. Jeśli możesz rozpoznać alerty wskazujące problem z konfiguracją, odczekaj dwie godziny i sprawdź, czy synchronizacja została pomyślnie zakończona.

Następujące typowe przyczyny powodują, że synchronizacja ma się zatrzymać w domenach zarządzanych usług Azure AD DS:

* Wymagana łączność sieciowa jest zablokowana. Aby dowiedzieć się więcej o tym, jak sprawdzić problemy w sieci wirtualnej platformy Azure i co jest wymagane, zobacz [rozwiązywanie problemów z grupami zabezpieczeń sieci](alert-nsg.md) i [wymaganiami sieciowymi dotyczącymi usług domenowych usługi Azure AD](network-considerations.md).
*  Synchronizacja haseł nie została skonfigurowana ani pomyślnie ukończona podczas wdrażania domeny zarządzanej usług Azure AD DS. Synchronizację haseł można skonfigurować dla [użytkowników korzystających wyłącznie](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) z chmury lub [użytkowników hybrydowych z on-prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Kopia zapasowa nie została podjęta w czasie

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Kopia zapasna domeny zarządzanej została ostatnio utworzona w dniu [data].*

### <a name="resolution"></a>Rozwiązanie

[Sprawdź kondycję usług Ad DS platformy Azure](check-health.md) dla alertów, które wskazują problemy w konfiguracji domeny zarządzanej. Problemy z konfiguracją sieci mogą uniemożliwić platformie Azure pomyślne wykonywanie kopii zapasowych. Jeśli możesz rozpoznać alerty wskazujące problem z konfiguracją, odczekaj dwie godziny i sprawdź, czy synchronizacja została pomyślnie zakończona.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Zawieszenie z powodu wyłączonej subskrypcji

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Domena zarządzana jest zawieszona, ponieważ subskrypcja platformy Azure skojarzona z domeną nie jest aktywna.*

### <a name="resolution"></a>Rozwiązanie

> [!WARNING]
> Jeśli domena zarządzana usługą Azure AD DS jest zawieszona na dłuższy okres czasu, istnieje niebezpieczeństwo jej usunięcia. Jak najszybciej rozwiąż przyczynę zawieszenia. Aby uzyskać więcej informacji, zobacz [Opis zawieszonych stanów usług Azure AD DS](suspension.md).

Usługi Azure AD DS wymaga aktywnej subskrypcji. Jeśli subskrypcja platformy Azure, z którą została skojarzona domena zarządzana usługą Azure AD DS, nie jest aktywna, należy ją odnowić, aby ponownie aktywować subskrypcję.

1. [Odnów swoją subskrypcję platformy Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po odnowieniu subskrypcji powiadomienie usługi Azure AD DS umożliwia ponowne włączenie domeny zarządzanej.

Gdy domena zarządzana jest ponownie włączona, kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Zawieszenie z powodu nieprawidłowej konfiguracji

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Domena zarządzana jest zawieszona z powodu nieprawidłowej konfiguracji. Usługa przez długi czas nie mogła zarządzać, łatać ani aktualizować kontrolerów domeny dla domeny zarządzanej.*

### <a name="resolution"></a>Rozwiązanie

> [!WARNING]
> Jeśli domena zarządzana usługą Azure AD DS jest zawieszona na dłuższy okres czasu, istnieje niebezpieczeństwo jej usunięcia. Jak najszybciej rozwiąż przyczynę zawieszenia. Aby uzyskać więcej informacji, zobacz [Opis zawieszonych stanów usług Azure AD DS](suspension.md).

[Sprawdź kondycję usług Ad DS platformy Azure](check-health.md) dla alertów, które wskazują problemy w konfiguracji domeny zarządzanej. Jeśli możesz rozpoznać alerty wskazujące problem z konfiguracją, odczekaj dwie godziny i sprawdź ponownie, czy synchronizacja została zakończona. Gdy będzie to gotowe, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby ponownie włączyć domenę zarządzaną usługą Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
