---
ms.assetid: ''
title: Usługa Azure Key Vault usuwania nietrwałego | Dokumentacja firmy Microsoft
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 6a3573cf31418309a31126b2a0c6a43ea2e0c745
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-key-vault-soft-delete-overview"></a>Omówienie usługi Azure Key Vault soft-delete

Funkcja usuwania nietrwałego Key Vault umożliwia odzyskiwanie usuniętych magazynów i magazynu obiektów, znany jako usuwania nietrwałego. W szczególności można rozwiązać następujących scenariuszy:

- Obsługa możliwych do odzyskania usuwanie magazynu kluczy
- Obsługa możliwych do odzyskania usuwanie magazynu kluczy obiektów (np. klucze, klucze tajne certyfikatów)

## <a name="supporting-interfaces"></a>Obsługa interfejsów

Funkcja usuwania nietrwałego jest początkowo dostępna za pośrednictwem pozostałe .NET / C#, interfejsy środowiska PowerShell i interfejsu wiersza polecenia.

Ogólne informacje dla odwołań tych więcej szczegółów, [z informacjami o kluczach magazynu](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scenariusze

Azure magazynów kluczy są śledzone zasoby zarządzanym przez Menedżera zasobów Azure. Usługa Azure Resource Manager określa również dobrze zdefiniowanego zachowania do usunięcia, co wymaga czy powodzenie operacji DELETE muszą dawać w wyniku tego zasobu nie nie jest już dostępny. Funkcja usuwania nietrwałego dotyczy Odzyskiwanie usuniętego obiektu czy przypadkowym lub zamierzone został usunięty.

1. W typowy scenariusz użytkownik może przypadkowo usunięto magazyn kluczy lub obiekt magazynu kluczy; Jeśli który klucz magazynu lub klucza obiekt magazynu był możliwy do odzyskania przez okres wcześniej, użytkownik może cofnąć i odzyskiwanie ich danych.

2. W przypadku różnych złośliwy użytkownik może próbować usunąć magazyn kluczy lub obiektu magazynu kluczy, na przykład klucz w magazynie, powodować zakłócenia biznesowych. Oddzielanie usuwanie magazynu kluczy lub obiekt magazynu kluczy z rzeczywiste usunięcie danych może służyć jako zabezpieczeniem, na przykład, ograniczanie uprawnień do usunięcia danych z innym zaufane roli. Takie podejście efektywnie wymaga kworum dla danej operacji, które w przeciwnym razie może spowodować utratę danych bezpośrednim.

### <a name="soft-delete-behavior"></a>Zachowanie soft-delete

Przy użyciu tej funkcji operacji usuwania w magazynie kluczy lub obiekt magazynu kluczy jest soft usunięciem, efektywnie zawierający zasoby na okres przechowywania danego podczas przekazywania wygląd, że obiekt jest usunięty. Dodatkowo usługa zapewnia mechanizm Odzyskiwanie usuniętego obiektu zasadniczo cofanie usuwania. 

Usuwania nietrwałego jest opcjonalne zachowanie magazyn kluczy i **nie jest włączone domyślnie** w tej wersji. 

### <a name="key-vault-recovery"></a>Magazyn kluczy odzyskiwania

Podczas usuwania magazynu kluczy, usługa tworzy zasób serwera proxy w ramach subskrypcji, dodawanie metadanych wystarczające do odzyskania. Zasób serwera proxy jest obiektem przechowywane, dostępne w tej samej lokalizacji co magazyn kluczy usunięte. 

### <a name="key-vault-object-recovery"></a>Odzyskiwanie obiektów magazynu kluczy

Podczas usuwania obiektu magazynu kluczy, na przykład klucz usługi spowoduje umieszczenie obiektu w stanie usunięty, dzięki czemu niedostępna dla wszystkich operacji pobierania. W tym stanie obiektu magazynu kluczy może wystąpić tylko, odzyskana lub wymuszone/trwale usunięte. 

W tym samym czasie Key Vault zaplanowane usunięcie danych odpowiadające usunięto magazynu kluczy lub obiekt magazynu kluczy do wykonania po upływie czasu przechowywania wcześniej. Rekord DNS odpowiadający magazynu także jest przechowywany na czas trwania okresu przechowywania.

### <a name="soft-delete-retention-period"></a>Okres przechowywania soft-delete

Elastyczne zasoby usunięte zostaną zachowane na wybrany okres czasu, w ciągu 90 dni. Podczas interwału usuwania nietrwałego przechowywania następujących warunków:

- Użytkownik może wyświetlić listę wszystkich magazynów kluczy i obiektów magazynu kluczy w stanie usuwania nietrwałego dla Twojej subskrypcji, a także dostęp do usunięcia i odzyskiwania informacji o nich.
    - Tylko użytkownicy z uprawnieniami specjalne można wyświetlić listę usuniętych magazynów. Zaleca się naszych użytkowników utworzenie niestandardowej roli zabezpieczeń z odpowiednimi uprawnieniami specjalne magazynów Obsługa usunięta.
- Nie można utworzyć magazyn kluczy o takiej samej nazwie w tej samej lokalizacji; odpowiednio magazynu kluczy nie można utworzyć obiektu w danym magazynie Jeśli tego magazynu kluczy zawiera obiekt o tej samej nazwie i która jest w stanie usunięty 
- W szczególności uprzywilejowanych użytkownik może przywrócić magazynu kluczy lub obiekt magazynu kluczy wydając polecenie Odzyskaj na odpowiadający jej zasób serwera proxy.
    - Użytkownik, członek roli niestandardowego, który ma uprawnienia do tworzenia magazynu kluczy w grupie zasobów można przywrócić magazynu.
- Tylko użytkownik, w szczególności uprzywilejowanych może wymusić usuwanie magazynu kluczy lub obiektu magazynu kluczy wydając polecenie delete na odpowiadający jej zasób serwera proxy.

Chyba że zostanie przywrócona magazynu kluczy lub obiekt magazynu kluczy, usługa z końcem okresu przechowywania wykonuje przeczyszczanie wszystkie usunięte nietrwale magazynu kluczy lub obiektu magazynu kluczy i jego zawartości. Nie może być zmienił jego usunięcia zasobu.

### <a name="permitted-purge"></a>Przeczyszczanie dozwolone

Trwałe usuwanie, usuwanie, magazyn kluczy jest możliwe za pośrednictwem operację POST zasobu serwera proxy i wymaga specjalnych uprawnień. Ogólnie rzecz biorąc tylko właściciel subskrypcji będą mogli przeczyścić magazynu kluczy. Operację POST wyzwala usunięcia natychmiastowe i nie można odzyskać tego magazynu. 

Wyjątek ma miejsce w przypadku subskrypcji platformy Azure została oznaczona jako *nieusuwalnej*. W takim przypadku tylko usługa może następnie wykonaj rzeczywiste usunięcie i wykonuje zaplanowane proces. 

### <a name="billing-implications"></a>Implikacje rozliczeń

Ogólnie rzecz biorąc, gdy obiekt (magazynu kluczy lub klucz lub klucz tajny) jest w stanie usunięty, istnieją tylko dwa operacje możliwe: "przeczyścić" i "Odzyskiwanie". Wszystkie inne operacje zakończy się niepowodzeniem. W związku z tym mimo, że obiekt nie istnieje, nie może być wykonane i dlatego nie użycia nastąpi, więc nie ma rachunku. Jednak są następujące wyjątki:

- "przeczyścić" i "odzyskać" działania są zliczane operacje normalne magazynu kluczy i będą naliczane.
- Obiekt jest klucza HSM, opłata "Klucza chronionego przez moduł HSM" według klucza wersji na miesiąc opłat zostanie zastosowana Jeśli użyto klucza wersji w ciągu ostatnich 30 dni. Po tym ponieważ obiekt jest w stanie usunięty, może być wykonywane żadne operacje na nim, więc bez dodatkowych opłat zostaną zastosowane.

## <a name="next-steps"></a>Kolejne kroki

Następujące dwa przewodniki oferują scenariuszy użycia głównej dla używanie opcji usuwania nietrwałego.

- [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](key-vault-soft-delete-powershell.md) 
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](key-vault-soft-delete-cli.md)

